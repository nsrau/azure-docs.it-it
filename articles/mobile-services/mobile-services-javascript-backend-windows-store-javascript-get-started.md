<properties
	pageTitle="Introduzione a Servizi mobili per app JavaScript di Windows Store - Servizi mobili di Azure"
	description="Seguire questa esercitazione per iniziare a usare Servizi mobili di Azure per lo sviluppo di Windows Store in JavaScript."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="javascript"
	ms.topic="get-started-article"
	ms.date="02/10/2016"
	ms.author="glenga"/>

# Introduzione a Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

In questa esercitazione viene illustrato come aggiungere un servizio back-end basato su cloud a un'app JavaScript di Windows Store mediante Servizi mobili di Azure. In questa esercitazione si creeranno sia un nuovo servizio mobile che una semplice app *To do list* che archivia i dati dell'app nel nuovo servizio mobile. Per il servizio mobile creato verrà utilizzato JavaScript per la logica di business sul lato server.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
* [Visual Studio 2013 Express per Windows]

## Creare un nuovo servizio mobile

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Creazione di una nuova app di Windows Store

Dopo avere creato il servizio mobile, è possibile seguire una facile guida introduttiva nel portale di Azure classico per creare una nuova app JavaScript di Windows Store 8.1 che permetta la connessione al servizio mobile.

1.  Nel [portale di Azure classico] fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.


2. Nella scheda Quickstart fare clic su **Windows** in **Choose platform** ed espandere **Create a new Windows Store app**.

3. Se necessario, scaricare e installare [Visual Studio 2013][Visual Studio 2013 Express for Windows] nel computer locale o nella macchina virtuale.

4. Fare clic su **Create TodoItem table** per creare una tabella in cui archiviare i dati dell'app.

5. In **Download and run your app** selezionare un linguaggio per l'app, quindi fare clic su **Download**.

  	Verrà scaricato il progetto per l'applicazione *To do list* di esempio connessa al servizio mobile. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

## Esecuzione dell'app per Windows

La fase finale di questa esercitazione prevede la compilazione e l'esecuzione della nuova app.

1. Individuare il percorso in cui sono stati salvati i file compressi del progetto ed espanderli sul computer in uso, quindi aprire il file di soluzione in Visual Studio.

2. Premere **F5** per ricompilare il progetto e avviare l'app.

3. Nell'app digitare un testo significativo, ad esempio *Complete the tutorial*, in **Insert a TodoItem**, quindi fare clic su **Save**.

   	Verrà inviata una richiesta POST al nuovo servizio mobile ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal servizio mobile e i dati vengono visualizzati nella seconda colonna dell'app.

4. (Facoltativo) Eseguire nuovamente l'app e notare se i dati salvati nel passaggio precedente vengano caricati dal servizio mobile dopo l'avvio dell'applicazione.
 
4. Tornare al [portale di Azure classico], fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItem**.

   	In questo modo sarà possibile visualizzare i dati inseriti nella tabella dall'app.

>[AZURE.NOTE] È possibile esaminare il codice che accede al servizio mobile per eseguire una query e inserire i dati, disponibile nel file default.js.

## Passaggi successivi
Ora che è stata completata la Guida introduttiva, è possibile acquisire familiarità con il [client di Servizi Mobili per HTML/JavaScript](mobile-services-html-how-to-use-client-library.md).

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Visual Studio 2013 Express for Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Visual Studio 2013 Express per Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[portale di Azure classico]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0211_2016-->