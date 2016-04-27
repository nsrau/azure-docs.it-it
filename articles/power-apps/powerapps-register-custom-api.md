<properties
	pageTitle="Come usare le API personalizzate nei flussi logici e in PowerApps | Microsoft Azure"
	description="Che cosa sono le API personalizzate, uso di provider OAuth e uso di Swagger per aggiungere API personalizzate in PowerApps e nei flussi logici"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="sunaysv"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="04/12/2016"
   ms.author="mandia"/>

# Che cosa sono le API personalizzate

Le API personalizzate sono le API RESTful che è possibile importare e usare con PowerApps e i flussi logici. Queste API possono essere ospitate ovunque, purché sia disponibile una specifica ben documentata conforme allo standard [OpenAPI][1].

## Elementi necessari per iniziare

- Le API personalizzate possono essere usate da chiunque con PowerApps. Non è necessario PowerApps Enterprise. 
- Sono necessari un file Swagger (JSON) e un'icona per l'API personalizzata. Questo argomento contiene diverse opzioni per creare il file Swagger. Per un'icona, è possibile usare un'immagine qualsiasi.


## Autenticazione

È possibile usare uno dei meccanismi di autenticazione seguenti:

- Autenticazione di base 
- OAuth 2.0: di seguito sono elencati tutti i provider OAuth 2.0 supportati che è possibile usare con l'API personalizzata (sarà presto disponibile il supporto per altri provider):	

	- Azure Active Directory
	- Box
	- Dropbox
	- Facebook
	- Google
	- Instagram
	- OneDrive
	- SalesForce
	- Slack
	- Yammer

> [AZURE.NOTE] Sarà presto disponibile il supporto per l'autenticazione delle chiavi API.


Per altre informazioni sulla specifica del tipo di autenticazione nel documento OpenAPI (Swagger), vedere la [specifica di OpenAPI][1].

Se l'endpoint API consente l'accesso non autenticato, è consigliabile rimuovere l'oggetto ```securityDefintions``` dal file OpenAPI (Swagger). Nell'esempio seguente rimuovere interamente l'oggetto ```securityDefintions``` seguente:

```
  "securityDefinitions": {
    "AAD": {
      "type": "oauth2",
      "flow": "implicit",
      "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
      "scopes": []
    }
  },
```

> [AZURE.TIP] Nei file JSON non è possibile aggiungere commenti. Tutto il testo viene considerato parte dei dati.

### Esempi di autenticazione
* [Azure Resource Manager](powerapps-azure-resource-manager-tutorial.md) con l'autenticazione AAD
* [Azure WebApp](powerapps-web-api-tutorial.md) con l'autenticazione AAD

## Registrare un'API personalizzata

### Passaggio 1: Creare un file Swagger

È possibile creare un file Swagger da **qualsiasi** endpoint API, tra cui:

- Qualsiasi API pubblicata e disponibile pubblicamente, ad esempio [Spotify][2], [Uber][3], [Slack][4], [Rackspace][5] e altre. 
- Un'API creata e distribuita in qualsiasi servizio cloud dove è possibile distribuire app Web, tra cui Amazon Web Services (AWS), Heroku, app Web di Azure, Google Cloud e altre.  
- Un'API distribuita in rete o nel computer, purché sia disponibile pubblicamente su Internet.

Quando si crea il file Swagger, viene creato un file JSON. Tenere pronto questo file JSON.

#### Ottenere assistenza per la creazione di file Swagger

- Se non si ha familiarità con la creazione di un file Swagger o non si è mai creato un file Swagger prima, vedere l'[introduzione a Swagger][6]. 
 
