<properties
	pageTitle="Eseguire l'aggiornamento da Servizi mobili a Servizio app di Azure - Node.js"
	description="Informazioni su come eseguire facilmente l'aggiornamento dell'applicazione di Servizi mobili a un'app per dispositivi mobili del servizio app"
	services="app-service\mobile"
	documentationCenter=""
	authors="christopheranderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile"
	ms.devlang="node"
	ms.topic="article"
	ms.date="12/02/2015"
	ms.author="chrande"/>

# Eseguire l'aggiornamento del servizio mobile di Azure Node.js esistente al servizio app

Le app per dispositivi mobili del servizio app offrono un nuovo modo per creare applicazioni per dispositivi mobili usando Microsoft Azure. Per altre informazioni, vedere [Informazioni sulle app per dispositivi mobili].

Questo argomento descrive come eseguire l'aggiornamento di un'applicazione back-end Node.js esistente da Servizi mobili di Azure a una nuova app per dispositivi mobili del servizio app. Durante l'aggiornamento, l'applicazione Servizi mobili esistente può continuare a funzionare.

Quando si esegue l'aggiornamento di un back-end per dispositivi mobili a Servizio app di Azure, questo ha accesso a tutte le funzionalità del servizio app e la fatturazione viene eseguita in base ai [prezzi del servizio app], non a quelli di Servizi mobili.

## Eseguire la migrazione o aggiornare

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP]Prima di procedere a un aggiornamento, è consigliabile [eseguire una migrazione](app-service-mobile-migrating-from-mobile-services.md). In questo modo, è possibile inserire entrambe le versioni dell'applicazione nello stesso piano del servizio app, senza costi aggiuntivi.

### Miglioramenti apportati all'SDK del server Node.js di App per dispositivi mobili

L'aggiornamento alla nuova versione di [Mobile Apps SDK](https://www.npmjs.com/package/azure-mobile-apps) fornisce numerosi miglioramenti, tra cui:

