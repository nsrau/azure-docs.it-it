---
title: Creare una risorsa di Servizi cognitivi usando l'interfaccia della riga di comando di AzureCreate a Cognitive Services resource using the Azure CLI
titleSuffix: Azure Cognitive Services
description: Iniziare a usare Servizi cognitivi di Azure creando e sottoscrivendo una risorsa usando l'interfaccia della riga di comando di Azure.Get started with Azure Cognitive Services by creating and subscribing to a resource using the Azure command line interface.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: aahi
ms.openlocfilehash: 72b00d78d19ed0e963b4dad01b82033c659e1efd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219610"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Creare una risorsa di Servizi cognitivi usando l'interfaccia della riga di comando di AzureCreate a Cognitive Services resource using the Azure Command-Line Interface (CLI)

Usare questa guida introduttiva per iniziare a usare Servizi cognitivi di Azure usando l'interfaccia della riga di [comando (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)di Azure. I servizi cognitivi sono rappresentati dalle risorse di Azure create nella sottoscrizione di Azure.Cognitive Services are represented by Azure [resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) that you create in your Azure subscription. Dopo aver creato la risorsa, usare le chiavi e l'endpoint generati per l'autenticazione delle applicazioni. 


In questa guida introduttiva verrà illustrato come iscriversi a Servizi cognitivi di Azure e creare un account con una sottoscrizione a servizio singolo o multiservizio, usando l'interfaccia della riga di comando [di Azure.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Questi servizi sono rappresentati dalle risorse di Azure, che consentono di connettersi a una o più API di Servizi cognitivi di Azure.These services are represented by Azure [resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), which enable you to connect to one or more of the Azure Cognitive Services APIs.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione valida di Azure - [Creane una](https://azure.microsoft.com/free/) gratuitamente.
* Interfaccia della riga di comando di [AzureThe Azure Command Line Interface (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="install-the-azure-cli-and-sign-in"></a>Installare l'interfaccia della riga di comando di Azure ed eseguire l'accesso 

Installare [l'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)di Azure. Per accedere all'installazione locale dell'interfaccia della riga di comando, eseguire il comando [az login:](https://docs.microsoft.com/cli/azure/reference-index#az-login)

```azurecli-interactive
az login
```

È inoltre possibile utilizzare il pulsante verde **Prova per** eseguire questi comandi nel browser.
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Creare un nuovo gruppo di risorse Servizi cognitivi di AzureCreate a new Azure Cognitive Services resource group

Prima di creare una risorsa Servizi cognitivi, è necessario disporre di un gruppo di risorse di Azure per contenere la risorsa. Quando si crea una nuova risorsa, è possibile creare un nuovo gruppo di risorse o usarne uno esistente. Questo articolo illustra come creare un nuovo gruppo di risorse.

### <a name="choose-your-resource-group-location"></a>Scegliere la posizione del gruppo di risorseChoose your resource group location

Per creare una risorsa, è necessario uno dei percorsi di Azure disponibili per la sottoscrizione. È possibile recuperare un elenco di posizioni disponibili con il comando [az account list-locations.](/cli/azure/account#az-account-list-locations) È possibile accedere alla maggior parte dei servizi cognitivi da diverse posizioni. Scegli quello più vicino a te o vedi quali località sono disponibili per il servizio.

> [!IMPORTANT]
> * Ricordare la posizione di Azure, poiché sarà necessario quando si chiamano i servizi cognitivi di Azure.Remember your Azure location, as you will need it when calling the Azure Cognitive Services.
> * La disponibilità di alcuni servizi cognitivi può variare in base all'area geografica. Per altre informazioni, vedere [Prodotti di Azure per area](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Dopo aver creato il percorso di azure, creare un nuovo gruppo di risorse nell'interfaccia della riga di comando di Azure usando il comando [az group create.](/cli/azure/group#az-group-create)

Nell'esempio seguente sostituire il `westus2` percorso di azure con uno dei percorsi di Azure disponibili per la sottoscrizione.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Creare una risorsa per Servizi cognitivi

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Scegliere un servizio cognitivo e un piano tariffario

Quando si crea una nuova risorsa, è necessario conoscere il "tipo" di servizio che si desidera utilizzare, insieme al [piano tariffario](https://azure.microsoft.com/pricing/details/cognitive-services/) (o sku) desiderato. Queste e altre informazioni verranno utilizzate come parametri durante la creazione della risorsa.

### <a name="multi-service"></a>Multi-servizio

| Service                    | Tipo                      |
|----------------------------|---------------------------|
| Più servizi. Vedere la pagina [dei prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/) per ulteriori dettagli.            | `CognitiveServices`     |


> [!NOTE]
> Molti dei servizi cognitivi seguenti dispongono di un livello gratuito che è possibile utilizzare per provare il servizio. Per usare il livello `F0` gratuito, usare come sku per la risorsa.

### <a name="vision"></a>Visione

| Service                    | Tipo                      |
|----------------------------|---------------------------|
| Visione artificiale            | `ComputerVision`          |
| Visione personalizzata - Previsione | `CustomVision.Prediction` |
| Visione personalizzata - Formazione   | `CustomVision.Training`   |
| Viso                       | `Face`                    |
| Riconoscimento modulo            | `FormRecognizer`          |
| Riconoscimento input penna             | `InkRecognizer`           |

### <a name="search"></a>Ricerca

| Service            | Tipo                  |
|--------------------|-----------------------|
| Suggerimenti automatici Bing   | `Bing.Autosuggest.v7` |
| Ricerca personalizzata Bing | `Bing.CustomSearch`   |
| Ricerca entità Bing | `Bing.EntitySearch`   |
| Ricerca Bing        | `Bing.Search.v7`      |
| Controllo ortografico Bing   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>Sintesi vocale

| Service            | Tipo                 |
|--------------------|----------------------|
| Servizi Voce    | `SpeechServices`     |
| Riconoscimento vocale | `SpeakerRecognition` |

### <a name="language"></a>Linguaggio

| Service            | Tipo                |
|--------------------|---------------------|
| Comprensione del modulo | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Text Analytics     | `TextAnalytics`     |
| Traduzione testuale   | `TextTranslation`   |

### <a name="decision"></a>Decisione

| Service           | Tipo               |
|-------------------|--------------------|
| Rilevamento anomalie  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizza esperienze      | `Personalizer`     |

È possibile trovare un elenco di "tipi" di servizio cognitivo disponibili con il comando [az cognitiveservices account list-tipi:](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds)

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Aggiungere una nuova risorsa al gruppo di risorseAdd a new resource to your resource group

Per creare e sottoscrivere una nuova risorsa di Servizi cognitivi, usare il comando [az cognitiveservices account create.](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) Questo comando aggiunge una nuova risorsa fatturabile al gruppo di risorse creato in precedenza. Quando si crea la nuova risorsa, è necessario conoscere il "tipo" di servizio che si vuole usare, insieme al piano tariffario (o sku) e a una posizione di Azure:When creating your new resource, you will need to know the "kind" of service you want to use, along with its pricing tier (or sku) and an Azure location:

È possibile creare una risorsa F0 (libera) per `anomaly-detector-resource` Rilevatore di anomalie, denominata con il comando seguente.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-resource"></a>Ottenere le chiavi per la risorsaGet the keys for your resource

Per accedere all'installazione locale dell'interfaccia della riga di comando (CLI), utilizzare il comando [az login.](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login)

```azurecli-interactive
az login
```

Usare il comando elenco chiavi account [az cognitiveservices](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) per ottenere le chiavi per la risorsa del servizio cognitivo.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Piani tariffari e fatturazione

I livelli dei prezzi (e l'importo fatturato) si basano sul numero di transazioni inviate utilizzando le informazioni di autenticazione. Ogni piano tariffario specifica:
* numero massimo di transazioni consentite al secondo (TPS).
* funzionalità di servizio abilitate all'interno del piano tariffario.
* Costo per un importo predefinito di transazioni. Superando questo importo, si è verificato un costo aggiuntivo, come specificato nei [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) per il servizio.

## <a name="get-current-quota-usage-for-your-resource"></a>Ottenere l'utilizzo corrente della quota per la risorsaGet current quota usage for your resource

Usare il comando [az cognitiveservices account list-usage](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-usage) per ottenere l'utilizzo della risorsa del servizio cognitivo.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una risorsa di Servizi cognitivi, è possibile eliminarla o il gruppo di risorse. L'eliminazione del gruppo di risorse comporta anche l'eliminazione di tutte le altre risorse contenute nel gruppo.

Per rimuovere il gruppo di risorse e le risorse associate, usare il comando az group delete.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>Vedere anche

* [Autenticare le richieste a Servizi cognitivi di Azure](authentication.md)
* [Che cos'è Servizi cognitivi di Azure?](Welcome.md)
* [Supporto del linguaggio naturale](language-support.md)
* [Supporto per contenitori Docker](cognitive-services-container-support.md)
