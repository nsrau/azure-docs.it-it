<properties urlDisplayName="Get Started with Data" pageTitle="Introduzione ai dati (Windows Store JavaScript) | Mobile Developer Center" metaKeywords="" description="Informazioni su come iniziare a usare Servizi mobili per sfruttare i dati nell'app JavaScript per Windows Store." metaCanonical="https://www.windowsazure.com/it-it/develop/mobile/tutorials/get-started-with-data-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />


# Aggiungere Servizi mobili a un'app esistente
[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Questo argomento illustra come usare Servizi mobili di Azure per sfruttare i dati in un'app universale di Windows. Le soluzioni per app di Windows universali includono progetti di app di Windows Store 8.1 e Windows Phone Store 8.1 e un progetto condiviso comune. Per altre informazioni, vedere [Creare app di Windows universali per Windows e Windows Phone](http://msdn.microsoft.com/it-it/library/windows/apps/xaml/dn609832.aspx).

In questa esercitazione si scaricherà un progetto di Visual Studio 2013 per un'app universale di Windows che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.

>[WACOM.NOTE]Questo argomento illustra come usare gli strumenti in Visual Studio Express 2013 con aggiornamento 3 per Windows per connettere un nuovo servizio mobile a un'app di Windows universale. È possibile eseguire la stessa procedura per connettere un servizio mobile a un'app di Windows Store o Windows Phone Store 8.1. Per connettere un servizio mobile a un'app di Windows Phone 8.0 o Windows Phone Silverlight 8.1, vedere [Introduzione ai dati per Windows Phone](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data).

In questa esercitazione vengono descritte le operazioni di base seguenti:

1. [Scaricare il progetto di app di Windows Store][Scaricare l'app di Windows Store] 
2. [Creare il servizio mobile]
3. [Aggiungere una tabella dati per l'archiviazione]
4. [Aggiornare l'app per l'uso di Servizi mobili]
5. [Testare l'app in Servizi mobili]

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/it-it/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-it%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-javascript-get-started-data%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Express 2013 per Windows</a> con aggiornamento 2 o versione successiva. 

##<a name="download-app"></a>Scaricare il progetto GetStartedWithData

[WACOM.INCLUDE [mobile-services-windows-universal-javascript-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]

##<a name="create-service"></a>Creare un nuovo servizio mobile da Visual Studio

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="8">
<li><p>In Esplora soluzioni passare alla sottocartella <strong>services\mobileService\scripts</strong> aprire il file di script service.js e osservare la nuova variabile globale, che presenta un aspetto simile al seguente:</p> 

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>Questo codice consente di accedere al nuovo servizio mobile nell'app tramite una variabile globale. Per creare il client, è necessario specificare l'URI e la chiave dell'applicazione del nuovo servizio mobile. Poiché nel file default.html è stato aggiunto un riferimento a questo script, questa variabile è disponibile per tutti i file di script a cui viene fatto riferimento da questa pagina.</p>
</li>
<li><p>Aprire il file di progetto default.html, individuare il riferimento al nuovo file di script service.js e accertarsi che il percorso a cui fa riferimento sia simile al seguente:</p>
<pre><code>&lt;script src="/services/mobileServices/scripts/todolist.js"&gt;</script></code></pre>
<p>Attualmente esiste un bug in Visual Studio che causa la generazione di un nome di cartella errato nel percorso.</p></li>
<li><p>Fare clic con il pulsante destro del mouse sul progetto di app di Windows Phone, selezionare <strong>Aggiungi</strong> e fare clic su <strong>Servizio connesso...</strong>, quindi selezionare il servizio mobile appena creato e fare clic su <strong>OK</strong>. </p>
<p>Lo stesso nuovo file di codice viene aggiunto al progetto di app di Windows Phone Store. Accertarsi di correggere anche il percorso di riferimento aggiunto al file default.html.</p></li>
</ol>

##<a name="add-table"></a>Aggiungere una nuova tabella al servizio mobile

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

>[WACOM.NOTE]Le nuove tabelle create includono le colonne Id, __createdAt, __updatedAt e __version. Quando è abilitato lo schema dinamico, in Servizi mobili vengono generate automaticamente nuove colonne basate sull'oggetto JSON nella richiesta di inserimento o di aggiornamento. Per altre informazioni, vedere [Schema dinamico](http://msdn.microsoft.com/it-it/library/windowsazure/jj193175.aspx).

#<a name="update-app"></a>Aggiornare l'app per l'uso del servizio mobile

[WACOM.INCLUDE [mobile-services-windows-javascript-update-data-app](../includes/mobile-services-windows-javascript-update-data-app.md)]

##<a name="test-azure-hosted"></a>Testare il servizio mobile ospitato in Azure

Ora è possibile testare entrambe le versioni dell'app di Windows universale con il servizio mobile ospitato in Azure.

[WACOM.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

<ol start="4">
<li><p>Nel <a href="https://manage.windowsazure.com/" target="_blank">portale di gestione</a> fare clic su <strong>Servizi mobili</strong> e quindi sul servizio mobile.<p></li>
<li><p>Fare clic sulla scheda <strong>Dati</strong> e quindi su <strong>Sfoglia</strong>.</p>
<p>Si noti che la tabella <strong>TodoItem</strong> ora contiene dati, con valori ID generati da Servizi mobili, e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.</p></li>
</ol>

![](./media/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data/mobile-todoitem-data-browse.png)
     	
L'esercitazione **Introduzione ai dati** è terminata.

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione sono state illustrate le nozioni di base per consentire a un progetto di app di Windows universale di usare dati in Servizi mobili. In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

* [Convalidare e modificare dati mediante script]
  <br/>Altre informazioni sull'uso di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

* [Usare il paging per ridefinire le query]
  <br/>Altre informazioni su come usare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, provare a eseguire una delle esercitazioni seguenti:

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app.

* [Introduzione alle notifiche push] 
  <br/>Informazioni sull'invio di una notifica push di base all'app.

* [Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]
  <br/>Altre informazioni su come usare Servizi mobili con HTML e JavaScript.

<!-- Anchors. -->

[Scaricare l'app di Windows Store]: #download-app
[Creare il servizio mobile]: #create-service
[Aggiungere una tabella dati per l'archiviazione]: #add-table
[Aggiornare l'app per l'uso di Servizi mobili]: #update-app
[Testare l'app in Servizi mobili]: #test-app
[Passaggi successivi]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-quickstart-startup.png

[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png


<!-- URLs. -->
[Convalidare e modificare dati mediante script]: /it-it/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
[Usare il paging per ridefinire le query]: /it-it/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started/
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Portale di gestione]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Sito di esempi di codice per sviluppatori]:  http://go.microsoft.com/fwlink/p/?LinkID=510826
[Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]: /it-it/documentation/articles/mobile-services-html-how-to-use-client-library/
