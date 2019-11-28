# Workshop Repository Pattern
In deze workshop ga je de applicatie van de voorgaande workshop uitbreiden met het Repository Pattern en het Unit of Work pattern.

## Repository Pattern
Er moeten een aantal classes en interfaces aangemaakt worden. In onderstaand voorbeeld worden deze in de map "Repositories" geplaatst.

<img src="./afbeeldingen/repository-classes.png" alt="Repository files" width="300px"><br/>
<em>Figuur 9: Overzicht Repository bestanden</em>

Maak eerst een nieuwe interface aan met de naam `IRepository`. Deze krijgt vervolgens onderstaande methoden:

```c#
public interface IRepository<TEntity> where TEntity : class //zorgt ervoor dat er alleen een repository aangemaakt kan worden voor classes
{
    TEntity Get(int id); //Vind een enitity met primary key
    IEnumerable<TEntity> GetAll(); //Alle entities in repository ophalen
    IEnumerable<TEntity> Find(Expression<Func<TEntity, bool>> predicate); //Vind meerdere entities met een predicate

    void Add(TEntity entity); //Entity toevoegen aan de repository
    void AddRange(IEnumerable<TEntity> entities); //Meerdere entities toevoegen aan de repository

    void Remove(TEntity entity); //Entity verwijderen uit de repository
    void RemoveRange(IEnumerable<TEntity> entities); //Meerdere entities verwijderen uit de repository
}
```
Vervolgens, maak een `Repository` class aan. Zorg ervoor dat deze het zojuist aangemaakte interface implementeert. Vergeet niet om `where TEntity : class` toe te voegen. 

```c#
public class Repository<TEntity> : IRepository<TEntity> where TEntity : class
{
    protected readonly DbContext context;

    public Repository(DbContext context)
    {
        this.context = context;
    }

    public TEntity Get(int id)
    {
        return context.Set<TEntity>().Find(id);
    }

    public IEnumerable<TEntity> GetAll()
    {
        return context.Set<TEntity>().ToList();
    }

    public IEnumerable<TEntity> Find(Expression<Func<TEntity, bool>> predicate)
    {
        return context.Set<TEntity>().Where(predicate);
    }

    public void Add(TEntity entity)
    {
        context.Set<TEntity>().Add(entity);
    }

    public void AddRange(IEnumerable<TEntity> entities)
    {
        context.Set<TEntity>().AddRange(entities);
    }

    public void Remove(TEntity entity)
    {
        context.Set<TEntity>().Remove(entity);
    }

    public void RemoveRange(IEnumerable<TEntity> entities)
    {
        context.Set<TEntity>().RemoveRange(entities);
    }
}
```
Het mooie aan bovenstaande classes is dat deze generic zijn. Hierdoor kunnen ze in elke applicatie hergebruikt worden.

De volgende stap is om een repository interface te maken voor elke `DBSet`. Dit interface extend het `IRepository` interface met een concrete entity en zal methoden bevatten die gebruikt worden om queries te encapsulaten. Onderstaand voorbeeld is voor de `Neighbourhoods` table.

```c#
public interface INeighbourhoodRepository : IRepository<Neighbourhoods>
{
    IEnumerable<Neighbourhoods> GetNeighbourhoodsContainingString(string indexString); //Extra methode om neighbourhoods te filteren
}
```
Om vervolgens alle classes en interfaces aan elkaar te koppelen moet er een class gemaakt worden dat bovenstaand interface implementeert en de `Repository` class extend.

```c#
public class NeighbourhoodRepository : Repository<Neighbourhoods>, INeighbourhoodRepository
{
    public DBFirstDemoContext DemoContext
    {
        get { return context as DBFirstDemoContext; }
    }

    public NeighbourhoodRepository(DBFirstDemoContext context) : base(context)
    {
    }

    public IEnumerable<Neighbourhoods> GetNeighbourhoodsContainingString(string indexString)
    {
        return DemoContext.Neighbourhoods.Where(n => n.Neighbourhood.ToLower().Contains("a"));
    }
}
```
Het is mogelijk om deze classes nu zo te gebruiken:
```c#
//Neighbourhood aanmaken
var newNeighbourhood = new Neighbourhoods()
{
    Neighbourhood = "NewNeighbourhood"
};

//Repository aanmaken
var neighBourHoodRespository = new NeighbourhoodRepository(new DBFirstDemoContext());

neighBourHoodRespository.DemoContext.Neighbourhoods.Add(newNeighbourhood); //Neighbourhood toevoegen

//Wijzigingen opslaan
neighBourHoodRespository.DemoContext.SaveChanges();
```
Dit is echter ongewenst. Dan zou je dus overal waar je dit repository nodig hebt, steeds een nieuwe instantie aan moeten maken.

## Unit of Work Pattern

Een oplossing hiervoor is het Unit of Work pattern. Hier is een interface en een class voor nodig. Deze zijn verschillend per applicatie omdat er gebruik wordt gemaakt van alle Repository interfaces zoals bijvoorbeeld `INeighbourhoodRepository`.

Maak eerst een `IUnitOfWork` interface aan zoals onderstaand voorbeeld:

```c#
public interface IUnitOfWork : IDisposable
{
    INeighbourhoodRepository Neighbourhoods { get; }
    int Complete();
}
```
Maak daarna een `UnitOfWork` class die dit interface implementeert:

```c#
public class UnitOfWork : IUnitOfWork
{
    private readonly DBFirstDemoContext context;
    public INeighbourhoodRepository Neighbourhoods { get; private set; }
    //Voeg hier extra repositories toe

    public UnitOfWork(DBFirstDemoContext context)
    {
        this.context = context;
        Neighbourhoods = new NeighbourhoodRepository(context);
    }

    public int Complete()
    {
        return context.SaveChanges();
    }

    public void Dispose()
    {
        context.Dispose();
    }
}
```
Nu is het dus mogelijk om meerdere CRUD operaties uit te voeren op verschillende repositories en vervolgens de `Complete` methode te gebruiken om deze wijzigingen op te slaan. Onderstaand een voorbeeld hoe je dit kan toepassen op de `Neighbourhoods` repository. Plaats deze code in de Main methode van

```c#
using (UnitOfWork unitOfWork = new UnitOfWork(new DBFirstDemoContext()))
{
    //Neighbourhood aanmaken
    var newNeighbourhood = new Neighbourhoods()
    {
        Neighbourhood = "NewNeighbourhood"
    };
    unitOfWork.Neighbourhoods.Add(newNeighbourhood); //Neighbourhood toevoegen

    unitOfWork.Complete(); //Wijzigingen opslaan

    //Print alle neighbourhoods
    foreach (var neighbourhood in unitOfWork.Neighbourhoods.GetAll())
    {
        Console.WriteLine(neighbourhood.Neighbourhood);
    }
}
```

Probeer het programma uit te breiden met extra interfaces en classes voor de entities `Listings` en `Reviews`. Vergeet niet om `IUnitOfWork` en `UnitOfWork` aan te passen. Test of je deze ook weer kunt gebruiken in de `Main` methode. 