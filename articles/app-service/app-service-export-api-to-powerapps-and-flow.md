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
ms.date: 06/20/2017
ms.author: mahender
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 0d166a2e5b60c3b9f911f9fc3ad49ad7f252abb4
ms.contentlocale: it-it
ms.lasthandoff: 07/01/2017


---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Esportazione di un'API ospitata in Azure in PowerApps e Microsoft Flow

## <a name="creating-custom-connectors-for-powerapps-and-microsoft-flow"></a>Creare connettori personalizzati per PowerApps e Microsoft Flow

[PowerApps](https://powerapps.com) è un servizio per la creazione e l'uso di applicazioni aziendali personalizzate in grado di connettersi ai dati dell'utente e di funzionare su più piattaforme. [Microsoft Flow](https://flow.microsoft.com) facilita l'automazione dei flussi di lavoro e dei processi aziendali tra le app e i servizi preferiti dagli utenti. Sia PowerApps che Microsoft Flow dispongono di un'ampia varietà di connettori predefiniti a origini di dati come Office 365, Dynamics 365, Salesforce e così via. Tuttavia, gli utenti devono inoltre essere in grado di sfruttare le origini dati e le API create dall'organizzazione.

Analogamente, gli sviluppatori che desiderano esporre ulteriormente le proprie API nell'organizzazione potrebbero volerle rendere disponibili agli utenti di PowerApps e Microsoft Flow. Questo argomento illustrerà come esporre un'API creata con il Servizio app di Azure o le Funzioni di Azure in PowerApps e Microsoft Flow. Il [Servizio app di Azure](https://azure.microsoft.com/services/app-service/) è un'offerta di piattaforma distribuita come servizio (PaaS - Platform as a Service) che consente agli sviluppatori di creare applicazioni Web, API e per dispositivi mobili di livello aziendale, con rapidità e semplicità. [Funzioni di Azure](https://azure.microsoft.com/services/functions/) è una soluzione di calcolo senza server basata su eventi che consente di scrivere rapidamente codice in grado di reagire ad altre parti del sistema e di ridimensionarsi in base alla domanda.

Per altre informazioni su questo servizio, vedere:
- [Formazione guidata di PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) 
- [Formazione guidata di Microsoft Flow](https://flow.microsoft.com/guided-learning/learning-introducing-flow/)
- [Informazioni sul servizio app](https://docs.microsoft.com/azure/app-service/app-service-value-prop-what-is)
- [Che cos'è Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview)

## <a name="sharing-an-api-definition"></a>Condividere la definizione di un'API

Le API sono spesso descritte tramite un [documento OpenAPI](https://www.openapis.org/), talvolta definito documento "Swagger". Questo file contiene tutte le informazioni sulle operazioni disponibili e su come strutturare i dati. PowerApps e Microsoft Flow possono creare connettori personalizzati per qualsiasi documento OpenAPI 2.0. Dopo aver creato un connettore personalizzato, è possibile usarlo allo stesso modo dei connettori predefiniti ed è rapidamente integrabile in un'applicazione.

Il Servizio app di Azure e Funzioni di Azure dispongono di [supporto integrato](https://docs.microsoft.com/azure/app-service-api/app-service-api-metadata) per la creazione, l'hosting e la gestione di un documento OpenAPI. Per creare un connettore personalizzato per un'App Web, un'app per dispositivi mobili, per API o un'app per le funzioni, PowerApps e Flow devono ricevere una copia della definizione.

> [!NOTE]
> Poiché si sta usando una copia della definizione dell'API, PowerApps e Microsoft Flow non rileveranno subito eventuali aggiornamenti o modifiche di rilievo all'applicazione. Se viene resa disponibile una nuova versione dell'API, sarà necessario ripetere questi passaggi per la nuova versione. 

Per dotare PowerApps e Microsoft Flow della definizione dell'API ospitata, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com) e passare all'applicazione di servizio App o Funzioni di Azure.

    Se si usa il Servizio app di Azure, selezionare **Definizione dell'API** dall'elenco delle impostazioni. 
    
    Se si usa Funzioni di Azure, selezionare l'app per le funzioni interessata, scegliere **Funzionalità della piattaforma** e quindi **Definizione API**. In alternativa, è anche possibile scegliere di aprire la scheda **Definizione API (anteprima)**.

2. Se è stata specificata una definizione dell'API, verrà visualizzato il pulsante **Export to PowerApps + Microsoft Flow** (Esporta in PowerApps + Microsoft Flow). Fare clic su questo pulsante per avviare il processo di esportazione.

3. Selezionare la **modalità di esportazione**. Questa opzione determina la procedura che dovrà essere seguita per creare un connettore. Il servizio app offre due opzioni per fornire la definizione dell'API a PowerApps e Microsoft Flow:

    **Rapida**: consente di creare il connettore personalizzato dall'interno del portale di Azure. L'utente connesso deve necessariamente disporre dell'autorizzazione per la creazione di connettori nell'ambiente di destinazione. Questo è l'approccio consigliato se tale requisito può essere soddisfatto. Se si usa questa modalità, seguire le istruzioni per l'[esportazione rapida](#express) riportate di seguito.

    **Manuale**: consente di esportare una copia della definizione dell'API che può essere importata tramite il portale di PowerApps o di Microsoft Flow. Questo approccio è consigliato se l'utente di Azure e l'utente con l'autorizzazione per la creazione di connettori sono diversi o se il connettore deve essere creato in un altro tenant. Se si usa questa modalità, seguire le istruzioni per l'[importazione e l'esportazione manuali](#manual) riportate di seguito.

<a name="express"></a>
## <a name="express-export"></a>Esportazione rapida

In questa sezione si crea un nuovo connettore personalizzato dall'interno del portale di Azure. È necessario essere connessi al tenant verso cui si desidera esportare ed è necessario disporre dell'autorizzazione per creare un connettore personalizzato nell'ambiente di destinazione.

1. Selezionare l'ambiente in cui si vuole creare il connettore. Specificare quindi un nome per il connettore personalizzato.

2. Se la definizione dell'API include definizioni di sicurezza, queste verranno indicate nel passaggio 2. Se necessario, specificare i dettagli di configurazione di sicurezza necessari per concedere ad altri utenti l'accesso all'API. Per altre informazioni, vedere [Autenticazione](#auth) di seguito. 

3. Fare clic su **OK** per creare il connettore personalizzato.


<a name="manual"></a>
## <a name="manual-export-and-import"></a>Esportazione e importazione manuali

Per creare un connettore personalizzato per un'app web, API, per le funzioni o per dispositivi mobili, sono necessari due passaggi:

1. [Recuperare la definizione dell'API dal servizio app o da Funzioni di Azure](#export)
2. [Importare la definizione dell'API in PowerApps e Microsoft Flow](#import)

È possibile che questi due passaggi debbano essere eseguiti da due utenti diversi in un'organizzazione, in quanto un dato utente potrebbe non avere le autorizzazioni per eseguirli entrambi. In questo caso, uno sviluppatore con accesso di collaboratore all'applicazione del servizio app o a Funzioni di Azure dovrà ottenere la definizione dell'API (un singolo file JSON) o un collegamento a essa. Dovrà quindi fornire tale definizione a un proprietario di PowerApps o Microsoft flow. Tale proprietario può usare i metadati per creare il connettore personalizzato.

<a name="export"></a>
### <a name="retrieving-the-api-definition-from-app-service-or-azure-functions"></a>Recuperare la definizione dell'API dal servizio app o da Funzioni di Azure

In questa sezione si esporterà la definizione dell'API per l'API del servizio app, per usarla in un secondo momento nel portale di PowerApps o di Microsoft Flow.

1. È possibile scegliere se **scaricare la definizione dell'API** o **utilizzare un collegamento**. A prescindere dalla scelta, il risultato verrà fornito nella sezione successiva. Selezionare una delle opzioni e seguire le istruzioni.
 
2. Se la definizione dell'API include definizioni di sicurezza, queste verranno indicate nel passaggio 2. Durante l'importazione, PowerApps e Microsoft Flow le rileveranno e richiederanno informazioni sulla sicurezza. Raccogliere le credenziali relative a ogni definizione per l'utilizzo nella sezione successiva. Per altre informazioni, vedere [Autenticazione](#auth) di seguito. 

<a name="import"></a>
### <a name="importing-the-api-definition-into-powerapps-and-microsoft-flow"></a>Importare la definizione dell'API in PowerApps e Microsoft Flow

In questa sezione si creerà un connettore personalizzato in PowerApps e Microsoft Flow usando la definizione dell'API ottenuta in precedenza. Poiché i connettori personalizzati sono condivisi tra i due servizi, è necessario importare la definizione una sola volta. Per altre informazioni sui connettori personalizzati, vedere [Registrare e usare connettori personalizzati in PowerApps] e [Registrare e usare connettori personalizzati in Microsoft Flow].

1. Aprire il [portale Web di PowerApps](https://web.powerapps.com) o il [portale Web di Microsoft Flowl](https://flow.microsoft.com/) ed eseguire l'accesso. 

2. Fare clic sul pulsante **Impostazioni** (icona a forma di ingranaggio) nella parte superiore destra della pagina e selezionare **Custom connectors** (Connettori personalizzati). 

3. Fare clic su **Create custom connector** (Crea connettore personalizzato).

4. Nella scheda **Generale** specificare un nome per l'API, quindi caricare la definizione OpenAPI o incollare l'URL dei metadati. Fare clic su **Continue**.

4. Nella scheda **Sicurezza**, se viene chiesto di fornire i dettagli di autenticazione, immettere i valori ottenuti nella sezione precedente. In caso contrario, procedere con il passaggio successivo.

5. Nella scheda **Definizioni** tutte le operazioni definite nel file OpenAPI vengono popolate automaticamente. Se sono state definite tutte le operazioni necessarie, si può procedere al passaggio successivo. In caso contrario, è possibile aggiungere e modificare qui le operazioni.

6. Fare clic su **Crea connettore**. Se si desidera testare le chiamate API, andare al passaggio successivo.

7. Nella scheda **Test** creare una connessione, selezionare un'operazione da testare e immettere i dati richiesti dall'operazione.

8. Fare clic su **Verifica operazione**.


<a name="auth"></a>
## <a name="authentication"></a>Autenticazione

PowerApps e Microsoft Flow supportano in modo nativo una raccolta di provider di identità che può essere usata per l'accesso degli utenti del connettore personalizzato. Se l'API richiede l'autenticazione, assicurarsi che venga acquisita come _definizione di sicurezza_ nel documento OpenAPI. Durante l'esportazione è necessario specificare valori di configurazione che consentano a PowerApps e Microsoft Flow di eseguire azioni di accesso.

Questa sezione descrive i tipi di autenticazione che sono supportati dal flusso rapido: chiave API, Azure Active Directory e OAuth 2.0 generica. Per un elenco completo dei provider e delle credenziali richieste da ognuno, vedere [Registrare e usare connettori personalizzati in PowerApps] e [Registrare e usare connettori personalizzati in Microsoft Flow].

### <a name="api-key"></a>Chiave API
Quando si usa questo schema di sicurezza, per creare una connessione gli utenti del connettore devono specificare la chiave. È possibile indicare loro il nome di chiave API da usare. Per Funzioni di Azure la chiave è in genere una delle chiavi host, che copre varie funzioni all'interno dell'app per le funzioni.

### <a name="azure-active-directory"></a>Azure Active Directory
Quando si configura un connettore personalizzato che richiede l'accesso ad AAD, sono necessarie due registrazioni dell'applicazione AAD: una per modellare le API di back-end e una per modellare il connettore in PowerApps e Flow.

È consigliabile configurare l'API in modo da usarla con la prima registrazione e questo aspetto sarà già configurato se è stata usata la funzione [Autenticazione/Autorizzazione servizio app](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication).

Sarà necessario creare manualmente la seconda registrazione per il connettore, usando la procedura riportata in [Aggiunta di un'applicazione in AAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application). La registrazione deve disporre dell'accesso delegato all'API e di un URL di risposta di `https://msmanaged-na.consent.azure-apim.net/redirect`. Per altre informazioni, vedere [questo esempio](
https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) per la sostituzione dell'API per Azure Resource Manager.

Sono richiesti i valori di configurazione seguenti:
- **ID client** - L'ID client della registrazione in AAD del connettore
- **Segreto client** - Il segreto client della registrazione in AAD del connettore
- **URL di accesso** - URL di base per AAD. In Azure pubblico questo URL è in genere `https://login.windows.net`.
- **ID tenant** - L'ID del tenant da usare per l'accesso. Deve essere "comune" oppure l'ID del tenant in cui viene creato il connettore.
- **URL risorsa** - L'URL della risorsa della registrazione in AAD di back-end dell'API

> [!IMPORTANT]
> Se un altro utente intende importare la definizione dell'API in PowerApps e Microsoft Flow nell'ambito del flusso manuale, è necessario specificare all'utente l'ID client e il segreto client **della registrazione del connettore**, nonché l'URL di risorsa dell'API. Assicurarsi che questi segreti siano gestiti in modo sicuro. **Non condividere le credenziali di sicurezza dell'API stessa.**

### <a name="generic-oauth-20"></a>OAuth 2.0 generica
Il supporto di OAuth 2.0 generica consente l'integrazione con qualsiasi provider OAuth 2.0. Ciò consente di usare qualsiasi provider personalizzato che non è supportato in modo nativo.

Sono richiesti i valori di configurazione seguenti:
- **ID client** - L'ID client OAuth 2.0
- **Segreto client** - Il segreto client OAuth 2.0
- **URL dell'autorizzazione** - L'URL dell'autorizzazione OAuth 2.0
- **URL del token** - L'URL del token OAuth 2.0
- **Aggiorna URL** - L'URL di aggiornamento OAuth 2.0



[Registrare e usare connettori personalizzati in PowerApps]: https://powerapps.microsoft.com/tutorials/register-custom-api/
[Registrare e usare connettori personalizzati in Microsoft Flow]: https://flow.microsoft.com/documentation/register-custom-api/

