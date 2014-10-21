<properties linkid="mobile-services-dotnet-backend-windows-store-dotnet-get-started-data" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"></tags>

# Introduzione ai dati in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-data][]]

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure come origine dati di back-end per un'app di Windows Store. In questa esercitazione si scaricherà un progetto di Visual Studio 2013 per un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si visualizzeranno le modifiche apportate ai dati durante l'esecuzione dell'app.

Il servizio mobile che verrà creato in questa esercitazione è un servizio mobile back-end di .NET. Il Back-end .NET consente di utilizzare i linguaggi .NET e Visual Studio per la logica di business sul lato server nel servizio mobile; inoltre è possibile eseguire il servizio mobile ed effettuarne il debugging sul computer locale in uso. Per creare un servizio mobile che consenta di scrivere la logica di business sul lato server in JavaScript, vedere la versione per back-end JavaScript di questo argomento.

> [WACOM.NOTE]In questo argomento viene illustrato come utilizzare gli strumenti in Visual Studio Professional 2013 con aggiornamento 3 per connettere un nuovo servizio mobile a un'app di Windows universale. È possibile eseguire la stessa procedura per connettere un servizio mobile a un'app di Windows Store o Windows Phone Store 8.1. Per connettere un servizio mobile a un'app di Windows Phone 8.0 o Windows Phone Silverlight 8.1, vedere [Introduzione ai dati per Windows Phone][].

> Se non è possibile eseguire l'aggiornamento a Visual Studio Professional 2013 aggiornamento 3 o si preferisce aggiungere manualmente il progetto di servizio mobile a una soluzione app di Windows Store, vedere [questa versione][] dell'argomento.

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Download del progetto dell'app di Windows Store][]
2.  [Creazione di un nuovo servizio mobile da Visual Studio][]
3.  [Test del progetto di servizio mobile in locale][]
4.  [Aggiornamento dell'app per l'utilizzo del servizio mobile][]
5.  [Pubblicazione del servizio mobile in Azure][]
6.  [Test dell'app con il servizio ospitato in Azure][]
7.  [Visualizzazione dei dati archiviati nel database SQL][]

Per completare l'esercitazione, sono necessari gli elementi seguenti:

-   Un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][].
-   [Visual Studio Professional 2013][] (aggiornamento 3 o versione successiva).
    È disponibile una versione di valutazione gratuita.

## <a name="download-app"></a>Download del progetto GetStartedWithData

[WACOM.INCLUDE [mobile-services-windows-universal-dotnet-download-project][]]

## <a name="create-service"></a>Creazione di un nuovo servizio mobile da Visual Studio

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013][]]

1.  In Esplora soluzioni, aprire il file di codice App.xaml.cs nella cartella di progetto GetStartedWithData.Shared e notare il nuovo campo statico aggiunto alla classe **App** al'interno di un blocco di compilazione condizionale di un'app di Windows Store, il cui aspetto ricorda quello illustrato nel seguente esempio:

        public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
                    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
                

    Questo codice consente di accedere al nuovo servizio mobile nell'app tramite un'istanza della [classe MobileServiceClient][]. Per creare il client, è necessario specificare l'URI e la chiave dell'applicazione del nuovo servizio mobile. Questo campo static è disponibile in tutte le pagine dell'app.

2.  Fare clic con il pulsante destro del mouse sul progetto di app di Windows Phone, selezionare **Aggiungi** e fare clic su **Servizio connesso...**, quindi selezionare il servizio mobile appena creato e fare clic su **OK**.

    Il medesimo codice viene aggiunto al file App.xaml.cs condiviso, ma questa volta all'interno di un blocco di compilazione condizionale di un'app di Windows Phone.

A questo punto, l'app di Windows Store e l'app di Windows Phone Store sono entrambe connesse al nuovo sevizio mobile. Il passaggio successivo consiste nel testare il nuovo progetto di servizio mobile.

## <a name="test-the-service-locally"></a>Test del progetto di servizio mobile in locale

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation][]]

## <a name="update-app"></a>Aggiornamento dell'app per l'utilizzo del servizio mobile

In questa sezione l'app per Windows universale verrà aggiornata per l'utilizzo del servizio mobile come servizio back-end per l'applicazione. È necessario apportare modifiche solo al file di progetto MainPage.xaml.cs nella cartella di progetto GetStartedWithData.Shared.

[WACOM.INCLUDE [mobile-services-windows-dotnet-update-data-app][]]

## <a name="publish-mobile-service"></a>Pubblicazione del servizio mobile in Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][]]

## <a name="test-azure-hosted"></a>Test del servizio mobile ospitato in Azure

Ora è possibile testare entrambe le versioni dell'app di Windows universale con il servizio mobile ospitato in Azure.

[WACOM.INCLUDE [mobile-services-windows-universal-test-app][]]

## <a name="view-stored-data"></a>Visualizzazione dei dati archiviati nel database SQL

[WACOM.INCLUDE [mobile-services-dotnet-backend-view-sql-data][]]

L'esercitazione **Introduzione ai dati** è terminata.

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione vengono illustrate le nozioni di base per consentire a un'app di Windows universale di utilizzare dati in Servizi mobili. In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

-   [Utilizzo di script per la convalida e la modifica di dati][]
    Ulteriori informazioni sull'utilizzo di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

-   [Utilizzo del paging per ridefinire le query][]
    Ulteriori informazioni su come utilizzare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, provare a eseguire una delle esercitazioni seguenti:

-   [Introduzione all'autenticazione][]
    Informazioni sull'autenticazione degli utenti dell'app.

-   [Introduzione alle notifiche push][]
    Informazioni sull'invio di una notifica push di base all'app.

-   [Riferimento per i concetti e le procedure di Servizi mobili con .NET][]
    Ulteriori informazioni su come utilizzare Servizi mobili con .NET.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [Introduzione ai dati per Windows Phone]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
  [questa versione]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
  [Download del progetto dell'app di Windows Store]: #download-app
  [Creazione di un nuovo servizio mobile da Visual Studio]: #create-service
  [Test del progetto di servizio mobile in locale]: #test-the-service-locally
  [Aggiornamento dell'app per l'utilizzo del servizio mobile]: #update-app
  [Pubblicazione del servizio mobile in Azure]: #publish-mobile-service
  [Test dell'app con il servizio ospitato in Azure]: #test-azure-hosted
  [Visualizzazione dei dati archiviati nel database SQL]: #view-stored-data
  [versione di valutazione gratuita di Azure]: http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-dotnet-get-started-data%2F
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=391934
  [mobile-services-windows-universal-dotnet-download-project]: ../includes/mobile-services-windows-universal-dotnet-download-project.md
  [mobile-services-dotnet-backend-create-new-service-vs2013]: ../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md
  [classe MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  [mobile-services-dotnet-backend-test-local-service-api-documentation]: ../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md
  [mobile-services-windows-dotnet-update-data-app]: ../includes/mobile-services-windows-dotnet-update-data-app.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [mobile-services-windows-universal-test-app]: ../includes/mobile-services-windows-universal-test-app.md
  [mobile-services-dotnet-backend-view-sql-data]: ../includes/mobile-services-dotnet-backend-view-sql-data.md
  [Utilizzo di script per la convalida e la modifica di dati]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [Utilizzo del paging per ridefinire le query]: /en-us/develop/mobile/tutorials/add-paging-to-data-dotnet
  [Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
  [Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
  [Riferimento per i concetti e le procedure di Servizi mobili con .NET]: /it-it/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
