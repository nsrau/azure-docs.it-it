<properties
	pageTitle="Hub di notifica di Azure"
	description="Informazioni su come usare le notifiche push in Azure. Gli esempi di codice sono scritti in C# mediante l'API .NET."
	authors="wesmc7777"
	manager="erikre"
	editor=""
	services="notification-hubs"
	documentationCenter=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="multiple"
	ms.devlang="multiple"
	ms.topic="hero-article"
	ms.date="08/25/2016"
	ms.author="wesmc"/>  


#Hub di notifica di Azure

##Overview

Gli hub di notifica di Azure forniscono un'infrastruttura push di facile utilizzo, multipiattaforma e con scalabilità orizzontale che consente di inviare notifiche push mobili da qualsiasi back-end (nel cloud o locale) a qualsiasi piattaforma mobile.

Con Hub di notifica, è possibile inviare facilmente notifiche push multipiattaforma personalizzate, astraendo i dettagli dei diversi sistemi PNS (Platform Notification System). Con una singola chiamata di API, è possibile indirizzarsi a singoli utenti o interi segmenti di destinatari contenenti milioni di utenti, in tutti i dispositivi.

È possibile utilizzare Hub di notifica per scenari aziendali e di consumo. ad esempio:

- Inviare notifiche relative alle ultime notizie a milioni di utenti con bassa latenza (Hub di notifica alimenta applicazioni Bing preinstallate in tutti i dispositivi Windows e Windows Phone).
- Inviare coupon basati sulla posizione a segmenti di utenti.
- Inviare le notifiche degli eventi a utenti o gruppi per applicazioni di sport, finanza e gioco.
- Notificare agli utenti eventi aziendali, come nuovi messaggi/posta elettronica e clienti potenziali.
- Inviare password monouso richieste per Multi-Factor Authentication.



##Cosa sono le notifiche push?

Smartphone e tablet possono "notificare" agli utenti il verificarsi di un evento. Queste notifiche possono assumere molte forme.

Nelle applicazioni Windows Store e Windows Phone la notifica può essere sotto forma di _avviso popup_, ovvero viene visualizzata una finestra senza modo, con un suono, per segnalare una nuova notifica. Altri tipi supportati sono i _riquadri_, le _notifiche non elaborate_ e le _notifiche_. Per altre informazioni sui tipi di notifiche supportati nei dispositivi Windows, vedere [Riquadri, badge e notifiche](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx).

