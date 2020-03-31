---
title: Abilitare i blocchi appunti nell'account database Cosmos di Azure (anteprima)Enable notebooks in the Azure Cosmos DB account (preview)
description: I blocchi appunti predefiniti di Azure Cosmos DB consentono di analizzare e visualizzare i dati dal portale. In questo articolo viene descritto come abilitare questa funzionalità per gli account Cosmos.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: dcec310db43baa513b2d574d03f3f35dee3f773b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768014"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Abilitare i blocchi appunti per gli account di Azure Cosmos DB (anteprima)Enable notebooks for Azure Cosmos DB accounts (preview)

> [!IMPORTANT]
> I blocchi appunti predefiniti per Azure Cosmos DB sono attualmente disponibili nelle seguenti aree di Azure: Australia orientale, Stati Uniti orientali, Stati Uniti orientali 2, Nord Europa, Stati Uniti centro-meridionali, Sud-Est asiatico, Regno Unito meridionale, Europa occidentale e Stati Uniti occidentali 2. Per usare i blocchi appunti, [creare un nuovo account con blocchi appunti](#enable-notebooks-in-a-new-cosmos-account) o [abilitare i blocchi appunti in un account esistente](#enable-notebooks-in-an-existing-cosmos-account) in una di queste aree.

I blocchi appunti Jupyter incorporati in Azure Cosmos DB consentono di analizzare e visualizzare i dati dal portale di Azure.Built-in Jupyter notebooks in Azure Cosmos DB enable you to analyze and visualize your data from the Azure portal. Questo articolo descrive come abilitare questa funzionalità per l'account Azure Cosmos DB.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>Abilitare i blocchi appunti in un nuovo account Cosmos
1. Accedere al portale di [Azure](https://portal.azure.com/).
1. Selezionare **Crea una risorsa** > **Database** > **cosmo di Azure DB**.
1. Nella pagina **Crea account database cosmo di Azure** selezionare Blocchi **appunti.** 
 
    ![Selezionare l'opzione blocchi appunti nel pannello Crea database Cosmos di AzureSelect notebooks option in Azure Cosmos DB Create blade](media/enable-notebooks/create-new-account-with-notebooks.png)
1. Selezionare **Revisione e creazione**. È possibile ignorare l'opzione **Rete** e **tag.** 
1. Esaminare le impostazioni dell'account e quindi selezionare **Crea**. La creazione dell'account richiede alcuni minuti. Attendere che la pagina del portale visualizzi **La distribuzione è stata completata**. 

    ![Riquadro Notifiche del portale di Azure](media/enable-notebooks/create-new-account-with-notebooks-complete.png)
1. Selezionare **Vai alla risorsa** per passare alla pagina dell'account Azure Cosmos DB. 

    ![Pagina dell'account Azure Cosmos DB](../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png)

1. Passare al riquadro **Esplora dati.** Verrà visualizzata l'area di lavoro blocchi appunti.

    ![Nuovo spazio di lavoro per i blocchi appunti di Azure Cosmos DB](media/enable-notebooks/new-notebooks-workspace.png)

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Abilitare i blocchi appunti in un account Cosmos esistente
È inoltre possibile abilitare i blocchi appunti sugli account esistenti. Questo passaggio deve essere eseguito una sola volta per account.

1. Passare al riquadro **Esplora dati** nell'account Cosmos.
1. Selezionare **Abilita blocchi appunti**.

    ![Creare una nuova area di lavoro Blocchi appunti in Esplora dati](media/enable-notebooks/enable-notebooks-workspace.png)
1. Verrà richiesto di creare una nuova area di lavoro blocchi appunti. Selezionare **Completa configurazione.**
1. Il tuo account è ora abilitato per usare i blocchi appunti!

## <a name="create-and-run-your-first-notebook"></a>Creare ed eseguire il primo blocco appunti

Per verificare che sia possibile usare i blocchi appunti, selezionare uno dei blocchi appunti in Blocchi appunti di esempio. Verrà salvata una copia del blocco appunti nell'area di lavoro e verrà aperta.

In questo esempio verrà utilizzato **GettingStarted.ipynb**. 

![Visualizza il blocco appunti GettingStarted.ipynb](media/enable-notebooks/select-getting-started-notebook.png)

Per eseguire il blocco appunti:
1. Selezionare la prima cella di codice che contiene il codice Python. 
1. Selezionare **Esegui** per eseguire la cella. Per eseguire la cella, è anche possibile utilizzare **Maiusc e INVIO.**
1. Aggiornare il riquadro delle risorse per visualizzare il database e il contenitore che sono stati creati.

    ![Eseguire il blocco appunti introduttivo](media/enable-notebooks/run-first-notebook-cell.png)

È anche possibile selezionare **Nuovo blocco appunti** per creare un nuovo blocco appunti o caricare un file di blocco appunti esistente (ipynb) selezionando Carica **file** dal menu Blocchi **appunti personali.** 

![Creare o caricare un nuovo blocco appunti](media/enable-notebooks/create-or-upload-new-notebook.png)

## <a name="next-steps"></a>Passaggi successivi

- Scopri i vantaggi dei [notebook Azure Cosmos DB Jupyter](cosmosdb-jupyter-notebooks.md)
