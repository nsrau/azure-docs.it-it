## Metadati dell'app per le API

Questa sezione fornisce informazioni aggiuntive sui metadati delle app per le API che possono essere personalizzati.

La maggior parte delle proprietà nel file *apiapp.json* e i file nella cartella *Metadata* influiscono sul modo in cui un pacchetto dell'app per le API viene presentato in Azure Marketplace. Le sezioni seguenti spiegano il modo in cui le proprietà e i file influiscono sulle app per le API quando si distribuisce direttamente il codice in un'app per le API nella sottoscrizione di Azure.

### ID app per le API 

La proprietà `id` determina il nome dell'app per le API. Ad esempio:

		"id": "ContactsList",

![](./media/app-service-api-direct-deploy-metadata/apiappname.png)

### Spazio dei nomi

Impostare la proprietà `namespace` sul dominio del tenant Azure Active Directory. Per trovare il dominio, aprire il [portale classico di Azure](https://manage.windowsazure.com/) nel browser, passare ad **Active Directory** e selezionare la scheda **Domini**. Ad esempio:

		"namespace": "contoso.onmicrosoft.com",

### Definizione dell'API Swagger dinamica

Per specificare un endpoint dell'URL per una definizione dell'API [Swagger](http://swagger.io/) dinamica, archiviare nella proprietà `endpoints.apiDefinition` l'URL relativo di un'API esposta dall'app per le API che restituisce una definizione dell'API Swagger 2.0 su una richiesta GET. ad esempio:

		"endpoints": {
		    "apiDefinition": "/swagger/docs/v1"
		}

### Definizione dell'API Swagger statica

Per specificare un file di definizione dell'API [Swagger](http://swagger.io/) 2.0 statica, archiviare il file nella cartella *Metadata* e assegnargli il nome *apiDefinition.swagger.json*

![](./media/app-service-api-direct-deploy-metadata/apidefinmetadata.png)

Escludere `endpoints.apiDefinition` dal file *apiapp.json* o impostarne il valore su Null. Se si include sia un URL `endpoints.apiDefinition` che un file *apiDefinition.swagger.json*, l'URL avrà la precedenza e il file verrà ignorato.
 
### Altri metadati dell'app per le API

Per altre informazioni sul file *apiapp.json* e sulla cartella *Metadata*, vedere [Creare un pacchetto dell'app per le API](app-service-api-create-package.md).


<!--HONumber=54-->