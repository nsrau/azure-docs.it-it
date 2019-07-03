---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 1ea0b01997d3d5cecff73f951c51de5898c2f07a
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503634"
---
I comandi seguenti dell'interfaccia della riga di comando di Azure eseguono il provisioning di una risorsa di Rilevamento anomalie nel piano tariffario gratuito. Fare clic sul pulsante **Prova**, incollare il codice e premere INVIO per eseguirlo in Azure Cloud Shell. Verranno stampate le chiavi per l'autenticazione dell'applicazione. Al termine, [creare una variabile di ambiente](../articles/cognitive-services/cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) per una delle chiavi, denominata `ANOMALY_DETECTOR_KEY`.

> [!NOTE]
> * È possibile:
>    * Creare una risorsa usando il [portale di Azure](../articles/cognitive-services/cognitive-services-apis-create-account.md) o l'[interfaccia della riga di comando di Azure](../articles/cognitive-services/cognitive-services-apis-create-account.md) nel computer locale.
>    * Ottenere un [codice della versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/#decision), valido per 7 giorni. Dopo aver eseguito l'iscrizione, sarà disponibile sul [sito Web di Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).
>    * Visualizzare questa risorsa nel [portale di Azure](https://portal.azure.com/). 

I Servizi cognitivi sono rappresentati dalle risorse di Azure di cui si effettua il provisioning. Ogni account di Servizi cognitivi (e la risorsa di Azure associata) deve appartenere a un gruppo di risorse di Azure.

1. Creare un gruppo di risorse di Azure

    ```azurecli-interactive
    az group create \
        --name example-anomaly-detector-resource-group \
        --location westus2
    ```

2. Creare una risorsa di Rilevamento anomalie nel piano tariffario gratuito

    ```azurecli-interactive
    az cognitiveservices account create \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group \
        --kind AnomalyDetector \
        --sku F0 \
        --location westus2 \
        --yes
    ```

3. Visualizzare l'elenco di chiavi per la risorsa

    ```azurecli-interactive
    az cognitiveservices account keys list \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group
    ```