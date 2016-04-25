<properties
	pageTitle="Logica di ripetizione dei tentativi in C# per database SQL | Microsoft Azure"
	description="L’esempio C# include una logica di ripetizione dei tentativi per un'interazione affidabile con database SQL di Azure."
	services="sql-database"
	documentationCenter=""
	authors="annemill"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/08/2016"
	ms.author="annemill"/>


# Codice di esempio: logica di ripetizione tentativi in C# per la connessione a Database SQL



[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]



In questo argomento viene fornito un esempio di codice C# che illustra la logica di ripetizione tentativi personalizzata. La logica di ripetizione dei tentativi è progettata per elaborare correttamente gli errori temporanei o i *guasti temporanei* che tendono a risolversi se il programma attende qualche secondo ed effettua un nuovo tentativo.


Le classi ADO.NET per la connessione all'istanza locale di Microsoft SQL Server possono essere usate anche per la connessione al database SQL di Azure. Tuttavia, le classi ADO.NET non forniscono autonomamente tutta la solidità e l’affidabilità necessarie per l’uso in un ambiente di produzione. Il programma client può incorrere in guasti temporanei da cui deve poter eseguire un ripristino automatico e continuare a funzionare.


Alcuni esempi di guasti temporanei sono:


- Una connessione su Internet è soggetta a brevi interruzioni di rete dopo le quali è possibile ricreare la connessione.
- Il cloud computing implica il bilanciamento del carico che può bloccare brevemente i tentativi di connessione o di query.


## R. Identificare gli errori temporanei


Il programma deve distinguere gli errori temporanei dagli errori persistenti. Se il programma presenta un errore di ortografia del nome del database di destinazione, l'errore "Database non trovato" viene mantenuto e si presenta ogni volta che si esegue nuovamente il programma.

L'elenco dei numeri di errore classificati come guasti temporanei è disponibile all'indirizzo:

- [Messaggi di errore per programmi client di Database SQL](sql-database-develop-error-messages.md#bkmk_connection_errors)
  - Vedere la sezione superiore su *Errori temporanei, errori di perdita della connessione*.


L'esempio di codice C# riportato più avanti in questo argomento elenca i numeri degli errori temporanei in un campo denominato **TransientErrorNumbers**.



## B. Esempio di codice C#


L'esempio di codice C# nel presente argomento contiene la logica di ripetizione tentativi e il rilevamento personalizzato per gestire gli errori temporanei. Nell'esempio si presuppone che sia installato .NET Framework 4.5.1 o versione successiva.


L'esempio di codice segue alcune linee guida di base o indicazioni che si applicano indipendentemente dalla tecnologia utilizzata per interagire con Database SQL di Azure. È possibile visualizzare le indicazioni generali in:


- [Connessione al database SQL: collegamenti, procedure consigliate e linee guida per la progettazione](sql-database-connect-central-recommendations.md)


L'esempio di codice C# è costituito da un file denominato Program.cs. Il relativo codice viene specificato nella sezione successiva.


### B.1 Acquisire e compilare il codice di esempio


È possibile compilare l'esempio con i passaggi seguenti:


1. Nell’[edizione gratuita Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs), creare un nuovo progetto dal modello di applicazione Console C#.
  - File > Nuovo > Progetto > Installati > Modelli > Visual C# > Windows > Desktop classico > Applicazione console
  - Assegnare al progetto il nome **RetryAdo2**.
2. Aprire il riquadro Esplora soluzioni.
  - Visualizzare il nome del progetto.
  - Visualizzare il nome del file Program.cs.
3. Salvare il file Program.cs.
4. Sostituire completamente il contenuto del file Program.cs con il codice nel blocco di codice seguente.
5. Fare clic sul menu Compilazione > Compila soluzione.


#### Il codice sorgente C# da incollare


Incollare questo codice nel file **Program.cs**.


È necessario modificare le stringhe per nome del server, password e così via. È possibile trovare queste stringhe nel metodo denominato **GetSqlConnectionStringBuilder**.



```
using System;   // C#
using G = System.Collections.Generic;
using S = System.Data.SqlClient;
using T = System.Threading;
   
namespace RetryAdo2
{
   public class Program
   {
      static public int Main(string[] args)
      {
         bool succeeded = false;
         int totalNumberOfTimesToTry = 4;
         int retryIntervalSeconds = 10;
   
         for (int tries = 1;
            tries <= totalNumberOfTimesToTry;
            tries++)
         {
            try
            {
               if (tries > 1)
               {
                  Console.WriteLine
                     ("Transient error encountered. Will begin attempt number {0} of {1} max...",
                     tries, totalNumberOfTimesToTry
                     );
                  T.Thread.Sleep(1000 * retryIntervalSeconds);
                  retryIntervalSeconds = Convert.ToInt32
                     (retryIntervalSeconds * 1.5);
               }
               AccessDatabase();
               succeeded = true;
               break;
            }
   
            catch (S.SqlException sqlExc)
            {
               if (TransientErrorNumbers.Contains
                  (sqlExc.Number) == true)
               {
                  Console.WriteLine("{0}: transient occurred.", sqlExc.Number);
                  continue;
               }
               else
               {
                  Console.WriteLine(sqlExc);
                  succeeded = false;
                  break;
               }
            }
   
            catch (TestSqlException sqlExc)
            {
               if (TransientErrorNumbers.Contains
                  (sqlExc.Number) == true)
               {
                  Console.WriteLine("{0}: transient occurred. (TESTING.)", sqlExc.Number);
                  continue;
               }
               else
               {
                  Console.WriteLine(sqlExc);
                  succeeded = false;
                  break;
               }
            }
   
            catch (Exception Exc)
            {
               Console.WriteLine(Exc);
               succeeded = false;
               break;
            }
         }
   
         if (succeeded == true)
         {
            return 0;
         }
         else
         {
            Console.WriteLine("ERROR: Unable to access the database!");
            return 1;
         }
      }
   
      /// <summary>
      /// Connects to the database, reads,
      /// prints results to the console.
      /// </summary>
      static public void AccessDatabase()
      {
         //throw new TestSqlException(4060); //(7654321);  // Uncomment for testing.
   
         using (var sqlConnection = new S.SqlConnection
                  (GetSqlConnectionString()))
         {
            using (var dbCommand = sqlConnection.CreateCommand())
            {
               dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";
   
               sqlConnection.Open();
               var dataReader = dbCommand.ExecuteReader();
   
               while (dataReader.Read())
               {
                  Console.WriteLine("{0}\t{1}",
                     dataReader.GetString(0),
                     dataReader.GetString(1));
               }
            }
         }
      }
   
      /// <summary>
      /// You must edit the four 'my' string values.
      /// </summary>
      /// <returns>An ADO.NET connection string.</returns>
      static private string GetSqlConnectionString()
      {
         // Prepare the connection string to Azure SQL Database.
         var sqlConnectionSB = new S.SqlConnectionStringBuilder();
   
         // Change these values to your values.
         sqlConnectionSB.DataSource = "tcp:myazuresqldbserver.database.windows.net,1433"; //["Server"]
         sqlConnectionSB.InitialCatalog = "MyDatabase"; //["Database"]
   
         sqlConnectionSB.UserID = "MyLogin";  // "@yourservername"  as suffix sometimes.
         sqlConnectionSB.Password = "MyPassword";
         sqlConnectionSB.IntegratedSecurity = false;
   
         // Adjust these values if you like. (ADO.NET 4.5.1 or later.)
         sqlConnectionSB.ConnectRetryCount = 3;
         sqlConnectionSB.ConnectRetryInterval = 10;  // Seconds.
   
         // Leave these values as they are.
         sqlConnectionSB.IntegratedSecurity = false;
         sqlConnectionSB.Encrypt = true;
         sqlConnectionSB.ConnectTimeout = 30;
   
         return sqlConnectionSB.ToString();
      }
   
      static public G.List<int> TransientErrorNumbers =
         new G.List<int> { 4060, 40197, 40501, 40613,
         49918, 49919, 49920, 11001 };
   }
   
   /// <summary>
   /// For testing retry logic, you can have method
   /// AccessDatabase start by throwing a new
   /// TestSqlException with a Number that does
   /// or does not match a transient error number
   /// present in TransientErrorNumbers.
   /// </summary>
   internal class TestSqlException : ApplicationException
   {
      internal TestSqlException(int testErrorNumber)
      { this.Number = testErrorNumber; }
   
      internal int Number
      { get; set; }
   }
}
```



## C. Eseguire il programma


L’eseguibile **RetryAdo2.exe** non invia parametri. Per eseguire il file EXE:

1. Aprire una finestra della console in cui è stato compilato il file binario RetryAdo2.exe.
2. Eseguire RetryAdo2.exe senza parametri di input.



```
database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535
```



## D. Come testare la logica di ripetizione dei tentativi

Per testare la logica di ripetizione dei tentativi è possibile simulare un errore temporaneo in diversi modi.


### D.1 Generare un'eccezione di test

L'esempio di codice include:

- Una seconda classe di piccole dimensioni denominata **TestSqlException**, con una proprietà denominata **Number**.
- `//throw new TestSqlException(4060);`, da cui è possibile rimuovere il commento.

Se si rimuove il commento da throw e si ricompila, l'esecuzione successiva di **RetryAdo2.exe** restituisce un output simile a quanto segue.



```
[C:\_MainW\VS15\RetryAdo2\RetryAdo2\bin\Debug]
>> RetryAdo2.exe
4060: transient occurred. (TESTING.)
Transient error encountered. Will begin attempt number 2 of 4 max...
4060: transient occurred. (TESTING.)
Transient error encountered. Will begin attempt number 3 of 4 max...
4060: transient occurred. (TESTING.)
Transient error encountered. Will begin attempt number 4 of 4 max...
4060: transient occurred. (TESTING.)
ERROR: Unable to access the database!

[C:\_MainW\VS15\RetryAdo2\RetryAdo2\bin\Debug]
>>
```



#### Eseguire nuovamente il test con un errore persistente


Per verificare se il codice gestisce correttamente gli errori persistenti, rieseguire il test precedente facendo attenzione a non usare il numero di un errore temporaneo effettivo, come 4060. Usare invece il numero fittizio 7654321. Il programma deve trattarlo come un errore persistente e ignorare qualsiasi ripetizione dei tentativi.



### D.2 Disconnettersi dalla rete

1. Disconnettere il computer client dalla rete.
  - In un computer desktop scollegare il cavo di rete.
  - In un computer portatile premere la combinazione di tasti funzione che disattiva la scheda di rete.
2. Avviare RetryAdo2.exe e attendere che venga visualizzato il primo errore temporaneo, probabilmente il numero 11001.
3. Riconnettersi alla rete mentre RetryAdo2.exe è ancora in esecuzione.
4. La console segnalerà l'esito positivo di un tentativo successivo.


### D.3 Specificare temporaneamente il nome del server in modo errato

1. Aggiungere temporaneamente il numero di errore 40615 a **TransientErrorNumbers** e ricompilare.
2. Impostare un punto di interruzione sulla riga `new S.SqlConnectionStringBuilder()`.
3. Usare la funzionalità *Modifica e continuazione* per specificare intenzionalmente in modo errato il nome del server un paio di righe più in basso.
  - Consentire l'esecuzione del programma e tornare al punto di interruzione.
  - Si verifica l'errore 40615.
4. Correggere l'errore di ortografia.
5. Consentire l'esecuzione e il completamento del programma.
6. Rimuovere il numero 40615 e ricompilare.


## E. Collegamenti correlati

- [Esempi di codice di avvio rapido del client per il database SQL](sql-database-develop-quick-start-client-code-samples.md)
- [Prova del database SQL: usare C# per creare un database SQL con la libreria del database SQL per .NET](sql-database-get-started-csharp.md)

<!---HONumber=AcomDC_0413_2016-->