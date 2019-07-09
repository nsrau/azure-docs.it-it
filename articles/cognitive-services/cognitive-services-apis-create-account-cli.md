---
title: Creare un account servizi cognitivi tramite la CLI di Azure
titlesuffix: Azure Cognitive Services
description: Come creare un account API servizi cognitivi di Azure tramite la CLI di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: acafc2c42c2946632496b646d001c58d6b48c2a6
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657718"
---
# <a name="create-a-cognitive-services-account-using-the-azure-command-line-interfacecli"></a>Creare un account servizi cognitivi tramite il Interface(CLI) della riga di comando di Azure

In questa Guida introduttiva si apprenderà come effettuare l'iscrizione per servizi cognitivi di Azure e creare un account che dispone di una sottoscrizione di singoli servizi o multiservizio tramite il [Interface(CLI) della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Questi servizi sono rappresentati da Azure [risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), che consentono di connettersi a uno o più delle API servizi cognitivi di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure valida. [Creare un account](https://azure.microsoft.com/free/) gratuitamente.
* Il [Interface(CLI) riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="install-the-azure-cli-and-sign-in"></a>Installare CLI Azure ed eseguire l'accesso 

Installare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Per accedere all'installazione locale della riga di comando, eseguire la [account di accesso di az](https://docs.microsoft.com/cli/azure/reference-index#az-login) comando:

```console
az login
```

È anche possibile usare verde **prova** pulsante per eseguire questi comandi nel browser.
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Creare un nuovo gruppo di risorse di servizi cognitivi di Azure

Le sottoscrizioni a servizi cognitivi sono rappresentate dalle risorse di Azure. Ogni account di Servizi cognitivi (e la risorsa di Azure associata) deve appartenere a un gruppo di risorse di Azure.

### <a name="choose-your-resource-group-location"></a>Scegliere la località del gruppo di risorse

Per creare una risorsa, è necessario uno dei percorsi di Azure disponibili per la sottoscrizione. È possibile recuperare un elenco delle località disponibili con il [percorsi con az account list-](/cli/azure/account#az-account-list-locations) comando. La maggior parte dei servizi cognitivi sono accessibili da diverse posizioni. Scegliere quello più vicino all'utente o visualizzare le posizioni disponibili per il servizio.

> [!IMPORTANT]
> * Ricordare la località di Azure, perché sarà necessaria quando si chiamano i servizi cognitivi di Azure.
> * La disponibilità di alcuni servizi cognitivi può variare ogni area. Per altre informazioni, vedere [prodotti di Azure in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Dopo aver creato la località di azure, creare un nuovo gruppo di risorse nel comando di Azure usando il [creare il gruppo di az](/cli/azure/group#az-group-create) comando.

Nell'esempio seguente, sostituire la località di azure `westus2` con uno dei percorsi di Azure disponibili per la sottoscrizione.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Creare una risorsa per Servizi cognitivi

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Scegliere un servizio cognitivo e il piano tariffario

Quando si crea una nuova risorsa, è necessario conoscere il "tipo" del servizio da usare, insieme con il [tariffario](https://azure.microsoft.com/pricing/details/cognitive-services/) (o dello sku) desiderato. Si userà questa e altre informazioni come parametri durante la creazione della risorsa.

> [!NOTE]
> Molti servizi cognitivi hanno un livello gratuito che è possibile usare per provare il servizio. Per usare il livello gratuito, usare `F0` come sku per la risorsa.

### <a name="vision"></a>Visione

| Service                    | Tipo                      |
|----------------------------|---------------------------|
| Visione artificiale            | `ComputerVision`          |
| Servizio visione artificiale personalizzato - stima | `CustomVision.Prediction` |
| Custom Vision - formazione   | `CustomVision.Training`   |
| API Viso                   | `Face`                    |
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
| Informazioni su forma | `FormUnderstanding` |
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

È possibile trovare un elenco di servizi cognitivi disponibili "tipi" con il [az cognitiveservices account elenco tipi](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds) comando:

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Aggiungere una nuova risorsa al gruppo di risorse

Per creare e sottoscrivere una nuova risorsa di servizi cognitivi, usare il [az cognitiveservices account creare](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) comando. Questo comando aggiunge una nuova risorsa fatturabile al gruppo di risorse creato in precedenza. Quando si crea la nuova risorsa, è necessario conoscere il "tipo" del servizio da usare, insieme ai relativi piano tariffario (o dello sku) e una località di Azure:

È possibile creare una risorsa (gratuita) F0 per rilevatore di anomalie, denominato `anomaly-detector-resource` con il comando seguente.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-subscription"></a>Ottenere le chiavi per la sottoscrizione

Per accedere all'installazione locale dell'Interface(CLI) riga di comando, usare il [account di accesso di az](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) comando.

```console
az login
```

Usare la [az cognitiveservices account elenco chiavi](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) comando per ottenere le chiavi per la risorsa di servizi cognitivi.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Se si desidera pulire e rimuovere una sottoscrizione a servizi cognitivi, è possibile eliminare la risorsa o gruppo di risorse. L'eliminazione del gruppo di risorse elimina anche eventuali altre risorse associate al gruppo di risorse.

Per rimuovere il gruppo di risorse e le risorse associate, incluso il nuovo account di archiviazione, usare il comando di az group delete.

```azurecli-interactive
az group delete --name storage-resource-group
```

## <a name="see-also"></a>Vedere anche

* [Autenticare le richieste a Servizi cognitivi di Azure](authentication.md)
* [Che cos'è servizi cognitivi di Azure?](Welcome.md)
* [Supporto del linguaggio naturale](language-support.md)
* [Supporto per i contenitori docker](cognitive-services-container-support.md)
