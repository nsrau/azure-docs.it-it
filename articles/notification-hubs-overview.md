<properties 
	pageTitle="Hub di notifica di Azure" 
	description="Informazioni su come usare le notifiche push in Azure. Gli esempi di codice sono scritti in C# mediante l'API .NET." 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="notification-hubs" 
	documentationCenter=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/24/2015" 
	ms.author="wesmc"/>


#Hub di notifica di Azure

##Informazioni generali

Il supporto per le notifiche push in Azure consente di accedere a un'infrastruttura push facile da usare, multipiattaforma e con scalabilità orizzontale, che semplifica considerevolmente l'implementazione delle notifiche push sia per le applicazioni consumer sia per quelle aziendali per piattaforme mobili.

##Cosa sono le notifiche push?

Smartphone e tablet sono in grado di "notificare" agli utenti il verificarsi di un evento. Nelle applicazioni Windows Store la notifica può produrre un _avviso popup_, ovvero la visualizzazione di una finestra non modale associata a un avviso acustico. Analogamente, nei dispositivi Apple iOS la notifica push visualizza una finestra di dialogo in cui viene richiesto all'utente se desidera visualizzare o chiudere la notifica. Facendo clic su **Visualizza** è possibile aprire l'applicazione che sta ricevendo il messaggio.

Le notifiche push consentono ai dispositivi mobili di visualizzare informazioni aggiornate con un consumo minimo di energia. Rappresentano un elemento essenziale per le app consumer, in cui vengono usate per migliorare l'uso e l'interesse verso l'app. Le notifiche sono utili anche per le aziende, in cui la disponibilità di informazioni aggiornate aumenta la velocità di risposta dei dipendenti agli eventi aziendali. 

Di seguito sono illustrati alcuni scenari d'uso delle notifiche su dispositivi mobili:

1.  Aggiornamento di un riquadro in Windows 8 o in Windows Phone con le ultime informazioni finanziarie.
2.  Comunicazione a un utente che gli è stato assegnato un elemento di lavoro mediante una notifica di tipo avviso popup in un'app aziendale basata sul flusso di lavoro.
3.  Visualizzazione di una notifica con il numero attuale di clienti potenziali in un'app CRM, ad esempio Microsoft Dynamics CRM.

##Funzionamento delle notifiche push

Le notifiche push vengono recapitate attraverso infrastrutture specifiche della piattaforma denominate PNS (Platform Notification System, sistema di notifica tramite piattaforma). I sistemi PNS offrono funzioni limitate. Non forniscono, ad esempio, il supporto per la trasmissione o la personalizzazione e non offrono un'interfaccia comune. Per inviare una notifica a un'app di Windows Store, ad esempio, uno sviluppatore deve contattare Servizi notifica Push Windows (WNS). Per inviare una notifica a un dispositivo iOS, lo stesso sviluppatore deve contattare Apple Push Notification Service (APN) e inviare nuovamente il messaggio.

A livello generale, tuttavia, i sistemi di notifica tramite piattaforma seguono tutti lo stesso modello:

1.  L'app client contatta il sistema PNS per recuperare il proprio _handle_. Il tipo di handle dipende dal sistema. Per WNS si tratta di un URI o un "canale di notifica", mentre per il servizio APN si tratta di un token.
2.  L'app client archivia l'handle nel _back-end_ dell'app per l'uso successivo. Per WNS, il back-end è generalmente un servizio cloud. Per Apple, il sistema prende il nome di _provider_.
3.  Per inviare una notifica push, il back-end dell'app contatta il PNS usando l'handle per individuare una specifica istanza di app client.
4.  Il PNS inoltra quindi la notifica al dispositivo specificato dall'handle.

![][0]

##Difficoltà associate alle notifiche push

Seppur molto potenti, questi sistemi richiedono un grande impegno da parte degli sviluppatori di app per implementare anche i più comuni scenari di notifica push, come la trasmissione o l'invio di notifiche push a un utente.

Le notifiche push rappresentano attualmente una delle funzionalità più richieste nei servizi cloud per app mobili. L'infrastruttura richiesta per la gestione delle notifiche push è infatti piuttosto complessa e per lo più indipendente dalla logica di business principale dell'app. Di seguito sono illustrate alcune delle principali difficoltà associate alla creazione di un'infrastruttura push su richiesta:

- **Dipendenza dalla piattaforma.** Per poter inviare notifiche a dispositivi basati su piattaforme diverse, è necessario codificare nel back-end più tipi di interfacce. Non solo, infatti, i dettagli del codice a basso livello sono diversi, ma la presentazione stessa della notifica (riquadro, popup o badge) dipende dalla piattaforma. Queste differenze possono portare alla generazione di un codice di back-end complesso e difficile da gestire.

