---
title: Hub di notifica di Azure eliminato diagnosi di notifica
description: Come diagnosticare i problemi comuni relativi a notifiche ignorate nell'hub di notifica di Azure.
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
ms.openlocfilehash: 47808fcdb419dd44cfbd19ac7882b78b4c846b2c
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="diagnose-dropped-notifications-in-notification-hubs"></a>Diagnosticare le notifiche di ignorati nell'hub di notifica

Una delle domande più comuni dei clienti di hub di notifica di Azure viene illustrato come risolvere i problemi quando le notifiche inviate da un'applicazione non vengono visualizzati nei dispositivi client. Desiderano sapere dove e perché sono state eliminate le notifiche e come risolvere il problema. In questo articolo identifica il motivo per cui le notifiche, potrebbe essere eliminate o non essere ricevute dai dispositivi. Informazioni su come analizzare e determinare la causa principale. 

È fondamentale per capire come hub di notifica push notifiche a un dispositivo.

![Architettura degli hub di notifica][0]

In un flusso di notifica di trasmissione tipico, il messaggio viene inviato dal *back-end dell'applicazione* agli hub di notifica. Gli hub di notifica esegue alcune operazioni di elaborazione in tutte le registrazioni. L'elaborazione prende in considerazione il tag configurato e le espressioni tag per determinare "target". Le destinazioni sono tutte le registrazioni che devono ricevere la notifica push. Queste registrazioni possono estendersi su uno qualsiasi o tutte le piattaforme supportate nostro: iOS, Google, Windows, Windows Phone, Kindle e Baidu per Android Cina.

Con le destinazioni stabilite, gli hub di notifica push le notifiche di *servizio di notifica push* per la piattaforma del dispositivo. Esempi includono il servizio Apple Push Notification (APN) per la messaggistica Cloud Firebase (FCM) di Apple e per Google. Le notifiche push degli hub di notifica è suddiviso in più batch di registrazioni. Gli hub di notifica autentica con il servizio di notifica push corrispondente in base alle credenziali impostate nel portale di Azure, in **configurare Hub di notifica**. La notifica push del servizio quindi inoltrare le notifiche ai rispettivi *dispositivi client*. 

