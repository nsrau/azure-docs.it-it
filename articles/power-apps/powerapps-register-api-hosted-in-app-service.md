<properties
	pageTitle="Sviluppare o creare un'API ospitata nell'ambiente del servizio app in PowerApps Enterprise | Microsoft Azure"
	description="Informazioni su come registrare un'API personalizzata ospitata nell'ambiente del servizio app nel portale di Azure"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="12/09/2015"
   ms.author="guayan"/>

# Registrare un'API ospitata all'interno dell'ambiente del servizio app
PowerApps supporta la registrazione delle API esistenti ospitate in un punto qualsiasi nel cloud o locali, funzionalità molto potente. In alcuni scenari, è possibile sviluppare o creare alcune nuove API. Ad esempio, è possibile:

- Implementare alcune nuove funzionalità che l'organizzazione può utilizzare.
- Basarsi sulle funzionalità o sui dati esistenti per fornire una migliore esperienza agli utenti che creano le proprie app.

Quando si ospitano le API nell'ambiente del servizio app, sfruttare tutte le funzionalità esistenti dell’[ambiente del servizio app](../app-service-app-service-environment-intro.md) e ottenere anche una migliore esperienza di integrazione.

Per utilizzare queste API nelle applicazioni, è necessario "registrare" le API nel portale di Azure. Sono disponibili le seguenti opzioni:

- Registrare un'[API gestita da Microsoft o gestita dal reparto IT](powerapps-register-from-available-apis.md) predefinita.
- Registrare un'app Web, un'app per le API e un'app per dispositivi mobili ospitata nell'ambiente del servizio app (in questo argomento).
- Registrare una delle API Swagger usando una [definizione API di Swagger 2.0](powerapps-register-existing-api-from-api-definition.md).

Questo articolo illustra come **registrare un'app Web, un'app per le API e un'app per dispositivi mobili ospitata nell'ambiente del servizio app**.

#### Prerequisiti per iniziare

- Iscriversi a [PowerApps Enterprise](powerapps-get-started-azure-portal.md).
- Creare un [ambiente di servizio app](powerapps-get-started-azure-portal.md).


## Sviluppare e distribuire un'API in un ambiente del servizio app

Lo sviluppo di un'API nell'ambiente del servizio app è semplice. Si sceglie il linguaggio di programmazione preferito per creare un'API Web e quindi si utilizza [Swagger 2.0](http://swagger.io) per descrivere la definizione dell'API. Di seguito sono riportati alcuni esempi:

- [Compilare e distribuire un'app .NET nel servizio app di Azure](../app-service-api-dotnet-get-started.md)
- [Compilare e distribuire un'app per le API Java nel servizio app di Azure](../app-service-api-java-api-app.md)
- [Compilare e distribuire un'app per le API Node.js in Servizio app di Azure](../app-service-api-nodejs-api-app.md)

Sono inoltre disponibili opzioni per distribuire l'API Web in un ambiente del servizio app, tra cui la distribuzione da Visual Studio e la distribuzione continua mediante un sistema di controllo del codice sorgente. [Distribuire un'app Web nel servizio app di Azure](../web-sites-deploy.md) rappresenta una buona risorsa.

## Registrare l'API personalizzata nell'ambiente del servizio app

Dopo aver distribuito l'API nell'ambiente del servizio app, utilizzare la procedura seguente per registrare:

1. Nel [portale di Azure](https://portal.azure.com/), selezionare **PowerApps**, quindi selezionare **Gestisci API**: ![][11]
2. In Gestisci API, selezionare **Aggiungi**: ![][12]  
3. In **Aggiungi API**, immettere le proprietà dell'API:  

	- In **Nome**, immettere un nome per l'API. Tenere presente che il nome immesso viene compreso nell'URL di runtime dell'API. Il nome deve essere significativo e univoco all'interno dell'organizzazione.	
	- In **Origine** selezionare **Importa le API ospitate nell'ambiente del servizio App**: ![][13]
4. In **API ospitata nell'ambiente del servizio app** selezionare l'API che si desidera importare. Questo elenco mostra tutte le app Web, le app per le API e le app per dispositivi mobili nell'ambiente del servizio app la cui proprietà **apiDefinition.url** è configurata. Per importare l'API, viene utilizzata la definizione API Swagger 2.0 esposta tramite questa proprietà. Assicurarsi che questo URL sia pubblicamente accessibile quando si registra l'API: ![][14]
5. Selezionare **AGGIUNGI** per completare questi passaggi.

> [AZURE.TIP]Quando si registra un'API, ciò avviene nel proprio ambiente del servizio app. Una volta effettuata tale registrazione, l'API è utilizzabile da altre app presenti nello stesso ambiente del servizio app.

## Riepilogo e passaggi successivi
In questo articolo è stato possibile esaminare come registrare API ospitate nell'ambiente del servizio app. Per altre informazioni sulle PowerApps, vedere gli articoli e le risorse correlati seguenti:

- [Configurare le proprietà dell'API](powerapps-configure-apis.md)
- [Consentire agli utenti di accedere alle API](powerapps-manage-api-connection-user-access.md)
- [Iniziare a creare le app in PowerApps](https://powerapps.microsoft.com/tutorials/)

<!--Reference-->
[11]: ./media/powerapps-register-api-hosted-in-app-service/registered-apis-part.png
[12]: ./media/powerapps-register-api-hosted-in-app-service/add-api-button.png
[13]: ./media/powerapps-register-api-hosted-in-app-service/add-api-blade.png
[14]: ./media/powerapps-register-api-hosted-in-app-service/add-api-select-from-ase.png

<!---HONumber=AcomDC_1210_2015-->