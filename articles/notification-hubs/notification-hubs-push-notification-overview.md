---
title: Informazioni su Hub di notifica
description: Informazioni su come aggiungere le funzionalità di notifica push con Hub di notifica di Azure.
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
documentationcenter: ''
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 44086bc20966d9c01ff27dda68f837101c71a778
ms.sourcegitcommit: 15bfce02b334b67aedd634fa864efb4849fc5ee2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "33777930"
---
# <a name="what-is-azure-notification-hubs"></a>Informazioni su Hub di notifica
Hub di notifica di Azure offre un motore di push di facile uso e con scalabilità orizzontale che consente di inviare notifiche a qualsiasi piattaforma (iOS, Android, Windows, Kindle, Baidu e così via) da qualsiasi back-end (cloud o locale). Hub di notifica funziona perfettamente per scenari aziendali e di consumo. Di seguito vengono forniti alcuni scenari di esempio:

- Invio di notifiche sulle ultime notizie a milioni di utenti con bassa latenza.
- Invio di coupon in base alla posizione a segmenti di utenti interessati.
- Invio di notifiche sugli eventi a utenti o gruppi per applicazioni di media, sport, finanza e gioco.
- Push di contenuti promozionali alle applicazioni per coinvolgere e offrire prodotti ai clienti.
- Notifiche agli utenti su eventi aziendali, come nuovi messaggi ed elementi di lavoro.
- Invio di codici per l'autenticazione a più fattori.

## <a name="what-are-push-notifications"></a>Informazioni sulle notifiche push
Le notifiche push sono una forma di comunicazione tra l'app e l'utente in cui gli utenti delle app per dispositivi mobili vengono informati di determinate notizie desiderate, in genere con una finestra popup o con una finestra di dialogo. Gli utenti possono in genere scegliere di visualizzare o chiudere il messaggio. Nel primo caso si aprirà l'applicazione per dispositivi mobili che ha inviato la notifica.

Le notifiche push sono fondamentali per le app di consumo perché aumentano l'interesse e l'uso delle app, mentre per le app aziendali favoriscono la comunicazione di informazioni aziendali aggiornate. Sono la migliore forma di comunicazione tra app e utente perché consentono un notevole risparmio energetico per i dispositivi mobili, sono flessibili per i mittenti di notifiche e disponibili anche quando le applicazioni corrispondenti non sono attive.

