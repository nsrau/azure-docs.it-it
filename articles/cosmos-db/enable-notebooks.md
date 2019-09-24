---
title: Abilitare i notebook nell'account Azure Cosmos DB
description: I notebook predefiniti di Azure Cosmos DB consentono di analizzare e visualizzare i dati all'interno del portale. Questo articolo descrive come abilitare questa funzionalità per gli account Cosmos.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: 34e1d9fe937118a2eafcdd85c153653eb28752d4
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219253"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts"></a>Abilitare i notebook per gli account Azure Cosmos DB

I notebook Jupyter incorporati in Azure Cosmos DB consentono di analizzare e visualizzare i dati dall'portale di Azure. Questo articolo descrive come abilitare questa funzionalità per l'account Azure Cosmos DB.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>Abilitare i notebook in un nuovo account Cosmos
1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Selezionare **Crea una risorsa** > **Database** > **Azure Cosmos DB**.
1. Nella pagina **Crea Account Azure Cosmos DB** selezionare **notebook**. 
 
    ![Selezionare l'opzione Notebooks nel pannello Azure Cosmos DB crea](media/enable-notebooks/create-new-account-with-notebooks.png)
1. Selezionare **Rivedi e crea**. È possibile ignorare l'opzione **rete** e **tag** . 
1. Esaminare le impostazioni dell'account e quindi selezionare **Crea**. La creazione dell'account richiede alcuni minuti. Attendere che la pagina del portale visualizzi **La distribuzione è stata completata**. 

    ![Riquadro Notifiche del portale di Azure](media/enable-notebooks/create-new-account-with-notebooks-complete.png)
1. Selezionare **Vai alla risorsa** per passare alla pagina dell'account Azure Cosmos DB. 

    ![Pagina dell'account Azure Cosmos DB](../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png)

1. Passare al riquadro **Esplora dati** . Verrà visualizzata l'area di lavoro dei notebook.

    ![Nuova area di lavoro Azure Cosmos DB notebook](media/enable-notebooks/new-notebooks-workspace.png)

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Abilitare i notebook in un account Cosmos esistente
È anche possibile abilitare notebook per gli account esistenti. Questo passaggio deve essere eseguito solo una volta per ogni account.

1. Passare al riquadro **Esplora dati** nell'account Cosmos.
1. Selezionare **Abilita notebook**.

    ![Creare una nuova area di lavoro notebook in Esplora dati](media/enable-notebooks/enable-notebooks-workspace.png)
1. Verrà richiesto di creare una nuova area di lavoro dei notebook. Selezionare **installazione completa.**
1. L'account è ora abilitato per l'uso dei notebook.

## <a name="create-and-run-your-first-notebook"></a>Creare ed eseguire il primo notebook

Per verificare che sia possibile usare i notebook, selezionare uno dei notebook in notebook di esempio. Verrà salvata una copia del notebook nell'area di lavoro e la si aprirà.

In questo esempio si userà **GettingStarted. ipynb**. 

![Visualizzare il notebook GettingStarted. ipynb](media/enable-notebooks/select-getting-started-notebook.png)

Per eseguire il notebook:
1. Selezionare la prima cella di codice che contiene il codice Python. 
1. Selezionare **Esegui** per eseguire la cella. È anche possibile usare **MAIUSC + INVIO** per eseguire la cella.
1. Aggiornare il riquadro delle risorse per visualizzare il database e il contenitore che è stato creato.

    ![Esegui notebook introduttivo](media/enable-notebooks/run-first-notebook-cell.png)

È anche possibile selezionare **nuovo notebook** per creare un nuovo notebook o caricare un file Notebook (con estensione ipynb) esistente selezionando **Carica file** dal menu **notebook personali** . 

![Creare o caricare un nuovo notebook](media/enable-notebooks/create-or-upload-new-notebook.png)

## <a name="next-steps"></a>Passaggi successivi

- Scopri i vantaggi di [Azure Cosmos DB notebook Jupyter](cosmosdb-jupyter-notebooks.md)
