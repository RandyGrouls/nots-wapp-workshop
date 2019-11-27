# Workshop Entity Framework

In deze workshop gaan wij het Entity Framework implementeren samen met een database.

## Database Setup

Begin met het openen van `Microsoft SQL Server Management Studio` en log in via `SQL Server Authentication`en importeer `DBFirstDemo.bacpac`.
[image]
Zoals je kan zien staat in deze database 3 tabellen, een genaamd `listings`, een genaamd `neighbourhoods` en een genaamd `reviews`.
[image]

## Project Setup

Om te beginnen open `Visual Studio Preview`, kies dan `Create a new project`. 
[image]
Maak dan een `C# .NET Core Console app` aan en noem de app EntityWorkshop. 
[image]
Ga naar `Tools > NuGet Package Manager > Manage NuGet Packages for Solution` en ga naar `Browse` en type in de searchbar `microsoft.entityframeworkcore` en zorg ervoor dat `Include prerelease` aangevinkt is.
[image]
Selecteer het eerste resultaat vink dan `EntityWorkshop` aan, zorg er ook voor dat de versie op `Latest prerelease` staat, wanneer dit gedaan is klik op install en accepteer de prompts.
[image]
Herhaal vervolgens dit process met `microsoft.entityframeworkcore.tools` en `microsoft.entityframeworkcore.sqlserver`.

## Project Packages

Ga naar `Tools > NuGet Package Manager > Package Manager Console`, voer hier vervolgens `Scaffold-DbContext "Server=[SERVER_NAME];Database=DBFirstDemo;` vervang `[SERVER_NAME]` met de naam van jouw database servernaam, in dit geval waarschijnlijk `localhost`.
Plak hierna het volgende achter het eerste commando: `Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir [DIRECTORY_NAME]` hierbij verplaats je `[DIRECTORY_NAME]` met een naam naar keuze, wij gebruiken in ons geval de root directory en laten `-OutputDir [DIRECTORY_NAME]` weg uit het commando.
[image]
Wanneer dit is uitgevoerd zijn er een aantal bestanden gegenereert voor ons, deze files zijn onze gemapte database tabellen.

## Praten met de database

Om queries te kunnen schrijven naar de database moeten wij nog enkele stappen zetten, deze stappen bevinden zich binnen de `Context class`, in ons geval heet deze `DBFirstDemoContext.cs`.
Open dit bestand, zoals je kan zien bevat dit bestand 3 `DBSet<TEntity>'s`. Deze DBSet's zorgen ervoor dat onze maps entities worden en gedragen zich als een collectie, dit maakt het mogelijk om LINQ queries uit te voeren, wij gaan er ook een paar implementeren.
[image]


Ga naar `Program.cs` en binnen de Main voer het volgende in:

We beginnen eerst met het maken van een nieuwe context class instantie.
[image]
Hierna maken we een neighbourhood aan met de naam NewNeighbourhood en voegen deze toen via onze context class aan de Neigbourhoods map, hierna slaan wij de wijzigingen op.
[image]

Nu gaan wij enkele CRUD operaties uitvoeren doormiddel van LINQ.
We beginnen met het maken van een SELECT query, in LINQ is dit een `Find`. Zoals je kan zien in de afbeelding hieronder wordt er gekeken in de context.Neigbourhood naar het NeigbourhoodId, deze wordt vervolgens in de variabel gezet en kan later gebruikt worden om het resultaat in dit geval in de console te gebruiken.
[image]

Nu dat wij een `Find` hebben uitgevoerd willen wij een SELECT uitvoeren maar meerdere resultaten ophalen in een query. Dit gaat doormiddel van `Where`, in dit voorbeeld zoeken wij naar alle Neighbourhoods die de letter "a" bevatten.
[image]

Nu pakken wij de Neighbourhood die wij met de eerste LINQ query hebben opgehaald en veranderen de naam van de Neighbourhood.
[image]

En omdat wij een aanpassing maken moeten wij deze weer opslaan.
[image]

Als afsluiting gaan wij deze Neighbourhoods ook weer verwijderen. Om een enkele te verwijderen gebruik je `Remove` en voor meerdere gebruik je `RemoveRange`, deze voeren wij weer uit via de context en geven de variabelen mee die wij eerder hebben opgehaald via de `Find` en `Where` queries, en natuurlijk omdat we aanpassingen maken moeten we weer een `SaveChanges` doen.
[image]
