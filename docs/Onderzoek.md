# Inleiding
Dit onderzoek is geschreven voor de kennissessie: **Workshop Performance en Security** en ter ondersteuning van het ontwikkelproces voor de InsideAirBn webapplicatie. Er zal onderzoek gedaan worden naar een onderwerp betreft Performance en een onderwerp betreft Security.

# Opzet onderzoek
Het te onderzoeken onderwerp betreft Performance is ***Entity Framework/Repository pattern*** en het onderwerp betreft Security is ***Sensitive Data Exposure***. Elk onderwerp heeft vooraf bepaalde deelvragen die beantwoord zullen worden. Deze deelvragen zijn:

## Performance
**Wat is het?**<br/>
    *Onderzoeksmethode: bieb*

**Hoe werkt het?**<br/>
    *Onderzoeksmethode: bieb*

**Hoe implementeer je het?**<br/>
    *Onderzoeksmethode: bieb en lab*

## Security
**Wat is het?**<br/>
    *Onderzoeksmethode: bieb*

**Hoe werkt het?**<br/>
    *Onderzoeksmethode: bieb*

**Hoe merk je het?**<br/>
    *Onderzoeksmethode: bieb*

**Hoe voorkom je het?**<br/>
    *Onderzoeksmethode: bieb*

**Hoe implementeer je het?**<br/>
    *Onderzoeksmethode: bieb en lab*

