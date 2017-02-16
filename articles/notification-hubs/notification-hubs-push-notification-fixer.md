---
title: Hub di notifica di Azure - Linee guida sulla diagnostica
description: Linee guida sulla diagnostica dei problemi comuni relativi a Hub di notifica di Azure.
services: notification-hubs
documentationcenter: Mobile
author: ysxu
manager: dwrede
editor: 
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b0b067f02f02f722534238891f7412153635df80


---
# <a name="azure-notification-hubs---diagnosis-guidelines"></a>Hub di notifica di Azure - Linee guida sulla diagnostica
## <a name="overview"></a>Overview
Una delle domande più frequenti dei clienti che usano Hub di notifica di Azure riguarda il motivo per cui una notifica inviata dal back-end dell'applicazione non viene visualizzata nel dispositivo client: dove e perché sono state eliminate le notifiche e come risolvere il problema? In questo articolo verranno esaminate le varie ragioni per cui le notifiche possono essere eliminate o non giungere ai dispositivi. Verrà anche illustrato come analizzare e individuare la causa principale. 

Prima di tutto, è fondamentale comprendere il modo in cui Hub di notifica di Azure invia le notifiche push ai dispositivi.
![][0]

In un tipico flusso di invio di notifiche il messaggio viene inviato dal **back-end dell'applicazione** a **Hub di notifica di Azure**, che a sua volta elabora tutte le registrazioni tenendo conto dei tag configurati e delle espressioni tag per determinare i "destinatari", ovvero tutte le registrazioni che devono ricevere la notifica push. Queste registrazioni possono coprire alcune o tutte le piattaforme supportate: iOS, Google, Windows, Windows Phone, Kindle e Baidu per Android in Cina. Una volta determinate le destinazioni, Hub di notifica invia notifiche push, suddivise su più batch di registrazioni, al **servizio di notifica push (PNS)** specifico della piattaforma del dispositivo, ad esempio il servizio APNS per Apple, GCM per Google e così via. Hub di notifica esegue l'autenticazione con il rispettivo PNS in base alle credenziali impostate nel portale di Azure classico e nella pagina Configura Hub di notifica. Il PNS inoltra quindi le notifiche ai **dispositivi client** corrispondenti. Questa è la modalità ottimale per la piattaforma per il recapito delle notifiche push e tenere presente che l'ultima parte del recapito delle notifiche avviene tra il PNS della piattaforma e il dispositivo. Si hanno pertanto quattro componenti principali: *client*, *back-end dell'applicazione*, *Hub di notifica di Azure (NH)* e *servizio di notifica push (PNS)*. Uno di questi componenti può essere la causa dell'eliminazione delle notifiche. Informazioni più dettagliate su questa architettura sono disponibili in [Panoramica dell'Hub di notifica].

Il mancato recapito delle notifiche può avvenire nella fase di test iniziale/staging, che potrebbe indicare un problema di configurazione, oppure può avvenire nell'ambiente di produzione, con l'eliminazione di alcune o tutte le notifiche, che indica un problema più profondo a livello di modello di messaggistica o applicazione. Nella seguente sezione verranno esaminati alcuni scenari di notifiche eliminate, dai più comuni ai più rari, alcuni dei quali possono sembrare ovvi, altri molto meno. 

## <a name="azure-notifications-hub-mis-configuration"></a>Configurazione errata di Hub di notifica di Azure
Hub di notifica di Azure deve eseguire l'autenticazione nel contesto dell'applicazione dello sviluppatore affinché possa inviare notifiche al PNS corrispondente. Questa operazione è resa possibile dagli sviluppatori creando un account sviluppatore con la rispettiva piattaforma (Google, Apple, Windows e così via) e quindi registrando l'applicazione, dove ottengono le credenziali che devono essere configurate nella sezione di configurazione di Hub di notifica nel portale. Se non perviene nessuna notifica, prima di tutto occorre assicurarsi che siano configurate le credenziali corrette in Hub di notifica, verificandone la corrispondenza con l'applicazione creata con l'account sviluppatore specifico della piattaforma. L' [esercitazione introduttiva] è utile per ottenere istruzioni dettagliate su questa procedura. Ecco alcuni errori di configurazione comuni:

