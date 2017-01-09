---
title: Consigli di Azure Advisor sui costi | Microsoft Docs
description: Usare Azure Advisor per ottimizzare il costo delle distribuzioni di Azure.
services: advisor
documentationcenter: NA
author: kumudd
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
translationtype: Human Translation
ms.sourcegitcommit: a560aa9cc6c70fd3827f649627466c9071b8ab8b
ms.openlocfilehash: 39a882a8338ce820c6b9b98f58d1a9db7448c7be

---

# <a name="advisor-cost-recommendations"></a>Consigli di Advisor sui costi

Advisor aiuta a ottimizzare e ridurre la spesa complessiva legata ad Azure identificando le risorse inattive e sottoutilizzate. È possibile ottenere consigli sui costi nella scheda **Costo** del dashboard di Advisor.

![Scheda Costo di Advisor](./media/advisor-cost-recommendations/advisor-cost-tab2.png)

## <a name="low-utilization-virtual-machines"></a>Macchine virtuali dall'utilizzo ridotto 

Mentre in alcuni scenari applicativi può verificarsi un utilizzo ridotto legato alla progettazione, è comunque possibile risparmiare denaro gestendo le dimensioni e il numero delle macchine virtuali. Advisor consente di monitorare l'uso delle macchine virtuali per 14 giorni e identifica le macchine virtuali dall'utilizzo ridotto. Le macchine virtuali in cui l'utilizzo della CPU è pari al 5% o inferiore e l'uso di rete è pari a 7 MB o inferiore per quattro o più giorni sono considerate macchine virtuali dall'utilizzo ridotto.

Advisor mostra una stima dei costi legati all'esecuzione continuativa della macchina virtuale. È possibile scegliere di arrestare o ridimensionare la macchina virtuale.  

![Consigli di Advisor sui costi per il ridimensionamento delle macchine virtuali](./media/advisor-cost-recommendations/advisor-cost-resizevms.png)

## <a name="sql-elastic-database-pool-recommendations"></a>Consigli sul pool di database elastici di SQL

Advisor identifica le istanze di SQL Server che possono trarre vantaggio dalla creazione di pool di database elastici. I pool di database elastici offrono una soluzione semplice e conveniente per gestire gli obiettivi di prestazioni per più database con modelli di uso mutevoli. Per altre informazioni sui pool di database elastici, vedere [Cos'è un pool elastico di Azure?](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/)

![Consigli di Advisor sui costi per pool di database elastici](./media/advisor-cost-recommendations/advisor-cost-elasticdbpools.png)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Come accedere ai consigli sui costi in Azure Advisor

1. Accedere al [Portale di Azure](https://portal.azure.com).
2. Nel riquadro di navigazione sinistro fare clic su **More services** (Altri servizi), nel riquadro con il menu dei servizi scorrere verso il basso fino a **Monitoring and Management** (Monitoraggio e gestione), quindi fare clic su **Azure Advisor**. Viene avviato il dashboard di Advisor. 
3. Nel dashboard di Advisor fare clic sulla scheda **Costo**, selezionare la sottoscrizione per cui si desidera ricevere suggerimenti e quindi fare clic su **Get recommendations** (Ricevi consigli)

> [!NOTE]
> Azure Advisor genera consigli per le sottoscrizioni in cui all'utente è assegnato il ruolo di **proprietario**, **collaboratore** o **lettore**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere le risorse seguenti:
-  [Introduction to Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
-  [Attività iniziali](advisor-get-started.md)
-  [Advisor High Availability recommendations](advisor-cost-recommendations.md) (Consigli di Advisor sulla disponibilità elevata)
-  [Advisor Security recommendations](advisor-cost-recommendations.md) (Consigli di Advisor sulla sicurezza)
-  [Advisor Performance recommendations](advisor-cost-recommendations.md) (Consigli di Advisor sulle prestazioni)



<!--HONumber=Nov16_HO3-->


