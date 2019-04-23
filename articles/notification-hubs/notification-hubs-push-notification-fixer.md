---
title: Diagnosticare le notifiche non recapitate in hub di notifica di Azure
description: Informazioni su come diagnosticare i problemi comuni relativi alle notifiche non recapitate in Hub di notifica di Azure.
services: notification-hubs
documentationcenter: Mobile
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 04/04/2019
ms.author: jowargo
ms.openlocfilehash: 4fc4175c03baa4ddb81507dd4001fcdbe7c7058b
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149547"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Diagnosticare le notifiche non recapitate in hub di notifica di Azure

Una domanda comune sull'hub di notifica di Azure viene illustrato come risolvere i problemi quando le notifiche da un'applicazione non vengono visualizzate nei dispositivi client. I clienti desiderano sapere dove e perché sono state eliminate le notifiche e come risolvere il problema. Questo articolo illustra i motivi per cui le notifiche possono essere eliminate o non essere ricevute dai dispositivi. Viene inoltre spiegato come determinare la causa radice.

È prima di tutto fondamentale capire come viene eseguito il push delle notifiche a un dispositivo da Hub di notifica.

![Architettura di Hub di notifica][0]

In un flusso tipico di invio delle notifiche, il messaggio viene inviato dal *back-end dell'applicazione* ad Hub di notifica. Hub di notifica elabora tutte le registrazioni. Prende in considerazione i tag configurati e le espressioni tag per determinare le destinazioni. Le destinazioni sono le registrazioni che devono ricevere la notifica push. Queste registrazioni possono coprire alcune le piattaforme supportate: Android, Baidu (per dispositivi Android in Cina), del sistema operativo Fire (Amazon) iOS, Windows e Windows Phone.

Con le destinazioni stabilite, Hub di notifica esegue il push delle notifiche al *servizio di notifica push* per la piattaforma del dispositivo. Alcuni esempi sono il servizio Apple Push Notification (APN) per Apple e Firebase Cloud Messaging (FCM) per Google. Hub di notifica esegue il push delle notifiche suddivise in più batch di registrazioni. Esegue l'autenticazione con il servizio di notifica push corrispondente, in base alle credenziali impostate nel portale di Azure, in **Configura Hub di notifica**. Il servizio di notifica push inoltra quindi le notifiche ai *dispositivi client* corrispondenti.

