<properties 
   pageTitle="Esercitazione .NET sulla messaggistica negoziata del Bus di servizio| Microsoft Azure"
   description="Esercitazione di messaggistica negoziata .NET."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/14/2015"
   ms.author="sethm" />

# Esercitazione sulla messaggistica negoziata del bus di servizio - .NET

Il bus di servizio di Azure fornisce due soluzioni di messaggistica complete, tramite un servizio di "inoltro" centralizzato in esecuzione nel cloud che supporta una vasta gamma di protocolli di trasporto e standard dei servizi Web, tra cui SOAP, WS-* e REST. Il client non necessita di una connessione diretta al servizio locale, non è necessario che conosca la posizione in cui risiede il servizio e il servizio locale non richiede porte in ingresso aperte sul firewall.

La seconda soluzione di messaggistica consente funzionalità di messaggistica "negoziata". Queste funzionalità possono essere considerate come funzionalità di messaggistica asincrone o disaccoppiate che supportano scenari di pubblicazione-sottoscrizione, disaccoppiamento temporale e bilanciamento del carico tramite l'infrastruttura di messaggistica del bus di servizio. La comunicazione disaccoppiata presenta molti vantaggi, ad esempio client e server possono connettersi quando necessario ed eseguire le relative operazioni in modo asincrono.

Questa esercitazione intende offrire una panoramica ed esperienza pratica con le code, uno dei componenti principali di messaggistica negoziata del bus di servizio. Dopo aver esaminato la sequenza di argomenti in questa esercitazione, si ha a disposizione un'applicazione che popola un elenco dei messaggi, crea una coda e invia messaggi alla coda. Infine, l'applicazione riceve e visualizza i messaggi dalla coda, quindi pulisce le proprie risorse e si chiude. Per un'esercitazione equivalente che descrive come creare un'applicazione che utilizzi le funzionalità di messaggistica "inoltrata" del bus di servizio, vedere l’[esercitazione sulla messaggistica inoltrata del bus di servizio](service-bus-relay-tutorial.md).

## Introduzione e prerequisiti

Le code consentono un recapito dei messaggi di tipo First In, First Out (FIFO) a uno o più consumer concorrenti. FIFO significa che i messaggi vengono in genere ricevuti ed elaborati dai destinatari nell'ordine temporale in cui sono stati aggiunti alla coda e ogni messaggio viene ricevuto ed elaborato da un solo consumer di messaggi. Il vantaggio principale derivante dall’uso delle code è quello di ottenere un *disaccoppiamento temporale* dei componenti dell'applicazione: in altre parole, non è necessario che i producer e i consumer inviino e ricevano i messaggi contemporaneamente, in quanto i messaggi restano memorizzati nella coda. Un vantaggio correlato è quello del *livellamento del carico*, che permette ai producer e ai consumer di inviare e ricevere i messaggi con frequenze diverse.

Di seguito alcuni passaggi amministrativi e prerequisiti, da seguire prima di iniziare l'esercitazione. Il primo passaggio consiste nel creare uno spazio dei nomi del servizio e ottenere una chiave di firma di accesso condiviso (SAS). Uno spazio dei nomi servizio fornisce un limite per ogni applicazione esposta tramite il bus di servizio. Una chiave di firma di accesso condiviso viene generata dal sistema quando viene creato uno spazio dei nomi del servizio. La combinazione di spazio dei nomi servizio e chiave di firma di accesso condiviso fornisce le credenziali che consentono al bus di servizio di autenticare l'accesso a un'applicazione.

### Creazione di uno spazio dei nomi del servizio e acquisizione di una chiave SAS

1. Per creare uno spazio dei nomi del servizio, visitare il [portale di Azure classico][]. Fare clic su **Bus di servizio** sul lato sinistro, quindi fare clic su **Crea**. Digitare un nome per lo spazio dei nomi, quindi fare clic sul segno di spunta.

1. Nella finestra principale del [portale di Azure classico][], fare clic sul nome dello spazio dei nomi del servizio creato al passaggio precedente.

1. Fare clic su **Configure**.

