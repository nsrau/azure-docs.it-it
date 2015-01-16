<properties pageTitle="Introduzione a Servizi mobili di Azure per le app per Android" metaKeywords="Azure android application, mobile service android, getting started Azure android, azure droid, getting started droid windows" description="Seguire questa esercitazione per iniziare a usare Servizi mobili di Azure per lo sviluppo per Android." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="ricksal,glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="hero-article" ms.date="10/16/2014" ms.author="ricksal,glenga" />

# <a name="getting-started"> </a>Introduzione a Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app per Android con Servizi mobili di Azure. In questa esercitazione si creeranno sia un nuovo servizio mobile che una semplice app <em>To do list</em> che archivia i dati dell'app nel nuovo servizio mobile.</p>
<p>Di seguito è riportata una schermata dell'app completata:</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="label">guarda l'esercitazione</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-get-started-android-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Riproduci video</span></a><span class="time">07:26:00</span></div>
</div>

![][0]

Per completare questa esercitazione, è necessario disporre di [Android Developer Tools][Android SDK] che include l'IDE (Integrated Development Environment) di Eclipse, il plug-in Android Developer Tools (ADT) e l'ultima piattaforma Android. È necessario Android 4.2 o versione successiva. 

Il progetto di guida introduttiva scaricato contiene Mobile Services SDK per Android. Questo progetto richiede Android 4.2 o versione successiva, invece Mobile Services SDK richiede solo Android 2.2 o versione successiva.

<div class="dev-callout"><strong>Nota</strong> <p>Per completare l'esercitazione è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">versione di valutazione gratuita di Azure</a>.</p></div>

>[AZURE.NOTE] Per visualizzare il codice sorgente dell'app completata, fare clic <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStarted/Android" target="_blank">qui</a>.

## <a name="create-new-service"> </a>Creare un nuovo servizio mobile

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <h2>Creare una nuova app per Android</h2>

Dopo aver creato il servizio mobile, è possibile seguire una facile guida introduttiva nel portale di gestione per creare una nuova app o modificare un'app esistente per connettersi al servizio mobile. 

In questa sezione si creerà una nuova app per Android connessa al servizio mobile.

1.  Nel portale di gestione fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.

2. Nella scheda Avvio rapido fare clic su **Android** in **Scegli piattaforma** ed espandere **Crea una nuova app Android**.

   	![][6]

   	Di seguito sono visualizzati i tre semplici passaggi per creare un'app per Android connessa al servizio mobile.

  	![][7]

3. Se necessario, scaricare e installare [Android Developer Tools][Android SDK] nel computer locale o nella macchina virtuale.

4. Fare clic su **Crea tabella TodoItem** per creare una tabella in cui archiviare i dati dell'app.


5. Scaricare ora l'app:
	- La versione più recente dell'app usa Mobile Services Android SDK 2.0, che è possibile scaricare <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures">qui</a>. Fare clic su **Download ZIP** e decomprimere il file. Il progetto è disponibile in GettingStarted nella cartella Android.
	 
	- Con una versione più recente si userà la versione precedente dell'SDK. A questo scopo, in **Scaricare ed eseguire l'applicazione** fare clic su **Download**. Verrà scaricato il progetto per l'applicazione _To do list_ di esempio connessa al servizio mobile. I file di progetto sono compressi. Individuare il relativo percorso ed espanderli nel computer.


## Eseguire l'app per Android

La fase finale di questa esercitazione prevede la compilazione e l'esecuzione della nuova app.

2. In Eclipse fare clic su **File** quindi su **Import**, espandere **Android**, fare clic su **Existing Android Code into Workspace** e quindi su **Next.** 

 	![][14]

3. Fare clic su **Browse**, individuare il percorso dei file espansi del progetto, fare clic su **OK**, e verificare che il progetto TodoActivity sia selezionato, quindi fare clic su **Finish**. 

 	![][15]

	I file di progetto verranno importati nell'area di lavoro corrente.

   	![][8]

4. Se è stata scaricata la versione 2.0 dell'SDK, è necessario aggiornare il codice con l'URL e la chiave del servizio mobile:
	- 	Trovare il metodo **OnCreate** in **TodoActivity.java** e individuare il codice che crea un'istanza del client di Servizi mobili. Il codice è visibile nell'immagine precedente.
	- 	Sostituire "MobileServiceUrl" con l'URL effettivo del servizio mobile.
	- 	Sostituire "AppKey" con la chiave del servizio mobile.
	- 	Per informazioni dettagliate, vedere l'esercitazione <a href="http://azure.microsoft.com/it-it/documentation/articles/mobile-services-android-get-started-data/">Aggiungere Servizi mobili a un'app esistente</a>. 



4. Nel menu **Run** scegliere **Run** per avviare il progetto nell'emulatore di Android.

	<div class="dev-callout"><strong>Nota</strong> <p>Per poter eseguire il progetto nell'emulatore di Android, è necessario definire almeno un Android Virtual Device (AVD). Usare AVD Manager per creare e gestire questi dispositivi.</p></div>

5. Nell'app digitare un testo significativo, ad esempio _Complete the tutorial_ e quindi fare clic su **Add**.

   	![][10]

   	Verrà inviata una richiesta POST al nuovo servizio mobile ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal servizio mobile e i dati vengono visualizzati nell'elenco.

	<div class="dev-callout"><strong>Nota</strong> 
   	<p>È possibile esaminare il codice che accede al servizio mobile per eseguire una query e inserire i dati trovati nel file ToDoActivity.java.</p>
 	</div>

6. Tornare al portale di gestione e fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItems**.

   	![][11]

   	In questo modo sarà possibile visualizzare i dati inseriti nella tabella dall'app.

   	![][12]

## <a name="next-steps"> </a>Passaggi successivi
Dopo aver completato la guida introduttiva, è possibile eseguire altre importanti attività in Servizi mobili: 

* [Introduzione ai dati]
  <br/>Altre informazioni sull'archiviazione e l'esecuzione di query sui dati tramite Servizi mobili.

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

* [Introduzione alle notifiche push] 
  <br/>Informazioni sull'invio di una notifica push di base all'app.

<!-- Anchors. -->
[Introduzione a Servizi mobili]:#getting-started
[Creare un nuovo servizio mobile]:#create-new-service
[Definire l'istanza del servizio mobile]:#define-mobile-service-instance
[Passaggi successivi]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png





[6]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-android-get-started/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-android-get-started-data/
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-android-get-started-users/
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Portale di gestione]: https://manage.windowsazure.com/
