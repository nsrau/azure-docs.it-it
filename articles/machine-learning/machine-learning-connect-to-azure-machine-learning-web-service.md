<properties 
	pageTitle="Connessione a un servizio Web di Machine Learning | Microsoft Azure" 
	description="Effettuare la connessione a un servizio Web di Azure Machine Learning con c# o Python utilizzando una chiave di autorizzazione." 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="derrickv" />


# Connessione a un servizio Web di Azure Machine Learning 
L'esperienza dello sviluppatore di Azure Machine Learning è un'API del servizio Web per eseguire stime dai dati di input in tempo reale o in modalità batch. È possibile usare Azure Machine Learning Studio per creare stime e pubblicare un servizio Web di Azure Machine Learning.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Per informazioni su come creare e pubblicare un servizio Web di Azure Machine Learning tramite Studio:

- [Pubblicare un servizio Web di Machine Learning](machine-learning-publish-a-machine-learning-web-service.md)
- [Introduzione a ML Studio](http://azure.microsoft.com/documentation/videos/getting-started-with-ml-studio/)
- [Anteprima di Azure Machine Learning](https://studio.azureml.net/)
- [Centro di documentazione Machine Learning](http://azure.microsoft.com/documentation/services/machine-learning/)

## Servizio Web Azure Machine Learning ##

Con il servizio Web di Azure Machine Learning (ML), un'applicazione esterna comunica con un modello di valutazione del flusso di lavoro ML in tempo reale. Una chiamata al servizio Web ML restituisce i risultati della stima a un'applicazione esterna. Per effettuare una chiamata servizio Web ML, è possibile passare una chiave API creata quando si pubblica una stima. Il servizio Web ML è basato su REST, una scelta di architettura diffusa per progetti di programmazione Web.

Azure Machine Learning dispone di due tipi di servizi:

- Servizio di richiesta-risposta (RRS). Un servizio a latenza bassa e altamente scalabile che offre un'interfaccia ai modelli senza stati creati e pubblicati da ML Studio.
- Servizio esecuzione batch (BES). Un servizio asincrono che valuta un batch di record di dati.

Per altre informazioni sui servizi Web di Azure Machine Learning, vedere [Pubblicare un servizio Web Machine Learning](machine-learning-publish-a-machine-learning-web-service.md).

## Ottenere una chiave di autorizzazione Azure Machine Learning ##
È possibile ottenere una chiave API del servizio Web da un servizio Web ML. È possibile ottenerla da Microsoft Azure Machine Learning studio o dal portale di gestione di Azure.
### Microsoft Azure Machine Learning Studio ###
1. In Microsoft Azure Machine Learning Studio fare clic su **WEB SERVICES** a sinistra.
2. Fare clic su un servizio Web. La "chiave API" si trova nella scheda **DASHBOARD**.

### Portale di gestione di Azure ###

1. Fare clic su **MACHINE LEARNING** a sinistra.
2. Fare clic su un'area di lavoro.
3. Fare clic su **WEB SERVICES**.
4. Fare clic su un servizio Web.
5. Fare clic su un endpoint. La "CHIAVE API" si trova in basso a destra.

## <a id="connect"></a>Connettersi a un servizio Web di Azure Machine Learning

È possibile effettuare la connessione a un servizio Web di Azure Machine Learning utilizzando qualsiasi linguaggio di programmazione che supporta la risposta e richiesta HTTP. È possibile visualizzare gli esempi in c#, Python e R da una pagina della Guida del servizio Web ML di Azure.

### Per visualizzare una pagina della Guida API del servizio Web di Azure ML ###
Una pagina della Guida API ML Azure viene creata quando si pubblica un servizio Web. Vedere [Procedura dettagliata di Azure Machine Learning - Pubblicare il servizio Web](machine-learning-walkthrough-5-publish-web-service.md).


**Per visualizzare una pagina della Guida per l'API di Azure ML** In Microsoft Azure Machine Learning Studio:

1. Scegliere **WEB SERVICES**.
2. Scegliere un servizio Web.
3. Scegliere la **pagina della Guida per l'API** - **REQUEST/RESPONSE** o **BATCH EXECUTION**.


**Pagina della Guida per l'API di Azure ML** La pagina della Guida per l'API di Azure ML contiene i dettagli su un servizio Web di stima tra cui


<table>
	<tr>
		<td>&#160;</td>
		<td>Esempio </td>
	</tr>
	<tr>
		<td>URI POST della richiesta </td>

		<td>https://ussouthcentral.services.azureml.net/workspaces/{WorkspaceId}/services/{ServiceId}/score
		</td>
	</tr>
	<tr>
		<td>Richiesta di esempio </td>
		<td>{ <br/> 
			&#160;&#160; "Id": "score00001",   <br/>
			&#160;&#160; "Instance": <br/>
			&#160;&#160;&#160;&#160; {  <br/>  
 			&#160;&#160;&#160;&#160; &#160;&#160; "FeatureVector": { <br/>
			&#160;&#160;&#160;&#160; &#160;&#160;  "Col1": "0", <br/>      
			&#160;&#160;&#160;&#160; &#160;&#160;  "Col2": "0", <br/>      
			&#160;&#160;&#160;&#160; &#160;&#160;  "Col3": "0", <br/>  
			&#160;&#160;&#160;&#160; &#160;&#160;  ... },   <br/>
			&#160;&#160;&#160;&#160;   "GlobalParameters": {}   <br/>
			&#160;&#160;&#160;&#160; } <br/>
		}</td>
	</tr>
	<tr>
		<td>Contenuto risposta </td>
		<td>
		<table style="width: 100%">

			<tr>
				<td><B>Nome</B></td>
				<td><B>Tipo di dati</B></td>
			</tr>
	
			<tr>
				<td>Funzionalità</td>
				<td>Stringa</td>
			</tr>
			<tr>
				<td>Conteggio</td>
				<td>Numerico</td>
			</tr>
			<tr>
				<td>Conteggio valori univoci </td>
				<td>Numerico </td>
			</tr>
			<tr>
				<td>... </td>
				<td>... </td>
			</tr>
		</table>
		</td>
	</tr>
	<tr>
		<td>Risposta di esempio </td>
		<td>["Col1","1","1",…] </td>
	</tr>
	<tr>
		<td>Codice di esempio </td>
		<td>(Codice di esempio in C#, Python e R) </td>
	</tr>
</table>

**NOTA** Gli esempi sono tratti da Sample 1: Download dataset from UCI: Adult 2 class dataset che fa parte della raccolta di esempi di Azure ML.

### Esempio C# ###

Per connettersi a un servizio Web di Azure ML, usare **HttpClient** per passare ScoreData. ScoreData contiene FeatureVector, un vettore n-dimensionale di funzioni numeriche che rappresentano ScoreData. Effettuare l'autenticazione al servizio Azure ML con una chiave API.

Per connettersi a un servizio Web di ML, è necessario installare il pacchetto NuGet **Microsoft.AspNet.WebApi.Client**.

**Installare Microsoft.AspNet.WebApi.Client Nuget su Visual Studio**

1. Pubblicare il set di dati di download dal servizio Web UCI: Adult 2 class dataset.
2. Fare clic su **Strumenti** > **Gestione pacchetto NuGet** > **Console di Gestione pacchetti**.
2. Scegliere **Install-Package Microsoft.AspNet.WebApi.Client**.

**Per eseguire l'esempio di codice**

1. Pubblicare l'esperimento "Sample 1: Download dataset from UCI: Adult 2 class dataset", parte della raccolta di esempi di Azure ML.
2. Assegnare la chiave API con la chiave da un servizio Web. Scoprire come ottenere una chiave di autorizzazione ML di Azure.
3. Assegnare l'URI del servizio con l'URI della richiesta. Scoprire come ottenere un URI della richiesta.

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
	    public class ScoreData
	    {
	        public Dictionary<string, string> FeatureVector { get; set; }
	        public Dictionary<string, string> GlobalParameters { get; set; }
	    }
	
	    public class ScoreRequest
	    {
	        public string Id { get; set; }
	        public ScoreData Instance { get; set; }
	    }
	
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            InvokeRequestResponseService().Wait();
	
	            Console.ReadLine();
	        }
	
	        static async Task InvokeRequestResponseService()
	        {
	            //Assign apiKey with the key from a web service.
	            const string apiKey = "{ApiKey}";
	
	            //Assign serviceUri with the Request URI. See How to get a Request URI.
	            const string serviceUri = "{ServiceUri}";
	            
	            using (var client = new HttpClient())
	            {
	                ScoreData scoreData = new ScoreData()
	                {
	                    //Input data
	                    FeatureVector = new Dictionary<string, string>() 
	                    {
	                        { "Col1", "0" },
	                        { "Col2", "0" },
	                        { "Col3", "0" },
	                        { "Col4", "0" },
	                        { "Col5", "0" },
	                        { "Col6", "0" },
	                        { "Col7", "0" },
	                        { "Col8", "0" },
	                        { "Col9", "0" },
	                        { "Col10", "0" },
	                        { "Col11", "0" },
	                        { "Col12", "0" },
	                        { "Col13", "0" },
	                        { "Col14", "0" },
	                        { "Col15", "0" },
	                    },
	                    GlobalParameters = 
	                        new Dictionary<string, string>() {}
	                };
	
	                ScoreRequest scoreRequest = new ScoreRequest()
	                {
	                    Id = "score00001",
	                    Instance = scoreData
	                };
	
	                //Set authorization header
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
	             
	                client.BaseAddress = new Uri(serviceUrl);
	
	                //Post HTTP response message
	                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);
	
	                if (response.IsSuccessStatusCode)
	                {
	                    //Read result string
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


### Esempio Python ###

Per connettersi a un servizio Web di Azure ML, usare la libreria **urllib2** per passare ScoreData. ScoreData contiene FeatureVector, un vettore n-dimensionale di funzioni numeriche che rappresentano ScoreData. Effettuare l'autenticazione al servizio Azure ML con una chiave API.


**Per eseguire l'esempio di codice**

1. Pubblicare l'esperimento "Sample 1: Download dataset from UCI: Adult 2 class dataset", parte della raccolta di esempi di Azure ML.
2. Assegnare la chiave API con la chiave da un servizio Web. Scoprire come ottenere una chiave di autorizzazione ML di Azure.
3. Assegnare l'URI del servizio con l'URI della richiesta. Scoprire come ottenere un URI della richiesta.

		import urllib2
		# If you are using Python 3+, import urllib instead of urllib2
	
		import json 
	
		data =  {
	            "Id": "score00001",
	            "Instance": {
	                "FeatureVector": {
	                    "Col1": "0",
	                    "Col2": "0",
	                    "Col3": "0",
	                    "Col4": "0",
	                    "Col5": "0",
	                    "Col6": "0",
	                    "Col7": "0",
	                    "Col8": "0",
	                    "Col9": "0",
	                    "Col10": "0",
	                    "Col11": "0",
	                    "Col12": "0",
	                    "Col13": "0",
	                    "Col14": "0",
	                    "Col15": "0",
	                },
	                "GlobalParameters": { }
	            }
	        }
	
		body = str.encode(json.dumps(data))
	
		#Assign serviceUrl with the Request URI. See How to get a Request URI.
		uri = '{ServiceUri}'
	
		#Assign apiKey with the key from a web service.
		api_key = '{ApiKey}'
		headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
	
		req = urllib2.Request(uri, body, headers) 
		response = urllib2.urlopen(req)
	
		#If you are using Python 3+, replace urllib2 with urllib.request in the above code:
		#req = urllib.request.Request(uri, body, headers) 
		#response = urllib.request.urlopen(req)
	
		result = response.read()
		print(result) 
 

<!---HONumber=July15_HO2-->