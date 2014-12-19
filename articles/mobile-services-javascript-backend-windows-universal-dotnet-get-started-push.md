<properties pageTitle="Introduzione alle notifiche push mediante un servizio mobile back-end JavaScript" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/27/2014" ms.author="glenga" />


# Aggiungere notifiche push all'app di Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Questo argomento illustra come usare Servizi mobili di Azure con un back-end JavaScript per inviare notifiche push a un'app di Windows universale. In questa esercitazione si userà Hub di notifica di Azure per abilitare le notifiche push nel progetto di app universale di Windows. Al termine dell'esercitazione, il servizio mobile invierà una notifica push dal back-end JavaScript a tutte le app di Windows Store e Windows Phone Store registrate ogni volta che viene inserita una tabella TodoList. L'hub di notifica creato può essere usato gratuitamente con il servizio mobile, può essere gestito indipendentemente da quest'ultimo e può essere usato da altri servizi e applicazioni.

>[WACOM.NOTE]Questo argomento illustra come usare gli strumenti di Visual Studio 2013 con aggiornamento 3 per aggiungere il supporto per le notifiche push inviate da Servizi mobili a un'app di Windows universale. La stessa procedura può essere eseguita per aggiungere le notifiche push da Servizi mobili a un'app di Windows Store o Windows Phone Store 8.1. Per aggiungere le notifiche push a un'app di Windows Phone 8 o Windows Phone Silverlight 8.1, vedere questa versione della guida [Introduzione alle notifiche push in Servizi mobili](/it-it/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push).

> Se non è possibile eseguire l'aggiornamento a Visual Studio 2013 aggiornamento 3 o se si preferisce aggiungere manualmente il proprio progetto di servizio mobile a una soluzione app di Windows Store, vedere [questa versione](/it-it/documentation/articles/mobile-services-javscript-backend-windows-store-dotnet-get-started-push) dell'argomento.

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1. [Registrare l'app per le notifiche push](#register)
2. [Aggiornare il servizio per l'invio di notifiche push](#update-service)
3. [Testare le notifiche push nell'app](#test)

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un [account di Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045) attivo.
* [Visual Studio 2013 Express per Windows](http://go.microsoft.com/fwlink/?LinkId=257546) con aggiornamento 3 o versione successiva

##<a id="register"></a>Registrare l'app per le notifiche push

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Passare alla cartella di progetto <code>\Services\MobileServices\nome_servizio</code>, <strong>UploadChannel</strong> che registra l'URL del canale del dispositivo con l'hub di notifica.</p></li> 
<li><p>Aprire il file di codice App.xaml.cs condiviso. Si noti come una chiamata al nuovo metodo <strong>UploadChannel</strong> è stata aggiunta al gestore eventi <strong>OnLaunched</strong>.</p> <p>In questo modo verrà effettuato un tentativo di registrazione del dispositivo a ogni avvio dell'app.</p></li>
<li><p>Ripetere i passaggi precedenti per aggiungere notifiche push al progetto di app di Windows Phone Store. Quindi, nel file App.xaml.cs condiviso, rimuovere la chiamata supplementare a <strong>UploadChannel</strong> e il wrapper condizionale <code>#if...#endif</code> restante.</p> <p>Ora entrambi i progetti possono condividere un'unica chiamata a <strong>UploadChannel</strong>.</p>
<div class="dev-callout"><strong>Nota</strong> <p>È anche possibile semplificare il codice generato unendo le definizioni <code>MobileServiceClient</code> <a href="http://msdn.microsoft.com/it-it/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">#if...#endif</a> con wrapping in un'unica definizione  senza wrapping usata da entrambe le versioni dell'app.</p></div></li>
</ol>

Ora che le notifiche push sono abilitate nell'app, è necessario aggiornare il servizio mobile per l'invio delle notifiche push. 

##<a id="update-service"></a>Aggiornare il servizio per l'invio di notifiche push

I passaggi seguenti aggiornano lo script insert registrato nella tabella TodoItem. È possibile implementare un codice simile in qualsiasi script del server o in qualsiasi altro punto dei propri servizi di back-end. 

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]


##<a id="test"></a> Testare le notifiche push nell'app

[WACOM.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push](../includes/mobile-services-javascript-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione sono state illustrate le nozioni di base per consentire a un'app di Windows Store di usare Servizi mobili e Hub di notifica per l'invio di notifiche push. In seguito, è consigliabile eseguire l'esercitazione [Inviare notifiche push agli utenti autenticati], che illustra come usare i tag per inviare notifiche push da un servizio mobile a un solo utente autenticato.

Per altre informazioni su Servizi mobili e su Hub di notifica, fare riferimento ai seguenti argomenti:

* [Introduzione ai dati]
  <br/>Altre informazioni sull'archiviazione e l'esecuzione di query sui dati tramite Servizi mobili.

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app con tipi di account diversi mediante i servizi mobili.

* [Informazioni su Hub di notifica]
  <br/>Altre informazioni sull'uso di Hub di notifica per recapitare le notifiche alle app in tutte le principali piattaforme client.

* [Eseguire il debug delle applicazioni dell'Hub di notifica](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Informazioni sulla risoluzione dei problemi e sul debug di soluzioni dell'Hub di notifica. 

* [Come usare un client .NET per Servizi mobili di Azure]
  <br/>Altre informazioni su come usare Servizi mobili dalle app di Windows in C#.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Pagina per l'invio di app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-users

[Inviare notifiche push agli utenti autenticati]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/

[Informazioni su Hub di notifica]: /it-it/documentation/articles/notification-hubs-overview/

[Come usare un client .NET per Servizi mobili di Azure]: /it-it/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