1. Nella sezione **Generatore di firma di accesso condiviso**, prendere nota della chiave primaria associata al criterio **RootManagerSharedAccessKey** oppure copiarlo negli Appunti. Questo valore sarà utilizzato più avanti nell'esercitazione.

Il passaggio successivo consiste nel creare un progetto di Visual Studio e scrivere due funzioni di supporto che caricano un elenco delimitato da virgole di messaggi nell'oggetto fortemente tipizzato di [elenco](https://msdn.microsoft.com/library/6sh2ey19.aspx) di .NET [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx).

### Creare un progetto di Visual Studio

1. Aprire Visual Studio come amministratore facendo clic con il pulsante destro del mouse sul programma nel menu Start, quindi selezionare **Esegui come amministratore**.

1. Creare un nuovo progetto di applicazione console. Fare clic sul menu**File**, selezionare **Nuovo**, quindi fare clic su **Progetto**. Nella finestra di dialogo **Nuovo progetto**, fare clic su **Visual C#** (se **Visual C#** non è visibile, cercare in **Altri linguaggi**), fare clic sul modello **Applicazione console** e denominarlo **QueueSample**. Usare il valore predefinito del campo **Posizione**. Fare clic su **OK** per creare il progetto.

1. Utilizzare Gestione pacchetti NuGet per aggiungere le librerie del bus di servizio al progetto:
	1. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla cartella di progetto e selezionare **Gestisci pacchetti NuGet**.
	2. Nella finestra di dialogo **Gestisci pacchetti Nuget**, eseguire la ricerca online per il **bus di servizio** e fare clic su **Installa**. <br />
1. In Esplora soluzioni fare doppio clic sul file Program.cs per aprirlo nell'editor di Visual Studio. Modificare il nome dello spazio dei nomi da nome predefinito di `QueueSample` a `Microsoft.ServiceBus.Samples`.

	```
	Microsoft.ServiceBus.Samples
	{
	    …
	```

1. Modificare le istruzioni `using` come illustrato nel seguente codice.

	```
	using System;
	using System.Collections.Generic;
	using System.Data;
	using System.IO;
	using System.Threading;
	using Microsoft.ServiceBus.Messaging;
	```

1. Creare un file di testo denominato Data.csv e copiarvi il seguente testo delimitato da virgole.

	```
	IssueID,IssueTitle,CustomerID,CategoryID,SupportPackage,Priority,Severity,Resolved
	1,Package lost,1,1,Basic,5,1,FALSE
	2,Package damaged,1,1,Basic,5,1,FALSE
	3,Product defective,1,2,Premium,5,2,FALSE
	4,Product damaged,2,2,Premium,5,2,FALSE
	5,Package lost,2,2,Basic,5,2,TRUE
	6,Package lost,3,2,Basic,5,2,FALSE
	7,Package damaged,3,7,Premium,5,3,FALSE
	8,Product defective,3,2,Premium,5,3,FALSE
	9,Product damaged,4,6,Premium,5,3,TRUE
	10,Package lost,4,8,Basic,5,3,FALSE
	11,Package damaged,5,4,Basic,5,4,FALSE
	12,Product defective,5,4,Basic,5,4,FALSE
	13,Package lost,6,8,Basic,5,4,FALSE
	14,Package damaged,6,7,Premium,5,5,FALSE
	15,Product defective,6,2,Premium,5,5,FALSE
	```

	Salvare e chiudere il file Data.csv e ricordare il percorso in cui è stato salvato.

1. In Esplora soluzioni, fare doppio clic con il pulsante destro del mouse sul nome del progetto (in questo esempio, **QueueSample**), fare clic su **Aggiungi**, quindi fare clic su **Elemento esistente**.

1. Cercare il file Data.csv creato al passaggio 6. Fare clic sul file, quindi fare clic su **Aggiungi**. Assicurarsi che l’opzione **Tutti i file (*.*)** sia selezionata nell'elenco dei tipi di file.

### Creazione di una funzione che analizza un elenco di messaggi

