<properties 
	pageTitle="Introduzione alle notifiche push mediante un servizio mobile back-end .NET" 
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
	ms.date="09/27/2014" 
	ms.author="glenga"/>


# Aggiungere notifiche push all'app di Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Questo argomento descrive come usare Servizi mobili di Azure con un back-end .NET per inviare notifiche push a un'app universale di Windows. In questa esercitazione si userà Hub di notifica di Azure per abilitare le notifiche push nel progetto di app universale di Windows. Al termine dell'esercitazione, il servizio mobile in uso invierà una notifica push dal back-end .NET a tutte le app di Windows Store e Windows Phone Store registrate ogni volta che viene inserito un record nella tabella TodoList. L'hub di notifica creato può essere usato gratuitamente con il servizio mobile, può essere gestito indipendentemente da quest'ultimo e può essere usato da altri servizi e applicazioni.

>[AZURE.NOTE] Questo argomento descrive come usare gli strumenti in Visual Studio Professional 2013 con aggiornamento 3 per aggiungere il supporto per le notifiche push di Servizi mobili a un'app di Windows universale. La stessa procedura può essere eseguita per aggiungere le notifiche push da Servizi mobili a un'app di Windows Store o Windows Phone Store 8.1. Se non è possibile eseguire l'aggiornamento a Visual Studio Professional 2013 aggiornamento 3 o si preferisce aggiungere manualmente il progetto di servizio mobile a una soluzione app di Windows Store, vedere [questa versione](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push) dell'argomento.

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1. [Registrare l'app per le notifiche push](#register)
2. [Aggiornare il servizio per l'invio di notifiche push](#update-service)
3. [Abilitare le notifiche push per test locali](#local-testing)
4. [Testare le notifiche push nell'app](#test)

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un [account di Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045) attivo.
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Professional 2013</a> (aggiornamento 3 o versione successiva). <br/>È disponibile una versione di valutazione gratuita. 

##<a id="register"></a>Registrare l'app per le notifiche push

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Passare alla <code>\services\mobileServices\scripts</code> cartella di progetto, copiare il file di script &lt;<em>your_service_name</em>&gt;.push.register.js generato nella cartella <code>\js</code> condivisa, quindi eliminare questo file da entrambi i progetti di app Windows e WindowsPhone.</p></li> 
<li><p>Aprire il file di script nella <code>\js</code> cartella di progetto , condivisa, individuare il codice nel listener di eventi <em>activated</em> che registra l'URL del canale del dispositivo con l'hub di notifica, quindi eliminare la funzione di promessa <strong>done</strong>.</p>
<p>In questa esercitazione le notifiche vengono inviate in corrispondenza dell'inserimento di un nuovo elemento e non della chiamata di un'API personalizzata.</p></li>
<li><p>Nel progetto di app di Windows aprire il file default.html e modificare il percorso del riferimento al file di script con la cartella di progetto <code>\js</code> condivisa, in modo che sia simile al seguente:</p><pre><code>&lt;script src="/js/your_service_name.push.register.js"&gt;&lt;/script&gt;</code></pre></li>
<li><p>Ripetere il passaggio per il progetto diapp di Windows Phone.</p>
<p>Ora entrambi i progetti usano una versione condivisa dello script di registrazione push.</p></li>
</ol>

Ora che le notifiche push sono abilitate nell'app, è necessario aggiornare il servizio mobile per l'invio delle notifiche push. 

##<a id="update-service"></a>Aggiornare il servizio per l'invio di notifiche push

Seguire i seguenti passaggi per aggiornare la classe TodoItemController esistente in modo da inviare una notifica push a tutti i dispositivi registrati quando viene inserito un nuovo elemento. È possibile implementare un codice simile in qualsiasi [ApiController] e [TableController] personalizzato o in qualsiasi altro punto dei propri servizi di back-end. 

[AZURE.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> Abilitare le notifiche push per test locali

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

I seguenti passaggi di questa sezione sono facoltativi e consentono di testare l'app con il proprio servizio mobile in esecuzione su un computer locale. Se si desidera testare le notifiche push usando il servizio mobile in esecuzione su Azure, è possibile passare direttamente all'ultima sezione, in quanto la procedura guidata Aggiungi notifica Push ha già configurato l'app per la connessione al proprio servizio in esecuzione su Azure.  

>[AZURE.NOTE] Non usare mai un servizio mobile di produzione per operazioni di test e sviluppo. Pubblicare sempre il proprio progetto di servizio mobile in un ambiente di gestione temporanea per i test.

<ol start="5">
<li><p>Passare alla <code>\services\mobileServices\settings</code> cartella di progetto, copiare il file di script &lt;<em>your_service_name</em>&gt;.js generato nella cartella <code>\js</code> di progetto condivisa, quindi eliminare questo file da entrambi i progetti di app Windows e WindowsPhone. Eliminare questo file anche dalla <code>\services\mobileServices\scripts</code> cartella in ciascun progetto di app se presente.</p></li> 
<li><p>Aprire il file di script nella <code>\js</code> cartella di progetto URL e impostare come commento il codice esistente che definisce l'oggetto <a href="http://msdn.microsoft.com/library/azure/jj554219.aspx">MobileServiceClient object</a> usato per accedere al servizio mobile in esecuzione su Azure.</p></li>
<li><p>Aggiungere una nuova definizione dell'oggetto <strong>MobileServiceClient</strong> con lo stesso nome, ma usando l'URL dell'host locale nel costruttore, in modo simile all'esempio seguente:</p>
<pre><code>// This MobileServiceClient has been configured to communicate with your local
// test project for debugging purposes.
var todolistClient = new WindowsAzure.MobileServiceClient(
	"http://localhost:4584");
</code></pre><p>Usando questo oggetto <strong>MobileServiceClient</strong>, l'app si connette al servizio locale e non alla versione ospitata in Azure. Per tornare indietro ed eseguire l'app sul servizio mobile ospitato in Azure, ripristinare le definizioni dell'oggetto <strong>MobileServiceClient</strong> originali.</p></li>
</ol>

##<a id="test"></a> Testare le notifiche push nell'app

[AZURE.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

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
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-users

[Inviare notifiche push agli utenti autenticati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/

[Informazioni su Hub di notifica]: /it-it/documentation/articles/notification-hubs-overview/

[Come usare un client HTML/JavaScript per Servizi mobili di Azure]: /it-it/documentation/articles/mobile-services-html-how-to-use-client-library



<!--HONumber=42-->
