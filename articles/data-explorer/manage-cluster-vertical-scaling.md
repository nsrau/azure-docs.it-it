---
title: Gestire la scalabilità verticale del cluster (aumento delle prestazioni) in Esplora dati di Azure per soddisfare la richiesta di modifica
description: Questo articolo descrive i passaggi per la scalabilità verticale e orizzontale di un cluster Esplora dati di Azure in base alla richiesta di modifica.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 80bbdf3a5d936719b06782cd78d56088b36cb21d
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67985471"
---
# <a name="manage-cluster-vertical-scaling-scale-up-in-azure-data-explorer-to-accommodate-changing-demand"></a>Gestire la scalabilità verticale del cluster (aumento delle prestazioni) in Esplora dati di Azure per soddisfare la richiesta di modifica

Ridimensionare un cluster in modo appropriato è fondamentale per garantire le prestazioni di Esplora dati di Azure. La scelta di dimensioni statiche per un cluster può portare al suo sottoutilizzo o sovrautilizzo e nessuna delle due condizioni è ideale.

Poiché la richiesta in un cluster non può essere prevista con una precisione assoluta, un approccio migliore  consiste nel ridimensionare un cluster, aggiungendo e rimuovendo la capacità e le risorse della CPU con la richiesta di modifica. 

Sono disponibili due flussi di lavoro per la scalabilità di un cluster Esplora dati di Azure:

* [Scalabilità orizzontale](manage-cluster-horizontal-scaling.md), detta anche scalabilità in uscita e in uscita.
* Scalabilità verticale, definita anche scalabilità verticale e verticale.

Questo articolo illustra il flusso di lavoro di scalabilità verticale:

## <a name="configure-vertical-scaling"></a>Configurare la scalabilità verticale

1. Nella portale di Azure passare alla risorsa cluster di Azure Esplora dati. In **Impostazioni**selezionare **scalabilità verticale**.

1. Nella finestra **scalabilità verticale** viene visualizzato un elenco di SKU disponibili per il cluster. Nella figura seguente, ad esempio, sono disponibili solo quattro SKU.

    ![Aumentare le prestazioni](media/manage-cluster-vertical-scaling/scale-up.png)

    Gli SKU sono disabilitati perché sono lo SKU corrente o non sono disponibili nell'area in cui si trova il cluster.

1. Per modificare lo SKU, selezionare un nuovo SKU e fare clic su **Seleziona**.

> [!NOTE]
> * Il processo di scalabilità verticale può richiedere alcuni minuti e, durante tale periodo, il cluster verrà sospeso. 
> * Il ridimensionamento può compromettere le prestazioni del cluster.
> * Il prezzo è una stima delle macchine virtuali del cluster e dei costi del servizio Esplora dati di Azure. Non sono inclusi altri costi. Per informazioni complete sui prezzi, vedere la pagina relativa alla stima dei [costi](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html) di Azure Esplora dati per una stima e la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/data-explorer/) di Azure Esplora dati.

A questo punto è stata configurata la scalabilità verticale per il cluster Esplora dati di Azure. Aggiungere un'altra regola per il ridimensionamento orizzontale. Se è necessaria assistenza per i problemi di scalabilità del cluster, [aprire una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) nella portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

* [Gestire la scalabilità orizzontale del cluster](manage-cluster-horizontal-scaling.md) per aumentare in modo dinamico il numero di istanze in base alle metriche specificate.

* Monitorare l'utilizzo delle risorse seguendo questo articolo: [Monitora le prestazioni, l'integrità e l'utilizzo di Azure Esplora dati con le metriche](using-metrics.md).

