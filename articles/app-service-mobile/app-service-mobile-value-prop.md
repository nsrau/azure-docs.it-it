---
title: Informazioni sulle app per dispositivi mobili
description: Informazioni sui vantaggi che il servizio app può apportare alle app per dispositivi mobili aziendali.
services: app-service\mobile
documentationcenter: ''
author: adrianhall
manager: yochayk
editor: ''

ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: adrianha

---
# <a name="getting-started"></a>Informazioni sulle app per dispositivi mobili
Il servizio app di Azure è un'offerta di [piattaforma distribuita come servizio](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) interamente gestita per sviluppatori professionisti, che offre un set completo di funzionalità per scenari Web, mobili e di integrazione. Le *app per dispositivi mobili* nel *servizio app di Azure* offrono una piattaforma di sviluppo di applicazioni mobili estremamente scalabile e disponibile a livello globale per sviluppatori aziendali e integratori di sistemi, che fornisce un set completo di funzionalità per gli sviluppatori di soluzioni per dispositivi mobili.

![App per dispositivi mobili](./media/app-service-mobile-value-prop/overview.png)

## Perché le app per dispositivi mobili?
Le *app per dispositivi mobili* nel *servizio app di Azure* offrono una piattaforma di sviluppo di applicazioni mobili estremamente scalabile e disponibile a livello globale per sviluppatori aziendali e integratori di sistemi, che fornisce un set completo di funzionalità per gli sviluppatori di soluzioni per dispositivi mobili. Con le app per dispositivi mobili è possibile:

* **Compilare app native e multipiattaforma**: indipendentemente dal tipo di app compilata, ad esempio app native per iOS, Android e Windows oppure app multipiattaforma Xamarin o Cordova (Phonegap), è possibile sfruttare il servizio app usando SDK nativi.
* **Connettersi ai propri sistemi aziendali**: con le app per dispositivi mobili è possibile aggiungere l'accesso aziendale in pochi minuti e connettersi alle risorse aziendali locali o nel cloud.
* **Compilare app offline con sincronizzazione dei dati**: è possibile aumentare la produttività della forza lavoro mobile creando app eseguibili offline e usando le app per dispositivi mobili per sincronizzare i dati in background, quando la connettività è disponibile, con qualsiasi origine dati o API SaaS aziendale.
* **Inviare notifiche push a migliaia di utenti in pochi secondi**: coinvolgere i propri clienti con notifiche push istantanee e personalizzate su qualsiasi dispositivo, inviate al momento giusto, non è mai stato così facile.

## Funzionalità delle app per dispositivi mobili
Le funzionalità seguenti sono importanti per lo sviluppo per dispositivi mobili abilitati per il cloud:

* **Autenticazione e autorizzazione**: è possibile scegliere da un elenco in continua evoluzione di provider di identità, tra cui Azure Active Directory per l'autenticazione aziendale, nonché provider di servizi di social networking come Facebook, Google, Twitter e account Microsoft. App per dispositivi mobili di Azure fornisce un servizio OAuth 2.0 per ogni provider. È anche possibile integrare l'SDK del provider di identità per funzionalità specifiche del provider.
  
  Altre informazioni sulle [funzionalità di autenticazione].
* **Accesso ai dati**: le app per dispositivi mobili di Azure offrono un'origine dati OData v3 ideale per dispositivi mobili collegata a SQL Azure o a un'istanza di SQL Server locale. Poiché questo servizio può essere basato su Entity Framework, consente una facile integrazione con altri provider di dati NoSQL e SQL, inclusi [Archiviazione tabelle di Azure], MongoDB, [DocumentDB] e provider di API SaaS, ad esempio Office 365 e Salesforce.com.
* **Sincronizzazione offline**: gli SDK client facilitano la compilazione di applicazioni per dispositivi mobili reattive e affidabili che funzionano con un set di dati offline sincronizzabile automaticamente con i dati back-end, con supporto per la risoluzione dei conflitti.
  
  Altre informazioni sulle [funzionalità dati].
* **Notifiche push**: gli SDK client si integrano facilmente con le funzionalità di registrazione di Hub di notifica di Azure, consentendo di inviare notifiche push a milioni di utenti contemporaneamente.
  
  Altre informazioni sulle [funzionalità di notifica push].
