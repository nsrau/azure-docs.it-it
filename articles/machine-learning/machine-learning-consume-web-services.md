<properties 
	pageTitle="Come utilizzare un servizio Web di apprendimento automatico pubblicato da una prova di Machine Learning | Azure" 
	description="Dopo la pubblicazione di un servizio di apprendimento automatico, può essere utilizzato il servizio Web RESTFul che viene reso disponibile come servizio di richiesta-risposta o come un servizio di esecuzione del batch." 
	services="machine-learning" 
	solutions="big-data" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="tbd" 
	ms.date="02/20/2015" 
	ms.author="bradsev" />


# Come utilizzare un servizio Web di Azure Machine Learning pubblicato

## Introduzione

Quando viene pubblicato come servizio Web, gli esperimenti Azure Machine Learning forniscono un'API REST che può essere utilizzata da un'ampia gamma di dispositivi e piattaforme. Infatti, la semplice API REST accetta e risponde con messaggi in formato JSON. Il portale di Azure Machine Learning fornisce il codice che può essere utilizzato per chiamare il servizio Web in R, c# e Python. Ma questi servizi possono essere chiamati con qualsiasi linguaggio di programmazione e da qualsiasi dispositivo che soddisfi i tre criteri:

* Dispone di una connessione di rete
* Dispone di funzionalità di SSL per eseguire le richieste HTTPS
* È in grado di analizzare JSON (dalle librerie disponibili o supporto)

Ciò significa che i servizi possono essere utilizzati da applicazioni Web, applicazioni per dispositivi mobili, applicazioni desktop personalizzate e persino all'interno di Excel.  

Un servizio Web di Azure Machine Learning può essere utilizzato in due modi diversi, come un servizio di richiesta-risposta o come un servizio di esecuzione batch. In ogni scenario, la funzionalità viene fornita tramite il servizio Web RESTFul reso disponibile per l'utilizzo dopo la pubblicazione dell'esperimento. La distribuzione di un servizio Web di apprendimento automatico in Azure con un endpoint del servizio Web di Azure, dove il servizio viene ridimensionato automaticamente in base all'utilizzo, è possibile evitare i costi iniziali e successivi per le risorse hardware.

<!-- Alla pubblicazione dell'articolo, correggere il collegamento e rimuovere i commenti
Per ulteriori informazioni su come gestire il servizio Web Azure Machine Learning utilizzando l'API REST, vedere **Endpoint del servizio Web Azure Machine Learning**. 
-->

Per informazioni su come creare e pubblicare un servizio Web di Azure Machine Learning, 
vedere [Pubblicazione di un servizio Web di Azure Machine Learning][pubblicazione]. Per una procedura dettagliata di creazione e pubblicazione di un esperimento di Machine Learning, vedere [Sviluppo di una soluzione predittiva tramite Azure Machine Learning][procedura dettagliata].

[pubblicazione]: machine-learning-publish-a-machine-learning-web-service.md
[procedura dettagliata]: machine-learning-walkthrough-develop-predictive-solution.md


## Servizio di richiesta-risposta (RRS)

Un servizio di richiesta-risposta (RRS) è un servizio Web a bassa latenza e altamente scalabile utilizzato per fornire un'interfaccia ai modelli senza stato,creati e pubblicati da un esperimento Azure Machine Learning Studio.

RRS accetta una sola riga di parametri di input e genera una sola riga di output. La riga di output può contenere più colonne.

Un esempio per RRS convalida l'autenticità di un'applicazione. In questo caso, è possibile prevedere centinaia di milioni di installazioni di un'applicazione. All'avvio dell'applicazione, effettua una chiamata al servizio RRS con input pertinente. Quindi, l'applicazione riceve una risposta di convalida dal servizio che consente o blocca le esecuzioni da parte dell'applicazione.


## Servizio Esecuzione batch (BES)
 
Un servizio di esecuzione del batch (BES) è un servizio che gestisce volumi elevati, asincroni e valutazioni di un batch di record di dati. L'input per il BES contiene un batch di record da varie origini, ad esempio blob, tabelle di Azure, SQL Azure, HDInsight (i risultati di una Query Hive, ad esempio) e origini HTTP. L'output per il BES contiene i risultati della valutazione. I risultati vengono inseriti su un file nell'archiviazione blob di Azure e i dati dall'endpoint di archiviazione vengono restituiti nella risposta.

Un BES potrebbe essere utile quando le risposte non sono necessarie immediatamente, ad esempio per valutazioni regolarmente pianificate per singoli utenti o dispositivi IOT (Internet of things).

