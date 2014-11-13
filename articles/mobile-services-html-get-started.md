<properties pageTitle="Introduzione a Servizi mobili di Azure per le app HTML 5" metaKeywords="" description="Seguire questa esercitazione per iniziare a usare Servizi mobili di Azure per lo sviluppo HTML. " metaCanonical="" services="" documentationCenter="Mobile" title="Introduzione a Servizi mobili" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga" />

# <a name="getting-started"> </a>Introduzione a Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">

<div class="dev-onpage-left-content">

In questa esercitazione viene illustrato come aggiungere un servizio back-end basato su cloud a un'app HTML mediante Servizi mobili di Azure. In questa esercitazione si creeranno sia un nuovo servizio mobile che una semplice app *To do list* che archivia i dati dell'app nel nuovo servizio mobile. È inoltre possibile visualizzare una versione video dell'esercitazione facendo clic sul fotogramma a destra.

</div>

<div class="dev-onpage-video-wrapper">

[video di esercitazione][video di esercitazione] [<span class="icon"> </span>][video di esercitazione] <span class="time">Riproduci video 03:51:00</span>

</div>

</div>

Di seguito è riportata una schermata dell'app completata:

![][0]

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Servizi mobili relative ad app HTML.

<div class="dev-callout">

**Nota**
Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][versione di valutazione gratuita di Azure].

</div>

### Requisiti aggiuntivi

-   Per completare questa esercitazione, è necessario che nel computer locale sia in esecuzione uno dei server Web seguenti:

    -   **In Windows**: IIS Express. IIS Express viene installato tramite l'[Installazione guidata piattaforma Web Microsoft][Installazione guidata piattaforma Web Microsoft].
    -   **In MacOS X**: Python, che dovrebbe essere già installato.
    -   **In Linux**: Python. È necessario installare la [versione più recente di Python][versione più recente di Python].

    È possibile usare qualsiasi server Web per ospitare l'app, tuttavia quelli indicati sono i server Web supportati dagli script scaricati.

-   Un Web browser che supporta HTML5.

## <a name="create-new-service"> </a>Creazione di un nuovo servizio mobile

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## 

## <span class="short-header">Creare una nuova app</span>Creare una nuova app HTML

</h2>
Dopo aver creato il servizio mobile, è possibile seguire una facile guida introduttiva nel portale di gestione per creare una nuova app o modificare un'app esistente per connettersi al servizio mobile.

In questa sezione si creerà una nuova app HTML connessa al servizio mobile.

1.  Nel portale di gestione fare clic su **Mobile Services** e quindi sul servizio mobile appena creato.

2.  Nella scheda Quickstart fare clic su **Windows** in **Choose platform** ed espandere **Create a new HTML app**.

    ![][1]

    Di seguito sono visualizzati i tre semplici passaggi per creare e ospitare un'app HTML connessa al servizio mobile.

    ![][2]

3.  Fare clic su **Create TodoItems table** per creare una tabella in cui archiviare i dati dell'app.

4.  In **Download and run your app** fare clic su **Download**.

    Vengono scaricati i file del sito Web per l'applicazione *Elenco azioni* di esempio connessa al servizio mobile. Salvare il file compresso nel computer locale e prendere nota del percorso.

5.  Nella scheda **Configura**, verificare che `localhost` sia giù indicato nell'elenco **Consentire le richieste da nomi host** in **Condivisione risorse tra le origini (CORS)**. In caso contrario, digitare `localhost` nel campo **Nome host** e quindi fare clic su **Salva**.

    ![][3]

    <div class="dev-callout">

    **Nota**
    Se si distribuisce l'app di guida introduttiva in un server Web diverso da localhost, è necessario aggiungere il nome host del server Web all'elenco **Consentire le richieste da nomi host**. Per altre informazioni, vedere [Condivisione risorse tra le origini][Condivisione risorse tra le origini].

    </div>

## Ospitare ed eseguire l'app HTML

La fase finale di questa esercitazione prevede l'hosting e l'esecuzione della nuova app nel computer locale.

1.  Individuare il percorso in cui sono stati salvati i file compressi del progetto ed espanderli nel computer in uso, quindi avviare uno dei file di comando seguenti dalla sottocartella **server**.

    -   **launch-windows** (computer Windows)
    -   **launch-mac.command** (computer Mac OS X)
    -   **launch-linux.sh** (computer Linux)

    <div class="dev-callout">

    **Nota**
    In un computer Windows, digitare 'R' quando PowerShell chiede di confermare che si desidera eseguire lo script. Il Web browser potrebbe visualizzare un avviso in cui si consiglia di non eseguire lo script in quanto scaricato da Internet. In questo caso, è necessario richiedere che il browser proceda nel caricamento dello script.

    </div>

    Verrà quindi avviato un server Web nel computer locale per ospitare la nuova app.

2.  Aprire l'URL <http://localhost:8000/> in un Web browser per avviare l'app.

3.  Nell'app digitare un testo significativo, ad esempio *Complete the tutorial*, in **Enter new task** e quindi fare clic su **Add**.

    ![][4]

    Verrà inviata una richiesta POST al nuovo servizio mobile ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal servizio mobile e i dati vengono visualizzati nella seconda colonna dell'app.

    <div class="dev-callout">

    **Nota**
    È possibile esaminare il codice che accede al servizio mobile per eseguire una query e inserire i dati, disponibile nel file app.js.

    </div>

4.  Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItems**.

    ![][5]

    In questo modo sarà possibile visualizzare i dati inseriti nella tabella dall'app.

    ![][6]

## <a name="next-steps"> </a>Passaggi successivi

Dopo aver completato la guida introduttiva, è possibile eseguire altre importanti attività in Servizi mobili:

-   **[Introduzione ai dati][Introduzione ai dati]**
    Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   **[Chiamare un'API personalizzata da un'applicazione HTML][Chiamare un'API personalizzata da un'applicazione HTML]**
    Connettere l'applicazione HTML a un'API personalizzata ospitata in Servizi mobili.

-   **[Introduzione all'autenticazione][Introduzione all'autenticazione]**
    Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

-   **[Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript][Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]**
    Ulteriori informazioni su come usare Servizi mobili con HTML/JavaScript

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [video di esercitazione]: http://go.microsoft.com/fwlink/?LinkId=287040
  [0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png
  [versione di valutazione gratuita di Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F
  [Installazione guidata piattaforma Web Microsoft]: http://go.microsoft.com/fwlink/p/?LinkId=286333
  [versione più recente di Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342
  [1]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
  [2]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png
  [3]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
  [Condivisione risorse tra le origini]: http://msdn.microsoft.com/it-it/library/windowsazure/dn155871.aspx
  [4]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
  [5]: ./media/mobile-services-html-get-started/mobile-data-tab.png
  [6]: ./media/mobile-services-html-get-started/mobile-data-browse.png
  [Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-html
  [Chiamare un'API personalizzata da un'applicazione HTML]: /it-it/documentation/articles/mobile-services-html-call-custom-api
  [Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-html
  [Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]: /it-it/develop/mobile/how-to-guides/work-with-html-js-client
