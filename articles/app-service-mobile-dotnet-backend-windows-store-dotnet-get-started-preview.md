<properties
	pageTitle="Introduzione ai back-end dell'app per dispositivi mobili per app Windows Store | Mobile Dev Center"
	description="Seguire questa esercitazione per iniziare a usare i back-end dell'app per dispositivi mobili di Azure per lo sviluppo per Windows Store in C#, VB o JavaScript."
	services="app-service\mobile"
	documentationCenter="windows"
	authors="christopheranderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/24/2015"
	ms.author="chrande"/>


# <a name="getting-started"> </a>Creare un'app di Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../includes/app-service-mobile-selector-get-started-preview.md)]

Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app di Windows mediante un back-end dell'app per dispositivi mobili di Azure. Le soluzioni per app di Windows universali includono progetti di app per Windows Store 8.1 e Windows Phone Store 8.1 e un progetto condiviso comune.

[AZURE.INCLUDE [app-service-mobile-windows-universal-get-started-preview](../includes/app-service-mobile-windows-universal-get-started-preview.md)]

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si dispone di un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 app per dispositivi mobili gratuite che potranno essere usate anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE]Se si vuole iniziare a usare il servizio app di Azure prima di creare un account Azure, passare alla pagina di [prova del servizio app](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), in cui è possibile creare immediatamente un'app per dispositivi mobili di base temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## <a name="create-new-service"> </a>Creare un nuovo back-end dell'app per dispositivi mobili

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Creare una nuova app di Windows universale

Dopo aver creato il back-end dell'app per dispositivi mobili, usare l'avvio rapido disponibile nel portale di Azure per creare una nuova app o modificare un'app esistente per connettersi al back-end dell'app per dispositivi mobili.

In questa sezione si creerà una nuova app di Windows universale connessa al back-end dell'app per dispositivi mobili.

1. Nel portale di Azure fare clic su **App per dispositivi mobili** e selezionare l'app per dispositivi mobili appena creata.

2. Nella parte superiore del pannello fare clic su **Aggiungi client** ed espandere **Windows \(C\#\)**.

   ![Procedura di creazione rapida dell'app per dispositivi mobili](./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview/windows-quickstart.png)

   Vengono visualizzati i tre semplici passaggi per creare un'app di Windows Store connessa al back-end dell'app per dispositivi mobili.

3. Se necessario, scaricare e installare <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> nel computer locale o nella macchina virtuale.

4. In **Scarica ed esegui l'app e il servizio localmente** selezionare un linguaggio per l'app di Windows Store, quindi fare clic su **Scarica**.

   Verrà scaricata una soluzione che contiene progetti sia per il back-end dell'app per dispositivi mobili, sia per l'applicazione _To do list_ di esempio connessa al back-end dell'app per dispositivi mobili. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

## Testare l'app per dispositivi mobili

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Pubblicare il back-end dell'app per dispositivi mobili

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Esecuzione dell'app per Windows

Una volta pubblicato il back-end dell'app per dispositivi mobili e aver connesso il client al back-end dell'app per dispositivi mobili ospitato in Azure, è possibile eseguire l'app usando Azure per l'archiviazione degli elementi.

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-app-preview](../includes/app-service-mobile-windows-universal-test-app-preview.md)]

<!-- Anchors. -->

[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Define the mobile app backend instance]: #define-mobile-app-backend-instance
[Next Steps]: #next-steps

<!-- Images. -->



<!-- URLs. -->
[Get started with authentication]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-users-preview.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure Portal]: https://portal.azure.com/

<!--HONumber=52-->
