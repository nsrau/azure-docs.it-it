---
title: Guide alla risoluzione dei problemi di Azure Mobile Engagement
description: Guida alla risoluzione dei problemi di Azure Mobile Engagement
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 31134a29-a513-4e5e-b626-f6cf6fe04769
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 93b5e3f4892f974bf9df28955956136528470e03
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement---troubleshooting-guide"></a>Azure Mobile Engagement - Guida alla risoluzione dei problemi
## <a name="introduction"></a>Introduzione
La guida alla risoluzione dei problemi seguente semplifica la comprensione delle cause principali di alcuni problemi comuni e consente di risolvere autonomamente i problemi. 

## <a name="general"></a>Generale
È in genere necessario eseguire le verifiche seguenti:

1. Assicurarsi di avere eseguito tutti i passaggi necessari per l'integrazione, come illustrato nelle [Esercitazioni introduttive](mobile-engagement-windows-store-dotnet-get-started.md)
2. Verificare di usare la versione più recente degli SDK della piattaforma. 
3. Eseguire il test in un dispositivo effettivo e in un emulatore, perché alcuni problemi sono specifici solo dell'emulatore. 
4. Assicurarsi di non superare alcun limite/limitazione di Mobile Engagement illustrato [qui](../azure-subscription-service-limits.md)
5. Se non si riesce a connettersi al back-end del servizio Mobile Engagement o se i dati non vengono caricati in modo continuo, assicurarsi che non siano in corso incidenti del servizio verificando [qui](https://azure.microsoft.com/status/)

## <a name="monitor-issues"></a>Problemi di monitoraggio
### <a name="i-am-not-seeing-my-device-showing-up-on-the-monitor-tab"></a>Il dispositivo non viene visualizzato nella scheda Monitoraggio
La scheda Monitoraggio mostra i dispositivi connessi alla piattaforma Mobile Engagement in tempo reale. Se si esegue il debug in un emulatore e in un dispositivo, in questa scheda dovrebbe essere visualizzata almeno una sessione. Se l'app è stata distribuita, il misuratore Sessioni attive rifletterà i dispositivi connessi alla piattaforma in tempo reale. 

Se il dispositivo non viene visualizzato nella scheda Monitoraggio, si tratta probabilmente di un problema di integrazione dell'SDK. Ecco alcuni passaggi da eseguire per risolvere i problemi:

1. Assicurarsi di usare la stringa di connessione corretta nell'app per dispositivi mobili e che la stringa provenga dalla sezione delle chiavi dell'SDK, non dalla sezione delle chiavi API. La stringa di connessione connette l'app per dispositivi mobili all'istanza dell'app Mobile Engagement in cui verrà visualizzato il dispositivo nella scheda Monitoraggio. 
2. Per la piattaforma Windows: se la pagina esegue l'override del metodo `OnNavigatedTo`, assicurarsi di chiamare `base.OnNavigatedTo(e)`.
3. Se si sta integrando Mobile Engagement in un'app per dispositivi mobili esistente, sarà possibile anche assicurarsi di non tralasciare alcun passaggio esaminando i passaggi di integrazione avanzati [qui](mobile-engagement-windows-store-integrate-engagement.md)
4. Assicurarsi di inviare almeno una schermata/attività eseguendo l'override della pagina con EngagementActivity in base alla piattaforma usata, come illustrato nelle [Esercitazioni introduttive](mobile-engagement-windows-store-dotnet-get-started.md).

### <a name="i-am-seeing-the-monitor-tab-showing-a-session-even-when-i-have-disconnected-or-closed-my-app-emulator"></a>La scheda Monitoraggio mostra una sessione anche dopo la disconnessione o chiusura dell'app/emulatore.
Se si è l'unico utente connesso alla piattaforma in questo momento e si usa un emulatore per aprire l'app, è probabile che si tratti di un problema dell'emulatore. In genere, è necessario assicurarsi di tornare alla schermata iniziale dell'emulatore per consentire la disconnessione della sessione dell'app. Nella piattaforma Windows, durante il debug con Visual Studio, per chiudere effettivamente la sessione potrebbe essere necessario passare a Visual Studio e fare clic su **Sospendi** nella barra dei menu **Eventi ciclo di vita**. Per informazioni dettagliate, vedere l' [esercitazione su Windows](mobile-engagement-windows-store-dotnet-get-started.md) . 

## <a name="analytics-issues"></a>Problemi di analisi
### <a name="i-am-not-seeing-any-data-refreshed-data-on-analytics-tab"></a>I dati o i dati aggiornati non vengono visualizzati nella scheda Analisi
I dati di analisi vengono ricalcolati a intervalli regolati e per l'aggiornamento potrebbero essere necessarie fino a 24 ore. I dati non sono in tempo reale e verranno aggiornati entro questo periodo di tempo di 24 ore.
Assicurarsi tuttavia di inviare almeno una schermata o un'attività al back-end della piattaforma eseguendo l'override di almeno una pagina con `EngagementActivity` o chiamando esplicitamente `SendActivity`. 

### <a name="i-am-seeing-incorrectly-captured-datetime-for-a-device-on-the-analytics-tab"></a>Nella scheda Analisi vengono visualizzati valori di data/ora acquisiti in modo errato per un dispositivo
Il periodo di tempo per le analisi è basato sulla data delle impostazioni del dispositivo degli utenti. Assicurarsi quindi che la data del dispositivo sia impostata correttamente. 

## <a name="segment-issues"></a>Problemi relativi ai segmenti
### <a name="i-created-a-segment-and-it-is-showing-up-as-greyed-out-or-not-showing-any-data"></a>Il segmento creato viene visualizzato come disattivato o privo di dati
La creazione di un segmento non è attualmente in tempo reale. Viene calcolato contemporaneamente all'aggregazione dei dati di analisi, quindi l'operazione potrebbe richiedere fino a 24 ore. È possibile ricontrollare in seguito, ma nel frattempo è consigliabile assicurarsi che le app per dispositivi mobili stiano effettivamente inviando i dati su cui si basa la creazione dei segmenti. Ad esempio, se un evento 'foo' non viene inviato da alcun dispositivo mobile, non saranno disponibili dati di segmento per un segmento creato con il criterio EventName = foo. È anche consigliabile controllare l'integrazione dell'SDK per assicurarsi che l'app per dispositivi mobili stia inviando correttamente i dati. 

## <a name="reach-or-push-notifications-issues"></a>Problemi di copertura o delle notifiche push
### <a name="my-push-messages-are-not-being-delivered"></a>I messaggi push non vengono recapitati
1. Provare a inviare notifiche per testare prima di tutto un dispositivo, per assicurarsi che tutti i componenti, ovvero l'app per dispositivi mobili, l'SDK e il servizio, siano connessi correttamente e riescano a recapitare notifiche push. 
2. Inviare sempre la più semplice 'notifica out-of-app' prima di tutto tramite una campagna non pianificata e priva di criteri specifici per i destinatari. Ciò consente di verificare il corretto funzionamento della connettività per le notifiche. 
3. Se si verificano problemi di recapito delle notifiche in-app, è comunque consigliabile provare a inviare prima di tutto una notifica out-of-app. 
4. Assicurarsi che il 'Push nativo' sia configurato correttamente per l'app per dispositivi mobili. In base alla piattaforma, saranno necessarie chiavi (Android, Windows) o certificati (iOS). Per informazioni dettagliate, vedere l' [Interfaccia utente - Impostazioni](mobile-engagement-user-interface-settings.md)
5. È anche possibile che le notifiche out-of-app siano bloccate dall'utente tramite il sistema operativo del dispositivo mobile, quindi occorre assicurarsi che non siano bloccate. 
6. Assicurarsi che l'opzione *Ignora i destinatari. Il push verrà inviato agli utenti tramite l'API* della sezione **Campagna** di una campagna di copertura non sia impostata, perché ciò consentirà l'invio di notifiche push solo tramite le API. 
7. Assicurarsi di testare la campagna push con un dispositivo connesso tramite WiFi e con una rete di operatore telefonico per eliminare la connessione di rete come possibile origine dei problemi.
8. Assicurarsi che la data/ora d sistema nel dispositivo/emulatore sia corretta, perché eventuali dispositivi non sincronizzati interferiranno anche con la capacità del servizio di notifica push di recapitare le notifiche. 

Ecco altre istruzioni per la risoluzione dei problemi specifiche per le piattaforme:

1. **iOS** 
   
   * Assicurarsi che i certificati siano validi e non siano scaduti per le notifiche push iOS. 
   * Assicurarsi che un certificato di *produzione* sia configurato correttamente nell'app Mobile Engagement. 
   * Assicurarsi di eseguire il test su un *dispositivo fisico reale.* Il simulatore iOS non è in grado di elaborare i messaggi push.
   * Assicurarsi che l'identificatore del bundle sia configurato correttamente nell'app per dispositivi mobili. Le istruzioni sono disponibili [qui](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)
   * Durante il test, utilizzare la distribuzione "Ad Hoc" nel profilo di provisioning per dispositivi mobili. Non sarà possibile ricevere una notifica se l'applicazione viene compilata con "Debug"
2. **Android**
   
   * Assicurarsi di avere specificato il numero di progetto corretto nel file AndroidManifest.xml dell'app per dispositivi mobili, seguito dal carattere \n. 
     
           <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
   * Assicurarsi che tutte le autorizzazioni necessarie nel file manifesto Android siano presenti e siano configurate correttamente 
   * Assicurarsi che il numero di progetto da aggiungere all'app client provenga dallo stesso account da cui è stata ottenuta la chiave del server GCM. Se i due account non corrispondono, i push non potranno uscire. 
   * Se si ricevono le notifiche del sistema, ma non le notifiche in-app, vedere la sezione [Specificare un'icona per le notifiche](mobile-engagement-android-get-started.md) , perché è probabile che non sia stata specificata l'icona corretta nel file manifesto Android. 
   * Se si invia una notifica BigPicture, assicurarsi che eventuali server di immagini esterni disponibili riescano a supportare HTTP "GET" e "HEAD".
3. **Windows**
   
   * Assicurarsi di avere associato l'app a un'app di Windows Store valida. In Visual Studio sarà necessario fare clic con il pulsante destro del mouse sul progetto e scegliere l'opzione "Associa applicazione a Store", quindi selezionare l'app creata in Windows Store. L'app di Windows Store deve essere uguale a quella da cui sono state ottenute le credenziali push native da configurare nel portale di Mobile Engagement.
   * Se si ricevono le notifiche push out-of-app ma non le notifiche in-app con l'integrazione `EngagementOverlay` , assicurarsi che la pagina includa un elemento griglia radice. EngagementOverlay usa il primo elemento "Grid" rilevato nel file con estensione xaml per aggiungere due visualizzazioni Web alla pagina. Per individuare il punto in cui verranno configurate le visualizzazioni Web, è possibile definire una griglia denominata "EngagementGrid" in questo modo, ma sarà necessario assicurarsi che l'altezza e la larghezza siano sufficienti per le due visualizzazioni Web successive, che mostreranno la notifica e l'annuncio seguente come notifica in-app:
     
           <Grid x:Name="EngagementGrid"></Grid>

### <a name="i-created-a-push-notificationannouncement-campaign-and-even-after-it-sent-me-the-notification-it-is-showing-as-active-what-does-it-mean"></a>La notifica push/l'annuncio/la campagna ha stato 'Attivo' anche dopo l'invio della notifica all'utente. Da cosa dipende il problema?
La **campagna** creata in Mobile Engagement viene definita in questo modo perché si tratta di una notifica push a esecuzione prolungata. Ciò significa che quando i nuovi dispositivi si connettono alla piattaforma di Mobile Engagement, riceveranno automaticamente la notifica configurata qui, purché soddisfino i criteri definiti nella campagna. La configurazione della notifica richiede più passaggi. Sarà necessario fare clic manualmente sul pulsante **Fine** per terminare la campagna, in modo che non invii altre notifiche. 

### <a name="i-created-a-push-campaign-and-i-am-receiving-notifications-successfully-however-whenever-i-open-up-the-app-i-get-the-same-notification-even-when-i-had-actioned-it-before"></a>È stata creata una campagna push e si ricevono correttamente le notifiche, ma quando si apre l'app si ottiene la stessa notifica anche se era già stata eseguita in precedenza un'azione in merito
È possibile che questo problema si verifichi durante i test e se si usano emulatori o framework di test come TestFlight. Ogni istanza di esecuzione dell'app acquisisce un nuovo ID dispositivo e lo invia al back-end. La piattaforma di Mobile Engagement lo considera quindi come un nuovo dispositivo e invia la notifica. 

## <a name="getting-support"></a>Ottenere assistenza
Se non si riesce a risolvere il problema autonomamente, sono disponibili le opzioni seguenti:

1. Cercare il problema specifico nei thread esistenti nel forum StackOverflow e nel [forum su MSDN](https://social.msdn.microsoft.com/Forums/windows/en-US/home?forum=azuremobileengagement). Se il problema non è già stato segnalato, inviare una domanda ai forum. 
2. Se si ritiene che manchi una funzionalità, aggiungere/votare la relativa richiesta nel [forum UserVoice](https://feedback.azure.com/forums/285737-mobile-engagement/)
3. Se si ha diritto al Supporto Microsoft, aprire una richiesta di assistenza, fornendo i dettagli seguenti: 
   * ID sottoscrizione di Azure
   * Piattaforma (ad esempio iOS, Android e così via)
   * ID app
   * ID campagna (per problemi di notifiche push)
   * ID dispositivo
   * Versione di Mobile Engagement SDK (ad esempio Android SDK v2.1.0)
   * Dettagli dell'errore con messaggio di errore esatto e scenario

