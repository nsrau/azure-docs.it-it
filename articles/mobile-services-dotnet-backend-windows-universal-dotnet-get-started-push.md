<properties pageTitle="Introduzione alle notifiche push mediante un servizio mobile back-end .NET" metaKeywords="" description="Informazioni su come usare Servizi mobili di Azure e Hub di notifica per inviare notifiche push all'app Windows universale." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/27/2014" ms.author="glenga" />

# Aggiungere notifiche push all'app di Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Questo argomento illustra come usare Servizi mobili di Azure con un back-end .NET per inviare notifiche push a un'app universale di Windows. In questa esercitazione si userà Hub di notifica di Azure per abilitare le notifiche push nel progetto di app universale di Windows. Al termine dell'esercitazione, il servizio mobile in uso invierà una notifica push dal back-end .NET a tutte le app di Windows Store e Windows Phone Store registrate ogni volta che viene inserito un record nella tabella TodoList. L'hub di notifica creato può essere usato gratuitamente con il servizio mobile, può essere gestito indipendentemente da quest'ultimo e può essere usato da altri servizi e applicazioni.

>[WACOM.NOTE]Questo argomento illustra come usare gli strumenti in Visual Studio Professional 2013 con aggiornamento 3 per aggiungere il supporto per le notifiche push di Servizi mobili a un'app universale di Windows. La stessa procedura può essere eseguita per aggiungere le notifiche push da Servizi mobili a un'app di Windows Store o Windows Phone Store 8.1. Per aggiungere le notifiche push a un'app di Windows Phone 8 o Windows Phone Silverlight 8.1, vedere questa versione della guida [Introduzione alle notifiche push in Servizi mobili](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push).

> Se non è possibile eseguire l'aggiornamento a Visual Studio Professional 2013 aggiornamento 3 o se si preferisce aggiungere manualmente il proprio progetto mobile a una soluzione di app di Windows Store, consultare [questa versione](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push) dell'argomento.

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1. [Registrare l'app per le notifiche push](#register)
2. [Aggiornare il servizio per l'invio di notifiche push](#update-service)
3. [Abilitare le notifiche push per test locali](#local-testing)
4. [Testare le notifiche push nell'app](#test)

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un [account di Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045) attivo.
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Professional 2013</a> (aggiornamento 3 o versione successiva). <br/>È disponibile una versione di valutazione gratuita. 

##<a id="register"></a>Registrare l'app per le notifiche push

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Passare alla cartella di progetto <code>\Services\MobileServices\nome_servizio</code>, <strong>UploadChannel</strong> che registra l'URL del canale del dispositivo con l'hub di notifica.</p></li> 
<li><p>Aprire il file di codice App.xaml.cs condiviso. Si noti come una chiamata al nuovo metodo <strong>UploadChannel</strong> è stata aggiunta al gestore eventi <strong>OnLaunched</strong>.</p> <p>In questo modo verrà effettuato un tentativo di registrazione del dispositivo a ogni avvio dell'app.</p></li>
<li><p>Ripetere i passaggi precedenti per aggiungere notifiche push al progetto di app di Windows Phone Store. Quindi, nel file App.xaml.cs condiviso, rimuovere la chiamata supplementare a <strong>UploadChannel</strong> e il wrapper condizionale <code>#if...#endif</code> restante.</p> <p>Ora entrambi i progetti possono condividere un'unica chiamata a <strong>UploadChannel</strong>.</p>
<div class="dev-callout"><strong>Nota</strong> <p>È anche possibile semplificare il codice generato unendo le definizioni <code>#if...#endif</code> <a href="http://msdn.microsoft.com/it-it/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> con wrapping in un'unica definizione  senza wrapping usata da entrambe le versioni dell'app.</p></div></li>
</ol>

Ora che le notifiche push sono abilitate nell'app, è necessario aggiornare il servizio mobile per l'invio delle notifiche push. 

##<a id="update-service"></a>Aggiornare il servizio per l'invio di notifiche push

Seguire i seguenti passaggi per aggiornare la classe TodoItemController esistente per inviare una notifica push a tutti i dispositivi registrati quando viene inserito un nuovo elemento. È possibile implementare un codice simile in qualsiasi [ApiController] e [TableController] personalizzato o in qualsiasi altro punto dei propri servizi di back-end. 

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> Abilitare le notifiche push per test locali

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

I seguenti passaggi di questa sezione sono facoltativi e consentono di testare l'app con il proprio servizio mobile in esecuzione su un computer locale. Se si desidera testare le notifiche push usando il servizio mobile in esecuzione su Azure, è possibile passare direttamente all'ultima sezione, in quanto la procedura guidata Aggiungi notifica Push ha già configurato l'app per la connessione al proprio servizio in esecuzione su Azure. 

>[WACOM.NOTE]Non usare mai un servizio mobile di produzione per operazioni di test e sviluppo. Pubblicare sempre il proprio progetto di servizio mobile in un ambiente di gestione temporanea per i test.

<ol start="5">
<li><p>Aprire il file di progetto App.xaml.cs condiviso e individuare qualsiasi riga di codice che crei una nuova istanza della classe <a href="http://msdn.microsoft.com/it-it/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> per accedere al servizio mobile in esecuzione su Azure.</p></li>
<li><p>Rimuovere quindi i simboli di commento dal codice e aggiungere codice per creare una nuova classe <a href="http://msdn.microsoft.com/it-it/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> con lo stesso nome, ma che usa l'URL dell'host locale nel costruttore, analogamente a quanto segue:</p>
<pre><code>// This MobileServiceClient has been configured to communicate with your local
// test project for debugging purposes.
public static MobileServiceClient todolistClient = new MobileServiceClient(
	"http://localhost:4584"
);
</code></pre><p>Usando questo <a href="http://msdn.microsoft.com/it-it/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a>, l'app si connetterà al servizio locale anziché alla versione ospitata in Azure. Quando si vuole tornare indietro ed eseguire l'app sul servizio mobile ospitato in Azure, tornare alle definizioni di <a href="http://msdn.microsoft.com/it-it/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> originali.</p></li>
</ol>

##<a id="test"></a> Testare le notifiche push nell'app

[WACOM.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

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
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users

[Inviare notifiche push agli utenti autenticati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/

[Informazioni su Hub di notifica]: /it-it/documentation/articles/notification-hubs-overview/

[Come usare un client .NET per Servizi mobili di Azure]: /it-it/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
