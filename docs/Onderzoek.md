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
Om te kunnen communiceren met een database, is een persistence framework nodig. Hiermee kun je CRUD operaties uitvoeren op een database. Het is mogelijk om dit zelf te schrijven met [ADO.NET](https://nl.wikipedia.org/wiki/ADO.NET) classes zoals `SqlConnection`, `SqlCommand` en `SqlDataReader`. Maar dit kost erg veel tijd omdat je dan zelf deze data in de database moet inlezen en omzetten naar bruikbare objecten.

Hiervoor is het Entity Framework ontwikkeld. Het Entity Framework is een open-source ORM framework voor .NET applicaties dat developers helpt met het communiceren met een database en ervoor zorgt dat alle bovenstaande acties niet zelf meer hoeven worden uitgevoerd. 

<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/EF.PNG" alt="Entity Framework" width="250px"><br/>
<em>Figuur 1: Entity Framework structuur</em>

In bovenstaand figuur is te zien waar het Entity Framework pas in de structuur van de applicatie. Er kan gewerkt worden met data met gebruik van objecten of domein specifieke classes zonder te hoeven focussen op de onderliggende database waar deze data staat opgeslagen. Hierdoor kan er op een hoger abstractieniveau omgegaan worden met deze data en is het mogelijk om applicaties te ontwikkelen met minder code en welke gemakkelijker te onderhouden zijn.

## Hoe werkt het Entity Framework?
Het Entity Framework werkt doormiddel van het mappen van een class naar de database. Deze class heet een `Context class`, en wordt altijd afgeleid van `DbContext`. In deze context classes worden domein specifieke classes (Entities) gebruikt doormiddel van een generic genaamd `DbSet<TEntity>`

<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/dbset-example.PNG" alt="DBset example" width="400px"><br/>
<em>Figuur 2: Voorbeeld DbSet</em>

Om een entity te maken maak je eerst een class aan dat een databasetabel mapped. Als dit gedaan is, moet het eerst in een context class gezet worden doormiddel van de voorheen besproken `DbSet<TEntity>`. Wanneer deze context en entity classes aangemaakt zijn kunnen er queries geschreven worden. Deze queries worden geschreven met LINQ queries. Dit is de query taal die gebruikt wordt binnen .NET. 

<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/linq-query.PNG" alt="Entity Framework"><br/>
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

# Repository Pattern
## Wat is het Repository Pattern?
Het Repository Pattern is een veel gebruikt pattern om duplicatie van data access logica binnen een applicatie te voorkomen. Denk hierbij bijvoorbeeld aan het communiceren met een database. Het biedt een abstractie van gegevens zodat de applicatie kan werken met een interface dat de interface van een collection van entities benadert. Het uitvoeren van CRUD operaties op deze collection gebeurd dan via een aantal simpele methoden, zonder bezig te hoeven zijn met database gerelateerde taken zoals verbinding maken en queries verwerken. Onderstaand de voordelen van het Repository Pattern:
- Het voorkomt het dupliceren van querylogica.
- Het ontkoppelt de applicatie van persistence frameworks (zoals bijvoorbeeld [Entity Framework](#entity-framework)).
- Het maakt de applicatie beter testbaar.

## Hoe werkt het Repository Pattern? 
Het repository pattern zorgt voor een extra laag abstractie over de data access laag, in ons geval houdt dat in dat wij met behulp van het repository pattern extra abstractie willen bij het maken van CRUD operaties. 

Om het repository pattern toe te passen is er een repository nodig, en dit interface is een interface voor een verzameling objecten in het geheugen. Vervolgens is er een klasse nodig dat dit interface implementeert dat een generic `DbContext` bevat. Deze kan vervolgens ook in andere applicaties gebruikt worden.

In het geval van onze applicatie zal er voor elk entity een repository aangemaakt worden dat dit generic interface implementeert. Hier kunnen dan extra methoden worden toegevoegd die afhankelijk zijn van de toepassingscontext. Als laatste is er dan nog een klasse nodig die de functionaliteit van deze extra methoden bevat.

<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/repository-diagram.PNG" alt="Entity Framework" width="500px"><br/>
<em>Figuur 4: Voorbeeld repository implementatie</em>

Merk op dat er bovenstaand geen methode aanwezig is om een object te updaten. Dit is namelijk een veelgemaakte fout. Als een object aangepast moet worden is het de bedoeling dat deze uit de collectie wordt gehaald met de `Get(id)` methode en vervolgens wordt aangepast. Nogmaals, een repository moet zich gedragen als een collectie van objecten. een `List` heeft geen methode om een object te updaten, daarom heeft een repository dit ook niet.

Om de wijzigingen aan deze collectie op te slaan in een database, wordt gebruik gemaakt van het Unit of Work Pattern. Een Unit of Work houdt een of meerdere soorten collecties bij van objecten waar CRUD operaties op zijn uitgevoerd. Een Unit of Work bevat een `Save()` methode dat verantwoordelijk is voor het opslaan van deze wijzigingen in een database.

## Hoe implementeer je het Repository Pattern?
# Bronnen
## Entity Framework
https://www.entityframeworktutorial.net/what-is-entityframework.aspx

https://www.youtube.com/watch?time_continue=3&v=ZX7_12fwQLU&feature=emb_logo

## Repository Pattern
https://www.youtube.com/watch?time_continue=1&v=rtXpYpZdOzM&feature=emb_logo

https://www.infragistics.com/community/blogs/b/dhananjay_kumar/posts/how-to-implement-the-repository-pattern-in-asp-net-mvc-application

https://dev.to/kylegalbraith/getting-familiar-with-the-awesome-repository-pattern--1ao3

https://medium.com/@pererikbergman/repository-design-pattern-e28c0f3e4a30