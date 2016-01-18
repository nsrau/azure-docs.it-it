
<properties
	pageTitle="Migliorare l'app per le API per le app per la logica | Microsoft Azure"
	description="Come decorare l'app per le API perché funzioni correttamente con le app per la logica."
	services="app-service\logic"
	documentationCenter=".net"
	authors="sameerch"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-logic"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/15/2015"
	ms.author="sameerch"/>

# Migliorare l'app per le API per le app per la logica #

In questo articolo si apprenderà come definire l'app per le API per garantirne il funzionamento corretto con le app per la logica. Verrà così migliorata l'esperienza dell'utente finale per l'app per le API, quando questa viene usata nella finestra di progettazione delle app per la logica.

## Prerequisiti

Se non si ha familiarità con le [app per le API](app-service-api-apps-why-best-platform.md) nel [servizio app di Azure](../app-service/app-service-value-prop-what-is.md), è consigliabile leggere la serie in più parti sulla [creazione di app per le API](app-service-dotnet-create-api-app.md)


## Aggiungere nomi visualizzati ##
La finestra di progettazione delle app per la logica visualizza i nomi di operazioni, campi e parametri che in alcuni casi possono risultare difficili da leggere, essendo generati a livello di codice. Per facilitare la lettura, la finestra di progettazione delle app per la logica può visualizzare, se è disponibile, un valore di testo più leggibile, noto come *nome visualizzato*, al posto dei nomi predefiniti di operazioni, campi e parametri. A questo scopo, la finestra di progettazione delle app per la logica verifica se sono presenti determinate proprietà nei metadati Swagger forniti dall'app per le API. Le proprietà seguenti vengono usate come nomi visualizzati:

* Operazioni (azioni e trigger): il valore della proprietà **summary** se presente; in caso contrario, il valore della proprietà **operationId**. Notare che la specifica Swagger 2.0 consente un massimo di 120 caratteri per la proprietà **summary**.

* Parametri (input): il valore della proprietà di estensione **x-ms-summary** se presente; in caso contrario, il valore della proprietà **name**. La proprietà di estensione **x-ms-summary** deve essere impostata dinamicamente nel codice. Questo processo è descritto nella sezione "Uso di attributi personalizzati per annotare le proprietà di estensione" in questo argomento. La proprietà **name** può essere impostata usando i commenti ///. Questo processo è descritto nella sezione "Uso di commenti XML nella generazione di definizione dell'API" in questo argomento.

* Campi di schema (risposte di output): il valore della proprietà di estensione **x-ms-summary** se presente; in caso contrario, il valore della proprietà **name**. La proprietà di estensione **x-ms-summary** deve essere impostata dinamicamente nel codice. Questo processo è descritto nella sezione "Uso di attributi personalizzati per annotare le proprietà di estensione" in questo argomento. La proprietà **name** può essere impostata usando i commenti ///. Questo processo è descritto nella sezione "Uso di commenti XML nella generazione di definizione dell'API" in questo argomento.

**Nota:** è consigliabile mantenere la lunghezza dei nomi visualizzati entro un massimo di 30 caratteri.

### Uso di commenti XML nella generazione di definizione dell'API

