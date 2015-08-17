<properties 
	pageTitle="Codice di esempio: logica di ripetizione tentativi della libreria Enterprise, in C# per la connessione al database SQL| Microsoft Azure"
	description="La libreria Enterprise Library è progettata per semplificare l'operazione di inserimento della logica di ripetizione tentativi per i programmi client che accedono ai servizi cloud."
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
	ms.date="08/04/2015" 
	ms.author="genemi"/>


# Codice di esempio: logica di ripetizione tentativi della libreria Enterprise, in C&#x23; per la connessione al database SQL


In questo argomento viene presentato un esempio di codice completo che illustra la libreria Enterprise (EntLib). EntLib permette al programmatore di inserire con maggiore facilità la logica di ripetizione tentativi nel client che interagisce con il servizio cloud del database SQL di Microsoft Azure.


La libreria Enterprise 6 (EntLib60) è la versione più recente.


Per informazioni dettagliate su EntLib sono consigliati i seguenti collegamenti:


- [Libreria Enterprise 6, aprile 2013](http://msdn.microsoft.com/library/dn169621.aspx)<br/>Fornisce numerosi collegamenti per ottenere ulteriori informazioni.

- Ebook gratuito in formato .PDF di Microsoft:<br/>[Guida alla libreria Microsoft Enterprise, 2° Edition, per sviluppatori ](http://www.microsoft.com/download/details.aspx?id=41145).


## Prerequisiti


EntLib non è inclusa nell’installazione di Visual Studio e Microsoft .NET Framework. È necessario eseguire un'operazione di download separata.


Il sistema **NuGet** tramite Visual Studio semplifica il download. Il download restituisce una directory denominata Packages\\ nella stessa directory in cui è archiviato il file con estensione sln della soluzione Visual Studio.


I file .dll di assembly EntLib vengono installati in una sottodirectory. I due file di assembly sono denominati come segue:


- `Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.dll`
- `Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.Data.dll`


## C&#x23; file di codice illustrati


L'esempio di codice C# è costituito da tre file con estensione cs il cui contenuto viene incollato nelle sezioni che seguono. I nomi dei file sono:


- `Program.cs`
- `Custom_SqlDatabaseTransientErrorDetectionStrategy.cs`
- `ForcePersistent_SqlCommandTransientErrorDetectionStrategy.cs`


### `Program.cs` più breve senza `try/catch`


Un altro file con estensione cs fornito è una versione molto più breve di `Program.cs`. Tutto il codice `try/catch` è stato rimosso. Ciò consente di osservare più facilmente le chiamate di EntLib.



### File con estensione cs di prova


L’esecuzione di test sulla logica di ripetizione tentativi è difficile perché non esiste un modo ovvio per causare un errore temporaneo originale. Una soluzione è utilizzare codice temporaneo per:


1. Provocare un finto errore temporaneo.
2. Correggere la causa dell'errore temporaneo.
3. Riprovare a eseguire la connessione o la query, in attesa di esito positivo.


Per consentire i test, viene fornito un altro file con estensione cs utilizzabile come sostituto temporaneo:


- `Test2_TransientErrorDetectionStrategy`
 - Sostituzione temporanea per `Custom_SqlDatabaseTransientErrorDetectionStrategy.cs`.


I percorsi di codice in cui apportare modifiche per questo test sono contrassegnati con un commento contenente la stringa`TEST.PASSWORD_FIX`.


## Compilare ed eseguire l'esempio di codice


È possibile compilare l'esempio con i passaggi seguenti:


1. In Visual Studio creare un nuovo progetto dal modello di Applicazione console C#.

2. Fare clic con il pulsante destro del mouse sul progetto, quindi aggiungere i file con estensione cs per i quali viene fornito il codice sorgente in questo argomento.

3. In una finestra di comando di `cmd.exe`, eseguire il programma come indicato di seguito. Viene inoltre visualizzato l’output effettivo di un’esecuzione.


		[C:\MyVS\ConsoleApplication1\ConsoleApplication1\bin\Debug]
		>> ConsoleApplication1.exe
		
		database_firewall_rules_table   245575913
		filestream_tombstone_2073058421 2073058421
		filetable_updates_2105058535    2105058535
		
		[C:\MyVS\ConsoleApplication1\ConsoleApplication1\bin\Debug]
		>>


Il codice sorgente C# per i file con estensione cs si trova nelle sezioni seguenti.


## File Program.cs


Il file di codice seguente, Program.cs è lungo perché contiene blocchi `try/catch` che vengono eseguiti solo se si verifica un errore. Verso la fine del presente argomento viene riportata una versione molto più breve di `Program.cs` con tutte le righe `try/catch` eliminate.


Il metodo `Main` si trova in `Program.cs`. Lo stack di chiamate viene eseguito come segue:


1. `Main` chiama `ConnectAndQuery`.

2. `ConnectAndQuery` chiama `InitializeFields`.

3. `ConnectAndQuery` chiama `EstablishConnection`.

4. `EstablishConnection` chiama `IssueQueryCommand`.


& nbsp;


	using     System;  // C#
	using X = System.Text;
	using D = System.Data;
	using C = System.Data.SqlClient;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	namespace ConsoleApplication1 
	{
	    /// <summary>
	    /// Demo sample C# program that uses Microsoft's Enterprise Library 6 to robustly
	    /// connect to and interact with Azure SQL Database.
	    /// .
	    /// The sample chooses a strategy of discarding the Sql Connection whenever
	    /// a Sql Command fails, even for a transient reason. The 'for' loop
	    /// manages this choice. We are neither recommending nor disrecommending
	    /// this particular choice, just demonstrating flexibility.
	    /// .
	    /// For an optional test mechanism manually built into this program, find all:
	    /// 'TEST.PASSWORD_FIX' (related to connection).
	    /// </summary>
	    class Program
	    {
	        // const fields, so that SqlException objects thrown from Sql Commands
	        // are tested for transience by our custom logic.
	        private const string M_progressCreatingConnection = "CreatingConnection";
	        private const string M_progressIssuingCommand     = "IssuingCommand";
	
	        // Fields, shared among methods.
	        private string progressLocation;
	        private E.ReliableSqlConnection rsConnection;
	        private E.RetryPolicy connectionRetryPolicy;
	        private E.RetryPolicy commandRetryPolicy;
	        private C.SqlConnectionStringBuilder scsBuilder;
	        private Action EstablishConnection_action;
	        private Action IssueQueryCommand_action;
	        private string sqlSelectCode;
	        private ForcePersistent_SqlCommandTransientErrorDetectionStrategy
	            forcePersistent_SqlCommandTransientErrorDetectionStrategy;
	
	        // Also consider E.FixedInterval or E.Incremental.
	        private E.ExponentialBackoff exponentialBackoffStrategy;
	
	        // TEST.PASSWORD_FIX.  Replace with Test2_TransientErrorDetectionStrategy.
	        private Custom_SqlDatabaseTransientErrorDetectionStrategy
	            custom_SqlDatabaseTransientErrorDetectionStrategy;
	
	
	        static void Main(string[] args)
	        {
	            new Program().ConnectAndQuery();
	        }
	
	
	        /// <summary>
	        /// Called from Main.
	        /// Calls a method to establish a connection, which calls
	        /// another method to issue a query through the connection. 
	        /// </summary>
	        void ConnectAndQuery()
	        {
	            int maxCountTriesConnectAndQuery = 3;  // Adjustable.
	
	            this.InitializeFields();
	
	            //-------------------------------------------------------
	            // Preparations are complete.
	            // Next is a call .ExecuteAction to invoke the method EstablishConnection.
	            // Method EstablishConnection then calls another .ExecuteAction to
	            //     invoke the method IssueQueryCommand.
	            // Then control is returned to this method.
	
	            for (int cc = 1; cc <= maxCountTriesConnectAndQuery; cc++)
	            {
	                if (cc >= 2) { Console.WriteLine("-- Another iteration of outer loop, cc=={0}. --", cc); }
	
	                try
	                {
	                    // [A.1] Connect, which proceeds to issue a query command.
	                    this.connectionRetryPolicy.ExecuteAction(this.EstablishConnection_action);
	
	                    break; // [A.2] All has gone well, so let the program end.
	                }
	                catch (C.SqlException sqlExc)
	                {
	                    bool isTransientErrorNumber = false;
	
	                    // [A.3] Call our collection of transient error numbers.
	                    isTransientErrorNumber =
	                        Custom_SqlDatabaseTransientErrorDetectionStrategy // TEST.PASSWORD_FIX.
	                        .IsTransientStatic(sqlExc);
	
	                    if (isTransientErrorNumber == false)
	                    {
	                        Console.WriteLine();
	                        Console.WriteLine("Persistent error suffered, SqlException.Number = {0}."
	                            + "  Will terminate.", sqlExc.Number);
	                        Console.WriteLine(sqlExc.ToString());
	
	                        // [A.4] Either the connection attempt or the query command attempt
	                        //     suffered a persistent SqlException.
	                        // Break the query command loop, let the hopeless program end.
	                        break;
	                    }
	
	                    if (this.progressLocation == M_progressCreatingConnection)
	                    {
	                        // [A.5] The EntLib retry mechanisms already gave the Sql Connection
	                        //     enough retries, so this block will not give connection any more tries.
	                        // This block gives retries to certain Sql Command only.
	                        break;
	                    }
	
	                    // [A.6] The SqlException identified a transient error from an attempt
	                    //     to issue a query command.
	                    // So let this method reloop and try again. However, we recommend that the new
	                    // query attempt should start at the beginning and establish a new connection.
	                    Console.WriteLine();
	                    Console.WriteLine("Transient error encountered.  SqlException.Number = {0}."
	                        + "  Program might retry by itself.", sqlExc.Number);
	                    Console.WriteLine("{0} = Attempts so far. Might retry.", cc);
	                    Console.WriteLine(sqlExc.Message);
	                }
	                catch (Exception exc)
	                {
	                    Console.WriteLine();
	                    Console.WriteLine("Unexpected exception type caught.  Will terminate.");
	                    throw exc; // [A.7] The program must end, so re-throw the unrecognized exception.
	                }
	
	
	                // [A.8] Throw an exception if transient SqlExceptions caused us
	                // to exceed our self-imposed maximum of retries.
	                if (cc > maxCountTriesConnectAndQuery)
	                {
	                    Console.WriteLine();
	                    string mesg = String.Format(
	                        "Transient errors suffered in too many retries ({0}). Will terminate.",
	                        cc - 1);
	                    Console.WriteLine(mesg);
	
	                    // [A.9] To end the program, throw a new exception of a different type.
	                    ApplicationException appExc = new ApplicationException(mesg);
	                    throw appExc;
	                }
	            } // for cc
	            return;
	        } // method ConnectAndQuery
	
	
	        void EstablishConnection()
	        {
	            try
	            {
	                // [C.1]
	                using ( this.rsConnection = new E.ReliableSqlConnection(
	                    this.scsBuilder.ToString(),
	                    this.connectionRetryPolicy,
	                    this.commandRetryPolicy )
	                    )
	                {
	                    this.progressLocation = M_progressCreatingConnection;
	                    this.rsConnection.Open(); // [C.2] Open the newly constructed reliable connection.
	
	                    // [C.3] Prepare delegate, then issue a query command.
	                    this.commandRetryPolicy.ExecuteAction(this.IssueQueryCommand_action);
	                }
	            }
	            // TEST.PASSWORD_FIX.
	                // Step_1: In InitializeFields(), set password to a wrong value.
	                // Step_2: Uncomment the following 'catch' of sqlExc.
	            //catch (C.SqlException sqlExc)  // For TESTING only.  Usually comment out this one CATCH.
	            //{
	            //    if (sqlExc.Number == 18456)  // Could mean wrong password, could not connect.
	            //    {
	            //        this.scsBuilder["Password"] = "MyCorrectPassword";
	            //        Console.WriteLine("Testing.  Password now fixed in catch.");
	            //    }
	            //    throw sqlExc;
	            //}
	            catch (Exception exc)
	            {
	                throw exc;  // [C.4] Let caller assess any exception, SqlException or any kind.
	            }
	            return;
	        } // method EstablishConnection
	
	
	        void IssueQueryCommand()
	        {
	            D.IDataReader dReader = null;
	            D.IDbCommand dbCommand = null;
	
	            X.StringBuilder sBuilder = new X.StringBuilder(256);
	
	            try
	            {
	                // [D.1] Use the connection to create a query command.
	                using (dbCommand = this.rsConnection.CreateCommand())
	                {
	                    dbCommand.CommandText = this.sqlSelectCode;
	
	                    // [D.2] Issue the query command through the connection.
	                    this.progressLocation = M_progressIssuingCommand;
	                    using (dReader = this.rsConnection.ExecuteCommand<D.IDataReader>(dbCommand))
	                    {
	                        Console.WriteLine();
	                        // [D.3] Loop through all returned rows, writing the data to the console.
	                        while (dReader.Read())
	                        {
	                            sBuilder.Length = 0;
	                            sBuilder.Append(dReader.GetString(0));
	                            sBuilder.Append("\t");
	                            sBuilder.Append(dReader.GetString(1));
	
	                            Console.WriteLine(sBuilder.ToString());
	                        }
	                    }
	                }
	            }
	            catch (Exception exc)
	            {
	                throw exc; // [D.4] Let caller assess any exception.
	            }
	            return;
	        } // method IssueQueryCommand
	
	
	        void InitializeFields()
	        {
	            // [B.1] Prepare the connection string to Azure SQL Database.
	            this.scsBuilder = new C.SqlConnectionStringBuilder();
	            // Change these values to your values.
	            this.scsBuilder["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
	            this.scsBuilder["User ID"] = "MyLogin";  // @yourservername suffix sometimes.
	            this.scsBuilder["Password"] = "MyCorrectPassword";  // TEST.PASSWORD_FIX.
	            this.scsBuilder["Database"] = "MyDatabase";
	            // Leave these values as they are.
	            this.scsBuilder["Trusted_Connection"] = false;
	            this.scsBuilder["Integrated Security"] = false;
	            this.scsBuilder["Encrypt"] = true;
	            this.scsBuilder["Connection Timeout"] = 30; // Default is 15 seconds, too brief over Internet!
	
	            // [B.2] Prepare the Transact-SQL select statement.
	            this.sqlSelectCode =
	                @"SELECT TOP 3 name, CAST(object_id as nvarchar(32)) as [object_id]
	                  FROM sys.objects
	                  WHERE type='IT'
	                  ORDER BY name;";
	
	            // [B.3] Begin steps to create a retry policy for connecting.
	            this.exponentialBackoffStrategy = new E.ExponentialBackoff(
	                "exponentialBackoffStrategy",
	                2,                               // Maximum number of times to retry.
	                TimeSpan.FromMilliseconds(2000), // Minimum interval between retries.
	                TimeSpan.FromMilliseconds(8000), // Maximum interval between retries.
	                TimeSpan.FromMilliseconds(2000)  // Factor for random differences in interval between retries.
	                );
	
	            this.forcePersistent_SqlCommandTransientErrorDetectionStrategy =
	                new ForcePersistent_SqlCommandTransientErrorDetectionStrategy();
	
	            this.custom_SqlDatabaseTransientErrorDetectionStrategy =
	                new Custom_SqlDatabaseTransientErrorDetectionStrategy();// TEST.PASSWORD_FIX.
	
	            this.connectionRetryPolicy = new E.RetryPolicy(
	                this.custom_SqlDatabaseTransientErrorDetectionStrategy,
	                this.exponentialBackoffStrategy);
	            this.commandRetryPolicy = new E.RetryPolicy(
	                this.forcePersistent_SqlCommandTransientErrorDetectionStrategy,
	                this.exponentialBackoffStrategy);
	
	            this.EstablishConnection_action = delegate() { this.EstablishConnection(); };
	            this.IssueQueryCommand_action   = delegate() { this.IssueQueryCommand(); };
	
	            return;
	        } // method InitializeFields
	    } // class Program
	}


& nbsp;


## File `Custom_SqlDatabaseTransientErrorDetectionStrategy.cs`


EntLib60 dispone di una classe denominata `ReliableSqlConnection`. È possibile controllare come l'istanza di connessione decide se un'eccezione è un errore temporaneo assegnandogli una classe che implementa `ITransientErrorDetectionStrategy`.

EntLib60 offre la classe `SqlDatabaseTransientErrorDetectionStrategy`. Ma in questo argomento è stato scelto di implementare e utilizzare una classe personalizzata `Custom_SqlDatabaseTransientErrorDetectionStrategy`. La classe personalizzata dispone di un elenco di valori consentiti da confrontare con `SqlException.Number`.


	using     System;
	using G = System.Collections.Generic;
	using C = System.Data.SqlClient;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	namespace ConsoleApplication1
	{
	    /// <summary>
	    /// A custom alternative to class SqlDatabaeTransientErrorDetectionStrategy.
	    /// </summary>
	    public class Custom_SqlDatabaseTransientErrorDetectionStrategy
	        : E.ITransientErrorDetectionStrategy
	    {
	        static private G.List<int> M_listTransientErrorNumbers;
	
	
	        /// <summary>
	        /// This method is required by ITransientErrorDetectionStrategy.
	        /// </summary>
	        public bool IsTransient(Exception exc)
	        {
	            return IsTransientStatic(exc);
	        }
	
	
	        /// <summary>
	        /// For general use beyond formal Enterprise Library classes.
	        /// </summary>
	        static public bool IsTransientStatic(Exception exc)
	        {
	            bool returnBool = false;  // Assume is a persistent error.
	            C.SqlException sqlExc;
	
	            if (exc is C.SqlException)
	            {
	                sqlExc = exc as C.SqlException;
	                if (M_listTransientErrorNumbers.Contains(sqlExc.Number) == true)
	                {
	                    returnBool = true;  // Error is transient, not persistent.
	                }
	            }
	            return returnBool;
	        }
	
	
	        /// <summary>
	        /// Lists the SqlException.Number values that are considered
	        /// to indicate transient errors.
	        /// </summary>
	        static Custom_SqlDatabaseTransientErrorDetectionStrategy()
	        {
	            int[] arrayOfTransientErrorNumbers =
	                {4060, 10928, 10929, 40197, 40501, 40613};
	
	            M_listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
	        }
	    } // class Custom_SqlDatabaseTransientErrorDetectionStrategy
	}


& nbsp;


## File `ForcePersistent_SqlCommandTransientErrorDetectionStrategy.cs`


Questa classe personalizzata che implementa `ITransientErrorDetectionStrategy` fa osservare la scelta di non consentire mai a EntLib60 di considerare un qualsiasi errore di comando query semplicemente temporaneo. Al contrario, il programma generale è progettato per valutare `SqlException.Number` nella sua logica personalizzata.


Con la classe riportata in questa sezione, è stata fatta la scelta di progettazione di fare in modo che il programma elimini e ricrei una nuova connessione prima di ritentare qualsiasi comando di query non riuscito, in modo temporaneo o meno.


	using     System;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	
	namespace ConsoleApplication1
	{
	    /// <summary>
	    /// Is a substitute for class E.SqlCommandTransientErrorDetectionStrategy.
	    /// Choosing to always discard the Sql Connection whenever the command fails,
	    /// regardless of whether command failure was transient.
	    /// Program try/catch and for-loop logic prefers to discard then re-construct
	    /// a new Sql Connection.
	    /// </summary>
	    public class ForcePersistent_SqlCommandTransientErrorDetectionStrategy
	        : E.ITransientErrorDetectionStrategy
	    {
	        public bool IsTransient(Exception exc)
	        {
	            return false;  // Force a failure, even if is transient.  No retry of sql command.
	        }
	    }
	}


& nbsp;


## Eseguire test sulla logica di ripetizione tentativi


È complesso eseguire test sul programma EntLib60. È difficile provocare un errore temporaneo in modo reale e controllarne gli intervalli. Questo codice di esempio include una tecnica semplice utilizzabile per simulare un errore temporaneo.


La tecnica consiste nell'utilizzare la classe `Test2_TransientErrorDetectionStrategy` di rilevamento personalizzato al posto della classe `Custom_SqlTransientErrorDetectionStrategy`. In questo modo è possibile forzare `ReliableSqlConnection` di EntLib60 a valutare *tutte* le occorrenze di `SqlException` come temporanee. Il trucco è fare in modo che il programma risolva automaticamente il problema prima della prima ripetizione del tentativo.


### Il trucco delle password errate


Un approccio semplice consiste nell'iniziare con un valore errato per la password di connessione. Quando ciò provoca un `SqlException` con `sqlExc.Number==18456`, il programma deve correggere la password. Alla ripetizione del tentativo, il programma completa correttamente l’operazione.


Per implementare questo test nel programma demo, cercare il codice sorgente per tutte le occorrenze di `TEST.PASSWORD_FIX`. Dopo avere trovato tutte le occorrenze di `TEST.PASSWORD_FIX` e apportato le modifiche illustrate nei commenti in ogni posizione, saranno state apportate le modifiche seguenti:


- Nel codice in tempo reale del metodo `InitializeFields`, modificare il valore della password in un valore non corretto.

- Nel codice commentato `//`, nel metodo `EstablishConnection`, esiste un intero blocco `catch` ...<br/>`//catch (SDSqlC.SqlException sqlExc)`<br/>commentato. Rimuovere il commento dall'intero blocco.

- Nel blocco `catch` da cui è appena stato rimosso il commento, assegnare il valore password corretto.

- Nel punto del codice in cui viene fatto riferimento alla classe `Custom_TransientErrorDetectionStrategy`, sostituire il riferimento con `Test2_TransientErrorDetectionStrategy`. È possibile mantenere lo stesso nome per la variabile.


### File `Test2_TransientErrorDetectionStrategy.cs`


	using     System;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	namespace ConsoleApplication1
	{
	    /// <summary>
	    /// For testing, is a substitute for class E.SqlDatabaseTransientErrorDetectionStrategy.
	    /// Change the true/false assignment to returnValue for different tests.
	    /// </summary>
	    public class Test2_TransientErrorDetectionStrategy
	        : E.ITransientErrorDetectionStrategy
	    {
	        public bool IsTransient(Exception exc)
	        {
	            bool returnValue = Test2_TransientErrorDetectionStrategy.IsTransientStatic(exc);
	            Console.WriteLine("Inside Test2_TransientErrorDetectionStrategy .IsTransientStatic,"
	                + " returning {0}.", returnValue);
	            return returnValue;
	        }
	        static public bool IsTransientStatic(Exception exc)
	        {
	            return true;  //false;
	        }
	    }
	}


& nbsp;


## `Program.cs` più breve, senza `try/catch`


In questa sezione viene compilato il codice più breve che se eseguito funziona in modo normale. Ma il codice più breve non deve essere eseguito nell'ambiente di produzione.


L'unico scopo di questo esempio di codice `Program.cs` più breve, è facilitare l’individuazione delle stesse chiamate di EntLib presenti nel `Program.cs` reale. La rimozione di tutti i blocchi di `try/catch` facilita l’individuazione delle chiamate di EntLib.


	using     System;  // C#
	using X = System.Text;
	using D = System.Data;
	using C = System.Data.SqlClient;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	namespace ConsoleApplication1_Shorter_No_TryCatch
	{
	    class Program
	    {
	        private E.ReliableSqlConnection rsConnection;
	        private E.RetryPolicy connectionRetryPolicy;
	        private E.RetryPolicy commandRetryPolicy;
	        private E.ExponentialBackoff exponentialBackoffStrategy;
	        private C.SqlConnectionStringBuilder scsBuilder;
	        private Action EstablishConnection_action;
	        private Action IssueQueryCommand_action;
	        private string sqlSelectCode;
	        private Custom_SqlDatabaseTransientErrorDetectionStrategy
	            custom_SqlDatabaseTransientErrorDetectionStrategy;
	        private ForcePersistent_SqlCommandTransientErrorDetectionStrategy
	            forcePersistent_SqlCommandTransientErrorDetectionStrategy;
	
	        static void Main(string[] args)
	        {
	            new Program().ConnectAndQuery();
	        }
	
	        void ConnectAndQuery()
	        {
	            this.InitializeFields();
	            this.connectionRetryPolicy.ExecuteAction(this.EstablishConnection_action);
	        } // method ConnectAndQuery
	
	        void EstablishConnection()
	        {
	            using ( this.rsConnection = new E.ReliableSqlConnection(
	                this.scsBuilder.ToString(),
	                this.connectionRetryPolicy,
	                this.commandRetryPolicy )
	                    )
	            {
	                this.rsConnection.Open();
	                this.commandRetryPolicy.ExecuteAction(this.IssueQueryCommand_action);
	            }
	        } // method EstablishConnection
	
	
	        void IssueQueryCommand()
	        {
	            D.IDataReader dReader = null;
	            D.IDbCommand dbCommand = null;
	            X.StringBuilder sBuilder = new X.StringBuilder(256);
	
	            using (dbCommand = this.rsConnection.CreateCommand())
	            {
	                dbCommand.CommandText = this.sqlSelectCode;
	                using (dReader = this.rsConnection.ExecuteCommand<D.IDataReader>(dbCommand))
	                {
	                    while (dReader.Read())
	                    {
	                        sBuilder.Length = 0;
	                        sBuilder.Append(dReader.GetString(0));
	                        sBuilder.Append("\t");
	                        sBuilder.Append(dReader.GetString(1));
	                        Console.WriteLine(sBuilder.ToString());
	                    }
	                }
	            }
	        } // method IssueQueryCommand
	
	        void InitializeFields()
	        {
	            this.scsBuilder = new C.SqlConnectionStringBuilder();
	            // Change these values to your values.
	            this.scsBuilder["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
	            this.scsBuilder["User ID"] = "MyLogin";
	            this.scsBuilder["Password"] = "MyPassword";  // TEST.PASSWORD_FIX.
	            this.scsBuilder["Database"] = "MyDatabase";
	            // Leave these values as they are.
	            this.scsBuilder["Trusted_Connection"] = false;
	            this.scsBuilder["Integrated Security"] = false;
	            this.scsBuilder["Encrypt"] = true;
	            this.scsBuilder["Connection Timeout"] = 30;
	
	            this.sqlSelectCode =
	                @"SELECT TOP 3 name, CAST(object_id as nvarchar(32)) as [object_id]
	                  FROM sys.objects
	                  WHERE type='IT'
	                  ORDER BY name;";
	
	            this.exponentialBackoffStrategy = new E.ExponentialBackoff(
	                "exponentialBackoffStrategy",
	                2,
	                TimeSpan.FromMilliseconds(2000),
	                TimeSpan.FromMilliseconds(8000),
	                TimeSpan.FromMilliseconds(2000)
	                );
	
	            this.forcePersistent_SqlCommandTransientErrorDetectionStrategy =
	                new ForcePersistent_SqlCommandTransientErrorDetectionStrategy();
	            this.custom_SqlDatabaseTransientErrorDetectionStrategy =
	                new Custom_SqlDatabaseTransientErrorDetectionStrategy();
	
	            this.connectionRetryPolicy = new E.RetryPolicy(
	                this.custom_SqlDatabaseTransientErrorDetectionStrategy,
	                this.exponentialBackoffStrategy);
	            this.commandRetryPolicy = new E.RetryPolicy(
	                this.forcePersistent_SqlCommandTransientErrorDetectionStrategy,
	                this.exponentialBackoffStrategy);
	
	            this.EstablishConnection_action = delegate() { this.EstablishConnection(); };
	            this.IssueQueryCommand_action   = delegate() { this.IssueQueryCommand(); };
	        } // method InitializeFields
	    } // class Program
	}


## Collegamenti correlati


- [Spazio dei nomi Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling](http://msdn.microsoft.com/library/microsoft.practices.enterpriselibrary.transientfaulthandling.aspx)

- [Libreria di classi Enterprise 6](http://msdn.microsoft.com/library/dn170426.aspx)

- [Codice di esempio: logica di ripetizione tentativi in C# per la connessione al database SQL con ADO.NET](sql-database-develop-csharp-retry-windows.md)

- [Esempi di codice di avvio rapido del client per il database SQL](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=August15_HO6-->