- **Scalabilità.** Per quanto riguarda il ridimensionamento di questa infrastruttura, è necessario prendere in considerazione due aspetti:
1. In base alle linee guida dei sistemi PNS,è necessario aggiornare i token di dispositivo a ogni avvio dell'app. Questo comporta la produzione di un grande volume di traffico e di conseguenti accessi al database solo per tenere aggiornati i token di dispositivo. Se il numero di dispositivi cresce, fino a raggiungere potenzialmente i milioni di unità, i costi per la creazione e la gestione dell'infrastruttura diventano piuttosto significativi.
2.  La maggior parte dei sistemi PNS non supporta la trasmissione a più dispositivi. Ne consegue che per la trasmissione a milioni di dispositivi è necessario effettuare milioni di chiamate ai sistemi PNS. Scalare correttamente queste richieste è pertanto un obiettivo difficile da raggiungere, poiché in genere gli sviluppatori desiderano tenere bassa la latenza totale. L'ultimo dispositivo a cui viene inviato il messaggio, ad esempio, dovrebbe ricevere la notifica non più di 30 minuti dopo l'invio della prima notifica per non annullare il presupposto essenziale delle notifiche push.
- **Routing.** I sistemi PNS offrono un modo per inviare un messaggio a un dispositivo. Nella maggior parte delle app, tuttavia, le notifiche sono destinate a utenti e/o gruppi di interesse, ad esempio, tutti i dipendenti assegnati a un determinato account cliente. Per instradare le notifiche ai dispositivi corretti, il back-end dell'app deve quindi mantenere un registro in cui i vari gruppi di interesse siano associati ai relativi token di dispositivo. Questo aumento del carico di lavoro contribuisce inevitabilmente ad aumentare i tempi di produzione e i costi di manutenzione di un'app.

##Perché usare gli Hub di notifica?

Gli hub di notifica forniscono un'infrastruttura per notifiche push pronta all'uso in grado di supportare:

- **Piattaforme multiple.** Gli hub di notifica forniscono un'interfaccia comune per l'invio di notifiche a tutte le piattaforme supportate.Il back-end dell'app può inviare notifiche sia nel formato specifico di una piattaforma sia in un formato indipendente dalle piattaforme. Gli hub di notifica possono inviare notifiche push ad app di Windows Store, per iOS, per Android e per Windows Phone.
- **Routing di pubblicazione/sottoscrizione.** Quando invia l'handle a un hub di notifica, ogni dispositivo può specificare uno o più _tag_. Per altre informazioni sui tag, vedere la seguente sezione. Non è necessario eliminare o eseguire preventivamente il provisioning dei tag. I tag offrono un modo molto semplice per inviare notifiche a utenti o gruppi di interesse. Poiché possono contenere qualsiasi identificatore specifico dell'app, ad esempio ID utente o di gruppo, i tag eliminano la necessità di usare il back-end dell'app per archiviare e gestire gli handle di dispositivo.
- **Scalabilità.** Gli hub di notifica garantiscono la scalabilità necessaria per inviare notifiche a milioni di dispositivi senza dover eseguire il partizionamento o la riprogettazione dell'architettura.

Usano un'infrastruttura per notifiche push con scalabilità orizzontale e completamente multipiattaforma, in grado di ridurre considerevolmente il codice specifico del push eseguito nel back-end dell'app. Implementano inoltre tutte le funzionalità di un'infrastruttura push. I dispositivi devono eseguire soltanto la registrazione dei propri handle PNS, mentre il back-end è responsabile dell'invio di messaggi indipendenti dalla piattaforma a utenti o gruppi di interesse.

![][1]

##Passaggi successivi

Nei seguenti argomenti sono disponibili altre informazioni su Hub di notifica:

+ **[Uso di Hub di notifica da parte dei clienti]**

+ **[Esercitazioni e guide su HUB di notifica]** 

+ **Introduzione ad Hub di notifica** ([iOS], [Android], [Windows Universal], [Windows Phone], [Kindle], [Xamarin.iOS], [Xamarin.Android])

Di seguito sono riportati i riferimenti pertinenti per l'API gestita .NET relativa alle notifiche push:

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications] 


  [0]: ./media/notification-hubs-overview/SBPushNotifications1.gif
  [1]: ./media/notification-hubs-overview/SBPushNotifications2.gif
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
  

<!--HONumber=49-->