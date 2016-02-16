<properties
	pageTitle="Introduzione a Servizi mobili per Xamarin Android | Microsoft Azure"
	writer="craigd"
	description="Informazioni su come usare Servizi mobili di Azure con l'app per Xamarin.Android."
	documentationCenter="xamarin"
	authors="lindydonna"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/10/2016"
	ms.author="donnam"/>

# <a name="getting-started"></a>Introduzione a Servizi mobili

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app per Xamarin.Android con Servizi mobili di Azure. In questa esercitazione si creeranno sia un nuovo servizio mobile che una semplice app *To do list* che archivia i dati dell'app nel nuovo servizio mobile.

Se si preferisce guardare un video, la clip seguente usa gli stessi passaggi dell'esercitazione.

Video "Introduzione a Xamarin e a Servizi mobili di Azure" con Craig Dunn, Developer Evangelist per Xamarin (durata: 10:05 min)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

Di seguito è riportata una schermata dell'app completata:

![][0]

Per completare questa esercitazione, è necessario disporre di [Xamarin.Android], che installa Xamarin Studio e un plug-in di Visual Studio (in Windows), nonché della piattaforma Android più recente. È necessario Android 4.2 SDK o una versione successiva.

Il progetto di Guida introduttiva scaricato contiene il componente Servizi mobili di Azure per Xamarin.Android. Questo progetto richiede Android 4.2 o versioni successive, mentre l'SDK di Servizi mobili richiede solo Android 2.2 o versioni successive.

> [AZURE.IMPORTANT] Per completare l'esercitazione, è necessario un account Azure. Se non si ha un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 servizi mobili gratuiti che potranno essere usati anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5).

## <a name="create-new-service"> </a>Creare un nuovo servizio mobile

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Creare una nuova app per Xamarin.Android

Dopo aver creato il servizio mobile, è possibile seguire una facile guida introduttiva nel portale di Azure classico per creare una nuova app o modificare un'app esistente per connettersi al servizio mobile.

In questa sezione si creerà una nuova app per Xamarin.Android connessa al servizio mobile.

1.  Nel [portale di Azure classico] fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.

2. Nella scheda Avvio rapido fare clic su **Xamarin.Android** in **Scegli piattaforma** ed espandere **Crea una nuova app Android**.

	![][6]

	Di seguito sono visualizzati i tre semplici passaggi per creare un'app di Xamarin.Android connessa al servizio mobile.

	![][7]

3. Fare clic su **Create TodoItem table** per creare una tabella in cui archiviare i dati dell'app.

4. In **Download and run app** fare clic su **Download**.

	Verrà scaricato il progetto per l'applicazione _To do list_ di esempio connessa al servizio mobile. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

## Esecuzione dell'app per Android

La fase finale di questa esercitazione prevede la compilazione e l'esecuzione della nuova app.

1. Individuare il percorso in cui sono stati salvati i file compressi del progetto ed espanderli nel computer in uso.

2. In Xamarin Studio o Visual Studio fare clic su **File**, quindi su **Apri**, passare ai file di esempio non compressi e selezionare **XamarinTodoQuickStart.Android.sln** per aprirlo.

3. Premere il pulsante **Esegui** per compilare il progetto e avviare l'app. Verrà chiesto di selezionare un emulatore o un dispositivo USB collegato.

	> [AZURE.NOTE] Per poter eseguire il progetto nell'emulatore di Android, è necessario definire almeno un dispositivo Android Virtual Device (AVD). Usare AVD Manager per creare e gestire questi dispositivi.

4. Nell'app digitare un testo significativo, ad esempio _Complete the tutorial_ e quindi fare clic su **Add**.

	![][10]

	Verrà inviata una richiesta POST al nuovo servizio mobile ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal servizio mobile e i dati vengono visualizzati nell'elenco.

	> [AZURE.NOTE]
   	È possibile esaminare il codice che accede al servizio mobile per eseguire una query e inserire i dati trovati nel file ToDoActivity.cs in C#.

6. Tornare al [portale di Azure classico], fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItems**.

	![][11]

	In questo modo sarà possibile visualizzare i dati inseriti nella tabella dall'app.

	![][12]

## <a name="next-steps"> </a>Passaggi successivi
Dopo aver completato la guida introduttiva, è possibile eseguire altre importanti attività in Servizi mobili:

* [Introduzione alla sincronizzazione dei dati offline] Informazioni sull'uso della sincronizzazione dei dati offline per rendere l'app più affidabile e veloce nelle risposte.

* [Introduzione all'autenticazione] Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

* [Introduzione alle notifiche push] Informazioni sull'invio di una notifica push di base all'app.



[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-completed-android.png
[2]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create.png
[3]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page1.png
[4]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page2.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started/obile-services-selection.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-portal-quickstart-xamarin-android.png
[7]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-xs.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-vs.png
[10]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-services-diagram.png


<!-- URLs. -->
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-android
[Introduzione alla sincronizzazione dei dati offline]: mobile-services-xamarin-android-get-started-offline-data.md
[Introduzione all'autenticazione]: /develop/mobile/tutorials/get-started-with-users-xamarin-android
[Introduzione alle notifiche push]: /develop/mobile/tutorials/get-started-with-push-xamarin-android
[Xamarin.Android]: http://xamarin.com/download
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Azure]: http://azure.microsoft.com/
[portale di Azure classico]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0211_2016-->