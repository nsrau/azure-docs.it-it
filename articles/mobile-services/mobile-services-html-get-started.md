<properties
	pageTitle="Introduzione a Servizi mobili di Azure per le app HTML/JavaScript | Microsoft Azure"
	description="Seguire questa esercitazione per iniziare a usare Servizi mobili di Azure per lo sviluppo HTML."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html5"
	ms.devlang="javascript"
	ms.topic="get-started-article" 
	ms.date="11/30/2015"
	ms.author="glenga"/>


# <a name="getting-started"> </a>Introduzione a Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

##Panoramica 

In questa esercitazione viene illustrato come aggiungere un servizio back-end basato su cloud a un'app HTML mediante Servizi mobili di Azure. In questa esercitazione si creeranno sia un nuovo servizio mobile che una semplice app *To do list* che archivia i dati dell'app nel nuovo servizio mobile. È possibile visualizzare la versione video seguente di questa esercitazione.

> [AZURE.VIDEO mobile-get-started-html]
 
Di seguito è riportata una schermata dell'app completata:

![][0]

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Servizi mobili relative ad app HTML. Per un'app PhoneGap/Cordova, vedere la [versione PhoneGap/Cordova](mobile-services-javascript-backend-phonegap-get-started.md) di questa esercitazione.

##Prerequisiti

Per completare l'esercitazione è necessario quanto segue:

+ Nel computer locale deve essere in esecuzione uno dei server Web seguenti:

	+  **In Windows**: IIS Express. IIS Express viene installato tramite l'[Installazione guidata piattaforma Web Microsoft].
	+  **In MacOS X**: Python, che dovrebbe essere già installato.
	+  **In Linux**: Python. È necessario installare la [versione più recente di Python].

	È possibile utilizzare qualsiasi server Web per ospitare l'app, tuttavia quelli indicati sono i server Web supportati dagli script scaricati.

+ Un Web browser che supporta HTML5.
+ Un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-IT%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F"%20target="_blank). 


## <a name="create-new-service"> </a>Creare un nuovo servizio mobile

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Crea nuova app HTML

Dopo aver creato il servizio mobile, è possibile seguire una facile guida introduttiva nel portale di Azure classico per creare una nuova app o modificare un'app esistente per connettersi al servizio mobile.

In questa sezione si creerà una nuova app HTML connessa al servizio mobile.

1.  Nel [portale di Azure classico] fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.


2. Nella scheda Quickstart fare clic su **Windows** in **Choose platform** ed espandere **Create a new HTML app**.

   	![][6]

   	Di seguito sono visualizzati i tre semplici passaggi per creare e ospitare un'app HTML connessa al servizio mobile.

  	![][7]

3. Fare clic su **Create TodoItems table** per creare una tabella in cui archiviare i dati dell'app.

4. In **Download and run your app** fare clic su **Download**.

  	Vengono scaricati i file del sito Web per l'applicazione _Elenco azioni_ di esempio connessa al servizio mobile. Salvare il file compresso nel computer locale e prendere nota del percorso.

5. Nella scheda **Configura** verificare che `localhost` sia già indicato nell'elenco **Consentire le richieste da nomi host** in **Condivisione risorse tra le origini (CORS)**. In caso contrario, digitare `localhost` nel campo **Nome host** e quindi fare clic su **Salva**.

  	![][9]

	> [AZURE.IMPORTANT]Se si distribuisce l'app di guida introduttiva in un server Web diverso da localhost, è necessario aggiungere il nome host del server Web all'elenco **Consentire le richieste da nomi host**. Per altre informazioni, vedere [Utilizzare la condivisione di risorse tra origini](http://msdn.microsoft.com/library/windowsazure/dn155871.aspx).

## Ospitare ed eseguire l'app HTML

La fase finale di questa esercitazione prevede l'hosting e l'esecuzione della nuova app nel computer locale.

1. Individuare il percorso in cui sono stati salvati i file compressi del progetto ed espanderli nel computer in uso, quindi avviare uno dei file di comando seguenti dalla sottocartella **server**.

	+ **launch-windows** (computer Windows)
	+ **launch-mac.command** (computer Mac OS X)
	+ **launch-linux.sh** (computer Linux)

	> [AZURE.NOTE]In un computer Windows digitare `R` quando PowerShell chiede di confermare l'esecuzione dello script. Il Web browser potrebbe visualizzare un avviso in cui si consiglia di non eseguire lo script in quanto scaricato da Internet. In questo caso, è necessario richiedere che il browser proceda nel caricamento dello script.

	Verrà quindi avviato un server Web nel computer locale per ospitare la nuova app.

2. Aprire l'URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> in un Web browser per avviare l'app.

3. Nell'app digitare un testo significativo, ad esempio _Complete the tutorial_, in **Enter new task** e quindi fare clic su **Add**.

   	![][10]

   	Verrà inviata una richiesta POST al nuovo servizio mobile ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal servizio mobile e i dati vengono visualizzati nella seconda colonna dell'app.

	> [AZURE.NOTE]È possibile esaminare il codice che accede al servizio mobile per eseguire una query e inserire i dati, disponibile nel file page.js.

4. Tornare al [portale di Azure classico], fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItem**.

   	![][11]

   	In questo modo sarà possibile visualizzare i dati inseriti nella tabella dall'app.

   	![][12]

## <a name="next-steps"> </a>Passaggi successivi
Dopo aver completato la guida introduttiva, è possibile eseguire altre importanti attività in Servizi mobili:

* **[Aggiungere l'autenticazione all'app]** Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

* **[Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]** Altre informazioni su come utilizzare Servizi mobili con HTML/JavaScript


[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png

[6]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
[7]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png

[9]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
[10]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
[11]: ./media/mobile-services-html-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-html-get-started/mobile-data-browse.png


<!-- URLs. -->
[Aggiungere l'autenticazione all'app]: mobile-services-html-get-started-users.md

[portale di Azure classico]: https://manage.windowsazure.com/
[Installazione guidata piattaforma Web Microsoft]: http://go.microsoft.com/fwlink/p/?LinkId=286333
[versione più recente di Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342
[Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]: mobile-services-html-how-to-use-client-library.md
[Cross-origin resource sharing]: http://msdn.microsoft.com/library/azure/dn155871.aspx
 

<!---HONumber=AcomDC_1217_2015-->