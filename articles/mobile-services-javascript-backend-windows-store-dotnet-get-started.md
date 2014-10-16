<properties pageTitle="Get Started with Mobile Services for Windows Store apps | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C# or JavaScript. " metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="hero-article" ms.date="08/18/2014" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Introduzione a Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started][]]

Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app Windows universale mediante Servizi mobili di Azure. Le soluzioni per app Windows universali includono progetti di app per Windows Store 8.1 e Windows Phone Store 8.1 e un progetto condiviso comune. Per altre informazioni, vedere [Creare app Windows universali per Windows e Windows Phone][].

In questa esercitazione si creeranno sia un nuovo servizio mobile che una semplice app *To do list* che archivia i dati dell'app nel nuovo servizio mobile. Per il servizio mobile creato verrà utilizzato JavaScript per la logica di business sul lato server. Per creare un servizio mobile che consenta di scrivere la logica di business sul lato server nei linguaggi .NET supportati tramite Visual Studio, vedere la versione per backend .NET di questo argomento.

> [WACOM.NOTE]Questo argomento illustra come creare un nuovo progetto di servizio mobile e un'app Windows universale usando il portale di gestione di Azure. Visual Studio 2013 permette di aggiungere un nuovo progetto di servizio mobile a una soluzione Visual Studio esistente. Per altre informazioni, vedere [Guida introduttiva: Aggiungere un servizio mobile (back-end JavaScript)][].

> Per aggiungere un servizio mobile a un progetto app per Windows Phone 8.0 o Windows Phone Store 8.1, vedere [Introduzione ai dati per Windows Phone][].

[WACOM.INCLUDE [mobile-services-windows-universal-get-started][]]

Per completare l'esercitazione, sono necessari gli elementi seguenti:

-   Un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][].
-   [Visual Studio 2013 Express per Windows][]

## Creazione di un nuovo servizio mobile

[WACOM.INCLUDE [mobile-services-create-new-service][]]

## Creare una nuova app Windows universale

Dopo aver creato il servizio mobile, è possibile seguire una facile guida introduttiva nel portale di gestione per creare una nuova app Windows universale o per modificare un progetto app esistente per Windows Store o Windows Phone per connettersi al servizio mobile.

In questa sezione si creerà una nuova app Windows universale connessa al servizio mobile.

1.  Nel portale di gestione fare clic su **Mobile Services** e quindi sul servizio mobile appena creato.

2.  Nella scheda Quickstart fare clic su **Windows** in **Choose platform** ed espandere **Create a new Windows Store app**.

    ![][]

    Di seguito sono visualizzati i tre semplici passaggi per creare un'app di Windows Store connessa al servizio mobile.

    ![][1]

3.  Se necessario, scaricare e installare [Visual Studio 2013 Express per Windows][] nel computer locale o nella macchina virtuale.

4.  Fare clic su **Create TodoItem table** per creare una tabella in cui archiviare i dati dell'app.

5.  In **Download and run your app** selezionare un linguaggio per l'app, quindi fare clic su **Download**.

    Verrà scaricato il progetto per l'applicazione *To do list* di esempio connessa al servizio mobile. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

## Esecuzione dell'app per Windows

[WACOM.INCLUDE [mobile-services-javascript-backend-run-app][]]

> [WACOM.NOTE]È possibile esaminare il codice che accede al servizio mobile per eseguire una query e inserire i dati trovati nel file MainPage.xaml.cs.

## Passaggi successivi

Dopo aver completato la guida introduttiva, è possibile eseguire altre importanti attività in Servizi mobili:

-   [Introduzione ai dati][] Altre informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Introduzione all'autenticazione][] Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

-   [Introduzione alle notifiche push][] Informazioni sull'invio di una notifica push di base all'app.

Per altre informazioni sulle app Windows universali, vedere [Supporto di piattaforme per più dispositivi da un singolo servizio mobile][].

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [Creare app Windows universali per Windows e Windows Phone]: http://msdn.microsoft.com/en-us/library/windows/apps/xaml/dn609832.aspx
  [Guida introduttiva: Aggiungere un servizio mobile (back-end JavaScript)]: http://msdn.microsoft.com/en-us/library/windows/apps/xaml/dn263180.aspx
  [Introduzione ai dati per Windows Phone]: /it-it/documentation/articles/mobile-services-windows-phone-get-started-data
  [mobile-services-windows-universal-get-started]: ../includes/mobile-services-windows-universal-get-started.md
  [versione di valutazione gratuita di Azure]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F
  [Visual Studio 2013 Express per Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  []: ./media/mobile-services-javascript-backend-windows-store-dotnet-get-started/mobile-portal-quickstart.png
  [1]: ./media/mobile-services-javascript-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png
  [mobile-services-javascript-backend-run-app]: ../includes/mobile-services-javascript-backend-run-app.md
  [Introduzione ai dati]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-data
  [Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-users
  [Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push
  [Supporto di piattaforme per più dispositivi da un singolo servizio mobile]: /it-it/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service#shared-vs
