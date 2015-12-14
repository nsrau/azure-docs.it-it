
<properties 
	pageTitle="Personalizzare le definizioni delle API generate da Swashbuckle" 
	description="Informazioni su come personalizzare le definizioni delle API di Swagger generate da Swashbuckle per un'app per le API nel servizio app di Azure." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2015" 
	ms.author="bradyg"/>

# Personalizzare le definizioni delle API generate da Swashbuckle 

## Panoramica

Questo articolo illustra come personalizzare Swashbuckle per gestire scenari comuni in cui è possibile modificare il comportamento predefinito:

* Swashbuckle genera identificatori di operazione duplicati per gli overload dei metodi del controller
* Swashbuckle presuppone che l'unica risposta valida restituita da un metodo sia HTTP 200 (OK) 
 
## Personalizzare la generazione di identificatori di operazione

Swashbuckle genera identificatori di operazione Swagger concatenando il nome del controller e il nome del metodo. Questo modello provoca problemi quando sono presenti più overload di un metodo: Swashbuckle genera ID operazione duplicati, ovvero JSON Swagger non valido.

Ad esempio, il codice seguente del controller provoca la generazione di tre ID operazione Contact\_Get da parte di Swashbuckle.

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsincode.png)

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsinjson.png)

È possibile risolvere il problema manualmente specificando nomi univoci per i metodi, come illustrato nell'esempio seguente:

* Get
* GetById
* GetPage

In alternativa, è possibile estendere Swashbuckle in modo che generi automaticamente ID operazione univoci,

I passaggi seguenti illustrano come personalizzare Swashbuckle usando il file *SwaggerConfig.cs* incluso nel progetto dal modello di progetto di anteprima delle app per le API di Visual Studio. È anche possibile personalizzare Swashbuckle in un progetto API Web configurato per la distribuzione come app per le API.

1. Creare un'implementazione personalizzata di `IOperationFilter` 

	L'interfaccia `IOperationFilter` fornisce un punto di estendibilità per gli utenti di Swashbuckle che vogliono personalizzare diversi aspetti del processo di metadati di Swagger. Il codice seguente illustra un metodo di modifica del comportamento di generazione dell'ID operazione. Il codice aggiunge nomi di parametro alla fine del nome dell'ID operazione.

		using Swashbuckle.Swagger;
		using System.Web.Http.Description;
		
		namespace ContactsList
		{
		    public class MultipleOperationsWithSameVerbFilter : IOperationFilter
		    {
		        public void Apply(
		            Operation operation,
		            SchemaRegistry schemaRegistry,
		            ApiDescription apiDescription)
		        {
		            if (operation.parameters != null)
		            {
		                operation.operationId += "By";
		                foreach (var parm in operation.parameters)
		                {
		                    operation.operationId += string.Format("{0}",parm.name);
		                }
		            }
		        }
		    }
		}

2. Nel file *App\_Start\\SwaggerConfig.cs* chiamare il metodo `OperationFilter` in modo che Swashbuckle usi la nuova implementazione di `IOperationFilter`.

		c.OperationFilter<MultipleOperationsWithSameVerbFilter>();

	![](./media/app-service-api-dotnet-swashbuckle-customize/usefilter.png)

	Il file *SwaggerConfig.cs* fornito dal pacchetto Swashbuckle NuGet contiene molti esempi con commenti relativi ai punti di estendibilità. I commenti aggiuntivi non sono visualizzati in questo articolo.

	Dopo questa modifica, viene usata l'implementazione di `IOperationFilter`, che provoca la generazione di ID operazione univoci.
 
	![](./media/app-service-api-dotnet-swashbuckle-customize/uniqueids.png)

<a id="multiple-response-codes" name="multiple-response-codes"></a>
	
## Consentire codici di risposta diversi da 200

