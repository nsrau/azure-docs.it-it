---
title: Diagnosticare le notifiche eliminate in hub di notifica di Azure
description: Informazioni su come diagnosticare i problemi comuni relativi alle notifiche non recapitate in Hub di notifica di Azure.
services: notification-hubs
documentationcenter: Mobile
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 04/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/04/2019
ms.openlocfilehash: 3aaa99caca461d4b8e339cf4c1f7847adef4027a
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076854"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Diagnosticare le notifiche eliminate in hub di notifica di Azure

Una domanda comune sugli hub di notifica di Azure è la modalità di risoluzione dei problemi quando le notifiche provenienti da un'applicazione non vengono visualizzate nei dispositivi client. I clienti vogliono capire dove e perché sono state eliminate le notifiche e come risolvere il problema. Questo articolo illustra i motivi per cui le notifiche possono essere eliminate o non essere ricevute dai dispositivi. Viene inoltre illustrato come determinare la causa principale.

È prima di tutto fondamentale capire come viene eseguito il push delle notifiche a un dispositivo da Hub di notifica.

![Architettura di Hub di notifica][0]

In un flusso tipico di invio delle notifiche, il messaggio viene inviato dal *back-end dell'applicazione* ad Hub di notifica. Hub di notifica elabora tutte le registrazioni. Prende in considerazione i tag e le espressioni Tag configurati per determinare le destinazioni. Le destinazioni sono le registrazioni che devono ricevere la notifica push. Queste registrazioni possono estendersi a tutte le piattaforme supportate: Android, Baidu (dispositivi Android in Cina), Fire OS (Amazon) iOS, Windows e Windows Phone.

Con le destinazioni stabilite, Hub di notifica esegue il push delle notifiche al *servizio di notifica push* per la piattaforma del dispositivo. Gli esempi includono il servizio Apple Push Notification (APNs) per iOS e macOS e Firebase Cloud Messaging (FCM) per i dispositivi Android. Hub di notifica esegue il push delle notifiche suddivise in più batch di registrazioni. Esegue l'autenticazione con il rispettivo servizio di notifica push, in base alle credenziali impostate nel portale di Azure, in **configura Hub di notifica**. Il servizio di notifica push inoltra quindi le notifiche ai *dispositivi client* corrispondenti.

L'ultima parte del recapito delle notifiche è tra il servizio di notifica push della piattaforma e il dispositivo. Il recapito delle notifiche può avere esito negativo in una delle quattro fasi del processo di notifica push (client, back-end dell'applicazione, hub di notifica e il servizio di notifica push della piattaforma). Per altre informazioni sull'architettura di Hub di notifica, vedere [Panoramica dell'Hub di notifica].

Un errore di recapito delle notifiche potrebbe verificarsi durante la fase iniziale di test/gestione temporanea. Le notifiche non recapitate in questa fase potrebbero indicare un problema di configurazione. Se si verifica un errore di recapito delle notifiche in produzione, è possibile che vengano eliminate alcune o tutte le notifiche. In questo caso è indicato un problema più approfondito del modello di messaggistica o di applicazione.

Nella sezione successiva vengono esaminati gli scenari in cui è possibile che vengano eliminate le notifiche, da comuni a rare.

## <a name="notification-hubs-misconfiguration"></a>Configurazione errata di Hub di notifica ##

Per inviare notifiche al rispettivo servizio di notifica push, gli hub di notifica devono autenticarsi nel contesto dell'applicazione. È necessario creare un account sviluppatore con il servizio di notifica della piattaforma di destinazione (Microsoft, Apple, Google e così via). Quindi, è necessario registrare l'applicazione con il sistema operativo in cui si ottiene un token o una chiave che si usa per lavorare con il PNS di destinazione.

È necessario aggiungere le credenziali della piattaforma nel portale di Azure. Se nessuna notifica raggiunge il dispositivo, il primo passaggio consiste nel verificare che le credenziali corrette siano configurate in hub di notifica. Le credenziali devono corrispondere all'applicazione creata con un account sviluppatore specifico della piattaforma.

