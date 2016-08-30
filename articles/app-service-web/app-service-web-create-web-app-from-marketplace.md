<properties
	pageTitle="Creare un'app Web da Azure Marketplace | Microsoft Azure"
	description="Informazioni su come creare una nuova app Web WordPress da Azure Marketplace usando il portale di Azure."
	services="app-service\web"
	documentationCenter=""
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/11/2016"
	ms.author="robmcm"/>

<!-- Note: This article replaces web-sites-php-web-site-gallery.md -->

# Creare un'app Web da Azure Marketplace

[AZURE.INCLUDE [schede](../../includes/app-service-web-get-started-nav-tabs.md)]

Azure Marketplace rende disponibile un'ampia varietà di applicazioni Web ampiamente diffuse, sviluppate da Microsoft, da terze parti e tramite iniziative software open source. Ad esempio, WordPress, Umbraco CMS, Drupal e così via. Queste app Web sono basate su una vasta gamma di framework noti, ad esempio [PHP] in questo esempio WordPress, [.NET], [Node.js], [Java] e [Python], per citarne alcuni. Per creare un'app Web da Azure Marketplace, l'unico software necessario è il browser usato per il [portale di Azure].

In questa esercitazione si apprenderà come:

* Individuare e creare un'app Web nel servizio app di Azure basata su un modello di Azure Marketplace.
* Configurare le impostazioni del servizio app di Azure per la nuova app Web.
* Avviare e gestire l'app Web.

Ai fini di questa esercitazione, si distribuirà un sito blog di WordPress da Azure Marketplace. Una volta completati i passaggi di questa esercitazione, si otterrà un sito WordPress attivo e in esecuzione nel cloud.

