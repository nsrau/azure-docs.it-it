<properties
	pageTitle="Introduzione a Servizi mobili per app per Xamarin iOS | Microsoft Azure"
	description="Seguire questa esercitazione per iniziare a usare Servizi mobili di Azure per lo sviluppo per Xamarin iOS."
	services="mobile-services"
	documentationCenter="xamarin"
	authors="lindydonna"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="03/16/2016"
	ms.author="donnam"/>

# <a name="getting-started"> </a>Introduzione a Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Per l'equivalente di App per dispositivi mobili di questo argomento, vedere [Creare un'app per Xamarin.iOS](../app-service-mobile/app-service-mobile-xamarin-ios-get-started.md).

Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app per Xamarin iOS mediante Servizi mobili di Azure. In questa esercitazione si creeranno sia un nuovo servizio mobile che una semplice app _To do list_ che archivia i dati dell'app nel nuovo servizio mobile. Il servizio mobile che verrà creato usa i linguaggi .NET supportati tramite Visual Studio per la logica di business sul lato server e per la gestione del servizio mobile. Per creare un servizio mobile che consenta di scrivere la logica di business sul lato server in JavaScript, vedere la [versione per back-end JavaScript] di questo argomento.

>[AZURE.NOTE]Questo argomento descrive come creare un nuovo progetto di servizio mobile usando il portale di Azure classico. Visual Studio 2013 Update 2 consente di aggiungere un nuovo progetto di servizio mobile a una soluzione Visual Studio esistente. Per altre informazioni, vedere [Guida rapida: Aggiungere un servizio mobile (back-end .NET)](http://msdn.microsoft.com/library/windows/apps/dn629482.aspx)

Di seguito è riportata una schermata dell'app completata:

![][0]


Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni di Servizi mobili per app per Xamarin iOS.

>[AZURE.NOTE]Per completare l'esercitazione, è necessario un account Azure. Se non si ha un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 servizi mobili gratuiti che potranno essere usati anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-IT%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-ios-get-started" target="_blank">pagina relativa alla versione di valutazione gratuita di Azure</a>.<br />Per completare questa esercitazione, è necessario disporre di <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. È disponibile una versione di valutazione gratuita.

## Creare un nuovo servizio mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Creare una nuova app per Xamarin iOS

Dopo aver creato il servizio mobile, è possibile seguire una facile guida introduttiva nel portale di Azure classico per creare una nuova app o modificare un'app esistente per connettersi al servizio mobile.

In questa sezione si scaricherà una nuova app per Xamarin iOS e un progetto di servizio per il servizio mobile.

1. Se non è già installato, installare Visual Studio con Xamarin. Le istruzioni sono disponibili in [Configurazione e installazione per Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx). È anche possibile usare Xamarin Studio in un computer Mac OS X. Vedere [Configurazione, installazione e verifiche per gli utenti Mac](https://msdn.microsoft.com/library/mt488770.aspx).

2. Nel [portale di Azure classico] fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.

3. Nella scheda Quickstart fare clic su **Xamarin** in **Scegli piattaforma **ed espandere **Crea una nuova app Xamarin**.

   	![][6]

   	Di seguito sono visualizzati i tre semplici passaggi per creare un'app per Xamarin iOS connessa al servizio mobile.

  	![][7]

4. In **Scaricare e pubblicare il servizio nel cloud** selezionare **iOS** e fare clic su **Download**.

  	Verrà scaricata una soluzione che contiene progetti sia per il servizio mobile, sia per l'applicazione _To do list_ di esempio connessa al servizio mobile. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

5. Scaricare il proprio profilo di pubblicazione, salvare il file scaricato nel computer locale e prendere nota del percorso.

## Testare il servizio mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Pubblicare il servizio mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## Eseguire l'app per Xamarin iOS

La fase finale di questa esercitazione prevede la compilazione e l'esecuzione della nuova app.

1. Passare al progetto client nella soluzione del servizio mobile, in Visual Studio o in Xamarin Studio.

	![][8]

	![][9]

2. Fare clic su **Esegui** per compilare il progetto e avviare l'app nell'emulatore iPhone.

3. Nell'app digitare un testo significativo, ad esempio _Complete the tutorial_, quindi fare clic sull'icona con il segno PIÙ (**+**).

	![][10]

	Verrà inviata una richiesta POST al nuovo servizio mobile ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal servizio mobile e i dati vengono visualizzati nell'elenco.

>[AZURE.NOTE]È possibile esaminare il codice che accede al servizio mobile per eseguire una query e inserire i dati nel file QSTodoService.cs C#.


## Passaggi successivi
Dopo aver completato la guida introduttiva, è possibile eseguire altre importanti attività in Servizi mobili:

* [Introduzione alla sincronizzazione dei dati offline] <br/>Informazioni sull'uso della sincronizzazione dei dati offline per rendere l'app più affidabile e veloce nelle risposte.

* [Introduzione all'autenticazione] <br/>Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

* [Introduzione alle notifiche push] <br/>Informazioni sull'invio di una notifica push di base all'app.

* [Risoluzione dei problemi relativi a un back-end .NET di Servizi mobili] <br/> Informazioni su come diagnosticare e correggere i problemi che possono verificarsi con un back-end .NET di Servizi mobili.

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Introduzione alla sincronizzazione dei dati offline]: mobile-services-xamarin-ios-get-started-offline-data.md
[Introduzione all'autenticazione]: mobile-services-dotnet-backend-xamarin-ios-get-started-users.md
[Introduzione alle notifiche push]: mobile-services-dotnet-backend-xamarin-ios-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript and HTML]: mobile-services-win8-javascript/
[portale di Azure classico]: https://manage.windowsazure.com/
[versione per back-end JavaScript]: mobile-services-ios-get-started.md
[Risoluzione dei problemi relativi a un back-end .NET di Servizi mobili]: mobile-services-dotnet-backend-how-to-troubleshoot.md

<!---HONumber=AcomDC_0413_2016-->