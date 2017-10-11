---
title: Hub di notifica di Azure
description: "Informazioni su come aggiungere le funzionalità di notifica push con Hub di notifica di Azure."
author: ysxu
manager: erikre
editor: 
services: notification-hubs
documentationcenter: 
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 1/17/2017
ms.author: yuaxu
ms.openlocfilehash: a1be0b13cd1feb582a23965df142e44d90ac6851
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="azure-notification-hubs"></a>Hub di notifica di Azure
## <a name="overview"></a>Panoramica
Hub di notifica di Azure offre un motore di push di facile uso, multipiattaforma e con scalabilità orizzontale. Con una sola chiamata all'API multipiattaforma, è possibile inviare facilmente notifiche push mirate e personalizzate a qualsiasi piattaforma mobile da qualsiasi cloud o back-end locale.

Hub di notifica funziona perfettamente per scenari aziendali e di consumo. Di seguito sono riportati alcuni esempi degli usi di Hub di notifica fatti dai clienti:

* Invio di notifiche sulle ultime notizie a milioni di utenti con bassa latenza.
* Invio di coupon in base alla posizione a segmenti di utenti interessati.
* Invio di notifiche sugli eventi a utenti o gruppi per applicazioni di media, sport, finanza e gioco.
* Contenuti push promozionali per le app per coinvolgere e offrire prodotti ai clienti.
* Notifiche agli utenti su eventi aziendali, come nuovi messaggi ed elementi di lavoro.
* Invio di codici per l'autenticazione a più fattori.

## <a name="what-are-push-notifications"></a>Cosa sono le notifiche push?
Le notifiche push sono una forma di comunicazione tra l'app e l'utente in cui gli utenti delle app per dispositivi mobili vengono informati di determinate notizie desiderate, in genere con una finestra popup o con una finestra di dialogo. Gli utenti in genere possono scegliere di visualizzare o chiudere il messaggio, la prima opzione consente di aprire l'app per dispositivi mobili che ha comunicato la notifica.

Le notifiche push sono fondamentali per le app di consumo perché aumentano l'interesse e l'uso delle app, mentre per le app aziendali favoriscono la comunicazione di informazioni aziendali aggiornate. Sono la migliore forma di comunicazione tra app e utente perché consentono un notevole risparmio energetico per i dispositivi mobili, sono flessibili per i mittenti di notifiche e disponibili anche quando le app corrispondenti non sono attive.