![Dashboard dell'app Web WordPress di esempio][WordPressDashboard1]

Il sito WordPress che verrà distribuito in questa esercitazione usa MySQL come database. Se si preferisce usare un database SQL, vedere [Project Nami], disponibile anche in Azure Marketplace.

> [AZURE.NOTE]
Per completare l'esercitazione, è necessario un account Microsoft Azure. Se non si dispone di un account, è possibile [attivare i benefici della sottoscrizione Visual Studio][activate] oppure [iscriversi per ottenere una versione di valutazione gratuita][free trial].
>
> Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, passare alla pagina [Prova il servizio app]. In questa pagina è possibile creare immediatamente un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Individuare e trovare un'app Web nel servizio app di Azure

1. Accedere al [Portale di Azure].

1. Fare clic su **New**.
	
	![Creare una nuova risorsa di Azure][MarketplaceStart]
	
1. Cercare **WordPress**, quindi fare clic su **WordPress**. Per usare il database SQL invece di MySQL, cercare **Project Nami**.

	![Cercare WordPress nel Marketplace][MarketplaceSearch]
	
1. Dopo aver letto la descrizione dell'app WordPress, fare clic su **Crea**.

	![Creare un'app Web WordPress][MarketplaceCreate]

## Configurare le impostazioni del servizio app di Azure per la nuova app Web

1. Al termine della creazione di una nuova app Web, verrà visualizzato il pannello delle impostazioni WordPress, in cui sarà possibile completare i passaggi seguenti:

	![Configurare le impostazioni dell'app Web WordPress][ConfigStart]

1. Immettere un nome per l'app Web nella casella **App Web**.

	Il nome deve essere univoco nel dominio azurewebsites.net perché l'URL dell'app Web sarà *{nome}*.azurewebsites.net. Se il nome immesso non è univoco, nella casella di testo verrà visualizzato un punto esclamativo rosso.

	![Configurare il nome dell'app Web WordPress][ConfigAppName]

1. Se sono disponibili più sottoscrizioni, scegliere quella da usare.

	![Configurare la sottoscrizione per l'app Web][ConfigSubscription]

1. Selezionare un **Gruppo di risorse** o crearne uno nuovo.

	Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager][ResourceGroups].

	![Configurare il gruppo di risorse per l'app Web][ConfigResourceGroup]

1. Selezionare un **Piano di servizio app/Posizione** o crearne uno nuovo.

	Per altre informazioni sui piani del servizio app, vedere [Panoramica approfondita dei piani del servizio app di Azure][AzureAppServicePlans].

	![Configurare il piano di servizio per l'app Web][ConfigServicePlan]

1. Fare clic su **Database**, quindi nel pannello **Nuovo database MySQL** specificare i valori necessari per configurare il database MySQL.

	a. Immettere un nuovo nome o lasciare il nome predefinito.

	b. Lasciare **Tipo database** impostato su **Condiviso**.

	c. Scegliere la stessa posizione usata per l'app Web.

	d. Scegliere un piano tariffario. **Mercury**, gratuito con quantità minima di connessioni e di spazio su disco, è ottimale per questa esercitazione.

	e. Nel pannello **Nuovo database MySQL** accettare le condizioni legali, quindi fare clic su **OK**.

	![Configurare le impostazioni del database per l'app Web][ConfigDatabase]

1. Nel pannello **WordPress** accettare le condizioni legali, quindi fare clic su **Crea**.

	![Completare le impostazioni dell'app Web e fare clic su OK][ConfigFinished]

	Il servizio app di Azure crea l'app Web, in genere in meno di un minuto. È possibile verificare lo stato facendo clic sull'icona a forma di campana nella parte superiore della pagina del portale.

	![Indicatore di stato][ConfigProgress]

## Avviare e gestire l'app Web WordPress
	
1. Al termine della creazione dell'app Web, nel portale di Azure passare al gruppo di risorse in cui è stata creata l'applicazione. L'app Web e il database saranno visualizzati nel gruppo.

	La risorsa aggiuntiva con l'icona a forma di lampadina è [Application Insights][ApplicationInsights], che fornisce servizi di monitoraggio per l'app Web.

1. Nel pannello **Gruppo di risorse** fare clic sulla riga dell'app Web.

	![Selezionare l'app Web WordPress][WordPressSelect]

1. Nel pannello dell'app Web fare clic su **Sfoglia**.

	![Passare all'app Web WordPress][WordPressBrowse]

1. Se viene chiesto di selezionare la lingua per il blog WordPress, selezionare la lingua preferita e quindi fare clic su **Continua**.

	![Configurare la lingua dell'app Web WordPress][WordPressLanguage]

1. Nella pagina **iniziale** di WordPress immettere le informazioni di configurazione richieste da WordPress, quindi fare clic su **Install WordPress**.

	![Configurare le impostazioni dell'app Web WordPress][WordPressConfigure]

1. Accedere usando le credenziali create nella pagina **iniziale**.

1. Verrà aperta la pagina Dashboard del sito che visualizzerà le informazioni fornite.

	![Visualizzare il dashboard di WordPress][WordPressDashboard2]

## Passaggi successivi

In questa esercitazione è stato illustrato come creare e distribuire un'app Web di esempio da Azure Marketplace.

Per altre informazioni su come utilizzare le app Web del servizio app, vedere i collegamenti nel lato sinistro della pagina (nelle finestre del browser di grandi dimensioni) o nella parte superiore della pagina (per le finestre del browser ridotte).

Per altre informazioni sullo sviluppo di app Web WordPress in Azure, vedere [Sviluppo di WordPress nel servizio app di Azure][WordPressOnAzure].

<!-- URL List -->

[PHP]: https://azure.microsoft.com/develop/php/
[.NET]: https://azure.microsoft.com/develop/net/
[Node.js]: https://azure.microsoft.com/develop/nodejs/
[Java]: https://azure.microsoft.com/develop/java/
[Python]: https://azure.microsoft.com/develop/python/
[activate]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[free trial]: https://azure.microsoft.com/pricing/free-trial/
[Prova il servizio app]: http://go.microsoft.com/fwlink/?LinkId=523751
[ResourceGroups]: ../resource-group-overview.md
[AzureAppServicePlans]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[ApplicationInsights]: https://azure.microsoft.com/services/application-insights/
[portale di Azure]: https://portal.azure.com/
[Project Nami]: http://projectnami.org/
[WordPressOnAzure]: ./develop-wordpress-on-app-service-web-apps.md

<!-- IMG List -->

[MarketplaceStart]: ./media/app-service-web-create-web-app-from-marketplace/marketplacestart.png
[MarketplaceSearch]: ./media/app-service-web-create-web-app-from-marketplace/marketplacesearch.png
[MarketplaceCreate]: ./media/app-service-web-create-web-app-from-marketplace/marketplacecreate.png
[ConfigStart]: ./media/app-service-web-create-web-app-from-marketplace/configstart.png
[ConfigAppName]: ./media/app-service-web-create-web-app-from-marketplace/configappname.png
[ConfigSubscription]: ./media/app-service-web-create-web-app-from-marketplace/configsubscription.png
[ConfigResourceGroup]: ./media/app-service-web-create-web-app-from-marketplace/configresourcegroup.png
[ConfigServicePlan]: ./media/app-service-web-create-web-app-from-marketplace/configserviceplan.png
[ConfigDatabase]: ./media/app-service-web-create-web-app-from-marketplace/configdatabase.png
[ConfigFinished]: ./media/app-service-web-create-web-app-from-marketplace/configfinished.png
[ConfigProgress]: ./media/app-service-web-create-web-app-from-marketplace/configprogress.png
[WordPressSelect]: ./media/app-service-web-create-web-app-from-marketplace/wpselect.png
[WordPressBrowse]: ./media/app-service-web-create-web-app-from-marketplace/wpbrowse.png
[WordPressLanguage]: ./media/app-service-web-create-web-app-from-marketplace/wplanguage.png
[WordPressDashboard1]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard1.png
[WordPressDashboard2]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard2.png
[WordPressConfigure]: ./media/app-service-web-create-web-app-from-marketplace/wpconfigure.png

<!-----HONumber=AcomDC_0720_2016-->