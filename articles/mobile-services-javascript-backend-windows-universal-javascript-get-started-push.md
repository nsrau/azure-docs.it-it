<properties 
	pageTitle="Introduzione alle notifiche push mediante un servizio mobile back-end JavaScript" 
	description="Informazioni su come usare Servizi mobili di Azure e Hub di notifica per inviare notifiche push all'app Windows universale." 
	services="mobile-services, notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/15/2014" 
	ms.author="glenga"/>


# Aggiungere notifiche push all'app di Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Questo argomento descrive come usare Servizi mobili di Azure con un back-end JavaScript per inviare notifiche push a un'app Windows universale. In questa esercitazione si userà Hub di notifica di Azure per abilitare le notifiche push nel progetto di app universale di Windows. Al termine dell'esercitazione, il servizio mobile invierà una notifica push dal back-end JavaScript a tutte le app di Windows Store e Windows Phone Store registrate ogni volta che viene inserita una tabella TodoList. L'hub di notifica creato può essere usato gratuitamente con il servizio mobile, può essere gestito indipendentemente da quest'ultimo e può essere usato da altri servizi e applicazioni.

>[AZURE.NOTE]Questo argomento descrive come usare gli strumenti in Visual Studio 2013 con aggiornamento 3 per aggiungere il supporto per le notifiche push di Servizi mobili a un'app di Windows universale. La stessa procedura può essere eseguita per aggiungere le notifiche push da Servizi mobili a un'app di Windows Store o Windows Phone Store 8.1. Se non è possibile eseguire l'aggiornamento a Visual Studio 2013 aggiornamento 3 o si preferisce aggiungere manualmente il progetto di servizio mobile a una soluzione app di Windows Store, vedere [questa versione](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push) dell'argomento.

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1. [Registrare l'app per le notifiche push](#register)
2. [Aggiornare il servizio per l'invio di notifiche push](#update-service)
3. [Testare le notifiche push nell'app](#test)

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un [account di Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045) attivo.
* [Visual Studio 2013 Express per Windows](http://go.microsoft.com/fwlink/?LinkId=257546) con Update 3 o versione successiva 

##<a id="register"></a>Registrare l'app per le notifiche push

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Accedere alla cartella del progetto <code>\services\mobileServices\scripts</code>, copiare il file di script &lt;<em>nome_servizio</em>&gt;.push.register.js generato nella cartella<code>\js</code> condivisa, quindi eliminare questo file da entrambi i progetti di app Windows e WindowsPhone.</p></li> 
<li><p>Aprire il file di script nella cartella del progetto <code>\js</code> condivisa, individuare il codice nel listener di eventi <em>activated</em> che registra l'URL del canale del dispositivo con l'hub di notifica, quindi eliminare la funzione di promessa <strong>done</strong>.</p>
<p>In questa esercitazione le notifiche vengono inviate in corrispondenza dell'inserimento di un nuovo elemento e non della chiamata di un'API personalizzata.</p></li>
<li><p>Nel progetto di app di Windows aprire il file default.html e modificare il percorso del riferimento al file di script con la cartella di progetto <code>\js</code> condivisa, in modo che sia simile al seguente:</p><pre><code>&lt;script src="/js/your_service_name.push.register.js"&gt;&lt;/script&gt;</code></pre></li>
<li><p>Ripetere il passaggio per il progetto diapp di Windows Phone.</p>
<p>Ora entrambi i progetti usano una versione condivisa dello script di registrazione push.</p></li>
</ol>

Ora che le notifiche push sono abilitate nell'app, è necessario aggiornare il servizio mobile per l'invio delle notifiche push. 

##<a id="update-service"></a>Aggiornare il servizio per l'invio di notifiche push

La procedura seguente consente di aggiornare lo script insert registrato nella tabella TodoItem. È possibile implementare un codice simile in qualsiasi script del server o in qualsiasi altro punto dei propri servizi di back-end. 

[AZURE.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

##<a id="test"></a> Testare le notifiche push nell'app

[AZURE.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push](../includes/mobile-services-javascript-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Passaggi successivi

Questa esercitazione illustra le nozioni di base per consentire alle app di Windows Store di usare Servizi mobili e Hub di notifica per l'invio delle notifiche push. In seguito, provare a eseguire l'esercitazione [Inviare notifiche push agli utenti autenticati], che illustra come usare i tag per inviare notifiche push da un servizio mobile a un solo utente autenticato.

Per altre informazioni su Servizi mobili e su Hub di notifica, fare riferimento ai seguenti argomenti:

* [Introduzione ai dati]
  <br/>Altre informazioni sull'archiviazione e l'esecuzione di query sui dati tramite Servizi mobili.

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app con tipi di account diversi mediante servizi mobili.

* [Informazioni su Hub di notifica]
  <br/>Altre informazioni sull'uso di Hub di notifica per recapitare le notifiche alle app in tutte le principali piattaforme client.

* [Eseguire il debug delle applicazioni di Hub di notifica](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Informazioni aggiuntive sulla risoluzione dei problemi e sul debug di soluzioni Hub di notifica. 

* [Come usare un client HTML/JavaScript per Servizi mobili di Azure]
  <br/>Altre informazioni su come usare Servizi mobili da app HTML e JavaScript.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Pagina Invia un'app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-data
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-users

[Inviare notifiche push agli utenti autenticati]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users/

[Informazioni su Hub di notifica]: /it-it/documentation/articles/notification-hubs-overview/

[Come usare un client HTML/JavaScript per Servizi mobili di Azure]: /it-it/documentation/articles/mobile-services-html-how-to-use-client-library



<!--HONumber=42-->