1. Prima del metodo `Main()`, dichiarare due variabili: una di tipo **DataTable**, per contenere l'elenco dei messaggi in Data.csv. L'altra deve essere di tipo List object, fortemente tipizzato in [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Quest'ultimo è l'elenco dei messaggi negoziati utilizzati dai passaggi successivi dell'esercitazione.

	```
	namespace Microsoft.ServiceBus.Samples
	{
	    publicclass Program
	    {
	
	        private static DataTable issues;
	        private static List<BrokeredMessage> MessageList;
	```

1. Al di fuori di `Main()`, definire un metodo `ParseCSV()` che analizzi l'elenco dei messaggi in Data.csv e carichi i messaggi in una tabella [DataTable](https://msdn.microsoft.com/library/azure/system.data.datatable.aspx), come illustrato di seguito. Il metodo restituisce un oggetto **DataTable**.

	```
	static DataTable ParseCSVFile()
	{
	    DataTable tableIssues = new DataTable("Issues");
	    string path = @"..\..\data.csv";
	    try
	    {
	        using (StreamReader readFile = new StreamReader(path))
	        {
	            string line;
	            string[] row;
	
	            // create the columns
	            line = readFile.ReadLine();
	            foreach (string columnTitle in line.Split(','))
	            {
	                tableIssues.Columns.Add(columnTitle);
	            }
	
	            while ((line = readFile.ReadLine()) != null)
	            {
	                row = line.Split(',');
	                tableIssues.Rows.Add(row);
	            }
	        }
	    }
	    catch (Exception e)
	    {
	        Console.WriteLine("Error:" + e.ToString());
	    }
	
	    return tableIssues;
	}
	```

1. Nel metodo `Main()`, aggiungere un'istruzione che chiama il metodo `ParseCSVFile()`:

	```
	public static void Main(string[] args)
	{
	
	    // Populate test data
	    issues = ParseCSVFile();
	
	}
	```

### Creazione di una funzione che carica un elenco di messaggi

1. Al di fuori di `Main()`, definire un metodo `GenerateMessages()` che accetta l’oggetto **DataTable** restituito da `ParseCSVFile()` e carica la tabella in un elenco fortemente tipizzato di messaggi negoziati. Il metodo restituisce quindi l’oggetto **Elenco**, come nell’esempio di seguito. 

	```
	static List<BrokeredMessage> GenerateMessages(DataTable issues)
	{
	    // Instantiate the brokered list object
	    List<BrokeredMessage> result = new List<BrokeredMessage>();
	
	    // Iterate through the table and create a brokered message for each row
	    foreach (DataRow item in issues.Rows)
	    {
	        BrokeredMessage message = new BrokeredMessage();
	        foreach (DataColumn property in issues.Columns)
	        {
	            message.Properties.Add(property.ColumnName, item[property]);
	        }
	        result.Add(message);
	    }
	    return result;
	}
	```

1. In `Main()`, direttamente sotto la chiamata a `ParseCSVFile()`, aggiungere un'istruzione che chiama il metodo `GenerateMessages()` con il valore restituito da `ParseCSVFile()` come argomento:

	```
	public static void Main(string[] args)
	{
	
	    // Populate test data
	    issues = ParseCSVFile();
	    MessageList = GenerateMessages(issues);
	}
	```

### Acquisizione delle credenziali dell’utente

1. Innanzitutto, creare tre variabili stringa globali per contenere tali valori. Dichiarare le variabili direttamente dopo le dichiarazioni di variabili precedenti; ad esempio:

	```
	namespace Microsoft.ServiceBus.Samples
	{
	    publicclass Program
	    {
	
	        privatestatic DataTable issues;
	        privatestatic List<BrokeredMessage> MessageList; 
	        // Add these variablesprivatestaticstring ServiceNamespace;
	        privatestaticstring sasKeyName = "RootManageSharedAccessKey";
	        privatestaticstring sasKeyValue;
	        …
	```

1. Successivamente, creare una funzione che accetta e memorizza lo spazio dei nomi del servizio e la chiave SAS. Aggiungere questo metodo all'esterno di `Main()`. Ad esempio:

	```
	static void CollectUserInput()
	{
	    // User service namespace
	    Console.Write("Please enter the service namespace to use: ");
	    ServiceNamespace = Console.ReadLine();
	
	    // Issuer key
	    Console.Write("Please enter the SAS key to use: ");
	    sasKeyValue = Console.ReadLine();
	}
	```

1. In `Main()`, direttamente sotto la chiamata a `GenerateMessages()`, aggiungere un'istruzione che chiama il metodo`CollectUserInput()`:

	```
	public static void Main(string[] args)
	{
	
	    // Populate test data
	    issues = ParseCSVFile();
	    MessageList = GenerateMessages(issues);
	    
	    // Collect user input
	    CollectUserInput();
	}
	```

### Compilare la soluzione

Dal menu **Compila** in Visual Studio, è possibile fare clic su **Compila soluzione** o premere F6 per confermare la correttezza del lavoro svolto finora.

Creazione di credenziali di gestione

Questa è la seconda fase nell'esercitazione sulle funzionalità di messaggistica del bus di servizio. In questa fase, si definiscono le operazioni di gestione utilizzate per creare le credenziali di firma condivisa (SAS) con cui l'applicazione verrà autorizzata.

## Creazione delle credenziali di gestione

In questa fase, si definiscono le operazioni di gestione utilizzate per creare le credenziali di firma condivisa (SAS) con cui l'applicazione verrà autorizzata.

1. Per maggiore chiarezza, l’esercitazione inserisce tutte le operazioni della coda in un metodo separato. Creare un metodo `Queue()` nella classe `Program`, di seguito il metodo `Main()`. Ad esempio:
 
	```
	public static void Main(string[] args)
	{
	…
	}
	staticvoid Queue()
	{
	}
	```

1. Il passaggio successivo consiste nel creare credenziali della firma di accesso condiviso utilizzando un oggetto [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx). Il metodo di creazione accetta il nome della chiave della firma di accesso condiviso e il valore ottenuto nel metodo `CollectUserInput()`. Aggiungere il codice seguente al metodo `Queue()`:

	```
	staticvoid Queue()
	{
	    // Create management credentials
	    TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
	}
	```
### Creazione della gestione dello spazio dei nomi

1. Creare un nuovo oggetto di gestione dello spazio dei nomi, con un URI che contiene il nome dello spazio dei nomi e le credenziali di gestione ottenute nell'ultimo passaggio, come argomenti. Aggiungere questo codice direttamente sotto il codice aggiunto nel passaggio precedente:
	
	```
	NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", <namespaceName>, string.Empty), credentials);
	```

### Esempio

A questo punto, il codice dovrebbe essere simile al seguente:

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  class Program
  {
    private static DataTable issues;
    private static List<BrokeredMessage> MessageList;
    private static string ServiceNamespace;
    private static string sasKeyName = "RootManageSharedAccessKey";
    private static string sasKeyValue;

    static void Main(string[] args)
    {
      // Populate test data
      issues = ParseCSVFile();
      MessageList = GenerateMessages(issues);

      // Collect user input
      CollectUserInput();

      // Add this call
      Queue();
    }

    static void Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
    }

    static DataTable ParseCSVFile()
    {
      DataTable tableIssues = new DataTable("Issues");
      string path = @"..\..\data.csv";
      try
      {
        using (StreamReader readFile = new StreamReader(path))
        {
          string line;
          string[] row;

          // create the columns
          line = readFile.ReadLine();
          foreach (string columnTitle in line.Split(','))
          {
            tableIssues.Columns.Add(columnTitle);
          }

          while ((line = readFile.ReadLine()) != null)
          {
            row = line.Split(',');
            tableIssues.Rows.Add(row);
          }
        }
      }
      catch (Exception e)
      {
        Console.WriteLine("Error:" + e.ToString());
      }

      return tableIssues;
    }

    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
      // Instantiate the brokered list object
      List<BrokeredMessage> result = new List<BrokeredMessage>();

      // Iterate through the table and create a brokered message for each row
      foreach (DataRow item in issues.Rows)
      {
        BrokeredMessage message = new BrokeredMessage();
        foreach (DataColumn property in issues.Columns)
        {
          message.Properties.Add(property.ColumnName, item[property]);
        }
        result.Add(message);
      }
      return result;
    }

    static void CollectUserInput()
    {
      // User service namespace
      Console.Write("Please enter the service namespace to use: ");
      ServiceNamespace = Console.ReadLine();

      // Issuer key
      Console.Write("Please enter the issuer key to use: ");
      sasKeyValue = Console.ReadLine();
    }
  }
}
```

Nel passaggio successivo, si crea la coda a cui vengono inviati messaggi.

## Invio di messaggi alla coda

In questa fase, si crea una coda, per poi inviare i messaggi contenuti nell'elenco dei messaggi negoziati a tale coda.

### Creazione di una coda e invio di messaggi alla coda

1. Per prima cosa, creare la coda. Ad esempio, chiamarla `myQueue` e dichiararla direttamente dopo le operazioni di gestione aggiunte durante la fase precedente:

	```
	QueueDescription myQueue;
	myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
	```

1. Nel metodo `Queue()`, creare un oggetto factory di messaggistica con un URI per il bus di servizio appena creato come argomento. Aggiungere il codice seguente direttamente dopo le operazioni di gestione aggiunte nella fase precedente:

	```
	MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
	```

1. Successivamente, creare l'oggetto coda utilizzando la classe [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx). Aggiungere il codice seguente direttamente dopo il codice aggiunto nella fase precedente:

	```
	QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");
	```

1. Successivamente, aggiungere il codice che consente di scorrere l'elenco di messaggi negoziati creato in precedenza, inviando ciascun messaggio alla coda. Aggiungere il codice seguente direttamente dopo l’istruzione `CreateQueueClient()` nella fase precedente:
	
	```
	// Send messages
	Console.WriteLine("Now sending messages to the queue.");
	for (int count = 0; count < 6; count++)
	{
	    var issue = MessageList[count];
	    issue.Label = issue.Properties["IssueTitle"].ToString();
	    myQueueClient.Send(issue);
	    Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
	}
	```

## Ricezione di messaggi dalla coda

In questa fase, si ottiene l'elenco dei messaggi dalla coda creata nella fase precedente.

### Creazione di un destinatario e ricezione di messaggi dalla coda

Nel metodo `Queue()`, scorrere la coda e ricevere messaggi utilizzando il metodo [Microsoft.ServiceBus.Messaging.QueueClient.Receive](https://msdn.microsoft.com/library/azure/hh322678.aspx), stampando ciascun messaggio nella console. Aggiungere il seguente codice direttamente sotto il codice aggiunto nella fase precedente:

	```
	Console.WriteLine("Now receiving messages from Queue.");
	BrokeredMessage message;
	while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
	    {
	        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
	        message.Complete();
	
	        Console.WriteLine("Processing message (sleeping...)");
	        Thread.Sleep(1000);
	    }
	```

### Terminare il metodo `Queue()` e pulire le risorse

Direttamente sotto il codice precedente, aggiungere il codice seguente per pulire la factory del messaggio e le risorse della coda:

	```
	factory.Close();
	myQueueClient.Close();
	namespaceClient.DeleteQueue("IssueTrackingQueue");
	```

### Chiamare il metodo `Queue()`

L'ultima fase consiste nell'aggiungere un'istruzione che chiama il metodo `Queue()` da `Main()`. Aggiungere la seguente riga di codice evidenziata alla fine di Main ():
	
	```
	public static void Main(string[] args)
	{
	    // Collect user input
	    CollectUserInput();
	
	    // Populate test data
	    issues = ParseCSVFile();
	    MessageList = GenerateMessages(issues);
	
	    // Add this call
	    Queue();
	}
	```

### Esempio

Il codice seguente contiene l’applicazione **QueueSample** completa.

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  class Program
  {
    private static DataTable issues;
    private static List<BrokeredMessage> MessageList;
    private static string ServiceNamespace;
    private static string sasKeyName = "RootManageSharedAccessKey";
    private static string sasKeyValue;

    static void Main(string[] args)
    {
      // Populate test data
      issues = ParseCSVFile();
      MessageList = GenerateMessages(issues);

      // Collect user input
      CollectUserInput();

      // Add this call
      Queue();
    }

    static void Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);

      QueueDescription myQueue;
      myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");

      MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
      QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");

      // Send messages
      Console.WriteLine("Now sending messages to the Queue.");
      for (int count = 0; count < 6; count++)
      {
        var issue = MessageList[count];
        issue.Label = issue.Properties["IssueTitle"].ToString();
        myQueueClient.Send(issue);
        Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
      }

      Console.WriteLine("Now receiving messages from Queue.");
      BrokeredMessage message;
      while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
      {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();

        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
      }

      factory.Close();
      myQueueClient.Close();
      namespaceClient.DeleteQueue("IssueTrackingQueue");
    }

    static DataTable ParseCSVFile()
    {
      DataTable tableIssues = new DataTable("Issues");
      string path = @"..\..\data.csv";
      try
      {
        using (StreamReader readFile = new StreamReader(path))
        {
          string line;
          string[] row;

          // create the columns
          line = readFile.ReadLine();
          foreach (string columnTitle in line.Split(','))
          {
            tableIssues.Columns.Add(columnTitle);
          }

          while ((line = readFile.ReadLine()) != null)
          {
            row = line.Split(',');
            tableIssues.Rows.Add(row);
          }
        }
      }
      catch (Exception e)
      {
        Console.WriteLine("Error:" + e.ToString());
      }

      return tableIssues;
    }

    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
      // Instantiate the brokered list object
      List<BrokeredMessage> result = new List<BrokeredMessage>();

      // Iterate through the table and create a brokered message for each row
      foreach (DataRow item in issues.Rows)
      {
        BrokeredMessage message = new BrokeredMessage();
        foreach (DataColumn property in issues.Columns)
        {
          message.Properties.Add(property.ColumnName, item[property]);
        }
        result.Add(message);
      }
      return result;
    }

    static void CollectUserInput()
    {
      // User service namespace
      Console.Write("Please enter the service namespace to use: ");
      ServiceNamespace = Console.ReadLine();

      // Issuer key
      Console.Write("Please enter the issuer key to use: ");
      sasKeyValue = Console.ReadLine();
    }
  }
}
```

