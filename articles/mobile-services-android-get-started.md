<properties pageTitle="Get Started with Azure Mobile Services for Android apps" metaKeywords="Azure android application, mobile service android, getting started Azure android, azure droid, getting started droid windows" description="Follow this tutorial to get started using Azure Mobile Services for Android development." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Introduzione a Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started][]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>In questa esercitazione viene illustrato come aggiungere un servizio back-end basato su cloud a un'app per Android mediante Servizi mobili di Azure. In questa esercitazione si creeranno sia un nuovo servizio mobile che una semplice app <em>To do list</em> che archivia i dati dell'app nel nuovo servizio mobile.</p>
<p>Di seguito &egrave; riportata una schermata dell'app completata:</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="label">guarda l'esercitazione</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-get-started-android-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Riproduci video</span></a><span class="time">07:26:00</span></div>

</div>

![][]

Per completare questa esercitazione, è necessario disporre di [Android Developer Tools][] che include l'IDE (Integrated Development Environment) di Eclipse, il plug-in Android Developer Tools (ADT) e l'ultima piattaforma Android. È necessario Android 4.2 o versione successiva.

Il progetto di guida introduttiva scaricato contiene l'SDK di Servizi mobili per Android. Questo progetto richiede Android 4.2 o versione successiva, invece l'SDK di Servizi mobili richiede solo Android 2.2 o versione successiva.

<div class="dev-callout"><strong>Nota</strong> <p>Per completare l'esercitazione, &egrave; necessario un account Azure. Se non si dispone di un account, &egrave; possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">versione di valutazione gratuita di Azure</a>.</p></div>

## <a name="create-new-service"> </a>Creazione di un nuovo servizio mobile

[WACOM.INCLUDE [mobile-services-create-new-service][]]

## 

## <span class="short-header">Creazione di una nuova app</span>Creazione di una nuova app per Android

</h2>
Dopo aver creato il servizio mobile, è possibile seguire una facile guida introduttiva nel portale di gestione per creare una nuova app o modificare un'app esistente per connettersi al servizio mobile.

In questa sezione si creerà una nuova app per Android connessa al servizio mobile.

1.  Nel portale di gestione fare clic su **Mobile Services** e quindi sul servizio mobile appena creato.

2.  Nella scheda Quickstart fare clic su **Android** in **Choose platform** ed espandere **Create a new Android app**.

    ![][1]

    Di seguito sono visualizzati i tre semplici passaggi per creare un'app per Android connessa al servizio mobile.

    ![][2]

3.  Se necessario, scaricare e installare [Android Developer Tools][] nel computer locale o nella macchina virtuale.

4.  Fare clic su **Create TodoItem table** per creare una tabella in cui archiviare i dati dell'app.

5.  In **Download and run your app** fare clic su **Download**.

    Verrà scaricato il progetto per l'applicazione *To do list* di esempio connessa al servizio mobile. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

## Esecuzione dell'app per Android

La fase finale di questa esercitazione prevede la compilazione e l'esecuzione della nuova app.

1.  Individuare il percorso in cui sono stati salvati i file compressi del progetto ed espanderli sul computer in uso.

2.  In Eclipse fare clic su **File**, quindi su **Import**, espandere **Android**, fare clic su **Existing Android Code into Workspace** e quindi su **Next**.

    ![][3]

3.  Fare clic su **Browse**, individuare il percorso dei file espansi del progetto, fare clic su **OK**, verificare che il progetto TodoActivity sia selezionato, quindi fare clic su **Finish**.

    ![][4]

    I file del progetto verranno importati nell'area di lavoro corrente.

    ![][5]

4.  Scegliere **Run** dal menu **Run** per avviare il progetto nell'emulatore di Android.

    <div class="dev-callout"><strong>Nota</strong> <p>Per poter eseguire il progetto nell'emulatore di Android, &egrave; necessario definire almeno un Android Virtual Device (AVD). Utilizzare AVD Manager per creare e gestire questi dispositivi.</p></div>

5.  Nell'app digitare un testo significativo, ad esempio *Complete the tutorial* e quindi fare clic su **Add**.

    ![][6]

    Verrà inviata una richiesta POST al nuovo servizio mobile ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal servizio mobile e i dati vengono visualizzati nell'elenco.

    <div class="dev-callout"><strong>Nota</strong> 
<p>&Egrave; possibile esaminare il codice che accede al servizio mobile per eseguire una query e inserire i dati trovati nel file ToDoActivity.java.</p>
</div>

6.  Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItems**.

    ![][7]

    In questo modo sarà possibile visualizzare i dati inseriti nella tabella dall'app.

    ![][8]

## <a name="next-steps"> </a>Passaggi successivi

Dopo aver completato la guida introduttiva, è possibile eseguire altre importanti attività in Servizi mobili:

-   [Introduzione ai dati][]
    Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Introduzione all'autenticazione][]
    Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

-   [Introduzione alle notifiche push][]
    Informazioni sull'invio di una notifica push di base all'app.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [guarda l'esercitazione]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services
  []: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png
  [Android Developer Tools]: https://go.microsoft.com/fwLink/p/?LinkID=280125
  [versione di valutazione gratuita di Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=AE564AB28
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
  [2]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android.png
  [3]: ./media/mobile-services-android-get-started/mobile-services-import-android-workspace.png
  [4]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png
  [5]: ./media/mobile-services-android-get-started/mobile-eclipse-quickstart.png
  [6]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
  [7]: ./media/mobile-services-android-get-started/mobile-data-tab.png
  [8]: ./media/mobile-services-android-get-started/mobile-data-browse.png
  [Introduzione ai dati]: /it-it/documentation/articles/mobile-services-android-get-started-data/
  [Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-android-get-started-users/
  [Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
