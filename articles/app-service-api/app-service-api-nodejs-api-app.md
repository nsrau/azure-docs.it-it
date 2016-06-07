<properties
	pageTitle="App per le API Node.js nel servizio app di Azure | Microsoft Azure"
	description="Informazioni su come creare un'API RESTful Node.js e distribuirla in un'app per le API nel servizio app di Azure."
	services="app-service\api"
	documentationCenter="node"
	authors="bradygaster"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="node"
	ms.topic="get-started-article"
	ms.date="05/26/2016"
	ms.author="bradygaster"/>

# Compilare un'API RESTful Node.js e distribuirla a un'app per le API in Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

Questa esercitazione illustra come creare un'API [Node.js](http://nodejs.org) e distribuirla in un'[app per le API](app-service-api-apps-why-best-platform.md) nel [servizio app di Azure](../app-service/app-service-value-prop-what-is.md) usando [Git](http://git-scm.com). È possibile usare qualsiasi sistema operativo in grado di eseguire Node.js. Tutte le operazioni verranno eseguite usando strumenti da riga di comando, ad esempio cmd.exe o bash.

## Prerequisiti

1. Account Microsoft Azure (è possibile aprire un account gratuito [qui](https://azure.microsoft.com/pricing/free-trial/))
1. [Node.js](http://nodejs.org) installato (questo esempio presuppone che sia installato Node.js versione 4.2.2)
2. [Git](https://git-scm.com/) installato
1. Un account [GitHub](https://github.com/)

Per quanto il servizio app consenta di distribuire il codice in un'app per le API in molti modi, questa esercitazione illustra il metodo Git e presuppone una conoscenza di base dell'uso di Git. Per informazioni sugli altri metodi di distribuzione, vedere [Distribuire l'app nel servizio app di Azure](../app-service-web/web-sites-deploy.md).

## Scaricare il codice di esempio

1. Aprire un'interfaccia della riga di comando per eseguire Node.js e i comandi di Git.

1. Passare a una cartella che può essere usata per un repository Git locale e clonare il [repository GitHub contenente il codice di esempio](https://github.com/Azure-Samples/app-service-api-node-contact-list).

		git clone https://github.com/Azure-Samples/app-service-api-node-contact-list.git

	L'API di esempio fornisce due endpoint: una richiesta Get a `/contacts` restituisce un elenco di nomi e indirizzi di posta elettronica in formato JSON, mentre `/contacts/{id}` restituisce solo il contatto selezionato.

## Eseguire lo scaffolding (generazione automatica) del codice Node.js in base a metadati di Swagger

[Swagger](http://swagger.io/) è un formato di file per i metadati che descrivono un'API RESTful. Il servizio app di Azure include il [supporto predefinito per i metadati di Swagger](app-service-api-metadata.md). In questa sezione dell'esercitazione viene modellato un flusso di lavoro di sviluppo di API che crea prima i metadati di Swagger e li usa per lo scaffolding (generazione automatica) del codice server per l'API.

>[AZURE.NOTE] Se non si è interessati allo scaffolding del codice Node.js da un file di metadati Swagger, è possibile ignorare questa sezione. Se si vuole semplicemente distribuire codice di esempio in una nuova app per le API, andare direttamente alla sezione [Creare una nuova app per le API](#createapiapp).

### Installare ed eseguire Swaggerize

1. Per installare i moduli NPM **yo** e **generator-swaggerize** a livello globale, eseguire questi comandi.

		npm install -g yo
		npm install -g generator-swaggerize

	Swaggerize è uno strumento che genera un codice server per un'API descritta da un file di metadati Swagger. Il file Swagger che si userà è denominato *api.json* e si trova nella cartella *start* del repository clonato.

2. Passare alla cartella *start* e quindi eseguire il comando `yo swaggerize`. Swaggerize formulerà una serie di domande. Per **What would you like to call this project** immettere "contactlist", per **Path (or URL) to swagger document** immettere "api.json" e per **Express, Hapi or Restify** immettere "express".

		yo swaggerize

	![Riga di comando di Swaggerize](media/app-service-api-nodejs-api-app/swaggerize-command-line.png)
    
	**Nota**: se si riscontra un errore in questo passaggio, il passaggio successivo illustra come correggerlo.

	Swaggerize crea una cartella dell'applicazione, esegue lo scaffolding dei gestori e dei file di configurazione e genera un file **package.json**. Il motore di visualizzazione express viene usato per generare la pagina della Guida di Swagger.

3. Se il comando `swaggerize` ha esito negativo con un errore "unexpected token" o "invalid escape sequence", correggere la causa dell'errore modificando il file *package.json* generato. Nella riga `regenerate` sotto `scripts` sostituire la barra rovesciata che precede *api.json* con una barra, in modo che la riga si presenti come nell'esempio seguente:

 		"regenerate": "yo swaggerize --only=handlers,models,tests --framework express --apiPath config/api.json"

1. Passare alla cartella che contiene il codice sottoposto a scaffolding (in questo caso, la sottocartella *ContactList*).

1. Eseguire `npm install`.
	
		npm install
		
2. Installare il modulo NPM **jsonpath**.

		npm install --save jsonpath
        
    ![Installazione di Jsonpath](media/app-service-api-nodejs-api-app/jsonpath-install.png)

1. Installare il modulo NPM **swaggerize-ui**.

		npm install --save swaggerize-ui
        
    ![Installazione dell'interfaccia utente Swaggerize](media/app-service-api-nodejs-api-app/swaggerize-ui-install.png)

### Personalizzare il codice sottoposto a scaffolding

1. Copiare la cartella **lib** dalla cartella **start** alla cartella **ContactList** creata dal processo di scaffolding. 

1. Sostituire il codice nel file **handlers/contacts.js** con il codice seguente.

	Questo codice usa i dati JSON archiviati nel file **lib/contacts.json** servito da **lib/contactRepository.js**. Il nuovo codice contacts.js risponde alle richieste HTTP per ottenere tutti i contatti e restituirli come payload JSON.

        'use strict';
        
        var repository = require('../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.all())
            }
        };

1. Sostituire il codice nel file **handlers/contacts/{id}.js** con il codice seguente.

        'use strict';

        var repository = require('../../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.get(req.params['id']));
            }    
        };

1. Sostituire il codice nel file **server.js** con il codice seguente.

	Le modifiche apportate al file server.js vengono indicate usando i commenti per poter visualizzare le modifiche.

        'use strict';

        var port = process.env.PORT || 8000; // first change

        var http = require('http');
        var express = require('express');
        var bodyParser = require('body-parser');
        var swaggerize = require('swaggerize-express');
        var swaggerUi = require('swaggerize-ui'); // second change
        var path = require('path');

        var app = express();

        var server = http.createServer(app);

        app.use(bodyParser.json());

        app.use(swaggerize({
            api: path.resolve('./config/api.json'), // third change
            handlers: path.resolve('./handlers'),
            docspath: '/swagger' // fourth change
        }));

        // change four
        app.use('/docs', swaggerUi({
          docs: '/swagger'  
        }));

        server.listen(port, function () { // fifth and final change
        });

### Eseguire il test con l'API in esecuzione in locale

1. Attivare il server usando l'eseguibile della riga di comando di Node.js. 

        node server.js

1. Quando si passa a ****http://localhost:8000/contacts**, viene visualizzato l'output JSON dell'elenco contatti (o viene richiesto di scaricarlo, a seconda del browser).

    ![Chiamata dell'API a tutti contatti](media/app-service-api-nodejs-api-app/all-contacts-api-call.png)

1. Quando si passa a ****http://localhost:8000/contacts/2**, viene visualizzato il contatto rappresentato da tale valore di ID.

    ![Chiamata dell'API a un contatto specifico](media/app-service-api-nodejs-api-app/specific-contact-api-call.png)

1. I dati JSON di Swagger vengono gestiti attraverso l'endpoint **/swagger**:

    ![Json Swagger Contatti](media/app-service-api-nodejs-api-app/contacts-swagger-json.png)

1. L'interfaccia utente di Swagger viene gestita attraverso l'endpoint **/docs**. Nell'interfaccia utente di Swagger è possibile usare le funzionalità client HTML complete per testare l'API.

    ![Interfaccia utente di Swagger](media/app-service-api-nodejs-api-app/swagger-ui.png)

## <a id="createapiapp"></a> Creare una nuova app per le API

In questa sezione si usa il portale di Azure per creare una nuova app per le API in Azure. Questa app per le API rappresenta le risorse di calcolo che verranno fornite da Azure per l'esecuzione del codice. Nelle sezioni successive verrà distribuito il codice nella nuova app per le API.

1. Accedere al [portale di Azure](https://portal.azure.com/). 

1. Fare clic su **Nuovo > Web e dispositivi mobili > App per le API**.

    ![Nuova app per le API nel portale](media/app-service-api-nodejs-api-app/new-api-app-portal.png)

4. Immettere un **Nome app** univoco nel dominio *azurewebsites.net*, ad esempio NodejsAPIApp, aggiungendovi un numero per renderlo univoco.

	Ad esempio, se il nome del sito è `NodejsAPIApp`, l'URL sarà `nodejsapiapp.azurewebsites.net`.

	Se si immette un nome già usato da qualcun altro, viene visualizzato un punto esclamativo a destra.

6. Nell'elenco a discesa **Gruppo di risorse** fare clic su **Nuovo** e quindi in **Nuovo nome gruppo di risorse** immettere "NodejsAPIAppGroup" o un altro nome, se si preferisce.

	Un [gruppo di risorse](../azure-portal/resource-group-portal.md) è una raccolta di risorse di Azure, ad esempio app per le API, database e VM. Per questa esercitazione è consigliabile creare un nuovo gruppo di risorse, per eliminare facilmente tutte le risorse di Azure create per l'esercitazione in un unico passaggio.

4. Fare clic su **Piano di servizio app/Località** e quindi su **Crea nuovo**.

	![Creare un piano di servizio app](./media/app-service-api-nodejs-api-app/newappserviceplan.png)

	Nei passaggi seguenti si crea un piano di servizio app per il nuovo gruppo di risorse. Un piano di servizio app specifica le risorse di calcolo in cui viene eseguita l'app per le API. Se, ad esempio, si sceglie il livello gratuito, l'app per le API viene eseguita in VM condivise, mentre con alcuni livelli a pagamento viene eseguita in VM dedicate. Per altre informazioni sui piani di servizio app, vedere [Panoramica approfondita dei piani del servizio app di Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. Nel pannello **Piano di servizio app** immettere "NodejsAPIAppPlan" o un altro nome, se si preferisce.

5. Nell'elenco a discesa **Località** scegliere la località più vicina.

	Questa impostazione specifica il data center di Azure in cui verrà eseguita l'app. Per questa esercitazione, è possibile selezionare qualsiasi area senza riscontrare differenze evidenti, ma per un'app di produzione è consigliabile che il server sia il più vicino possibile ai client che vi accedono per ridurre al minimo la [latenza](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. Fare clic su **Piano tariffario > Visualizza tutto > F1 Gratuito**.

	Per questa esercitazione il piano tariffario Gratuito fornirà prestazioni sufficienti.

	![Selezionare il piano tariffario Gratuito](./media/app-service-api-nodejs-api-app/selectfreetier.png)

6. Nel pannello **Piano di servizio app** fare clic su **OK**.

7. Nel pannello **App per le API** fare clic su **Crea**.

## Configurare la nuova app per le API per la distribuzione Git

Per distribuire il codice nell'app per le API, sarà necessario effettuare il push dei commit in un repository Git nel servizio app di Azure. In questa sezione dell'esercitazione si creano le credenziali e il repository Git in Azure che si useranno per la distribuzione.

1. Dopo che l'app per le API è stata creata, fare clic su **Servizi app > {app per le API}** nella home page del portale. 

	Il portale visualizza i pannelli **App per le API** e **Impostazioni**.

    ![Pannelli App per le API e Impostazioni nel portale](media/app-service-api-nodejs-api-app/portalapiappblade.png)

1. Nel pannello **Impostazioni** scorrere verso il basso fino alla sezione **Pubblicazione** e quindi fare clic su **Credenziali per la distribuzione**.
 
3. Nel pannello **Imposta credenziali di distribuzione** immettere un nome utente e una password e quindi fare clic su **Salva**.

	Queste credenziali verranno usate per pubblicare il codice Node.js nell'app per le API.

    ![Credenziali di distribuzione](media/app-service-api-nodejs-api-app/deployment-credentials.png)

1. Nel pannello **Impostazioni** fare clic su **Origine distribuzione > Scegliere l'origine > Repository Git locale** e quindi fare clic su **OK**.

    ![Creare l'archivio Git](media/app-service-api-nodejs-api-app/create-git-repo.png)

1. Dopo aver creato il repository Git, il pannello mostra le distribuzioni attive. Poiché l'archivio è nuovo, non sono presenti distribuzioni attive nell'elenco.

    ![Nessuna distribuzione attiva](media/app-service-api-nodejs-api-app/no-active-deployments.png)

1. Copiare l'URL del repository Git. A tale scopo, passare al pannello della nuova app per le API e osservare la sezione **Informazioni di base**. Si noti **URL clone GIT** nella sezione **Informazioni di base**. Quando si passa il puntatore su questo URL, a destra viene visualizzata un'icona per copiare l'URL negli Appunti. Fare clic su questa icona per copiare l'URL.

    ![Ottenere l'URL Git dal portale](media/app-service-api-nodejs-api-app/get-the-git-url-from-the-portal.png)

    **Nota**: nella sezione successiva è necessario l'URL clone Git, quindi assicurarsi di salvarlo.

Quando è disponibile l'app per le API basata sul repository Git, è possibile inserire il codice nel repository per distribuire il codice nell'app per le API.

## Distribuire il codice dell'API in Azure

In questa sezione si crea un repository Git locale contenente il codice server per l'API e quindi si effettua il push del codice da tale repository al repository in Azure creato in precedenza.

1. Copiare la cartella `ContactList` in una posizione che possa essere usata per un nuovo repository Git locale. Se la prima parte dell'esercitazione è stata eseguita, copiare `ContactList` dalla cartella `start`. In caso contrario, copiare `ContactList` dalla cartella `end`.

1. Nello strumento da riga di comando passare alla nuova cartella, quindi eseguire il comando seguente per creare un nuovo repository Git locale.

        git init

     ![Nuovo archivio Git locale](media/app-service-api-nodejs-api-app/new-local-git-repo.png)

1. Eseguire il comando seguente per aggiungere un repository Git remoto per il repository dell'app per le API.

        git remote add azure YOUR_GIT_CLONE_URL_HERE

    **Nota**: sostituire la stringa "YOUR\_GIT\_CLONE\_URL\_HERE" con il proprio URL clone Git copiato in precedenza.

1. Eseguire i comandi seguenti per creare un commit che contiene tutto il codice.

        git add .
        git commit -m "initial revision"

    ![Output del commit di Git](media/app-service-api-nodejs-api-app/git-commit-output.png)

1. Eseguire il comando per effettuare il push del codice in Azure. Quando viene chiesta la password, immettere quella creata prima nel portale di Azure.

        git push azure master

	Viene attivata una distribuzione nell'app per le API.

1. Nel browser tornare al pannello **Distribuzioni** dell'app per le API, in cui è possibile verificare che la distribuzione sia in corso.

    ![Distribuzione in corso](media/app-service-api-nodejs-api-app/deployment-happening.png)

    Contemporaneamente, l'interfaccia della riga di comando riflette lo stato della distribuzione in corso.

    ![Distribuzione di Node.js in corso](media/app-service-api-nodejs-api-app/node-js-deployment-happening.png)

	Al termine della distribuzione, il pannello **Distribuzioni** riflette il completamento della distribuzione delle modifiche al codice nell'app per le API.

## Eseguire il test con l'API in esecuzione in Azure
 
3. Copiare l'**URL** nella sezione **Informazioni di base** del pannello dell'app per le API. 

    ![Distribuzione completata](media/app-service-api-nodejs-api-app/deployment-completed.png)

1. Usando un client di API REST, ad esempio Postman o Fiddler o il Web browser, fornire l'URL della chiamata all'API dei contatti, che è l'endpoint `/contacts` dell'app per le API. L'URL sarà `https://{your API app name}.azurewebsites.net/contacts`.

    Quando si invia una richiesta GET a questo endpoint, si ottiene l'output JSON dell'app per le API.

    ![Postman raggiunge l'API](media/app-service-api-nodejs-api-app/postman-hitting-api.png)

2. In un browser andare all'endpoint `/docs` per provare l'interfaccia utente di Swagger mentre è in esecuzione in Azure.

Ora che il recapito continuo è stato associato, è possibile apportare modifiche al codice e distribuirle in Azure semplicemente effettuando il push dei commit nel repository Git di Azure.

## Passaggi successivi

A questo punto sono state completate la creazione di un'app per le API e la distribuzione in tale app del codice di un'API Node.js. L'esercitazione successiva mostra come [utilizzare app per le API da client JavaScript tramite CORS](app-service-api-cors-consume-javascript.md).

<!---HONumber=AcomDC_0601_2016-->