Per lo sviluppo in Visual Studio è pratica comune annotare i controller API usando [commenti XML](https://msdn.microsoft.com/library/b2s063f7.aspx). Quando si compila con [/doc](https://msdn.microsoft.com/library/3260k4x7.aspx), il compilatore crea un file di documentazione XML. Il set di strumenti Swashbuckle incluso nell'API App SDK è in grado di incorporare tali commenti durante la generazione dei metadati dell'API ed è possibile configurare il progetto API in modo che esegua tale operazione attenendosi alla procedura seguente:

1. Aprire il progetto in Visual Studio.

2. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Proprietà**.

	![Proprietà del progetto](./media/app-service-api-optimize-for-logic-apps/project-properties.png)

3. Quando viene visualizzata la pagina delle proprietà del progetto, eseguire i passaggi seguenti:

	- Selezionare la **Configurazione** per cui si applicano le impostazioni. In genere si seleziona Tutte le configurazioni in modo che le impostazioni specificate siano applicabili alle build di debug e di rilascio.
	
	- Selezionare la scheda **Build** a sinistra.
	
	- Verificare che l'opzione **File di documentazione XML** sia selezionata. Visual Studio fornirà un nome di file predefinito basato sul nome del progetto. È possibile impostare questo valore sulla convenzione di denominazione preferita o lasciarlo invariato.

	![Impostare la proprietà di documentazione XML](./media/app-service-api-optimize-for-logic-apps/xml-documentation-file-property.png)

4. Aprire il file *SwaggerConfig.cs* file (disponibile nella cartella **App\_Start** del progetto).

5. Aggiungere le direttive **using** all'inizio del file *SwaggerConfig.cs* per gli spazi dei nomi **System** e **System.Globalization**.

		using System;
		using System.Globalization;
 
6. Cercare nel file *SwaggerConfig.cs* una chiamata a **GetXmlCommentsPath** e rimuovere il commento dalla riga per consentirne l'esecuzione. Poiché questo metodo non è ancora stato implementato, Visual Studio sottolinea la chiamata a **GetXmlCommentsPath** e indica che non è definita nel contesto corrente. Questo comportamento è corretto. Il metodo verrà implementato nel passaggio successivo.

7. Aggiungere l'implementazione seguente del metodo **GetXmlCommentsPath** alla classe **SwaggerConfig** (definita nel file *SwaggerConfig.cs*). Questo metodo restituirà semplicemente il file di documentazione XML specificato in precedenza nelle impostazioni del progetto.

        public static string GetXmlCommentsPath()
        {
            return String.Format(CultureInfo.InvariantCulture, 
								 @"{0}\bin\ContactsList.xml", 
								 AppDomain.CurrentDomain.BaseDirectory);
        }

8. Infine, specificare i commenti XML per i metodi del controller. A questo scopo, aprire uno dei file del controller dell'app per le API e digitare /// in una riga vuota prima di un metodo del controller da documentare. Visual Studio inserirà automaticamente una sezione commentata nella quale è possibile specificare un riepilogo dei metodi, oltre a informazioni sui parametri e sui valori restituiti.

A questo punto, quando si compila e si pubblica l'app per le API, si vedrà che anche il file di documentazione è incluso nel payload e caricato con il resto dell'app per le API.

## Classificare operazioni e proprietà avanzate

L'area dello schermo della finestra di progettazione delle app per la logica, per visualizzare operazioni, parametri e proprietà, è limitata. Inoltre, un'app per le API può definire un ampio set di operazioni e proprietà. Il risultato di una così elevata quantità di informazioni in un'area limitata può rendere difficile l'uso della finestra di progettazione per l'utente finale.

Per evitare confusione, la finestra di progettazione delle app per la logica consente di raggruppare le operazioni e le proprietà dell'app per le API in categorie definite dall'utente. Usando una modalità di categorizzazione delle operazioni e delle proprietà appropriata, un'app per le API può migliorare l'esperienza utente presentando per prime le operazioni e le proprietà di base e più utili.

Per fornire questa funzionalità, la finestra di progettazione delle app per la logica verifica se è presente una specifica proprietà di estensione del fornitore personalizzata nella definizione dell'API Swagger dell'app per le API. Questa proprietà è denominata **x-ms-visibility** e può accettare i valori seguenti:

* vuoto o "none" Queste operazioni e proprietà possono essere visualizzate facilmente dall'utente.

* "advanced" Poiché queste operazioni e proprietà sono avanzate, sono nascoste per impostazione predefinita. L'utente può facilmente accedervi, se necessario.

* "internal" Queste operazioni e proprietà sono considerate come proprietà di sistema o interne e non sono destinate all'uso diretto da parte dell'utente. Di conseguenza, sono nascoste nella finestra di progettazione e disponibili solo nella visualizzazione Codice. Per queste proprietà è anche possibile specificare la proprietà di estensione **x-ms-scheduler-recommendation** per impostare il valore tramite la finestra di progettazione delle app per la logica. Per un esempio, fare riferimento all'articolo relativo all'[aggiunta di trigger a un'app per le API](app-service-api-dotnet-triggers.md).


## Uso di attributi comuni per annotare proprietà di estensione

Come detto in precedenza, le proprietà di estensione del fornitore personalizzate vengono usate per annotare i metadati dell'API, in modo da fornire informazioni più dettagliate utilizzabili dalla finestra di progettazione delle app per la logica. Se si usano metadati statici per descrivere l'app per le API, è possibile modificare direttamente il file */metadata/apiDefinition.swagger.json* nel progetto per aggiungere manualmente le proprietà di estensione necessarie.

Per le app per le API che usano metadati dinamici, è possibile usare attributi personalizzati per annotare il codice. Si può quindi definire un filtro delle operazioni nel file *SwaggerConfig.cs* per cercare attributi personalizzati e per aggiungere l'estensione del fornitore necessaria. Questo approccio è decritto dettagliatamente di seguito per la generazione dinamica della proprietà di estensione **x-ms-summary**.

1. Definire una classe attribute denominata **CustomSummaryAttribute** che sarà usata per annotare il codice.

	    [AttributeUsage(AttributeTargets.All)]
	    public class CustomSummaryAttribute : Attribute
	    {
	        public string SummaryText { get; internal set; }

	        public CustomSummaryAttribute(string summaryText)
	        {
	            this.SummaryText = summaryText;
	        }
	    }

2. Definire un filtro di operazioni denominato **AddCustomSummaryFilter** che cercherà questo attributo personalizzato nei parametri dell'operazione.


	    using Swashbuckle.Swagger;

		...

		public class AddCustomSummaryFilter : IOperationFilter
	    {
	        public void Apply(Operation operation, SchemaRegistry schemaRegistry, System.Web.Http.Description.ApiDescription apiDescription)
	        {
	            if (operation.parameters == null)
	            {
	                // no parameter
	                return;
	            }

	            foreach (var param in operation.parameters)
	            {
	                var summaryAttributes = apiDescription.ParameterDescriptions.First(x => x.Name.Equals(param.name))
	                                        .ParameterDescriptor.GetCustomAttributes<CustomSummaryAttribute>();

	                if (summaryAttributes != null && summaryAttributes.Count > 0)
	                {
	                    // add x-ms-summary extension
	                    if (param.vendorExtensions == null)
	                    {
	                        param.vendorExtensions = new Dictionary<string, object>();
	                    }
	                    param.vendorExtensions.Add("x-ms-summary", summaryAttributes[0].SummaryText);
	                }
	            }
	        }
	    }

3. Modificare il file *SwaggerConfig.cs* e aggiungere la classe di filtro definita in precedenza.


            GlobalConfiguration.Configuration
                .EnableSwagger(c =>
                    {
                        ...
                        c.OperationFilter<AddCustomSummaryFilter>();
                        ...
                    }

4. Usare la classe **CustomSummaryAttribute** per annotare il codice, come mostrato nel frammento di codice seguente.

        /// <summary>
        /// Send Message
        /// </summary>
        /// <param name="queueName">The name of the Storage Queue</param>
        /// <param name="messageText">The message text to be sent</param>
        public void SendMessage(
            [CustomSummary("Queue Name")] string queueName,
            [CustomSummary("Message Text")] string messageText)
        {
             ...
        }

	Quando si crea l'app per le API sovrastante, questa genera i seguenti metadati dell'API:


			...
            "post": {
                ...
                "parameters": [
                    {
                        "name": "queueName",
                        "in": "query",
                        "description": "The name of the Storage Queue",
                        "required": true,
                        "x-ms-summary": "Queue Name",
                        "type": "string"
                    },
                    {
                        "name": "messageText",
                        "in": "query",
                        "description": "The message text to be sent",
                        "required": true,
                        "x-ms-summary": "Message Text",
                        "type": "string"
                    }
                ],
                ...

5. Analogamente, è possibile definire un filtro di schema **AddCustomSummarySchemaFilter** per annotare automaticamente la proprietà di estensione **x-ms-summary** per i modelli di schema, come nell'esempio seguente.

	    public class AddCustomSummarySchemaFilter: ISchemaFilter
	    {
	        public void Apply(Schema schema, SchemaRegistry schemaRegistry, Type type)
	        {
	            SetCustomSummary(schema, type.GetCustomAttribute<CustomSummaryAttribute>());

	            if (schema.properties != null)
	            {
	                foreach (var property in schema.properties)
	                {
	                    var summaryAttribute = type.GetProperty(property.Key).GetCustomAttribute<CustomSummaryAttribute>();
	                    SetCustomSummary(property.Value, summaryAttribute);
	                }
	            }
	        }

	        private static void SetCustomSummary(Schema schema, CustomSummaryAttribute summaryAttribute)
	        {
	            if (summaryAttribute != null)
	            {
	                if (schema.vendorExtensions == null)
	                {
	                    schema.vendorExtensions = new Dictionary<string, object>();
	                }
	                schema.vendorExtensions.Add("x-ms-summary", summaryAttribute.SummaryText);
	            }
	        }
	    }

## Riepilogo

In questo articolo si è appreso come migliorare l'esperienza utente dell'app per le API quando questa viene usata nella finestra di progettazione delle app per la logica. In genere è consigliabile fornire nomi descrittivi appropriati per tutte le operazioni (azioni e trigger), i parametri e le proprietà. È anche consigliabile fornire non più di 5 operazioni di base. Per i parametri di input è consigliabile limitare il numero di proprietà di base a un massimo di 4, mentre per le proprietà il numero massimo consigliato è 5. Le rimanenti operazioni e proprietà dovrebbero essere contrassegnate come avanzate.
 

<!---HONumber=AcomDC_0107_2016-->