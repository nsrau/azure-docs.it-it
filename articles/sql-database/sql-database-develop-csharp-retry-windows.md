<properties 
	pageTitle="Logica di ripetizione dei tentativi C# per connettersi al database SQL | Microsoft Azure" 
	description="L’esempio C# include una logica di ripetizione dei tentativi per un'interazione affidabile con database SQL di Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/30/2015" 
	ms.author="genemi"/>


# Codice di esempio: logica di ripetizione tentativi in C# per la connessione a Database SQL


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


In questo argomento viene fornito un esempio di codice C# che illustra la logica di ripetizione tentativi personalizzata. La logica di ripetizione tentativi è progettata per elaborare correttamente gli errori temporanei o *guasti temporanei* che tendono a risolversi se il programma attende qualche secondo ed effettua un nuovo tentativo.


Le classi ADO.NET utilizzate per connettersi a Microsoft SQL Server locale possono anche connettersi a Database SQL di Azure. Tuttavia, le classi ADO.NET non forniscono autonomamente tutta la solidità e l’affidabilità necessarie per l’uso in un ambiente di produzione. Il programma client può rilevare guasti temporanei da cui dovrebbe eseguire un ripristino automatico e corretto autonomamente.


Alcuni esempi di guasti temporanei sono:


- Una connessione su Internet è soggetta a brevi interruzioni di rete dopo le quali è possibile ricreare la connessione.

- Il cloud computing implica il bilanciamento del carico che può bloccare brevemente i tentativi di connessione o di query.


## Identificare gli errori temporanei


Il programma deve distinguere gli errori temporanei dagli errori persistenti. Se il programma presenta un errore di ortografia del nome del database di destinazione, l'errore "Database non trovato" viene mantenuto e si presenta ogni volta che si esegue nuovamente il programma.


L'elenco dei numeri di errore classificati come guasti temporanei è disponibile all'indirizzo:


