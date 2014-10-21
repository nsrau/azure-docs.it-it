<properties linkid="develop-mobile-tutorials-get-started-with-data-js-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store JavaScript app." metaCanonical="https://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Introduzione ai dati in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-data][]]

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure per sfruttare i dati in un'app universale di Windows. Le soluzioni per app Windows universali includono progetti di app per Windows Store 8.1 e Windows Phone Store 8.1 e un progetto condiviso comune. Per altre informazioni, vedere [Creare app Windows universali per Windows e Windows Phone][].

In questa esercitazione si scaricherà un progetto di Visual Studio 2013 per un'app universale di Windows che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.

> [WACOM.NOTE]In questo argomento viene illustrato come utilizzare gli strumenti in Visual Studio Express 2013 con aggiornamento 3 per Windows per connettere un nuovo servizio mobile a un'app di Windows universale. È possibile eseguire la stessa procedura per connettere un servizio mobile a un'app di Windows Store o Windows Phone Store 8.1. Per connettere un servizio mobile a un'app di Windows Phone 8.0 o Windows Phone Silverlight 8.1, vedere [Introduzione ai dati per Windows Phone][].

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Download del progetto dell'app di Windows Store][]
2.  [Creazione del servizio mobile][]
3.  [Aggiunta di una tabella dati per l'archiviazione][]
4.  [Aggiornamento dell'app per l'utilizzo di Servizi mobili][]
5.  [Test dell'app in Servizi mobili][]

Per completare l'esercitazione, sono necessari gli elementi seguenti:

-   Un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][].
-   [Visual Studio Express 2013 per Windows][] con aggiornamento 2 o versione successiva

## <a name="download-app"></a>Download del progetto GetStartedWithData

[WACOM.INCLUDE [mobile-services-windows-universal-javascript-download-project][]]

## <a name="create-service"></a>Creazione di un nuovo servizio mobile da Visual Studio

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013][]]

1.  In Esplora soluzioni passare alla sottocartella **services\\mobileService\\scripts**, aprire il file di script service.js e osservare la nuova variabile globale, che presenta un aspetto simile al seguente:

        var todolistClient = new WindowsAzure.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");

    Questo codice consente di accedere al nuovo servizio mobile nell'app tramite una variabile globale. Per creare il client, è necessario specificare l'URI e la chiave dell'applicazione del nuovo servizio mobile. Poiché nel file default.html è stato aggiunto un riferimento a questo script, questa variabile è disponibile per tutti i file di script a cui viene fatto riferimento da questa pagina.

2.  Aprire il file di progetto default.html, individuare il riferimento al nuovo file di script service.js e accertarsi che il percorso a cui fa riferimento sia simile al seguente:

        <script src="/services/mobileServices/scripts/todolist.js">

    Attualmente esiste un bug in Visual Studio che causa la generazione di un nome di cartella errato nel percorso.

3.  Fare clic con il pulsante destro del mouse sul progetto di app di Windows Phone, selezionare **Aggiungi** e fare clic su **Servizio connesso...**, quindi selezionare il servizio mobile appena creato e fare clic su **OK**.

    Lo stesso nuovo file di codice viene aggiunto al progetto di app per Windows Phone Store. Accertarsi di correggere anche il percorso di riferimento aggiunto al file default.html.

## <a name="add-table"></a>Aggiunta di una nuova tabella la servizio mobile

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013][]]

> [WACOM.NOTE]Le nuove tabelle create includono le colonne Id, \_\_createdAt, \_\_updatedAt, e \_\_version. Quando è abilitato lo schema dinamico, in Servizi mobili vengono generate automaticamente nuove colonne basate sull'oggetto JSON nella richiesta di inserimento o di aggiornamento. Per altre informazioni, vedere [Schema dinamico][].

# <a name="update-app"></a>Aggiornamento dell'app per l'utilizzo del servizio mobile

[WACOM.INCLUDE [mobile-services-windows-javascript-update-data-app][]]

## <a name="test-azure-hosted"></a>Test del servizio mobile ospitato in Azure

Ora è possibile testare entrambe le versioni dell'app di Windows universale con il servizio mobile ospitato in Azure.

[WACOM.INCLUDE [mobile-services-windows-universal-test-app][]]

1.  Nel [portale di gestione][] fare clic su **Mobile Services** e quindi sul servizio mobile.

    <p>
2.  Fare clic sulla scheda **Data** e quindi su **Browse**.

    Si noti che la tabella **TodoItem** ora contiene dati, con valori ID generati da Servizi mobili, e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.

![][]

L'esercitazione **Introduzione ai dati** è terminata.

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione vengono illustrate le nozioni di base per consentire a un progetto di app di Windows universale di utilizzare dati in Servizi mobili. In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

-   [Utilizzo di script per la convalida e la modifica di dati][]
    Ulteriori informazioni sull'utilizzo di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

-   [Utilizzo del paging per ridefinire le query][]
    Ulteriori informazioni su come utilizzare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, provare a eseguire una delle esercitazioni seguenti:

-   [Introduzione all'autenticazione][]
    Informazioni sull'autenticazione degli utenti dell'app.

-   [Introduzione alle notifiche push][]
    Informazioni sull'invio di una notifica push di base all'app.

-   [Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript][]
    Ulteriori informazioni su come utilizzare Servizi mobili con HTML e JavaScript.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [Creare app Windows universali per Windows e Windows Phone]: http://msdn.microsoft.com/en-us/library/windows/apps/xaml/dn609832.aspx
  [Introduzione ai dati per Windows Phone]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
  [Download del progetto dell'app di Windows Store]: #download-app
  [Creazione del servizio mobile]: #create-service
  [Aggiunta di una tabella dati per l'archiviazione]: #add-table
  [Aggiornamento dell'app per l'utilizzo di Servizi mobili]: #update-app
  [Test dell'app in Servizi mobili]: #test-app
  [versione di valutazione gratuita di Azure]: http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-javascript-get-started-data%2F
  [Visual Studio Express 2013 per Windows]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [mobile-services-windows-universal-javascript-download-project]: ../includes/mobile-services-windows-universal-dotnet-download-project.md
  [mobile-services-create-new-service-vs2013]: ../includes/mobile-services-create-new-service-vs2013.md
  [mobile-services-create-new-table-vs2013]: ../includes/mobile-services-create-new-table-vs2013.md
  [Schema dinamico]: http://msdn.microsoft.com/en-us/library/windowsazure/jj193175.aspx
  [mobile-services-windows-javascript-update-data-app]: ../includes/mobile-services-windows-javascript-update-data-app.md
  [mobile-services-windows-universal-test-app]: ../includes/mobile-services-windows-universal-test-app.md
  [portale di gestione]: https://manage.windowsazure.com/
  []: ./media/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data/mobile-todoitem-data-browse.png
  [Utilizzo di script per la convalida e la modifica di dati]: /it-it/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
  [Utilizzo del paging per ridefinire le query]: /it-it/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
  [Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
  [Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/
  [Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]: /it-it/documentation/articles/mobile-services-html-how-to-use-client-library/
