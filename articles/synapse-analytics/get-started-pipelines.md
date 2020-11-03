---
title: "Esercitazione: Introduzione all'integrazione con le pipeline"
description: In questa esercitazione si apprenderà come integrare pipeline e attività usando Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 10/27/2020
ms.openlocfilehash: af01d5b5e424dd5ea229115f7aa3570d0b7cd511
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744933"
---
# <a name="integrate-with-pipelines"></a>Integrare con le pipeline

In questa esercitazione si apprenderà come integrare pipeline e attività usando Synapse Studio. 

## <a name="overview"></a>Panoramica

È possibile integrare un'ampia gamma di attività in Azure Synapse.

1. In Synapse Studio assare all'hub **Integrate** (Integrazione).
1. Selezionare **+**  > **Pipeline** per creare una nuova pipeline.
1. Passare all'hub **Sviluppo** e selezionare uno dei notebook creati in precedenza.
1. Trascinare il notebook nella pipeline. **Nota** : aggiungere il passaggio per l'importazione di moduli nel notebook come specificato nel [documento](https://docs.microsoft.com/azure/synapse-analytics/spark/synapse-spark-sql-pool-import-export#transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace), perché sono necessari durante l'esecuzione dalla pipeline.
1. Nella pipeline selezionare **Aggiungi trigger** > **Nuovo/Modifica**.
1. In **Trigger** selezionare **Nuovo** e impostare **Ricorrenza** su "Ogni ora".
1. Selezionare **OK**. 
1. Selezionare **Pubblica tutti**.
1. Per fare in modo che la pipeline venga eseguita immediatamente, senza attendere l'ora successiva, selezionare **Aggiungi trigger** > **Trigger now** (Trigger immediato).



## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Visualizzare i dati con Power BI](get-started-visualize-power-bi.md)
                                 
