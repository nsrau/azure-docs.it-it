<properties urlDisplayName="Get Started with Mobile Services for Xamarin Android apps" pageTitle="Introduzione a Servizi mobili per app per Xamarin Android - Servizi mobili di Azure" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Xamarin Android development" metaCanonical="" services="" documentationCenter="Mobile" title="Get Started with Mobile Services for Xamarin Android apps" authors="donnam" solutions="" manager="dwrede" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="11/11/2014" ms.author="donnam" />

# <a name="getting-started"> </a>Introduzione a Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app per Xamarin Android con Servizi mobili di Azure. In questa esercitazione si creeranno sia un nuovo servizio mobile che una semplice app _To do list_ che archivia i dati dell'app nel nuovo servizio mobile. Il servizio mobile che verrà creato usa i linguaggi .NET supportati tramite Visual Studio per la logica di business sul lato server e per la gestione del servizio mobile. Per creare un servizio mobile che consenta di scrivere la logica di business sul lato server in JavaScript, vedere la [versione per back-end JavaScript] di questo argomento.

>[WACOM.NOTE]Questo argomento illustra come creare un nuovo progetto di servizio mobile usando il portale di gestione di Azure. Visual Studio 2013 Update 2 consente di aggiungere un nuovo progetto di servizio mobile a una soluzione Visual Studio esistente. Per altre informazioni, vedere [Guida introduttiva: aggiunta di un servizio mobile (back-end .NET)](http://msdn.microsoft.com/it-it/library/windows/apps/dn629482.aspx)

Di seguito è riportata una schermata dell'app completata:

![][0]

Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni di Servizi mobili per app per Xamarin Android. 

>[WACOM.NOTE]Per completare l'esercitazione, è necessario un account Azure. Se non si ha un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 servizi mobili gratuiti che potranno essere usati anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-android-get-started" target="_blank">versione di valutazione gratuita di Azure</a>.<br />Per completare questa esercitazione, è necessario disporre di <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. È disponibile una versione di valutazione gratuita.

## Creare un nuovo servizio mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Creare una nuova app per Xamarin Android

Dopo aver creato il servizio mobile, è possibile seguire una facile guida introduttiva nel portale di gestione per creare una nuova app o modificare un'app esistente per connettersi al servizio mobile. 

In questa sezione si scaricherà una nuova app per Xamarin Android e un progetto di servizio per il servizio mobile.

1. Nel portale di gestione fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.
   
2. Nella scheda Guida introduttiva fare clic su **Xamarin** in **Scegli piattaforma** ed espandere **Crea una nuova app Xamarin**.

   	![][6]

   	Di seguito sono visualizzati i tre semplici passaggi per creare un'app per Xamarin Android connessa al servizio mobile.

  	![][7]

3. Se necessario, scaricare e installare <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> nel computer locale o nella macchina virtuale.  

4. Se necessario, scaricare e installare [Xamarin Studio] o Xamarin per Visual Studio.

5. In **Scaricare e pubblicare il servizio nel cloud** selezionare **Android** e fare clic su **Download**. 

  	Verrà scaricata una soluzione che contiene progetti sia per il servizio mobile, sia per l'applicazione _To do list_ di esempio connessa al servizio mobile. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

6. Scaricare il proprio profilo di pubblicazione, salvare il file scaricato nel computer locale e prendere nota del percorso.

## Testare il servizio mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Pubblicare il servizio mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Eseguire l'app per Xamarin Android

La fase finale di questa esercitazione prevede la compilazione e l'esecuzione della nuova app.

1. Passare al progetto client nella soluzione del servizio mobile, in Visual Studio o in Xamarin Studio.

	![][8]

	![][9]

2. Premere il pulsante **Esegui** per compilare il progetto e avviare l'app. Verrà chiesto di selezionare un emulatore o un dispositivo USB collegato. 

	<div class="dev-callout">
	<strong>Nota</strong>
	<p>Per poter eseguire il progetto nell'emulatore di Android, è necessario definire almeno un Android Virtual Device (AVD). Usare AVD Manager per creare e gestire questi dispositivi.</p></div>

3. Nell'app digitare un testo significativo, ad esempio _Complete the tutorial_, quindi fare clic sull'icona con il segno PIÙ (**+**).

	![][10]

	Verrà inviata una richiesta POST al nuovo servizio mobile ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal servizio mobile e i dati vengono visualizzati nell'elenco.

	> [WACOM.NOTE] 
   	> È possibile esaminare il codice che accede al servizio mobile per eseguire una query e inserire i dati trovati nel file ToDoActivity.cs in C#.
    
## Passaggi successivi
Dopo aver completato la guida introduttiva, è possibile eseguire altre importanti attività in Servizi mobili: 

* [Introduzione alla sincronizzazione dei dati offline]
  <br/>Informazioni su come usare la sincronizzazione dei dati offline per rendere l'app più affidabile e veloce nelle risposte.

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

* [Introduzione alle notifiche push] 
  <br/>Informazioni sull'invio di una notifica push di base all'app.

* [Risolvere i problemi relativi a un back-end .NET di Servizi mobili]
  <br/> Informazioni su come diagnosticare e correggere i problemi che possono verificarsi con un back-end .NET di Servizi mobili. 

<!-- Anchors. -->
[Introduzione a Servizi mobili]:#getting-started
[Creare un nuovo servizio mobile]:#create-new-service
[Passaggi successivi]:#next-steps



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[7]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Introduzione alla sincronizzazione dei dati offline]: /it-it/documentation/articles/mobile-services-xamarin-android-get-started-offline-data
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript e HTML]: mobile-services-win8-javascript/
[Portale di gestione]: https://manage.windowsazure.com/
[Versione di back-end JavaScript]: /it-it/documentation/articles/partner-xamarin-mobile-services-android-get-started
[Introduzione ai dati in Servizi mobili tramite Visual Studio 2012]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-data-vs2012
[Risolvere i problemi relativi a un back-end .NET di Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/


[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin per Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
