---
title: 'Esercitazione: Inviare messaggi di posta elettronica con le App per la logica'
description: Informazioni su come richiamare i processi aziendali dall'app del Servizio app. Inviare messaggi di posta elettronica, tweet e post di Facebook, aggiungere alle liste di distribuzione e molto altro ancora.
ms.topic: tutorial
ms.date: 04/08/2020
ms.custom: mvc
ms.openlocfilehash: 44f2dfb83e96e1d8fa31ee1acf350193b954dbd8
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892570"
---
# <a name="tutorial-send-email-and-invoke-other-business-processes-from-app-service"></a>Esercitazione: Inviare messaggi di posta elettronica e richiamare altri processi aziendali dal Servizio app

Questa esercitazione illustra come integrare un'app del Servizio app con i processi aziendali. Si tratta di scenari comuni per le app Web, ad esempio:

- Inviare un messaggio di posta elettronica di conferma per una transazione
- Aggiungere un utente al gruppo Facebook
- Connettersi a sistemi di terze parti come SAP, SalesForce e così via.
- Messaggi B2B standard di Exchange

Questa esercitazione illustra come inviare messaggi di posta elettronica con Gmail dall'app del Servizio app usando le [App per la logica di Azure](../logic-apps/logic-apps-overview.md). Esistono altri modi per inviare messaggi di posta elettronica da un'app Web, ad esempio la configurazione SMTP fornita dal framework del linguaggio. Tuttavia, le app per la logica offrono all'app del Servizio app maggiore potenza senza aggiungere complessità al codice. App per la logica offre una semplice interfaccia di configurazione per le integrazioni aziendali più diffuse e l'app può chiamarle in qualsiasi momento con una richiesta HTTP.

## <a name="prerequisite"></a>Prerequisito

Distribuire un'app con il framework del linguaggio desiderato nel Servizio app. Per seguire un'esercitazione per la distribuzione di un'app di esempio, vedere di seguito:

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