Per impostazione predefinita, Swashbuckle presuppone che l'*unica* risposta valida restituita da un metodo API Web sia la risposta HTTP 200 (OK). In alcuni casi, potrebbe essere necessario che vengano restituiti altri codici di risposta senza che il client generi un'eccezione. Ad esempio, il codice di API Web seguente illustra uno scenario in cui il client deve accettare come risposte valide sia il codice 200 che 404.

	[ResponseType(typeof(Contact))]
    public HttpResponseMessage Get(int id)
    {
        var contacts = GetContacts();

        var requestedContact = contacts.FirstOrDefault(x => x.Id == id);

        if (requestedContact == null)
        {
            return Request.CreateResponse(HttpStatusCode.NotFound);
        }
        else
        {
            return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
        }
    }

In questo scenario, il file Swagger generato da Swashbuckle per impostazione predefinita specifica solo un codice di stato HTTP valido, ovvero HTTP 200.

![](./media/app-service-api-dotnet-swashbuckle-customize/http-200-output-only.png)

Poiché Visual Studio usa la definizione dell'API Swagger per generare il codice per il client, viene creato un codice client che genera un'eccezione per tutte le risposte diverse da HTTP 200. Il codice seguente viene restituito da un client C# generato per questo metodo API Web di esempio.

	if (statusCode != HttpStatusCode.OK)
    {
        HttpOperationException<object> ex = new HttpOperationException<object>();
        ex.Request = httpRequest;
        ex.Response = httpResponse;
        ex.Body = null;
        if (shouldTrace)
        {
            ServiceClientTracing.Error(invocationId, ex);
        }
        throw ex;
    } 

In Swashbuckle sono disponibili due metodi per personalizzare l'elenco dei codici di risposta HTTP previsti generato, ovvero con i commenti XML o con l'attributo `SwaggerResponse`. L'attributo è più semplice, ma è disponibile solo in Swashbuckle 5.1.5 o versione successiva. Il modello nuovo progetto di anteprima delle app per le API in Visual Studio 2013 include Swashbuckle versione 5.0.0, pertanto se si usa il modello e non si vuole aggiornare Swashbuckle, l'unica possibilità consiste nell'usare i commenti XML.

### Personalizzare i codici di risposta previsti con i commenti XML

Usare questo metodo per specificare i codici di risposta se la versione di Swashbuckle è precedente alla versione 5.1.5.

1. In primo luogo, aggiungere i commenti della documentazione XML sopra i metodi per cui specificare i codici di risposta HTTP. Prendendo l'azione API Web di esempio illustrata in precedenza e applicandovi la documentazione XML, si otterrà un codice simile a quello riportato nell'esempio seguente. 

		/// <summary>
		/// Returns the specified contact.
		/// </summary>
		/// <param name="id">The ID of the contact.</param>
		/// <returns>A contact record with an HTTP 200, or null with an HTTP 404.</returns>
		/// <response code="200">OK</response>
		/// <response code="404">Not Found</response>
		[ResponseType(typeof(Contact))]
		public HttpResponseMessage Get(int id)
		{
		    var contacts = GetContacts();
		
		    var requestedContact = contacts.FirstOrDefault(x => x.Id == id);
		
		    if (requestedContact == null)
		    {
		        return Request.CreateResponse(HttpStatusCode.NotFound);
		    }
		    else
		    {
		        return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
		    }
		}

1. Aggiungere le istruzioni nel file *SwaggerConfig.cs* per indicare a Swashbuckle di usare il file di documentazione XML.

	* Aprire *SwaggerConfig.cs* e creare un metodo nella classe *SwaggerConfig* per specificare il percorso del file di documentazione XML. 

			private static string GetXmlCommentsPath()
			{
			    return string.Format(@"{0}\XmlComments.xml", 
			        System.AppDomain.CurrentDomain.BaseDirectory);
			}

	* Scorrere verso il basso il file *SwaggerConfig.cs* finché non viene visualizzata la riga di codice con commenti simile a quella riportata nella schermata seguente.

		![](./media/app-service-api-dotnet-swashbuckle-customize/xml-comments-commented-out.png)
	
	* Rimuovere i simboli di commento dalla riga per consentire l'elaborazione dei commenti XML durante la generazione del file Swagger.
	
		![](./media/app-service-api-dotnet-swashbuckle-customize/xml-comments-uncommented.png)
	
