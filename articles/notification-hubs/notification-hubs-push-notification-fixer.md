---
title: Diagnosi dei problemi delle notifiche non recapitate da Hub di notifica di Azure
description: Informazioni su come diagnosticare i problemi comuni relativi alle notifiche non recapitate in Hub di notifica di Azure.
services: notification-hubs
documentationcenter: Mobile
author: jwhitedev
manager: kpiteira
editor: 
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 3925208fe56bcd9513ec4c0f21aa1e2dd8fbf9c5
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="diagnose-dropped-notifications-in-notification-hubs"></a>Diagnosticare le notifiche non recapitate in Hub di notifica

Una delle domande più comuni dei clienti di Hub di notifica di Azure è come risolvere i problemi quando le notifiche inviate da un'applicazione non vengono visualizzate nei dispositivi client. I clienti desiderano sapere dove si è verificato l'errore e perché le notifiche non sono state recapitate e come risolvere il problema. Questo articolo illustra i motivi per cui le notifiche possono essere eliminate o non essere ricevute dai dispositivi. Informazioni su come analizzare e determinare la causa principale. 

È prima di tutto fondamentale capire come viene eseguito il push delle notifiche a un dispositivo da Hub di notifica.

![Architettura di Hub di notifica][0]

In un flusso tipico di invio delle notifiche, il messaggio viene inviato dal *back-end dell'applicazione* ad Hub di notifica. Hub di notifica esegue alcune operazioni di elaborazione per tutte le registrazioni. L'elaborazione prende in considerazione i tag e le espressioni tag configurati per determinare le "destinazioni". Le destinazioni sono tutte le registrazioni che devono ricevere la notifica push. Queste registrazioni possono coprire alcune o tutte le piattaforme supportate: iOS, Google, Windows, Windows Phone, Kindle e Baidu per Android in Cina.

Con le destinazioni stabilite, Hub di notifica esegue il push delle notifiche al *servizio di notifica push* per la piattaforma del dispositivo. Alcuni esempi sono il servizio Apple Push Notification (APN) per Apple e Firebase Cloud Messaging (FCM) per Google. Hub di notifica esegue il push delle notifiche suddivise in più batch di registrazioni. Hub di notifica esegue l'autenticazione nel servizio di notifica push corrispondente in base alle credenziali impostate nel portale di Azure, in **Configure Notification Hub** (Configura Hub di notifica). Il servizio di notifica push inoltra quindi le notifiche ai *dispositivi client* corrispondenti. 

