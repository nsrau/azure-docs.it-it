---
title: Aumentare le prestazioni di un cluster di Esplora dati di Azure per soddisfare la richiesta di modifica
description: Questo articolo descrive i passaggi per aumentare le prestazioni e ridurre un cluster di Esplora dati di Azure basato su richiesta di modifica.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: dc9ca8bb592e699d19835efeafb91e81408ae297
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571530"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Gestire l'aumento delle prestazioni di un cluster per rispondere al cambiamento della domanda

Esistono due flussi di lavoro per il ridimensionamento di un cluster di Esplora dati di Azure:
1. [Scalabilità orizzontale](manage-cluster-horizontal-scaling.md), definita anche scalabilità orizzontale.
2. Scalabilità verticale, detta anche scalabilità verticale.

Questo articolo illustra come gestire la scalabilità verticale del cluster.

Ridimensionare un cluster in modo appropriato è fondamentale per garantire le prestazioni di Esplora dati di Azure. Ma la domanda in un cluster non è possibile prevedere con accuratezza assoluto. Una dimensione del cluster statico può causare sottoutilizzo o di Sovrautilizzo, nessuno dei quali è ideale. Un approccio migliore consiste *scalabilità* un cluster, aggiungendo e rimuovendo la capacità e risorse della CPU con la richiesta di modifica. 

## <a name="steps-to-configure-vertical-scaling"></a>Passaggi per configurare la scalabilità verticale

1. Passare al cluster. Sotto **le impostazioni**, selezionare **aumentare**.

    Si è illustrato un elenco degli SKU disponibili. Nella figura seguente, ad esempio, solo quattro SKU sono disponibili.

    ![Aumentare le prestazioni](media/manage-cluster-vertical-scaling/scale-up.png)

    Gli SKU sono disabilitati perché si tratta dello SKU corrente oppure non sono disponibili nell'area in cui si trova il cluster.

1. Per modificare lo SKU, selezionare lo SKU desiderato e scegliere il **seleziona** pulsante.

> [!NOTE]
> Il processo di ridimensionamento verticale può richiedere alcuni minuti e durante tale periodo verrà sospeso il cluster. Si noti che, riducendo le prestazioni, si può compromettere l'efficienza del cluster.

Ora effettuata un'operazione di aumento o riduzione delle prestazioni per il cluster di Esplora dati di Azure.

Se occorre assistenza per problemi di ridimensionamento del cluster [aprire una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) nel portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

* [Gestire la scalabilità orizzontale di cluster](manage-cluster-horizontal-scaling.md) ridimensionare in modo dinamico il numero di istanze in base alle metriche che specificano out.

* Monitorare l'utilizzo delle risorse seguendo questo articolo: [Monitorare le prestazioni di Esplora dati di Azure, integrità e sull'utilizzo con le metriche](using-metrics.md).