1. **Generale**
   
    a) Assicurarsi che il nome dell'hub di notifica (privo di errori di digitazione) sia lo stesso:
   
   * Dove si esegue la registrazione dal client 
   * Dove si inviano le notifiche dal back-end  
   * Dove sono state configurate le credenziali PNS e 
   * Di cui sono state configurate le credenziali della firma di accesso condiviso nel client e nel back-end. 
     
     b) Assicurarsi di usare le stringhe corrette di configurazione della firma di accesso condiviso nel client e nel back-end dell'applicazione. In linea generale, è necessario usare **DefaultListenSharedAccessSignature** sul client e **DefaultFullSharedAccessSignature** sul back-end dell'applicazione (che fornisce l'autorizzazione per poter inviare la notifica all'Hub di notifica)
2. **Configurazione del servizio di notifica push di Apple (APNS)**
   
    È necessario gestire due hub diversi, uno per la produzione e un altro a scopo di test. Ciò significa che il certificato da usare nell'ambiente sandbox e il certificato da usare nell'ambiente di produzione devono essere caricati in hub separati. Non tentare di caricare certificati di tipo diverso in uno stesso hub poiché questo può provocare errori di notifica a valle. Se dovesse capitare di caricare inavvertitamente certificati di tipo diverso nello stesso hub, è consigliabile eliminare l'hub e ricominciare da zero. Se per qualche motivo non fosse possibile eliminare l'hub, sarà necessario quanto meno eliminare tutte le registrazioni esistenti dall'hub. 
3. **Configurazione di Google Cloud Messaging (GCM)** 
   
    a) Assicurarsi di abilitare "Google Cloud Messaging per Android" nel progetto cloud. 
   
    ![][2]
   
    b) Creare una chiave server durante l'acquisizione delle credenziali, che Hub di notifica userà per l'autenticazione con GCM. 
   
    ![][3]
   
    c) Assicurarsi di avere configurato un ID progetto sul client, un'entità totalmente numerica che è possibile ottenere dal dashboard:
   
    ![][1]

## <a name="application-issues"></a>Problemi relativi all'applicazione
1) **Tag/Espressioni tag**

Se si usano tag o espressioni tag per segmentare i destinatari, è sempre possibile che al momento dell'invio della notifica non venga trovato alcun destinatario in base ai tag o alle espressioni tag specificate nella chiamata di invio. Si consiglia di esaminare le registrazioni per assicurarsi che vi siano tag corrispondenti quando si invia la notifica, quindi verificare la ricezione della notifica solo dai client con tali registrazioni. Ad esempio, se è stato usato il tag "Politica" in tutte le registrazioni con Hub di notifica e si invia una notifica con il tag "Sport", la notifica non viene inviata ad alcun dispositivo. Un caso complesso con espressioni tag è quello in cui la registrazione è stata eseguita solo con "Tag A" OPPURE "Tag B", ma durante l'invio delle notifiche si usa "Tag A & & Tag B" come destinazione. Nella sezione dei suggerimenti per la diagnostica in autonomia sono indicati alcuni modi per esaminare le registrazioni con i tag di cui dispongono. 

2) **Problemi relativi ai modelli**

Se si usano i modelli, seguire le indicazioni riportate in [Linee guida sui modelli]. 

3) **Registrazioni non valide**

Supponendo che Hub di notifica sia stato configurato correttamente e che tutti i tag/espressioni tag siano stati usati correttamente, con conseguente individuazione di destinatari validi a cui inviare le notifiche, Hub di notifica genera diversi batch di elaborazione in parallelo, ognuno dei quali invia messaggi a un set di registrazioni. 

> [!NOTE]
> Dato che l'elaborazione avviene in parallelo, l'ordine di recapito delle notifiche non è garantito. 
> 
> 

