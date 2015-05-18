<properties 
	pageTitle="Creare un pacchetto dell'app per le API" 
	description="Informazioni su come pubblicare un pacchetto dell'app per le API in Azure Marketplace." 
	services="app-service\api" 
	documentationCenter=".net"
	authors="guangyang"
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="guayan"/>

# Pubblicare un pacchetto dell'app per le API in Azure Marketplace

## Panoramica

Questo articolo illustra come pubblicare un pacchetto dell'app per le API in [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/).

- Per informazioni su come creare un'app per le API, vedere [Creare un'app per le API con Visual Studio](app-service-dotnet-create-api-app.md).
- Per informazioni su come creare un pacchetto dell'app per le API, vedere [Creare un pacchetto dell'app per le API](app-service-api-create-package).

## Flusso di pubblicazione generale

Di seguito è illustrato il flusso di pubblicazione generale

1. Creare un pacchetto NuGet per l'app per le API seguendo le istruzioni fornite nell'esercitazione [Creare un pacchetto dell'app per le API](app-service-api-create-package).
2. Pubblicarla in una raccolta supportata da Nuget all'indirizzo https://apiapps.nuget.org.
3. Verrà quindi sincronizzata automaticamente con [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/).
4. Passare ad [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/) e al [portale di anteprima di Azure](https://portal.azure.com) per verificare l'esperienza end-to-end.

## Pubblicazione nella raccolta supportata da NuGet

1. Passare a https://apiapps.nuget.org.

    ![Home page della raccolta supportata da NuGet](./media/app-service-api-publish-package/nuget-homepage.png)

2. Fare clic su **Sign in** e accedere con l'account Azure.
3. Fare clic sulla scheda **API Apps**, quindi su **Upload API App** e infine caricare il pacchetto dell'app per le API.

    ![Pagina di caricamento del pacchetto della raccolta supportata da NuGet](./media/app-service-api-publish-package/nuget-upload-package-page.png)

4. Nell'elenco a discesa di **Namespace** verranno visualizzati tutti i domini verificati dei tenant Azure Active Directory dell'account Azure. Assicurarsi di selezionarne uno che corrisponde alla proprietà namespace nel file apiapp.json del pacchetto dell'app per le API. Questo garantisce che gli editori richiedano spazi dei nomi validi per i propri pacchetti di app per le API.
5. Assicurarsi che la casella **Unlist this API app** sia deselezionata.
6. Fare clic su **Publish**.
7. Se vengono restituiti errori di convalida, correggerli e caricare di nuovo il pacchetto.

## Visualizzare il pacchetto dell'app per le API in Azure Marketplace

Dopo qualche minuto, il pacchetto dell'app per le API verrà sincronizzato con Azure Marketplace. Fare clic [qui](http://azure.microsoft.com/marketplace/api-apps/) per verificare il titolo, la descrizione, l'icona e così via. Se è necessario cambiare qualcosa, apportare le modifiche nel pacchetto dell'app per le API e ripubblicarlo.

![Pagina dell'app per le API di Azure Marketplace](./media/app-service-api-publish-package/acom-marketplace-apiapp-page.png)

## Distribuire il pacchetto dell'app per le API nel portale di anteprima di Azure

È possibile accedere al [portale di anteprima di Azure](https://portal.azure.com) usando l'account di Azure \(può essere un account diverso da quello usato per pubblicare il pacchetto dell'app per le API\). Qui sarà possibile verificare l'esperienza di creazione del portale di anteprima di Azure per il pacchetto dell'app per le API. Se è necessario cambiare qualcosa, apportare le modifiche nel pacchetto dell'app per le API e ripubblicarlo.

Per informazioni dettagliate su come distribuire un pacchetto dell'app per le API nel portale di Azure, fare clic [qui](app-service-api-connnect-your-app-to-saas-connector.md) per visualizzare un esempio di distribuzione di DropboxConnector.

<!--HONumber=52-->