- Nuova versione di Node SDK leggera e progettata per adattarsi alle nuove versioni di Node man mano che vengono rilasciate, perché basata sul [framework di Express](http://expressjs.com/en/index.html). È possibile personalizzare il comportamento dell'applicazione con il middleware di Express.

- Miglioramenti significativi delle prestazioni rispetto a Mobile Services SDK.

- Possibilità di ospitare un sito Web con il back-end per dispositivi mobili e di aggiungere facilmente Azure Mobile SDK a qualsiasi applicazione expressv4 esistente.

- Creato per lo sviluppo multipiattaforma e locale, Mobile Apps SDK può essere sviluppato ed eseguito in locale su piattaforme Windows, Linux e OSX. L'uso di tecniche di sviluppo comuni di Node, ad esempio l'esecuzione di test [Mocha](https://mochajs.org/) prima della distribuzione, è ora più semplice.

- Possibilità di usare Redis con moduli nativi come [hiredis](https://www.npmjs.com/package/hiredis). Inoltre, poiché il servizio app installa i pacchetti npm automaticamente durante la distribuzione, non è necessario includere i file binari nei pacchetti di distribuzione.

## <a name="overview"></a>Panoramica di base sull'aggiornamento

A differenza di quanto avviene con .NET Mobile Apps SDK, l'aggiornamento di un back-end di Node da Servizi mobili ad App per dispositivi mobili non è semplice come la sostituzione dei pacchetti. L'utente, non Azure, è ora proprietario dell'intero gruppo di applicazioni, pertanto è necessario creare un'app Express di base che ospiti il back-end per dispositivi mobili. Per i controller dell'API e della tabella, i concetti sono simili, ma è ora necessario esportare gli oggetti tabella e le API della funzione sono state in qualche modo modificate. Questo articolo illustra in dettaglio le strategie di base dell'aggiornamento, ma prima di eseguire la migrazione, è opportuno leggere l'articolo relativo all'[uso del back-end di Node](app-service-mobile-node-backend-how-to-use-server-sdk.md).

>[AZURE.TIP]Si consiglia di leggere e comprendere integralmente il resto di questo argomento prima di avviare un aggiornamento. Prendere nota delle funzionalità usate che sono indicate di seguito.

Gli SDK del client di Servizi mobili **non** sono compatibili con il nuovo SDK del server di App per dispositivi mobili. Per garantire la continuità del servizio per l'app, non devono essere pubblicate modifiche in un sito che usa client pubblicati. È invece necessario creare una nuova app per dispositivi mobili che agisce da duplicato. È possibile inserire questa applicazione nello stesso piano di servizio app per evitare di sostenere costi finanziari aggiuntivi.

Si avranno quindi due versioni dell'applicazione: una che rimane invariata e usa le app pubblicate e l'altra che è possibile aggiornare e usare come destinazione con una nuova versione del client. È possibile spostare e testare il codice in base alle esigenze, ma è necessario assicurarsi che tutte le correzioni di bug apportate vengano applicate a entrambe le versioni. Quando si ritiene di avere aggiornato alla versione più recente il numero desiderato di app client, è possibile eliminare l'app migrata originale, se lo si desidera. Questa operazione non comporta l'applicazione di costi aggiuntivi, se l'app è ospitata nello stesso piano di servizio app dell'app per dispositivi mobili.

La struttura completa del processo di aggiornamento è la seguente:

1. Creare una nuova app per dispositivi mobili
2. Aggiornare il progetto per l'uso dei nuovi SDK del server
3. Rilasciare una nuova versione dell'applicazione client
4. (Facoltativo) Eliminare l'app del servizio mobile originale migrata

## <a name="mobile-app-version"></a> Avvio dell'aggiornamento
Il primo passaggio per l'aggiornamento prevede la creazione della risorsa dell'app per dispositivi mobili che ospiterà la nuova versione dell'applicazione. Se è già stata eseguita la migrazione di un servizio mobile esistente, è consigliabile creare questa versione nello stesso piano di hosting. Aprire il [portale di Azure] e passare all'applicazione migrata. Prendere nota del piano di servizio app in cui è in esecuzione.

### Creazione di una seconda istanza dell'applicazione
Successivamente, creare una seconda istanza dell'applicazione. Quando viene richiesto di selezionare il piano di servizio app o "piano di hosting", scegliere il piano dell'applicazione migrata.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Potrebbe essere utile usare lo stesso database e hub di notifica usati in Servizi mobili. È possibile copiare questi valori aprendo il [portale di Azure] e passando all'applicazione originale, quindi facendo clic su **Impostazioni** > **Impostazioni applicazione**. In **Stringhe di connessione** copiare `MS_NotificationHubConnectionString` e `MS_TableConnectionString`. Passare al nuovo sito di aggiornamento e incollare i valori, sovrascrivendo gli eventuali valori esistenti. Ripetere questa procedura per tutte le altre impostazioni dell'applicazione necessarie per l'app. Se non si usa un servizio migrato, è possibile leggere le stringhe di connessione e le impostazioni dell'app dalla scheda **Configura** della sezione Servizi mobili del [portale di Azure].

### Creare un back-end dell'app per dispositivi mobili di base con Node

Ogni back-end Node.js per App per dispositivi mobili del servizio app di Azure viene avviato come applicazione ExpressJS. È possibile creare un'applicazione [Express](http://expressjs.com/en/index.html) di base seguendo questa procedura:

1. Creare una nuova directory per il progetto in una finestra di comando o di PowerShell.

        mkdir basicapp

2. Eseguire npm init per inizializzare la struttura del pacchetto.

        cd basicapp
        npm init

    Il comando npm init porrà una serie di domande per inizializzare il progetto. L'output di esempio è riportato di seguito

    ![Output di init npm][0]

3. Installare le librerie express e azure-mobile-apps dal repository npm.

        npm install --save express azure-mobile-apps

4. Creare un file app.js per implementare il server per dispositivi mobili di base.

        var express = require('express'),
            azureMobileApps = require('azure-mobile-apps');

        var app = express(),
            mobile = azureMobileApps();

        // Important all tables in the 'tables' directory
        mobile.tables.import('./tables');
        mobile.api.import('./api');

        // Provide initialization of any tables that are statically defined
        mobile.tables.initialize().then(function () {
           // Add the mobile API so it is accessible as a Web API
           app.use(mobile);

           // Start listening on HTTP
           app.listen(process.env.PORT || 3000);
           console.log('Now listening on ' + (process.env.PORT || 3000)));
        });


## Aggiornamento del progetto server

App per dispositivi mobili fornisce una nuova versione dell'[SDK del server dell'app per dispositivi mobili] che offre molte delle funzionalità del runtime di Servizi mobili, con la differenza che ora l'utente è proprietario del runtime completo. App per dispositivi mobili non impone all'utente una versione di Node o eventuali aggiornamenti del codice. Se è stata seguita la procedura descritta sopra, si dispone di una versione di base del runtime per dispositivi mobili di Node. È ora possibile iniziare a spostare le tabelle e la logica dell'API dal servizio mobile all'app per dispositivi mobili, personalizzando la configurazione del server, abilitando il push, configurando l'autenticazione e altro ancora.

### Configurazione di base

Il server ha numerose impostazioni di configurazione, ma un'ampia gamma di valori predefiniti in grado di semplificare le operazioni iniziali. Molte delle impostazioni vengono configurate automaticamente, nel [portale di Azure], tramite i menu delle impostazioni di **dati**, **autenticazione/autorizzazione** e **push**. Per lo sviluppo locale, se si desidera usare le impostazioni di dati, autenticazione e push, può essere necessario configurare l'ambiente di sviluppo locale.

È possibile definire la configurazione del server tramite le variabili di ambiente che possono essere specificate mediante le impostazioni dell'app nel back-end dell'app per dispositivi mobili.

È inoltre possibile personalizzare Mobile Apps SDK passando un [oggetto di configurazione](http://azure.github.io/azure-mobile-apps-node/global.html#configuration) all'inizializzatore o [creando un file denominato azureMobile.js](app-service-mobile-node-backend-how-to-use-server-sdk/#howto-config-localdev) nella radice del progetto.

### Uso di dati e tabelle

L'SDK dispone di un provider di dati in memoria per una rapida e semplice esecuzione di attività iniziali. È necessario passare all'uso di un database SQL nella fase iniziale, in quanto il provider in memoria perde tutti i dati al riavvio e non rimane coerente tra più istanze.

Per iniziare a spostare la logica di business dal servizio mobile ad App per dispositivi mobili, è innanzitutto opportuno creare un file con il nome della tabella (seguito da '.js') nella directory `./tables`. È possibile visualizzare un esempio completo di una tabella dell'app per dispositivi mobili su [GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/todo/tables/TodoItem.js). La versione più semplice è:

    var azureMobileApps = require('azure-mobile-apps');

    // Create a new table definition
    var table = azureMobileApps.table();

    module.exports = table;

Per avviare il trasferimento di alcune logiche, per ogni tabella `<tablename>.<operation>.js`, è necessaria una funzione per la tabella. Aggiungere, a titolo di esempio, una funzione di lettura.

In un servizio mobile con una tabella TodoItem e un'operazione di lettura che filtra gli elementi in base a ID utente, simile alla seguente:

  function(query, user, request) { query.where({ userId: user.userId}); request.execute(); }

La funzione che viene aggiunta al codice della tabella di App per dispositivi mobili di Azure sarà simile alla seguente:

    table.read(function (context) {
        context.query.where({ userId: context.user.id });
        return context.execute();
    });

Esaminando il codice, è possibile riconoscere la maggior parte dei parametri della funzione da

### CORS

È possibile abilitare la Condivisione risorse tra le origini (CORS) tramite un'[impostazione di configurazione](http://azure.github.io/azure-mobile-apps-node/global.html#corsConfiguration) specifica nell'SDK.

Le principali aree problematiche per l'uso di CORS sono rappresentate dal fatto che le intestazioni `eTag` e `Location` devono essere consentite per il corretto funzionamento degli SDK del client.

### Notifiche push

Ad Azure Notification Hubs SDK sono stati apportati alcuni aggiornamenti significativi a partire da Servizi mobili, è quindi possibile che alcune firme di funzione di hub di notifica siano diverse. In caso contrario, la funzionalità è simile a quella di Servizi mobili. Azure Mobile SDK esegue il provisioning di un'istanza di hub di notifica se è presente l'impostazione dell'app per gli hub di notifica e la espone su `context.push`. In [GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/push-on-insert/tables/TodoItem.js) è disponibile un esempio con la relativa sezione illustrata di seguito:

    table.insert(function (context) {
        // For details of the Notification Hubs JavaScript SDK,
        // see https://azure.microsoft.com/it-IT/documentation/articles/notification-hubs-nodejs-how-to-use-notification-hubs/
        logger.silly('Running TodoItem.insert');

        // This push uses a template mechanism, so we need a template/
        var payload = '<toast><visual><binding template="Toast01"><text id="1">INSERT</text></binding></visual></toast>';

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    context.push.wns.send(null, payload, 'wns/toast', function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.silly('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
    });


### Processi pianificati
I processi pianificati non vengono creati in App per dispositivi mobili, è quindi necessario eseguire singolarmente l'aggiornamento di eventuali processi presenti nel back-end di Servizi mobili. Una possibilità consiste nel creare un [processo Web] pianificato nel sito del codice dell'app per dispositivi mobili. È inoltre possibile impostare un'API che contiene il codice del processo e configurare l'[Utilità di pianificazione di Azure] per accedere a tale endpoint in base alla pianificazione prevista.

## <a name="authentication"></a>Considerazioni sull'autenticazione

I componenti di autenticazione di Servizi mobili sono ora stati spostati nella funzionalità di autenticazione/autorizzazione del servizio app. Per informazioni sull'abilitazione di questa funzionalità per il sito, vedere l'argomento [Aggiungere l'autenticazione all'app per dispositivi mobili](app-service-mobile-ios-get-started-users.md).

Per alcuni provider, come AAD, Facebook e Google, dovrebbe essere possibile sfruttare la registrazione esistente dall'applicazione di copia. È sufficiente passare al portale del provider di identità e aggiungere un nuovo URL di reindirizzamento alla registrazione. Configurare quindi l'autenticazione/autorizzazione del servizio app con il segreto e l'ID client.

### Autorizzazione dell'azione del controller e identità dell'utente

Per limitare l'accesso alla tabella, è possibile impostare quest'ultimo a livello di tabella con `table.access = 'authenticated';`. È possibile visualizzare un esempio completo in [GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/authentication/tables/TodoItem.js).

È possibile accedere alle informazioni sull'identità utente tramite il metodo `user.getIdentity` descritto [qui](http://azure.github.io/azure-mobile-apps-node/module-azure-mobile-apps_auth_user.html#~getIdentity).

## <a name="updating-clients"></a>Aggiornamento dei client
Dopo aver reso operativo un back-end dell'app per dispositivi mobili, è possibile lavorare su una nuova versione dell'applicazione client che ne faccia uso. App per dispositivi mobili include anche una nuova versione degli SDK del client e, come nel caso dell'aggiornamento del server descritto in precedenza, sarà necessario rimuovere tutti i riferimenti agli SDK di Servizi mobili prima di installare le versioni di App per dispositivi mobili.

Una delle principali modifiche tra le versioni è rappresentata dai costruttori che non richiedono più una chiave applicazione. Ora è sufficiente passare l'URL dell'app per dispositivi mobili. Ad esempio, nei client .NET, il costruttore `MobileServiceClient` ora è:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Per altre informazioni sull'installazione dei nuovi SDK e sull'uso della nuova struttura, visitare i collegamenti seguenti:

- [iOS versione 3.0.0 o successiva](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) versione 2.0.0 o successiva](app-service-mobile-dotnet-how-to-use-client-library.md)

Se l'applicazione usa le notifiche push, prendere nota delle specifiche istruzioni di registrazione per ogni piattaforma, perché sono state apportate alcune modifiche anche in questo ambito.

Quando la nuova versione del client è pronta, provarla con il progetto server aggiornato. Dopo averne verificato il funzionamento, è possibile rilasciare una nuova versione dell'applicazione per i clienti. Infine, quando i clienti avranno ricevuto gli aggiornamenti, sarà possibile eliminare la versione di Servizi mobili dell'app. A questo punto, sono stati aggiornati completamente a un'App per dispositivi mobili del servizio app utilizzando l’SDK più recente del server di App per dispositivi mobili.

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png

<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[Informazioni sulle app per dispositivi mobili]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /it-IT/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[SDK del server dell'app per dispositivi mobili]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Utilità di pianificazione di Azure]: /it-IT/documentation/services/scheduler/
[processo Web]: ../app-service-web/websites-webjobs-resources.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[prezzi del servizio app]: https://azure.microsoft.com/it-IT/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md

[portale di Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/it-IT/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/it-IT/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

<!---HONumber=AcomDC_1210_2015-->