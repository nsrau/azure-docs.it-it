---
title: Creare una risorsa di servizi cognitivi usando l'interfaccia della riga di comando di Azure
titleSuffix: Azure Cognitive Services
description: Per iniziare a usare servizi cognitivi di Azure, è possibile creare e sottoscrivere una risorsa tramite l'interfaccia della riga di comando di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: aahi
ms.openlocfilehash: e57bb55f5bbeba9cb476672723511a4d19c46165
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383265"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Creare una risorsa di servizi cognitivi usando l'interfaccia della riga di comando di Azure

Usare questa Guida introduttiva per iniziare a usare servizi cognitivi di Azure tramite l' [interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). I servizi cognitivi sono rappresentati da [risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) di Azure create nella sottoscrizione di Azure. Dopo aver creato la risorsa, usare le chiavi e l'endpoint generati per autenticare le applicazioni. 


In questa Guida introduttiva si apprenderà come iscriversi ai servizi cognitivi di Azure e creare un account con una sottoscrizione a servizio singolo o multiservizio usando l' [interfaccia della riga di comando (CLI) di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Questi servizi sono rappresentati da [risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)di Azure, che consentono di connettersi a una o più API servizi cognitivi di Azure.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure valida- [crearne una](https://azure.microsoft.com/free/) gratuita.
* [Interfaccia della riga di comando di Azure (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="install-the-azure-cli-and-sign-in"></a>Installare l'interfaccia della riga di comando di Azure e accedere 

Installare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Per accedere all'installazione locale dell'interfaccia della riga di comando, eseguire il comando [AZ login](https://docs.microsoft.com/cli/azure/reference-index#az-login) :

```console
az login
```

È anche possibile usare il pulsante **prova** in verde per eseguire questi comandi nel browser.
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Creare un nuovo gruppo di risorse servizi cognitivi di Azure

Prima di creare una risorsa Servizi cognitivi, è necessario disporre di un gruppo di risorse di Azure per contenere la risorsa. Quando si crea una nuova risorsa, è possibile scegliere di creare un nuovo gruppo di risorse o utilizzarne uno esistente. Questo articolo illustra come creare un nuovo gruppo di risorse.

### <a name="choose-your-resource-group-location"></a>Scegliere il percorso del gruppo di risorse

Per creare una risorsa, è necessario disporre di una delle località di Azure disponibili per la sottoscrizione. È possibile recuperare un elenco di percorsi disponibili con il comando [AZ account list-locations](/cli/azure/account#az-account-list-locations) . È possibile accedere alla maggior parte dei servizi cognitivi da diverse posizioni. Scegliere quella più vicina o vedere quali sono le località disponibili per il servizio.

> [!IMPORTANT]
> * Ricordare la località di Azure, perché sarà necessaria quando si chiamano i servizi cognitivi di Azure.
> * La disponibilità di alcuni servizi cognitivi può variare in base all'area. Per altre informazioni, vedere [prodotti di Azure in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Dopo aver creato la località di Azure, creare un nuovo gruppo di risorse nell'interfaccia della riga di comando di Azure usando il comando [AZ Group create](/cli/azure/group#az-group-create) .

Nell'esempio seguente sostituire la località `westus2` di Azure con una delle località di Azure disponibili per la sottoscrizione.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Creare una risorsa per Servizi cognitivi

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Scegliere un servizio cognitivo e un piano tariffario

Quando si crea una nuova risorsa, è necessario conoscerne il tipo di servizio che si vuole usare, oltre al [piano tariffario](https://azure.microsoft.com/pricing/details/cognitive-services/) (o SKU) desiderato. Questa e altre informazioni vengono usate come parametri durante la creazione della risorsa.

> [!NOTE]
> Molti servizi cognitivi hanno un livello gratuito che è possibile usare per provare il servizio. Per usare il livello gratuito, usare `F0` come SKU per la risorsa.

### <a name="vision"></a>Visione

| Service                    | Kind                      |
|----------------------------|---------------------------|
| Visione artificiale            | `ComputerVision`          |
| Visione personalizzata-stima | `CustomVision.Prediction` |
| Training di Visione personalizzata   | `CustomVision.Training`   |
| API Viso                   | `Face`                    |
| Riconoscimento modulo            | `FormRecognizer`          |
| Riconoscimento input penna             | `InkRecognizer`           |

### <a name="search"></a>Cerca

| Service            | Kind                  |
|--------------------|-----------------------|
| Suggerimenti automatici Bing   | `Bing.Autosuggest.v7` |
| Ricerca personalizzata Bing | `Bing.CustomSearch`   |
| Ricerca entità Bing | `Bing.EntitySearch`   |
| Ricerca Bing        | `Bing.Search.v7`      |
| Controllo ortografico Bing   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>Riconoscimento vocale

| Service            | Kind                 |
|--------------------|----------------------|
| Servizi Voce    | `SpeechServices`     |
| Riconoscimento vocale | `SpeakerRecognition` |

### <a name="language"></a>Linguaggio

| Service            | Kind                |
|--------------------|---------------------|
| Informazioni sui moduli | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Analisi del testo     | `TextAnalytics`     |
| Traduzione testuale   | `TextTranslation`   |

### <a name="decision"></a>Decisione

| Service           | Kind               |
|-------------------|--------------------|
| Rilevamento anomalie  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizza esperienze      | `Personalizer`     |

È possibile trovare un elenco di "tipi" di servizi cognitivi disponibili con il comando [AZ cognitiveservices account list-kinds](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds) :

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Aggiungere una nuova risorsa al gruppo di risorse

Per creare e sottoscrivere una nuova risorsa di servizi cognitivi, usare il comando [AZ cognitiveservices account create](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) . Questo comando aggiunge una nuova risorsa fatturabile al gruppo di risorse creato in precedenza. Quando si crea la nuova risorsa, è necessario conoscerne il "tipo" di servizio che si vuole usare, oltre al piano tariffario (o SKU) e a una località di Azure:

È possibile creare una risorsa F0 (gratuita) per il rilevatore `anomaly-detector-resource` di anomalie, denominato con il comando seguente.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-resource"></a>Ottenere le chiavi per la risorsa

Per accedere all'installazione locale dell'interfaccia della riga di comando (CLI), usare il comando [AZ login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) .

```console
az login
```

Usare il comando [AZ cognitiveservices account keys List](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) per ottenere le chiavi per la risorsa del servizio cognitivo.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Piani tariffari e fatturazione

I piani tariffari (e l'importo fatturato) si basano sul numero di transazioni inviate usando le informazioni di autenticazione. Ogni piano tariffario specifica:
* numero massimo di transazioni consentite al secondo (TPS).
* funzionalità del servizio abilitate all'interno del piano tariffario.
* Costo di una quantità predefinita di transazioni. Il superamento di questo importo comporterà un addebito aggiuntivo come specificato nei dettagli relativi ai [prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) per il servizio.


## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole eseguire la pulizia e la rimozione di una risorsa di servizi cognitivi, è possibile eliminarla o il gruppo di risorse. Eliminando il gruppo di risorse, vengono eliminate anche tutte le altre risorse contenute nel gruppo.

Per rimuovere il gruppo di risorse e le risorse associate, usare il comando AZ Group Delete.

```azurecli-interactive
az group delete --name storage-resource-group
```

## <a name="see-also"></a>Vedere anche

* [Autenticare le richieste a Servizi cognitivi di Azure](authentication.md)
* [Che cosa sono i servizi cognitivi di Azure?](Welcome.md)
* [Supporto del linguaggio naturale](language-support.md)
* [Supporto del contenitore Docker](cognitive-services-container-support.md)
