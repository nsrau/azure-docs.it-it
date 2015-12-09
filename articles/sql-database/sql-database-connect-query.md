<properties
	pageTitle="Connettersi al database SQL con una query in C# | Microsoft Azure"
	description="Scrivere un programma in C# per eseguire query e connettersi al database SQL. Informazioni su indirizzi IP, stringhe di connessione, accesso sicuro e Visual Studio gratuito."
	services="sql-database"
	keywords="query in c# al database,query in c#,connettersi al database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="10/09/2015"
	ms.author="genemi"/>


# Scrivere un programma in C&#x23; per eseguire query e connettersi a un database SQL

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Informazioni su come scrivere un programma in C# per eseguire query e connettersi a un database SQL di Azure nel cloud.

Questo articolo descrive tutte le procedure necessarie ed è destinato agli utenti che non hanno familiarità con il database SQL di Azure, C# e ADO.NET. Gli utenti con conoscenze di Microsoft SQL Server e C# possono ignorare alcune procedure e passare direttamente a quelle specifiche per il database SQL.


## Prerequisiti


Per eseguire l'esempio di codice della query in C#, sono necessari i prerequisiti seguenti:


- Un account e una sottoscrizione di Azure. È possibile iscriversi per una [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).


- Un database **AdventureWorksLT** dimostrativo nel servizio database SQL di Azure.
 - È possibile [crearne uno](sql-database-get-started.md) in pochi minuti.


