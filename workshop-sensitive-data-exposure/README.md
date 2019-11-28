# Workshop Sensitive Data Exposure
In deze workshop wordt er introductie gegeven over Sensitive Data Exposure en hoe ermee wordt omgegaan.

## Sensitive Data Exposure
Voor .NET Core heeft Microsoft een standaard library System.Security.Cryptography ontwikkeld, waarin alle algoritmes van cryptografie geïmplementeerd zijn. Dus wordt er een workshop gehouden over het implementeren van één van de beschikbare cryptografieën. In dit geval wordt er hashing toegepast bij meerdere wachtwoorden en hoe het mogelijk is om het kraken van hashing te minimaliseren.
Implementatie van hashing
1.	Maak een nieuwe ConsoleApp aan binnen de Visual Studio Preview.
2.	Voeg een statische methode HashPassword(string password) binnen de klasse Program.
private static string HashPassword(string password)
        {
            using var sha256 = SHA256.Create();
            var hashedBytes = sha256.ComputeHash(Encoding.UTF8.GetBytes(password));
            return BitConverter.ToString(hashedBytes).Replace("-", "").ToLower();
        }
3.	Voeg een string array toe van de top 10 meest gebruikte wachtwoorden in de Main-methode en maak van twee wachtwoorden duplicaten. In totaal zijn er 12 wachtwoorden, waarvan 10 wachtwoorden uniek zijn. 
4.	Maak een for-loop van de string array van wachtwoorden in combinatie met de gehashte wachtwoorden en print deze in de console. Wat valt je op? 
5.	Gehashte wachtwoorden kunnen achterhaald worden door een bruteforce uit te voeren met een lijst van alle mogelijke wachtwoorden. In dit geval wordt er gebruik gemaakt van een online password hash cracker. Kopieer één van de gehashte wachtwoorden in de volgende website: https://crackstation.net/.
6.	Voeg een statische methode GenerateRandomSalt() binnen de klasse Program.
private static string GenerateRandomSalt()
        {
            byte[] bytes = new byte[128 / 8];
            using var keyGenerator = RandomNumberGenerator.Create();
            keyGenerator.GetBytes(bytes);
            return BitConverter.ToString(bytes).Replace("-", "").ToLower();
        }
7.	Voeg bij de for-loop van wachtwoorden een salt toe bij de hashing van iedere wachtwoord. 
