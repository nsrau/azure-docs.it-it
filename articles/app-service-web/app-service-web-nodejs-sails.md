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
	ms.date="07/01/2016"
	ms.author="cephalin"/>

# Distribuire un'app Web Sails.js nel servizio app di Azure

Questa esercitazione illustra come distribuire un'app Sails.js nel servizio app di Azure. Durante il processo vengono offerte informazioni generali sulla configurazione dell'app Node.js da eseguire nel servizio app.

## Prerequisiti

- Node.js. I file binari per l'installazione sono disponibili [qui](https://nodejs.org/).
- Sails.js. Le istruzioni per l'installazione sono disponibili [qui](http://sailsjs.org/get-started).
- Conoscenza pratica di Sails.js. Questa esercitazione non fornisce informazioni sulla risoluzione dei problemi generali di esecuzione di Sail.js.
- Git. I file binari per l'installazione sono disponibili [qui](http://www.git-scm.com/downloads).
- Interfaccia della riga di comando di Azure. Le istruzioni per l'installazione sono disponibili [qui](../xplat-cli-install.md).
- Un account Microsoft Azure. Se non si ha un account, è possibile [iscriversi per ottenere una versione di valutazione gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) oppure [attivare i benefici della sottoscrizione Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Per provare il servizio app di Azure prima di iscriversi per ottenere un account Azure, vedere [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751). In questa pagina è possibile creare immediatamente un'app iniziale temporanea nel servizio app. Non è richiesta una carta di credito né occorre impegnarsi in alcun modo.

## Passaggio 1: Creare un'app Sails.js nell'ambiente di sviluppo

Innanzitutto, creare rapidamente un'app Sails.js predefinita attenendosi alla procedura seguente:

1. Aprire il terminale della riga di comando desiderato ed eseguire `CD` in una directory di lavoro.

2. Creare una nuova app Sails.js ed eseguirla:

        sails new <appname>
        cd <appname>
        sails lift

    Assicurarsi che è possibile passare alla home page predefinita in http://localhost:1377.

## Passaggio 2: Creare la risorsa dell'app del servizio app in Azure

Creare la risorsa dell'app del servizio app. In seguito, l'app Sails.js verrà distribuita nella risorsa.

1. Nello stesso terminale accedere ad Azure nel modo seguente:

        azure login

    Seguire le istruzioni per continuare l'accesso in un browser usando un account con la sottoscrizione di Azure.

2. Assicurarsi di essere ancora nella directory radice del progetto Sails.js. Creare la risorsa dell'app del servizio app in Azure con un nome app univoco con il comando successivo. L'URL dell'app Web sarà http://&lt;appname>.azurewebsites.net.

        azure site create --git <appname>

    Seguire le istruzioni e selezionare un'area di Azure in cui eseguire la distribuzione. Se non sono mai state configurate credenziali di distribuzione Git/FTP per la sottoscrizione di Azure, verrà chiesto di crearle.

    Dopo aver creato la risorsa dell'app del servizio app:

    - L'app Sails.js viene inizializzata da Git,
    - Il repository inizializzato Git locale viene connesso alla nuova app del servizio app come Git remoto denominato "azure" e
    - Il file iisnode viene creato nella directory radice. Questo file consente di configurare [iisnode](https://github.com/tjanczuk/iisnode) usato dal servizio app per l'esecuzione delle app Node.js.

## Passaggio 3: Configurare e distribuire l'app Sails.js

 L'uso di una app Sails.js nel servizio app prevede tre passaggi principali:

 - Configurare l'app per l'esecuzione nel servizio app
 - Distribuirla nel servizio app
 - Leggere i log stderr e stdout per risolvere eventuali problemi di distribuzione

A tale scopo, seguire questa procedura:

1. Aprire il nuovo file iisnode.yml nella directory radice e aggiungere le due righe seguenti:

        loggingEnabled: true
        logDirectory: iisnode

    La registrazione è ora abilitata per iisnode. Per altre informazioni sul funzionamento, vedere [Ottenere i log stdout e stderr da iisnode](app-service-web-nodejs-get-started.md#iisnodelog).

2. Aprire config/env/production.js per configurare l'ambiente di produzione e impostare `port` e `hookTimeout`:

        module.exports = {

            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,

            ...
        };

    La documentazione di queste impostazioni di configurazione è disponibile nella [Documentazione di Sails.js](http://sailsjs.org/documentation/reference/configuration/sails-config).

    Successivamente, è necessario assicurarsi che [Grunt](https://www.npmjs.com/package/grunt) sia compatibile con le unità di rete di Azure. Al momento della stesura di questo articolo, è possibile che Grunt generi l'errore ["ENOTSUP: operation not supported on socket"](https://github.com/isaacs/node-glob/issues/205) poiché attualmente usa un pacchetto [glob](https://www.npmjs.com/package/glob) obsoleto (v3.1.21) che non supporta le unità di rete. I passaggi successivi descrivono come configurare Grunt per l'uso di [glob v5.0.14 o versione successiva](https://github.com/isaacs/node-glob/commit/bf3381e90e283624fbd652835e1aefa55d45e2c7).

3. Poiché `npm install` è già stato eseguito al momento della creazione dell'app, generare npm-shrinkwrap.json nella radice del progetto:

        npm shrinkwrap

4. Aprire npm-shrinkwrap.json, individuare il codice JSON per `"grunt":` e aggiungere la dipendenza per la versione glob desiderata. Al termine, il codice JSON dovrebbe essere simile al seguente:

        "grunt": {
            "version": "0.4.5",
            "from": "grunt@0.4.5",
            "resolved": "https://registry.npmjs.org/grunt/-/grunt-0.4.5.tgz",
            "dependencies": {
                "glob": {
                    "version": "5.0.14",
                    "from": "glob@5.0.14",
                    "resolved": "https://registry.npmjs.org/glob/-/glob-5.0.14.tgz"
                }
            }
        },

5. Individuare tutti i riferimenti a glob cercando `"glob":`. Se vengono trovati riferimenti v3.1.21 o versioni precedenti, modificare il codice JSON in:

        "glob": {
            "version": "5.0.14",
            "from": "glob@5.0.14",
            "resolved": "https://registry.npmjs.org/glob/-/glob-5.0.14.tgz"
        }

6. Salvare le modifiche e testare le modifiche per assicurarsi che l'app continui a essere eseguita localmente:

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

## Risoluzione dei problemi di distribuzione

Se l'applicazione Sails.js per qualche motivo non viene avviata nel servizio app, cercare i log stderr per risolvere i problemi. Per altre informazioni, vedere [Ottenere i log stdout e stderr da iisnode](app-service-web-nodejs-sails.md#iisnodelog). Se l'applicazione è stata avviata correttamente, il log stdout visualizzerà il messaggio:

                .-..-.

    Sails              <|    .-..-.
    v0.12.1             |\
                        /|.\
                        / || \
                    ,'  |'  \
                    .-'.-==|/_--'
                    `--'-------' 
    __---___--___---___--___---___--___
    ____---___--___---___--___---___--___-__

    Server lifted in `D:\home\site\wwwroot`
    To see your app, visit http://localhost:\\.\pipe\a76e8111-663e-449d-956e-5c5deff2d304
    To shut down Sails, press <CTRL> + C at any time.

## Connettersi a un database in Azure

Per connettersi a un database in Azure, creare il database desiderato in Azure, ad esempio un database SQL di Azure, MySQL, MongoDB, Cache (Redis) di Azure e così via e usare l'[adattatore dell'archivio dati](https://github.com/balderdashy/sails#compatibility) corrispondente per la connessione. I passaggi descritti in questa sezione illustrano come connettersi a un database SQL di Azure.

1. Seguire l'esercitazione [qui](../sql-database/sql-database-get-started.md) per creare un database SQL di Azure vuoto in un nuova istanza di SQL Server. Le impostazioni del firewall predefinite consentono la connessione dei servizi di Azure al database, ad esempio del servizio app.

2. Dal terminale della riga di comando installare l'adattatore di SQL Server:

        npm install sails-sqlserver --save

    Poiché package.json è stato modificato, è necessario rigenerare npm-shrinkwrap.json. L'operazione verrà eseguita più avanti.
    
3. Eliminare la directory node\_modules/.

4. Eseguire `npm shrinkwrap`.

5. Aprire di nuovo npm-shrinkwrap.json e aggiornare le versioni del pacchetto `glob` come descritto nella sezione precedente.

    Tornare all'attività principale.
        
3. Aprire config/connections.js e aggiungere il codice JSON seguente all'elenco degli adattatori:

        sqlserver: {
            adapter: 'sails-sqlserver',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.sqlserver, 
            database: process.env.dbname,
            options: {
                encrypt: true   // use this for Azure databases
            }
        },

4. Per ogni variabile di ambiente (`process.env.*`), è necessario eseguire l'impostazione nel servizio app. A tale scopo, eseguire i comandi seguenti dal terminale:

        azure site appsetting add dbuser="<database server administrator>"
        azure site appsetting add dbpassword="<database server password>"
        azure site appsetting add sqlserver="<database server name>.database.windows.net"
        azure site appsetting add dbname="<database name>"
        
4. Aprire config/env/production.js per configurare l'ambiente di produzione e impostare `connection` e `migrate` nell'oggetto JSON `models`:

        models: {
            connection: 'sqlserver',
            migrate: 'alter'
        },

4. Dal terminale eseguire [generate](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate) per generare un'[API di progetto](http://sailsjs.org/documentation/concepts/blueprints) di Sails.js con la procedura consueta. Ad esempio:

         sails generate api mywidget
     
5. Salvare tutte le modifiche, eseguire il push delle modifiche in Azure e passare all'app per verificare che funzioni correttamente.

        git add .
        git commit -m "<your commit message>"
        git push azure master
        azure site browse

6. Accedere all'API di progetto creata nel browser. Ad esempio:

        http://<appname>.azurewebsites.net/widget/create
    
    L'API restituisce la voce creata nella finestra del browser:
    
        {"id":1,"createdAt":"2016-03-28T23:08:01.000Z","updatedAt":"2016-03-28T23:08:01.000Z"}

## Altre risorse

- [Introduzione alle app Web Node.js nel servizio app di Azure](app-service-web-nodejs-get-started.md)
- [Utilizzo di moduli Node.js con le applicazioni Azure](../nodejs-use-node-modules-azure-apps.md)

<!---HONumber=AcomDC_0706_2016-->