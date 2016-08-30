<properties
	pageTitle="Eseguire l'aggiornamento da Servizi mobili a Servizio app di Azure - Node.js"
	description="Informazioni su come eseguire facilmente l'aggiornamento dell'applicazione Servizi mobili a un'app per dispositivi mobili del servizio app"
	services="app-service\mobile"
	documentationCenter=""
	authors="adrianhall"
	manager="ggailey"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile"
	ms.devlang="node"
	ms.topic="article"
	ms.date="08/11/2016"
	ms.author="adrianha"/>

# Aggiornare il servizio mobile Node.js di Azure esistente al servizio app

Le app per dispositivi mobili del servizio app offrono un nuovo modo per creare applicazioni per dispositivi mobili usando Microsoft Azure. Per altre informazioni, vedere [Informazioni sulle app per dispositivi mobili].

Questo argomento descrive come eseguire l'aggiornamento di un'applicazione back-end Node.js esistente da Servizi mobili di Azure a una nuova app per dispositivi mobili del servizio app. Durante una migrazione, l'applicazione Servizi mobili esistente può continuare a funzionare. Se si deve aggiornare un'applicazione back-end Node.js, vedere [Aggiornare il servizio mobile .NET di Azure esistente al servizio app](./app-service-mobile-net-upgrading-from-mobile-services.md).

Quando si esegue l'aggiornamento di un back-end per dispositivi mobili a Servizio app di Azure, questo ha accesso a tutte le funzionalità del servizio app e la fatturazione viene eseguita in base ai [prezzi del servizio app], non a quelli di Servizi mobili.

## Eseguire la migrazione o aggiornare

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP] Prima di procedere a un aggiornamento, è consigliabile [eseguire una migrazione](app-service-mobile-migrating-from-mobile-services.md). In questo modo, è possibile inserire entrambe le versioni dell'applicazione nello stesso piano del servizio app, senza costi aggiuntivi.

### Miglioramenti apportati all'SDK del server Node.js di App per dispositivi mobili

L'aggiornamento alla nuova versione di [Mobile Apps SDK](https://www.npmjs.com/package/azure-mobile-apps) fornisce numerosi miglioramenti, tra cui:

- Nuova versione di Node SDK leggera e progettata per adattarsi alle nuove versioni di Node man mano che vengono rilasciate, perché basata sul [framework di Express](http://expressjs.com/en/index.html). È possibile personalizzare il comportamento dell'applicazione con il middleware di Express.

- Miglioramenti significativi delle prestazioni rispetto a Mobile Services SDK.

- Possibilità di ospitare un sito Web con il back-end per dispositivi mobili e di aggiungere facilmente Azure Mobile SDK a qualsiasi applicazione express.v4 esistente.

- Creato per lo sviluppo multipiattaforma e locale, Mobile Apps SDK può essere sviluppato ed eseguito in locale su piattaforme Windows, Linux e OSX. L'uso di tecniche di sviluppo comuni di Node, ad esempio l'esecuzione di test [Mocha](https://mochajs.org/) prima della distribuzione, è ora più semplice.

## <a name="overview"></a>Panoramica di base sull'aggiornamento

Per agevolare l'aggiornamento di un back-end Node.js, in Servizio app di Azure è stato incluso un pacchetto per la compatibilità. Al termine dell'aggiornamento sarà disponibile un nuovo sito che potrà essere distribuito in un nuovo sito del servizio app.

Gli SDK del client di Servizi mobili **non** sono compatibili con il nuovo SDK del server di App per dispositivi mobili. Per garantire la continuità del servizio per l'app, non devono essere pubblicate modifiche in un sito che usa client pubblicati. È invece necessario creare una nuova app per dispositivi mobili che agisce da duplicato. È possibile inserire questa applicazione nello stesso piano di servizio app per evitare di sostenere costi finanziari aggiuntivi.

Si avranno quindi due versioni dell'applicazione: una che rimane invariata e viene usata dalle app pubblicate in condizioni normali, l'altra che può essere aggiornata e integrata con una nuova versione del client. È possibile spostare e testare il codice in base alle esigenze, ma è necessario assicurarsi che tutte le correzioni di bug apportate vengano applicate a entrambe le versioni. Quando si ritiene di avere aggiornato alla versione più recente il numero desiderato di app client, è possibile eliminare l'app migrata originale, se lo si desidera. Questa operazione non comporta l'applicazione di costi aggiuntivi, se l'app è ospitata nello stesso piano di servizio app dell'app per dispositivi mobili.

La struttura completa del processo di aggiornamento è la seguente:

1. Scaricare il servizio mobile di Azure esistente (migrato).
2. Convertire il progetto in un'app per dispositivi mobili di Azure usando il pacchetto per la compatibilità.
3. Correggere le eventuali differenze (ad esempio nelle impostazioni di autenticazione).
4. Distribuire il progetto dell'app per dispositivi mobili convertito in un nuovo servizio app.
4. Rilasciare una nuova versione dell'applicazione client che usa la nuova app per dispositivi mobili.
5. (Facoltativo) Eliminare l'app del servizio mobile originale migrata.

L'eliminazione può essere eseguita quando non è presente traffico nel servizio mobile originale migrato.

## <a name="install-npm-package"></a>Installare i prerequisiti

È consigliabile installare Node nel computer locale. Installare anche il pacchetto per la compatibilità. Al termine dell'installazione di Node, è possibile eseguire il comando seguente da un nuovo prompt dei comandi o di PowerShell:

```npm i -g azure-mobile-apps-compatibility```

## <a name="obtain-ams-scripts"></a> Ottenere gli script di Servizi mobili di Azure

- Accedere al [Portale di Azure].
- Usare **Tutte le risorse** o **Servizi app** per trovare il sito di Servizi mobili.
- All'interno del sito fare clic su **Strumenti** -> **Kudu** -> **Vai** per aprire il sito Kudu.
- Fare clic su **Debug Console** (Console di debug) -> **PowerShell** per aprire la console di debug.
- Passare a `site/wwwroot/App_Data/config` facendo clic su una directory alla volta.
- Fare clic sull'icona per il download accanto alla directory `scripts`.

Verranno così scaricati gli script in formato ZIP. Creare una nuova directory nel computer locale e decomprimere il file `scripts.ZIP` nella directory. Verrà così creata una directory `scripts`.

## <a name="scaffold-app"></a> Eseguire lo scaffolding del nuovo back-end delle app per dispositivi mobili di Azure

Eseguire il comando seguente dalla directory contenente la directory scripts:

```scaffold-mobile-app scripts out```

Verrà così creato un back-end sottoposto a scaffolding delle app per dispositivi mobili di Azure nella directory `out`. Nonostante non sia obbligatorio, è opportuno archiviare la directory `out` nel repository di codice sorgente preferito.

## <a name="deploy-ama-app"></a> Distribuire il back-end delle app per dispositivi mobili

Durante la distribuzione, è necessario eseguire le operazioni seguenti:

1. Creare una nuova app per dispositivi mobili nel [portale di Azure].
2. Eseguire lo script `createViews.sql` sul proprio database connesso.
3. Collegare al nuovo servizio app il database collegato al servizio mobile.
4. Collegare tutte le altre risorse (ad esempio, Hub di notifica) al nuovo servizio app.
5. Distribuire il codice generato nel nuovo sito.

### Creare una nuova app per dispositivi mobili

1. Accedere al [portale di Azure].

2. Fare clic su **+NUOVO** > **Web e dispositivi mobili** > **App per dispositivi mobili** e quindi specificare un nome per il back-end dell'app per dispositivi mobili.

3. Per **Gruppo di risorse** selezionare un gruppo di risorse esistente o crearne uno nuovo usando lo stesso nome dell'app.
 
	È possibile selezionare un altro piano del servizio app o crearne uno nuovo. Per altre informazioni sui piani di servizio app e su come creare un nuovo piano in un piano tariffario diverso e nella località preferita, vedere [Panoramica approfondita dei piani di servizio app di Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

4. Per **Piano di Servizio app** viene selezionato il piano predefinito nel [livello Standard](https://azure.microsoft.com/pricing/details/app-service/). È anche possibile selezionare un piano diverso oppure [crearne uno nuovo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). Le impostazioni del piano di servizio app determinano [località, funzionalità, costo e risorse di calcolo](https://azure.microsoft.com/pricing/details/app-service/) associati all'app.

	Dopo aver scelto il piano, fare clic su **Crea**. Verrà creato il back-end dell'app per dispositivi mobili.


### Eseguire CreateViews.SQL

L'app sottoposta a scaffolding contiene un file denominato `createViews.sql`. Questo script deve essere eseguito sul database di destinazione. La stringa di connessione per il database di destinazione può essere ottenuta dal servizio mobile migrato ed è disponibile nel pannello **Impostazioni** in **Stringhe di connessione**. È denominata `MS_TableConnectionString`.

È possibile eseguire lo script da SQL Server Management Studio o Visual Studio.

### Collegare il database al servizio app

Collegare il database esistente al servizio app:

- Nel [portale di Azure] aprire il servizio app.
- Selezionare **Tutte le impostazioni** -> **Connessioni dati**.
- Fare clic su **+ Aggiungi**.
- Nell'elenco a discesa selezionare **Database SQL**
- In **Database SQL** selezionare il database esistente e quindi fare clic su **Seleziona**.
- In **Stringa di connessione** immettere il nome utente e la password per il database e quindi fare clic su **OK**.
- Nel pannello **Add data connections** (Aggiungi connessioni dati) fare clic su **OK**.

Per trovare il nome utente e la password, visualizzare la stringa di connessione per il database di destinazione nel servizio mobile migrato.


### Configurare l'autenticazione

Le app per dispositivi mobili di Azure consentono di configurare l'autenticazione di Azure Active Directory, Facebook, Google, Microsoft e Twitter all'interno del servizio. L'autenticazione personalizzata dovrà essere sviluppata separatamente. Per altre informazioni, vedere la documentazione relativa ai [concetti dell'autenticazione] e la [guida introduttiva all'autenticazione].

## <a name="updating-clients"></a>Aggiornare i client per dispositivi mobili

Dopo aver reso operativo un back-end dell'app per dispositivi mobili, è possibile lavorare su una nuova versione dell'applicazione client che ne faccia uso. App per dispositivi mobili include anche una nuova versione degli SDK del client e, come nel caso dell'aggiornamento del server descritto in precedenza, sarà necessario rimuovere tutti i riferimenti agli SDK di Servizi mobili prima di installare le versioni di App per dispositivi mobili.

Una delle principali modifiche tra le versioni è rappresentata dai costruttori che non richiedono più una chiave applicazione. Ora è sufficiente passare l'URL dell'app per dispositivi mobili. Ad esempio, nei client .NET, il costruttore `MobileServiceClient` ora è:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Per altre informazioni sull'installazione dei nuovi SDK e sull'uso della nuova struttura, visitare i collegamenti seguenti:

- [Android versione 2.2 o successiva](app-service-mobile-android-how-to-use-client-library.md)
- [iOS versione 3.0.0 o successiva](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) versione 2.0.0 o successiva](app-service-mobile-dotnet-how-to-use-client-library.md)
- [Apache Cordova versione 2.0 o successiva](app-service-mobile-cordova-how-to-use-client-library.md)

Se l'applicazione usa le notifiche push, prendere nota delle specifiche istruzioni di registrazione per ogni piattaforma, perché sono state apportate alcune modifiche anche in questo ambito.

Quando la nuova versione del client è pronta, provarla con il progetto server aggiornato. Dopo averne verificato il funzionamento, è possibile rilasciare una nuova versione dell'applicazione per i clienti. Infine, quando i clienti avranno ricevuto gli aggiornamenti, sarà possibile eliminare la versione di Servizi mobili dell'app. A questo punto, sono stati aggiornati completamente a un'App per dispositivi mobili del servizio app utilizzando l’SDK più recente del server di App per dispositivi mobili.

<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[Informazioni sulle app per dispositivi mobili]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /it-IT/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /it-IT/documentation/services/scheduler/
[Web Job]: ../app-service-web/websites-webjobs-resources.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[prezzi del servizio app]: https://azure.microsoft.com/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[concetti dell'autenticazione]: ../app-service/app-service-authentication-overview.md
[guida introduttiva all'autenticazione]: app-service-mobile-auth.md

[Portale di Azure]: https://portal.azure.com/
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

<!---HONumber=AcomDC_0817_2016-->