## esempi
Per visualizzare il funzionamento di RRS e BES, viene utilizzato un servizio Web di Azure di esempio. Questo servizio potrebbe essere utilizzato in uno scenario IOT (Internet Of Things). In breve, il dispositivo invia solo un valore, `cog_speed` e riceve una sola risposta. 

Esistono quattro tipi di informazioni necessari per chiamare il servizio RRS o BES. Queste informazioni sono disponibili dalle pagine di servizio in [Pagine del servizio Azure Machine Learning](https://studio.azureml.net) dopo la pubblicazione dell'esperimento. Fare clic sul collegamento SERVIZI WEB sulla sinistra dello schermo per visualizzare i servizi pubblicati. Per informazioni su un servizio specifico, esistono i collegamenti alla pagina Guida API RRS e BES.

1.	La **chiave API del servizio**, disponibile sulla pagina principale dei servizi
2.	L'**URI del servizio**, disponibile sulla pagina di Guida dell'API per il servizio scelto
3.	Il **contenuto della richiesta API** previsto, disponibile nella pagina della Guida di API per il servizio scelto
4.	Il **contenuto della risposta API** previsto, disponibile nella pagina della Guida di API per il servizio scelto

Nei due esempi seguenti, il linguaggio c# viene utilizzato per illustrare che il codice necessario e la piattaforma di destinazione è un desktop di Windows 8. 

### Esempio RRS
Nella pagina della Guida di API, a parte l'URI, è necessario immettere e restituire definizioni ed esempi di codice. L'input API viene chiamato, in particolare per questo servizio, e rappresenta il carico utile della chiamata API. 

**Richiesta di esempio**

	{
	  "Inputs": {
	    "input1": {
	      "ColumnNames": [
	        "cog_speed"
	      ],
	      "Values": [
	        [
	          "0"
	        ],
	        [
	          "1"
	        ]
	      ]
	    }
	  },
	  "GlobalParameters": {}
	}


Analogamente, la risposta dell'API viene chiamata nuovamente in particolare per questo servizio.

**Risposta di esempio**

	{
	  "Results": {
	    "output1": {
	      "type": "DataTable",
	      "value": {
	        "ColumnNames": [
	          "cog_speed"
	        ],
	        "ColumnTypes": [
	          "Numeric"
	        ].
	      "Values": [
	        [
	          "0"
	        ],
	        [
	          "1"
	        ]
	      ]
	    }
	  },
	  "GlobalParameters": {}
	}

Nella parte inferiore della pagina si trovano gli esempi di codice. Di seguito si trova il codice di esempio per l'implementazione c# 
                   
**Codice di esempio**
	using System;
	using System.Collections.Generic;
	using System.IO;
	using System.Net.Http;
	using System.Net.Http.Formatting;
	using System.Net.Http.Headers;
	using System.Text;
	using System.Threading.Tasks;
	
	namespace CallRequestResponseService
	{
	    public class StringTable
	    {
	        public string[] ColumnNames { get; set; }
	        public string[,] Values { get; set; }
	    }
	
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            InvokeRequestResponseService().Wait();
	        }
	
	        static async Task InvokeRequestResponseService()
	        {
	            using (var client = new HttpClient())
	            {
	                var scoreRequest = new
	                {
	                    Inputs = new Dictionary<string, StringTable> () { 
	                        { 
	                            "input1", 
	                            new StringTable() 
	                            {
	                                ColumnNames = new string[] {"cog_speed"},
	                                Values = new string[,] {  { "0"},  { "1"}  }
	                            }
	                        },
	                    GlobalParameters = new Dictionary<string, string>() { }
	                };
	                
	                const string apiKey = "abc123"; // Replace this with the API key for the web service
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
	
	                client.BaseAddress = new Uri("https://ussouthcentral.services.azureml.net/workspaces/<workspace id>/services/<service id>/execute?api-version=2.0&details=true");
	                
	                // WARNING: The 'await' statement below can result in a deadlock if you are calling this code from the UI thread of an ASP.Net application.
	                // One way to address this would be to call ConfigureAwait(false) so that the execution does not attempt to resume on the original context.
	                // For instance, replace code such as:
	                //      result = await DoSomeTask()
	                // with the following:
	                //      result = await DoSomeTask().ConfigureAwait(false)

	                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);
	
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    Console.WriteLine("Result: {0}", result);
	                }
	                else
	                {
	                    Console.WriteLine("Failed with status code: {0}", response.StatusCode);
	                }
	            }
	        }
	    }
	}

### Esempio BES
Nella pagina della Guida di API, oltre all'URI, è possibile trovare informazioni sulle diverse chiamate disponibili. A differenza del servizio RRS, il servizio BES è asincrono. Ciò significa che l'API BES sta semplicemente accodando un processo da eseguire. Ma tale operazione non verrà eseguita prima che venga ricevuta la risposta dell'API. Di seguito vengono descritte tre operazioni che uno sviluppatore può effettuare con il servizio BES.