Per altre informazioni sulle notifiche push per alcune piattaforme più comuni, vedere gli argomenti seguenti: 
* [iOS](https://developer.apple.com/notifications/)
* [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
* [Windows](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-push-notifications-work"></a>Funzionamento delle notifiche push
Le notifiche push vengono recapitate attraverso infrastrutture specifiche della piattaforma denominate *Platform Notification System* (PNS). Offrono funzionalità push essenziali per il recapito di un messaggio a un dispositivo con handle e non hanno un'interfaccia comune. Per inviare una notifica a tutti i clienti delle versioni di un'app per Windows, iOS e Android, lo sviluppatore deve usare il servizio APNS (Apple Push Notification Service), il servizio FCM (Firebase Cloud Messaging) e con il servizio WNS (Servizio di notifica di Windows Notification).

In particolare, ecco come funziona un push:

1. L'app client decide di voler ricevere una notifica. Contatta quindi il PNS corrispondente per recuperare l'handle di push univoco e temporaneo. Il tipo di handle dipende dal sistema, ad esempio per WNS si tratta di URI mentre per APNS si tratta di i token.
2. L'app client archivia l'handle nel provder o nel back-end dell'app.
3. Per inviare una notifica push, il back-end dell'app contatta il PNS usando l'handle per individuare una specifica app client.
4. Il PNS inoltra quindi la notifica al dispositivo specificato dall'handle.

![Flusso di lavoro delle notifiche push](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>Difficoltà associate alle notifiche push
I PNS sono potenti. Richiedono tuttavia un grande impegno da parte degli sviluppatori di app per implementare anche i più comuni scenari di notifica push, come la trasmissione di notifiche push a utenti segmentati.

Il push delle notifiche richiede un'infrastruttura complessa che non è correlata alla logica di business principale dell'applicazione. Di seguito sono riportate alcune delle difficoltà infrastrutturali:

- **Dipendenza dalla piattaforma**
    - Il back-end deve avere una logica dipendente dalla piattaforma complessa e difficile da gestire per inviare notifiche a dispositivi su diverse piattaforme perché i PNS non sono unificati.
- **Ridimensionare**
    - In base alle linee guida dei sistemi PNS,è necessario aggiornare i token di dispositivo a ogni avvio dell'app. Il back-end si occupa di una grande quantità di traffico e dell'accesso al database solo per mantenere aggiornati i token. Se il numero di dispositivi cresce fino a raggiungere centinaia o milioni di unità, i costi per la creazione e la gestione dell'infrastruttura diventano significativi.
    - La maggior parte dei sistemi PNS non supporta la trasmissione a più dispositivi. Una semplice trasmissione a un milione di dispositivi causa un milione di chiamate ai PNS. La scalabilità di questa quantità di traffico con latenza minima è molto complessa.
- **Routing** 
    - Benché i PNS siano un modo per inviare messaggi ai dispositivi, la maggior parte delle notifiche app sono destinate a utenti o a gruppi di interesse. Il back-end deve mantenere un registro per associare i dispositivi ai gruppi, agli utenti, alle proprietà e così via. Questo aumento del carico di lavoro contribuisce inevitabilmente ad aumentare i tempi di produzione e i costi di manutenzione di un'app.

## <a name="why-use-azure-notification-hubs"></a>Vantaggi di Hub di notifica di Azure
Hub di notifica elimina tutte le complessità associate al push delle notifiche dal back-end dell'app. La sua infrastruttura di notifiche push multipiattaforma e con scalabilità orizzontale riduce la codifica correlata al push e semplifica il back-end. Con Hub di notifica, i dispositivi devono eseguire soltanto la registrazione dei propri handle PNS su un hub, mentre il back-end è responsabile dell'invio di messaggi a utenti o gruppi di interesse, come mostrato nella figura seguente:

![Diagramma di Hub di notifica](./media/notification-hubs-overview/notification-hub-diagram.png)

Hub di notifica è un motore di push pronto all'uso e presenta i vantaggi seguenti:

- **Multipiattaforma**
    - Supporta tutte le principali piattaforme push tra cui iOS, Android, Window, Kindle e Baidu.
    - È dotato di un'interfaccia comune per effettuare il push a tutte le piattaforme in formati specifici o indipendenti dalla piattaforma senza un processo specifico per la piattaforma.
    - Consente di gestire l'handle di dispositivo in un solo posto.
- **Multi back-end**
    - Cloud o in locale
    - .NET, Node. js, Java e così via.
- **Insieme completo di modelli di recapito**
    - Trasmissione a una o più piattaforme: è possibile trasmettere immediatamente a milioni di dispositivi in più piattaforme con una singola chiamata API.
    - Push al dispositivo: è possibile inviare notifiche ai singoli dispositivi.
    - Push all'utente: le funzioni tag e modelli consentono di raggiungere tutti i dispositivi multipiattaforma di un utente.
    - Push a segmenti con tag dinamico: la funzione tag consente di segmentare i dispositivi e di inviarvi push in base alle esigenze, che si stia eseguendo l'invio a un segmento o a un'espressione di segmenti (ad esempio active AND lives in Seattle NOT new user). Anziché essere limitati a pubblicazione-sottoscrizione, è possibile aggiornare i tag del dispositivo ovunque e in qualsiasi momento.
    - Push localizzato: la funzione modelli consente di ottenere la localizzazione senza influire sul codice di back-end.
    - Push non interattivo: consente di abilitare il modello push-to-pull inviando notifiche automatiche ai dispositivi, attivandoli in modo da completare determinati pull o azioni.
    - Push pianificato: è possibile pianificare l'invio delle notifiche in qualsiasi momento.
    - Push diretto: è possibile ignorare la registrazione dei dispositivi nel servizio Hub di notifica ed eseguire direttamente push in batch per un elenco di handle di dispositivo.
    - Push personalizzati: le variabili push del dispositivo consentono di inviare notifiche push personalizzate specifiche per il dispositivo con coppie chiave-valore personalizzate.
- **Telemetria avanzata**
    - Dati di telemetria generali di push, dispositivi, errori e operazioni sono disponibili nel portale di Azure e a livello di codice.
    - La telemetria per messaggio tiene traccia di ogni push dalla chiamata della richiesta iniziale al corretto invio di push in batch da parte del servizio Hub di notifica.
    - Il feedback di Platform Notification System comunica tutto il feedback di Platform Notification System per facilitare il debug.
- **Scalabilità** 
    - Invia messaggi veloci a milioni di dispositivi senza riprogettazione o partizionamento orizzontale del dispositivo.
- **Sicurezza**
    - Firma di accesso condiviso (SAS) o autenticazione federata.

## <a name="integration-with-app-service-mobile-apps"></a>Integrazione con le app per dispositivi mobili del servizio app
Per favorire un'esperienza lineare e uniforme nei servizi di Azure, le [app per dispositivi mobili del servizio app](../app-service-mobile/app-service-mobile-value-prop.md) comprendono il supporto predefinito per le notifiche push mediante Hub di notifica. [app per dispositivi mobili del servizio app](../app-service-mobile/app-service-mobile-value-prop.md) offrono una piattaforma di sviluppo di applicazioni mobili estremamente scalabile e disponibile a livello globale per sviluppatori aziendali e integratori di sistemi, che fornisce un set completo di funzionalità per gli sviluppatori di soluzioni per dispositivi mobili.

Gli sviluppatori di app per dispositivi mobili possono usare gli hub di notifica con il seguente flusso di lavoro:

1. Recuperare la gestione del dispositivo PNS
2. Registrare il dispositivo con gli Hub di notifica mediante il pratico API Register SDK client delle app per dispositivi mobili

    > [!NOTE]
    > Si noti che le app per dispositivi mobili consentono di eliminare tutti i tag nelle registrazioni per motivi di sicurezza. Usare le hub di notifica dal back-end direttamente per associare tag ai dispositivi.
1. Invio di notifiche dal back-end dell'app con hub di notifica

Ecco alcuni vantaggi per gli sviluppatori inclusi in questa integrazione:

- **SDK client per app per dispositivi mobili**: questi SDK multipiattaforma forniscono API semplici per la registrazione e per comunicare automaticamente con l'Hub di notifica collegato all'app per dispositivi mobili. Gli sviluppatori non hanno bisogno di cercare tra le credenziali di Hub di notifica e lavorare con un servizio aggiuntivo.
    - *Push all'utente*: gli SDK assegnano automaticamente tag al dispositivo specificato con l'ID utente autenticato di App per dispositivi mobili per abilitare il push all'utente.
    - *Push al dispositivo*: l'ID di installazione di App per dispositivi mobili viene usato automaticamente dagli SDK come GUID per la registrazione in Hub di notifica. In questo modo gli sviluppatori non sono costretti a mantenere più GUID di servizio.
- **Modello di installazione**: le app per dispositivi mobili funzionano con il modello di push più recente degli Hub di notifica per rappresentare tutte le proprietà di push associate a un dispositivo in un'installazione JSON che sia in linea con i servizi di notifica push e che sia facile da usare.
- **Flessibilità**: gli sviluppatori possono scegliere di usare direttamente Hub di notifica in qualsiasi momento, anche dopo l'integrazione.
- **Esperienza integrata nel [Portale di Azure](https://portal.azure.com)**: il push come funzionalità è rappresentato visivamente nelle app per dispositivi mobili e gli sviluppatori possono utilizzare facilmente l'Hub di notifica associato tramite le App per dispositivi mobili.

## <a name="next-steps"></a>Passaggi successivi
Introduzione alla creazione e all'uso di un hub di notifica con l'esercitazione [Eseguire il push delle notifiche alle applicazioni per dispositivi mobili](notification-hubs-android-push-notification-google-fcm-get-started.md). [0]: ./media/notification-hubs-overview/registration-diagram.png [1]: ./media/notification-hubs-overview/notification-hub-diagram.png [Uso di Hub di notifica da parte dei clienti]: http://azure.microsoft.com/services/notification-hubs [Esercitazioni e guide su Hub di notifica]: http://azure.microsoft.com/documentation/services/notification-hubs [iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started [Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started [Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started [Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started [Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started [Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started [Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx [App per dispositivi mobili del servizio app]: https://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop/ [modelli]: notification-hubs-templates-cross-platform-push-messages.md [portale di Azure]: https://portal.azure.com [tag]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)
