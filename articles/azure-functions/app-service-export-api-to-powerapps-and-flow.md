---
title: Esportazione di un'API ospitata in Azure in PowerApps e Microsoft Flow | Microsoft Docs
description: Panoramica su come esporre un'API ospitata nel servizio app in PowerApps e Microsoft Flow
services: app-service
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 12/15/2017
ms.author: mahender; mblythe
ms.openlocfilehash: 7482ca27c2edcb281180fb8fbbfb1884a515d379
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Esportazione di un'API ospitata in Azure in PowerApps e Microsoft Flow

[PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) è un servizio per la creazione e l'uso di applicazioni aziendali personalizzate in grado di connettersi ai dati dell'utente e di funzionare su più piattaforme. [Microsoft Flow](https://flow.microsoft.com/guided-learning/learning-introducing-flow/) facilita l'automazione dei flussi di lavoro e dei processi aziendali tra le app e i servizi preferiti dagli utenti. Sia PowerApps che Microsoft Flow dispongono di un'ampia varietà di connettori predefiniti a origini di dati come Office 365, Dynamics 365, Salesforce e così via. In alcuni casi, chi crea app e flussi vuole anche connettersi alle origini dati e alle API create dall'organizzazione.

Analogamente, gli sviluppatori che vogliono esporre ulteriormente le proprie API in un'organizzazione possono renderle disponibili a chi crea app e flussi. Questo argomento mostra come esportare un'API compilata con [Funzioni di Azure](../azure-functions/functions-overview.md) o [Servizio app di Azure](../app-service/app-service-web-overview.md). L'API esportata diventa un *connettore personalizzato*, che viene usato in PowerApps e Microsoft Flow proprio come un connettore predefinito.

## <a name="create-and-export-an-api-definition"></a>Creare ed esportare una definizione dell'API
Prima di esportare un'API, è necessario descrivere l'API usando una definizione OpenAPI (nota in passato come file [Swagger](http://swagger.io/)). Questa definizione contiene informazioni sulle operazioni disponibili in un'API e su come devono essere strutturati i dati della richiesta e della risposta per l'API. PowerApps e Microsoft Flow possono creare connettori personalizzati per qualsiasi definizione OpenAPI 2.0. Funzioni di Azure e Servizio app di Azure includono supporto predefinito per creazione, hosting e gestione di definizioni OpenAPI. Per altre informazioni, vedere [Creare un'API RESTful nelle app Web di Azure](../app-service/app-service-web-tutorial-rest-api.md).

> [!NOTE]
> È anche possibile creare connettori personalizzati nell'interfaccia utente di PowerApps e Microsoft Flow, senza usare una definizione OpenAPI. Per altre informazioni, vedere [Registrare e usare connettori personalizzati in PowerApps](https://powerapps.microsoft.com/tutorials/register-custom-api/) e [Registrare e usare connettori personalizzati in Microsoft Flow](https://flow.microsoft.com/documentation/register-custom-api/).

Per esportare la definizione dell'API, seguire questi passaggi:

1. Nel [portale di Azure](https://portal.azure.com), passare a funzioni di Azure o un'altra applicazione di servizio App.

    Se si usa Funzioni di Azure, selezionare l'app per le funzioni, scegliere **Funzionalità della piattaforma** e quindi **Definizione API**.

    ![Definizione dell'API di Funzioni di Azure](media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png)

    Se si usa il Servizio app di Azure, selezionare **Definizione dell'API** dall'elenco delle impostazioni.

    ![Definizione dell'API del servizio app](media/app-service-export-api-to-powerapps-and-flow/api-definition-app.png)

2. Il pulsante **Esporta in PowerApps e Microsoft Flow** dovrebbe essere disponibile. In caso contrario, è necessario creare prima una definizione OpenAPI. Fare clic su questo pulsante per avviare il processo di esportazione.

    ![Pulsante Esporta in PowerApps e Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Selezionare il **modalità esportazione**:

    **Rapida**: consente di creare il connettore personalizzato dall'interno del portale di Azure. È necessario aver effettuato l'accesso in PowerApps o Microsoft Flow e avere l'autorizzazione per creare connettori nell'ambiente di destinazione. Questo è l'approccio consigliato se possono essere soddisfatti questi due requisiti. Se si usa questa modalità, seguire le istruzioni incluse nella sezione [Usare l'esportazione rapida](#express) di seguito.

    **Manuale**: permette di esportare la definizione dell'API, che può quindi essere importata tramite il portale di PowerApps o di Microsoft Flow. Questo è l'approccio consigliato se l'utente di Azure e l'utente con autorizzazione per creare i connettori sono diverse o se il connettore deve essere creato in un altro tenant di Azure. Se si usa questa modalità, seguire le istruzioni incluse nella sezione [Usare l'esportazione manuale](#manual) di seguito.

    ![Modalità di esportazione](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> Poiché il connettore personalizzato usa una *copia* della definizione dell'API, PowerApps e Microsoft Flow non riconoscono immediatamente se sono state apportate modifiche all'applicazione e alla definizione dell'API associata. Se si apportano modifiche, ripetere i passaggi di esportazione per la nuova versione.

<a name="express"></a>
## <a name="use-express-export"></a>Usare l'esportazione rapida

Per completare l'esportazione in modalità **Rapida**, seguire questi passaggi:

1. Assicurarsi di aver effettuato l'accesso al tenant di PowerApps o Microsoft Flow in cui si vuole eseguire l'esportazione. 

2. Usare le impostazioni specificate nella tabella.

    |Impostazione|DESCRIZIONE|
    |--------|------------|
    |**Environment**|Selezionare l'ambiente in cui deve essere salvato il connettore personalizzato. Per altre informazioni, vedere [Panoramica degli ambienti](https://powerapps.microsoft.com/tutorials/environments-overview/).|
    |**Nome dell'API personalizzata**|Immettere un nome, che gli sviluppatori che usano PowerApps e Microsoft Flow visualizzeranno nel proprio elenco di connettori.|
    |**Preparare la configurazione di sicurezza**|Se necessario, specificare i dettagli di configurazione di sicurezza necessari per concedere ad altri utenti l'accesso all'API. Questo esempio mostra una chiave API. Per altre informazioni, vedere [Specificare il tipo di autenticazione](#auth) di seguito.|
 
    ![Esportazione rapida in PowerApps e Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Fare clic su **OK**. Il connettore personalizzato viene ora creato e aggiunto all'ambiente specificato.

Per un esempio dell'uso della modalità **Rapida** con Funzioni di Azure, vedere [Chiamare una funzione da PowerApps](functions-powerapps-scenario.md) e [Chiamare una funzione da Microsoft Flow](functions-flow-scenario.md).

<a name="manual"></a>
## <a name="use-manual-export"></a>Usare l'esportazione manuale

Per completare l'esportazione in modalità **Manuale**, seguire questi passaggi:

1. Fare clic su **Scarica** e salvare il file oppure fare clic sul pulsante Copia e salvare l'URL. Durante l'importazione verrà usato il file di download o l'URL.
 
    ![Esportazione manuale in PowerApps e Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Se la definizione dell'API include definizioni di sicurezza, queste verranno mostrate nel passaggio 2. Durante l'importazione, PowerApps e Microsoft Flow rileveranno queste definizioni e chiederanno informazioni sulla sicurezza. Raccogliere le credenziali relative a ogni definizione per l'utilizzo nella sezione successiva. Per altre informazioni, vedere [Specificare il tipo di autenticazione](#auth) di seguito.

    ![Sicurezza per l'esportazione manuale](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    Questo esempio mostra la definizione di sicurezza con chiave API che è stata inclusa nella definizione OpenAPI.

Dopo aver esportato la definizione dell'API, è necessario importarla per creare un connettore personalizzato in PowerApps e Microsoft Flow. Poiché i connettori personalizzati sono condivisi tra i due servizi, è necessario importare la definizione una sola volta.

Per importare la definizione API in PowerApps e Microsoft Flow, seguire questi passaggi:

1. Passare a [powerapps.com](https://web.powerapps.com) o [flow.microsoft.com](https://flow.microsoft.com).

2. In alto a destra, fare clic sull'icona a forma di ingranaggio, quindi fare clic su **connettori personalizzati**.

   ![Icona dell'ingranaggio nel servizio](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. Fare clic su **creare un connettore personalizzato**, quindi fare clic su **importare una definizione di OpenAPI**.

   ![Creare un connettore personalizzato](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. Immettere un nome per il connettore personalizzato, quindi passare alla definizione OpenAPI esportato e fare clic su **continua**.

   ![Caricare la definizione di OpenAPI](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. Nel **generale** scheda, esaminare le informazioni da cui deriva la definizione di OpenAPI.

5. Se nella scheda **Sicurezza** viene chiesto di fornire i dettagli di autenticazione, immettere i valori appropriati per il tipo di autenticazione. Fare clic su **Continue**.

    ![Scheda Sicurezza](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    Questo esempio mostra i campi obbligatori per l'autenticazione con chiave API. I campi differiscono a seconda del tipo di autenticazione.

6. Nella scheda **Definizioni** tutte le operazioni definite nel file OpenAPI vengono popolate automaticamente. Se sono state definite tutte le operazioni necessarie, si può procedere al passaggio successivo. In caso contrario, è possibile aggiungere e modificare qui le operazioni.

    ![Scheda Definizioni](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    Questo esempio include un'unica operazione, chiamata `CalculateCosts`. I metadati, come **Descrizione**, provengono tutti dal file OpenAPI.

7. Fare clic su **Crea connettore** nella parte superiore della pagina.

È ora possibile connettersi al connettore personalizzato in PowerApps e Microsoft Flow. Per altre informazioni sulla creazione di connettori nei portali di PowerApps e Microsoft Flow, vedere [Registrare il connettore personalizzato (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) e [Registrare il connettore personalizzato (Microsoft Flow)](https://flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector).

<a name="auth"></a>
## <a name="specify-authentication-type"></a>Specificare il tipo di autenticazione

PowerApps e Microsoft Flow supportano una raccolta di provider di identità che forniscono l'autenticazione per i connettori personalizzati. Se l'API richiede l'autenticazione, assicurarsi che venga acquisita come _definizione di sicurezza_ nel documento OpenAPI, come nell'esempio seguente:

```json
"securityDefinitions": {
    "AAD": {
    "type": "oauth2",
    "flow": "accessCode",
    "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
    "scopes": {}
    }
}
``` 
Durante l'esportazione, è necessario specificare i valori di configurazione che consentono a PowerApps e Microsoft Flow di autenticare gli utenti.

Questa sezione descrive i tipi di autenticazione supportati in modalità **Rapida**: chiave API, Azure Active Directory e OAuth 2.0 generica. PowerApps e Microsoft Flow supportano anche l'autenticazione di base e OAuth 2.0 per servizi specifici, tra cui Dropbox, Facebook e SalesForce.

### <a name="api-key"></a>Chiave API
Quando si usa una chiave API, agli utenti del connettore viene chiesto di specificare una chiave quando creano una connessione. È necessario specificare il nome della chiave API per aiutarli a identificare la chiave necessaria. Nell'esempio precedente viene usato il nome `API Key (contact meganb@contoso.com)` per indicare agli utenti dove ottenere informazioni sulla chiave API. Per Funzioni di Azure, la chiave è in genere una delle chiavi host, che svolge diverse funzioni all'interno dell'app per le funzioni.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Quando si usa Azure AD, sono necessarie due registrazioni per l'applicazione Azure AD: una per l'API stessa e l'altra per il connettore personalizzato:

- Per configurare la registrazione per l'API, usare la funzionalità di [autenticazione/autorizzazione del servizio app](../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md).

- Per configurare la registrazione per il connettore, seguire i passaggi riportati in [Aggiunta di un'applicazione Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application). La registrazione deve avere accesso delegato all'API e un URL di risposta `https://msmanaged-na.consent.azure-apim.net/redirect`. 

Per altre informazioni, vedere gli esempi di registrazione in Azure AD per [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) e [Microsoft Flow](https://flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/). Questi esempi usano Azure Resource Manager come API. Usare la propria API se si seguono questi passaggi.

Sono richiesti i valori di configurazione seguenti:
- **ID client** - L'ID client della registrazione in Azure AD del connettore
- **Segreto client** - Il segreto client della registrazione in Azure AD del connettore
- **URL di accesso** - L'URL di base per Azure AD. In Azure, si tratta in genere di `https://login.windows.net`.
- **ID tenant** - L'ID del tenant da usare per l'accesso. Deve essere "common" oppure l'ID del tenant in cui viene creato il connettore.
- **URL risorsa** - URL della risorsa della registrazione in Azure AD per l'API

> [!IMPORTANT]
> Se un altro utente importa la definizione dell'API in PowerApps e Microsoft Flow nell'ambito del flusso manuale, è necessario fornire all'utente l'ID client e il segreto client della *registrazione del connettore*, nonché l'URL della risorsa dell'API. Assicurarsi che questi segreti siano gestiti in modo sicuro. **Non condividere le credenziali di sicurezza dell'API stessa.**

### <a name="generic-oauth-20"></a>OAuth 2.0 generica
Quando si usa l'autenticazione OAuth 2.0 generica, è possibile eseguire l'integrazione con qualsiasi provider OAuth 2.0. In questo modo, è possibile usare provider personalizzati che non sono supportati in modo nativo.

Sono richiesti i valori di configurazione seguenti:
- **ID client** - L'ID client OAuth 2.0
- **Segreto client** - Il segreto client OAuth 2.0
- **URL dell'autorizzazione** - L'URL dell'autorizzazione OAuth 2.0
- **URL del token** - L'URL del token OAuth 2.0
- **Aggiorna URL** - L'URL di aggiornamento OAuth 2.0