A questo punto, Hub di notifica di Azure è ottimizzato per un modello di recapito "at-most-once" per i messaggi, che comporta un tentativo di deduplicazione per fare in modo che nessuna notifica venga recapitata più di una volta a un dispositivo. A questo scopo, prima dell'invio effettivo del messaggio al PNS vengono esaminate le registrazioni per garantire che venga inviato un solo messaggio per ID dispositivo. Dato che ogni batch viene inviato al PNS, che a sua volta accetta e convalida le registrazioni, è possibile che il PNS rilevi un errore in una o più registrazioni in un batch, restituisca un errore a Hub di notifica di Azure e interrompa l'elaborazione, eliminando completamente il batch interessato. Questo vale soprattutto per gli APNS che usano un protocollo di flusso TCP. Sebbene sia possibile eseguire al massimo un recapito, in questo caso rimuoviamo la registrazione dell'errore dal database e ripetiamo il recapito della notifica per il resto dei dispositivi nel batch.

È possibile che venga visualizzata una comunicazione di errore per il tentativo di recapito non riuscito su una registrazione tramite le API REST dell'Hub di notifica di Azure: [Per Message Telemetry: Get Notification Message Telemetry](https://msdn.microsoft.com/library/azure/mt608135.aspx) (Dati di telemetria per messaggio: ottenere i dati di telemetria del messaggio di notifica) e [PNS Feedback](https://msdn.microsoft.com/library/azure/mt705560.aspx) (Feedback PNS). Per un codice di esempio, vedere [SendRESTExample](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample) .

## <a name="pns-issues"></a>Problemi relativi a PNS
Quando il PNS corrispondente riceve il messaggio di notifica, si occuperà di recapitare la notifica al dispositivo. Hub di notifica di Azure non è coinvolto in questa operazione e non controlla in alcun modo se e quando la notifica verrà recapitata al dispositivo. Dato che i servizi di notifica della piattaforma sono piuttosto affidabili, le notifiche tendono a raggiungere i dispositivi in pochi secondi dal PNS. In caso di limitazione del PNS, però, Hub di notifica di Azure applica una strategia di backoff esponenziale e se il PNS rimane irraggiungibile per 30 minuti viene applicato un criterio che determina la scadenza e l'eliminazione definitiva di quei messaggi. 

Se un PNS tenta di recapitare una notifica, ma il dispositivo è offline, la notifica viene archiviata dal PNS per un periodo di tempo limitato e viene recapitata al dispositivo quando ritorna disponibile. Viene archiviata solo una notifica recente per una determinata app. Se vengono inviate più notifiche quando il dispositivo è offline, ogni nuova notifica determina la rimozione di quella precedente. Il comportamento che prevede che venga mantenuta solo la notifica più recente prende il nome di unione di notifiche in APNS e compressione in GCM (che usa una chiave di compressione). Se il dispositivo rimane offline a lungo, le notifiche archiviate per esso vengono rimosse. Fonte: [Linee guida sul servizio APN] & [Linee guida su GCM]

Con Hub di notifica di Azure è possibile passare una chiave di unione tramite un'intestazione HTTP mediante l'API `SendNotification` generica (ad esempio per .NET SDK - `SendNotificationAsync`) che accetta anche le intestazioni HTTP che vengono passate al rispettivo PNS così come sono. 

## <a name="self-diagnose-tips"></a>Suggerimenti per l'utente relativi alla diagnostica
Di seguito vengono illustrate le diverse strategie per diagnosticare e identificare la causa principale dei problemi relativi a Hub di notifica:

### <a name="verify-credentials"></a>Verificare le credenziali
1. **Portale per sviluppatori PNS**
   
    Verificarle nel rispettivo portale per sviluppatori PNS (APNS, GCM, WNS e così via) usando le [esercitazione introduttiva].
2. **Portale di Azure classico**
   
    Passare alla scheda Configura per esaminare e confrontare le credenziali con quelle ottenute dal portale per sviluppatori PNS. 
   
    ![][4]

