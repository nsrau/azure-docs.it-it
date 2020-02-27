---
title: Creare la risorsa di Personalizza esperienze
description: La configurazione del servizio include il modo in cui vengono trattate le ricompense, la frequenza delle esplorazioni, la frequenza di ripetizione del training del modello e la quantità di dati archiviati.
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: cb14415f3a5950ad1534d9eb8da94198a41f4f91
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624231"
---
# <a name="create-a-personalizer-resource"></a>Creare una risorsa di personalizzazione

Una risorsa di personalizzazione è la stessa del ciclo di apprendimento della personalizzazione. Viene creata una singola risorsa, o un ciclo di apprendimento, per ogni dominio soggetto o area di contenuto. Non usare più aree di contenuto nello stesso ciclo perché questa operazione consentirà di confondere il ciclo di apprendimento e fornire stime insufficienti.

Se si vuole che il Personalizzatore selezioni il contenuto migliore per più di un'area di contenuto di una pagina Web, usare un ciclo di apprendimento diverso per ciascuno di essi.


## <a name="create-a-resource-in-the-azure-portal"></a>Creare una risorsa nel portale di Azure

Creare una risorsa di Personalizza esperienze per ogni ciclo di feedback.

1. Accedere al [portale di Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Il collegamento precedente consente di accedere alla pagina **Crea** per il servizio Personalizza esperienze.
1. Immettere il nome del servizio, selezionare una sottoscrizione, una località, un piano tariffario e un gruppo di risorse.

    > [!div class="mx-imgBorder"]
    > ![usare portale di Azure per creare una risorsa di personalizzazione, definita anche ciclo di apprendimento.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. Selezionare **Crea** per creare la risorsa.

1. Dopo che la risorsa è stata distribuita, selezionare il pulsante **Vai alla risorsa** per passare alla risorsa di personalizzazione. Per [configurare il ciclo di apprendimento](how-to-settings.md), passare alla pagina di **configurazione** per la nuova risorsa.

## <a name="create-a-resource-with-the-azure-cli"></a>Creare una risorsa con l'interfaccia della riga di comando di Azure

1. Accedere all'interfaccia della riga di comando di Azure con il comando seguente:

    ```bash
    az login
    ```

1. Creare un gruppo di risorse, un raggruppamento logico per gestire tutte le risorse di Azure che si intende usare con la risorsa di personalizzazione.


    ```bash
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. Creare una nuova risorsa di personalizzazione, _Learning loop_, con il comando seguente per un gruppo di risorse esistente.

    ```bash
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```
## <a name="next-steps"></a>Passaggi successivi

* [Configura](how-to-settings.md) Ciclo di apprendimento della personalizzazione