Si noti che la parte finale del recapito delle notifiche avviene tra il servizio di notifica push di piattaforma e il dispositivo. Uno dei quattro componenti principali del processo di notifica push (client back-end dell'applicazione, gli hub di notifica e il servizio di notifica push di piattaforma) potrebbe causare notifiche da eliminare. Per ulteriori informazioni sull'architettura degli hub di notifica, vedere [Panoramica di hub di notifica].

Impossibile recapitare le notifiche potrebbe verificarsi durante il primo test o gestione temporanea fase. Le notifiche eliminate in questa fase potrebbero indicare un problema di configurazione. In caso di errore per il recapito delle notifiche nell'ambiente di produzione, alcune o tutte le notifiche potrebbero essere rimossi. In questo caso, un'applicazione più approfondita o problema di modello di messaggistica è indicata. 

La sezione successiva vengono esaminati gli scenari in cui potrebbero essere rimossi notifiche, compreso fra comuni e più rari.

## <a name="notification-hubs-misconfiguration"></a>Configurazione errata degli hub di notifica
Per inviare notifiche per il servizio di notifica push corrispondente, gli hub di notifica deve autenticarsi nel contesto dell'applicazione dello sviluppatore. A questo scopo, lo sviluppatore crea un account sviluppatore, con la piattaforma corrispondente (Google, Apple, Windows e così via). Quindi, lo sviluppatore registra l'applicazione con la piattaforma dove ottenere le credenziali. 

È necessario aggiungere le credenziali di piattaforma per il portale di Azure. Se nessuna notifica raggiungono il dispositivo, per garantire che le credenziali corrette siano configurate in hub di notifica deve essere il primo passaggio. Le credenziali devono corrispondere l'applicazione che viene creato in un account sviluppatore specifico della piattaforma. 

Per istruzioni dettagliate completare questo processo, vedere [iniziare con gli hub di notifica di Azure].

Ecco alcune configurazioni errate da cercare:

* **Generale**
   
    * Verificare che il nome di hub di notifica (senza errori di digitazione) sia lo stesso in ognuna di queste posizioni:

        * In cui registrare dal client.
        * In cui si inviano notifiche da back-end.
        * Quale sono state configurate le credenziali del servizio notifica push.
    
    * Assicurarsi di usare le stringhe di configurazione di firma di accesso condiviso corretto sul client e nel back-end dell'applicazione. In genere, è necessario utilizzare **DefaultListenSharedAccessSignature** sul client e **DefaultFullSharedAccessSignature** sull'applicazione back-end (concede le autorizzazioni per inviare notifiche a Hub di notifica).

* **Configurazione servizio APN**
   
    È necessario mantenere due hub diverse: un hub per la produzione e un altro hub di test. Ciò significa che è necessario caricare il certificato che utilizza in un ambiente sandbox per un hub separato rispetto a un hub che si intende utilizzare nell'ambiente di produzione e il certificato. Non provare a caricare i tipi diversi di certificati allo stesso hub. Ciò potrebbe causare errori di notifica. 
    
    Se si carica inavvertitamente tipi diversi di certificati allo stesso hub, è consigliabile eliminare l'hub e di iniziare con un nuovo hub. Se per qualche motivo non è possibile eliminare l'hub, come minimo, è necessario eliminare tutte le registrazioni esistenti dall'hub. 

* **Configurazione FCM** 
   
    1. Verificare che il *chiave server* ottenuta Firebase corrisponde alla chiave del server che è stato registrato nel portale di Azure.
   
    ![Chiave del server firebase][3]
   
    2. Assicurarsi di aver configurato **ID progetto** sul client. È possibile ottenere il valore per **ID progetto** dal dashboard Firebase.
   
    ![ID progetto firebase][1]

## <a name="application-issues"></a>Problemi relativi all'applicazione
* **Tag e le espressioni tag**

    Se si utilizza il tag o espressioni tag per segmentare i destinatari, è possibile che quando si invia la notifica, nessuna destinazione si trova in base ai tag o espressioni tag specificati nella chiamata a send. 
    
    Esaminare le registrazioni per verificare che siano presenti tag corrispondenti quando si invia una notifica. Verificare quindi la ricezione di notifiche solo da client che dispongono di registrazioni. 
    
    Ad esempio, se sono state apportate tutte le registrazioni con gli hub di notifica tramite il tag "Politica" e si invia una notifica con il tag "Sport", la notifica non viene inviata a tutti i dispositivi. Un caso complesso potrebbe includere espressioni tag in cui è registrato con "Tag A" o "Tag B", ma durante l'invio di notifiche, la destinazione è "Tag A & & Tag b.." Nella sezione suggerimenti autocontrollo avanti in questo articolo, è illustrato come controllare le registrazioni e i relativi tag. 

* **Problemi relativi ai modelli**

    Se si utilizzano i modelli, assicurarsi di seguire le istruzioni riportate in [modelli]. 

* **Registrazioni non valide**

    Se l'hub di notifica è stato configurato correttamente e se qualsiasi tag o espressioni tag utilizzate correttamente, vengono trovate destinazioni valide. Notifiche da inviare a queste destinazioni. Gli hub di notifica genera quindi l'elaborazione di numerosi batch in parallelo. Ogni batch invia messaggi a un set di registrazioni. 

    > [!NOTE]
    > Poiché l'elaborazione viene eseguita in parallelo, l'ordine in cui vengono recapitate le notifiche non è garantito. 

    Gli hub di notifica è ottimizzata per un modello di recapito del messaggio "in most once". Si tenta la deduplicazione, in modo che non le notifiche vengono recapitate più volte in un dispositivo. A tale scopo, è controllare le registrazioni e assicurarsi che solo un messaggio viene inviato per l'identificatore del dispositivo prima che il messaggio viene inviato al servizio di notifica push. 

    In ogni batch viene inviato al servizio di notifica push, che a sua volta viene accettato e convalida le registrazioni, è possibile che il servizio di notifica push rileva un errore con uno o più delle registrazioni in un batch. In questo caso, il servizio di notifica push restituisce un errore per gli hub di notifica e il processo viene arrestato. Il servizio di notifica push elimina completamente tale batch. Ciò è particolarmente vero con il servizio APN, che utilizza un protocollo di flusso TCP. 

    Ci stiamo ottimizzati per in più recapito una volta. Ma in questo caso, la registrazione di errore viene rimosso dal database. Quindi, abbiamo ripetere recapito delle notifiche per il resto dei dispositivi nel batch.

    Per ottenere ulteriori informazioni sugli errori di mancato recapito tentativo su una registrazione, è possibile utilizzare le API REST degli hub di notifica [per ogni messaggio di telemetria: ottenere dati di telemetria messaggio di notifica](https://msdn.microsoft.com/library/azure/mt608135.aspx) e [feedback PNS](https://msdn.microsoft.com/library/azure/mt705560.aspx). Per esempio di codice, vedere il [esempio REST Invia](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample).

## <a name="push-notification-service-issues"></a>I problemi del servizio notifica push
Dopo la ricezione del messaggio di notifica dal servizio di notifica push di piattaforma, è responsabilità del servizio di notifica push per recapitare la notifica al dispositivo. A questo punto, gli hub di notifica non rientra nell'immagine e non ha alcun controllo su quando o se la notifica viene recapitata al dispositivo. 

Poiché i servizi di notifica della piattaforma sono affidabili, le notifiche tendono a raggiungere i dispositivi dal servizio di notifica push in pochi secondi. Se la limitazione delle richieste del servizio di notifica push, gli hub di notifica applica una strategia con backoff esponenziale. Se il servizio di notifica push rimane non raggiungibile per 30 minuti, abbiamo un criterio in grado di scadenza ed eliminare i messaggi in modo permanente. 

Se un servizio di notifica push tenta di recapitare una notifica, ma il dispositivo è offline, la notifica viene archiviata dal servizio di notifica push per un periodo di tempo limitato. La notifica viene recapitata al dispositivo quando il dispositivo diventa disponibile. 

Per ciascuna applicazione viene archiviata solo una notifica di recente. Se più notifiche vengono inviate quando un dispositivo è offline, ogni nuova notifica causa la notifica precedente verranno ignorati. Mantenere solo la notifica più recente è detto *notifiche coalescing* in APN, e *compressione* in FCM (che usa una chiave di compressione). Se il dispositivo rimane offline per un lungo periodo, vengono eliminate le notifiche per il dispositivo è state archiviate. Per ulteriori informazioni, vedere [APN Panoramica] e [messaggi su FCM].

Con gli hub di notifica di Azure, è possibile passare una chiave coalescing tramite un'intestazione HTTP tramite l'API SendNotification generico. Ad esempio, il SDK per .NET, utilizzare **SendNotificationAsync**. L'API SendNotification utilizza inoltre le intestazioni HTTP che vengono passate come-è il servizio di notifica push corrispondente. 

## <a name="self-diagnosis-tips"></a>Suggerimenti autocontrollo
Ecco i percorsi per diagnosticare la causa radice di notifiche ignorate nell'hub di notifica:

### <a name="verify-credentials"></a>Verificare le credenziali
* **Portale per sviluppatori del servizio di notifica push**
   
    Verificare le credenziali nel rispettivo push notifica portale dei servizi per sviluppatori (APN FCM, il servizio di notifica di Windows e così via). Per ulteriori informazioni, vedere [iniziare con gli hub di notifica di Azure].

* **Portale di Azure**
   
    Per esaminare e corrispondere alle credenziali con quelli ottenuti tramite il portale di sviluppo servizi notifica push, nel portale di Azure, passare al **criteri di accesso** scheda. 
   
    ![Portale di Azure i criteri di accesso][4]

### <a name="verify-registrations"></a>Verificare le registrazioni
* **Visual Studio**
   
    Se si utilizza Visual Studio per lo sviluppo, è possibile connettersi a Azure tramite Esplora Server per visualizzare e gestire più servizi di Azure, inclusi gli hub di notifica. Questo risulta particolarmente utile per l'ambiente sviluppo/test. 
   
    ![Esplora server di Visual Studio.][9]
   
    È possibile visualizzare e gestire tutte le registrazioni nell'hub, suddivisi in categorie dalla piattaforma, nativa o registrazione del modello, qualsiasi tag, identificatore del servizio notifica push, ID di registrazione e data di scadenza. È anche possibile modificare una registrazione in questa pagina. Ciò è particolarmente utile per la modifica di tag. 
   
    ![Registrazioni di dispositivi di Visual Studio][8]
   
   > [!NOTE]
   > Utilizzare Visual Studio per modificare le registrazioni solo durante sviluppo/test e con un numero limitato di registrazioni. Se è necessario modificare le registrazioni in blocco, è consigliabile usare l'esportazione e importazione delle funzionalità di registrazione descritto in [esportare e modificare registrazioni in blocco](https://msdn.microsoft.com/library/dn790624.aspx).
   > 
   > 
* **Service Bus Explorer**
   
    Utilizzano molti clienti [Service Bus Explorer] per visualizzare e gestire il relativo hub di notifica. Service Bus Explorer è un progetto open source. Per esempi, vedere [codice Service Bus Explorer].

### <a name="verify-message-notifications"></a>Verificare le notifiche di messaggi
* **Portale di Azure**
   
    Per inviare una notifica di prova ai client senza un back-end di servizio attivo e in esecuzione, in **supporto + TROUBLESHOOTING**selezionare **invio di Test**. 
   
    ![Testare la funzionalità di trasmissione in Azure][7]
* **Visual Studio**
   
    È anche possibile inviare notifiche di prova da Visual Studio.
   
    ![Funzionalità di trasmissione di test in Visual Studio][10]
   
    Per ulteriori informazioni sull'utilizzo degli hub di notifica con Esplora Server Visual Studio, vedere i seguenti articoli: 
   
   * [Registrazioni di dispositivi di visualizzazione per gli hub di notifica]
   * [Approfondimento: Visual Studio 2013 Update 2 RC e Azure SDK 2.3]
   * [Versione di Visual Studio 2013 Update 3 e Azure SDK 2.4 annuncio]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Eseguire il debug di notifiche non riuscite ed esaminare il risultato di notifica
**Proprietà EnableTestSend**

Quando si invia una notifica tramite hub di notifica, inizialmente, la notifica viene accodata per l'elaborazione di hub di notifica. Gli hub di notifica determina le destinazioni corrette e quindi invia la notifica al servizio di notifica push. Se si utilizza l'API REST o qualsiasi client SDK, la restituzione ha esito positivo della chiamata trasmissione significa solo che il messaggio è stato accodato con gli hub di notifica. Non si dispone di qualsiasi approfondite sulle operazioni eseguite per gli hub di notifica infine inviato il messaggio al servizio di notifica push. 

Se la notifica non arrivano al dispositivo client, è possibile che si è verificato un errore durante il tentativo di recapitare il messaggio al servizio di notifica push con hub di notifica. Ad esempio, le dimensioni del payload potrebbero superare il numero massimo consentito dal servizio di notifica push o le credenziali configurate nell'hub di notifica potrebbero non essere valida. 

Per ottenere approfondimenti push errori del servizio di notifica, è possibile utilizzare il [EnableTestSend] proprietà. Questa proprietà è abilitata automaticamente quando si inviano messaggi di prova dal portale o client di Visual Studio. È possibile utilizzare questa proprietà per visualizzare informazioni di debug dettagliate. È inoltre possibile utilizzare la proprietà tramite le API. Attualmente, è possibile utilizzare in .NET SDK. Infine, verrà aggiunto a tutti i client SDK. 

Utilizzare il **EnableTestSend** proprietà con la chiamata REST, aggiungere un parametro di stringa di query denominato *test* alla fine della chiamata di trasmissione. Ad esempio:  

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

**Esempio (.NET SDK)**

Di seguito è riportato un esempio di utilizzo di .NET SDK per inviare una notifica popup nativo (popup):

```csharp
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);
```

Al termine dell'esecuzione, **risultato. Stato** indica semplicemente **accodati**. I risultati non forniscono alcun comprendere cosa è successo per la notifica push. 

Successivamente, è possibile utilizzare il **EnableTestSend** proprietà booleana. Utilizzare il **EnableTestSend** proprietà mentre è inizializzare **NotificationHubClient** per ottenere uno stato dettagliato sul push errori del servizio di notifica che si verificano quando viene inviata la notifica. La chiamata di invio è necessario ulteriore tempo per restituire poiché viene restituito solo dopo che l'hub di notifica ha recapitato la notifica al servizio di notifica push per determinare il risultato. 

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

**Esempio di output**

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong

Questo messaggio indica che le credenziali non valide sono configurate nell'hub di notifica oppure si verifica un problema con le registrazioni nell'hub. È consigliabile eliminare questa registrazione e consentire al client di creare nuovamente la registrazione prima di inviare il messaggio. 

> [!NOTE]
> Utilizzare il **EnableTestSend** proprietà è molto limitata. Utilizzare questa opzione solo in un ambiente di sviluppo e test e con un numero limitato di registrazioni. Vengono inviate notifiche di debug solo 10 nei dispositivi. È necessario anche un limite di elaborazione debug invia a 10 al minuto. 
> 
> 

### <a name="review-telemetry"></a>Esaminare i dati di telemetria
* **Usare il portale di Azure**
   
    Nel portale, è possibile ottenere una rapida panoramica di tutte le attività nell'hub di notifica. 
   
    1. Nel **Panoramica** scheda, è possibile visualizzare una visualizzazione aggregata di registrazioni, notifiche e gli errori dalla piattaforma. 
   
        ![Dashboard panoramica degli hub di notifica][5]
   
    2. Nel **monitoraggio** scheda, è possibile aggiungere molte altre metriche specifiche della piattaforma per un'analisi approfondita relativa. È possibile esaminare in particolare gli eventuali errori relativi al servizio di notifica push che vengono restituiti quando gli hub di notifica viene inviata la notifica al servizio di notifica push. 
   
        ![Log attività del portale di Azure][6]
   
    3. Iniziare esaminando **i messaggi in arrivo**, **operazioni di registrazione**, e **notifiche operazioni riuscite**. Quindi, passare alla scheda per ogni piattaforma per esaminare gli errori che sono specifici per il servizio di notifica push. 
   
    4. Se le impostazioni di autenticazione per l'hub di notifica non sono corrette, il messaggio **errore di autenticazione PNS** viene visualizzato. Si tratta di una buona indicazione per verificare le credenziali del servizio notifica push. 

* **Accesso a livello di codice**

    Per ulteriori informazioni sull'accesso a livello di codice, vedere i seguenti articoli: 

    * [Accesso ai dati di telemetria a livello di codice]  
    * [Accesso ai dati di telemetria attraverso l'esempio di API] 

    > [!NOTE]
    > Diverse funzionalità di telemetria correlati, come l'esportazione e importazione di registrazioni e l'accesso ai dati di telemetria attraverso le API, sono disponibili solo a livello di servizio Standard. Se si tenta di utilizzare queste funzionalità da gratuito o Basic del livello servizio, viene visualizzato un messaggio di eccezione, se si utilizza il SDK e un errore HTTP 403 (accesso negato) se si utilizzano le funzionalità direttamente le API REST. 
    >
    >Per utilizzare la funzionalità correlate ai dati di telemetria, verificare innanzitutto nel portale di Azure che si sta utilizzando il livello di servizio Standard.  
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
[Panoramica di hub di notifica]: notification-hubs-push-notification-overview.md
[iniziare con gli hub di notifica di Azure]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[modelli]: https://msdn.microsoft.com/library/dn530748.aspx 
[APN Panoramica]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[messaggi su FCM]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: http://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer]: https://msdn.microsoft.com/library/dn530751.aspx#sb_explorer
[codice Service Bus Explorer]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Registrazioni di dispositivi di visualizzazione per gli hub di notifica]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[Approfondimento: Visual Studio 2013 Update 2 RC e Azure SDK 2.3]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[Versione di Visual Studio 2013 Update 3 e Azure SDK 2.4 annuncio]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[EnableTestSend]: https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Accesso ai dati di telemetria a livello di codice]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[Accesso ai dati di telemetria attraverso l'esempio di API]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