### <a name="verify-registrations"></a>Verificare le registrazioni
1. **Visual Studio**
   
    Se si usa Visual Studio per lo sviluppo è possibile connettersi a Microsoft Azure e visualizzare e gestire una serie di servizi di Azure, incluso Hub di notifica, da Esplora server. Questo risulta particolarmente utile per l'ambiente sviluppo/test. 
   
    ![][9]
   
    È possibile visualizzare e gestire tutte le registrazioni nell'hub, comodamente suddivise in registrazioni di piattaforma, native o di modello, tutti i tag, gli identificatori di PNS, gli ID di registrazione e le date di scadenza. È anche possibile modificare una registrazione in tempo reale; questo è utile, ad esempio, se si vogliono modificare i tag. 
   
    ![][8]
   
   > [!NOTE]
   > La funzionalità di Visual Studio per la modifica delle registrazioni deve essere usata solo durante le attività di sviluppo/test su un numero limitato di registrazioni. Se è necessario correggere le registrazioni in blocco, è possibile usare la funzionalità di esportazione/importazione delle registrazioni descritta in [Esportazione/Importazione di registrazioni](https://msdn.microsoft.com/library/dn790624.aspx)
   > 
   > 
2. **Service Bus Explorer**
   
    Molti clienti usano Service Bus Explorer (descritto in [Service Bus Explorer] ) per visualizzare e gestire l'hub di notifica. Si tratta di un progetto open source disponibile su code.microsoft.com - [codice di Service Bus Explorer]

### <a name="verify-message-notifications"></a>Verificare le notifiche di messaggi
1. **Portale di Azure classico**
   
    Passare alla scheda "Debug" per inviare notifiche di prova ai client senza la necessità di un servizio back-end operativo. 
   
    ![][7]
2. **Visual Studio**
   
    È anche possibile inviare notifiche comodamente da Visual Studio:
   
    ![][10]
   
    Altre informazioni sulla funzionalità di esplorazione di Hub di notifica di Azure di Visual Studio sono disponibili qui: 
   
   * [Informazioni generali su Esplora server di Visual Studio]
   * [Post di blog su Esplora server di Visual Studio - 1]
   * [Post di blog su Esplora server di Visual Studio - 2]

### <a name="debug-failed-notifications-review-notification-outcome"></a>Eseguire il debug di notifiche non riuscite/esaminare l'esito della notifica
**Proprietà EnableTestSend**

Quando si invia una notifica tramite Hub di notifica, questa viene inizialmente inserita nella coda per consentire a Hub di notifica di eseguire elaborazioni allo scopo di individuare tutte le destinazioni e infine viene inviata al PNS. Pertanto, quando si usa l'API REST o un SDK client, l'esito corretto della chiamata di invio significa solo che il messaggio è stato inserito nella coda in Hub di notifica. Non fornisce indicazioni su ciò che è accaduto quando Hub di notifica ha inviato il messaggio al PNS. Se la notifica non arriva al dispositivo client, è possibile che si sia verificato un errore quando Hub di notifica ha tentato di recapitare il messaggio al PNS, ad esempio che siano state superate le dimensioni massime per il payload consentite dal PNS oppure che le credenziali configurate in Hub di notifica non siano valide. Per informazioni sugli errori del PNS, è stata introdotta una proprietà denominata [funzionalità EnableTestSend]. Questa proprietà viene abilitata automaticamente quando si inviano messaggi di prova dal portale o dal client di Visual Studio e consente di visualizzare informazioni di debug dettagliate. Può essere usata tramite API, secondo l'esempio di .NET SDK in cui ora è disponibile, e verrà aggiunta a tutti gli SDK dei client. Per usarla con la chiamata REST, è sufficiente aggiungere un parametro di stringa di query denominato "test" alla fine della chiamata di invio, ad esempio 

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

*Esempio (.NET SDK)*

Si supponga di usare .NET SDK per inviare una notifica di tipo avviso popup nativa:

    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);