Tenere presente che l'ultima parte del recapito delle notifiche avviene tra il servizio di notifica push della piattaforma e il dispositivo. Uno qualsiasi dei quattro componenti principali del processo di notifica push (client, back-end dell'applicazione, Hub di notifica e servizio di notifica push della piattaforma) potrebbe causare l'eliminazione delle notifiche. Per altre informazioni sull'architettura di Hub di notifica, vedere [Panoramica dell'Hub di notifica].

Il mancato recapito delle notifiche potrebbe verificarsi durante la fase iniziale di test/staging. Le notifiche non recapitate in questa fase potrebbero indicare un problema di configurazione. Se i problemi di recapito delle notifiche si verificano nell'ambiente di produzione, potrebbero essere eliminate alcune o tutte le notifiche. In questo caso, si tratta di un problema più articolato a livello di applicazione o della messaggistica. 

Nella sezione successiva vengono esaminati gli scenari in cui potrebbe verificarsi il mancato recapito notifiche, dai più comuni a quelli più rari.

## <a name="notification-hubs-misconfiguration"></a>Configurazione errata di Hub di notifica
Per inviare notifiche al servizio di notifica push corrispondente, Hub di notifica di Azure deve eseguire l'autenticazione nel contesto dell'applicazione dello sviluppatore. A questo scopo, lo sviluppatore crea un account sviluppatore con la piattaforma corrispondente (Google, Apple, Windows e così via). Lo sviluppatore registra quindi l'applicazione per la piattaforma da cui ottiene le credenziali. 

È necessario aggiungere le credenziali della piattaforma nel portale di Azure. Se il dispositivo non riceve alcuna notifica, è prima di tutto necessario assicurarsi che in Hub di notifica siano configurate le credenziali corrette. Le credenziali devono corrispondere all'applicazione creata con un account sviluppatore specifico della piattaforma. 

Per istruzioni dettagliate per eseguire questo processo, vedere [Introduzione ad Hub di notifica di Azure].

Ecco alcuni errori di configurazione comuni:

* **Generale**
   
    * Assicurarsi che il nome dell'hub di notifica (privo di errori di digitazione) sia uguale in ognuna di queste posizioni:

        * Posizione di registrazione dal client.
        * Posizione di invio delle notifiche dal back-end.
        * Posizione di configurazione delle credenziali del servizio di notifica push.
    
    * Assicurarsi di usare le stringhe di configurazione della firma di accesso condiviso corrette nel client e nel back-end dell'applicazione. In linea generale, è necessario usare **DefaultListenSharedAccessSignature** nel client e **DefaultFullSharedAccessSignature** nel back-end dell'applicazione (vengono concesse le autorizzazioni per l'invio di notifiche ad Hub di notifica).

* **Configurazione del servizio APN**
   
    È necessario gestire due hub diversi, uno per la produzione e un altro per i test. Ciò significa che è necessario caricare il certificato da usare nell'ambiente sandbox in un hub separato dal certificato e dall'hub che verranno usati in produzione. Non provare a caricare tipi diversi di certificati nello stesso hub. Ciò potrebbe causare errori di notifica. 
    
    Se si caricano inavvertitamente tipi diversi di certificati nello stesso hub, è consigliabile eliminare l'hub e ricominciare con un nuovo hub. Se per qualche motivo non è possibile eliminare l'hub, sarà necessario quanto meno eliminare tutte le registrazioni esistenti dall'hub. 

* **Configurazione di FCM** 
   
    1. Verificare che la *chiave server* ottenuta da Firebase corrisponda alla chiave server registrata nel portale di Azure.
   
    ![Chiave server Firebase][3]
   
    2. Assicurarsi di aver configurato l'**ID progetto** nel client. È possibile ottenere il valore per **ID progetto** dal dashboard Firebase.
   
    ![ID progetto Firebase][1]

## <a name="application-issues"></a>Problemi relativi all'applicazione
* **Tag ed espressioni tag**

    Se si usano tag o espressioni tag per segmentare i destinatari, è possibile che al momento dell'invio della notifica non venga trovato alcun destinatario in base ai tag o alle espressioni tag specificati nella chiamata di invio. 
    
    Esaminare le registrazioni per verificare che siano presenti tag corrispondenti quando si invia una notifica. Verificare quindi la ricezione delle notifiche solo dai client con queste registrazioni. 
    
    Ad esempio, se tutte le registrazioni per Hub di notifica sono state eseguite con il tag "Politica" e si invia una notifica con il tag "Sport", la notifica non viene inviata ad alcun dispositivo. Un caso complesso con espressioni tag è quello in cui la registrazione è stata eseguita solo con "Tag A" OPPURE "Tag B", ma durante l'invio delle notifiche si usa "Tag A & & Tag B" come destinazione. Nella sezione dei suggerimenti per l'autodiagnosi più avanti in questo articolo viene illustrato come controllare le registrazioni e i relativi tag. 

* **Problemi relativi ai modelli**

    Se si usano modelli, assicurarsi di seguire le indicazioni riportate in [Modelli]. 

* **Registrazioni non valide**

    Se l'hub di notifica è stato configurato correttamente e se gli eventuali tag o espressioni tag sono stati usati correttamente, vengono trovate destinazioni valide. Le notifiche devono essere inviate a queste destinazioni. Hub di notifica attiva quindi vari batch di elaborazione in parallelo. Ogni batch invia messaggi a un set di registrazioni. 

    > [!NOTE]
    > Dato che l'elaborazione viene eseguita in parallelo, l'ordine di recapito delle notifiche non è garantito. 

    Hub di notifica è ottimizzato per un modello di recapito "at-most-once" per i messaggi, che comporta un tentativo di deduplicazione per fare in modo che nessuna notifica venga recapitata più di una volta a un dispositivo. A questo scopo, prima dell'invio effettivo del messaggio al servizio di notifica push vengono esaminate le registrazioni per garantire che venga inviato un solo messaggio per ogni ID di dispositivo. 

    Dato che ogni batch viene inviato al servizio di notifica push, che a sua volta accetta e convalida le registrazioni, è possibile che il servizio di notifica push rilevi un errore per una o più registrazioni in un batch. In questo caso, il servizio di notifica push restituisce un errore ad Hub di notifica e il processo viene arrestato. Il servizio di notifica push elimina completamente tale batch. Questo vale soprattutto per il servizio APN che usa un protocollo di flusso TCP. 

    Il sistema è ottimizzato per il recapito at-most-once, ma in questo caso, la registrazione che causa l'errore viene rimossa dal database. Viene quindi eseguito un ulteriore tentativo di recapito per il resto dei dispositivi nel batch.

    Per ottenere ulteriori informazioni sul tentativo di recapito non riuscito per una registrazione, è possibile usare le API REST di Hub di notifica: [Per Message Telemetry: Get Notification Message Telemetry](https://msdn.microsoft.com/library/azure/mt608135.aspx) (Dati di telemetria per messaggio: ottenere i dati di telemetria del messaggio di notifica) e [PNS feedback](https://msdn.microsoft.com/library/azure/mt705560.aspx) (Feedback PNS). Per un esempio di codice, vedere l'[esempio REST per l'invio](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample).

## <a name="push-notification-service-issues"></a>Problemi del servizio di notifica push
Dopo la ricezione del messaggio di notifica dal servizio di notifica push della piattaforma, è responsabilità del servizio di notifica push recapitare la notifica al dispositivo. A questo punto, Hub di notifica non è coinvolto in questa operazione e non controlla in alcun modo se e quando la notifica viene recapitata al dispositivo. 

Dato che i servizi di notifica della piattaforma sono affidabili, le notifiche raggiungono tendenzialmente i dispositivi in pochi secondi dal servizio di notifica push. Se il servizio di notifica push è limitato, Hub di notifica applica una strategia con backoff esponenziale. Se il servizio di notifica push rimane non raggiungibile per 30 minuti, viene applicato un criterio che determina la scadenza dei messaggi e la loro eliminazione permanente. 

Se un servizio di notifica push tenta di recapitare una notifica, ma il dispositivo è offline, la notifica viene archiviata dal servizio di notifica push per un periodo di tempo limitato e viene recapitata al dispositivo quando ritorna disponibile. 

Per ogni app viene archiviata una sola notifica recente. Se vengono inviate più notifiche quando un dispositivo è offline, ogni nuova notifica determina la rimozione di quella precedente. Il comportamento che prevede che venga mantenuta solo la notifica più recente prende il nome di *unione delle notifiche* nel servizio APN e *compressione* in FCM (che usa una chiave di compressione). Se il dispositivo rimane offline a lungo, le eventuali notifiche archiviate per esso vengono rimosse. Per altre informazioni, vedere [APNs overview] (Panoramica del servizio APN) e [About FCM messages] (Informazioni sui messaggi FCM).

Con Hub di notifica di Azure, è possibile passare una chiave di unione tramite un'intestazione HTTP usando l'API SendNotification generica. Ad esempio, **SendNotificationAsync** per .NET SDK. L'API SendNotification usa inoltre le intestazioni HTTP passate così come sono al servizio di notifica push corrispondente. 

## <a name="self-diagnosis-tips"></a>Suggerimenti per l'autodiagnosi
Di seguito vengono descritti i percorsi per diagnosticare la causa radice del mancato recapito delle notifiche in Hub di notifica:

### <a name="verify-credentials"></a>Verificare le credenziali
* **Portale per sviluppatori del servizio di notifica push**
   
    Verificare le credenziali nel rispettivo portale per sviluppatori del servizio di notifica push corrispondente (APN, FCM, il servizio di notifica di Windows e così via). Per altre informazioni vedere [Introduzione ad Hub di notifica di Azure].

* **Portale di Azure**
   
    Per verificare le credenziali e confrontarle con quelle ottenute dal portale per sviluppatori del servizio di notifica push, nel portale di Azure passare alla scheda **Criteri di accesso**. 
   
    ![Criteri di accesso del portale di Azure][4]

### <a name="verify-registrations"></a>Verificare le registrazioni
* **Visual Studio**
   
    Se si usa Visual Studio per lo sviluppo è possibile connettersi ad Azure tramite Esplora server per visualizzare e gestire più servizi di Azure, incluso Hub di notifica. Questo risulta particolarmente utile per l'ambiente sviluppo/test. 
   
    ![Esplora server di Visual Studio.][9]
   
    È possibile visualizzare e gestire tutte le registrazioni nell'hub, classificate in base a registrazioni di piattaforma, native o di modello, tutti i tag, l'identificatore del servizio di notifica push, l'ID di registrazione e la data di scadenza. È anche possibile modificare una registrazione in questa pagina. Questo è particolarmente utile per la modifica dei tag. 
   
    ![Registrazioni dei dispositivi con Visual Studio][8]
   
   > [!NOTE]
   > Usare Visual Studio per modificare le registrazioni solo durante le fasi di sviluppo/test e con un numero limitato di registrazioni. Se è necessario modificare le registrazioni in blocco, è possibile usare la funzionalità di esportazione e importazione delle registrazioni descritta in [Esportare e modificare in blocco le registrazioni](https://msdn.microsoft.com/library/dn790624.aspx).
   > 
   > 
* **Service Bus Explorer**
   
    Molti clienti usano [Service Bus Explorer] per visualizzare e gestire il relativo hub di notifica. Service Bus Explorer è un progetto open source. Per esempi, vedere [codice Service Bus Explorer].

### <a name="verify-message-notifications"></a>Verificare le notifiche di messaggi
* **Portale di Azure**
   
    Per inviare una notifica di prova ai client senza un back-end di servizio attivo e in esecuzione, in **Supporto e risoluzione dei problemi** selezionare **Invio di prova**. 
   
    ![Testare la funzionalità di invio in Azure][7]
* **Visual Studio**
   
    È anche possibile inviare notifiche di prova da Visual Studio.
   
    ![Testare la funzionalità di invio in Visual Studio][10]
   
    Per altre informazioni sull'uso di Hub di notifica con Esplora server di Visual Studio, vedere questi articoli: 
   
   * [Come visualizzare le registrazioni di dispositivi per gli hub di notifica]
   * [Deep dive: Visual Studio 2013 Update 2 RC and Azure SDK 2.3] (Approfondimento: Visual Studio 2013 Update 2 RC e Azure SDK 2.3)
   * [Announcing release of Visual Studio 2013 Update 3 and Azure SDK 2.4] (Annuncio del rilascio di Visual Studio 2013 Update 3 e Azure SDK 2.4)

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Eseguire il debug di notifiche non riuscite ed esaminare l'esito della notifica
**Proprietà EnableTestSend**

Quando si invia una notifica tramite Hub di notifica, inizialmente, la notifica viene accodata per l'elaborazione in Hub di notifica. Hub di notifica determina le destinazioni corrette e quindi invia la notifica al servizio di notifica push. Se si usa l'API REST o un SDK client, l'esito corretto della chiamata di invio significa solo che il messaggio è stato inserito correttamente nella coda in Hub di notifica. Non sono disponibili altri dettagli su quanto effettivamente accaduto quando Hub di notifica ha infine inviato il messaggio al servizio di notifica push. 

Se la notifica non arriva al dispositivo client, è possibile che si sia verificato un errore durante il tentativo di Hub di notifica di recapitare il messaggio al servizio di notifica push. Ad esempio, le dimensioni del payload potrebbero superare il massimo consentito dal servizio di notifica push o le credenziali configurate in Hub di notifica potrebbero non essere valide. 

Per ottenere informazioni dettagliate sugli errori del servizio di notifica push, è possibile usare la proprietà [EnableTestSend]. Questa proprietà viene abilitata automaticamente quando si inviano messaggi di prova dal portale o dal client di Visual Studio e consente di visualizzare informazioni di debug dettagliate. È anche possibile usare la proprietà tramite le API. Attualmente, è possibile usarla in .NET SDK. È previsto che venga aggiunta a tutti gli SDK client. 

Per usare la proprietà **EnableTestSend** con la chiamata REST, aggiungere un parametro di stringa di query denominato *test* alla fine della chiamata di invio. Ad esempio:  

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

**Esempio (.NET SDK)**

Di seguito è riportato un esempio di uso di .NET SDK per inviare una notifica popup nativa:

```csharp
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);
```

Al termine dell'esecuzione, **result.State** indica semplicemente **Enqueued**. I risultati non offrono alcuna informazione su quanto accaduto alla notifica push. 

Successivamente, è possibile usare la proprietà booleana **EnableTestSend**. Usare la proprietà **EnableTestSend** mentre si inizializza **NotificationHubClient** per ottenere uno stato dettagliato degli errori del servizio di notifica push che si verificano quando viene inviata la notifica. La chiamata di invio impiega ulteriore tempo per restituire il controllo, perché ciò avviene solo dopo che Hub di notifica ha recapitato la notifica al servizio di notifica push per determinare il risultato. 

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

**Output di esempio**

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong

Questo messaggio indica che in Hub di notifica sono configurate credenziali non valide oppure è presente un problema per le registrazioni nell'hub. È consigliabile eliminare questa registrazione e consentire al client di creare nuovamente la registrazione prima di inviare il messaggio. 

> [!NOTE]
> L'uso della proprietà **EnableTestSend** è molto limitato. Usare questa opzione solo in un ambiente di sviluppo/test e con un numero limitato di registrazioni. Le notifiche di debug vengono inviate solo a 10 dispositivi. Inoltre è previsto un limite di 10 invii al minuto per l'elaborazione di debug. 
> 
> 

### <a name="review-telemetry"></a>Esaminare i dati di telemetria
* **Usare il portale di Azure**
   
    Nel portale è possibile ottenere una rapida panoramica di tutte le attività nell'hub di notifica. 
   
    1. Nella scheda **Panoramica** è disponibile una visualizzazione aggregata di registrazioni, notifiche ed errori in base alla piattaforma. 
   
        ![Dashboard di panoramica di Hub di notifica][5]
   
    2. Nella scheda **Monitoraggio** è possibile aggiungere molte altre metriche specifiche della piattaforma per un'analisi più approfondita. È possibile esaminare in particolare gli eventuali errori relativi al servizio di notifica push restituiti quando Hub di notifica tenta l'invio della notifica al servizio di notifica push. 
   
        ![Log attività del portale di Azure][6]
   
    3. Per iniziare, esaminare **Messaggi in ingresso**, **Operazioni di registrazione** e **Notifiche riuscite**. Passare quindi alla scheda per ogni piattaforma per esaminare gli errori specifici per il servizio di notifica push. 
   
    4. Se le impostazioni di autenticazione per l'hub di notifica non sono corrette, viene visualizzato il messaggio **Errore di autenticazione PNS**. Questo errore suggerisce appunto che è necessario controllare le credenziali del servizio di notifica push. 

* **Accesso a livello di codice**

    Per altre informazioni sull'accesso programmatico, vedere questi articoli: 

    * [Accesso alla telemetria a livello di codice]  
    * [Esempio di accesso alla telemetria tramite API] 

    > [!NOTE]
    > Diverse funzionalità correlate alla telemetria, come l'esportazione e importazione di registrazioni e l'accesso ai dati di telemetria tramite le API, sono disponibili solo con il livello di servizio Standard. Se si tenta di usare queste funzionalità dal livello di servizio Gratuito o Basic, viene visualizzato un messaggio di eccezione se si usa l'SDK e un errore HTTP 403 (accesso negato) se si usano le funzionalità direttamente dalle API REST. 
    >
    >Per usare le funzionalità correlate alla telemetria, verificare innanzitutto nel portale di Azure che sia in uso il livello di servizio Standard.  
> 
> 

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/FCMConfigure.png
[3]: ./media/notification-hubs-diagnosing/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[Panoramica dell'Hub di notifica]: notification-hubs-push-notification-overview.md
[Introduzione ad Hub di notifica di Azure]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Modelli]: https://msdn.microsoft.com/library/dn530748.aspx 
[APNs overview]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html (Panoramica del servizio di notifica push)
[About FCM messages]: https://firebase.google.com/docs/cloud-messaging/concept-options (Informazioni sui messaggi FCM)
[Export and modify registrations in bulk]: http://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer]: https://msdn.microsoft.com/library/dn530751.aspx#sb_explorer
[codice Service Bus Explorer]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Come visualizzare le registrazioni di dispositivi per gli hub di notifica]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[Deep dive: Visual Studio 2013 Update 2 RC and Azure SDK 2.3]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs  (Approfondimento: Visual Studio 2013 Update 2 RC e Azure SDK 2.3)
[Announcing release of Visual Studio 2013 Update 3 and Azure SDK 2.4]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/  (Annuncio del rilascio di Visual Studio 2013 Update 3 e Azure SDK 2.4)
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Accesso alla telemetria a livello di codice]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[Esempio di accesso alla telemetria tramite API]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

