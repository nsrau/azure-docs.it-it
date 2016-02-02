<properties
	pageTitle="Introduzione ai back-end delle app per dispositivi mobili per app HTML/JavaScript | App per dispositivi mobili del servizio app di Azure"
	description="Seguire questa esercitazione per iniziare a usare i back-end dell'app per dispositivi mobili di Azure per lo sviluppo per app Web in HTML5 e JavaScript."
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html5"
	ms.devlang="javascript"
	ms.topic="get-started-article"
	ms.date="11/18/2015"
	ms.author="glenga"/>


#Creare un'app HTML

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)] 
&nbsp;  
<!--- [AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]-->

>[AZURE.IMPORTANT] Questo argomento non è attualmente supportato da App per dispositivi mobili perché la Guida introduttiva per le applicazioni HTML/JavaScript è stata temporaneamente rimossa dal portale di Azure. Si prevede di ripristinarla nel prossimo futuro. Grazie per la pazienza dimostrata.

##Panoramica

Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app Web HTML5/JavaScript. Per altre informazioni, vedere [Informazioni sulle app per dispositivi mobili](app-service-mobile-value-prop.md).

Di seguito è riportata una schermata dell'app completata:

![Screenshot dell'app completata](./media/app-service-mobile-html-get-started/mobile-quickstart-completed-html.png)

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni delle app per dispositivi mobili relative ad app HTML.

##Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non è disponibile un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 app per dispositivi mobili gratuite che potranno essere usate anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Community 2013] o versione successiva.

>[AZURE.NOTE] Se si desidera iniziare a usare il servizio app di Azure prima di creare un account Azure, passare al sito [Prova il servizio app](https://tryappservice.azure.com/?appServiceName=mobile), in cui è possibile creare immediatamente un'app per dispositivi mobili di base temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

##Creare un nuovo back-end dell'app per dispositivi mobili

Per creare un nuovo back-end dell'app per dispositivi mobili, attenersi alla procedura seguente.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

È stato eseguito il provisioning di un back-end dell'app per dispositivi mobili di Azure che può essere usato dalle applicazioni client per dispositivi mobili. Successivamente, scaricare un progetto server per un semplice back-end "todo list" e pubblicarlo in Azure.

## Scaricare il progetto server

1. Nel [portale di Azure] fare clic su **Esplora tutto** > **App Web**, quindi fare clic sul back-end dell'app per dispositivi mobili appena creato. 

2. Nel back-end dell'app per dispositivi mobili fare clic su **Tutte le impostazioni** e in **App per dispositivi mobili** fare clic su **Avvio rapido** > **HTML/JavaScript**.

3. In **Scarica ed esegui il progetto server** in **Crea una nuova app** fare clic su **Scarica**, estrarre i file di progetto compressi nel computer locale, quindi aprire la soluzione in Visual Studio.

4. Compilare il progetto per ripristinare i pacchetti NuGet.

##Abilitare CORS nel progetto server

La Condivisione di risorse tra le origini (CORS, Cross-Origin Resource Sharing) permette alle app basate sul Web di indicare i domini le cui richieste sono sicure e devono essere autorizzate dal browser. È necessario aggiungere una voce relativa a CORS per ogni sito Web che accederà al back-end dell'app per dispositivi mobili. Per controllare le impostazioni della Condivisione di risorse tra le origini, usare i comportamenti standard delle API Web di ASP.NET. Per altre informazioni, vedere [Abilitare la condivisione di richieste tra le origini nelle API Web di ASP.NET](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api#enable-cors).

Per impostazione predefinita, il progetto relativo alla guida introduttiva del client che verrà scaricato dal portale è in esecuzione su localhost sulla porta 8000. Sarà quindi necessario abilitare CORS per `http://localhost:8000` nel progetto server.

1. In Visual Studio scegliere **Gestione pacchetti NuGet** > **Console di gestione pacchetti** dal menu Strumenti, selezionare Nuget.org come **Origine pacchetto** ed eseguire il comando seguente nella finestra della console:
 
		Install-Package Microsoft.AspNet.WebApi.Cors  

2. Aprire il file di progetto App\_Start/Startup.MobileApp.cs e aggiungere l'istruzione using seguente:

		using System.Web.Http.Cors;

3. Aggiungere quindi il codice seguente al metodo **Startup.ConfigureMobileApp** dopo la creazione di **HttpConfiguration** (*config*):

        // Enable CORS support for localhost port 8000, all headers and methods.
        var cors = new EnableCorsAttribute("http://localhost:8000", "*", "*");
        config.EnableCors(cors);

4. Salvare gli aggiornamenti.

Sarà quindi necessario distribuire il progetto abilitato per CORS in Azure.

##Pubblicare il progetto server in Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

##Scaricare ed eseguire il progetto client

1. Nel pannello del back-end dell'app per dispositivi mobili fare clic su **Tutte le impostazioni** e in **App per dispositivi mobili** fare clic su **Avvio rapido** > **HTML/JavaScript**. 

2.  In **Scarica ed esegui il progetto HTML/Javascript** in **Crea una nuova app** fare clic su **Scarica** e salvare i file compressi del progetto nel computer locale.

3. Individuare il percorso in cui sono stati salvati i file compressi del progetto ed espanderli nel computer in uso, quindi avviare uno dei file di comando seguenti dalla sottocartella **server**.

	+ **launch-windows** (computer Windows)
	+ **launch-mac.command** (computer Mac OS X)
	+ **launch-linux.sh** (computer Linux)

	> [AZURE.NOTE] In un computer Windows digitare `R` quando PowerShell chiede di confermare l'esecuzione dello script. Il Web browser potrebbe visualizzare un avviso in cui si consiglia di non eseguire lo script in quanto scaricato da Internet. In questo caso, è necessario richiedere che il browser proceda nel caricamento dello script.

	Verrà quindi avviato un server Web nel computer locale per ospitare la nuova app.

4. Aprire l'URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> in un Web browser per avviare l'app. L'app client viene configurata per la connessione al back-end dell'app per dispositivi mobili in Azure.

5. Nell'app digitare un testo significativo, ad esempio _Complete the tutorial_, in **Enter new task** e quindi fare clic su **Add**.

   	![Esecuzione dell'app](./media/app-service-mobile-html-get-started/mobile-quickstart-startup-html.png)

   	Verrà inviata una richiesta POST al nuovo back-end dell'app per dispositivi mobili ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem nello schema dell'app per dispositivi mobili. Gli elementi archiviati nella tabella vengono restituiti dal servizio e i dati vengono visualizzati nella seconda colonna dell'app.

	> [AZURE.TIP] È possibile esaminare il codice che accede al servizio mobile per eseguire una query e inserire i dati, disponibile nel file app.js.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Get started with authentication]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[portale di Azure]: https://portal.azure.com/

[Visual Studio Community 2013]: https://www.visualstudio.com/downloads
 

<!----HONumber=AcomDC_0128_2016-->