L'ultima parte del recapito delle notifiche è compreso tra il servizio di notifica push della piattaforma e dispositivo. Recapito delle notifiche può non riuscire in una delle quattro fasi del processo di notifica push (client, back-end applicazione, hub di notifica e servizio di notifica push della piattaforma). Per altre informazioni sull'architettura di Hub di notifica, vedere [Panoramica dell'Hub di notifica].

Potrebbe verificarsi un errore per il recapito delle notifiche durante l'iniziale fase di test/staging. Le notifiche non recapitate in questa fase potrebbero indicare un problema di configurazione. Se si verifica un errore di recapito delle notifiche nell'ambiente di produzione, potrebbero essere eliminate alcune o tutte le notifiche. Un'applicazione più profonda o problema di modello di messaggistica è indicato in questo caso.

Nella sezione successiva vengono esaminati gli scenari in cui potrebbero essere eliminate le notifiche, compreso tra comuni e rare.

## <a name="notification-hubs-misconfiguration"></a>Configurazione errata di Hub di notifica ##

Per inviare notifiche al servizio di notifica push corrispondente, hub di notifica deve autenticarsi nel contesto dell'applicazione. È necessario creare un account sviluppatore con servizio di notifica della piattaforma di destinazione (Microsoft, Apple, Google e così via). Quindi, è necessario registrare l'applicazione con il sistema operativo in cui è ottenere un token o una chiave che usa per lavorare con il PNS di destinazione.

È necessario aggiungere le credenziali della piattaforma nel portale di Azure. Se non le notifiche raggiungono il dispositivo, il primo passaggio è assicurarsi che le credenziali corrette siano configurate in hub di notifica. Le credenziali devono corrispondere all'applicazione creata in base a un account sviluppatore specifico della piattaforma.

Per istruzioni dettagliate per eseguire questo processo, vedere [Introduzione ad Hub di notifica di Azure].

Ecco alcuni errori di configurazione comuni:

### <a name="notification-hub-name-location"></a>Percorso nome hub di notifica

Assicurarsi che il nome dell'hub di notifica (privo di errori di digitazione) sia uguale in ognuna di queste posizioni:
   * Posizione di registrazione dal client
   * Dove si inviare notifiche dal back-end
   * In cui è stato configurato le credenziali del servizio notifica push

Assicurarsi di usare le stringhe di configurazione di firma di accesso condiviso corrette nel client e il back-end applicazioni. In generale, è necessario utilizzare **DefaultListenSharedAccessSignature** sul client e **DefaultFullSharedAccessSignature** sull'applicazione back-end. In questo modo si concede autorizzazioni per l'invio di notifiche ad hub di notifica.

### <a name="apn-configuration"></a>Configurazione APN ###

È necessario gestire due hub diversi: uno per la produzione e l'altro per il test. È necessario caricare il certificato usato in un ambiente sandbox per un hub separato il certificato/hub che si userà in fase di produzione. Non provare a caricare tipi diversi di certificati nello stesso hub. Causerà errori di notifica.

Se si caricano inavvertitamente tipi diversi di certificati nello stesso hub, è necessario eliminare l'hub e ricominciare con un nuovo hub. Se per qualche motivo è possibile eliminare l'hub, è almeno necessario eliminare tutte le registrazioni esistenti dall'hub.

### <a name="fcm-configuration"></a>Configurazione di FCM ###

1. Verificare che il *chiave server* ottenuti dalle corrispondenze Firebase la chiave del server è stato registrato nel portale di Azure.

   ![Chiave server Firebase][3]

2. Assicurarsi di aver configurato l'**ID progetto** nel client. È possibile ottenere il valore per **ID progetto** dal dashboard Firebase.

   ![ID progetto Firebase][1]

## <a name="application-issues"></a>Problemi relativi all'applicazione ##

### <a name="tags-and-tag-expressions"></a>I tag ed espressioni tag ###

Se si usano tag o espressioni tag per segmentare i destinatari, è possibile che, quando si invia la notifica, non viene trovata alcuna destinazione. Questo errore si basa sul tag specificati o espressioni tag nella chiamata di invio.

Esaminare le registrazioni per assicurarsi che i tag corrispondono quando si invia una notifica. Verificare quindi la ricezione delle notifiche da solo i client che con queste registrazioni.

Ad esempio, si supponga che tutte le registrazioni con hub di notifica di usano il tag "Politica". Se si quindi invia una notifica con il tag "Sport", la notifica non verrà inviata a qualsiasi dispositivo. Un caso complesso espressioni tag in cui è registrato con "Tag A" è quello *o* "Tag B", ma è destinata "Tag A & & Tag b". La sezione dei suggerimenti per l'autodiagnosi più avanti in questo articolo illustra come esaminare le registrazioni e i relativi tag.

### <a name="template-issues"></a>Problemi del modello ###

Se si usano modelli, assicurarsi di seguire le indicazioni riportate in [Modelli].

### <a name="invalid-registrations"></a>Registrazioni non valide ###

Se l'hub di notifica è stato configurato correttamente e i tag o espressioni tag sono state usate correttamente, vengono trovate destinazioni valide. Le notifiche devono essere inviate a queste destinazioni. Hub di notifica attiva quindi vari batch di elaborazione in parallelo. Ogni batch invia messaggi a un set di registrazioni.

> [!NOTE]
> Poiché gli hub di notifica elabora i batch in parallelo, l'ordine in cui le notifiche vengono recapitate non è garantito.

Hub di notifica è ottimizzato per un modello di recapito di messaggi "at-most-once". che comporta un tentativo di deduplicazione per fare in modo che nessuna notifica venga recapitata più di una volta a un dispositivo. Le registrazioni vengono controllate per garantire che solo uno messaggio venga inviato per ID dispositivo prima che venga inviato al servizio di notifica push.

Ogni batch viene inviato al servizio di notifica push, che a sua volta accetta e convalida le registrazioni. Durante questo processo, è possibile che il servizio di notifica push rilevi un errore con uno o più registrazioni in un batch. Il servizio di notifica push restituisce quindi un errore ad hub di notifica e il processo viene arrestato. Il servizio di notifica push elimina completamente tale batch. Ciò è particolarmente vero con il servizio APN, che usa un protocollo di flusso TCP.

In questo caso, il rimuoviamo la registrazione viene rimosso dal database. Viene quindi eseguito un ulteriore tentativo di recapito per il resto dei dispositivi nel batch.

Per ottenere ulteriori informazioni sul tentativo di recapito non riuscito su una registrazione, è possibile usare l'API REST di hub di notifica [telemetria Per messaggio: Ottenere la telemetria di messaggio di notifica](https://msdn.microsoft.com/library/azure/mt608135.aspx) e [feedback PNS](https://msdn.microsoft.com/library/azure/mt705560.aspx). Per un esempio di codice, vedere l'[esempio REST per l'invio](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/).

## <a name="push-notification-service-issues"></a>Problemi del servizio di notifica push

Dopo che il servizio di notifica push riceve la notifica, invia la notifica al dispositivo. A questo punto, hub di notifica ha alcun controllo sul recapito della notifica al dispositivo.

Poiché i servizi di notifica della piattaforma sono affidabili, le notifiche raggiungono tendenzialmente i dispositivi in pochi secondi. Se il servizio di notifica push è limitato, hub di notifica applica una strategia con backoff esponenziale. Se il servizio di notifica push rimane non raggiungibile per 30 minuti, è disponibile un criterio possono scadere ed eliminare i messaggi in modo permanente.

Se un servizio di notifica push tenta di recapitare una notifica, ma il dispositivo è offline, la notifica viene archiviata dal servizio di notifica push. Viene archiviata solo per un limitato periodo di tempo. e viene recapitata al dispositivo quando ritorna disponibile.

Ogni app archivia una sola notifica recente. Se vengono inviate più notifiche quando un dispositivo è offline, ogni nuova notifica determina quello più recente essere eliminato. Mantenuta solo la notifica più recente viene chiamato *coalescing* nel servizio APN e *compressione* in FCM. (FCM Usa una chiave di compressione). Quando il dispositivo rimane offline a lungo, vengono eliminate le notifiche archiviate per il dispositivo. Per altre informazioni, vedere [APNs Overview] e [About FCM messages].

Con hub di notifica, è possibile passare una chiave di unione tramite un'intestazione HTTP mediante l'API SendNotification generica. Ad esempio, `SendNotificationAsync` per .NET SDK. L'API SendNotification accetta anche le intestazioni HTTP passate così come il servizio di notifica push corrispondente.

## <a name="self-diagnosis-tips"></a>Suggerimenti per l'autodiagnosi

Ecco i percorsi per diagnosticare la causa principale di notifiche non recapitate in hub di notifica.

### <a name="verify-credentials"></a>Verificare le credenziali ###

#### <a name="push-notification-service-developer-portal"></a>Portale per sviluppatori del servizio notifica push ####

Verificare le credenziali nel rispettivo portale per sviluppatori del servizio di notifica push corrispondente (APN, FCM, il servizio di notifica di Windows e così via). Per altre informazioni, vedere [Esercitazione: Inviare notifiche alle app della piattaforma UWP (Universal Windows Platform) con Hub di notifica di Azure](https://docs.microsoft.com/en-us/azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification).

#### <a name="azure-portal"></a>Portale di Azure ####

Per esaminare e confrontare le credenziali con quelli ottenuti dal portale per sviluppatori del servizio di notifica push, vedere la **criteri di accesso** scheda nel portale di Azure.

![Criteri di accesso del portale di Azure][4]

### <a name="verify-registrations"></a>Verificare le registrazioni

#### <a name="visual-studio"></a>Visual Studio ####

In Visual Studio, è possibile connettersi ad Azure tramite Esplora Server per visualizzare e gestire più servizi di Azure, incluso hub di notifica. Questo collegamento è particolarmente utile per l'ambiente di sviluppo/test.

![Esplora server di Visual Studio.][9]

È possibile visualizzare e gestire tutte le registrazioni nell'hub. Le registrazioni possono essere suddivisi in categorie dalla piattaforma, native o modello di registrazione, tag, identificatore del servizio notifica push, ID di registrazione e la data di scadenza. È anche possibile modificare una registrazione in questa pagina. È particolarmente utile per la modifica di tag.

Fare doppio clic su hub di notifica nel **Esplora Server**e selezionare **diagnosi**. 

![Visual Studio Server Explorer: Diagnosticare i menu](./media/notification-hubs-diagnosing/diagnose-menu.png)

Viene visualizzata la pagina seguente:

![Visual Studio: Diagnosticare pagina](./media/notification-hubs-diagnosing/diagnose-page.png)

Passare al **le registrazioni dei dispositivi** pagina:

![Visual Studio: Registrazioni dei dispositivi](./media/notification-hubs-diagnosing/VSRegistrations.png)

È possibile usare **invio di prova** per inviare un messaggio di notifica di prova:

![Visual Studio: Testare l'invio](./media/notification-hubs-diagnosing/test-send-vs.png)

> [!NOTE]
> Usare Visual Studio per modificare le registrazioni solo durante lo sviluppo e test e con un numero limitato di registrazioni. Se è necessario modificare le registrazioni in blocco, è consigliabile usare l'esportazione e importazione di funzionalità di registrazione descritto in [How To: Esportare e modificare registrazioni in blocco](https://msdn.microsoft.com/library/dn790624.aspx).

#### <a name="service-bus-explorer"></a>Service Bus Explorer ####

Molti clienti usano [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) per visualizzare e gestire hub di notifica di loro. Service Bus Explorer è un progetto open source. 

### <a name="verify-message-notifications"></a>Verificare le notifiche di messaggi

#### <a name="azure-portal"></a>Portale di Azure ####

Per inviare una notifica di prova ai client senza un back-end di servizio attivo e in esecuzione, in **Supporto e risoluzione dei problemi** selezionare **Invio di prova**.

![Testare la funzionalità di invio in Azure][7]

#### <a name="visual-studio"></a>Visual Studio ####

È anche possibile inviare notifiche di prova da Visual Studio.

![Testare la funzionalità di invio in Visual Studio][10]

Per altre informazioni sull'uso di Hub di notifica con Esplora server di Visual Studio, vedere questi articoli:

* [Come visualizzare le registrazioni dei dispositivi per hub di notifica](https://docs.microsoft.com/en-us/previous-versions/windows/apps/dn792122(v=win.10))
* [Approfondimento: Visual Studio 2013 Update 2 RC and Azure SDK 2.3] (Approfondimento: Visual Studio 2013 Update 2 RC e Azure SDK 2.3)
* [Announcing release of Visual Studio 2013 Update 3 and Azure SDK 2.4] (Annuncio del rilascio di Visual Studio 2013 Update 3 e Azure SDK 2.4)

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Eseguire il debug di notifiche non riuscite ed esaminare l'esito della notifica

#### <a name="enabletestsend-property"></a>Proprietà EnableTestSend ####

Quando si invia una notifica tramite hub di notifica, inizialmente è in coda la notifica. Hub di notifica determina le destinazioni corrette e quindi invia la notifica al servizio di notifica push. Se si usa l'API REST o uno qualsiasi degli SDK dei client, l'esito della chiamata di invio significa solo che il messaggio viene accodato con hub di notifica. Non fornisce informazioni su quanto effettivamente accaduto quando hub di notifica inviato alla fine della notifica al servizio di notifica push.

Se la notifica non arriva al dispositivo client, potrebbe essersi verificato un errore durante il tentativo di fornirli al servizio di notifica push con hub di notifica. Ad esempio, le dimensioni del payload potrebbero superare il massimo consentito dal servizio di notifica push o le credenziali configurate in Hub di notifica potrebbero non essere valide.

Per ottenere informazioni dettagliate sugli errori del servizio di notifica push, è possibile usare la proprietà [EnableTestSend]. Questa proprietà viene abilitata automaticamente quando si inviano messaggi di prova dal portale o dal client di Visual Studio È possibile usare questa proprietà per visualizzare informazioni di debug dettagliate e anche tramite le API. Attualmente, è possibile usarla in .NET SDK. Verrà aggiunto a tutti gli SDK client alla fine.

Per usare la proprietà `EnableTestSend` con la chiamata REST, aggiungere un parametro di stringa di query denominato *test* alla fine della chiamata di invio. Ad esempio: 

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>Esempio di .NET SDK ####

Di seguito è riportato un esempio di uso di .NET SDK per inviare una notifica popup nativa:

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

Al termine dell'esecuzione, `result.State` indica semplicemente `Enqueued`. I risultati non offrono alcuna informazione su quanto effettivamente accaduto alla notifica push.

Successivamente, è possibile usare la proprietà booleana `EnableTestSend`. Usare la proprietà `EnableTestSend` mentre si inizializza `NotificationHubClient` per ottenere uno stato dettagliato degli errori del servizio di notifica push che si verificano quando viene inviata la notifica. La chiamata di invio impiega ulteriore tempo per restituire perché è necessario prima di tutto gli hub di notifica per recapitare la notifica al servizio di notifica push.

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

#### <a name="sample-output"></a>Output di esempio ####

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Questo messaggio indica che le credenziali configurate in hub di notifica non sono validi o che si verifica un problema con le registrazioni nell'hub. Odstranit tuto registraci e consentire al client di creare nuovamente la registrazione prima di inviare il messaggio.

> [!NOTE]
> L'uso della proprietà `EnableTestSend` è molto limitato. Usare questa opzione solo in un ambiente di sviluppo/test e con un set limitato di registrazioni. Eseguire il debug le notifiche vengono inviate solo a 10 dispositivi. È anche previsto un limite sul debug invii, 10 minuto l'elaborazione.

### <a name="review-telemetry"></a>Esaminare i dati di telemetria ###

#### <a name="azure-portal"></a>Portale di Azure ####

Nel portale è possibile ottenere una rapida panoramica di tutte le attività nell'hub di notifica.

1. Nella scheda **Panoramica** è disponibile una visualizzazione aggregata di registrazioni, notifiche ed errori in base alla piattaforma.

   ![Dashboard di panoramica di Hub di notifica][5]

2. Nella scheda **Monitoraggio** è possibile aggiungere molte altre metriche specifiche della piattaforma per un'analisi più approfondita. È possibile esaminare in modo specifico gli errori restituiti quando hub di notifica di prova a inviare la notifica al servizio di notifica push.

   ![Log attività del portale di Azure][6]

3. Per iniziare, esaminare **Messaggi in ingresso**, **Operazioni di registrazione** e **Notifiche riuscite**. Passare quindi alla scheda per ogni piattaforma per esaminare gli errori specifici per il servizio di notifica push.

4. Se le impostazioni di autenticazione per l'hub di notifica non sono corrette, viene visualizzato il messaggio **Errore di autenticazione PNS**. È una buona indicazione per verificare le credenziali del servizio notifica push.

#### <a name="programmatic-access"></a>Accesso a livello di codice ####

Per altre informazioni sull'accesso a livello di codice, vedere [programmatica accesso](https://docs.microsoft.com/en-us/previous-versions/azure/azure-services/dn458823(v=azure.100)).

> [!NOTE]
> Diverse funzionalità correlate alla telemetria, come l'esportazione e importazione di registrazioni e l'accesso ai dati di telemetria tramite le API, sono disponibili solo con il livello di servizio Standard. Se si prova a usare queste funzionalità dal piano gratuito o Basic livello di servizio, si riceverà un messaggio di eccezione se si usa il SDK. Se si usano le funzionalità direttamente dalle API REST, si otterrà un errore HTTP 403 (accesso negato).
>
> Per usare le funzionalità correlate alla telemetria, verificare innanzitutto nel portale di Azure che si usi il livello di servizio Standard.  

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
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Approfondimento: Visual Studio 2013 Update 2 RC and Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs (Approfondimento: Visual Studio 2013 Update 2 RC e Azure SDK 2.3)
[Announcing release of Visual Studio 2013 Update 3 and Azure SDK 2.4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ (Annuncio del rilascio di Visual Studio 2013 Update 3 e Azure SDK 2.4)
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmatic telemetry access]: https://msdn.microsoft.com/library/azure/dn458823.aspx