## Compilazione ed esecuzione dell'applicazione QueueSample

Completati i passaggi precedenti, è possibile compilare ed eseguire l’applicazione **QueueSample**.

### Compilazione dell'applicazione QueueSample

In Visual Studio dal menu **Compila**, fare clic su **Compila soluzione** o premere F6. Se si presentano errori, verificare che il codice sia corretto in base all'esempio completo presentato alla fine del passaggio precedente.

### Esecuzione dell'applicazione QueueSample

1. Prima di eseguire l'applicazione, è necessario verificare di aver creato uno spazio dei nomi del servizio e ottenuto una chiave della firma di accesso condiviso, come descritto in [Introduzione e prerequisiti](#introduction-and-prerequisites).

1. Aprire un browser e raggiungere il [portale di Azure classico][].

1. Nella struttura ad albero a sinistra fare clic su **Bus di servizio**.

1. Fare clic sul nome dello spazio dei nomi da utilizzare. Nella parte inferiore della pagina, fare clic su **Informazioni di connessione**. Prendere nota della stringa di connessione con la chiave SAS o copiarla negli Appunti.

1. In Visual Studio dal menu **Debug**, fare clic su **Avvia debug** o premere F5. Quando richiesto, immettere il nome dello spazio dei nomi servizio e la chiave ottenuta nel passaggio precedente.

## Passaggi successivi

Questa esercitazione ha illustrato come creare un'applicazione client del bus di servizio e un servizio utilizzando le funzionalità di messaggistica negoziata del bus di servizio. Per un'esercitazione simile che utilizza la [messaggistica inoltrata](service-bus-messaging-overview.md#Relayed-messaging) del bus di servizio, consultare [Esercitazione sulla messaggistica inoltrata del bus di servizio](service-bus-relay-tutorial.md).

Per altre informazioni sul [Bus di servizio](https://azure.microsoft.com/services/service-bus/), vedere gli argomenti seguenti.

- [Panoramica della messaggistica del bus di servizio](service-bus-messaging-overview.md)
- [Dati fondamentali del bus di servizio](service-bus-fundamentals-hybrid-solutions.md)
- [Architettura del bus di servizio](service-bus-architecture.md)

[portale di Azure classico]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0121_2016-->