1. Per generare il file di documentazione XML, accedere alle proprietà del progetto e abilitare il file di documentazione XML come illustrato nella schermata seguente.

	![](./media/app-service-api-dotnet-swashbuckle-customize/enable-xml-documentation-file.png)

Dopo avere eseguito questi passaggi, il file JSON Swagger generato da Swashbuckle rifletterà i codici di risposta HTTP specificati nei commenti XML. La schermata seguente illustra il nuovo payload JSON.

![](./media/app-service-api-dotnet-swashbuckle-customize/swagger-multiple-responses.png)

Quando si usa Visual Studio per rigenerare il codice client per l'API REST, il codice C# accetterà i codici di stato HTTP OK e Non trovato senza generare un'eccezione, consentendo al codice usato di decidere come gestire la restituzione di un record di contatto Null.

		if (statusCode != HttpStatusCode.OK && statusCode != HttpStatusCode.NotFound)
		{
		    HttpOperationException<object> ex = new HttpOperationException<object>();
		    ex.Request = httpRequest;
		    ex.Response = httpResponse;
		    ex.Body = null;
		    if (shouldTrace)
		    {
		        ServiceClientTracing.Error(invocationId, ex);
		    }
        	    throw ex;
		}

Il codice per questa dimostrazione è disponibile in [questo archivio GitHub](https://github.com/Azure-Samples/API-Apps-DotNet-Swashbuckle-Customization-MultipleResponseCodes). Oltre al progetto API Web contrassegnato con i commenti della documentazione XML, è presente un progetto di applicazione console che contiene un client generato per questa API.

### Personalizzare i codici di risposta previsti con l'attributo SwaggerResponse

L'attributo [SwaggerResponse](https://github.com/domaindrivendev/Swashbuckle/blob/master/Swashbuckle.Core/Swagger/Annotations/SwaggerResponseAttribute.cs) è disponibile in Swashbuckle 5.1.5 e versioni successive. Nel caso in cui si ha una versione precedente del progetto, questa sezione inizia illustrando come aggiornare il pacchetto NuGet Swashbuckle in modo da poter usare questo attributo.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto API Web e scegliere **Gestisci pacchetti NuGet**. 

	![](./media/app-service-api-dotnet-swashbuckle-customize/manage-nuget-packages.png)

1. Fare clic sul pulsante *Aggiorna* accanto al pacchetto NuGet *Swashbuckle*.

	![](./media/app-service-api-dotnet-swashbuckle-customize/update-nuget-dialog.png)

1. Aggiungere gli attributi *SwaggerResponse* ai metodi dell'azione API Web per cui specificare i codici di risposta HTTP validi.

		[SwaggerResponse(HttpStatusCode.OK)]
		[SwaggerResponse(HttpStatusCode.NotFound)]
		[ResponseType(typeof(Contact))]
		public HttpResponseMessage Get(int id)
		{
		    var contacts = GetContacts();

		    var requestedContact = contacts.FirstOrDefault(x => x.Id == id);
		    if (requestedContact == null)
		    {
		        return Request.CreateResponse(HttpStatusCode.NotFound);
		    }
		    else
		    {
		        return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
		    }
		}

2. Aggiungere un'istruzione `using` per lo spazio dei nomi dell'attributo:

		using Swashbuckle.Swagger.Annotations;
		
1. Accedere all'URL */swagger/docs/v1* del progetto per visualizzare i vari codici di risposta HTTP nel file JSON Swagger.

	![](./media/app-service-api-dotnet-swashbuckle-customize/multiple-responses-post-attributes.png)

Il codice per questa dimostrazione è disponibile in [questo archivio GitHub](https://github.com/Azure-Samples/API-Apps-DotNet-Swashbuckle-Customization-MultipleResponseCodes-With-Attributes). Oltre al progetto API Web dotato dell'attributo*SwaggerResponse*, è presente un progetto di applicazione console che contiene un client generato per questa API.

## Passaggi successivi

Questo articolo ha illustrato come personalizzare il modo in cui Swashbuckle genera gli ID operazione e i codici di risposta validi. Per altre informazioni, vedere [Swashbuckle su GitHub](https://github.com/domaindrivendev/Swashbuckle).
 

<!---HONumber=AcomDC_1203_2015-->