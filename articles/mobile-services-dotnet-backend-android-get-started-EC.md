<properties 
	pageTitle="Introduzione a Servizi mobili di Azure per le app per Android" 
	description="Seguire questa esercitazione per iniziare a usare Servizi mobili di Azure per lo sviluppo per Android." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="02/13/2015" 
	ms.author="ricksal"/>

# <a name="getting-started"> </a>Introduzione a Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started-EC.md)]

Questa esercitazione illustra come aggiungere un servizio back-end basato su cloud a un'app per Android mediante Servizi mobili di Azure. In questa esercitazione si creeranno un nuovo servizio mobile e una semplice app _To do list_ che archivia i dati dell'app nel nuovo servizio mobile. Il servizio mobile che verrà creato usa i linguaggi .NET supportati tramite Visual Studio per la logica di business sul lato server e per la gestione del servizio mobile. Per creare un servizio mobile che consenta di scrivere la logica di business sul lato server in JavaScript, vedere la [versione back-end di JavaScript](/it-it/documentation/articles/mobile-services-android-get-started-EC/) di questo argomento.

Di seguito è riportata una schermata dell'app completata:

![][0]

Per completare questa esercitazione, è necessario disporre di [Android Developer Tools][Android SDK] che include l'IDE (Integrated Development Environment) di Eclipse, il plug-in Android Developer Tools (ADT) e l'ultima piattaforma Android. È necessario Android 4.2 o versione successiva. 

Il progetto di guida introduttiva scaricato contiene l'SDK di Servizi mobili per Android. Questo progetto richiede Android 4.2 o versione successiva, invece l'SDK di Servizi mobili richiede solo Android 2.2 o versione successiva.

> [AZURE.IMPORTANT] Per completare l'esercitazione è necessario un account Azure. Se non si ha un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 servizi mobili gratuiti che potranno essere usati anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28"%20target="_blank).

## <a name="create-new-service"> </a>Creare un nuovo servizio mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Scaricare il servizio mobile nel computer locale

Dopo aver creato il servizio mobile scaricare il progetto di servizio mobile personalizzato da eseguire sul computer locale o sulla macchina virtuale.

1. Fare clic sul servizio mobile appena creato, quindi nella scheda Avvio rapido fare clic su **Android** in **Scegli piattaforma** ed espandere **Crea una nuova app Android**.

	![][1]  

2. Se necessario, scaricare e installare [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) o versione successiva.

3. Fare clic su **Scarica** in **Scaricare e pubblicare il servizio nel cloud**.

	Verrà scaricato il progetto di Visual Studio che implementerà il servizio mobile. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

4. Inoltre, scaricare il proprio profilo di pubblicazione, salvare il file scaricato nel computer locale e prendere nota del percorso.

## Testare il servizio mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Pubblicare il servizio mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Creare una nuova app per Android

In questa sezione si creerà una nuova app per Android connessa al servizio mobile.

1. Nel [portale di gestione] fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.

2. Nella scheda Avvio rapido fare clic su **Android** in **Scegli piattaforma** ed espandere **Crea una nuova app Android**. 
 
	![][2]  

3. Se necessario, scaricare e installare [Android Developer Tools][Android SDK] nel computer locale o nella macchina virtuale.

4. In **Scaricare ed eseguire l'applicazione** fare clic su **Download**. 

  	Verrà scaricato il progetto per l'applicazione _To do list_ di esempio connessa al servizio mobile. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

## Eseguire l'app per Android

La fase finale di questa esercitazione prevede la compilazione e l'esecuzione della nuova app.

1. Individuare il percorso in cui sono stati salvati i file compressi del progetto ed espanderli sul computer in uso.

2. In Eclipse fare clic su **File** quindi su **Import**, espandere **Android**, fare clic su **Existing Android Code into Workspace** e quindi su **Next**. 

 	![][14]

3. Fare clic su **Browse**, passare al percorso dei file di progetto espansi, fare clic su **OK**, assicurarsi che il progetto TodoActivity sia selezionato e quindi fare clic su **Finish**. 

 	![][15]

	I file del progetto verranno importati nell'area di lavoro corrente.

   	![][8]

4. Dal menu **Run** scegliere **Run** per avviare il progetto nell'emulatore di Android.

	> [AZURE.IMPORTANT] Per eseguire il progetto nell'emulatore di Android, è necessario definire almeno un dispositivo AVD (Android Virtual Device). Usare AVD Manager per creare e gestire questi dispositivi.

5. Nell'app digitare un testo significativo, ad esempio _Complete the tutorial_ e quindi fare clic su **Add**.

   	![][10]

   	Verrà inviata una richiesta POST al nuovo servizio mobile ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal servizio mobile e i dati vengono visualizzati nell'elenco.

	> [AZURE.NOTE] È possibile esaminare il codice che accede al servizio mobile per eseguire una query e inserire i dati trovati nel file ToDoActivity.java.

<!--This shows how to run your new client app against the mobile service running in Azure. Before you can test the Android app with the mobile service running on a local computer, you must configure the Web server and firewall to allow access from your Android development computer. For more information, see [Configure the local web server to allow connections to a local mobile service](/it-it/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).-->

## <a name="next-steps"> </a>Passaggi successivi
Dopo aver completato la guida introduttiva, è possibile eseguire altre importanti attività in Servizi mobili: 

* [Introduzione ai dati]
  <br/>Informazioni su archiviazione ed esecuzione di query sui dati tramite Servizi mobili.

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

* [Introduzione alle notifiche push] 
  <br/>Informazioni sull'invio di una notifica push di base all'app.

* [Risolvere i problemi relativi a un back-end .NET di Servizi mobili]
  <br/> Informazioni su come diagnosticare e correggere i problemi che possono verificarsi con un back-end .NET di Servizi mobili. 

<!-- Anchors. -->
[Introduzione a Servizi mobili]:#getting-started
[Creare un nuovo servizio mobile]:#create-new-service
[Definire l'istanza del servizio mobile]:#define-mobile-service-instance
[Passaggi successivi]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png
[1]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-vs-EC.png
[2]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android-EC.png


[6]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-push
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Risolvere i problemi relativi a un back-end .NET di Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/

[Portale di gestione]: https://manage.windowsazure.com/

<!--HONumber=45--> 
