<properties pageTitle="Get Started with Azure Mobile Services for HTML 5 apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for HTML development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Introduzione a Servizi mobili
=============================

[Windows Store](/en-us/documentation/articles/mobile-services-windows-store-get-started "Windows Store") [Windows Phone](/en-us/documentation/articles/mobile-services-windows-phone-get-started "Windows Phone") [iOS](/en-us/documentation/articles/mobile-services-ios-get-started "iOS") [Android](/en-us/documentation/articles/mobile-services-android-get-started "Android") [HTML](/en-us/documentation/articles/mobile-services-html-get-started "HTML") [Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started "Xamarin.iOS") [Xamarin.Android](/en-us/documentation/articles/partner-xamarin-mobile-services-android-get-started "Xamarin.Android") [Sencha](/en-us/documentation/articles/partner-sencha-mobile-services-get-started/ "Sencha") [PhoneGap](/en-us/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/ "PhoneGap")

In questa esercitazione viene illustrato come aggiungere un servizio back-end basato su cloud a un'app HTML mediante Servizi mobili di Azure. In questa esercitazione si creeranno sia un nuovo servizio mobile che una semplice app *To do list* che archivia i dati dell'app nel nuovo servizio mobile. È inoltre possibile visualizzare una versione video dell'esercitazione facendo clic sul fotogramma a destra.

[guarda l'esercitazione](http://go.microsoft.com/fwlink/?LinkId=287040) [Riproduci video](http://go.microsoft.com/fwlink/?LinkId=287040) 3:51

Di seguito è riportata una schermata dell'app completata:

![](./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png)

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Servizi mobili relative ad app HTML.
**Nota**

Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F).

### Requisiti aggiuntivi

-   Per completare questa esercitazione, è necessario che nel computer locale sia in esecuzione uno dei server Web seguenti:

    -   **In Windows**: IIS Express. IIS Express viene installato tramite l'[Installazione guidata piattaforma Web Microsoft](http://go.microsoft.com/fwlink/p/?LinkId=286333).
    -   **In MacOS X**: Python, che dovrebbe essere già installato.
    -   **In Linux**: Python. È necessario installare la [versione più recente di Python](http://go.microsoft.com/fwlink/p/?LinkId=286342).

    È possibile utilizzare qualsiasi server Web per ospitare l'app, tuttavia quelli indicati sono i server Web supportati dagli script scaricati.

-   Un Web browser che supporta HTML5.

Creazione di un nuovo servizio mobile
-------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

Creare una nuova appCreare una nuova app HTML
------------------------------------------------------------------------------

Dopo aver creato il servizio mobile, è possibile seguire una facile guida introduttiva nel portale di gestione per creare una nuova app o modificare un'app esistente per connettersi al servizio mobile.

In questa sezione si creerà una nuova app HTML connessa al servizio mobile.

1.  Nel portale di gestione fare clic su **Mobile Services** e quindi sul servizio mobile appena creato.

2.  Nella scheda Quickstart fare clic su **Windows** in **Choose platform** ed espandere **Create a new HTML app**.

   	![][6]

   	Di seguito sono visualizzati i tre semplici passaggi per creare e ospitare un'app HTML connessa al servizio mobile.

   	![](./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png)

1.  Fare clic su **Create TodoItems table** per creare una tabella in cui archiviare i dati dell'app.

2.  In **Download and run your app** fare clic su **Download**.

   	Verranno scaricati i file del sito Web per l'applicazione *To do list* di esempio connessa al servizio mobile. Salvare il file compresso nel computer locale e prendere nota del percorso.

1.  Nella scheda **Configure** verificare che `localhost` sia presente nell'elenco **Allow requests from host names** in **Cross-Origin Resource Sharing (CORS)**. In caso contrario, digitare `localhost` nel campo **Host name** e quindi fare clic su **Save**.

   	![](./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png)

    <div class="dev-callout"><b>Nota</b>
        <p>Se si distribuisce l'app di guida introduttiva in un server Web diverso da localhost, è necessario aggiungere il nome host del server Web all'elenco <strong>Allow requests from host names</strong>. Per ulteriori informazioni, vedere <a href="http://msdn.microsoft.com/en-us/library/windowsazure/dn155871.aspx" target="_blank">Condivisione risorse tra le origini</a>.</p>
    </div>

Ospitare ed eseguire l'app HTML
-------------------------------

La fase finale di questa esercitazione prevede l'hosting e l'esecuzione della nuova app nel computer locale.

1.  Individuare il percorso in cui sono stati salvati i file compressi del progetto ed espanderli nel computer in uso, quindi avviare uno dei file di comando seguenti dalla sottocartella **server**.

    -   **launch-windows** (computer Windows)
    -   **launch-mac.command** (computer Mac OS X)
    -   **launch-linux.sh** (computer Linux)

	<div class="dev-callout"><b>Nota</b>
		<p>In un computer Windows, digitare 'R' quando PowerShell chiede di confermare che si desidera eseguire lo script. Il Web browser potrebbe visualizzare un avviso in cui si consiglia di non eseguire lo script in quanto scaricato da Internet. In questo caso, è necessario richiedere che il browser proceda nel caricamento dello script.</p>
	</div>

	Verrà quindi avviato un server Web nel computer locale per ospitare la nuova app.

2.  Aprire l'URL <http://localhost:8000/> in un Web browser per avviare l'app.

3.  Nell'app digitare un testo significativo, ad esempio *Complete the tutorial*, in **Enter new task** e quindi fare clic su **Add**.

   	![][10]

   	Verrà inviata una richiesta POST al nuovo servizio mobile ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal servizio mobile e i dati vengono visualizzati nella seconda colonna dell'app.


	<div class="dev-callout"> 
	<b>Nota</b> 
   	<p>È possibile esaminare il codice che accede al servizio mobile per eseguire una query e inserire i dati, disponibile nel file app.js.</p> 
 	</div>

4.  Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItems**.

   	![][11]

   	In questo modo sarà possibile visualizzare i dati inseriti nella tabella dall'app.

   	![][12]

Passaggi successivi
-------------------

Dopo aver completato la guida introduttiva, è possibile eseguire altre importanti attività in Servizi mobili:

-   **[Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-html)**
    <br/>Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   **[Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-html)**
    <br/>Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

-   **[Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript](/en-us/develop/mobile/how-to-guides/work-with-html-js-client)**
    <br/>Ulteriori informazioni su come utilizzare Servizi mobili con HTML/JavaScript



<!-- Images. -->
[0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png





[6]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
[7]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png

[9]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
[10]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
[11]: ./media/mobile-services-html-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-html-get-started/mobile-data-browse.png