1. Inviare un processo di esecuzione del Batch
1. Ottenere lo stato o il risultato di un processo di esecuzione del Batch
1. Eliminare un processo di esecuzione del Batch  

**1. Inviare un processo di esecuzione del Batch**

Un processo di esecuzione del batch viene inviato fornendo informazioni sul percorso di archiviazione dei dati del batch. In questo esempio si presuppone che il record di cui si desidera valutare il batch si trovi in un file blob di un account di archiviazione.

La risposta a un processo del batch è un ID di processo, perché il processo viene eseguito in modo asincrono. Per ottenere lo stato e i risultati del processo in un secondo momento viene utilizzato l'ID del processo.

**Richiesta di esempio**

	{
	  "Input": {
	    "ConnectionString":     
	    "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=mystorageacctKey",
	    "RelativeLocation": "/mycontainer/mydatablob.csv",
	    "BaseLocation": null,
	    "SasBlobToken": null
	  },
	  "Output": null,
	  "GlobalParameters": {}
	}

**Risposta di esempio**

	"539d0bc2fde945b6ac986b851d0000f0" // The JOB_ID

**Codice di esempio**

	// This code requires the Nuget package Microsoft.AspNet.WebApi.Client to be installed.
	// Instructions for doing this in Visual Studio:
	// Tools -> Nuget Package Manager -> Package Manager Console
	// Install-Package Microsoft.AspNet.WebApi.Client
	
	using System;
	using System.Collections.Generic;
	using System.Net.Http;
	using System.Threading.Tasks;
	using System.Net.Http.Headers;
	
	namespace CallBatchExecutionService
	{
	    internal class Program
	    {
	        private static void Main(string[] args)
	        {
	            InvokeBatchExecutionService().Wait();
	        }
	
	        private static async Task InvokeBatchExecutionService()
	        {
	            // API Information
	            const string BESUrl = "[BES URI]";
	            const string ApiKey = "abc123"; 
	            // The storage account information
	            const string StorageAccountName = @"mystorageacct"; 
	            const string StorageAccountKey = @"Dx9WbMIThAvXRQWap/aLnxT9LV5txxw==";
	            // Storage file with the batch of records
	            const string StorageInputFile = @"/mycontainermydatablob.csv"; 
	
	
	            String connString = String.Format(
	                "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
	                StorageAccountName,
	                StorageAccountKey);
	
	            BatchRequest request = new BatchRequest();
	            request.Input.RelativeLocation = StorageInputFile;
	            request.Input.ConnectionString = connString;
	
	            using (var client = new HttpClient())
	            {
	                client.BaseAddress = new Uri(BESUrl);
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", ApiKey);
	
	                HttpResponseMessage response = await client.PostAsJsonAsync("", request);
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    Console.WriteLine("Job ID: {0}", result);
	                }
	                else
	                {
	                    Console.WriteLine("Failed with status code: {0}", response.StatusCode);
	                }
	            }
	        }
	    }
	
	    public class BatchInput
	    {
	        public String ConnectionString { get; set; }
	        public String RelativeLocation { get; set; }
	        public String BaseLocation { get; set; }
	        public String SasBlobToken { get; set; }
	
	        public BatchInput()
	        {
	            ConnectionString = null;
	            RelativeLocation = null;
	            BaseLocation = null;
	            SasBlobToken = null;
	        }
	    }
	
	    public class BatchRequest
	    {
	        public BatchInput Input { get; set; }
	
	        public Object Output { get; set; }
	
	        public Dictionary<string, string> GlobalParameters { get; set; }
	
	        public BatchRequest()
	        {
	            this.GlobalParameters = new Dictionary<string, string>();
	            Input = new BatchInput();
	            Output = null;
	        }
	    }
	}
	
**2. Ottenere lo stato o il risultato di un processo di esecuzione del Batch**

Per ottenere il risultato di un processo, è necessario innanzitutto il relativo ID in risposta all'invio del processo. Non esiste alcun input reale alla chiamata API. Utilizza una piccola modifica all'URI BES e il metodo di richiesta. Invece di una richiesta POST, viene utilizzata una richiesta GET dopo l'URI definito nella pagina della Guida di API.
 
La risposta, tuttavia, è basata su livelli.

**Carico utile di risposta**

	{
	    "StatusCode": STATUS_CODE,
	    "Result": RESULT,
	    "Details": DETAILS
	}

`StatusCode` può avere un valore di 0, 1, 2, 3 o 4 con la seguente semantica:

* 0	Non avviato
* 1	In esecuzione
* 2	Non riuscito
* 3	Annullato
* 4	Completato

