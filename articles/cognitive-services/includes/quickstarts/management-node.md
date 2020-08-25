---
title: 'Avvio rapido: Libreria client di gestione di Azure per Node.js'
description: Questo argomento di avvio rapido contiene un'introduzione alla libreria client di gestione di Azure per Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 59063912b2976890976e3c27bc4f2d93b6392e37
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607645"
---
[Documentazione di riferimento](https://docs.microsoft.com/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices) | [Pacchetto (NPM)](https://www.npmjs.com/package/@azure/arm-cognitiveservices) | [Esempi](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices#sample-code)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure valida: [crearne una gratuitamente](https://azure.microsoft.com/free/).
* Versione corrente di [Node.js](https://nodejs.org/)

## <a name="create-an-azure-service-principal"></a>Creare un'entità servizio di Azure

Per fare in modo che l'applicazione interagisca con l'account Azure, è necessaria un'entità servizio di Azure per gestire le autorizzazioni. Seguire le istruzioni riportate in [Creare un'entità servizio di Azure](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0).

Quando si crea un'entità servizio, si noterà che include un valore segreto, un ID e un ID applicazione. Salvare l'ID applicazione e il segreto in un percorso temporaneo per i passaggi successivi.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per creare una risorsa di Servizi cognitivi, è necessario che l'account includa un gruppo di risorse di Azure in cui contenerla. Se non si ha già un gruppo di risorse, crearne uno nel [portale di Azure](https://ms.portal.azure.com/).

## <a name="create-a-new-nodejs-application"></a>Creare una nuova applicazione Node.js

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a una nuova directory per l'app. 

```console
mkdir myapp && cd myapp
```

Eseguire il comando `npm init` per creare un'applicazione Node con un file `package.json`. 

```console
npm init
```

Prima di procedere, creare un file denominato _index.js_.

### <a name="install-the-client-library"></a>Installare la libreria client

Installare i pacchetti NPM seguenti:

```console
npm install @azure/arm-cognitiveservices
npm install @azure/ms-rest-js
npm install @azure/ms-rest-nodeauth
```

Il file `package.json` dell'app viene aggiornato con le dipendenze.

### <a name="import-libraries"></a>Importare le librerie

Aprire lo script _index.js_ e importare le librerie seguenti.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Autenticare il client

Aggiungere i campi seguenti alla radice dello script e inserire i relativi valori usando l'entità servizio creata e le informazioni sull'account Azure.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_constants)]

Aggiungere quindi la funzione `quickstart` seguente per gestire le operazioni principali del programma. Il primo blocco di codice crea un oggetto **CognitiveServicesManagementClient** usando le variabili di credenziali immesse sopra. Questo oggetto è necessario per tutte le operazioni di gestione di Azure.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main_auth)]

## <a name="call-management-functions"></a>Chiamare le funzioni di gestione

Aggiungere il codice seguente alla fine della funzione `quickstart` per elencare le risorse disponibili, creare una risorsa di esempio, elencare le risorse di cui si è proprietari e quindi eliminare la risorsa di esempio. Queste funzioni verranno definite nei passaggi seguenti.

## <a name="create-a-cognitive-services-resource"></a>Creare una risorsa per Servizi cognitivi

### <a name="choose-a-service-and-pricing-tier"></a>Scegliere un servizio e un piano tariffario

Quando si crea una nuova risorsa, è necessario conoscere il tipo di servizio da usare, oltre al [piano tariffario](https://azure.microsoft.com/pricing/details/cognitive-services/) (o SKU) desiderato. Questa e altre informazioni verranno usate come parametri per la creazione della risorsa. La funzione seguente elenca i tipi di Servizi cognitivi disponibili.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

Vedere l'elenco degli SKU e le informazioni sui prezzi di seguito. 

#### <a name="multi-service"></a>Multi-servizio

| Servizio                    | Tipo                      |
|----------------------------|---------------------------|
| Più servizi. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/).            | `CognitiveServices`     |