Maggiori informazioni sulle notifiche push per alcune piattaforme più comuni:
* [iOS](https://developer.apple.com/notifications/)
* [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
* [Windows](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-push-notifications-work"></a>Funzionamento delle notifiche push
Le notifiche push vengono recapitate attraverso infrastrutture specifiche della piattaforma denominate *Platform Notification System* (PNS). Offrono funzionalità push essenziali per il recapito di un messaggio a un dispositivo con handle e un'interfaccia non comune. Per inviare una notifica a tutti i clienti delle versioni di un'app per Windows, iOS e Android, lo sviluppatore deve usare il servizio APNS (Apple Push Notification Service), il servizio FCM (Firebase Cloud Messaging) e con il servizio WNS (Servizio di notifica di Windows Notification), durante l'invio in batch.

In particolare, ecco come funziona un push:

1. L'app client deve ricevere le notifiche push pertanto contatta il PNS corrispondente per recuperare l'handle di push univoco e temporaneo. Il tipo di handle dipende dal sistema (ad esempio, per WNS c'è URI mentre per il servizio APN ci sono i token).
2. L'app client archivia l'handle nel provder o nel back-end dell'app.
3. Per inviare una notifica push, il back-end dell'app contatta il PNS usando l'handle per individuare una specifica app client.
4. Il PNS inoltra quindi la notifica al dispositivo specificato dall'handle.

![][0]

## <a name="the-challenges-of-push-notifications"></a>Difficoltà associate alle notifiche push
Seppur molto potenti, i PNS richiedono un grande impegno da parte degli sviluppatori di app per implementare anche i più comuni scenari di notifica push, come la trasmissione o l'invio di notifiche push a utenti segmentati.

Il push è una delle funzionalità più richieste nei servizi cloud per dispositivi mobili, perché il relativo uso richiede infrastrutture complesse che non sono correlate alla logica di business principale dell'app. Di seguito sono riportate alcune delle difficoltà infrastrutturali:

* **Dipendenza dalla piattaforma**: 

  * Il back-end deve avere una logica dipendente dalla piattaforma complessa e difficile da gestire per inviare notifiche a dispositivi su diverse piattaforme perché i PNS non sono unificati.
* **Scalabilità**:

  * In base alle linee guida dei sistemi PNS,è necessario aggiornare i token di dispositivo a ogni avvio dell'app. Ciò significa che il back-end si occupa di una grande quantità di traffico e dell'accesso al database solo per mantenere aggiornati i token. Se il numero di dispositivi cresce fino a raggiungere centinaia o milioni di unità, i costi per la creazione e la gestione dell'infrastruttura diventano significativi.
  * La maggior parte dei sistemi PNS non supporta la trasmissione a più dispositivi. Ciò significa che una semplice trasmissione a milioni di dispositivi causa un milione di chiamate ai PNS. La scalabilità di questa quantità di traffico con latenza minima è molto complessa.
* **Routing**:
  
  * Benché i PNS siano un modo per inviare messaggi ai dispositivi, la maggior parte delle notifiche app sono destinate a utenti o a gruppi di interesse. Ciò significa che il back-end deve mantenere un registro di sistema per associare i dispositivi ai gruppi, agli utenti, alle proprietà e così via. Questo aumento del carico di lavoro contribuisce inevitabilmente ad aumentare i tempi di produzione e i costi di manutenzione di un'app.

## <a name="why-use-notification-hubs"></a>Perché usare gli Hub di notifica?
Hub di notifica elimina tutte le complessità associate all'abilitazione autonoma del push. L'infrastruttura di notifiche push multipiattaforma e con scalabilità orizzontale riduce i codici dei push e semplifica il back-end. Con Hub di notifica, i dispositivi devono eseguire soltanto la registrazione dei propri handle PNS su un hub, mentre il back-end è responsabile dell'invio di messaggi a utenti o gruppi di interesse, come mostrato nella figura seguente:

![][1]

Hub di notifica è un motore di push pronto all'uso e presenta i vantaggi seguenti:

* **Multipiattaforma**

  * Supporta tutte le principali piattaforme push tra cui iOS, Android, Window, Kindle e Baidu.
  * È dotato di un'interfaccia comune per effettuare il push a tutte le piattaforme in formati specifici o indipendenti dalla piattaforma senza un processo specifico per la piattaforma.
  * Consente di gestire l'handle di dispositivo in un solo posto.
* **Multi back-end**
  
  * Cloud o in locale
  * .NET, Node. js, Java e così via.
* **Insieme completo di modelli di recapito**:

  * *Trasmissione a una o più piattaforme*: è possibile trasmettere immediatamente a milioni di dispositivi su piattaforme con una singola chiamata API.
  * *Push al dispositivo*: è possibile impostare le notifiche per i singoli dispositivi.
  * *Push a utente*: le funzioni tag e modelli consentono di raggiungere tutti i dispositivi multipiattaforma di un utente.
  * *Push a segmenti con tag dinamico*: la funzione tag consente di segmentare i dispositivi e di inviarvi push in base alle esigenze, che si stia inviando a un segmento o a un'espressione di segmenti (ad esempio AND attivo vive a Seattle NON è un nuovo utente). Anziché essere limitati a pubblicazione-sottoscrizione, è possibile aggiornare i tag del dispositivo ovunque e in qualsiasi momento.
  * *Push localizzato*: la funzione modelli consente di ottenere la localizzazione senza influire sul codice di back-end.
  * *Push silent*: consente di abilitare il modello push di pull inviando le notifiche silent ai dispositivi, attivandoli in modo da completare determinati pull o azioni.
  * *Push pianificato*: è possibile pianificare l'invio delle notifiche in qualsiasi momento.
  * *Push diretto*: è possibile ignorare la registrazione dei dispositivi sul servizio e creare direttamente batch di push per un elenco di handle di dispositivo.
  * *Push personalizzati*: le variabili push del dispositivo consentono di inviare notifiche push personalizzate specifiche per il dispositivo con coppie chiave-valore personalizzate.
* **Telemetria avanzata**
  
  * Nel Portale di Azure e a livello di programmazione è disponibile la telemetria generale di push, dispositivo, errore e operazione.
  * La Telemetria per messaggio registra ogni push dalla chiamata della richiesta iniziale al corretto invio di push da parte del servizio.
  * Platform Notification System comunica tutti i commenti di Platform Notification System per facilitare il debug.
* **Scalabilità** 
  
  * Invia messaggi veloci a milioni di dispositivi senza riprogettazione o partizionamento orizzontale del dispositivo.
* **Sicurezza**

  * Firma di accesso condiviso (SAS) o autenticazione federata.

## <a name="integration-with-app-service-mobile-apps"></a>Integrazione con le app per dispositivi mobili del servizio app
Per favorire un'esperienza lineare e uniforme nei servizi di Azure, le [app per dispositivi mobili del servizio app] comprendono il supporto predefinito per le notifiche push mediante Hub di notifica. [app per dispositivi mobili del servizio app] offrono una piattaforma di sviluppo di applicazioni mobili estremamente scalabile e disponibile a livello globale per sviluppatori aziendali e integratori di sistemi, che fornisce un set completo di funzionalità per gli sviluppatori di soluzioni per dispositivi mobili.

Gli sviluppatori di app per dispositivi mobili possono usare gli hub di notifica con il seguente flusso di lavoro:

1. Recuperare la gestione del dispositivo PNS
2. Registrare il dispositivo con gli Hub di notifica mediante il pratico API Register SDK client delle app per dispositivi mobili
   * Si noti che le app per dispositivi mobili consentono di eliminare tutti i tag nelle registrazioni per motivi di sicurezza. Usare le hub di notifica dal back-end direttamente per associare tag ai dispositivi.
3. Invio di notifiche dal back-end dell'app con hub di notifica

Ecco alcuni vantaggi per gli sviluppatori inclusi in questa integrazione:

* **SDK client per app per dispositivi mobili**: questi SDK multipiattaforma forniscono API semplici per la registrazione e per comunicare automaticamente con l'Hub di notifica collegato all'app per dispositivi mobili. Gli sviluppatori non hanno bisogno di cercare tra le credenziali di Hub di notifica e lavorare con un servizio aggiuntivo.

  * *Push all'utente*: gli SDK assegnano automaticamente tag al dispositivo specificato con l'ID utente autenticato di App per dispositivi mobili per abilitare il push all'utente.
  * *Push al dispositivo*: l'ID di installazione di App per dispositivi mobili viene usato automaticamente dagli SDK come GUID per la registrazione in Hub di notifica. In questo modo gli sviluppatori non sono costretti a mantenere più GUID di servizio.
* **Modello di installazione**: le app per dispositivi mobili funzionano con il modello di push più recente degli Hub di notifica per rappresentare tutte le proprietà di push associate a un dispositivo in un'installazione JSON che sia in linea con i servizi di notifica push e che sia facile da usare.
* **Flessibilità**: gli sviluppatori possono scegliere di usare direttamente Hub di notifica in qualsiasi momento, anche dopo l'integrazione.
* **Esperienza integrata nel [Portale di Azure]**: il push come funzionalità è rappresentato visivamente nelle app per dispositivi mobili e gli sviluppatori possono utilizzare facilmente l'Hub di notifica associato tramite le App per dispositivi mobili.

## <a name="next-steps"></a>Passaggi successivi
Nei seguenti argomenti sono disponibili altre informazioni su Hub di notifica:

* **[Uso di Hub di notifica da parte dei clienti]**
* **[Esercitazioni e guide su Hub di notifica]**
* **Introduzione ad Hub di notifica**: [iOS], [Android], [Windows Universal], [Windows Phone], [Kindle], [Xamarin.iOS], [Xamarin.Android]

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png
[Uso di Hub di notifica da parte dei clienti]: http://azure.microsoft.com/services/notification-hubs
[Esercitazioni e guide su Hub di notifica]: http://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
[Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
[Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
[Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
[Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
[Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
[Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
[Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
[Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
[app per dispositivi mobili del servizio app]: https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-value-prop/
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[Portale di Azure]: https://portal.azure.com
[tags]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)
