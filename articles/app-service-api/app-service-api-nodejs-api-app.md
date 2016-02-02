<properties
	pageTitle="Compilare e distribuire un'app per le API Node.js nel servizio app di Azure"
	description="Informazioni su come creare un pacchetto dell'app per le API Node.js e distribuirlo nel servizio app di Azure."
	services="app-service\api"
	documentationCenter="node"
	authors="bradygaster"
	manager="mohisri" 
	editor="tdykstra "/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="node"
	ms.topic="get-started-article"
	ms.date="11/27/2015"
	ms.author="bradygaster"/>

# Compilare e distribuire un'app per le API Node.js nel servizio app di Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Prerequisiti
1. [Node.js](nodejs.org) in esecuzione nel computer di sviluppo (in questo esempio si presuppone che sia installato Node.js versione 4.2.2)
1. Un account [GitHub](https://github.com/)
1. Un [account di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/) di Microsoft Azure
1. Git installato nella workstation di sviluppo locale

## Istruzioni per la configurazione
I comandi riportati di seguito devono essere eseguiti dalla riga di comando di Node.js. Usando il generatore Yo Swaggerize, è possibile eseguire lo scaffolding del codice Node.js di base necessario per rispondere alle richieste HTTP definite in un file JSON Swagger.
 
1. Installare i moduli NPM **yo** e **generator-swaggerize** a livello globale.

        npm install -g yo
	    npm install -g generator-swaggerize
		
1. Clonare il [repository GitHub contenente il codice di esempio](https://github.com/Azure-Samples/app-service-api-node-contact-list).

		git clone https://github.com/Azure-Samples/app-service-api-node-contact-list.git
				
1. Eseguire il comando per lo scaffolding dell'API in base al file **api.json** incluso con il codice sorgente. Il file **api.json** è un file Swagger che rappresenta l'API effettiva di cui verrà eseguito lo scaffolding usando il comando "yo swaggerize" nel passaggio successivo.

        yo swaggerize
        
    **Nota:** il file api.json e il file *apiapp.json* del periodo di anteprima delle app per le API non sono la stessa cosa.

1. Swaggerize esegue lo scaffolding dei gestori e la configurazione dei metadati Swagger inclusi nel file **api.json**. Durante il processo di scaffolding sarà necessario fornire una serie di informazioni, ad esempio l'indirizzo di posta elettronica e il nome utente di GitHub. Queste informazioni vengono usate per generare il file **package.json** nella cartella dell'applicazione. Durante il processo di scaffolding è importante selezionare **express** quando richiesto. In questo esempio si fa uso del motore di visualizzazione Express per generare la pagina della Guida di Swagger in un secondo momento, quando l'app per le API sarà in esecuzione in Azure (o in locale).

	![Riga di comando di Swaggerize](media/app-service-api-nodejs-api-app/swaggerize-command-line.png)
    
1. Spostarsi nella cartella contenente il codice sottoposto a scaffolding (in questo caso, la sottocartella *ContactList*). Installare quindi il modulo NPM **jsonpath**.

        npm install --save jsonpath
        
    I risultati dell'installazione verranno visualizzati nella riga di comando.

    ![Installazione di Jsonpath](media/app-service-api-nodejs-api-app/jsonpath-install.png)

1. Installare il modulo NPM **swaggerize-ui**.

        npm install --save swaggerize-ui
        
    I risultati dell'installazione verranno visualizzati nella riga di comando.

    ![Installazione dell'interfaccia utente Swaggerize](media/app-service-api-nodejs-api-app/swaggerize-ui-install.png)

1. Copiare la cartella **lib** dalla cartella **start** alla cartella **ContactList** creata dal processo di scaffolding.

1. Sostituire il codice nel file **handlers/contacts.js** con il codice seguente. Questo codice usa i dati JSON archiviati nel file **lib/contacts.json** servito da **lib/contactRepository.js**. Il nuovo codice contacts.js riportato di seguito risponde alle richieste HTTP per ottenere tutti i contatti che usano questo codice.

        'use strict';
        
        var repository = require('../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.all())
            }
        };

1. Sostituire il codice nel file **handlers/contacts/{id}.js** con il codice seguente, che usa **lib/contactRepository.js** per ottenere il contatto necessario per la richiesta HTTP e restituirlo come payload JSON.

        'use strict';

        var repository = require('../../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.get(req.params['id']));
            }    
        };

1. Sostituire il codice nel file **server.js** con il codice seguente. Si noti che le modifiche apportate al file server.js vengono evidenziate per mezzo di commenti in modo da visualizzare le modifiche.

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

        server.listen(port, function () { // fifth change
            app.setHost(undefined); // sixth and final change
        });

1. Attivare il server usando l'eseguibile della riga di comando di Node.js.

        node server.js

    Eseguendo questo comando viene avviato il server HTTP Node.js e ha inizio la gestione dell'API.

1. Quando si passa a ****http://localhost:8000/contacts** viene visualizzato l'output JSON dell'elenco contatti (o viene richiesto di scaricarlo, a seconda del browser).

    ![Chiamata dell'API a tutti contatti](media/app-service-api-nodejs-api-app/all-contacts-api-call.png)

1. Quando si passa a ****http://localhost:8000/contacts/2** viene visualizzato il contatto rappresentato da quel valore ID.

    ![Chiamata dell'API a un contatto specifico](media/app-service-api-nodejs-api-app/specific-contact-api-call.png)

1. I dati JSON di Swagger vengono gestiti attraverso l'endpoint **/swagger**:

    ![Json Swagger Contatti](media/app-service-api-nodejs-api-app/contacts-swagger-json.png)

1. L'interfaccia utente di Swagger viene gestita attraverso l'endpoint **/docs**. Nell'interfaccia utente di Swagger è possibile usare le funzionalità client HTML complete per testare l'API.

    ![Interfaccia utente di Swagger](media/app-service-api-nodejs-api-app/swagger-ui.png)

## Creare una nuova app per le API nel portale di Azure
In questa sezione viene illustrato il processo di creazione di una nuova app per le API vuota in Azure. L'app viene quindi collegata a un archivio Git, per abilitare la distribuzione continua delle modifiche al codice.

Il repository GitHub da cui è stato clonato il codice sorgente non è lo stesso archivio in cui verrà eseguito il push del codice per la distribuzione. Il repository GitHub di esempio contiene lo stato "start" del codice. Ora che è stato eseguito lo scaffolding dello stato "end" del codice, è necessario eseguire il push del codice nell'archivio Git associato all'app per le API. Il primo passaggio consiste nel creare l'app per le API usando il portale di Azure, quindi:

1. Accedere al [portale di Azure](https://portal.azure.com/). 

1. Creare una nuova app per le API.

    ![Nuovo portale dell'app per le API](media/app-service-api-nodejs-api-app/new-api-app-portal.png)

1. È possibile aggiungere la nuova app per le API a un gruppo di risorse e/o a un piano di servizio app esistente. In alternativa è possibile creare un gruppo di risorse e un piano di servizio app nuovi, come illustrato nella schermata seguente.

    ![Pannello di creazione dell'app per le API](media/app-service-api-nodejs-api-app/api-app-creation-blade.png)

1. Dopo aver creato l'app per le API nel portale, passare al pannello contenente le impostazioni dell'app per le API, come illustrato di seguito.

    ![Impostazioni di spostamento del portale](media/app-service-api-nodejs-api-app/portal-nav-to-settings.png)

1. Fare clic sull'elemento di navigazione **Credenziali di distribuzione** nel menu Impostazioni. Una volta aperto il pannello, aggiungere un nome utente e una password da usare per la pubblicazione del codice Node.js nell'app per le API. Fare clic sul pulsante **Salva** nel pannello **Imposta credenziali di distribuzione**.

    ![Credenziali di distribuzione](media/app-service-api-nodejs-api-app/deployment-credentials.png)

1. Dopo aver impostato le credenziali di distribuzione, è possibile creare un archivio Git associato al servizio app. Ogni volta che si esegue il push del codice a questo archivio, il servizio app di Azure preleva le modifiche e le distribuisce direttamente all'istanza di app per le API. Per creare un archivio Git da associare al sito, fare clic sulla voce **Distribuzione continua** nel pannello del menu Impostazioni, come illustrato di seguito. Selezionare quindi l'opzione **Archivio Git locale** nel pannello **Scegliere l'origine**. Quindi, fare clic sul pulsante OK per creare l'archivio Git.

    ![Creare l'archivio Git](media/app-service-api-nodejs-api-app/create-git-repo.png)

1. Dopo aver creato l'archivio Git, il pannello mostra le distribuzioni attive. Poiché l'archivio è nuovo, non dovrebbero essere presenti distribuzioni attive nell'elenco.

    ![Nessuna distribuzione attiva](media/app-service-api-nodejs-api-app/no-active-deployments.png)

1. L'ultimo passaggio consiste nel copiare l'URL dell'archivio Git dal portale. A tale scopo, passare al pannello della nuova app per le API e osservare la sezione **Informazioni di base**. In questa sezione dovrebbe essere visualizzato l'**URL clone GIT**. L'icona accanto permette di copiare l'URL negli Appunti. Fare clic sull'icona per copiare l'URL (il pulsante viene visualizzato quando si passa con il mouse sull'URL) oppure selezionare l'intero URL e copiarlo negli Appunti.

    ![Ottenere l'URL Git dal portale](media/app-service-api-nodejs-api-app/get-the-git-url-from-the-portal.png)

    **Nota**: nel passaggio successivo è necessario l'URL clone Git, quindi assicurarsi di salvarlo.

Ora che è disponibile una nuova app per le API con un archivio Git come backup, è possibile eseguire il push del codice nell'archivio e sfruttare le funzionalità di distribuzione continua di Azure per distribuire automaticamente le modifiche.

## Distribuire il codice dell'app per le API in Azure
Usando le funzionalità incorporate di recapito continuo offerte dal servizio app di Azure, è possibile eseguire semplicemente il commit del codice a un archivio Git associato al servizio app per fare in modo che Azure prelevi il codice sorgente e lo distribuisca nell'app per le API.

1. Copiare la cartella **src/end/ContactList** creata dal processo di scaffolding swaggerize sul desktop o in un'altra cartella, perché verrà creato un nuovo archivio Git locale per il codice che dovrebbe trovarsi all'esterno del repository principale clonato da GitHub e contenente il codice iniziale. 

1. Usare la riga di comando di Node.js per spostarsi nella nuova cartella. Eseguire nella nuova cartella il comando riportato di seguito per creare un nuovo archivio Git locale.

        git init

    Questo comando consente di creare un archivio Git locale. Verrà visualizzato un messaggio di conferma che il nuovo archivio è stato inizializzato.

    ![Nuovo archivio Git locale](media/app-service-api-nodejs-api-app/new-local-git-repo.png)

1. Usare la riga di comando di Node.js per eseguire il comando riportato di seguito, che consente di aggiungere un archivio Git remoto a quello locale. L'archivio remoto sarà quello appena creato e associato all'app per le API in esecuzione in Azure.

        git remote add azure YOUR_GIT_CLONE_URL_HERE

    **Nota**: è consigliabile sostituire la stringa "YOUR\_GIT\_CLONE\_URL\_HERE" precedente con il proprio URL clone Git copiato in precedenza.

1. Successivamente, eseguire i due comandi riportati di seguito dalla riga di comando di Node.js.

        git add .
        git commit -m "initial revision"

    Dopo aver eseguito i due comandi, nella finestra della riga di comando verrà visualizzato qualcosa di simile all'immagine riportata di seguito.

    ![Output del commit di Git](media/app-service-api-nodejs-api-app/git-commit-output.png)

1. Per eseguire il push del codice in Azure, che attiva la distribuzione all'app per le API, eseguire il comando riportato di seguito nella riga di comando di Node.js. Quando viene richiesto di immettere una password, digitare quella usata in precedenza durante la creazione delle credenziali di distribuzione nel portale di Azure.

        git push azure master

1. Passando nuovamente al pannello **Distribuzione continua** dell'app per le API, è possibile verificare che la distribuzione sia in corso.

    ![Distribuzione in corso](media/app-service-api-nodejs-api-app/deployment-happening.png)

    Contemporaneamente, la riga di comando di Node.js riflette lo stato della distribuzione in corso.

    ![Distribuzione di Node.js in corso](media/app-service-api-nodejs-api-app/node-js-deployment-happening.png)

1. Dopo aver completato la distribuzione, il pannello **Distribuzione continua** riflette la corretta distribuzione delle modifiche al codice nell'app per le API. Copiare l'**URL** nella sezione **Informazioni di base** del pannello dell'app per le API.

    ![Distribuzione completata](media/app-service-api-nodejs-api-app/deployment-completed.png)

1. Usando un client di API REST, ad esempio Postman o Fiddler o il Web browser, fornire l'URL della chiamata all'API dei contatti, che deve essere l'endpoint **/contacts** dell'app per le API.

    **Nota:** l'URL sarà simile a http://myapiapp.azurewebsites.net/contacts

    Quando si invia una richiesta GET a questo endpoint, dovrebbe essere possibile vedere l'output JSON dell'app per le API.

    ![Postman raggiunge l'API](media/app-service-api-nodejs-api-app/postman-hitting-api.png)

## Passaggi successivi

A questo punto è stata creata e distribuita la prima app per le API usando Node.js. L'esercitazione successiva della serie di esercitazioni introduttive sulle app per le API mostra come [utilizzare app per le API da client JavaScript tramite CORS](app-service-api-cors-consume-javascript.md).

Per basarsi su questo esempio, è possibile aggiungere codice ai gestori per archiviare i dati in un database o sul disco dell'istanza di app per le API. Ora che la distribuzione continua è stata attivata, modificare le funzionalità dell'app per le API ed estendere le modifiche è semplice come modificare il codice ed eseguirne il push all'archivio Git.

<!---HONumber=AcomDC_0128_2016-->