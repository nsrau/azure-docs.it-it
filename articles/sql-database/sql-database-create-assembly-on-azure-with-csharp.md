<properties 
	pageTitle="CREAZIONE ASSEMBLY nel database SQL di Azure con CSharp"
	description="Viene fornito un codice sorgente C# per rilasciare CREAZIONE ASSEMBLY nel database SQL di Azure dopo la prima codifica di un file DLL in una stringa contenente un numero esadecimale lungo." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/25/2015" 
	ms.author="genemi"/>


# CREAZIONE ASSEMBLY nel database SQL di Azure con CSharp


<!--
GeneMi , Latest edit = 2015-March-25  Wednesday  10:22am
Converting plain text "CREATE ASSEMBLY" into a link to the MSDN topic, ms189524.aspx. And ms186755.aspx for "CREATE FUNCTION".
-->


In questo argomento viene fornito un codice C# di esempio che è possibile utilizzare per rilasciare un'istruzione [CREAZIONE ASSEMBLY](http://msdn.microsoft.com/library/ms189524.aspx) nel database SQL di Azure. Per il database SQL la clausola FROM non è in grado di accettare il formato semplice di un percorso nel computer locale che ospita il database. Un'alternativa possibile consiste nel codificare i bit binari del DLL dell'assembly in una stringa lunga contenente un numero esadecimale. Assegnare quindi la stringa come valore nella clausola FROM.


### Prerequisiti


Per comprendere questo argomento, è necessario conoscere già parzialmente quanto segue:


- [Funzioni con valori di tabella CLR](http://msdn.microsoft.com/library/ms131103.aspx)<br/>Viene illustrato il funzionamento dell'istruzione Transact-SQL CREAZIONE ASSEMBLY con altre istruzioni per Microsoft SQL Server locale.


## A. Tecnica generale


1. ELIMINA FUNZIONE ed ELIMINA ASSEMBLY, se necessario, per eseguire il clean up di un'operazione precedente.
2. Tenere presente il percorso del file DLL dell'assembly di Microsoft .NET Framework compilato dal proprio codice. Fornire tale percorso nel passaggio successivo. 
3. Eseguire il file EXE per il quale è dato il codice sorgente C# in questo argomento. Indicare al file EXE il percorso del file DLL.
 - Codificare il DLL binario in una stringa lunga contenente un numero esadecimale.
 - Rilasciare un'istruzione CREAZIONE ASSEMBLY con la stringa esadecimale data nella clausola FROM.
4. [CREA FUNZIONE](http://msdn.microsoft.com/library/ms186755.aspx) per fare riferimento a un metodo nell'assembly.
5. Istruzione T-SQL SELECT per chiamare e testare la funzione.


L'elenco precedente non fornisce informazioni relative a...<br/>
**execute sp_configure 'clr enabled', 1;**<br/>
...perché non è necessario per il database SQL di Azure, pur essendo necessario per Microsoft SQL Server.


Se è necessario rieseguirlo, il codice T-SQL per eliminare la funzione e l'assembly è il seguente:


    DROP FUNCTION fnCompareStringsCaseSensitive;
    DROP ASSEMBLY CreateAssemblyFunctions3;


## B. DLL dell'assembly semplice per la funzione T-SQL cui fare riferimento


L'esempio di codice C# semplice in questa sezione può essere compilato in un file DLL dell'assembly.


Questo esempio di codice contiene il metodo **CompareCaseSensitiveNet**, cui si farà riferimento più avanti nell'istruzione T-SQL CREAZIONE FUNZIONE. Si noti che il metodo è dotato di un attributo .NET denominato **SqlFunction**. Un metodo dotato di questo attributo può essere chiamato da T-SQL come funzione.


	using           System;   // C#
	using SDSqTyp = System.Data.SqlTypes;
	using MSqServ = Microsoft.SqlServer.Server;
	
	namespace CreateAssemblyFunctions3
	{
	    public class SqlFunctionMethodsForSprocs
	    {
	        /// <summary> This method is referenced
	        /// by a T-SQL CREATE FUNCTION statement. </summary>
	        [MSqServ.SqlFunction(IsDeterministic = true, IsPrecise = true)]
	        static public SDSqTyp.SqlInt32 CompareCaseSensitiveNet(string strA, string strB)
	        {
	            return String.Compare(strA, strB, false);
	        }
	    }
	}


## C. C&#x23; Esempio di codice per EXE che rilascia CREAZIONE ASSEMBLY


La seguente sequenza si verifica quando si esegue il file EXE creato da questo esempio C#:


1. L'operazione della riga di comando del file EXE chiama il metodo **Main**.
2. Il metodo Main chiama il metodo **ObtainHexStringOfAssembly**.
 - Il metodo restituisce un SqlString che archivia l'assembly come numero esadecimale.
3. Il metodo Main chiama il metodo **SubmitCreateAssemblyToAzureSqlDb**.
 - L'input principale è SqlString.
 - L'output è una chiamata CREAZIONE ASSEMBLY inviata al database SQL di Azure.


			using             System;   // C#
			using SDat      = System.Data;
			using SDSClient = System.Data.SqlClient;
			using SGlo      = System.Globalization;
			using SIo       = System.IO;
			using STex      = System.Text;
			
			namespace CreateAssemblyFromHexString6
			{
			    /// <summary>
			    /// Run the Main method on your local computer console, so it can issue a
			    /// a CREATE ASSEMBLY statement to your Azure SQL Database server:
			    /// </summary>
			    class Program
			    {
			        /// <summary>
			        /// Calls the methods in the proper sequence.
			        /// </summary>
			        /// <param name="args">
			        /// Parameters for the cmd.exe command line
			        ///    run of CreateAssemblyFromHexString6.exe:
			        /// args[0] = FullDirPathFileNameOfAssembly.
			        /// args[1] = AssemblyName.
			        ///    For the CREATE ASSEMBLY assemblyName statement.
			        /// args[2] = Azure SQL Database - ServerName, including a suffix like .database.windows.net .
			        /// args[3] = Azure SQL Database - DatabaseName.
			        /// args[4] = Azure SQL Database - LoginName.
			        /// args[5] = Azure SQL Database - Password for login.
			        ///    (Better if from .config file.)
			        /// </param>
			        static int Main(string[] args)
			        {
			            int returnCode = 1; // Only 0 (zero) means Good Success.
			            string
			                fullPathNameAssemblyFile,
			                assemblyName,
			                AzureSqlDbServerName,
			                AzureSqlDbDatabaseName,
			                AzureSqlDbLoginName,
			                AzureSqlDbPassword;
			
			            try
			            {
			                fullPathNameAssemblyFile = args[0];
			                assemblyName             = args[1];
			                AzureSqlDbServerName     = args[2];
			                AzureSqlDbDatabaseName   = args[3];
			                AzureSqlDbLoginName      = args[4];
			                AzureSqlDbPassword       = args[5];
			
			                string hexStringOfAssembly = Program
			                    .ObtainHexStringOfAssembly(fullPathNameAssemblyFile);
			
			                Program.SubmitCreateAssemblyToAzureSqlDb(
			                    hexStringOfAssembly,
			                    assemblyName,
			                    AzureSqlDbServerName,
			                    AzureSqlDbDatabaseName,
			                    AzureSqlDbLoginName,
			                    AzureSqlDbPassword);
			
			                returnCode = 0;
			            }
			            catch (Exception ex)
			            {
			                Console.WriteLine("Bad, Failure.");
			                throw ex;
			            }
			            Console.WriteLine("Good, Success.");
			            return returnCode;
			        }
			
			        /// <summary> Encodes the binary bits of the assembly DLL into a 
			        /// string containing a hexadecimal number. </summary>
			        /// <param name="fullPathToAssembly"
			        /// >Full directory path plus the file name, to the .DLL file.</param>
			        /// <returns>A string containing a hexadecimal number that encodes
			        /// the binary bits of the .DLL file.</returns>
			        static private string ObtainHexStringOfAssembly
			            (string fullPathToAssembly)
			        {
			            STex.StringBuilder sbuilder = new STex.StringBuilder("0x");
			            using (SIo.FileStream fileStream = new SIo.FileStream(
			                fullPathToAssembly,
			                SIo.FileMode.Open, SIo.FileAccess.Read, SIo.FileShare.Read)
			                )
			            {
			                int byteAsInt;
			                while (true)
			                {
			                    byteAsInt = fileStream.ReadByte();
			                    if (-1 >= byteAsInt) { break; }
			                    sbuilder.Append(byteAsInt.ToString
			                        ("X2", SGlo.CultureInfo.InvariantCulture));
			                }
			            }
			            return sbuilder.ToString();
			        }
			
			        /// <summary>
			        /// Sends a Transact-SQL CREATE ASSEMBLY FROM hexString.
			        /// </summary>
			        static private void SubmitCreateAssemblyToAzureSqlDb(
			            string hexStringOfAssembly,
			            string assemblyName,
			            string AzureSqlDbServerName,
			            string AzureSqlDbDatabaseName,
			            string AzureSqlDbLoginName,
			            string AzureSqlDbPassword)
			        {
			            string sqlCreateAssembly = "CREATE ASSEMBLY [" + assemblyName
			                + "] FROM " + hexStringOfAssembly + ";";
			            STex.StringBuilder sbuilderConnection = new STex.StringBuilder();
			
			            sbuilderConnection.Append("Server=tcp:");
			            sbuilderConnection.Append(AzureSqlDbServerName);
			            sbuilderConnection.Append(",1433;");
			
			            sbuilderConnection.Append("Database=");
			            sbuilderConnection.Append(AzureSqlDbDatabaseName);
			            sbuilderConnection.Append(";");
			
			            sbuilderConnection.Append("Trusted_Connection=False;");
			            sbuilderConnection.Append("Connection Timeout=30;");
			
			            sbuilderConnection.Append("User ID=");
			            sbuilderConnection.Append(AzureSqlDbLoginName);
			            sbuilderConnection.Append(";");
			
			            sbuilderConnection.Append("Password=");
			            sbuilderConnection.Append(AzureSqlDbPassword);
			            sbuilderConnection.Append(";");
			
			            using (SDSClient.SqlConnection sqlConnection =
			                new SDSClient.SqlConnection())
			            {
			                SDSClient.SqlCommand sqlCommand;
			                sqlConnection.ConnectionString = sbuilderConnection.ToString();
			                sqlCommand = sqlConnection.CreateCommand();
			                sqlCommand.CommandType = SDat.CommandType.Text;
			                sqlCommand.CommandText = sqlCreateAssembly;
			                sqlConnection.Open();
			                sqlCommand.ExecuteNonQuery();
			            }
			            return;
			        }
			    }
			}


### C.1 Compilare riferimenti e versioni


Quando è stato compilato e testato il codice di esempio per lo strumento EXE, è stato utilizzato:


- Visual Studio 2013 Update 4
 - Per quanto riguarda il tipo di modello del progetto si è trattato di un'applicazione console semplice.
- .NET Framework 4.5


Il progetto di Visual Studio ha fatto riferimento alle seguenti assembly per compilare:


- Microsoft.CSharp
- System
- System.Core
- System.Data
- System.Data.DataSetExtensions
- System.Xml
- System.Xml.Linq


### C.2 Riga di comando per eseguire l'EXE


Nel seguente blocco di codice viene visualizzato un esempio della riga di comando da immettere per eseguire l'EXE dalla console. I parametri della riga di comando sono stati riepilogati artificialmente per una visualizzazione migliore.


	CreateAssemblyFromHexString6.exe
		C:\my\bin\debug\CreateAssemblyFunctions3.dll
		CreateAssemblyFunctions3
		myazuresqldbsvr2.database.windows.net
		myazuresqldbdab4
		myazurelogin
		Mypassword123


Per semplificare la spiegazione, in questo esempio la password viene fornita come parametro della riga di comando. L'ideale sarebbe far ottenere la password al codice C# da un file CONFIG.


## D. Eseguire un'istruzione CREAZIONE FUNZIONE


In seguito all'archiviazione dell'assembly nel server del database SQL di Azure, è necessario eseguire un'istruzione Transact-SQL CREAZIONE FUNZIONE che faccia riferimento al metodo nell'assembly.


Nel seguente blocco di codice Transact-SQL è riportata una coppia di istruzioni SELECT non essenziale per dimostrare che il sistema del database dispone di record per l'assembly e la funzione. Infine, tramite SELECT viene chiamata la funzione.


	SELECT a11.*, am2.*
		FROM           sys.assemblies       AS a11
		    INNER JOIN sys.assembly_modules AS am2 ON am2.assembly_id = a11.assembly_id
		WHERE a11.name = 'CreateAssemblyFunctions3';
	GO
	
	CREATE FUNCTION fnCompareStringsCaseSensitive
	    (@strA nvarchar(128), @strB nvarchar(128))
	    returns int
	    AS EXTERNAL NAME
	        CreateAssemblyFunctions3
	            .[CreateAssemblyFunctions3.SqlFunctionMethodsForSprocs]
	                .CompareCaseSensitiveNet;
	GO
	SELECT * FROM sys.objects WHERE name = 'fnCompareStringsCaseSensitive';
	
	 -- Use the new function.
	SELECT dbo.fnCompareStringsCaseSensitive('BLUE', 'blue') as returnedValue;
	GO


Il blocco di codice Transact-SQL precedente termina con un'istruzione SELECT che chiama la nuova funzione. È possibile inserire l'istruzione SELECT in una procedura archiviata.


<!-- EndOfFile -->


<!--HONumber=49--> 