- Per creare l'API, distribuirla in Azure e creare un file Swagger basato su questa nuova API, quindi considerare la possibilità di usare l'[esercitazione sull'API Web](powerapps-web-api-tutorial.md), che include un file Swagger funzionante. In GitHub è disponibile anche un [esempio di Hello World][7].

- Per convalidare i file Swagger, usare l'[editor Swagger][8]. È possibile incollare i dati del file JSON per eseguire automaticamente la convalida.

- Per personalizzare il documento Swagger per usarlo con PowerApps e i flussi logici, vedere [Personalizzare la definizione Swagger](powerapps-how-to-swagger.md).

### Passaggio 2: Aggiungere una connessione all'API personalizzata
Ora che il file Swagger (file JSON) è stato generato per l'API personalizzata, aggiungere la connessione a PowerApps. Sarà necessaria anche l'icona per l'API personalizzata.

1. Andare al [portale Web di PowerApps][9] e accedere con l'account aziendale.  

	> [AZURE.NOTE] Per il momento le API personalizzate possono essere usate solo nel portale Web di PowerApps. Non possono essere usate nel client PowerApps.

2. Selezionare **Connections** e quindi selezionare **Add a connection**:
![](./media/powerapps-register-custom-api/createnewconnection.png "Creare un'API personalizzata")

3. Selezionare **Add a custom API**:
![](./media/powerapps-register-custom-api/connecttocustomapi.png "Creare un'API personalizzata") Aggiungere le proprietà dell'API, inclusi i file JSON e dell'icona. Selezionare quindi **Next**:

	|Proprietà|Descrizione|
|---|---|
|Nome|Immettere il nome dell'API personalizzata. Se si tratta dell'API Web di esempio, è possibile denominarla **MySampleWebAPI**.|
|Swagger API definition|Passare al file JSON creato da Swagger.|
|Upload API icon|Passare al file dell'icona.|
|Descrizione|Immettere una descrizione dell'API personalizzata. Se si tratta dell'API Web di esempio, è possibile immettere **Esercitazione API Web di esempio**.|

4. Immettere eventuali proprietà di autenticazione. Se il file JSON usa l'autenticazione OAuth2 nell'oggetto ```securityDefintions```, vengono richiesti i valori seguenti:

	|Proprietà|Descrizione|
|---|---|
|Client Id|Se si usa uno dei provider di identità OAuth supportati (elencati in questo argomento), viene fornito un ID client. Immettere questo ID client.
|Segreto client|Immettere il segreto client dal provider di identità scelto.|  

	La sezione **Esempi di autenticazione** di questo argomento fornisce esempi delle proprietà OAuth.

	Se il file JSON non usa l'oggetto ```securityDefintions```, non saranno necessari valori aggiuntivi.

5. Selezionare **Create**. L'API personalizzata ora è visualizzata in **Available Connections**:

	![](./media/powerapps-register-custom-api/mycustomapi.png "Connessioni disponibili")


> [AZURE.TIP] Se la convalida dei file Swagger non riesce, potrebbero essere presenti caratteri aggiuntivi. Ad esempio, la maggior parte dei dati deve essere tra virgolette, inclusi i siti Web. Se quindi `https://mywebapi.mywebsite.com` non è racchiuso tra virgolette, la convalida del file non riesce.


### Passaggio 3: Aggiungere l'API personalizzata a un flusso logico e a PowerApps
Ora è possibile usare l'API personalizzata con PowerApps o con il flusso logico. In questa sezione si usa un'API per il meteo personalizzata.

#### Aggiungere l'API personalizzata al flusso logico
In questo passaggio si crea un flusso logico molto semplice che illustra come aggiungere l'API personalizzata. Per un'esperienza più approfondita, vedere [Introduzione ai flussi logici][10].

1. Nel [portale Web][9] di PowerApps selezionare la scheda **Home**.
2. In **Make a logic flow** selezionare **Get started**. 
3. In questa finestra sono disponibili diversi modelli di flusso logico già creati che usano alcuni scenari comuni. È possibile usarne uno qualsiasi e aggiungervi l'API personalizzata. È anche possibile scegliere **Create from blank** per creare un flusso logico da zero.  

	Il modo più rapido per aggiungere l'API personalizzata è selezionare **Create from blank**. In questo modo viene aperto il flusso logico seguente:
![](./media/powerapps-register-custom-api/createfromblank.png "Avvio di un flusso logico")

4. Selezionare **Recurrence** e impostare la frequenza su 1 minuto:
![](./media/powerapps-register-custom-api/logicrecurrence.png "Selezionare una ricorrenza")

5. Selezionare il segno più (![](./media/powerapps-register-custom-api/flowplussign.png)) e selezionare **Add an action**. Nell'elenco viene inserita l'API personalizzata:
![](./media/powerapps-register-custom-api/logicflow.png "API personalizzata")

I passaggi successivi dipendono dalle operazioni che possono essere eseguite dall'API. In un esempio relativo al meteo l'API potrebbe ottenere la temperatura corrente e quindi inviare un messaggio di posta elettronica con Office 365:

![](./media/powerapps-register-custom-api/logicflowexample.png "Esempio meteo")



#### Aggiungere l'API personalizzata a PowerApps
In questo passaggio si crea una PowerApp molto semplice che illustra come aggiungere l'API personalizzata. Per un'esperienza più approfondita, vedere [Creare un'app dai dati][11].

> [AZURE.NOTE] Per il momento le API personalizzate possono essere usate solo nel portale Web di PowerApps. Non possono essere usate nel client PowerApps.

1. Nel [portale Web][9] di PowerApps selezionare **New PowerApp**:
![](./media/powerapps-register-custom-api/newpowerapp.png "Selezionare New PowerApp")  
2. Viene aperta una nuova scheda nel browser. In questa nuova scheda viene automaticamente creata una PowerApp vuota. Selezionare **connect to data**:
![](./media/powerapps-register-custom-api/blankpowerapp.png "Selezionare connect to data")  
3. Nella scheda **Content** selezionare **Data sources**:
![](./media/powerapps-register-custom-api/datasources.png "Selezionare connect to data")  
4. Nella nuova schermata selezionare l'API personalizzata in **My connections**:
![](./media/powerapps-register-custom-api/screencustomapi.png "Selezionare l'API personalizzata.")  
5. Selezionare **Add data source**.

Una volta aggiunta l'API personalizzata, è possibile usarla nella barra delle funzioni, in una casella di testo e altro. Nella barra delle funzioni, ad esempio, è possibile iniziare a digitare **MySampleWebAPI** per visualizzare le funzioni disponibili. [Visualizzare i dati da Office 365][12] è un esempio dell'uso dell'API di Office 365.


## Condivisione di un'API personalizzata
Gli utenti possono anche condividere tra loro le API personalizzate. Una volta aggiunta un'API personalizzata, selezionare la scheda **Connections**, selezionare **Custom APIs** e quindi selezionare l'icona della condivisione:

![](./media/powerapps-register-custom-api/sharecustomapi.png "Condividere un'API personalizzata")

> [AZURE.NOTE] È possibile condividere API personalizzate con altri utenti solo nella propria organizzazione.

## Quote e limitazione

- È possibile creare fino a cinque API personalizzate in un account PowerApps. Le API personalizzate condivise con l'utente non vengono conteggiate a fronte di questa quota.
- Per ogni connessione creata in un'API personalizzata, gli utenti possono eseguire fino a 500 richieste al minuto.
- Si ricordi che, eliminando un'API personalizzata, vengono eliminate tutte le connessioni all'API create. 

Per domande o commenti sulle API personalizzate, inviare un messaggio di posta elettronica all'indirizzo [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com).

<!--Reference links in article-->
[1]: https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#securityDefinitionsObject
[2]: https://developer.spotify.com/
[3]: https://developer.uber.com/
[4]: https://api.slack.com/
[5]: http://docs.rackspace.com/
[6]: http://swagger.io/getting-started/
[7]: https://github.com/OAI/OpenAPI-Specification/wiki/Hello-World-Sample
[8]: http://editor.swagger.io/#/
[9]: https://web.powerapps.com
[10]: https://powerapps.microsoft.com/tutorials/get-started-logic-flow/
[11]: https://powerapps.microsoft.com/tutorials/get-started-create-from-data/
[12]: https://powerapps.microsoft.com/tutorials/show-office-data/

<!---HONumber=AcomDC_0413_2016-->