<properties
	pageTitle="Ripetizione dei tentativi EntLib per connettersi al database SQL | Microsoft Azure"
	description="La libreria Enterprise è progettata per semplificare diverse attività per i programmi client dei servizi cloud, inclusa l'integrazione della logica di ripetizione dei tentativi per gli errori temporanei."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor="" />


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="genemi"/>


# Codice di esempio: logica di ripetizione tentativi della libreria Enterprise 6, in C&#x23; per la connessione al database SQL

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


In questo argomento viene presentato un esempio di codice completo che illustra la libreria Enterprise (EntLib). EntLib semplifica molte attività per i programmi client che interagiscono con servizi cloud come il database SQL di Azure. L'esempio è incentrato sull'importante attività di inclusione della logica di ripetizione dei tentativi per gli errori temporanei.


Le classi EntLib sono progettate per distinguere due categorie di errori di runtime:

- Errori che non possono essere corretti automaticamente, ad esempio un nome di server errato.
- Errori temporanei, ad esempio un server che sospende per alcuni secondi l'accettazione di nuove connessioni mentre viene eseguito il bilanciamento del carico del sistema Azure.


La libreria Enterprise 6 (EntLib60) è la versione più recente ed è stata rilasciata ad aprile 2013.

- Microsoft ha rilasciato il codice sorgente al pubblico.
- Microsoft non prevede di gestire ulteriormente il codice sorgente.


## Prerequisiti


#### .NET Framework 4.0 o versione successiva


Deve essere installato Microsoft .NET Framework 4.0 o versione successiva. Al momento della stesura di questo articolo, è disponibile la versione 4.6 ed è consigliabile la versione più recente.


#### Edizione gratuita Visual Studio Community


È necessario un sistema per compilare il codice sorgente di questo esempio. È possibile installare l'[edizione gratuita di Microsoft Visual Studio *Community*](http://www.visualstudio.com/products/free-developer-offers-vs.aspx).


Potrebbe essere necessario registrare l'indirizzo di posta elettronica con MSDN. La procedura è simile alla seguente:


1. [Visitare MSDN](http://msdn.microsoft.com/).
2. Fare clic su **Abbonamenti MSDN** nella parte superiore.
3. Fare clic su **Iscriviti ora**.
4. Compilare il modulo con le informazioni richieste.
5. Fare clic su **Crea un account** nella parte inferiore.


#### Libreria Enterprise 6 (EntLib60)


Modalità per l'installazione di EntLib60:


- Usare la funzionalità per la gestione dei pacchetti *NuGet* in Visual Studio:
 - In NuGet cercare **enterpriselibrary**.


- Nell'[argomento principale della documentazione per EntLib60](http://msdn.microsoft.com/library/dn169621.aspx) individuare la riga denominata **Download**, quindi fare clic su [Microsoft Enterprise Library 6](http://go.microsoft.com/fwlink/?linkid=290898) per scaricare i file di assembly binari con estensione DLL.


EntLib60 dispone di numerosi file di assembly con estensione .DLL il cui nome inizia con lo stesso prefisso **Microsoft.Practices.EnterpriseLibrary.&#x2a;.dll**, ma per questo esempio di codice sono necessari solo i due assembly seguenti:

- Microsoft.Practices.EnterpriseLibrary.**TransientFaultHandling**.dll
- Microsoft.Practices.EnterpriseLibrary.**TransientFaultHandling.Data**.dll


## Interazione tra le classi EntLib


Le classi EntLib vengono utilizzate per creare altre classi EntLib. In questo esempio di codice, la sequenza di creazione e d'uso è quella illustrata di seguito:


1. Creare un oggetto **ExponentialBackoff**.
2. Creare un oggetto **SqlDatabaseTransientErrorDetectionStrategy**.
3. Creare un oggetto **RetryPolicy**. I parametri di input sono:
 - Oggetto **ExponentialBackoff**.
 - Oggetto **SqlDatabaseTransientErrorDetectionStrategy**.
4. Creare un oggetto **ReliableSqlConnection**. I parametri di input sono:
 - Un oggetto **stringa** con il nome del server e altre informazioni di connessione.
 - Oggetto **RetryPolicy**
5. Chiamare per la connessione, tramite il metodo **RetryPolicy .ExecuteAction**.
6. Chiamare il metodo **ReliableSqlConnection .CreateCommand**.
 - Restituisce un oggetto **System.SqlClient.Data.DbCommand**, parte di ADO.NET.
7. Chiamare per la query, tramite il metodo **RetryPolicy .ExecuteAction**.


## Compilare ed eseguire l'esempio di codice


L'esempio di codice sorgente Program.cs è fornito più avanti in questo argomento. È possibile compilare ed eseguire l'esempio con i passaggi seguenti:


1. In Visual Studio creare un nuovo progetto dal modello di Applicazione console C#.

2. Nel riquadro Esplora soluzioni modificare il file Program.cs quasi vuoto sostituendone il contenuto iniziale con il codice di Program.cs fornito più avanti in questo argomento.

3. Usare il menu Compila > Compila soluzione in Visual Studio per compilare il progetto.

4. In una finestra di comando di cmd.exe eseguire il programma come indicato di seguito. Viene inoltre visualizzato l’output effettivo di un’esecuzione.


```
[C:\MyVS\EntLib60Retry\EntLib60Retry\bin\Debug]
>> EntLib60Retry.exe

database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535

[C:\MyVS\EntLib60Retry\EntLib60Retry\bin\Debug]
>>
```


&nbsp;


## Codice sorgente di Program.cs


Tutto il codice sorgente per questo esempio EntLib è contenuto nel file Program.cs.


```
using     System;   // C#
using G = System.Collections.Generic;
using D = System.Data;
using C = System.Data.SqlClient;
using X = System.Text;
using H = System.Threading;
using Y = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;

namespace EntLib60Retry
{
   class Program
   {
      static void Main(string[] args)
      {
         Program program = new Program();

         if (program.Run(args) == false)
         {
            Console.WriteLine("Something was unable to complete.  :-( ");
         }
      }

      bool Run(string[] _args)
      {
         int retryIntervalSeconds = 10;
         bool returnBool = false;

         this.InitializeEntLibObjects();

         for (int tries = 1; tries <= 3; tries++)
         {
            if (tries > 1)
            {
               H.Thread.Sleep(1000 * retryIntervalSeconds);
               retryIntervalSeconds = Convert.ToInt32(retryIntervalSeconds * 1.5);
            }

            try
            {
               this.reliableSqlConnection = new Y.ReliableSqlConnection(
                  this.sqlConnectionSB.ToString(),
                  this.retryPolicy, null
                  );
               this.retryPolicy.ExecuteAction(this.OpenTheConnection_action);  // Open the connection.

               this.dbCommand = this.reliableSqlConnection.CreateCommand();
               this.dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";

               // We retry connection .Open after transient faults, but
               // we do not retry commands that use the connection.
               this.IssueTheQuery();  // Run the query, loop through results.

               returnBool = true;
               break;
            }

            catch (C.SqlException sqlExc)
            {
               if (false == this.sqlDatabaseTransientErrorDetectionStrategy.IsTransient(sqlExc))
               {
                  throw sqlExc;
               }
            }
         }
         return returnBool;
      }

      void OpenTheConnection()  // Called by .ExecuteAction.
      {
         this.reliableSqlConnection.Open();
      }

      void IssueTheQuery()      // Called by .ExecuteAction.
      {
         X.StringBuilder sBuilder = new X.StringBuilder(256);

         this.dataReader = this.dbCommand.ExecuteReader();

         while (this.dataReader.Read())
         {
            sBuilder.Length = 0;
            sBuilder.Append(this.dataReader.GetString(0));
            sBuilder.Append("\t");
            sBuilder.Append(this.dataReader.GetString(1));

            Console.WriteLine(sBuilder.ToString());
         }
      }

      void InitializeEntLibObjects()
      {
         this.InitializeSqlConnectionStringBuilder();

         this.exponentialBackoff = new Y.ExponentialBackoff(
            "exponentialBackoff",
             3,                    // Maximum number of times to retry, then let exception bubble up.
             TimeSpan.FromMilliseconds(10000), // Minimum interval between retries.
             TimeSpan.FromMilliseconds(30000), // Maximum interval between retries.
             TimeSpan.FromMilliseconds( 2000)  // For random differences in interval between retries.
            );
         this.sqlDatabaseTransientErrorDetectionStrategy =
            new Y.SqlDatabaseTransientErrorDetectionStrategy();
         this.retryPolicy = new Y.RetryPolicy(
               this.sqlDatabaseTransientErrorDetectionStrategy,
               this.exponentialBackoff
               );
         this.OpenTheConnection_action = delegate() { this.OpenTheConnection(); };
      }

      void InitializeSqlConnectionStringBuilder()
      {
         // Prepare the connection string to Azure SQL Database.
         this.sqlConnectionSB = new C.SqlConnectionStringBuilder();

         // Change these values to your values.
         this.sqlConnectionSB["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
         this.sqlConnectionSB["User ID"] = "MyLogin";  // "@yourservername"  as suffix sometimes.
         this.sqlConnectionSB["Password"] = "MyPassword";
         this.sqlConnectionSB["Database"] = "MyDatabase";

         // Leave these values as they are.
         this.sqlConnectionSB["Trusted_Connection"] = false;
         this.sqlConnectionSB["Integrated Security"] = false;
         this.sqlConnectionSB["Encrypt"] = true;
         this.sqlConnectionSB["Connection Timeout"] = 30;
      }

      Program()   // Constructor.
      {
         int[] arrayOfTransientErrorNumbers =
                { 4060, 10928, 10929, 40197, 40501, 40613, 49918, 49919, 49920
                    //,11001   // 11001 for testing, pretend network error is transient.
                    //,18456   // 18456 for testing, purposely misspell database name.
                };
         listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
      }

      // Fields.
      private G.List<int> listTransientErrorNumbers;

      private Y.ReliableSqlConnection reliableSqlConnection;
      private Y.ExponentialBackoff exponentialBackoff;
      private Y.SqlDatabaseTransientErrorDetectionStrategy
                   sqlDatabaseTransientErrorDetectionStrategy;
      private Y.RetryPolicy retryPolicy;

      private Action OpenTheConnection_action;

      private C.SqlConnectionStringBuilder sqlConnectionSB;
      private D.IDbCommand dbCommand;
      private D.IDataReader dataReader;
   }
}
```


&nbsp;


## Collegamenti correlati


- Numerosi collegamenti per ottenere ulteriori informazioni sono disponibili in: [Libreria Enterprise 6, aprile 2013](http://msdn.microsoft.com/library/dn169621.aspx)
 - Questo argomento include un pulsante nella parte superiore che consente di [scaricare il codice sorgente di EntLib60](http://go.microsoft.com/fwlink/p/?LinkID=290898), se si è interessati a visualizzare il codice sorgente.


- Ebook gratuito in formato PDF di Microsoft: [Guida per gli sviluppatori alla libreria Microsoft Enterprise, seconda edizione](http://www.microsoft.com/download/details.aspx?id=41145).


- [Spazio dei nomi Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling](http://msdn.microsoft.com/library/microsoft.practices.enterpriselibrary.transientfaulthandling.aspx)


- [Informazioni di riferimento sulla libreria di classi Enterprise 6](http://msdn.microsoft.com/library/dn170426.aspx)


- [Codice di esempio: logica di ripetizione tentativi in C# per la connessione al database SQL con ADO.NET](sql-database-develop-csharp-retry-windows.md)


- [Esempi di codice di avvio rapido del client per il database SQL](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=AcomDC_0107_2016-->