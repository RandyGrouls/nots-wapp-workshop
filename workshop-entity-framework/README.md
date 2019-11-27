# Workshop Entity Framework

In deze workshop gaan wij het Entity Framework implementeren samen met een database.

## Database Setup

Begin met het openen van `Microsoft SQL Server Management Studio` en log in via `SQL Server Authentication`en importeer `DBFirstDemo.bacpac`.
Zoals je kan zien staat in deze database 3 tabellen, een genaamd `listings`, een genaamd `neighbourhoods` en een genaamd `reviews`.

## Project Setup

Om te beginnen open `Visual Studio Preview`, kies dan `Create a new project`. 

Maak dan een `C# .NET Core Console app` aan en noem de app EntityWorkshop. 

Ga naar `Tools > NuGet Package Manager > Manage NuGet Packages for Solution` en ga naar `Browse` en type in de searchbar `microsoft.entityframeworkcore` en zorg ervoor dat `Include prerelease` aangevinkt is.

Selecteer het eerste resultaat vink dan `EntityWorkshop` aan, zorg er ook voor dat de versie op `Latest prerelease` staat, wanneer dit gedaan is klik op install en accepteer de prompts.

Herhaal vervolgens dit process met `microsoft.entityframeworkcore.tools` en `microsoft.entityframeworkcore.sqlserver`.

## Project Packages

Ga naar `Tools > NuGet Package Manager > Package Manager Console`, voer hier vervolgens `Scaffold-DbContext "Server=[SERVER_NAME];Database=DBFirstDemo;` vervang `[SERVER_NAME]` met de naam van jouw database servernaam, in dit geval waarschijnlijk `localhost`.
Plak hierna het volgende achter het eerste commando: `Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir [DIRECTORY_NAME]` hierbij verplaats je `[DIRECTORY_NAME]` met een naam naar keuze, wij gebruiken in ons geval de root directory en laten `-OutputDir [DIRECTORY_NAME]` weg uit het commando.

Wanneer dit is uitgevoerd zijn er een aantal bestanden gegenereert voor ons, deze files zijn onze gemapte database tabellen.

## Praten met de database

Om queries te kunnen schrijven naar de database moeten wij nog enkele stappen zetten, deze stappen bevinden zich binnen de `Context class`, in ons geval heet deze `DBFirstDemoContext.cs`.
Open dit bestand, zoals je kan zien bevat dit bestand 3 `DBSet<TEntity>'s`. Deze DBSet's zorgen ervoor dat onze maps entities worden en gedragen zich als een collectie, dit maakt het mogelijk om LINQ queries uit te voeren, wij gaan er ook een paar implementeren.