Per istruzioni dettagliate per eseguire questo processo, vedere [Introduzione ad Hub di notifica di Azure].

Ecco alcuni errori di configurazione comuni:

### <a name="notification-hub-name-location"></a>Percorso nome Hub di notifica

Assicurarsi che il nome dell'hub di notifica (privo di errori di digitazione) sia uguale in ognuna di queste posizioni:
   * Dove eseguire la registrazione dal client
   * Dove si inviano le notifiche dal back-end
   * Dove sono state configurate le credenziali del servizio di notifica push

Assicurarsi di usare le stringhe di configurazione corrette per la firma di accesso condiviso nel client e nel back-end dell'applicazione. In genere, è necessario usare **DefaultListenSharedAccessSignature** sul client e **DefaultFullSharedAccessSignature** nel back-end dell'applicazione. In questo modo vengono concesse le autorizzazioni per l'invio di notifiche a hub di notifica.

### <a name="apn-configuration"></a>Configurazione APN ###

È necessario gestire due hub diversi, uno per la produzione e un altro per il test. È necessario caricare il certificato usato in un ambiente sandbox in un hub separato rispetto al certificato/Hub che verrà usato in produzione. Non provare a caricare tipi diversi di certificati nello stesso hub. Verranno generati errori di notifica.

Se si caricano inavvertitamente tipi diversi di certificati nello stesso hub, è consigliabile eliminare l'hub e iniziare con un nuovo hub. Se per qualche motivo non è possibile eliminare l'hub, è necessario eliminare almeno tutte le registrazioni esistenti dall'hub.

### <a name="fcm-configuration"></a>Configurazione di FCM ###

1. Verificare che la *chiave del server* ottenuta da Firebase corrisponda alla chiave del server registrata nell'portale di Azure.

   ![Chiave server Firebase][3]

2. Assicurarsi di aver configurato l'**ID progetto** nel client. È possibile ottenere il valore per **ID progetto** dal dashboard Firebase.

   ![ID progetto Firebase][1]

## <a name="application-issues"></a>Problemi relativi all'applicazione ##

### <a name="tags-and-tag-expressions"></a>Tag ed espressioni Tag ###

Se si usano tag o espressioni tag per segmentare i destinatari, è possibile che quando si invia la notifica non venga trovata alcuna destinazione. Questo errore si basa sui tag o sulle espressioni tag specificati nella chiamata di invio.

Esaminare le registrazioni per assicurarsi che i tag corrispondano quando si invia una notifica. Verificare quindi la ricezione delle notifiche solo dai client che dispongono di tali registrazioni.

Si supponga, ad esempio, che tutte le registrazioni con hub di notifica usino il tag "politica". Se quindi si invia una notifica con il tag "sport", la notifica non verrà inviata a nessun dispositivo. Un caso complesso può coinvolgere espressioni di tag in cui è stato registrato usando "Tag A" *o* "tag b", ma è stato assegnato "tag a & & tag b". La sezione Suggerimenti per la diagnosi automatica più avanti in questo articolo illustra come rivedere le registrazioni e i relativi tag.

### <a name="template-issues"></a>Problemi relativi ai modelli ###

Se si usano modelli, assicurarsi di seguire le indicazioni riportate in [Modelli].

### <a name="invalid-registrations"></a>Registrazioni non valide ###

Se l'hub di notifica è stato configurato correttamente e i tag o le espressioni tag sono stati usati correttamente, vengono trovate destinazioni valide. Le notifiche devono essere inviate a queste destinazioni. Hub di notifica attiva quindi vari batch di elaborazione in parallelo. Ogni batch invia messaggi a un set di registrazioni.

> [!NOTE]
> Poiché Hub di notifica elabora i batch in parallelo, l'ordine in cui vengono recapitate le notifiche non è garantito.

Hub di notifica è ottimizzato per un modello di recapito dei messaggi "at-most-once". che comporta un tentativo di deduplicazione per fare in modo che nessuna notifica venga recapitata più di una volta a un dispositivo. Vengono controllate le registrazioni per assicurarsi che venga inviato un solo messaggio per ogni identificatore di dispositivo prima che venga inviato al servizio di notifica push.

