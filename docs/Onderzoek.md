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

<img src="./afbeeldingen/ef.png" alt="Entity Framework" width="250px"><br/>
<em>Figuur 1: Entity Framework structuur</em>

In bovenstaand figuur is te zien waar het Entity Framework pas in de structuur van de applicatie. Er kan gewerkt worden met data met gebruik van objecten of domein specifieke classes zonder te hoeven concentreren op de onderliggende database waar deze data staat opgeslagen. Hierdoor kan er op een hoger abstractieniveau omgegaan worden met deze data en is het mogelijk om applicaties te ontwikkelen met minder code en welke gemakkelijker te onderhouden zijn.

## Hoe werkt het Entity Framework?
Het Entity Framework werkt doormiddel van het mappen van een class naar de database. Deze class heet een `Context class`, en wordt altijd afgeleid van `DbContext`. In deze context classes worden domein specifieke classes (Entities) gebruikt doormiddel van een generic genaamd `DbSet<TEntity>`

[INSERT DBSET IMAGE HERE].


Om een entity te maken maak je eerst een class aan dat een databasetabel mapped. Als dit gedaan is, moet het eerst in een context class gezet worden doormiddel van de voorheen besproken `DbSet<TEntity>`. Wanneer deze context en entity classes aangemaakt zijn kunnen er queries geschreven worden. Deze queries worden geschreven met LINQ queries. Dit is de query taal die gebruikt wordt binnen .NET. 

[INSERT LINQ QUERY HERE]

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

## Hoe werkt het Repository Pattern?

## Hoe implementeer je het Repository Pattern?
# Bronnen
## Entity Framework
https://www.entityframeworktutorial.net/what-is-entityframework.aspx

https://www.youtube.com/watch?time_continue=3&v=ZX7_12fwQLU&feature=emb_logo

## Repository Pattern
https://www.youtube.com/watch?time_continue=1&v=rtXpYpZdOzM&feature=emb_logo

https://www.infragistics.com/community/blogs/b/dhananjay_kumar/posts/how-to-implement-the-repository-pattern-in-asp-net-mvc-application


