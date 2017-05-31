---
title: Esportazione di un&quot;API ospitata in Azure in PowerApps e Microsoft Flow | Microsoft Docs
description: Panoramica su come esporre un&quot;API ospitata nel servizio app in PowerApps e Microsoft Flow
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
ms.date: 02/06/2017
ms.author: mahender
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 6a6043de57eb211c93c510cf2a139141c3950662
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


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

Le API sono spesso descritte tramite un [documento Open API](https://www.openapis.org/), talvolta definito documento "Swagger". Questo file contiene tutte le informazioni sulle operazioni disponibili e su come strutturare i dati. PowerApps e Microsoft Flow possono creare connettori personalizzati per qualsiasi documento Open API 2.0. Dopo aver creato un connettore personalizzato, è possibile usarlo allo stesso modo dei connettori predefiniti ed è rapidamente integrabile in un'applicazione.

Il Servizio app di Azure e Funzioni di Azure dispongono di [supporto predefinito](https://docs.microsoft.com/azure/app-service-api/app-service-api-metadata) per la creazione, l'hosting e la gestione di un documento Open API. Per creare un connettore personalizzato per un'app web, API, per le funzioni o per dispositivi mobili, sono necessari due passaggi:

1. [Recuperare la definizione dell'API dal servizio app o da Funzioni di Azure](#export)
2. [Importare la definizione dell'API in PowerApps](#import)

È possibile che questi due passaggi debbano essere eseguiti da due utenti diversi in un'organizzazione, in quanto un dato utente potrebbe non avere le autorizzazioni per eseguirli entrambi. In questo caso, uno sviluppatore con accesso di collaboratore all'applicazione del servizio app o a Funzioni di Azure dovrà ottenere la definizione dell'API (un singolo file JSON) o un collegamento a essa. Dovrà quindi fornire tale definizione a un proprietario di PowerApps o Microsoft flow. Tale proprietario può usare i metadati per creare il connettore personalizzato.

> [!NOTE]
> Poiché si sta usando una copia della definizione dell'API, PowerApps e Microsoft Flow non rileveranno subito eventuali aggiornamenti o modifiche di rilievo all'applicazione. Se viene resa disponibile una nuova versione dell'API, sarà necessario ripetere questi passaggi per la nuova versione. 

<a name="export"></a>
## <a name="retrieving-the-api-definition-from-app-service-or-azure-functions"></a>Recuperare la definizione dell'API dal servizio app o da Funzioni di Azure

In questa sezione, si esporterà la definizione dell'API per l'API del servizio app, per usarla più avanti in PowerApps.

1. Aprire il [portale di Azure](https://portal.azure.com) e passare all'applicazione di servizio App o Funzioni di Azure.

    Se si usa il Servizio app di Azure, selezionare **Definizione dell'API** dall'elenco delle impostazioni. 
    
    Se si usa Funzioni di Azure, selezionare **Impostazioni dell'app per le funzioni** e quindi **Configura metadati dell'API**.

2. Se è stata specificata una definizione dell'API, verrà visualizzato il pulsante **Export to PowerApps + Microsoft Flow** (Esporta in PowerApps + Microsoft Flow). Fare clic su questo pulsante per avviare il processo di esportazione.

3. È possibile scegliere se **scaricare la definizione dell'API** o **utilizzare un collegamento**. A prescindere dalla scelta, il risultato verrà fornito nella sezione successiva. Selezionare una delle opzioni e seguire le istruzioni.
 
4. Se la definizione dell'API include definizioni di sicurezza, queste verranno indicate nel passaggio 2. Durante l'importazione, PowerApps e Microsoft Flow le rileveranno e richiederanno informazioni sulla sicurezza. I servizi le usano per consentire l'accesso all'API da parte degli utenti. Se l'API richiede l'autenticazione, assicurarsi che venga acquisita come _definizione di sicurezza_ nel documento Open API.

    Raccogliere le credenziali relative a ogni definizione per l'utilizzo nella sezione successiva.
 
> [!NOTE]
> Se si usa l'autenticazione di Azure Active Directory, sarà necessaria una nuova registrazione dell'app AAD con accesso delegato all'API e un URL di risposta di _https://msmanaged-na.consent.azure-apim.net/redirect_. Vedere [questo esempio](
https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) per maggiori dettagli, sostituendo l'API con Azure Resource Manager.
>
> Se sarà un altro utente a importare la definizione dell'API in PowerApps, si fornirà l'ID client e il segreto del client **della nuova registrazione** oltre all'URL di risorsa e al file di definizione dell'API. Assicurarsi che questi segreti siano gestiti in modo sicuro. **Non condividere le credenziali di sicurezza dell'API stessa.**

<a name="import"></a>
## <a name="importing-the-api-definition-into-powerapps-and-microsoft-flow"></a>Importare la definizione dell'API in PowerApps e Microsoft Flow

In questa sezione si creerà un connettore personalizzato in PowerApps e Microsoft Flow usando la definizione dell'API ottenuta in precedenza. Poiché i connettori personalizzati sono condivisi tra i due servizi, è necessario importare la definizione una sola volta. Per altre informazioni sui connettori personalizzati, vedere [Registrare e usare connettori personalizzati in PowerApps] e [Registrare e usare i connettori personalizzati in Microsoft Flow].

**Per eseguire l'importazione in PowerApps o Microsoft Flow:**

1. Aprire il [portale Web di PowerApps](https://web.powerapps.com) o il [portale Web di Microsoft Flowl](https://flow.microsoft.com/) ed eseguire l'accesso. 

2. Fare clic sul pulsante **Impostazioni** (icona a forma di ingranaggio) nella parte superiore destra della pagina e selezionare **Custom connectors** (Connettori personalizzati). 

3. Fare clic su **Create custom connector** (Crea connettore personalizzato).

4. Nella scheda **Generale** specificare un nome per l'API, quindi caricare la definizione OpenAPI o incollare l'URL dei metadati. Fare clic su **Continue**.

4. Nella scheda **Sicurezza**, se viene chiesto di fornire i dettagli di autenticazione, immettere i valori ottenuti nella sezione precedente. In caso contrario, procedere con il passaggio successivo.

5. Nella scheda **Definizioni** tutte le operazioni definite nel file OpenAPI vengono popolate automaticamente. Se sono state definite tutte le operazioni necessarie, si può procedere al passaggio successivo. In caso contrario, è possibile aggiungere e modificare qui le operazioni.

6. Fare clic su **Crea connettore**. Se si desidera testare le chiamate API, andare al passaggio successivo.

7. Nella scheda **Test** creare una connessione, selezionare un'operazione da testare e immettere i dati richiesti dall'operazione.

8. Fare clic su **Verifica operazione**.



[Registrare e usare connettori personalizzati in PowerApps]: https://powerapps.microsoft.com/tutorials/register-custom-api/
[Registrare e usare i connettori personalizzati in Microsoft Flow]: https://flow.microsoft.com/documentation/register-custom-api/

