---
title: Distribuire un&quot;app Web Sails.js nel servizio app di Azure | Documentazione Microsoft
description: Informazioni su come distribuire un&quot;applicazione Node.js nel servizio app di Azure. Questa esercitazione illustra come distribuire un&quot;app Web Sails.js.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: wpickett
editor: 
ms.assetid: 8877ddc8-1476-45ae-9e7f-3c75917b4564
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: f595be46983bf07783b529de885d889c18fdb61a
ms.openlocfilehash: 3f27488c8c0173f695edc98da01345d14ff64698


---
# <a name="deploy-a-sailsjs-web-app-to-azure-app-service"></a>Distribuire un'app Web Sails.js nel servizio app di Azure
Questa esercitazione illustra come distribuire un'app Sails.js nel servizio app di Azure. Durante il processo vengono offerte informazioni generali sulla configurazione dell'app Node.js da eseguire nel servizio app.

Questa esercitazione illustra alcune operazioni utili, ad esempio:

* Configurare l'esecuzione di un'app Sails.js nel servizio app.
* Distribuire un'app nel servizio app dalla riga di comando.
* Leggere i log stderr e stdout per risolvere eventuali problemi di distribuzione.
* Archiviare le variabili di ambiente al di fuori del controllo del codice sorgente.
* Accedere alle variabili di ambiente di Azure dall'app.
* Connettersi a un database (MongoDB).

Avere una conoscenza pratica di Sails.js. Questa esercitazione non fornisce informazioni sulla risoluzione dei problemi generali di esecuzione di Sail.js.

## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività

È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

- [Interfaccia della riga di comando di Azure 1.0](app-service-web-nodejs-sails-cli-nodejs.md): l'interfaccia della riga di comando per i modelli di distribuzione classici e di gestione delle risorse
- [Interfaccia della riga di comando di Azure 2.0 (anteprima)](app-service-web-nodejs-sails.md): l'interfaccia della riga di comando di nuova generazione per il modello di distribuzione di gestione delle risorse

