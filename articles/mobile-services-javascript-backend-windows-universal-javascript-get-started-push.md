<properties pageTitle="Get started with push notification using a JavaScript backend mobile service" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile-services,notification-hubs" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/15/2014" ms.author="glenga" />

# Introduzione alle notifiche push in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure con un back-end JavaScript per inviare notifiche push a un'app Windows universale. In questa esercitazione si utilizzerà Hub di notifica di Azure per abilitare le notifiche push nel progetto di app universale di Windows. Al termine dell'esercitazione, il servizio mobile invierà una notifica push dal back-end JavaScript a tutte le app Windows Store e Windows Phone Store registrate ogni volta che viene inserita una tabella TodoList. L'hub di notifica creato può essere utilizzato gratuitamente con il servizio mobile, può essere gestito indipendentemente da quest'ultimo e può essere utilizzato da altri servizi e applicazioni.

> [WACOM.NOTE]In questo argomento viene illustrato come utilizzare gli strumenti di Visual Studio 2013 con aggiornamento 3 per aggiungere il supporto per le notifiche push inviate da Servizi mobili a un'app Windows universale. La stessa procedura può essere eseguita per aggiungere le notifiche push da Servizi mobili a un'app Windows Store o Windows Phone Store 8.1. Se non è possibile eseguire l'aggiornamento a Visual Studio 2013 aggiornamento 3 o se si preferisce aggiungere manualmente il proprio progetto mobile a una soluzione di app Windows Store, consultare [questa versione][questa versione] dell'argomento.

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Registrazione dell'app per le notifiche push][Registrazione dell'app per le notifiche push]
2.  [Aggiornamento del servizio per l'invio di notifiche push][Aggiornamento del servizio per l'invio di notifiche push]
3.  [Esecuzione del test delle notifiche push nell'app][Esecuzione del test delle notifiche push nell'app]

Per completare l'esercitazione, sono necessari gli elementi seguenti:

-   Un [account di Microsoft Store][account di Microsoft Store] attivo.
-   [Visual Studio 2013 Express per Windows][Visual Studio 2013 Express per Windows] con aggiornamento 3 o versione successiva

## <span id="register"></span></a>Registrazione dell'app per le notifiche push

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Accedere alla cartella del progetto <code>\services\mobileServices\scripts</code>, copiare il file di script &lt;<em>your_service_name</em>&gt;.push.register.js generato nella cartella <code>\js</code> condivisa, quindi eliminare questo file da entrambi i progetti di app Windows e WindowsPhone.</p></li>

<li><p>Aprire il file di script nella cartella del progetto <code>\js</code> condivisa, individuare il codice nel listener di eventi <em>activated</em> che registra l'URL del canale del dispositivo con l'hub di notifica, quindi eliminare la funzione di promessa <b>done</b>.</p></li>

    <p>In questa esercitazione le notifiche vengono inviate in corrispondenza dell'inserimento di un nuovo elemento e non della chiamata di un'API personalizzata.</p></li>

<li><p>Nel progetto di app Windows, aprire il file default.html e modificare il percorso del riferimento al file di script con la cartella di progetto <code>\js</code> condivisa, nel seguente modo:</p>

<pre><code>&lt;script src="/js/your_service_name.push.register.js"&gt;&lt;/script&gt;</code></pre></li>

<li><p>Ripetere il passaggio per il progetto di app WindowsPhone.</p>

    <p>Ora entrambi i progetti utilizzano una versione condivisa dello script di registrazione push.</p></li>
</ol>

Ora che le notifiche push sono abilitate nell'app, è necessario aggiornare il servizio mobile per l'invio delle notifiche push.

## <span id="update-service"></span></a>Aggiornamento del servizio per l'invio di notifiche push

I seguenti passaggi aggiornano lo script insert registrato nella tabella TodoItem. È possibile implementare un codice simile in qualsiasi script del server o in qualsiasi altro punto dei propri servizi di back-end.

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

## <span id="test"></span></a>Esecuzione del test delle notifiche push nell'app

[WACOM.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push](../includes/mobile-services-javascript-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione vengono illustrate le nozioni di base per consentire alle app di Windows Store di utilizzare Servizi mobili e Hub di notifica per l'invio delle notifiche push. In seguito si consiglia di passare all'esercitazione seguente, [Invio di notifiche push agli utenti autenticati][Invio di notifiche push agli utenti autenticati], che spiega come utilizzare i tag per inviare notifiche push da un servizio mobile unicamente agli utenti autenticati.

Per altre informazioni su Servizi mobili e su Hub di notifica, fare riferimento ai seguenti argomenti:

-   [Introduzione ai dati][Introduzione ai dati]
    Altre informazioni sull'archiviazione e sulle query dei dati mediante i servizi mobili.

-   [Introduzione all'autenticazione][Introduzione all'autenticazione]
    Informazioni sull'autenticazione degli utenti dell'app con tipi di account diversi mediante i servizi mobili.

-   [Informazioni su Hub di notifica][Informazioni su Hub di notifica]
    Altre informazioni sull'uso di Hub di notifica per recapitare le notifiche alle app in tutte le principali piattaforme client.

-   [Come utilizzare un client HTML/JavaScript per Servizi mobili di Azure][Come utilizzare un client HTML/JavaScript per Servizi mobili di Azure]
    Informazioni su come utilizzare Servizi mobili dalle app HTML e JavaScript.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started-push]: ../includes/mobile-services-selector-get-started-push.md
  [questa versione]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push
  [Registrazione dell'app per le notifiche push]: #register
  [Aggiornamento del servizio per l'invio di notifiche push]: #update-service
  [Esecuzione del test delle notifiche push nell'app]: #test
  [account di Microsoft Store]: http://go.microsoft.com/fwlink/p/?LinkId=280045
  [Visual Studio 2013 Express per Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
  [mobile-services-create-new-push-vs2013]: ../includes/mobile-services-create-new-push-vs2013.md
  [mobile-services-javascript-update-script-notification-hubs]: ../includes/mobile-services-javascript-update-script-notification-hubs.md
  [mobile-services-javascript-backend-windows-universal-test-push]: ../includes/mobile-services-javascript-backend-windows-universal-test-push.md
  [Invio di notifiche push agli utenti autenticati]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users/
  [Introduzione ai dati]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-data
  [Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-users
  [Informazioni su Hub di notifica]: /it-it/documentation/articles/notification-hubs-overview/
  [Come utilizzare un client HTML/JavaScript per Servizi mobili di Azure]: /it-it/documentation/articles/mobile-services-html-how-to-use-client-library
