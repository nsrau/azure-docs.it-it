---
title: Gestire la scalabilità verticale del cluster (scalabilità verticale) per soddisfare la domanda in Azure Data ExplorerManage cluster vertical scaling (scale up) to match demand in Azure Data Explorer
description: Questo articolo descrive i passaggi per aumentare e ridurre un cluster di Azure Data Explorer in base alla modifica della domanda.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 95275598febae2b6b0355a7bc3e512490dae500d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560440"
---
# <a name="manage-cluster-vertical-scaling-scale-up-in-azure-data-explorer-to-accommodate-changing-demand"></a>Gestire la scalabilità verticale del cluster (scalabilità verticale) in Azure Data Explorer per soddisfare le mutevoli richiesteManage cluster vertical scaling (scale up) in Azure Data Explorer to accommodate changing demand

Ridimensionare un cluster in modo appropriato è fondamentale per garantire le prestazioni di Esplora dati di Azure. La scelta di dimensioni statiche per un cluster può portare al suo sottoutilizzo o sovrautilizzo e nessuna delle due condizioni è ideale.

Poiché la domanda in un cluster non può essere prevista con assoluta precisione, un approccio migliore consiste nel *ridimensionare* un cluster, aggiungendo e rimuovendo le risorse di capacità e CPU con la domanda in continua evoluzione. 

Esistono due flussi di lavoro per la scalabilità di un cluster di Azure Data Explorer:There are two workflows for scaling an Azure Data Explorer cluster:

* [Ridimensionamento orizzontale,](manage-cluster-horizontal-scaling.md)denominato anche ridimensionamento in orizzontale e in uscita.
* Ridimensionamento verticale, denominato anche ridimensionamento verso l'alto e verso il basso.

Questo articolo illustra il flusso di lavoro di scalabilità verticale:This article explains the vertical scaling workflow:

## <a name="configure-vertical-scaling"></a>Configurare il ridimensionamento verticale

1. Nel portale di Azure passare alla risorsa cluster di Azure Data Explorer.In the Azure portal, go to your Azure Data Explorer cluster resource. In **Impostazioni**selezionare **Scalabilità verticale**.

1. Nella finestra **Scalabilità verticale** verrà visualizzato un elenco di SKU disponibili per il cluster. Ad esempio, nella figura seguente sono disponibili solo quattro SKU.

    ![Aumentare le prestazioni](media/manage-cluster-vertical-scaling/scale-up.png)

    Gli SKU sono disabilitati perché sono lo SKU corrente o non sono disponibili nell'area in cui si trova il cluster.

1. Per modificare lo SKU, selezionare un nuovo SKU e fare clic su **Seleziona**.

> [!NOTE]
> * Il processo di ridimensionamento verticale può richiedere alcuni minuti e durante tale periodo il cluster verrà sospeso. 
> * La riduzione della scalabilità verticale può danneggiare le prestazioni del cluster.
> * Il prezzo è una stima delle macchine virtuali del cluster e dei costi del servizio Azure Data Explorer.The price is an estimate of the cluster's virtual machines and Azure Data Explorer service costs. Non sono inclusi altri costi. Vedere Pagina [di stima dei costi](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html) di Azure Data Explorer per una stima e la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/data-explorer/) di Azure Data Explorer per informazioni complete sui prezzi.

È stato configurato il ridimensionamento verticale per il cluster Azure Data Explorer.You've now configured vertical scaling for your Azure Data Explorer cluster. Aggiungere un'altra regola per un ridimensionamento orizzontale. Per assistenza sui problemi di scalabilità del cluster, aprire una richiesta di supporto nel portale di Azure.If you need assistance with cluster-scaling issues, [open a support request](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) in the Azure portal.

## <a name="next-steps"></a>Passaggi successivi

* Gestire la [scalabilità orizzontale del cluster](manage-cluster-horizontal-scaling.md) per scalare in modo dinamico il numero di istanze in base alle metriche specificate.

* Monitorare l'utilizzo delle risorse seguendo questo articolo: [Monitorare le prestazioni, l'integrità e l'utilizzo](using-metrics.md)di Azure Data Explorer con le metriche.