## <a name="prerequisites"></a>Prerequisiti
* [Node.JS](https://nodejs.org/)
* [Sails.js](http://sailsjs.org/get-started)
* [Git](http://www.git-scm.com/downloads)
* [Interfaccia della riga di comando di Azure](../xplat-cli-install.md)
* Un account Microsoft Azure. Se non si ha un account, è possibile [iscriversi per ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) oppure [attivare i vantaggi per i sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> È possibile [provare il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751) senza avere un account Azure. Creare un'app iniziale e provarla per un'ora, senza impegno e senza dover usare la carta di credito.
> 
> 

## <a name="step-1-create-a-sailsjs-app-locally"></a>Passaggio 1: Creare un'app Sails.js in locale
Innanzitutto, creare rapidamente un'app Sails.js predefinita nell'ambiente di sviluppo attenendosi alla procedura seguente:

1. Aprire il terminale della riga di comando desiderato ed eseguire `CD` in una directory di lavoro.
2. Creare un'app Sails.js ed eseguirla:

        sails new <appname>
        cd <appname>
        sails lift

    Assicurarsi che sia possibile passare alla home page predefinita in http://localhost:1377.

## <a name="step-2-create-the-azure-app-resource"></a>Passaggio 2: Creare la risorsa per l'app di Azure
Quindi creare la risorsa del servizio app in Azure. In seguito, l'app Sails.js verrà distribuita nella risorsa.

1. accedere ad Azure in questo modo:
2. Nel stesso terminale passare in modalità ASM e accedere ad Azure:

        azure config mode asm
        azure login

    Seguire le istruzioni per continuare l'accesso in un browser usando un account con la sottoscrizione di Azure.

3. Impostare l'utente di distribuzione per il servizio app. Si distribuirà il codice usando le credenziali in un secondo momento.
   
        azure site deployment user set --username <username> --pass <password>

3. Assicurarsi di essere ancora nella directory radice del progetto Sails.js. Creare la risorsa dell'app del servizio app in Azure con un nome app univoco con il comando successivo. L'URL dell'app Web sarà http://&lt;appname>.azurewebsites.net.

        azure site create --git <appname>

    Seguire le istruzioni e selezionare un'area di Azure in cui eseguire la distribuzione. Dopo aver creato la risorsa dell'app del servizio app:

   * L'app Sails.js viene inizializzata da Git,
   * Il repository inizializzato Git locale viene connesso alla nuova app del servizio app come Git remoto denominato "azure" e
   * Il file iisnode viene creato nella directory radice. Questo file consente di configurare [iisnode](https://github.com/tjanczuk/iisnode)usato dal servizio app per l'esecuzione delle app Node.js.

## <a name="step-3-configure-and-deploy-your-sailsjs-app"></a>Passaggio 3: Configurare e distribuire l'app Sails.js
 L'uso di una app Sails.js nel servizio app prevede tre passaggi principali:

* Configurare l'app per l'esecuzione nel servizio app
* Distribuirla nel servizio app
* Leggere i log stderr e stdout per risolvere eventuali problemi di distribuzione

A tale scopo, seguire questa procedura:

1. Aprire il nuovo file iisnode.yml nella directory radice e aggiungere le due righe seguenti:

        loggingEnabled: true
        logDirectory: iisnode

    La registrazione è ora abilitata per il server [iisnode](https://github.com/tjanczuk/iisnode) usato dal Servizio app di Azure per eseguire le app Node.js. 
    Per altre informazioni sul funzionamento, vedere  [Ottenere i log stdout e stderr da iisnode](app-service-web-nodejs-get-started.md#iisnodelog).
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

4. In package.json aggiungere la proprietà `engines` seguente per impostare la versione di Node.js desiderata.

        "engines": {
            "node": "6.9.1"
        },
5. Salvare le modifiche e testarle per assicurarsi che l'app sia ancora in esecuzione in locale. A tale scopo, eliminare la cartella `node_modules` e quindi eseguire:

        npm install
        sails lift
6. Usare Git per distribuire l'app in Azure:

        git add .
        git commit -m "<your commit message>"
        git push azure master
7. Infine, avviare l'app Azure attiva nel browser:

        azure site browse

    Verrà visualizzata la stessa home page di Sails.js.

    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## <a name="troubleshoot-your-deployment"></a>Risoluzione dei problemi di distribuzione
Se l'applicazione Sails.js per qualche motivo non viene avviata nel servizio app, cercare i log stderr per risolvere i problemi.
Per altre informazioni, vedere [Ottenere i log stdout e stderr da iisnode](app-service-web-nodejs-get-started.md#get-stdout-and-stderr-logs-from-iisnode).
Se l'applicazione è stata avviata correttamente, il log stdout visualizzerà il messaggio:

                   .-..-.
    
       Sails              <|    .-..-.
       v0.12.11            |\
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
Per connettersi a un database in Azure, creare il database desiderato in Azure, ad esempio un database SQL di Azure, MySQL, MongoDB, Cache (Redis) di Azure e così via e usare l' [adattatore dell'archivio dati](https://github.com/balderdashy/sails#compatibility) corrispondente per stabilire la connessione. I passaggi di questa sezione illustrano come connettersi a MongoDB usando un database [Azure DocumentDB](../documentdb/documentdb-protocol-mongodb.md), che può supportare le connessioni client MongoDB.

1. [Creare un account DocumentDB con supporto del protocollo per MongoDB](../documentdb/documentdb-create-mongodb-account.md).
2. [Creare una raccolta e un database DocumentDB](../documentdb/documentdb-create-collection.md). Il nome della raccolta non è importante, ma il nome del database è necessario quando ci si connette da Sails.js.
3. [Trovare le informazioni di connessione per il database DocumentDB](../documentdb/documentdb-connect-mongodb-account.md#a-idgetcustomconnectiona-get-the-mongodb-connection-string-to-customize).
2. Dal terminale della riga di comando installare l'adattatore di MongoDB:

        npm install sails-mongo --save

3. Aprire config/connections.js e aggiungere l'oggetto connessione seguente all'elenco:

        docDbMongo: {
            adapter: 'sails-mongo',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.dbhost,
            port: process.env.dbport,
            database: process.env.dbname,
            ssl: true
        },

    > [!NOTE] 
    > L'opzione `ssl: true` è importante perché [è necessaria per Azure DocumentDB](../documentdb/documentdb-connect-mongodb-account.md#connection-string-requirements). 
    >
    >

4. Per ogni variabile di ambiente (`process.env.*`), è necessario eseguirne l'impostazione nel servizio app. A tale scopo, eseguire i comandi seguenti dal terminale. Usare le informazioni di connessione per il database DocumentDB.

        azure site appsetting add dbuser="<database user>"
        azure site appsetting add dbpassword="<database password>"
        azure site appsetting add dbhost="<database hostname>"
        azure site appsetting add dbport="<database port>"
        azure site appsetting add dbname="<database name>"

    L'inserimento delle proprie impostazioni nelle impostazioni dell'app Azure mantiene i dati sensibili al di fuori del controllo del codice sorgente (Git). Si configurerà quindi l'ambiente di sviluppo per l'uso delle stesse informazioni di connessione.
5. Aprire config/local.js e aggiungere l'oggetto connessione seguente:

        connections: {
            docDbMongo: {
                user: "<database user>",
                password: "<database password>",
                host: "<database hostname>",
                database: "<database name>",
                ssl: true
            },
        },

    Questa configurazione sostituisce le impostazioni presenti nel file config/connections.js per l'ambiente locale. Questo file è escluso dal file con estensione gitignore predefinito del progetto, quindi non verrà archiviato in Git. A questo punto, è possibile connettersi al database DocumentDB (MongoDB) dall'app Web di Azure e dall'ambiente di sviluppo locale.
6. Aprire config/env/production.js per configurare l'ambiente di produzione e aggiungere l'oggetto `models` seguente:

        models: {
            connection: 'docDbMongo',
            migrate: 'safe'
        },
7. Aprire config/env/development.js per configurare l'ambiente di sviluppo e aggiungere l'oggetto `models` seguente:

        models: {
            connection: 'docDbMongo',
            migrate: 'alter'
        },

    `migrate: 'alter'` consente di usare le funzionalità di migrazione di database per creare e aggiornare facilmente le raccolte o le tabelle di database. Per l'ambiente di produzione di Azure si usa tuttavia `migrate: 'safe'`, perché Sails.js non consente di usare `migrate: 'alter'` in un ambiente di produzione. Vedere la  [documentazione di Sails.js](http://sailsjs.org/documentation/concepts/models-and-orm/model-settings).
8. Dal terminale eseguire [generate](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate) per generare un'[API di progetto](http://sailsjs.org/documentation/concepts/blueprints) (blueprint) Sails.js come di consueto, quindi eseguire `sails lift` per creare il database con la migrazione del database Sails.js. Ad esempio:

         sails generate api mywidget
         sails lift

    Il modello `mywidget` generato da questo comando è vuoto, ma è possibile usarlo per mostrare che la connettività del database è disponibile.
    Quando si esegue `sails lift`, vengono create le raccolte o le tabelle mancanti per i modelli usati dall'app.
9. Accedere all'API di progetto (blueprint) creata nel browser. Ad esempio:

        http://localhost:1337/mywidget/create

    L'API deve restituire all'utente la voce creata nella finestra del browser, per indicare che il database è stato creato correttamente.

        {"id":1,"createdAt":"2016-09-23T13:32:00.000Z","updatedAt":"2016-09-23T13:32:00.000Z"}
10. Effettuare quindi il push delle modifiche in Azure e passare all'app per verificare che funzioni ancora correttamente.

         git add .
         git commit -m "<your commit message>"
         git push azure master
         azure site browse
11. Accedere all'API di progetto dell'app Web di Azure. Ad esempio:

         http://<appname>.azurewebsites.net/mywidget/create

     Se l'API restituisce un'altra voce nuova, l'app Web di Azure sta comunicando con il database DocumentDB (MongoDB).

## <a name="more-resources"></a>Altre risorse
* [Introduzione alle app Web Node.js nel servizio app di Azure](app-service-web-nodejs-get-started.md)
* [Utilizzo di moduli Node.js con le applicazioni Azure](../nodejs-use-node-modules-azure-apps.md)



<!--HONumber=Dec16_HO3-->


