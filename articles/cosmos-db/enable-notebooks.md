---
title: Abilitare i notebook nell'account Azure Cosmos DB (anteprima)
description: I notebook predefiniti di Azure Cosmos DB consentono di analizzare e visualizzare i dati all'interno del portale. Questo articolo descrive come abilitare questa funzionalità per gli account Cosmos.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: c869069e40780b1fd399758e84b0ffba311398e3
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93334175"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Abilitare i notebook per gli account Azure Cosmos DB (anteprima)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> I notebook predefiniti per Azure Cosmos DB sono attualmente disponibili nelle aree di Azure seguenti: Australia orientale, Stati Uniti orientali, Stati Uniti orientali 2, Europa settentrionale, Stati Uniti centro-meridionali, Asia sudorientale, Regno Unito meridionale, Europa occidentale e Stati Uniti occidentali 2. Per usare i notebook, [creare un nuovo account con notebook](#enable-notebooks-in-a-new-cosmos-account) o [abilitare notebook in un account esistente](#enable-notebooks-in-an-existing-cosmos-account) in una di queste aree.

I notebook Jupyter predefiniti in Azure Cosmos DB consentono di analizzare e visualizzare i dati dal portale di Azure. Questo articolo descrive come abilitare questa funzionalità per l'account Azure Cosmos DB.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>Abilitare i notebook in un nuovo account Cosmos

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Selezionare **Crea una risorsa** > **Database** > **Azure Cosmos DB**.
1. Nella pagina **Crea Account Azure Cosmos DB** selezionare **notebook**. 
 
    :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks.png" alt-text="Selezionare l'opzione Notebooks nel pannello Azure Cosmos DB crea":::

1. Selezionare **Rivedi e crea**. È possibile ignorare l'opzione **rete** e **tag** . 
1. Esaminare le impostazioni dell'account e quindi selezionare **Crea**. La creazione dell'account richiede alcuni minuti. Attendere che la pagina del portale visualizzi **La distribuzione è stata completata**. 

   :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks-complete.png" alt-text="Riquadro Notifiche del portale di Azure":::

1. Selezionare **Vai alla risorsa** per passare alla pagina dell'account Azure Cosmos DB.

   :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png" alt-text="Pagina dell'account Azure Cosmos DB":::

1. Passare al riquadro **Esplora dati** . Verrà visualizzata l'area di lavoro dei notebook.

    :::image type="content" source="media/enable-notebooks/new-notebooks-workspace.png" alt-text="Nuova area di lavoro Azure Cosmos DB notebook":::

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Abilitare i notebook in un account Cosmos esistente

È anche possibile abilitare notebook per gli account esistenti. Questo passaggio deve essere eseguito solo una volta per ogni account.

1. Passare al riquadro **Esplora dati** nell'account Cosmos.
1. Selezionare **Abilita notebook**.

    :::image type="content" source="media/enable-notebooks/enable-notebooks-workspace.png" alt-text="Creare una nuova area di lavoro notebook in Esplora dati":::

1. Verrà richiesto di creare una nuova area di lavoro dei notebook. Selezionare **installazione completa.**
1. L'account è ora abilitato per l'uso dei notebook.

## <a name="create-and-run-your-first-notebook"></a>Creare ed eseguire il primo notebook

Per verificare che sia possibile usare i notebook, selezionare uno dei notebook in notebook di esempio. Verrà salvata una copia del notebook nell'area di lavoro e la si aprirà.

In questo esempio si userà **GettingStarted. ipynb**. 

:::image type="content" source="media/enable-notebooks/select-getting-started-notebook.png" alt-text="Visualizzare il notebook GettingStarted. ipynb":::

Per eseguire il notebook:
1. Selezionare la prima cella di codice che contiene il codice Python. 
1. Selezionare **Esegui** per eseguire la cella. È anche possibile usare **MAIUSC + INVIO** per eseguire la cella.
1. Aggiornare il riquadro delle risorse per visualizzare il database e il contenitore che è stato creato.

    :::image type="content" source="media/enable-notebooks/run-first-notebook-cell.png" alt-text="Esegui notebook introduttivo":::

È anche possibile selezionare **nuovo notebook** per creare un nuovo notebook o caricare un file Notebook (con estensione ipynb) esistente selezionando **Carica file** dal menu **notebook personali** . 

:::image type="content" source="media/enable-notebooks/create-or-upload-new-notebook.png" alt-text="Creare o caricare un nuovo notebook":::

## <a name="next-steps"></a>Passaggi successivi

- Vedere le informazioni sui vantaggi dei [notebook Jupyter di Azure Cosmos DB](cosmosdb-jupyter-notebooks.md)