- [Messaggi di errore per programmi client di Database SQL](sql-database-develop-error-messages.md#bkmk_connection_errors)
 - Vedere la sezione superiore su *Errori temporanei, errori di perdita della connessione*.


## Esempio di codice C#


L'esempio di codice C# nel presente argomento contiene la logica di ripetizione tentativi e il rilevamento personalizzato per gestire gli errori temporanei. Nell'esempio si presuppone che sia installato .NET Framework 4.5.1 o versione successiva.


L'esempio di codice segue alcune linee guida di base o indicazioni che si applicano indipendentemente dalla tecnologia utilizzata per interagire con Database SQL di Azure. È possibile visualizzare le indicazioni generali in:


- [Connessione al database SQL: collegamenti, procedure consigliate e linee guida per la progettazione](sql-database-connect-central-recommendations.md)


L'esempio di codice C# è costituito da un file denominato Program.cs. Il codice viene incollato nella sezione successiva.


### Acquisire e compilare il codice di esempio


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


Incollare il codice sorgente nel file **Program.cs**.


È necessario modificare le stringhe per nome del server, password e così via. È possibile trovare queste stringhe nel metodo denominato **GetSqlConnectionStringBuilder**.


```
using System;   // C#
using G = System.Collections.Generic;
using D = System.Data;
using C = System.Data.SqlClient;
using X = System.Text;
using H = System.Threading;

namespace RetryAdo2
{
	class Program
	{
		static void Main(string[] args)
		{
			Program program = new Program();
			bool returnBool;

			returnBool = program.Run(args);
			if (returnBool == false)
			{
				Console.WriteLine("Something failed.  :-( ");
			}
			return;
		}

		bool Run(string[] _args)
		{
			C.SqlConnectionStringBuilder sqlConnectionSB;
			C.SqlConnection sqlConnection;
			D.IDbCommand dbCommand;
			D.IDataReader dataReader;
			X.StringBuilder sBuilder = new X.StringBuilder(256);
			int retryIntervalSeconds = 10;
			bool returnBool = false;

			for (int tries = 1; tries <= 5; tries++)
			{
				try
				{
					if (tries > 1)
					{
						H.Thread.Sleep(1000 * retryIntervalSeconds);
						retryIntervalSeconds = Convert.ToInt32(retryIntervalSeconds * 1.5);
					}
					this.GetSqlConnectionStringBuilder(out sqlConnectionSB);

					sqlConnection = new C.SqlConnection(sqlConnectionSB.ToString());

					dbCommand = sqlConnection.CreateCommand();
					dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";

					sqlConnection.Open();
					dataReader = dbCommand.ExecuteReader();

					while (dataReader.Read())
					{
						sBuilder.Length = 0;
						sBuilder.Append(dataReader.GetString(0));
						sBuilder.Append("\t");
						sBuilder.Append(dataReader.GetString(1));

						Console.WriteLine(sBuilder.ToString());
					}
					returnBool = true;
					break;
				}

				catch (C.SqlException sqlExc)
				{
					if (this.m_listTransientErrorNumbers.Contains(sqlExc.Number) == true)
					{ continue; }
					else
					{ throw sqlExc; }
				}
			}
			return returnBool;
		}

		void GetSqlConnectionStringBuilder(out C.SqlConnectionStringBuilder _sqlConnectionSB)
		{
			// Prepare the connection string to Azure SQL Database.
			_sqlConnectionSB = new C.SqlConnectionStringBuilder();

			// Change these values to your values.
			_sqlConnectionSB["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
			_sqlConnectionSB["User ID"] = "MyLogin";  // "@yourservername"  as suffix sometimes.
			_sqlConnectionSB["Password"] = "MyPassword";
			_sqlConnectionSB["Database"] = "MyDatabase";

			// Adjust these values if you like. (.NET 4.5.1 or later.)
			_sqlConnectionSB["ConnectRetryCount"] = 3;
			_sqlConnectionSB["ConnectRetryInterval"] = 10;  // Seconds.

			// Leave these values as they are.
			_sqlConnectionSB["Trusted_Connection"] = false;
			_sqlConnectionSB["Integrated Security"] = false;
			_sqlConnectionSB["Encrypt"] = true;
			_sqlConnectionSB["Connection Timeout"] = 30;
		}

		Program()   // Constructor.
		{
			int[] arrayOfTransientErrorNumbers =
				{ 4060, 40197, 40501, 40613, 49918, 49919, 49920
					//,11001   // 11001 for testing, pretend network error is transient.
				};
			m_listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
		}

		private G.List<int> m_listTransientErrorNumbers;
	}
}
```


### Eseguire il programma


L’eseguibile **RetryAdo2.exe** non invia parametri. Per eseguire il file exe in Visual Studio:


1. Impostare un punto di interruzione sull’istruzione **return;** nel metodo **Main**.

2. Fare clic sul pulsante con la freccia Start verde. Verrà visualizzata una finestra della console.

3. Quando il debugger si interrompe alla fine in **Main**, passare alla finestra della console.

4. Visualizzare le tre righe probabilmente identiche alle seguenti:


```
database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535
```


## Eseguire test sulla logica di ripetizione tentativi


Un modo utile per testare la logica di ripetizione è:


1. Aggiungere temporaneamente **11001** alla propria raccolta di valori **SqlConnection.Number** che devono essere considerati come errori temporanei.

2. Ricompilare il programma.

3. Disconnettere il computer client dalla rete.

4. Eseguire il programma in un debugger, con un punto di interruzione nel ciclo.
 - Il primo ciclo avrà esito negativo con errore 11001.

5. Quando il programma viene posizionato su un punto di interruzione durante il secondo ciclo, riconnettere il computer alla rete.

6. Riprendere l'esecuzione del programma. Avrà esito positivo durante il secondo ciclo.


### Un'altra opzione di test


Un altro modo potrebbe essere l’aggiunta della logica al programma per riconoscere un valore di parametro della riga di comando di "test". In risposta al parametro, il programma sarebbe in grado di:


1. Aggiungere temporaneamente lettere indesiderate per scrivere in modo errato il nome del server del database SQL.

2. Aggiungere temporaneamente **40615** all'elenco di errori temporanei.

3. All'inizio del secondo ciclo, vale a dire il primo ciclo di nuovi tentativi, il programma:
 - Annullerebbe l'errore di scrittura del server.
 - Rimuoverebbe 40615 dall'elenco temporaneo.


Eseguirebbe il programma con il parametro "test" e verificherebbe che ha dapprima esito negativo, ma successivamente esegue correttamente il secondo ciclo.


## Collegamenti correlati


- [Esempi di codice di avvio rapido del client per il database SQL](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=AcomDC_1203_2015-->