# Entity Framework
## Wat is het Entity Framework?
Om te kunnen communiceren met een database, is een persistence framework nodig. Met een persistence framework kunnen er CRUD operaties uitgevoerd worden op een database. Het is mogelijk om dit zelf te schrijven met [ADO.NET](https://nl.wikipedia.org/wiki/ADO.NET) classes zoals `SqlConnection`, `SqlCommand` en `SqlDataReader`. Maar dit kost erg veel tijd omdat je dan zelf deze data in de database moet inlezen en omzetten naar bruikbare objecten.

Hiervoor is het Entity Framework ontwikkeld. Het Entity Framework is een open-source ORM framework voor .NET applicaties dat developers helpt met het communiceren met een database en ervoor zorgt dat alle bovenstaande acties niet zelf meer hoeven worden uitgevoerd. 

<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/EF.PNG" alt="Entity Framework" width="250px"><br/>
<em>Figuur 1: Entity Framework structuur</em>

In bovenstaand figuur is te zien waar het Entity Framework pas in de structuur van de applicatie. Er kan gewerkt worden met data met gebruik van objecten of domein specifieke classes zonder te hoeven focussen op de onderliggende database waar deze data staat opgeslagen. Hierdoor kan er op een hoger abstractieniveau omgegaan worden met deze data en is het mogelijk om applicaties te ontwikkelen met minder code en welke gemakkelijker te onderhouden zijn.

## Hoe werkt het Entity Framework?
Het Entity Framework werkt doormiddel van het mappen van een class naar de database. Deze class heet een `Context class`, en wordt altijd afgeleid van `DbContext`. In deze context classes worden domein specifieke classes (Entities) gebruikt doormiddel van een generic genaamd `DbSet<TEntity>`

<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/dbset-example.png" alt="DbSet example" width="400px"><br/>
<em>Figuur 2: Voorbeeld DbSet</em>

Om een entity te maken maak je eerst een class aan dat een databasetabel mapped. Als dit gedaan is, moet het eerst in een context class gezet worden doormiddel van de voorheen besproken `DbSet<TEntity>`. Wanneer deze context en entity classes aangemaakt zijn kunnen er queries geschreven worden. Deze queries worden geschreven met LINQ queries. Dit is de query taal die gebruikt wordt binnen .NET. 

<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/linq-query.png" alt="LINQ query"><br/>
<em>Figuur 3: Voorbeeld LINQ query</em>

Het entity framework zal deze LINQ queries omzetten naar SQL queries, de database voert deze SQL queries uit en stuurt dan het resultaat terug. Het Entity Framework zet dit resultaat dan om naar Entity Objects.

Het transformeren en vertalen van deze informatie wordt gedaan door het EDM (Entity Data Model). Het EDM staat binnen het Entity Framework en geeft een in-memory representatie van de gehele metadata. Deze metadata wordt in drie verschillende onderdelen opgedeeld: 

- **Conceptual model**<br/>
EF bouwt het conceptual model uit de domein classes, context class, standaard conventies van de domein classes, en configuraties.

- **Storage model**<br/>
EF bouwt het storage modal voor de onderliggende databaseschema. Bij het gebruik van code-first wordt er uitgegaan van het conceptual model. Bij het gebruik van database-first wordt er uitgegaan van de bestaande database.

- **Mappings**<br/>
EF bevat informatie over hoe het conceptual model mapped naar het databaseschema (storage model).

Deze drie worden samen gebruikt bij het uitvoeren van CRUD operaties op de database en het transformeren van de query resultaten naar Entity Objects.

## Hoe implementeer je het Entity Framework?
In dit voorbeeld wordt er uitgegaan van een Database First implementatie. Bij een Database first implementatie van het Entity framework wordt er eerst een database aangemaakt met daarin een of meerdere tabellen. 

<img src="./afbeeldingen/db-example.png" alt="db-example" width="200px"><br/>
<em>Figuur 4: Voorbeeld database</em>

Hierna kan er een applicatie aangemaakt worden. Wanneer deze applicatie is aangemaakt, moeten er een aantal packages geïnstalleerd worden. Ga naar `Tools > NuGet Package Manager > Manage NuGet Packages for Solution`. 

<img src="./afbeeldingen/package-manager.png" alt="package-manager" width="400px"><br/>
<em>Figuur 5: Package manager</em>


zoek in de search bar naar `microsoft.entityframeworkcore` en selecteer het eerste resultaat. Zorg dat `Include prerelease` is aangevinkt. Kies dan het project in het rechterscherm en selecteer de juiste preview versie van de package, wanneer dit gedaan is klik dan op install. Herhaal dit proces voor `microsoft.entityframeworkcore.tools` en `microsoft.entityframeworkcore.sqlserver`. 

<img src="./afbeeldingen/install-entity-framework.png" alt="install packages" width="500px"><br/>
<em>Figuur 6: Installeren packages</em>

Wanneer deze zijn geïnstalleerd ga dan naar `Tools > NuGet Package Manager > Package Manager Console`. In deze console wordt het volgende ingevoerd: `Scaffold-DbContext "Server=[SERVER_NAME];Database=DBFirstDemo;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir [DIRECTORY_NAME].` [SERVER_NAME] moet vervangen worden met de servernaam van de SQL Server, de [DIRECTORY_NAME] kan gewijzigd worden naar een map naar keuze.

Als dit command klaar is met uitvoeren, zijn er nieuwe bestanden aangemaakt voor de entities en context class. Er wordt voor elke tabel een entity aangemaakt. In dit voorbeeld is gekozen voor de Root directory van het project hiervoor is `-OutputDir [DIRECTORY_NAME]` weggelaten. Deze ziet er hierna zo uit:

<img src="./afbeeldingen/scaffolded-models.png" alt="install packages" width="300px"><br/>
<em>Figuur 7: Overzicht gegenereerde bestanden</em>

Daarna kun je met de gemaakte `DBContext` class CRUD operaties uitvoeren op de database. Deze class zal een één of meerdere `DBSets` bevatten afhankelijk van het aantal tabellen in de database. Omdat deze `DBSets` zich gedragen als een collectie, is het mogelijk om alle soorten LINQ queries te gebruiken om objecten te vinden.

```c#
DBFirstDemoContext context = new DBFirstDemoContext(); //Instantie van context aanmaken
```
```c#
//Neighbourhood aanmaken
var newNeighbourhood = new Neighbourhoods()
{
    Neighbourhood = "NewNeighbourhood"
};
context.Neighbourhoods.Add(newNeighbourhood); //Neighbourhood toevoegen
context.SaveChanges(); //Wijzigingen opslaan
```
```c#
var foundNeighbourhood = context.Neighbourhoods.Find(newNeighbourhood.NeighbourhoodId); //Neighbourhood vinden met primary key

var foundNeighbourhoods = context.Neighbourhoods.Where(n => n.Neighbourhood.ToLower().Contains("a")); //Alle Neighbourhoods vinden met de letter a

foundNeighbourhood.Neighbourhood = "UpdatedNeighbourhood"; //Properties aanpassen

context.SaveChanges(); //Wijzigingen opslaan
```
```c#
context.Neighbourhoods.Remove(foundNeighbourhood); //Bestaande Neighbourhood verwijderen

context.Neighbourhoods.RemoveRange(foundNeighbourhoods); //Meerdere Neighbourhoods verwijderen

context.SaveChanges(); //Wijzigingen opslaan
```
# Repository Pattern
## Wat is het Repository Pattern?
Het Repository Pattern is een veel gebruikt pattern om duplicatie van data access logica binnen een applicatie te voorkomen. Denk hierbij bijvoorbeeld aan het communiceren met een database. Het biedt een abstractie van gegevens zodat de applicatie kan werken met een interface dat de interface van een collection van entities benadert. 

Het uitvoeren van CRUD operaties op deze collection gebeurd dan via een aantal simpele methoden, zonder bezig te hoeven zijn met database gerelateerde taken zoals de connectie en queries. Onderstaand de voordelen van het Repository Pattern:
- Het voorkomt het dupliceren van querylogica.
- Het ontkoppelt de applicatie van persistence frameworks (zoals bijvoorbeeld [Entity Framework](#entity-framework)).
- Het maakt de applicatie beter testbaar.

## Hoe werkt het Repository Pattern? 
Het repository pattern zorgt voor een extra laag abstractie over de data access laag, in ons geval houdt dat in dat wij met behulp van het repository pattern extra abstractie willen bij het maken van CRUD operaties. 

Om het repository pattern toe te passen is er een repository nodig, een repository is een interface voor een verzameling objecten in het geheugen. Vervolgens is er een klasse nodig dat dit interface implementeert dat een generic `DbContext` bevat. Deze kan vervolgens ook in andere applicaties gebruikt worden.

In het geval van onze applicatie zal er voor elk entity een repository aangemaakt worden dat dit generic interface implementeert. Hier kunnen dan extra methoden worden toegevoegd die afhankelijk zijn van de toepassingscontext. Als laatste is er dan nog een klasse nodig die de functionaliteit van deze extra methoden bevat.

<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/repository-diagram.png" alt="Repository Diagram" width="500px"><br/>
<em>Figuur 8: Voorbeeld repository implementatie</em>

Merk op dat er bovenstaand geen methode aanwezig is om een object te updaten. Dit is namelijk een veelgemaakte fout. Als een object aangepast moet worden is het de bedoeling dat deze uit de collectie wordt gehaald met de `Get(id)` methode en vervolgens wordt aangepast. Nogmaals, een repository moet zich gedragen als een collectie van objecten. een `List` heeft geen methode om een object te updaten, daarom heeft een repository dit ook niet.

Om de wijzigingen aan deze collectie op te slaan in een database, wordt gebruik gemaakt van het Unit of Work Pattern. Een Unit of Work houdt een of meerdere soorten collecties bij van objecten waar CRUD operaties op zijn uitgevoerd. Een Unit of Work bevat een `Save()` methode dat verantwoordelijk is voor het opslaan van deze wijzigingen in een database.

## Hoe implementeer je het Repository Pattern?
Om het Repository pattern te implementeren begin je met het aanmaken van een Entity framework applicatie zoals beschreven in het [Entity framework hoofdstuk](hoe-implementeer-je-het-entity-framework).

Wanneer deze applicatie is aangemaakt moeten er een aantal classes en interfaces aangemaakt worden. In onderstaand voorbeeld worden deze in de `DAL` directory geplaatst.

<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/Repository-classes.PNG" alt="Repository files" width="300px"><br/>
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
<!-- Ga naar `NeighbourhoodsController` en vervang deze code:

```c#
private readonly DBFirstDemoContext _context;

public NeighbourhoodsController(DBFirstDemoContext context)
{
    _context = context;
}
```
Door deze code:

```c#
private readonly INeighbourhoodsRepository _context;

public NeighbourhoodsController(DBFirstDemoContext context)
{
    _context = new NeighbourhoodsRepository(context);
}
```
Alle foutmeldingen in `NeighbourhoodsController` worden in de volgende stappen opgelost. -->

### Unit of Work Pattern
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
Het programma kan nu uitgebreid worden met extra controllers, interfaces en classes voor alle andere entities waar nodig.
# Sensitive Data Exposure

## Wat is het?
Sensitive Data Exposure is één van de meest voorkomende beveiligingskwetsbaarheid met de meeste impact.   De meest voorkomende fout is dat de data in plaintext tussen systemen uitgewisseld worden, meestal in een onveilige verbinding. Dit is een probleem, omdat onbevoegden toegang kunnen krijgen tot gevoelige persoonlijke data. Dit probleem kan opgelost worden met behulp van cryptografie. 

Cryptografie zorgt ervoor dat gegevens overdraagbaar zijn, die niet leesbaar zijn door onbevoegden. Dat gebeurt met een encryptie en decryptie. Een encryptie is het proces dat een plaintext data vertaalt naar een code, zonder dat er een betekenis heeft, dit noemt men een ciphertext. Om de ciphertext weer te converteren naar een plaintext wordt dit een decryptie genoemd. 

Ondanks dat er cryptografie toegepast worden, komt het voor dat er gebruik wordt gemaakt van zwakke algoritmes, protocollen, codering en sleutelgeneratie en -beheer. Daarom is het van belang dat de juiste techniek gepast worden, die afhankelijk is van de noodzaak van de data. 

## Hoe werkt het?
Moderne cryptografieën zijn (nagenoegen) onmogelijk om te kraken, omdat het soms jaren kunnen duren om één geëncrypte informatie te kraken. Dat is voor criminelen niet aantrekkelijk. Daarom gaan zij voor sleutels, omdat de opslag ervan minder goed beveiligd is. De hackers kunnen diverse aanvallen uitvoeren, zonder de cryptografie te kraken:

### Man-in-the-middle-aanval
Man-in-the-middle-aanval is een aanval waarbij de communicatie tussen twee partijen onderschept worden, zonder dat zij ervan weten. Daarbij kunnen berichten gelezen en gewijzigd worden. Daarom is het van belang om een goede encryptie te gebruiken.  

### SQL-injectie
SQL-injectie is een aanval, waarbij in een gebruikersinput SQL-statements wordt ingevoerd, die onbewust in de database wordt gerund. Het is vooral populair om deze aanval bij inlogpagina’s uit te voeren.  
Het gevaar van deze aanvallen is dat alle gevoelige persoonlijke gegevens toegankelijk zijn voor onbevoegden, terwijl die beschermd hadden moeten zijn, zoals gezondheidsdossiers, inloggegevens, persoonlijke gegevens en creditcards. De bescherming van deze gegevens staan gedefinieerd door wetten, zoals EU’s GDPR en landelijke privacywetten.

## Hoe merk je het?
Een applicatie kan last hebben van Sensitive Data Exposure. Hiervoor zijn er een aantal mogelijkheden:
### Gevoelige data plaintext in database
Gevoelige persoonlijke informatie wordt opgeslagen in een database, zonder dat er gebruik wordt gemaakt van encryptie. Het is van belang om gevoelige data te encrypten. Als criminelen op een onbevoegde wijze toegang hebben tot de database, dan kunnen zij niets doen met de database, aangezien de data onleesbaar is voor hen. Zij moeten óf toevallig in bezit zijn van de sleutels óf bij iedere data een brute-force uitvoeren. Met een goede encryptie kan dit jaren duren. 
### Penetratietest
Een penetratietest is een manier om een applicatie op kwetsbaarheden te toetsen, waarbij kwetsbaarheden daadwerkelijk uitgevoerd worden om in de applicatie in te breken.   Hierbij is het mogelijk om een betere beeld te krijgen van de beveiligingsrisico’s van de applicatie. Na zo’n test wordt er aanbevelingen voor het verhelpen van de ontdekte kwetsbaarheden. 

## Hoe voorkom je het?

Het kan voorkomen worden door de code van de applicatie te reviewen en correct toepassen van veilige cryptografische algoritmen, veilige opslag van geheime sleutels, transportbeveiliging en identificeren van ontbrekende cryptografische bescherming. Hieronder staan een aantal specifieke maatregelen:
-	Het versleutelen van data tijdens transport met behulp van veilige protocollen.
-	Het onbruikbaar maken van caching bij een response, die gevoelige informatie bevatten. 
-	Het opslaan van wachtwoorden met behulp van sterke, adaptieve en salted hashfuncties. 
-	Het gebruikmaken van key management om ervoor te zorgen dat standaard algoritmes, protocollen en sleutels op z’n plaats zijn en up-to-date.
-	Het onafhankelijk valideren van de effectiviteit van configuratie en instellingen. 

Voor dit onderzoek wordt de focus gelegd op de cryptografie. Cryptografie bestaat uit hashfuncties, symmetrische en asymmetrische cryptografie. 

### Hashfuncties
Een hashfunctie is een toepassing om een plaintext van verschillende omvang om te zetten naar onleesbare code met dezelfde omvang.   Het is erg handig om gevoelige persoonlijke informatie te pseudonimiseren, zoals BSN, wachtwoorden, telefoonnummers en e-mailadressen. Een hashwaarde kan niet geraden worden, behalve door verschillende invoergegevens te proberen, net zolang totdat de gezochte hashwaarde tegen gekomen is. Dat maakt voor telefoonnummers en e-mailadressen minder uit. Voor wachtwoorden kan dit een nadeel zijn, omdat sommige mensen dezelfde wachtwoorden kunnen gebruiken. Daarom is het verstandig om extra data toe te voegen aan een hashfunctie, dit wordt ook wel salting genoemd. Een voorbeeld kan zijn om gebruikersnaam bij het wachtwoord van een gebruiker te hashen. 

### Symmetrische cryptografie
Een symmetrische cryptografie is een vorm van encryptie, waarbij er één sleutel wordt gebruikt om informatie te encrypten en decrypten.   Dat betekent dat de sleutel gebruikt wordt zowel de verzender als de ontvanger, ook wel shared secret genoemd. Het is veel sneller en vereist minder rekenkracht voor het encrypten en decrypten van informatie, zelfs als de lengte van een sleutel toeneemt. Aan de andere kant kent symmetrische cryptografie ook nadelen. Het voornaamste nadeel is het uitwisselen van een gezamenlijke sleutel onder gebruikers om informatie te encrypten en decrypten. Vooral bij de uitwisseling van een gezamenlijke sleutel over een onveilige connectie is de kans aanwezig dat de sleutel onderschept wordt door onbevoegde partijen. Daarnaast vormt dit een beveiligingsrisico als iedereen de sleutel krijgt om toegang tot bepaalde informatie te verschaffen. 

### Asymmetrische cryptografie
Een asymmetrische cryptografie is een vorm van encryptie, waarbij er twee sleutels gebruikt worden: één sleutel om te encrypten en een andere sleutel om te decrypten.  Deze sleutels noemt men public en private key. Het verschil is dat een public key toegankelijk voor iedereen is, maar een private key is geheim. Er zijn minder sleutels nodig ten opzichte van symmetrische cryptografie bij een grote groep. Daarbij is het een betrouwbare vorm van encryptie, ondanks dat er een grote groep is. Dat heeft te maken dat er een digitale handtekening wordt gezet, voordat een bericht wordt gelezen. Dit zorgt ervoor dat de actie niet ontkend kan worden. Asymmetrische cryptografie kent ook een aantal nadelen. Bij het verlies van private sleutels kan het nagenoegen niet meer hersteld worden en dan moeten alle public en private sleutels vervangen worden. Als de private sleutels in de verkeerde handen komen, dan kunnen zij dezelfde handelingen uitvoeren die de eigenaar ook kan.

## Hoe implementeer je het?
Voor .NET Core heeft Microsoft een standaard library System.Security.Cryptography ontwikkeld, waarin alle algoritmes van cryptografie geïmplementeerd zijn. Dus wordt er een workshop gehouden over het implementeren van één van de beschikbare cryptografieën. In dit geval wordt er hashing toegepast bij meerdere wachtwoorden en hoe het mogelijk is om het kraken van hashing te minimaliseren.
Implementatie van hashing
1.	Maak een nieuwe ConsoleApp aan binnen de Visual Studio Preview.
2.	Voeg een statische methode HashPassword(string password) binnen de klasse Program.
```c#
private static string HashPassword(string password)
{
    using var sha256 = SHA256.Create();
    var hashedBytes = sha256.ComputeHash(Encoding.UTF8.GetBytes(password));
    return BitConverter.ToString(hashedBytes).Replace("-", "").ToLower();
}
```
3.	Voeg een string array toe van de top 10 meest gebruikte wachtwoorden in de Main-methode en maak van twee wachtwoorden duplicaten. In totaal zijn er 12 wachtwoorden, waarvan 10 wachtwoorden uniek zijn. 
4.	Maak een for-loop van de string array van wachtwoorden in combinatie met de gehashte wachtwoorden en print deze in de console. Wat valt je op? 
5.	Gehashte wachtwoorden kunnen achterhaald worden door een bruteforce uit te voeren met een lijst van alle mogelijke wachtwoorden. In dit geval wordt er gebruik gemaakt van een online password hash cracker. Kopieer één van de gehashte wachtwoorden in de volgende website: https://crackstation.net/.
6.	Voeg een statische methode GenerateRandomSalt() binnen de klasse Program.
```c#
private static string GenerateRandomSalt()
{
    byte[] bytes = new byte[128 / 8];
    using var keyGenerator = RandomNumberGenerator.Create();
    keyGenerator.GetBytes(bytes);
    return BitConverter.ToString(bytes).Replace("-", "").ToLower();
}
```
7.	Voeg bij de for-loop van wachtwoorden een salt toe bij de hashing van iedere wachtwoord. 

## Entity Framework
Entityframeworktutorial. (z.d.-a). Entity Framework Core with Existing Database. Geraadpleegd op 28 november 2019, van https://www.entityframeworktutorial.net/efcore/create-model-for-existing-database-in-ef-core.aspx

Entityframeworktutorial. (z.d.-b). Install Entity Framework Core. Geraadpleegd op 28 november 2019, van https://www.entityframeworktutorial.net/efcore/install-entity-framework-core.aspx

Entityframeworktutorial. (z.d.-c). What is Entity Framework? Geraadpleegd op 28 november 2019, van https://www.entityframeworktutorial.net/what-is-entityframework.aspx

Programming with Mosh. (2017, 13 april). Entity Framework 6 Tutorial: Learn Entity Framework 6 from Scratch. Geraadpleegd op 28 november 2019, van https://www.youtube.com/watch?time_continue=3&v=ZX7_12fwQLU&feature=emb_logo

## Repository Pattern
Bergman, P.-E. (2019, 7 mei). Repository Design Pattern. Geraadpleegd op 28 november 2019, van https://medium.com/@pererikbergman/repository-design-pattern-e28c0f3e4a30

Galbraith, K. (2018, 6 maart). Getting Familiar With The Awesome Repository Pattern. Geraadpleegd op 28 november 2019, van https://dev.to/kylegalbraith/getting-familiar-with-the-awesome-repository-pattern--1ao3

Programming with Mosh. (2015, 15 oktober). Repository Pattern with C# and Entity Framework, Done Right | Mosh. Geraadpleegd op 28 november 2019, van https://www.youtube.com/watch?time_continue=1&v=rtXpYpZdOzM&feature=emb_logo

## Sensitive Data Exposure
Editor in Chief. (2017, 14 januari). 8 Pros and Cons of Asymmetric Encryption. Geraadpleegd op 28 november 2019, van https://greengarageblog.org/8-pros-and-cons-of-asymmetric-encryption

Fox-IT. (z.d.). Penetration testing - Fox-IT. Geraadpleegd op 28 november 2019, van https://www.fox-it.com/nl/diensten/testing/penetration-testing/

Microsoft. (2017, 30 maart). Generating Keys for Encryption and Decryption. Geraadpleegd op 28 november 2019, van https://docs.microsoft.com/en-us/dotnet/standard/security/generating-keys-for-encryption-and-decryption

Norton. (z.d.). What is a man-in-the-middle attack? Geraadpleegd op 28 november 2019, van https://us.norton.com/internetsecurity-wifi-what-is-a-man-in-the-middle-attack.html

OWASP. (2018, 1 januari). Top 10-2017 A3-Sensitive Data Exposure - OWASP. Geraadpleegd op 28 november 2019, van https://www.owasp.org/index.php/Top_10-2017_A3-Sensitive_Data_Exposure

Portswigger. (z.d.). What is SQL Injection? Tutorial & Examples. Geraadpleegd op 28 november 2019, van https://portswigger.net/web-security/sql-injection

Suiker, P. (2018, 25 oktober). Wat is een Hash? - simpele uitleg. Geraadpleegd op 28 november 2019, van https://allesovercrypto.nl/blog/hash-simpele-uitleg


