---
title: Creare la risorsa di Personalizza esperienze
description: La configurazione del servizio include il modo in cui vengono trattate le ricompense, la frequenza delle esplorazioni, la frequenza di ripetizione del training del modello e la quantità di dati archiviati.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: adb97db53d1fc0b6f0cdb14b697c82ec52501b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336052"
---
# <a name="create-a-personalizer-resource"></a>Creare una risorsa PersonalizerCreate a Personalizer resource

Una risorsa Personalizer è la stessa cosa di un ciclo di apprendimento Personalizer.A Personalizer resource is the same thing as a Personalizer learning loop. Viene creata una singola risorsa, o ciclo di apprendimento, per ogni dominio dell'oggetto o area di contenuto in uso. Non usare più aree di contenuto nello stesso ciclo perché questo confonderà il ciclo di apprendimento e fornirà stime scadenti.

Se si desidera che Personalizer selezioni il contenuto migliore per più aree di contenuto di una pagina Web, utilizzare un ciclo di apprendimento diverso per ognuna.


## <a name="create-a-resource-in-the-azure-portal"></a>Creare una risorsa nel portale di Azure

Creare una risorsa di Personalizza esperienze per ogni ciclo di feedback.

1. Accedere al portale di [Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Il collegamento precedente consente di accedere alla pagina **Crea** per il servizio Personalizza esperienze.
1. Immettere il nome del servizio, selezionare una sottoscrizione, una località, un piano tariffario e un gruppo di risorse.

    > [!div class="mx-imgBorder"]
    > ![Usare il portale di Azure per creare una risorsa Personalizer, detta anche ciclo di apprendimento.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. Selezionare **Crea** per creare la risorsa.

1. Dopo aver distribuito la risorsa, selezionare il pulsante Vai alla risorsa per passare alla risorsa Personalizer.After your resource has deployed, select the **Go to Resource** button to go to your Personalizer resource.

1. Selezionare la pagina **Guida introduttiva** per la risorsa, quindi copiare i valori per l'endpoint e la chiave. Per usare le API Rank e Reward sono necessari sia l'endpoint della risorsa che la chiave.

1. Selezionare la pagina **Configurazione** per la nuova risorsa per [configurare il ciclo di apprendimento.](how-to-settings.md)

## <a name="create-a-resource-with-the-azure-cli"></a>Creare una risorsa con l'interfaccia della riga di comando di AzureCreate a resource with the Azure CLI

1. Accedere all'interfaccia della riga di comando di Azure con il comando seguente:

    ```azurecli-interactive
    az login
    ```

1. Creare un gruppo di risorse, un raggruppamento logico per gestire tutte le risorse di Azure che si intende usare con la risorsa Personalizer.Create a resource group, a logical grouping to manage all Azure resources you intend to use with the Personalizer resource.


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. Creare una nuova risorsa Personalizer, _learning loop_, con il comando seguente per un gruppo di risorse esistente.

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    Restituisce un oggetto JSON, che include **l'endpoint della risorsa.**

1. Usare il comando dell'interfaccia della riga di comando di Azure seguente per ottenere la **chiave di risorsa.**

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    Per usare le API Rank e Reward sono necessari sia l'endpoint della risorsa che la chiave.

## <a name="next-steps"></a>Passaggi successivi

* [Configura](how-to-settings.md) Ciclo di apprendimento del personalizente