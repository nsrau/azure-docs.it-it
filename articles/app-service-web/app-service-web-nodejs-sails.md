<properties
    pageTitle="Distribuire un'app Web Sails.js nel servizio app di Azure"
    description="Informazioni su come distribuire un'applicazione Node.js nel servizio app di Azure. Questa esercitazione illustra come distribuire un'app Web Sails.js."
    services="app-service\web"
    documentationCenter="nodejs"
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="cephalin"/>


# <a name="deploy-a-sails.js-web-app-to-azure-app-service"></a>Distribuire un'app Web Sails.js nel servizio app di Azure

Questa esercitazione illustra come distribuire un'app Sails.js nel servizio app di Azure. Durante il processo vengono offerte informazioni generali sulla configurazione dell'app Node.js da eseguire nel servizio app. 

Avere una conoscenza pratica di Sails.js. Questa esercitazione non fornisce informazioni sulla risoluzione dei problemi generali di esecuzione di Sail.js.


## <a name="prerequisites"></a>Prerequisiti

- [Node.JS](https://nodejs.org/)
- [Sails.js](http://sailsjs.org/get-started)
- [Git](http://www.git-scm.com/downloads)
- [Interfaccia della riga di comando di Azure](../xplat-cli-install.md)
- Un account Microsoft Azure. Se non è disponibile un account, è possibile [iscriversi per ottenere una versione di valutazione gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) oppure [attivare i benefici della sottoscrizione di Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Per provare il servizio app di Azure prima di iscriversi per ottenere un account Azure, vedere [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751). In questa pagina è possibile creare immediatamente un'app iniziale temporanea nel servizio app. Non è richiesta una carta di credito né occorre impegnarsi in alcun modo.

## <a name="step-1:-create-a-sails.js-app-locally"></a>Passaggio 1: Creare un'app Sails.js in locale

Innanzitutto, creare rapidamente un'app Sails.js predefinita nell'ambiente di sviluppo attenendosi alla procedura seguente:

1. Aprire il terminale della riga di comando desiderato ed eseguire `CD` in una directory di lavoro.

2. Creare un'app Sails.js ed eseguirla:

        sails new <appname>
        cd <appname>
        sails lift

    Assicurarsi che sia possibile passare alla home page predefinita in http://localhost:1377.

## <a name="step-2:-create-the-azure-app-resource"></a>Passaggio 2: Creare la risorsa per l'app di Azure

Quindi creare la risorsa del servizio app in Azure. In seguito, l'app Sails.js verrà distribuita nella risorsa.

1. accedere ad Azure in questo modo:
1. Nel stesso terminale passare in modalità ASM e accedere ad Azure:

        azure config mode asm
        azure login

    Seguire le istruzioni per continuare l'accesso in un browser usando un account con la sottoscrizione di Azure.

2. Assicurarsi di essere ancora nella directory radice del progetto Sails.js. Creare la risorsa dell'app del servizio app in Azure con un nome app univoco con il comando successivo. L'URL dell'app Web sarà http://&lt;appname>.azurewebsites.net.

        azure site create --git <appname>

    Seguire le istruzioni e selezionare un'area di Azure in cui eseguire la distribuzione. Se non sono mai state configurate credenziali di distribuzione Git/FTP per la sottoscrizione di Azure, verrà chiesto di crearle.

    Dopo aver creato la risorsa dell'app del servizio app:

    - L'app Sails.js viene inizializzata da Git,
    - Il repository inizializzato Git locale viene connesso alla nuova app del servizio app come Git remoto denominato "azure" e
    - Il file iisnode viene creato nella directory radice. Questo file consente di configurare [iisnode](https://github.com/tjanczuk/iisnode)usato dal servizio app per l'esecuzione delle app Node.js.

## <a name="step-3:-configure-and-deploy-your-sails.js-app"></a>Passaggio 3: Configurare e distribuire l'app Sails.js

 L'uso di una app Sails.js nel servizio app prevede tre passaggi principali:

 - Configurare l'app per l'esecuzione nel servizio app
 - Distribuirla nel servizio app
 - Leggere i log stderr e stdout per risolvere eventuali problemi di distribuzione

A tale scopo, seguire questa procedura:

1. Aprire il nuovo file iisnode.yml nella directory radice e aggiungere le due righe seguenti:

        loggingEnabled: true
        logDirectory: iisnode

    La registrazione è ora abilitata per iisnode. Per altre informazioni sul funzionamento, vedere  [Ottenere i log stdout e stderr da iisnode](app-service-web-nodejs-get-started.md#iisnodelog).

2. Aprire config/env/production.js per configurare l'ambiente di produzione e impostare `port` e `hookTimeout`:

        module.exports = {

            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,

            ...
        };

    La documentazione di queste impostazioni di configurazione è disponibile nella  [Documentazione di Sails.js](http://sailsjs.org/documentation/reference/configuration/sails-config).

    Successivamente, è necessario assicurarsi che [Grunt](https://www.npmjs.com/package/grunt) sia compatibile con le unità di rete di Azure. Le versioni di Grunt precedenti alla 1.0.0 usano un pacchetto [glob](https://www.npmjs.com/package/glob) obsoleto, precedente alla versione 5.0.14, che non supporta unità di rete. 

3. Aprire package.json, modificare la versione di `grunt` in `1.0.0` e rimuovere tutti i pacchetti `grunt-*`. La proprietà `dependencies` sarà simile alla seguente:

        "dependencies": {
            "ejs": "<leave-as-is>",
            "grunt": "1.0.0",
            "include-all": "<leave-as-is>",
            "rc": "<leave-as-is>",
            "sails": "<leave-as-is>",
            "sails-disk": "<leave-as-is>",
            "sails-sqlserver": "<leave-as-is>"
        },

3. In package.json aggiungere la proprietà `engines` seguente per impostare la versione di Node.js desiderata.

        "engines": {
            "node": "6.6.0"
        },

6. Salvare le modifiche e testarle per assicurarsi che l'app sia ancora in esecuzione in locale. A tale scopo, eliminare la cartella `node_modules` e quindi eseguire:

        npm install
        sails lift

4. Usare Git per distribuire l'app in Azure:

        git add .
        git commit -m "<your commit message>"
        git push azure master

5. Infine, avviare l'app Azure attiva nel browser:

        azure site browse

    Verrà visualizzata la stessa home page di Sails.js.
    
    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## <a name="troubleshoot-your-deployment"></a>Risoluzione dei problemi di distribuzione

Se l'applicazione Sails.js per qualche motivo non viene avviata nel servizio app, cercare i log stderr per risolvere i problemi.
Per altre informazioni, vedere [Ottenere i log stdout e stderr da iisnode](app-service-web-nodejs-sails.md#iisnodelog).
Se l'applicazione è stata avviata correttamente, il log stdout visualizzerà il messaggio:

                .-..-.

    Sails              <|    .-..-.
    v0.12.4             |\
                        /|.\
                        / || \
                    ,'  |'  \
                    .-'.-==|/_--'
                    `--'-------' 
    __---___--___---___--___---___--___
    ____---___--___---___--___---___--___-__

    Server lifted in `D:\home\site\wwwroot`
    To see your app, visit http://localhost:\\.\pipe\c775303c-0ebc-4854-8ddd-2e280aabccac
    To shut down Sails, press <CTRL> + C at any time.

È possibile controllare la granularità dei log stdout nel file [config/log.js](http://sailsjs.org/#!/documentation/concepts/Logging) . 

## <a name="connect-to-a-database-in-azure"></a>Connettersi a un database in Azure

Per connettersi a un database in Azure, creare il database desiderato in Azure, ad esempio un database SQL di Azure, MySQL, MongoDB, Cache (Redis) di Azure e così via e usare l' [adattatore dell'archivio dati](https://github.com/balderdashy/sails#compatibility) corrispondente per stabilire la connessione. I passaggi descritti in questa sezione illustrano come connettersi a un database MySQL in Azure.

1. Seguire l'esercitazione disponibile [qui](../store-php-create-mysql-database.md) per creare un database MySQL in Azure.

2. Dal terminale della riga di comando installare l'adattatore di MySQL:

        npm install sails-mysql --save

3. Aprire config/connections.js e aggiungere l'oggetto connessione seguente all'elenco: 

        mySql: {
            adapter: 'sails-mysql',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.dbhost, 
            database: process.env.dbname,
            options: {
                encrypt: true
            }
        },

4. Per ogni variabile di ambiente (`process.env.*`), è necessario eseguirne l'impostazione nel servizio app. A tale scopo, eseguire i comandi seguenti dal terminale. Tutte le informazioni di connessione necessarie sono disponibili nel portale di Azure; vedere [Connettersi al database MySQL](../store-php-create-mysql-database.md#connect).

        azure site appsetting add dbuser="<database user>"
        azure site appsetting add dbpassword="<database password>"
        azure site appsetting add dbhost="<database hostname>"
        azure site appsetting add dbname="<database name>"
        
    L'inserimento delle proprie impostazioni nelle impostazioni dell'app Azure mantiene i dati sensibili al di fuori del controllo del codice sorgente (Git). Si configurerà quindi l'ambiente di sviluppo per l'uso delle stesse informazioni di connessione.

4. Aprire config/local.js e aggiungere l'oggetto connessione seguente:

        connections: {
            mySql: {
                user: "<database user>",
                password: "<database password>",
                host: "<database hostname>", 
                database: "<database name>",
            },
        },
    
    Questa configurazione sostituisce le impostazioni presenti nel file config/connections.js per l'ambiente locale. Questo file è escluso dal file con estensione gitignore predefinito del progetto, quindi non verrà archiviato in Git. A questo punto, è possibile connettersi al database MySQL dall'app Web di Azure e dall'ambiente di sviluppo locale.

4. Aprire config/env/production.js per configurare l'ambiente di produzione e aggiungere l'oggetto `models` seguente:

        models: {
            connection: 'mySql',
            migrate: 'safe'
        },

4. Aprire config/env/development.js per configurare l'ambiente di sviluppo e aggiungere l'oggetto `models` seguente:

        models: {
            connection: 'mySql',
            migrate: 'alter'
        },

    `migrate: 'alter'` consente di usare le funzionalità di migrazione di database per creare e aggiornare facilmente le tabelle di database nel database MySQL. Per l'ambiente di produzione di Azure si usa tuttavia `migrate: 'safe'`, perché Sails.js non consente di usare `migrate: 'alter'` in un ambiente di produzione. Vedere la  [documentazione di Sails.js](http://sailsjs.org/documentation/concepts/models-and-orm/model-settings).

4. Dal terminale eseguire [generate](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate) per generare un'[API di progetto](http://sailsjs.org/documentation/concepts/blueprints) (blueprint) Sails.js come di consueto, quindi eseguire `sails lift` per creare il database con la migrazione del database Sails.js. Ad esempio:

         sails generate api mywidget
         sails lift

    Il modello `mywidget` generato da questo comando è vuoto, ma è possibile usarlo per mostrare che la connettività del database è disponibile.
    Quando si esegue `sails lift`, vengono create le tabelle mancanti per i modelli usati dall'app.

6. Accedere all'API di progetto (blueprint) creata nel browser. Ad esempio:

        http://localhost:1337/mywidget/create
    
    L'API deve restituire all'utente la voce creata nella finestra del browser, per indicare che il database è stato creato correttamente.

        {"id":1,"createdAt":"2016-09-23T13:32:00.000Z","updatedAt":"2016-09-23T13:32:00.000Z"}

5. Effettuare quindi il push delle modifiche in Azure e passare all'app per verificare che funzioni ancora correttamente.

        git add .
        git commit -m "<your commit message>"
        git push azure master
        azure site browse

6. Accedere all'API di progetto dell'app Web di Azure. ad esempio:

        http://<appname>.azurewebsites.net/mywidget/create

    Se l'API restituisce un'altra voce nuova, l'app Web di Azure sta comunicando con il database MySQL.

## <a name="more-resources"></a>Altre risorse

- [Introduzione alle app Web Node.js nel servizio app di Azure](app-service-web-nodejs-get-started.md)
- [Utilizzo di moduli Node.js con le applicazioni Azure](../nodejs-use-node-modules-azure-apps.md)



<!--HONumber=Oct16_HO2-->