#### <a name="vision"></a>Visione

| Servizio                    | Tipo                      |
|----------------------------|---------------------------|
| Visione artificiale            | `ComputerVision`          |
| Visione personalizzata - Previsione | `CustomVision.Prediction` |
| Visione personalizzata - Training   | `CustomVision.Training`   |
| Viso                       | `Face`                    |
| Riconoscimento modulo            | `FormRecognizer`          |
| Riconoscimento input penna             | `InkRecognizer`           |

#### <a name="search"></a>Ricerca

| Servizio            | Tipo                  |
|--------------------|-----------------------|
| Suggerimenti automatici Bing   | `Bing.Autosuggest.v7` |
| Ricerca personalizzata Bing | `Bing.CustomSearch`   |
| Ricerca entità Bing | `Bing.EntitySearch`   |
| Ricerca Bing        | `Bing.Search.v7`      |
| Controllo ortografico Bing   | `Bing.SpellCheck.v7`  |

#### <a name="speech"></a>Voce

| Servizio            | Tipo                 |
|--------------------|----------------------|
| Servizi Voce    | `SpeechServices`     |
| Riconoscimento vocale | `SpeakerRecognition` |

#### <a name="language"></a>Linguaggio

| Servizio            | Tipo                |
|--------------------|---------------------|
| Comprensione modulo | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Text Analytics     | `TextAnalytics`     |
| Traduzione testuale   | `TextTranslation`   |

#### <a name="decision"></a>Decisione

| Servizio           | Tipo               |
|-------------------|--------------------|
| Rilevamento anomalie  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizza esperienze      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>Piani tariffari e fatturazione

I piani tariffari (e l'importo fatturato) si basano sul numero di transazioni inviate usando le informazioni di autenticazione. Ogni piano tariffario specifica:
* Il numero massimo di transazioni consentite al secondo.
* Le funzionalità del servizio abilitate all'interno del piano tariffario.
* Il costo per un numero predefinito di transazioni. Il superamento di questo numero determinerà un addebito aggiuntivo, come specificato nei [dettagli dei prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) per il servizio.

> [!NOTE]
> Molti servizi cognitivi prevedono un livello gratuito che è possibile usare per provarli. Per usufruire del livello gratuito, usare `F0` come SKU per la risorsa.

## <a name="create-a-cognitive-services-resource"></a>Creare una risorsa per Servizi cognitivi

Per creare e sottoscrivere una nuova risorsa di Servizi cognitivi, usare la funzione **Crea**. Questa funzione aggiunge una nuova risorsa fatturabile al gruppo di risorse che viene passato. Quando si crea una nuova risorsa, è necessario conoscere il tipo di servizio da usare, oltre al piano tariffario (o SKU) e a una località di Azure. La funzione seguente accetta tutti questi argomenti e crea una risorsa.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_create)]

## <a name="view-your-resources"></a>Visualizzare le risorse

Per visualizzare tutte le risorse nell'account Azure (in tutti i gruppi di risorse), usare la funzione seguente:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list)]

## <a name="delete-a-resource"></a>Eliminare una risorsa

La funzione seguente elimina la risorsa specificata dal gruppo di risorse specificato.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_delete)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Aggiungere il codice seguente alla fine dello script per chiamare la funzione `quickstart` principale con la gestione degli errori.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main)]

Quindi, nella finestra della console, eseguire l'applicazione con il comando `node`.

```console
node index.js
```

## <a name="see-also"></a>Vedere anche

* [Documentazione di riferimento Azure Management SDK](https://docs.microsoft.com/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest)
* [Informazioni su Servizi cognitivi di Azure](../../Welcome.md)
* [Autenticare le richieste a Servizi cognitivi di Azure](../../authentication.md)
* [Creare una nuova risorsa con il portale di Azure](../../cognitive-services-apis-create-account.md)