[Esercitazione: Creare un'app ASP.NET in Azure con un database SQL](app-service-web-tutorial-dotnet-sqldatabase.md)

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

[Esercitazione: Compilare un'app ASP.NET Core e database SQL in Servizio app di Azure](app-service-web-tutorial-dotnetcore-sqldb.md)

# <a name="nodejs"></a>[Node.js](#tab/node)

[Esercitazione: Creare un'app Node.js e MongoDB in Azure](app-service-web-tutorial-nodejs-mongodb-app.md)

# <a name="php"></a>[PHP](#tab/php)

[Esercitazione: Creare un'app PHP e MySQL in Azure](app-service-web-tutorial-php-mysql.md)

# <a name="python"></a>[Python](#tab/python)

[Esercitazione: Eseguire un'app Web Python (Django) con PostgreSQL nel Servizio app di Azure](containers/tutorial-python-postgresql-app.md)

# <a name="ruby"></a>[Ruby](#tab/ruby)

[Creare un'app Ruby e Postgres nel Servizio app di Azure in Linux](containers/tutorial-ruby-postgres-app.md)

---

## <a name="create-the-logic-app"></a>Creare l'app per la logica

1. Nel [portale di Azure](https://portal.azure.com) creare un'app per la logica vuota seguendo le istruzioni riportate in [Creare l'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app). Quando viene visualizzata la finestra **Progettazione app per la logica**, tornare a questa esercitazione.
1. Nella pagina iniziale di progettazione app per la logica selezionare **Alla ricezione di una richiesta HTTP** in **Inizia con un trigger comune**.

    ![](./media/tutorial-send-email/receive-http-request.png)
1. Nella finestra di dialogo **Alla ricezione di una richiesta HTTP**, selezionare **Usare il payload di esempio per generare lo schema**.

    ![](./media/tutorial-send-email/generate-schema-with-payload.png)

1. Copiare il codice JSON di esempio seguente nella casella di testo e selezionare **Fine**.

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

    Verrà venerato lo schema per i dati della richiesta desiderati. In pratica, è possibile acquisire solo i dati della richiesta effettivi generati dal codice dell'applicazione e consentire ad Azure di generare automaticamente lo schema JSON. 
1. Nella parte superiore di Progettazione app per la logica selezionare **Salva**. 

    Verrò visualizzato l'URL del trigger della richiesta HTTP. Selezionare l'icona di copia per copiarlo in modo da poterlo usare in seguito.

    ![](./media/tutorial-send-email/http-request-url.png)

    Questa definizione della richiesta HTTP è un trigger per qualsiasi operazione da eseguire in questa app per la logica, che sia Gmail o altro. Più avanti l'URL verrà richiamato nell'app del Servizio app. Per altre informazioni sul trigger della richiesta, vedere le [informazioni di riferimento sulla richiesta/risposta HTTP](../connectors/connectors-native-reqres.md).

1. Nella parte inferiore della finestra di progettazione fare clic su **Nuovo passaggio**, digitare **Gmail** nella casella di ricerca azioni e trovare e selezionare **Invia messaggio di posta elettronica (v2)** .
    
    > [!TIP]
    > È possibile cercare altri tipi di integrazioni, ad esempio SendGrid, MailChimp, Office 365 e SalesForce. Per altre informazioni, vedere la [Documentazione di App per la logica](https://docs.microsoft.com/azure/logic-apps/).
1. Nella finestra di dialogo **Gmail** selezionare **Accedi** e accedere all'account Gmail da cui si vuole inviare il messaggio di posta elettronica.

    ![](./media/tutorial-send-email/gmail-sign-in.png)

1. Una volta effettuato l'accesso, fare clic nella casella di testo **A** e la finestra di dialogo con il contenuto dinamico verrà aperta automaticamente.

1. Accanto all'azione **Alla ricezione di una richiesta HTTP**, selezionare **Vedi altre**.

    ![](./media/tutorial-send-email/expand-dynamic-content.png)

    Verranno quindi visualizzate le tre proprietà dei dati JSON di esempio usati in precedenza. In questo passaggio le proprietà della richiesta HTTP verranno usate per creare un messaggio di posta elettronica.
1. Poiché si sta selezionando il valore per il campo **A**, scegliere **posta elettronica**. Se si desidera, disattivare la finestra di dialogo con contenuto dinamico facendo clic su **Aggiungi contenuto dinamico**.

    ![](./media/tutorial-send-email/hide-dynamic-content.png)

1. Nell'elenco a discesa **Aggiungi nuovo parametro** selezionare **Oggetto** e **Corpo**.

1. Fare clic nella casella di testo **Oggetto** e, allo stesso modo, scegliere **attività**. Con il cursore ancora nella casella **Oggetto**, digitare *Creata*. 

1. Fare clic nella casella **Corpo** e, nello stesso modo, scegliere **Scadenza**. Spostare il cursore a sinistra di **Scadenza** e digitare *Scadenza dell'elemento*.

    > [!TIP]
    > Se si vuole modificare il contenuto HTML direttamente nel corpo del messaggio di posta elettronica, selezionare **Visualizzazione Codice** nella parte superiore della finestra Progettazione app per la logica. Assicurarsi di mantenere il codice del contenuto dinamico (ad esempio, `@{triggerBody()?['due']}`)
    >
    > ![](./media/tutorial-send-email/edit-rich-html-email.png) 

1. Aggiungere quindi una risposta HTTP asincrona al trigger HTTP. Tra il trigger HTTP e l'azione Gmail, fare clic sul segno **+** e selezionare **Aggiungi un ramo parallelo**.

    ![](./media/tutorial-send-email/add-http-response.png)

1. Nella casella di ricerca cercare **risposta**, quindi selezionare l'azione **Risposta**.

    ![](./media/tutorial-send-email/choose-response-action.png)

    Per impostazione predefinita, l'azione di risposta restituisce un codice HTTP 200. Per questa esercitazione, la risposta restituita è sufficiente. Per altre informazioni, vedere le [informazioni di riferimento sulla richiesta/risposta HTTP](../connectors/connectors-native-reqres.md).

1. Nella parte superiore di Progettazione app per la logica selezionare di nuovo **Salva**. 

## <a name="add-http-request-code-to-app"></a>Aggiungere il codice della richiesta HTTP all'app

Assicurarsi di aver copiato l'URL del trigger della richiesta HTTP nella sezione precedente. Poiché contiene informazioni sensibili, è consigliabile non inserirlo direttamente nel codice. Con il Servizio app, è possibile farvi riferimento come variabile di ambiente, usando le impostazioni dell'app. 

In [Cloud Shell](https://shell.azure.com) creare l'impostazione dell'app con il comando seguente (sostituire *\<app-name>* , *\<resource-group-name>* e *\<logic-app-url>* ):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings LOGIC_APP_URL="<your-logic-app-url>"
```

Nel codice creare un una richiesta HTTP POST standard nell'URL usando qualsiasi linguaggio client HTTP disponibile per il framework del linguaggio, con la configurazione seguente:

- Il corpo della richiesta contiene lo stesso formato JSON fornito all'app per la logica:

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

- La richiesta contiene l'intestazione `Content-Type: application/json`. 
- Per ottimizzare le prestazioni, inviare la richiesta in modo asincrono, se possibile.

Fare clic sulla scheda linguaggio/framework preferito sotto per visualizzare un esempio.

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

In ASP.NET è possibile inviare la richiesta HTTP POST con la classe [System.Net.Http.HttpClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient). Ad esempio:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // requires using System.Configuration;
    ConfigurationManager.AppSettings["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

Se si sta testando il codice nell'app di esempio per [Esercitazione: Creare un'app ASP.NET in Azure con un database SQL](app-service-web-tutorial-dotnet-sqldatabase.md), è possibile provare a inviare una conferma tramite posta elettronica in [Azione Crea](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/blob/master/DotNetAppSqlDb/Controllers/TodosController.cs#L52-L63), dopo aver aggiunto l'elemento `Todo`. Per usare il codice asincrono precedente, convertire l'azione Crea in un'azione asincrona.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

In ASP.NET Core è possibile inviare la richiesta HTTP POST con la classe [System.Net.Http.HttpClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient). Ad esempio:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // Requires DI configuration to access app settings. See https://docs.microsoft.com/azure/app-service/containers/configure-language-dotnetcore#access-environment-variables
    _configuration["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

> [!NOTE]
> Questo codice è stato scritto per una facile dimostrazione. In pratica, non creare un'istanza di un oggetto `HttpClient` per ogni richiesta. Seguire le indicazioni fornite in [Usare IHttpClientFactory per implementare richieste HTTP resilienti] (https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient -http-requests).

Se si sta testando il codice nell'app di esempio per [Esercitazione: Creare un'app ASP.NET Core con un database SQL nel Servizio app di Azure](app-service-web-tutorial-dotnetcore-sqldb.md), è possibile provare a inviare una conferma tramite posta elettronica in [Azione Crea](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial/blob/master/Controllers/TodosController.cs#L56-L65), dopo aver aggiunto l'elemento `Todo`.

# <a name="nodejs"></a>[Node.js](#tab/node)

In Node.js è possibile inviare facilmente la richiesta HTTP POST con un pacchetto npm, come [axios](https://www.npmjs.com/package/axios). Ad esempio:

```javascript
// Requires npm install --save axios
const axios = require('axios');

var jsonData = {
        email: "a-valid@emailaddress.com",
        due: "4/1/2020",
        task: "My new task!"
};

(async function(data) {
    try {
        const response = await axios.post(process.env.LOGIC_APP_URL, jsonData);
        console.log(response.status);
    } catch (error) {
        console.log(error);
    }
})(jsonData);

```

Se si sta testando il codice nell'app di esempio per [Esercitazione: Creare un'app Node.js e MongoDB in Azure](app-service-web-tutorial-nodejs-mongodb-app.md), è possibile provare a inviare una conferma tramite posta elettronica nella [funzione Crea](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L14-L27), dopo [aver salvato l'articolo](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L24).

# <a name="php"></a>[PHP](#tab/php)

In PHP è possibile inviare facilmente la richiesta HTTP POST con [Guzzle](http://docs.guzzlephp.org/en/stable/index.html). Ad esempio:

```php
// Requires composer require guzzlehttp/guzzle:~6.0
use GuzzleHttp\Client;
...
$client = new Client();
$options = [
    'json' => [ 
        'email' => 'a-valid@emailaddress.com',
        'due' => '4/1/2020',
        'task' => "My new task!"
    ]
];

$promise = $client-> postAsync(getenv($LOGIC_APP_URL), $options)->then( 
    function ($response) {
        return $response->getStatusCode();
    }, function ($exception) {
        return $exception->getResponse();
    }
);

$response = $promise->wait();
// Requires Laravel to run Log::info(). Check the documentation of your preferred framework for logging instructions.
Log::info(print_r($response, TRUE));
```

Se si sta testando il codice nell'app di esempio per [Esercitazione: Creare un'app PHP e MySQL in Azure](app-service-web-tutorial-php-mysql.md), è possibile provare a inviare una conferma tramite posta elettronica nella [funzione route::post](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48) prima dell'istruzione return.

# <a name="python"></a>[Python](#tab/python)

In Python è possibile inviare facilmente la richiesta HTTP POST con [requests](https://pypi.org/project/requests/). Ad esempio:

```python
# Requires pip install requests && pip freeze > requirements.txt
import requests
...
payload = {
    "email": "a-valid@emailaddress.com",
    "due": "4/1/2020",
    "task": "My new task!"
}
response = requests.post("https://prod-112.westeurope.logic.azure.com:443/workfl$
print(response.status_code)
```
<!-- ```python
# Requires pip install aiohttp && pip freeze > requirements.txt
import aiohttp
...
payload = {
        'email': 'a-valid@emailaddress.com',
        'due': '4/1/2020',
        'task': 'My new task!'
}
async with aiohttp.post('http://httpbin.org/post', data=json.dump(payload)) as resp:
    print(await resp.status())
``` -->

Se si sta testando il codice nell'app di esempio per [Esercitazione: Eseguire un'app Web Python (Django) con PostgreSQL nel Servizio app di Azure](containers/tutorial-python-postgresql-app.md), è possibile provare a inviare una conferma tramite posta elettronica nella [funzione route::post](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48) prima dell'istruzione return.

# <a name="ruby"></a>[Ruby](#tab/ruby)

In Ruby è possibile inviare facilmente la richiesta HTTP POST con [JSONClient](https://www.rubydoc.info/gems/httpclient/JSONClient). Ad esempio:

```ruby
clnt = JSONClient.new
body = { 
    'email' => 'a-valid@emailaddress.com',
    'due' => '4/1/2020',
    'task' => "My new task!"
}

connection = clnt.post_async(ENV['LOGIC_APP_URL'], body)
```

Se si sta testando questo codice nell'app di esempio per [Creare un'app Ruby e Postgres nel Servizio app di Azure in Linux](containers/tutorial-ruby-postgres-app.md), è possibile provare a inviare una conferma tramite posta elettronica nell'azione [Crea](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L26-L38) [quando @task.save ha esito positivo](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L30).

---

# <a name="more-resources"></a>Altre risorse

[Esercitazione: Ospitare un'API RESTful con CORS nel servizio app di Azure](app-service-web-tutorial-rest-api.md)  
[Informazioni di riferimento su richiesta/risposta HTTP per le App per la logica](../connectors/connectors-native-reqres.md)  
[Avvio rapido: Creare il primo flusso di lavoro con App per la logica di Azure - Portale di Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md)