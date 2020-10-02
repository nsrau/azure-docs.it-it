---
title: "Esercitazione: Introduzione all'orchestrazione con le pipeline"
description: In questa esercitazione si apprenderà come orchestrare pipeline ed attività usando Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 72eea7c46dd005cd16ae5b8f0022c1174dd28f27
ms.sourcegitcommit: 0194a29a960e3615f96a2d9d8a7e681cf3e8f9ab
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89667476"
---
# <a name="orchestrate-with-pipelines"></a>Orchestrare con le pipeline

In questa esercitazione si apprenderà come orchestrare pipeline ed attività usando Synapse Studio. 

## <a name="overview"></a>Panoramica

È possibile orchestrare un'ampia gamma di attività in Azure Synapse.

1. In Synapse Studio passare all'hub **Orchestrate** (Orchestrazione).
1. Selezionare **+**  > **Pipeline** per creare una nuova pipeline.
1. Passare all'hub **Sviluppo** e selezionare uno dei notebook creati in precedenza.
1. Trascinare il notebook nella pipeline.
1. Nella pipeline selezionare **Aggiungi trigger** > **Nuovo/Modifica**.
1. In **Scegliere un trigger** selezionare **Nuovo** e in **ricorrenza** impostare il trigger per essere eseguito ogni ora.
1. Selezionare **OK**.
1. Selezionare **Pubblica tutti**. La pipeline verrà eseguita ogni ora.
1. Per eseguire la pipeline adesso senza attendere l'ora successiva, selezionare **Aggiungi trigger** > **Nuovo/Modifica**.



## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Visualizzare i dati con Power BI](get-started-visualize-power-bi.md)
                                 