Ogni batch viene inviato al servizio di notifica push, che a sua volta accetta e convalida le registrazioni. Durante questo processo, è possibile che il servizio di notifica push rilevi un errore con una o più registrazioni in un batch. Il servizio di notifica push restituisce quindi un errore a hub di notifica e il processo viene interrotto. Il servizio di notifica push elimina completamente tale batch. Ciò vale soprattutto per APNs, che usa un protocollo di flusso TCP.

In questo caso, la registrazione con errori viene rimossa dal database. Viene quindi eseguito un ulteriore tentativo di recapito per il resto dei dispositivi nel batch.

Per ottenere altre informazioni sull'errore sul tentativo di recapito non riuscito per una registrazione, è possibile usare le API REST di hub di notifica [per telemetria dei messaggi: ottenere i dati di telemetria del messaggio di notifica](https://msdn.microsoft.com/library/azure/mt608135.aspx) e il [feedback PNS](https://msdn.microsoft.com/library/azure/mt705560.aspx) Per un esempio di codice, vedere l'[esempio REST per l'invio](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/).

## <a name="push-notification-service-issues"></a>Problemi del servizio di notifica push

Una volta ricevuta la notifica, il servizio di notifica push recapita la notifica al dispositivo. A questo punto, hub di notifica non ha alcun controllo sul recapito della notifica al dispositivo.

Poiché i servizi di notifica della piattaforma sono affidabili, le notifiche tendono a raggiungere i dispositivi in pochi secondi. Se il servizio di notifica push prevede la limitazione, hub di notifica applica una strategia di back-off esponenziale. Se il servizio di notifica push rimane irraggiungibile per 30 minuti, si verifica un criterio per la scadenza e l'eliminazione permanente dei messaggi.

Se un servizio di notifica push tenta di recapitare una notifica, ma il dispositivo è offline, la notifica viene archiviata dal servizio di notifica push. Viene archiviato solo per un periodo di tempo limitato. e viene recapitata al dispositivo quando ritorna disponibile.

Ogni app archivia solo una notifica recente. Se vengono inviate più notifiche quando un dispositivo è offline, ogni nuova notifica causa l'eliminazione dell'ultimo. Mantenere solo la notifica più recente viene *chiamata Unione* in APNs e *compressione* in FCM. (FCM usa una chiave di compressione). Quando il dispositivo rimane offline per molto tempo, le notifiche archiviate per il dispositivo vengono scartate. Per ulteriori informazioni, vedere [APNs Overview] e [About FCM messages].

Con hub di notifica è possibile passare una chiave di Unione tramite un'intestazione HTTP usando l'API SendNotification generica. Ad esempio, `SendNotificationAsync` per .NET SDK. L'API SendNotification accetta anche le intestazioni HTTP che vengono passate come sono al rispettivo servizio di notifica push.

## <a name="self-diagnosis-tips"></a>Suggerimenti per l'autodiagnosi

Ecco i percorsi per diagnosticare la causa radice delle notifiche eliminate in hub di notifica.

### <a name="verify-credentials"></a>Verificare le credenziali ###

#### <a name="push-notification-service-developer-portal"></a>Portale per sviluppatori del servizio di notifica push ####

Verificare le credenziali nel rispettivo portale per sviluppatori del servizio di notifica push corrispondente (APN, FCM, il servizio di notifica di Windows e così via). Per altre informazioni, vedere [esercitazione: inviare notifiche a piattaforma UWP (Universal Windows Platform) app usando hub di notifica di Azure](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification).

#### <a name="azure-portal"></a>portale di Azure ####

Per esaminare e confrontare le credenziali con quelle ottenute dal portale per sviluppatori del servizio di notifica push, passare alla scheda **criteri di accesso** nella portale di Azure.

![Criteri di accesso del portale di Azure][4]

### <a name="verify-registrations"></a>Verificare le registrazioni

#### <a name="visual-studio"></a>Visual Studio ####

In Visual Studio è possibile connettersi ad Azure tramite Esplora server per visualizzare e gestire più servizi di Azure, tra cui Hub di notifica. Questo collegamento è particolarmente utile per l'ambiente di sviluppo/test.

![Esplora server di Visual Studio.][9]

È possibile visualizzare e gestire tutte le registrazioni nell'hub. Le registrazioni possono essere categorizzate in base alla piattaforma, alla registrazione nativa o del modello, al tag, all'identificatore del servizio di notifica push, all'ID registrazione e alla data di scadenza. È anche possibile modificare una registrazione in questa pagina. È particolarmente utile per la modifica dei tag.

Fare clic con il pulsante destro del mouse sull'hub di notifica in **Esplora server**e selezionare **diagnostica**. 

![Esplora server di Visual Studio: menu diagnosi](./media/notification-hubs-diagnosing/diagnose-menu.png)

Viene visualizzata la pagina seguente:

![Visual Studio: pagina diagnostica](./media/notification-hubs-diagnosing/diagnose-page.png)

Passa alla pagina **registrazioni del dispositivo** :

![Visual Studio: registrazioni del dispositivo](./media/notification-hubs-diagnosing/VSRegistrations.png)

Per inviare un messaggio di notifica di prova, è possibile usare la pagina di **invio del test** :

![Visual Studio: invio di test](./media/notification-hubs-diagnosing/test-send-vs.png)

> [!NOTE]
> Utilizzare Visual Studio per modificare le registrazioni solo durante lo sviluppo e il test e con un numero limitato di registrazioni. Se è necessario modificare le registrazioni in blocco, provare a usare la funzionalità di esportazione e importazione della registrazione descritta in [procedura: esportare e modificare le registrazioni in blocco](https://msdn.microsoft.com/library/dn790624.aspx).

#### <a name="service-bus-explorer"></a>Service Bus Explorer ####

Molti clienti usano [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) per visualizzare e gestire gli hub di notifica. Service Bus Explorer è un progetto open source. 

### <a name="verify-message-notifications"></a>Verificare le notifiche di messaggi

#### <a name="azure-portal"></a>portale di Azure ####

Per inviare una notifica di prova ai client senza un back-end di servizio attivo e in esecuzione, in **Supporto e risoluzione dei problemi** selezionare **Invio di prova**.

![Testare la funzionalità di invio in Azure][7]

#### <a name="visual-studio"></a>Visual Studio ####

È anche possibile inviare notifiche di prova da Visual Studio.

![Testare la funzionalità di invio in Visual Studio][10]

Per altre informazioni sull'uso di Hub di notifica con Esplora server di Visual Studio, vedere questi articoli:

* [Come visualizzare le registrazioni dei dispositivi per hub di notifica](https://docs.microsoft.com/previous-versions/windows/apps/dn792122(v=win.10))
* [Deep dive: Visual Studio 2013 Update 2 RC and Azure SDK 2.3] (Approfondimento: Visual Studio 2013 Update 2 RC e Azure SDK 2.3)
* [Announcing release of Visual Studio 2013 Update 3 and Azure SDK 2.4] (Annuncio del rilascio di Visual Studio 2013 Update 3 e Azure SDK 2.4)

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Eseguire il debug di notifiche non riuscite ed esaminare l'esito della notifica

#### <a name="enabletestsend-property"></a>Proprietà EnableTestSend ####

Quando si invia una notifica tramite hub di notifica, la notifica viene inizialmente accodata. Hub di notifica determina le destinazioni corrette e quindi invia la notifica al servizio di notifica push. Se si usa l'API REST o uno degli SDK client, il ritorno della chiamata di invio significa solo che il messaggio viene accodato con hub di notifica. Non fornisce informazioni sugli eventi che si sono verificati quando hub di notifica ha inviato la notifica al servizio di notifica push.

Se la notifica non arriva al dispositivo client, potrebbe essersi verificato un errore durante il tentativo di distribuzione dell'hub di notifica al servizio di notifica push. Ad esempio, le dimensioni del payload potrebbero superare il massimo consentito dal servizio di notifica push o le credenziali configurate in Hub di notifica potrebbero non essere valide.

Per ottenere informazioni dettagliate sugli errori del servizio di notifica push, è possibile usare la proprietà [EnableTestSend]. Questa proprietà viene abilitata automaticamente quando si inviano messaggi di prova dal portale o dal client di Visual Studio È possibile usare questa proprietà per visualizzare informazioni di debug dettagliate e anche tramite API. Attualmente, è possibile usarla in .NET SDK. Verrà aggiunto a tutti gli SDK client.

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

Al termine dell'esecuzione, `result.State` indica semplicemente `Enqueued`. I risultati non forniscono informazioni dettagliate su ciò che è successo alla notifica push.

Successivamente, è possibile usare la proprietà booleana `EnableTestSend`. Usare la proprietà `EnableTestSend` mentre si inizializza `NotificationHubClient` per ottenere uno stato dettagliato degli errori del servizio di notifica push che si verificano quando viene inviata la notifica. La chiamata di invio richiede ulteriore tempo per la restituzione, perché per prima cosa necessita di hub di notifica per recapitare la notifica al servizio di notifica push.

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

Questo messaggio indica che le credenziali configurate in hub di notifica non sono valide o che si è verificato un problema con le registrazioni nell'hub. Eliminare questa registrazione e lasciare che il client crei nuovamente la registrazione prima di inviare il messaggio.

> [!NOTE]
> L'uso della proprietà `EnableTestSend` è molto limitato. Usare questa opzione solo in un ambiente di sviluppo/test e con un set limitato di registrazioni. Le notifiche di debug vengono inviate solo a 10 dispositivi. È anche previsto un limite per l'elaborazione degli invii di debug, a 10 al minuto.

### <a name="review-telemetry"></a>Esaminare i dati di telemetria ###

#### <a name="azure-portal"></a>portale di Azure ####

Nel portale è possibile ottenere una rapida panoramica di tutte le attività nell'hub di notifica.

1. Nella scheda **Panoramica** è disponibile una visualizzazione aggregata di registrazioni, notifiche ed errori in base alla piattaforma.

   ![Dashboard di panoramica di Hub di notifica][5]

2. Nella scheda **Monitoraggio** è possibile aggiungere molte altre metriche specifiche della piattaforma per un'analisi più approfondita. È possibile esaminare in modo specifico gli errori restituiti quando hub di notifica tenta di inviare la notifica al servizio di notifica push.

   ![Log attività del portale di Azure][6]

3. Per iniziare, esaminare **Messaggi in ingresso**, **Operazioni di registrazione** e **Notifiche riuscite**. Passare quindi alla scheda per ogni piattaforma per esaminare gli errori specifici per il servizio di notifica push.

4. Se le impostazioni di autenticazione per l'hub di notifica non sono corrette, viene visualizzato il messaggio **Errore di autenticazione PNS**. È opportuno controllare le credenziali del servizio di notifica push.

#### <a name="programmatic-access"></a>Accesso a livello di codice ####

Per ulteriori informazioni sull'accesso a livello di codice, vedere [accesso a livello di codice](https://docs.microsoft.com/previous-versions/azure/azure-services/dn458823(v=azure.100)).

> [!NOTE]
> Diverse funzionalità correlate alla telemetria, come l'esportazione e importazione di registrazioni e l'accesso ai dati di telemetria tramite le API, sono disponibili solo con il livello di servizio Standard. Se si tenta di usare queste funzionalità dal livello di servizio gratuito o Basic, viene ricevuto un messaggio di eccezione se si usa l'SDK. Se si usano le funzionalità direttamente dalle API REST, si otterrà un errore HTTP 403 (accesso negato).
>
> Per usare le funzionalità correlate alla telemetria, assicurarsi prima di tutto nella portale di Azure che si sta usando il livello di servizio standard.  

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
[Deep dive: Visual Studio 2013 Update 2 RC and Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs (Approfondimento: Visual Studio 2013 Update 2 RC e Azure SDK 2.3)
[Announcing release of Visual Studio 2013 Update 3 and Azure SDK 2.4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ (Annuncio del rilascio di Visual Studio 2013 Update 3 e Azure SDK 2.4)
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmatic telemetry access]: https://msdn.microsoft.com/library/azure/dn458823.aspx