* **SDK client**: viene offerto un set completo di SDK client che coprono lo sviluppo nativo ([iOS], [Android] e [Windows]), lo sviluppo multipiattaforma ([Xamarin per iOS e Android] e [Xamarin.Forms]) e lo sviluppo di applicazioni ibride ([Apache Cordova]). Ogni SDK client è disponibile con una licenza MIT ed è open source.

## Funzionalità del servizio app di Azure
Le funzionalità della piattaforma seguenti in genere sono utili per i siti di produzione di dispositivi mobili.

* **Scalabilità automatica**: il servizio app consente di aumentare il numero di istanze o scalare orizzontalmente le app in modo rapido, per gestire qualsiasi carico di lavoro in ingresso dei clienti. È possibile selezionare manualmente il numero e le dimensioni delle VM o configurare la scalabilità automatica per adattare i back-end dell'app per dispositivi mobili ai carichi o alla pianificazione.
  
  Altre informazioni sulla [scalabilità automatica].
* **Ambienti di staging**: il servizio app può eseguire più versioni del sito, consentendo di eseguire test A/B, test in ambiente di produzione nell'ambito di un piano DevOps più ampio e staging sul posto di un nuovo back-end.
  
  Altre informazioni sugli [ambienti di staging].
* **Distribuzione continua**: il servizio app può integrarsi con i comuni sistemi di gestione controllo servizi, consentendo di distribuire automaticamente una nuova versione del back-end effettuando il push in un ramo del sistema di gestione controllo servizi.
  
  Altre informazioni sulle [opzioni di distribuzione].
* **Rete virtuale**: il servizio app può connettersi alle risorse locali usando la rete virtuale, ExpressRoute o connessioni ibride.
  
  Altre informazioni su [connessioni ibride], [reti virtuali] ed [ExpressRoute].
* **Ambienti isolati/dedicati**: il servizio app può essere eseguito in un ambiente completamente isolato e dedicato per eseguire in modo sicuro le app del servizio app di Azure su larga scala. Ciò è ideale per i carichi di lavoro delle applicazioni che richiedono scalabilità molto elevata, isolamento o accesso alla rete sicuro.
  
  Altre informazioni sugli [ambienti del servizio app].

## Introduzione
Per iniziare a usare il servizio app per dispositivi mobili, seguire l'esercitazione [Attività iniziali]. Questa esercitazione illustrerà le informazioni di base relative a produzione di un back-end e di un client per dispositivi mobili scelti dall'utente, integrazione dell'autenticazione, sincronizzazione offline e notifiche push. È possibile ripetere l'[esercitazione introduttiva] più volte, una per ogni applicazione client.

Per altre informazioni sulle app per dispositivi mobili di Azure, vedere la [mappa di apprendimento]. Per altre informazioni sulla piattaforma del servizio app di Azure, vedere [Informazioni sul servizio app di Azure].

> [!NOTE]
> Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](https://tryappservice.azure.com/?appServiceName=mobile), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
> 
> 

<!-- URLs. -->
[Migrate your Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Informazioni sul servizio app di Azure]: ../app-service/app-service-value-prop-what-is.md
[Attività iniziali]: app-service-mobile-ios-get-started.md
[esercitazione introduttiva]: app-service-mobile-ios-get-started.md
[Archiviazione tabelle di Azure]: ../storage/storage-getting-started-guide.md
[DocumentDB]: ../documentdb/documentdb-get-started.md
[funzionalità di autenticazione]: ./app-service-mobile-auth.md
[funzionalità dati]: ./app-service-mobile-offline-data-sync.md
[funzionalità di notifica push]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin per iOS e Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[scalabilità automatica]: ../app-service-web/web-sites-scale.md
[ambienti di staging]: ../app-service-web/web-sites-staged-publishing.md
[opzioni di distribuzione]: ../app-service-web/web-sites-deploy.md
[connessioni ibride]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[reti virtuali]: ../app-service-web/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service-web/app-service-app-service-environment-network-configuration-expressroute.md
[ambienti del servizio app]: ../app-service-web/app-service-app-service-environment-intro.md
[mappa di apprendimento]: https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/

<!---HONumber=AcomDC_1005_2016-->