Se il processo non è stato completato, `Result` è **null**. Se il processo è stato completato, `Result` avrebbe una forma: 

	{
	  "ConnectionString": null,
	  "RelativeLocation": "RELATIVE_LOCATION",
	  "BaseLocation": "BASE_LOCATION",
	  "SasBlobToken": "SAS_BLOB_TOKEN"
	}

Dettagli mostra i dettagli dell'errore, se presente.

**Codice di esempio**

	// This code requires the Nuget package Microsoft.AspNet.WebApi.Client to be installed.
	// Instructions for doing this in Visual Studio:
	// Tools -> Nuget Package Manager -> Package Manager Console
	// Install-Package Microsoft.AspNet.WebApi.Client
	//
	// Also, add a reference to Microsoft.WindowsAzure.Storage.dll for reading from and writing to the Azure blob storage
	
	using System;
	using System.IO;
	using System.Net.Http;
	using System.Net.Http.Headers;
	using System.Threading.Tasks;
	using Newtonsoft.Json;
	
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;
	
	namespace CallBatchExecutionService
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            String jobId = "123";
	            InvokeBatchExecutionService(jobId).Wait();
	        }
	
	        static async Task InvokeBatchExecutionService(String JobId)
	        {
	            Console.WriteLine(String.Format("Getting job status for job {0}", JobId));
	
	            // BES Information
	            const string BaseUrl = @"[BES Job Id]/{0}";
	            const string ApiKey = "abc123"; 
	            // Replace this with the location you would like to use for your output file
	            const string OutputFileLocation = @"myresults.csv"; 
	
	            using (var client = new HttpClient())
	            {
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", ApiKey);
	
	                HttpResponseMessage response = await client.GetAsync(String.Format(BaseUrl, JobId));
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    BatchResponseStructure responseStruct = JsonConvert.DeserializeObject<BatchResponseStructure>(result);
	
	                    switch (responseStruct.StatusCode)
	                    {
	                        case (int)BatchScoreStatusCode.NotStarted:
	                            Console.WriteLine("Not started...");
	                            break;
	                        case (int)BatchScoreStatusCode.Running:
	                            Console.WriteLine("Running...");
	                            break;
	                        case (int)BatchScoreStatusCode.Failed:
	                            Console.WriteLine("Failed!");
	                            Console.WriteLine(string.Format(@"Error details: {0}", status.Details));
	                            break;
	                        case (int)BatchScoreStatusCode.Cancelled:
	                            Console.WriteLine("Cancelled!");
	                            break;
	                        case (int)BatchScoreStatusCode.Finished:
	                            Console.WriteLine("Finished!");
	                            var credentials = new StorageCredentials(status.Result.SasBlobToken);
	                            var cloudBlob = new CloudBlockBlob(new Uri(new Uri(responseStruct.Result.BaseLocation), 
	                                                                                               responseStruct.Result.RelativeLocation), credentials);
	                            cloudBlob.DownloadToFile(OutputFileLocation, FileMode.Create);
	                            Console.WriteLine(string.Format(@"The results have been written to the file {0}", OutputFileLocation));
	                            break;
	                    }
	                }
	                else
	                {
	                    Console.WriteLine(String.Format("Batch Result : Failed with status code: {0}", response.StatusCode));
	                }
	            }
	        }
	    }
	
	    public enum BatchScoreStatusCode : int
	    {
	        NotStarted = 0,
	        Running = 1,
	        Failed = 2,
	        Cancelled = 3,
	        Finished = 4
	    }
	
	    public class BatchResult
	    {
	        public String ConnectionString { get; set; }
	        public String RelativeLocation { get; set; }
	        public String BaseLocation { get; set; }
	        public String SasBlobToken { get; set; }
	    }
	
	    public class BatchResponseStructure
	    {
	        public int StatusCode { get; set; }
	        public BatchResult Result { get; set; }
	        public String Details { get; set; }
	        public BatchResponseStructure()
	        {
	            this.Result = new BatchResult();
	        }
	    }
	}

**3. Eliminare un processo di esecuzione del Batch**              
È possibile eliminare un processo anche dopo l'avvio. Ciò può essere effettuato per vari motivi, ad esempio se il processo impiega troppo tempo per il completamento. Per eliminare un processo, è necessario innanzitutto il relativo ID in risposta all'invio del processo.

Non esiste alcun input reale alla chiamata API. Una piccola modifica all'URI BES e il metodo di richiesta. Invece di una richiesta POST, viene utilizzata una richiesta DELETE dopo l'URI definito nella pagina della Guida di API. L'esempio di codice per questa operazione è molto semplice.


<!--HONumber=49--> 