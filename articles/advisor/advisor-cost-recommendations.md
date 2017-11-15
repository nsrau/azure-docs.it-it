---
title: Consigli di Azure Advisor sui costi | Microsoft Docs
description: Usare Azure Advisor per ottimizzare il costo delle distribuzioni di Azure.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 7b9c7037271fabd67c1ada80420ad72c340e46bb
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2017
---
# <a name="advisor-cost-recommendations"></a>Consigli di Advisor sui costi

Advisor aiuta a ottimizzare e ridurre la spesa complessiva legata ad Azure identificando le risorse inattive e sottoutilizzate. È possibile ottenere consigli sui costi nella scheda **Costo** del dashboard di Advisor.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Ottimizzare la spesa correlata alle macchine virtuali ridimensionando o arrestando le istanze sottoutilizzate 
Sebbene in alcuni scenari applicativi possa verificarsi un utilizzo ridotto legato alla progettazione, è comunque possibile risparmiare denaro mediante la gestione delle dimensioni e del numero delle macchine virtuali. Advisor monitora l'utilizzo delle macchine virtuali per 14 giorni, in modo da identificare le macchine virtuali il cui utilizzo è ridotto. Le macchine virtuali con un utilizzo della CPU pari al 5% o inferiore e un utilizzo di rete pari a 7 MB o inferiore per quattro o più giorni sono considerate macchine virtuali il cui utilizzo è ridotto.

Advisor mostra il costo stimato qualora si continui a eseguire la macchina virtuale, consentendo pertanto di scegliere se arrestarla o ridimensionarla.

Per un'individuazione più mirata delle macchine virtuali sottoutilizzate, è possibile perfezionare la regola di utilizzo medio della CPU in base alla sottoscrizione.

## <a name="use-a-cost-effective-solution-to-manage-performance-goals-of-multiple-sql-databases"></a>Usare una soluzione conveniente per gestire gli obiettivi di prestazioni di più database SQL
Advisor identifica le istanze di SQL Server che possono trarre vantaggio dalla creazione di pool di database elastici. I pool di database elastici offrono una soluzione semplice e conveniente per gestire gli obiettivi di prestazioni per più database con modelli di utilizzo mutevoli. Per altre informazioni sui pool elastici di Azure, vedere [Cos'è un pool elastico di Azure?](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/).

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Ridurre i costi eliminando i circuiti ExpressRoute il cui provisioning è stato annullato
Advisor identifica i circuiti ExpressRoute che sono rimasti nello stato provider *Senza provisioning* per più di un mese e consiglia di eliminare il circuito se non si intende eseguirne il provisioning con il provider di connettività.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Come accedere ai consigli sui costi in Azure Advisor

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi aprire [Advisor](https://aka.ms/azureadvisordashboard).

2.  Nel dashboard di Advisor fare clic sulla scheda **Costo**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:
* [Introduction to Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [esercitazione introduttiva](advisor-get-started.md)
* [Advisor Performance recommendations](advisor-cost-recommendations.md) (Consigli di Advisor sulle prestazioni)
* [Advisor High Availability recommendations](advisor-cost-recommendations.md) (Consigli di Advisor sulla disponibilità elevata)
* [Advisor Security recommendations](advisor-cost-recommendations.md) (Consigli di Advisor sulla sicurezza)
