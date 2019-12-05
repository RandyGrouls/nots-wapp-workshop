# Workshop Repository Pattern
In deze workshop ga je de applicatie van de voorgaande workshop uitbreiden met het Repository Pattern en het Unit of Work pattern.

## Repository Pattern
Er moeten een aantal classes en interfaces aangemaakt worden. In onderstaand voorbeeld worden deze in de `DAL` directory geplaatst.

<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/Repository-classes.PNG" alt="Repository files" width="300px"><br/>

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
Omdat bovenstaande classes generic zijn. Kunnen ze in elke applicatie hergebruikt worden.

De volgende stap is om een repository interface te maken voor elke `DBSet`. Dit interface extend het `IRepository` interface met een concrete entity en zal methoden bevatten die gebruikt worden om queries te encapsulaten. Onderstaand voorbeeld is voor de `Neighbourhoods` table.

```c#
public interface INeighbourhoodsRepository : IRepository<Neighbourhoods>
{
    IEnumerable<Neighbourhoods> GetNeighbourhoodsContainingString(string indexString); //Extra methode om neighbourhoods te filteren
}
```
Om vervolgens alle classes en interfaces aan elkaar te koppelen moet er een class gemaakt worden dat bovenstaand interface implementeert en de `Repository` class extend.

```c#
public class NeighbourhoodsRepository : Repository<Neighbourhoods>, INeighbourhoodsRepository
{
    public DBFirstDemoContext DemoContext
    {
        get { return context as DBFirstDemoContext; }
    }

    public NeighbourhoodsRepository(DBFirstDemoContext context) : base(context)
    {
    }

    public IEnumerable<Neighbourhoods> GetNeighbourhoodsContainingString(string indexString)
    {
        return DemoContext.Neighbourhoods.Where(n => n.Neighbourhood.ToLower().Contains("a"));
    }
}
```
Het is mogelijk om hier te stoppen met de implementatie en alleen gebruik te maken van het Repository Pattern. Echter kan het Unit of Work Pattern ook nog geïmplementeerd worden. Het voordeel van dit pattern is dat er meerdere CRUD operaties kunnen worden uitgevoerd in één keer. Dit heeft als gevolg dat óf alle operaties slagen óf alle operaties falen. Dit voorkomt data inconsistentie.  

## Unit of Work Pattern
Alle nieuwe interfaces en classes worden weer in de `DAL` folder geplaatst. Maak eerst een `IUnitOfWork` interface aan zoals onderstaand voorbeeld:

```c#
public interface IUnitOfWork : IDisposable
{
    INeighbourhoodsRepository Neighbourhoods { get; }
    int Complete();
}
```
Maak daarna een `UnitOfWork` class die dit interface implementeert:

```c#
public class UnitOfWork : IUnitOfWork
{
    private readonly DBFirstDemoContext context;
    public INeighbourhoodsRepository Neighbourhoods { get; private set; }
    //Voeg hier extra repositories toe

    public UnitOfWork(DBFirstDemoContext context)
    {
        this.context = context;
        Neighbourhoods = new NeighbourhoodsRepository(context);
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
Nu is het dus mogelijk om CRUD operaties uit te voeren op verschillende repositories en vervolgens de `Complete` methode te gebruiken om deze wijzigingen op te slaan.

Nu moet de `NeighbourhoodsController` aangepast worden om het Unit of Work Pattern te gebruiken.

```c#
    public class NeighbourhoodsController : Controller
    {
        private UnitOfWork unitOfWork;

        public NeighbourhoodsController(DBFirstDemoContext context)
        {
            unitOfWork = new UnitOfWork(context);
        }

        // GET: Neighbourhoods
        public IActionResult Index()
        {
            return View(unitOfWork.Neighbourhoods.GetAll());
        }

        // GET: Neighbourhoods/Details/5
        public IActionResult Details(int? id)
        {
            if (id == null)
            {
                return NotFound();
            }

            var neighbourhoods = unitOfWork.Neighbourhoods.Get((int)id);

            if (neighbourhoods == null)
            {
                return NotFound();
            }

            return View(neighbourhoods);
        }

        // GET: Neighbourhoods/Create
        public IActionResult Create()
        {
            return View();
        }

        // POST: Neighbourhoods/Create
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public IActionResult Create([Bind("NeighbourhoodGroup,Neighbourhood,NeighbourhoodId")] Neighbourhoods neighbourhoods)
        {
            if (ModelState.IsValid)
            {
                unitOfWork.Neighbourhoods.Add(neighbourhoods);
                unitOfWork.Complete();
                return RedirectToAction(nameof(Index));
            }
            return View(neighbourhoods);
        }

        // GET: Neighbourhoods/Edit/5
        public IActionResult Edit(int? id)
        {
            if (id == null)
            {
                return NotFound();
            }

            var neighbourhoods = unitOfWork.Neighbourhoods.Get((int)id);

            if (neighbourhoods == null)
            {
                return NotFound();
            }

            return View(neighbourhoods);
        }

        // POST: Neighbourhoods/Edit/5
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public IActionResult Edit(int id, [Bind("NeighbourhoodGroup,Neighbourhood,NeighbourhoodId")] Neighbourhoods neighbourhoods)
        {
            if (id != neighbourhoods.NeighbourhoodId)
            {
                return NotFound();
            }

            if (ModelState.IsValid)
            {
                try
                {
                    var found = unitOfWork.Neighbourhoods.Get(id);

                    found.NeighbourhoodGroup = neighbourhoods.NeighbourhoodGroup;
                    found.Neighbourhood = neighbourhoods.Neighbourhood;

                    unitOfWork.Complete();
                }
                catch (DbUpdateConcurrencyException)
                {
                    if (!NeighbourhoodsExists(neighbourhoods.NeighbourhoodId))
                    {
                        return NotFound();
                    }
                    else
                    {
                        throw;
                    }
                }
                return RedirectToAction(nameof(Index));
            }
            return View(neighbourhoods);
        }

        // GET: Neighbourhoods/Delete/5
        public IActionResult Delete(int? id)
        {
            if (id == null)
            {
                return NotFound();
            }

            var neighbourhoods = unitOfWork.Neighbourhoods.Get((int)id);
            if (neighbourhoods == null)
            {
                return NotFound();
            }

            return View(neighbourhoods);
        }

        // POST: Neighbourhoods/Delete/5
        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public IActionResult DeleteConfirmed(int id)
        {
            var neighbourhoods = unitOfWork.Neighbourhoods.Get(id);
            unitOfWork.Neighbourhoods.Remove(neighbourhoods);
            unitOfWork.Complete();
            return RedirectToAction(nameof(Index));
        }

        private bool NeighbourhoodsExists(int id)
        {
            return unitOfWork.Neighbourhoods.Get(id) != null;
        }
    }
```