`result.State` indicherà semplicemente `Enqueued` alla fine dell'esecuzione, senza alcun approfondimento su ciò che è accaduto al push. Ora è possibile usare la proprietà booleana `EnableTestSend` durante l'inizializzazione di `NotificationHubClient` per ottenere lo stato dettagliato relativo agli errori PNS verificatisi durante l'invio della notifica. In questo caso la chiamata di invio richiederà più tempo perché restituisce il risultato solo dopo il recapito della notifica a PNS da parte di Hub di notifica. 

    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }

*Output di esempio*

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong

Questo messaggio indica che nell'hub di notifica sono state configurate credenziali non valide oppure che si è verificato un problema relativo alle registrazioni nell'hub. La procedura consigliata consiste nell'eliminare la registrazione e lasciare che il client la ricrei prima di inviare il messaggio. 

> [!NOTE]
> Si noti che l'uso di questa proprietà è molto limitato e deve quindi essere riservato solo ad ambienti di sviluppo/test con un set limitato di registrazioni. Le notifiche di debug vengono inviate solo a 10 dispositivi. Inoltre è previsto un limite di 10 invii al minuto per l'elaborazione di debug. 
> 
> 

### <a name="review-telemetry"></a>Esaminare i dati di telemetria
1. **Usare il portale di Azure classico**
   
    Il portale consente di ottenere una rapida panoramica di tutte le attività in Hub di notifica. 
   
    a) Nella scheda "Dashboard" è presente una visualizzazione aggregata delle registrazioni, delle notifiche e degli errori per piattaforma. 
   
    ![][5]
   
    b) Nella scheda "Monitoraggio" è possibile aggiungere molte altre metriche specifiche della piattaforma per ottenere informazioni più approfondite sugli errori specifici del PNS restituiti quando Hub di notifica tenta di inviare la notifica al PNS. 
   
    ![][6]
   
    È consigliabile esaminare prima i **Messaggi in arrivo**, le **Operazioni di registrazione** e le **Notifiche operazioni riuscite** e quindi passare alla scheda relativa alla piattaforma per esaminare gli errori specifici del PNS. 
   
    d) Se Hub di notifica non è configurato con le impostazioni di autenticazione corrette, verrà visualizzato l'errore di autenticazione del PNS. Questo errore suggerisce appunto che è necessario controllare le credenziali PNS. 

2) **Accesso a livello di codice**

Per informazioni dettagliate: 

* [Accesso alla telemetria a livello di codice]
* [Esempio di accesso alla telemetria tramite API] 

> [!NOTE]
> Molte funzionalità correlate alla telemetria, come l'**esportazione/importazione di registrazioni** e l'**accesso alla telemetria tramite API**, sono disponibili solo con il livello Standard. Se si tenta di usare queste funzionalità in un livello Basic o Gratuito, viene visualizzato un apposito messaggio di eccezione quando si usa l'SDK e il messaggio HTTP 403 (Accesso negato) quando vengono usate direttamente dalle API REST. Assicurarsi di passare al livello Standard tramite il portale di Azure classico.  
> 
> 

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/GCMConfigure.png
[2]: ./media/notification-hubs-diagnosing/GCMEnable.png
[3]: ./media/notification-hubs-diagnosing/GCMServerKey.png
[4]: ./media/notification-hubs-diagnosing/PortalConfigure.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalMonitoring.png
[7]: ./media/notification-hubs-diagnosing/PortalTestNotification.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[Panoramica dell'Hub di notifica]: notification-hubs-push-notification-overview.md
[esercitazione introduttiva]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Linee guida sui modelli]: https://msdn.microsoft.com/library/dn530748.aspx 
[Linee guida sul servizio APN]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW4
[Linee guida su GCM]: http://developer.android.com/google/gcm/adv.html
[Export/Import Registrations]: http://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer]: http://msdn.microsoft.com/library/dn530751.aspx
[codice di Service Bus Explorer]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Informazioni generali su Esplora server di Visual Studio]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[Post di blog su Esplora server di Visual Studio - 1]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[Post di blog su Esplora server di Visual Studio - 2]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[funzionalità EnableTestSend]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx
[Accesso alla telemetria a livello di codice]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[Esempio di accesso alla telemetria tramite API]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel




<!--HONumber=Feb17_HO2-->


