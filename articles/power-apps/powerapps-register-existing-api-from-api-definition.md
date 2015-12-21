<properties
	pageTitle="Creare una definizione API Swagger 2.0 da un'API in PowerApps Enterprise | Microsoft Azure"
	description="Informazioni su come registrare un'API dalla definizione dell'API di Swagger 2.0 creata da un'API esistente"
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

# Registrare un'API dalla definizione dell'API Swagger 2.0  
Molte organizzazioni dispongono già di alcune API che gli utenti possono utilizzare e sfruttare all'interno delle proprie app. Per utilizzare queste API nelle applicazioni, è necessario "registrare" le API nel portale di Azure. Sono disponibili le seguenti opzioni:

- Registrare un'[API gestita da Microsoft o gestita dal reparto IT](powerapps-register-from-available-apis.md) predefinita.
- Registrare un'app Web, un'app per le API e un'app per dispositivi mobili ospitata nell'[ambiente del servizio app](powerapps-register-api-hosted-in-app-service.md).
- Registrare una delle API Swagger usando una definizione API di Swagger 2.0 (in questo argomento).

Questo articolo illustra come **registrare una delle API mediante la definizione delle API di Swagger 2.0** creata da un'API esistente.

#### Prerequisiti per iniziare

- Iscriversi a [PowerApps Enterprise](powerapps-get-started-azure-portal.md).
- Creare un [ambiente di servizio app](powerapps-get-started-azure-portal.md)

## Registrare un'API esistente utilizzando la definizione API Swagger 2.0

È molto semplice registrare queste API esistenti. Di seguito è riportata la procedura:

1. Creare la definizione API [Swagger 2.0](http://swagger.io) per le API esistenti. PowerApps utilizza API Swagger 2.0 come formato di definizione. È possibile utilizzare gli strumenti indicati nel [sito Web di Swagger 2.0](http://swagger.io) che consentono di creare facilmente una definizione API Swagger 2.0. Alcuni punti da notare:  

	- La proprietà ``host`` deve fare riferimento all'endpoint effettivo dell'API esistente. Non utilizzare lo schema o i percorsi secondari. Ad esempio, immettere ``api.contoso.com``. <br/><br/>
	- La proprietà ``basePath`` elenca i percorsi secondari dell'endpoint dell’API esistente, se presente. Iniziare con una barra ``/``. Ad esempio, immettere ``/purchaseorderapi``.

2. Assicurarsi che l’API esistente sia accessibile dall'ambiente del servizio app in modo sicuro: <br/><br/> a) Se si è sicuri di voler rendere l'API accessibile tramite Internet, è possibile configurare l'autenticazione dell’accesso di base HTTP tra l'ambiente del servizio app e l’API esistente. Vedere [qui](powerapps-configure-apis.md) per sapere come. <br/><br/> b) Se si desidera mantenere l’API all'interno della rete aziendale, è possibile impostare una rete virtuale nell'ambiente del servizio app per accedere alla rete dell'organizzazione in modo sicuro. Vedere [qui](../app-service-app-service-environment-intro.md) per sapere come.

3. Nel [portale di Azure](https://portal.azure.com/), selezionare **PowerApps**, quindi selezionare **Gestisci API**: ![][11]
4. In Gestisci API, selezionare **Aggiungi**: ![][12]
5. In **Aggiungi API**, immettere le proprietà dell'API:  

	- In **Nome**, immettere un nome per l'API. Tenere presente che il nome immesso viene compreso nell'URL di runtime dell'API. Il nome deve essere significativo e univoco all'interno dell'organizzazione.
	- In **Origine** selezionare **Importa da Swagger 2.0**.

6. In **Definizione API (Swagger 2.0)** caricare il file di definizione API Swagger 2.0: ![][13]
7. Selezionare **AGGIUNGI** per completare questi passaggi.

> [AZURE.TIP]Quando si registra un'API, ciò avviene nel proprio ambiente del servizio app. Una volta effettuata tale registrazione, l'API è utilizzabile da altre app presenti nello stesso ambiente del servizio app.

## Riepilogo e passaggi successivi

In questo argomento è stato possibile esaminare come registrare un'API dalla definizione API Swagger 2.0. Per altre informazioni sulle PowerApps, vedere gli articoli e le risorse correlati seguenti:

- [Configurare le proprietà dell'API](powerapps-configure-apis.md)
- [Consentire agli utenti di accedere alle API](powerapps-manage-api-connection-user-access.md)
- [Iniziare a creare le app in PowerApps](https://powerapps.microsoft.com/tutorials/)

<!--References-->
[11]: ./media/powerapps-register-existing-api-from-api-definition/registered-apis-part.png
[12]: ./media/powerapps-register-existing-api-from-api-definition/add-api-button.png
[13]: ./media/powerapps-register-existing-api-from-api-definition/add-api-blade.png

<!---HONumber=AcomDC_1210_2015-->