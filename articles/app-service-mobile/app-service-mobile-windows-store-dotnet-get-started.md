<properties
	pageTitle="Creare un'app UWP (Universal Windows Platform) in app per dispositivi mobili del servizio app di Azure | Microsoft Azure"
	description="Seguire questa esercitazione per iniziare a usare i back-end dell'app per dispositivi mobili di Azure per la distribuzione di app UWP (Universal Windows Platform) in C#, Visual Basic o JavaScript."
	services="app-service\mobile"
	documentationCenter="windows"
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="05/03/2016"
	ms.author="glenga"/>

#Creare un'app Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##Panoramica

Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app UWP (Universal Windows Platform). Per altre informazioni, vedere [Informazioni sulle app per dispositivi mobili](app-service-mobile-value-prop.md). Di seguito sono riportate alcune schermate dell'app completata:

![App desktop completata](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png) Esecuzione in un computer desktop.

![App per telefono completata](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png) Esecuzione in un telefono.

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni delle app per dispositivi mobili relative ad app UWP.

##Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non è disponibile un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 app per dispositivi mobili gratuite che potranno essere usate anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Community 2015] o versione successiva.

>[AZURE.NOTE] Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, passare alla pagina [Prova il servizio app](https://tryappservice.azure.com/?appServiceName=mobile). In questa pagina è possibile creare immediatamente un'app per dispositivi mobili iniziale temporanea in App Service. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

##Creare un nuovo back-end dell'app per dispositivi mobili di Azure

Per creare un nuovo back-end dell'app per dispositivi mobili, attenersi alla procedura seguente.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

È stato eseguito il provisioning di un back-end dell'app per dispositivi mobili di Azure che può essere usato dalle applicazioni client per dispositivi mobili. Successivamente, scaricare un progetto server per un semplice back-end "todo list" e pubblicarlo in Azure.

## Configurare il progetto server

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

##Scaricare ed eseguire il progetto client

Dopo aver configurato il back-end dell'app per dispositivi mobili, è possibile creare una nuova app client o modificarne una esistente per connettersi ad Azure. In questa sezione viene scaricato un progetto di modello di app UWP personalizzato per la connessione al back-end dell'app per dispositivi mobili.

1. Nel pannello **Avvio rapido** del back-end dell'app per dispositivi mobili fare clic su **Crea una nuova app** > **Download**, quindi estrarre i file di progetto compressi nel computer locale.

	![Download del progetto di guida introduttiva di Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-app-windows-quickstart.png)

3. (Facoltativo) Aggiungere il progetto di app UWP alla stessa soluzione in cui si trova il progetto server. Ciò semplifica il debug e il test dell'app e del back-end nella stessa soluzione di Visual Studio, se si sceglie di eseguire questa operazione. Per aggiungere un progetto di app UWP alla soluzione, è necessario usare Visual Studio 2015 o versione successiva.

4. Specificare l'app UWP come progetto di avvio, quindi premere il tasto F5 per distribuire ed eseguire l'app.

5. Nell'app digitare un testo significativo, ad esempio *Complete the tutorial* nella casella di testo **Insert a TodoItem**, quindi fare clic su **Save**.

	![Desktop completo di guida introduttiva di Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

	Verrà inviata una richiesta POST al nuovo back-end dell'app per dispositivi mobili ospitato in Azure.

6. (Facoltativo) Arrestare l'app e riavviarla in un dispositivo diverso o in un emulatore di dispositivi mobili.

	![Telefono completo di guida introduttiva di Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)

	Si noti che i dati salvati dal passaggio precedente vengono caricati da Azure dopo l'avvio dell'app UWP.

##Passaggi successivi

* [Aggiungere l'autenticazione all'app](app-service-mobile-windows-store-dotnet-get-started-users.md) Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

* [Aggiungere notifiche push all'app](app-service-mobile-windows-store-dotnet-get-started-push.md) Informazioni su come aggiungere il supporto per le notifiche push all'app e configurare il back-end dell'app per dispositivi mobili per l'utilizzo di Hub di notifica di Azure per l'invio di notifiche push.

* [Abilitare la sincronizzazione offline per l’app di Windows](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) informazioni su come aggiungere il supporto offline all’app mediante un back-end di App per dispositivi mobili. La sincronizzazione offline consente agli utenti finali di interagire con un'app, visualizzando, aggiungendo e modificando i dati, anche se non è disponibile una connessione di rete.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2015]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_0525_2016-->