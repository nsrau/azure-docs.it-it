---
title: Ridimensionamento di un cluster di Esplora dati di Azure per rispondere al cambiamento della domanda
description: Questo articolo descrive i passaggi per aumentare le prestazioni e ridurre un cluster di Esplora dati di Azure basato su richiesta di modifica.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 71f1d197b4a3fb437607f876ea524e582e62c0fd
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756336"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Gestire l'aumento delle prestazioni di un cluster per rispondere al cambiamento della domanda

Ridimensionare un cluster in modo appropriato è fondamentale per garantire le prestazioni di Esplora dati di Azure. Ma la domanda in un cluster non è possibile prevedere con accuratezza assoluto. Una dimensione del cluster statico può causare sottoutilizzo o di Sovrautilizzo, nessuno dei quali è ideale.

Un approccio migliore consiste *scalabilità* un cluster, aggiungendo e rimuovendo la capacità e risorse della CPU con la richiesta di modifica. Esistono due flussi di lavoro per la scalabilità: scalabilità verticale e orizzontale. Questo articolo illustra come gestire cluster scalabilità verticale.

1. Passare al cluster. Sotto **le impostazioni**, selezionare **aumentare**.

    Si è illustrato un elenco degli SKU disponibili. Nella figura seguente, ad esempio, solo quattro SKU sono disponibili.

    ![Aumentare le prestazioni](media/manage-cluster-scale-up/scale-up.png)

    Gli SKU sono disabilitati perché si tratta dello SKU corrente oppure non sono disponibili nell'area in cui si trova il cluster.

1. Per modificare lo SKU, selezionare lo SKU desiderato e scegliere il **seleziona** pulsante.

> [!NOTE]
> Il processo di scalabilità verticale può richiedere alcuni minuti e durante tale periodo verrà sospeso il cluster. Si noti che, riducendo le prestazioni, si può compromettere l'efficienza del cluster.

Ora effettuata un'operazione di aumento o riduzione delle prestazioni per il cluster di Esplora dati di Azure. È anche possibile [gestire i cluster di scalabilità orizzontale](manage-cluster-scale-out.md) ridimensionare in modo dinamico il numero di istanze in base alle metriche che specificano out.

Se occorre assistenza per problemi di ridimensionamento del cluster [aprire una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) nel portale di Azure.
