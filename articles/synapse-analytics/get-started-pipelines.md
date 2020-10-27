---
title: "Esercitazione: Introduzione all'orchestrazione con le pipeline"
description: In questa esercitazione si apprenderà come orchestrare pipeline ed attività usando Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 42d2ac6cf6592f8e22b0a66aee84c3436d466572
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92329884"
---
# <a name="orchestrate-with-pipelines"></a>Orchestrare con le pipeline

In questa esercitazione si apprenderà come orchestrare pipeline ed attività usando Synapse Studio. 

## <a name="overview"></a>Panoramica

È possibile orchestrare un'ampia gamma di attività in Azure Synapse.

1. In Synapse Studio assare all'hub **Integrate** (Integrazione).
1. Selezionare **+**  > **Pipeline** per creare una nuova pipeline.
1. Passare all'hub **Sviluppo** e selezionare uno dei notebook creati in precedenza.
1. Trascinare il notebook nella pipeline. **Nota** : aggiungere il passaggio per l'importazione di moduli nel notebook come specificato nel [documento](https://docs.microsoft.com/azure/synapse-analytics/spark/synapse-spark-sql-pool-import-export#transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace), perché sono necessari durante l'esecuzione dalla pipeline)
1. Nella pipeline selezionare **Aggiungi trigger** > **Nuovo/Modifica** .
1. In **Trigger** selezionare **Nuovo** e impostare **Ricorrenza** su "Ogni ora".
1. Selezionare **OK** . 
1. Selezionare **Pubblica tutti** .
1. Per fare in modo che la pipeline venga eseguita immediatamente, senza attendere l'ora successiva, selezionare **Aggiungi trigger** > **Trigger now** (Trigger immediato).



## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Visualizzare i dati con Power BI](get-started-visualize-power-bi.md)
                                 