Analogamente, nei dispositivi Apple iOS la notifica push visualizza una finestra di dialogo in cui viene richiesto all'utente se desidera visualizzare o chiudere la notifica. Facendo clic su **Visualizza** è possibile aprire l'applicazione che sta ricevendo il messaggio. Per altre informazioni sulle notifiche di iOS, vedere [Notifiche di iOS](http://go.microsoft.com/fwlink/?LinkId=615245).

Le notifiche push consentono ai dispositivi mobili di visualizzare informazioni aggiornate con un consumo minimo di energia. È possibile inviare notifiche da sistemi back-end a dispositivi mobili anche quando le app corrispondenti in un dispositivo non sono attive. Rappresentano un elemento essenziale per le app consumer, in cui vengono usate per migliorare l'uso e l'interesse verso l'app. Le notifiche sono utili anche per le aziende, in cui la disponibilità di informazioni aggiornate aumenta la velocità di risposta dei dipendenti agli eventi aziendali.

Di seguito sono illustrati alcuni scenari di utilizzo delle notifiche su dispositivi mobili:

1.  Aggiornamento di un riquadro in Windows 8 o in Windows Phone con le ultime informazioni finanziarie.
2.  Comunicazione a un utente che gli è stato assegnato un elemento di lavoro mediante una notifica di tipo avviso popup in un'app aziendale basata sul flusso di lavoro.
3.  Visualizzazione di una notifica con il numero attuale di clienti potenziali in un'app CRM, ad esempio Microsoft Dynamics CRM.

##Funzionamento delle notifiche push

Le notifiche push vengono recapitate attraverso infrastrutture specifiche della piattaforma denominate _PNS_ (Platform Notification System, sistema di notifica tramite piattaforma). I sistemi PNS offrono funzioni limitate. Non forniscono, ad esempio, il supporto per la trasmissione o la personalizzazione e non offrono un'interfaccia comune. Ad esempio, per inviare una notifica a un'app di Windows Store, uno sviluppatore deve contattare Servizi notifica Push Windows (WNS). Per inviare una notifica a un dispositivo iOS, lo stesso sviluppatore deve contattare il servizio APN (Apple Push Notification Service) e inviare il messaggio una seconda volta. Hub di notifica di Azure fornisce un'interfaccia comune, insieme ad altre funzionalità per supportare le notifiche push tra le piattaforme.

A livello generale, tuttavia, i sistemi di notifica tramite piattaforma seguono tutti lo stesso modello:

1.  L'app client contatta il sistema PNS per recuperare il proprio _handle_. Il tipo di handle dipende dal sistema. Per WNS si tratta di un URI o un "canale di notifica", mentre per APNS si tratta di un token.
2.  L'app client archivia l'handle nel _back-end_ dell'app per l'uso successivo. Per WNS, il back-end è generalmente un servizio cloud. Per Apple, il sistema prende il nome di _provider_.
3.  Per inviare una notifica push, il back-end dell'app contatta il PNS usando l'handle per individuare una specifica istanza di app client.
4.  Il PNS inoltra quindi la notifica al dispositivo specificato dall'handle.

![][0]

##Difficoltà associate alle notifiche push

Seppur molto potenti, questi sistemi richiedono un grande impegno da parte degli sviluppatori di app per implementare anche i più comuni scenari di notifica push, come la trasmissione o l'invio di notifiche push a utenti segmentati.

Le notifiche push rappresentano attualmente una delle funzionalità più richieste nei servizi cloud per app mobili. L'infrastruttura richiesta per la gestione delle notifiche push è infatti piuttosto complessa e per lo più indipendente dalla logica di business principale dell'app. Di seguito sono illustrate alcune delle principali difficoltà associate alla creazione di un'infrastruttura push su richiesta:

- **Dipendenza dalla piattaforma.** Per poter inviare notifiche a dispositivi basati su piattaforme diverse, è necessario codificare nel back-end più tipi di interfacce. Non solo, infatti, i dettagli del codice a basso livello sono diversi, ma la presentazione stessa della notifica (riquadro, popup o badge) dipende dalla piattaforma. Queste differenze possono portare alla generazione di un codice di back-end complesso e difficile da gestire.

- **Scalabilità.** Il ridimensionamento di questa infrastruttura deve tenere in considerazione due aspetti:
	+ In base alle linee guida dei sistemi PNS,è necessario aggiornare i token di dispositivo a ogni avvio dell'app. Questo comporta la produzione di un grande volume di traffico e di conseguenti accessi al database solo per tenere aggiornati i token di dispositivo. Se il numero di dispositivi cresce, fino a raggiungere potenzialmente i milioni di unità, i costi per la creazione e la gestione dell'infrastruttura diventano piuttosto significativi.

	+ La maggior parte dei sistemi PNS non supporta la trasmissione a più dispositivi. Ne consegue che per la trasmissione a milioni di dispositivi è necessario effettuare milioni di chiamate ai sistemi PNS. Ridimensionare correttamente queste richieste è quindi un obiettivo difficile da raggiungere, perché in genere gli sviluppatori vogliono tenere bassa la latenza totale. L'ultimo dispositivo a cui viene inviato il messaggio, ad esempio, dovrebbe ricevere la notifica non più di 30 minuti dopo l'invio della prima notifica per non annullare il presupposto essenziale delle notifiche push.
- **Routing.** I sistemi PNS offrono un modo per inviare un messaggio a un dispositivo. Nella maggior parte delle app, tuttavia, le notifiche sono destinate a utenti e/o gruppi di interesse, ad esempio, tutti i dipendenti assegnati a un determinato account cliente. Per instradare le notifiche ai dispositivi corretti, il back-end dell'app deve quindi mantenere un registro in cui i vari gruppi di interesse siano associati ai relativi token di dispositivo. Questo aumento del carico di lavoro contribuisce inevitabilmente ad aumentare i tempi di produzione e i costi di manutenzione di un'app.

##Perché usare gli Hub di notifica?

Eliminano la complessità: non è necessario gestire le problematiche delle notifiche push se si utilizzano gli Hub di notifica. Usano un'infrastruttura completa per notifiche push con scalabilità orizzontale e multipiattaforma, in grado di ridurre considerevolmente il codice specifico del push eseguito nel back-end dell'app. Gli Hub di notifica implementano tutte le funzionalità di un'infrastruttura push. I dispositivi devono eseguire soltanto la registrazione dei propri handle PNS, mentre il back-end è responsabile dell'invio di messaggi indipendenti dalla piattaforma a utenti o gruppi di interesse.

![][1]


Gli hub di notifica forniscono un'infrastruttura per notifiche push pronta all'uso in grado di supportare:

- **Piattaforme multiple.**
	+  Supporto per tutte le principali piattaforme mobili. Gli hub di notifica possono inviare notifiche push ad app di Windows Store, per iOS, per Android e per Windows Phone.

	+  Gli hub di notifica forniscono un'interfaccia comune per l'invio di notifiche a tutte le piattaforme supportate. Non sono necessari i protocolli specifici della piattaforma. Il back-end dell'app può inviare notifiche sia nel formato specifico di una piattaforma sia in un formato indipendente dalle piattaforme. L'applicazione comunica solo con gli hub di notifica.

	+  Gestione dell'handle di dispositivo. Hub di notifica gestisce il registro degli handle e il feedback proveniente dai PNS.

- **Funziona con qualsiasi back-end**: cloud o in locale, .NET, PHP, Java, nodo e così via.

- **Scalabilità.** Gli hub di notifica garantiscono la scalabilità necessaria per inviare notifiche a milioni di dispositivi senza dover eseguire il partizionamento o la riprogettazione dell'architettura.


- **Insieme completo di modelli di recapito**:

	- *Trasmissione*: consente la trasmissione quasi simultanea a milioni di dispositivi con una singola chiamata API.

	- *Unicast/Multicast*: push a tag che rappresentano singoli utenti, inclusi tutti i relativi dispositivi, o il gruppo più ampio, ad esempio fattori di forma distinti (tablet e telefono).

	- *Segmentazione*: push a segmenti complessi definiti da espressioni tag (ad esempio dispositivi a New York che seguono gli Yankees).

	Quando invia l'handle a un hub di notifica, ogni dispositivo può specificare uno o più _tag_. Per altre informazioni sull'uso di [tag]. Non è necessario eliminare o eseguire preventivamente il provisioning dei tag. I tag offrono un modo molto semplice per inviare notifiche a utenti o gruppi di interesse. Poiché possono contenere qualsiasi identificatore specifico dell'app, ad esempio ID utente o di gruppo, i tag eliminano la necessità di usare il back-end dell'app per archiviare e gestire gli handle di dispositivo.

- **Personalizzazione**: ogni dispositivo può avere uno o più modelli, per ottenere la localizzazione per dispositivo e la personalizzazione senza influire sul codice di back-end.

- **Protezione**: accesso segreto condiviso (SAS) o autenticazione federata.

- **Telemetria avanzata**: disponibile nel portale e a livello di codice.


##Integrazione con le app per dispositivi mobili del servizio app

Per favorire un'esperienza lineare e uniforme nei servizi di Azure, le [app per dispositivi mobili del servizio app] comprendono il supporto predefinito per le notifiche push mediante Hub di notifica. Le [app per dispositivi mobili del servizio app] offrono una piattaforma di sviluppo di applicazioni mobili estremamente scalabile e disponibile a livello globale per sviluppatori aziendali e integratori di sistemi, che fornisce un set completo di funzionalità per gli sviluppatori di soluzioni per dispositivi mobili.

Gli sviluppatori di app per dispositivi mobili possono usare gli hub di notifica con il seguente flusso di lavoro:

1. Recuperare la gestione del dispositivo PNS
2. Registrare il dispositivo e i [modelli] con gli hub di notifica mediante il pratico API Register SDK client delle app per dispositivi mobili.
    + Si noti che le app per dispositivi mobili consentono di eliminare tutti i tag nelle registrazioni per motivi di sicurezza. Usare le hub di notifica dal back-end direttamente per associare tag ai dispositivi.
3. Invio di notifiche dal back-end dell'app con hub di notifica

Ecco alcuni vantaggi per gli sviluppatori inclusi in questa integrazione:

- **SDK client delle app per dispositivi mobili.** Questi SDK multipiattaforma forniscono API semplici per la registrazione e per comunicare automaticamente con l'hub di notifica collegato all'app per dispositivi mobili. Gli sviluppatori non hanno bisogno di cercare tra le credenziali di Hub di notifica e lavorare con un servizio aggiuntivo.
    + Gli SDK assegnano automaticamente tag al dispositivo specificato con l'ID utente autenticato di App per dispositivi mobili per abilitare il push all'utente.
    + L'ID di installazione di App per dispositivi mobili viene usato automaticamente dagli SDK come GUID per la registrazione in Hub di notifica. In questo modo gli sviluppatori non sono costretti a mantenere più GUID di servizio.
    
- **Modello di installazione.** App per dispositivi mobili usa il modello push più recente di Hub di notifica per rappresentare tutte le proprietà push associate a un dispositivo in un'installazione JSON che sia in linea con i servizi di notifica push e facile da usare.

- **Flessibilità.** Gli sviluppatori possono scegliere di usare direttamente Hub di notifica in qualsiasi momento, anche dopo l'integrazione.

- **Esperienza integrata nel [portale di Azure].** Il Push come funzionalità è rappresentato visivamente nelle app per dispositivi mobili e gli sviluppatori possono utilizzare facilmente l'hub di notifica associato tramite le App per dispositivi mobili.



##Passaggi successivi

Nei seguenti argomenti sono disponibili altre informazioni su Hub di notifica:

+ **[Uso di Hub di notifica da parte dei clienti]**

+ **[Esercitazioni e guide su Hub di notifica]**

+ **Introduzione ad Hub di notifica** ([iOS], [Android], [Windows Universal], [Windows Phone], [Kindle], [Xamarin.iOS], [Xamarin.Android])

Di seguito sono riportati i riferimenti pertinenti per l'API gestita .NET relativa alle notifiche push:

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications]


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
  [app per dispositivi mobili del servizio app]: https://azure.microsoft.com/it-IT/documentation/articles/app-service-mobile-value-prop/
  [modelli]: notification-hubs-templates-cross-platform-push-messages.md
  [portale di Azure]: https://portal.azure.com
  [tag]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)

<!---HONumber=AcomDC_0907_2016-->