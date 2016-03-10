<properties
	pageTitle="Creare un'app universale di Windows Runtime 8.1 universale in app per dispositivi mobili del servizio app di Azure | Microsoft Azure"
	description="Seguire questa esercitazione per iniziare a usare i back-end dell'app per dispositivi mobili di Azure per lo sviluppo per Windows Store in C#, Visual Basic o JavaScript."
	services="app-service\mobile"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/04/2016"
	ms.author="glenga"/>

#Creare un'app Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##Panoramica

Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app di Windows universale. Per altre informazioni, vedere [Informazioni sulle app per dispositivi mobili](app-service-mobile-value-prop.md).

[AZURE.INCLUDE [app-service-mobile-windows-universal-get-started](../../includes/app-service-mobile-windows-universal-get-started.md)]

##Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non è disponibile un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 app per dispositivi mobili gratuite che potranno essere usate anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Community 2013] o versione successiva.

>[AZURE.NOTE] Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, passare alla pagina [Prova il servizio app](https://tryappservice.azure.com/?appServiceName=mobile). In questa pagina è possibile creare immediatamente un'app per dispositivi mobili iniziale temporanea in App Service. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

##Creare un nuovo back-end dell'app per dispositivi mobili di Azure

Per creare un nuovo back-end dell'app per dispositivi mobili, attenersi alla procedura seguente.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

È stato eseguito il provisioning di un back-end dell'app per dispositivi mobili di Azure che può essere usato dalle applicazioni client per dispositivi mobili. Successivamente, scaricare un progetto server per un semplice back-end "todo list" e pubblicarlo in Azure.

## Configurare il progetto server

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

##Scaricare ed eseguire il progetto client

Dopo aver configurato il back-end dell'app per dispositivi mobili, è possibile creare una nuova app client o modificarne una esistente per connettersi ad Azure. In questa sezione viene scaricato un progetto di modello di app di Windows universale personalizzato per la connessione al back-end dell'app per dispositivi mobili.

1. Nel pannello **Introduzione** del back-end dell'app per dispositivi mobili fare clic su **Crea una nuova app** > **Download**, quindi estrarre i file di progetto compressi nel computer locale.

3. (Facoltativo) Aggiungere il progetto di app universale di Windows alla soluzione con il progetto server. Ciò semplifica il debug e il test dell'app e del back-end nella stessa soluzione di Visual Studio, se si sceglie di eseguire questa operazione.

4. Con l'app di Windows Store come progetto di avvio, premere il tasto F5 per ricompilare il progetto e avviare l'app di Windows Store.

5. Nell'app digitare un testo significativo, ad esempio *Complete the tutorial* nella casella di testo **Insert a TodoItem**, quindi fare clic su **Save**.

	![](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

	Verrà inviata una richiesta POST al nuovo back-end dell'app per dispositivi mobili ospitato in Azure.

6. Interrompere il debug, fare clic con il pulsante destro del mouse sul progetto `<your app name>.WindowsPhone`, scegliere **Imposta come progetto di avvio** e quindi premere di nuovo F5.

	![](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-wp8.png)

	Tenere presente che i dati salvati dal passaggio precedente vengono caricati dall'app per dispositivi mobili dopo l'avvio dell'app Windows.

##Passaggi successivi

* [Aggiungere l'autenticazione all'app](app-service-mobile-windows-store-dotnet-get-started-users.md) <br/>Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

* [Aggiungere notifiche push all'app](app-service-mobile-windows-store-dotnet-get-started-push.md) <br/>Informazioni sull'invio di una notifica push di base all'app.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_0211_2016---->