- Visual Studio 2013 Update 4 o versioni successive. Ora Microsoft fornisce Visual Studio Community *gratuitamente*.
 - [Download di Visual Studio Community](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [Altre opzioni per la versione gratuita di Visual Studio](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)
 - In alternativa, andare direttamente al [passaggio](#InstallVSForFree) corrispondente più avanti in questo argomento, che descrive come installare Visual Studio dal [portale di Azure](http://portal.azure.com/).


<a name="InstallVSForFree" id="InstallVSForFree"></a>

& nbsp;

## Passaggio 1: Installare Visual Studio Community gratuitamente


Per installare Visual Studio, è possibile:

- Installare Visual Studio Community gratuitamente passando alle pagine Web dedicate a Visual Studio in cui sono disponibili download gratuiti o altre opzioni.
- Accedere al [portale di Azure](http://portal.azure.com/) e passare direttamente alla pagina Web per il download, seguendo la procedura descritta di seguito.


### Visual Studio tramite il portale di Azure


1. Accedere al [portale di Azure](http://portal.azure.com/), http://portal.azure.com/.

2. Fare clic su **ESPLORA* TUTTO** > **database SQL**. Verrà visualizzato un pannello in cui viene eseguita la ricerca dei database.

3. Nella casella di testo del filtro in alto digitare il nome del database **AdventureWorksLT**.

4. Quando viene visualizzata la riga corrispondente al database nel server, fare clic su di essa. Verrà visualizzato un pannello per il database.

5. Per praticità, ridurre a icona i pannelli precedenti facendo clic sul controllo corrispondente.

6. Fare clic sul pulsante **Apri in Visual Studio** in alto nel pannello del database. Verrà visualizzato un nuovo pannello per Visual Studio con i collegamenti alle pagine da cui è possibile eseguire l'installazione di Visual Studio.

	![Pulsante Apri in Visual Studio][20-OpenInVisualStudioButton]

7. Fare clic sul collegamento **Community (Gratuito)** o simile. Verrà aggiunta una nuova pagina Web.

8. Per installare Visual Studio, usare i collegamenti disponibili nella nuova pagina Web.

9. Al termine dell'installazione di Visual Studio, nel pannello **Apri in Visual Studio** fare clic sul pulsante **Apri in Visual Studio**. Verrà aperto Visual Studio.

10. Per poter usare il riquadro **Esplora oggetti di SQL Server**, in Visual Studio verrà visualizzata una finestra di dialogo in cui è necessario compilare i campi relativi alla stringa di connessione.
 - Scegliere **Autenticazione di SQL Server**, non **Autenticazione di Windows**.
 - Ricordare di specificare il database **AdventureWorksLT** in **Opzioni** > **Proprietà connessione** nella finestra di dialogo.

11. In **Esplora oggetti di SQL Server** espandere il nodo del database.


## Passaggio 2: Creare un nuovo progetto in Visual Studio


In Visual Studio creare un nuovo progetto basato sul modello di base per C# > Windows > **Applicazione console**.


1. Fare clic su **File** > **Nuovo** > **Progetto**. Verrà visualizzata la finestra di dialogo ****.

2. In **Installati** espandere fino a C# e Windows, in modo da visualizzare l'opzione **Applicazione console** nel riquadro centrale.

	![Finestra di dialogo Nuovo progetto][30-VSNewProject]

2. In **Nome** immettere **ConnectAndQuery\_Example**. Fare clic su **OK**.


## Passaggio 3: Aggiungere un riferimento all'assembly per l'elaborazione della configurazione


L'esempio in C# usa l'assembly **System.Configuration.dll** di .NET Framework, quindi verrà aggiunto un riferimento a tale assembly.


1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Riferimenti** > **Aggiungi riferimento**. Verrà visualizzata la finestra **Gestione riferimenti**.

2. Espandere **Assembly** > **Framework**.

3. Scorrere l'elenco e fare clic per evidenziare **System.Configuration**. Assicurarsi che la casella di controllo corrispondente sia selezionata.

4. Fare clic su **OK**.

5. Per compilare il programma, scegliere **COMPILA** > **Compila soluzione**.


## Passaggio 4: Ottenere la stringa di connessione


Per copiare la stringa di connessione necessaria per connettersi al database SQL, usare il [portale di Azure](http://portal.azure.com/).

Per prima cosa, è necessario connettere Visual Studio al database **AdventureWorksLT** del database SQL di Azure.


[AZURE.INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]


## Passaggio 5: Aggiungere la stringa di connessione al file App.config


1. In Visual Studio aprire il file App.config nel riquadro Esplora soluzioni.

2. Aggiungere un elemento **&#x3c;configuration&#x3e; &#x3c;/configuration&#x3e;** come illustrato nell'esempio di codice per App.config seguente.
 - Sostituire i *{segnaposto}* con i valori effettivi:

```
	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <startup>
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	    </startup>

		<connectionStrings>
			<clear />
			<add name="ConnectionString4NoUserIDNoPassword"
			connectionString="Server=tcp:{your_serverName_here}.database.windows.net,1433; Database={your_databaseName_here}; Connection Timeout=30; Encrypt=True; TrustServerCertificate=False;"
			/>
		</connectionStrings>
	</configuration>
```

3. Salvare il file App.config modificato.

4. Nel riquadro Esplora soluzioni fare clic con il pulsante destro del mouse sul nodo **App.config** e quindi scegliere **Proprietà**.

5. Impostare **Copia nella directory di output** su **Copia sempre**.
 - In questo modo, il contenuto del file App.config sostituirà il contenuto del file &#x2a;.exe.config nella directory in cui viene compilato il file &#x2a;.exe. La sostituzione si verifica ogni volta che viene ricompilato il file &#x2a;.exe.
 - Il file &#x2a;.exe.config viene letto durante l'esecuzione del programma C# di esempio.

	![Copia in directory di output = Copia sempre][50-VSCopyToOutputDirectoryProperty]


## Passaggio 6: Copiare il codice C# di esempio


1. In Visual Studio aprire il file **Program.cs** dal riquadro **Esplora soluzioni**.

	![Copiare il codice della query in C# di esempio][40-VSProgramCsOverlay]

2. Sovrascrivere tutto il codice di avvio in Program.cs incollando il codice C# di esempio seguente.
 - Se si vuole un esempio di codice più breve, è possibile assegnare l'intera stringa di connessione come valore letterale alla variabile **SQLConnectionString**. È quindi possibile cancellare i due metodi **GetConnectionStringFromExeConfig** e **GatherPasswordFromConsole**.


```
using System;  // C#
using G = System.Configuration;   // System.Configuration.dll
using D = System.Data;            // System.Data.dll
using C = System.Data.SqlClient;  // System.Data.dll
using T = System.Text;

namespace ConnectAndQuery_Example
{
	class Program
	{
		static void Main()
		{
			string connectionString4NoUserIDNoPassword,
				password, userName, SQLConnectionString;

			// Get most of the connection string from ConnectAndQuery_Example.exe.config
			// file, in the same directory where ConnectAndQuery_Example.exe resides.
			connectionString4NoUserIDNoPassword = Program.GetConnectionStringFromExeConfig
				("ConnectionString4NoUserIDNoPassword");
			// Get the user name from keyboard input.
			Console.WriteLine("Enter your User ID, without the trailing @ and server name: ");
			userName = Console.ReadLine();
			// Get the password from keyboard input.
			password = Program.GatherPasswordFromConsole();

			SQLConnectionString = "Password=" + password + ';' +
				"User ID=" + userName + ";" + connectionString4NoUserIDNoPassword;

			// Create an SqlConnection from the provided connection string.
			using (C.SqlConnection connection = new C.SqlConnection(SQLConnectionString))
			{
				// Formulate the command.
				C.SqlCommand command = new C.SqlCommand();
				command.Connection = connection;

				// Specify the query to be executed.
				command.CommandType = D.CommandType.Text;
				command.CommandText = @"
					SELECT TOP 9 CustomerID, NameStyle, Title, FirstName, LastName
					FROM SalesLT.Customer;  -- In AdventureWorksLT database.
					";
				// Open a connection to database.
				connection.Open();

				// Read data returned for the query.
				C.SqlDataReader reader = command.ExecuteReader();
				while (reader.Read())
				{
					Console.WriteLine("Values:  {0}, {1}, {2}, {3}, {4}",
						reader[0], reader[1], reader[2], reader[3], reader[4]);
				}
			}
			Console.WriteLine("View the results here, then press any key to finish...");
			Console.ReadKey(true);
		}
		//----------------------------------------------------------------------------------

		static string GetConnectionStringFromExeConfig(string connectionStringNameInConfig)
		{
			G.ConnectionStringSettings connectionStringSettings =
				G.ConfigurationManager.ConnectionStrings[connectionStringNameInConfig];

			if (connectionStringSettings == null)
			{
				throw new ApplicationException(String.Format
					("Error. Connection string not found for name '{0}'.",
					connectionStringNameInConfig));
			}
				return connectionStringSettings.ConnectionString;
		}

		static string GatherPasswordFromConsole()
		{
			T.StringBuilder passwordBuilder = new T.StringBuilder(32);
			ConsoleKeyInfo key;
			Console.WriteLine("Enter your password: ");
			do
			{
				key = Console.ReadKey(true);
				if (key.Key != ConsoleKey.Backspace)
				{
					passwordBuilder.Append(key.KeyChar);
					Console.Write("*");
				}
				else  // Backspace char was entered.
				{
					// Retreat the cursor, overlay '*' with ' ', retreat again.
					Console.Write("\b \b");
					passwordBuilder.Length = passwordBuilder.Length - 1;
				}
			}
			while (key.Key != ConsoleKey.Enter); // Enter key will end the looping.
			Console.WriteLine(Environment.NewLine);
			return passwordBuilder.ToString();
		}
	}
}
```


### Compilare il programma


1. Per compilare il programma in Visual Studio, scegliere **Compila** > **Compila soluzione**.


### Riepilogo delle azioni nel programma di esempio


1. Legge la maggior parte della stringa di connessione SQL da un file di configurazione.

2. Raccoglie il nome utente e la password dalla tastiera e li aggiunge per completare la stringa di connessione.

3. Usare la stringa di connessione e le classi ADO.NET per connettersi al database **AdventureWorksLT** dimostrativo nel database SQL.

4. Esegue un'istruzione **SELECT** di SQL per leggere i dati dalla tabella **SalesLT**.

5. Stampa le righe restituite nella console.


Tentiamo di mantenere l'esempio per C# il più breve possibile. Tuttavia, abbiamo aggiunto il codice per la lettura di un file di configurazione per rispondere alle richieste dei clienti. Concordiamo sul fatto che i programmi a livello di produzione debbano usare file di configurazione anziché i valori letterali hardcoded nel file EXE.


> [AZURE.WARNING]Sempre ai fini della brevità del codice, abbiamo preferito non includere il codice per la gestione delle eccezioni e la logica di ripetizione dei tentativi in questo esempio didattico. Ricordare però che i programmi per la produzione che interagiscono con un database cloud dovranno includere il codice per entrambe le funzionalità.
>
> Fare clic [qui](sql-database-develop-csharp-retry-windows.md) per un esempio di codice che contiene la logica di ripetizione dei tentativi.


## Passaggio 7: Aggiungere l'intervallo di indirizzi IP consentiti nel firewall del server


Il programma C# client non potrà connettersi al database SQL fino a quando l'indirizzo IP del computer client non viene aggiunto nel firewall del database SQL. Il programma restituirà un messaggio di errore che indica l'indirizzo IP necessario.


Per aggiungere l'indirizzo IP, è possibile usare il [portale di Azure](http://portal.azure.com/).



[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]



Per altre informazioni, vedere <br/> [Procedura: configurare le impostazioni del firewall nel database SQL](sql-database-configure-firewall-settings.md).



## Passaggio 8: Eseguire il programma


1. Per eseguire il programma della query in C# in Visual Studio, scegliere **Avvia debug** dal menu **DEBUG**. Verrà visualizzata una finestra della console.

2. Immettere nome utente e password, come richiesto.
 - Alcuni strumenti di connessione richiedono che venga aggiunto il suffisso "@{nome\_server\_utente}", ma per ADO.NET questo suffisso è facoltativo. Non preoccuparsi di digitarlo.

3. Verranno visualizzate le righe di dati.


## Collegamenti correlati


- [Esempi di codice di avvio rapido del client per il database SQL](sql-database-develop-quick-start-client-code-samples.md)

- Se il programma client viene eseguito in una macchina virtuale di Azure, leggere le informazioni sulle porte TCP diverse dalla porta 1433 in:<br/>[Porte superiori alla 1433 per ADO.NET 4.5 e database SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md).



<!-- Image references. -->

[20-OpenInVisualStudioButton]: ./media/sql-database-connect-query/connqry-free-vs-e.png

[30-VSNewProject]: ./media/sql-database-connect-query/connqry-vs-new-project-f.png

[40-VSProgramCsOverlay]: ./media/sql-database-connect-query/connqry-vs-program-cs-overlay-g.png

[50-VSCopyToOutputDirectoryProperty]: ./media/sql-database-connect-query/connqry-vs-appconfig-copytoputputdir-h.png

<!---HONumber=AcomDC_1203_2015-->