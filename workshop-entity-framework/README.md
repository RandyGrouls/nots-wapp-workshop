# Workshop Entity Framework

In deze workshop gaan wij het Entity Framework implementeren samen met een database.

## Database Setup

Begin met het openen van `Microsoft SQL Server Management Studio` en log in via `SQL Server Authentication` en importeer `DBFirstDemo.bacpac`.

Zoals je kan zien staat in deze database 3 tabellen, een genaamd `listings`, een genaamd `neighbourhoods` en een genaamd `reviews`. </br>
<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/db-example.png">

## Project Setup

Om te beginnen open `Visual Studio Preview`, kies dan `Create a new project`. </br>
<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/NewProject.png">

Maak dan een `ASP.NET Core Web Application` aan en noem de app EntityWorkshop. Kies `Web Application (Model-View-Controller)` als template.</br>
<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/CreateASPCoreApp.PNG">

<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/CreateMVCApp.PNG">

Ga naar `Tools > NuGet Package Manager > Manage NuGet Packages for Solution` en ga naar `Browse` en type in de searchbar `microsoft.entityframeworkcore` en zorg ervoor dat `Include prerelease` aangevinkt is.

Selecteer het eerste resultaat vink dan `EntityWorkshop` aan, zorg er ook voor dat de versie op `Latest prerelease` staat, wanneer dit gedaan is klik op install en accepteer de prompts. </br>
<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/install-entity-framework.png">

Herhaal vervolgens dit process met `microsoft.entityframeworkcore.tools` en `microsoft.entityframeworkcore.sqlserver`.

## Project Packages

Ga naar `Tools > NuGet Package Manager > Package Manager Console`, voer hier vervolgens `Scaffold-DbContext "Server=[SERVER_NAME];Database=DBFirstDemo;` vervang `[SERVER_NAME]` met de naam van jouw database servernaam, in dit geval waarschijnlijk de naam van jouw laptop.
Plak hierna het volgende achter het eerste commando: `Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir [DIRECTORY_NAME]` hierbij kun je `[DIRECTORY_NAME]`vervangen met een naam naar keuze, in dit voorbeeld gebruiken wij gebruiken de `Models` directory. </br>
<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/ConsolePackages.png">

Wanneer dit is uitgevoerd zijn er een aantal bestanden gegenereert voor ons, deze files zijn onze gemapte database tabellen.

## Praten met de database
Om CRUD operaties uit te kunnen voeren op de database moeten er nog enkele stappen gezet worden, maar eerst bekijken wij `DBFirstDemoContext.cs`.
Open dit bestand, zoals je kan zien bevat dit bestand 3 `DBSet<TEntity>'s`. </br>
<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/DBSet.png">

Deze DBSet's zorgen ervoor dat onze maps entities worden en gedragen zich als een collectie, dit maakt het mogelijk om LINQ queries uit te voeren, wij gaan er ook een paar implementeren.

Ga naar `Startup.cs` en vervang de `ConfigureServices` methode:

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    //Registreer Context class als service
    services.AddDbContext<DBFirstDemoContext>(); 
}
```
Voeg een nieuwe Controller toe voor de `Neighbourhoods` model.

<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/AddController.png" width="700px">

Kies `MVC Controller with views, using Entity Framework` en selecteer onderstaande waarden.

<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/CreateController.PNG" width="550px">

Ga naar `Startup.cs` en vervang de default controller `Home` door `Neighbourhoods`.

<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/ReplaceController.PNG" width="700px">

Hierna kunnen wij de gegenereerde controller bekijken, open `NeighbourhoodsController.cs`. Zoals je kan zien staan hier enkele CRUD operaties die al gegenereert zijn voor het project. In deze controller class kan je de CRUD operaties zetten voor het bijbehorende model. De gegenereerde queries kunnen als basis gebruikt worden voor eventuele andere queries.

<!-- We beginnen eerst met het maken van een nieuwe context class instantie. </br>
<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/NewContext.png">

Hierna maken we een neighbourhood aan met de naam NewNeighbourhood en voegen deze toen via onze context class aan de Neigbourhoods map, hierna slaan wij de wijzigingen op. </br>
<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/NewNeighbourhood.png">

Nu gaan wij enkele CRUD operaties uitvoeren doormiddel van LINQ.
We beginnen met het maken van een SELECT query, in LINQ is dit een `Find`. Zoals je kan zien in de afbeelding hieronder wordt er gekeken in de context.Neigbourhood naar het NeigbourhoodId, deze wordt vervolgens in de variabel gezet en kan later gebruikt worden om het resultaat in dit geval in de console te gebruiken. </br>
<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/Find.png">

Nu dat wij een `Find` hebben uitgevoerd willen wij een SELECT uitvoeren maar meerdere resultaten ophalen in een query. Dit gaat doormiddel van `Where`, in dit voorbeeld zoeken wij naar alle Neighbourhoods die de letter "a" bevatten. </br>
<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/Where.png">

Nu pakken wij de Neighbourhood die wij met de eerste LINQ query hebben opgehaald en veranderen de naam van de Neighbourhood.
En omdat wij een aanpassing maken moeten wij deze weer opslaan. </br>
<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/ChangeName.png">

Als afsluiting gaan wij deze Neighbourhoods ook weer verwijderen. Om een enkele te verwijderen gebruik je `Remove` en voor meerdere gebruik je `RemoveRange`, deze voeren wij weer uit via de context en geven de variabelen mee die wij eerder hebben opgehaald via de `Find` en `Where` queries, en natuurlijk omdat we aanpassingen maken moeten we weer een `SaveChanges` doen. </br>
<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/Remove.png">

Ga nu terug naar de database en klik met de rechtermuisknop op de Neighbourhoods tabel en `Select Top 1000`, zoals je kan zien staan er nu nog maar 13 Neighbourhoods in.

Als je wilt dat het process van de queries getoond wordt kan je via Console.WriteLine() de query resultaat variabelen erin zetten. </br>
<img src="https://github.com/RandyGrouls/nots-wapp-workshop/blob/master/docs/afbeeldingen/FindWrite.png"> -->
