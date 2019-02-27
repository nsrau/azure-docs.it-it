---
title: Ridimensionamento di un cluster di Esplora dati di Azure per rispondere al cambiamento della domanda
description: Questo articolo descrive la procedura per aumentare e ridurre le prestazioni di un cluster di Esplora dati di Azure in base al cambiamento della domanda.
author: radennis
ms.author: radennis
ms.reviewer: v-orspod
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: bc3f97c798f5e040908e8103c00d3f015f8c824d
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415335"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Gestire l'aumento delle prestazioni di un cluster per rispondere al cambiamento della domanda

Ridimensionare un cluster in modo appropriato è fondamentale per garantire le prestazioni di Esplora dati di Azure. Ma in un cluster non è possibile prevedere la domanda con un'accuratezza del 100%. La scelta di dimensioni statiche per un cluster può portare al suo sottoutilizzo o sovrautilizzo e nessuna delle due condizioni è ideale. Un approccio migliore consiste nel *ridimensionare* il cluster aumentandone o diminuendone la capacità e la CPU in base al cambiamento della domanda. Sono disponibili due flussi di lavoro per la scalabilità, l'aumento delle prestazioni e l'aumento del numero di istanze. Questo articolo illustra come aumentare le prestazioni del cluster.

1. Passare al cluster e in **Impostazioni** selezionare **Aumenta**.

    Verrà visualizzato un elenco degli SKU disponibili. Ad esempio, nella figura seguente è disponibile un solo SKU: D14_V2.

    ![Aumentare le prestazioni](media/manage-cluster-scale-up/scale-up.png)

    Lo SKU D13_V2 è disabilitato perché è quello corrente del cluster. L8 e L16 sono disabilitati perché non sono disponibili nell'area in cui si trova il cluster.

1. Per modificare lo SKU, selezionare lo SKU desiderato e premere il pulsante **Seleziona**.

> [!NOTE]
> Il processo per aumentare le prestazioni può richiedere alcuni minuti e durante tale periodo il cluster verrà sospeso. Si noti che, riducendo le prestazioni, si può compromettere l'efficienza del cluster.

A questo punto è stata eseguita un'operazione di aumento o riduzione delle prestazioni del cluster di Esplora dati di Azure. È anche possibile [aumentare il numero di istanze del cluster](manage-cluster-scale-out.md), operazione nota anche come scalabilità automatica, per eseguire il ridimensionamento dinamico in base alle metriche specificate.

Se occorre assistenza per problemi relativi al ridimensionamento di un cluster, aprire una richiesta di supporto nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
