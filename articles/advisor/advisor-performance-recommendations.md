---
title: Consigli di Azure Advisor sulle prestazioni | Microsoft Docs
description: Usare Advisor per ottimizzare le prestazioni delle distribuzioni di Azure.
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
ms.openlocfilehash: c350134e8cea5771900e2e88b02b572c20e9d5bd

---

# <a name="advisor-performance-recommendations"></a>Consigli di Advisor sulle prestazioni

Grazie ai consigli di Advisor sulle prestazioni, è possibile migliorare e aumentare la velocità e la reattività delle applicazioni aziendali critiche. È possibile ottenere consigli sulle prestazioni tramite Advisor dalla scheda **Prestazioni** del dashboard di Advisor.

![Scheda Prestazioni di Advisor](./media/advisor-performance-recommendations/advisor-performance-tab.png)

## <a name="sql-database-recommendations"></a>Consigli sul database SQL

Advisor fornisce una visualizzazione coerente e consolidata dei consigli per tutte le risorse di Azure. Si integra con Advisor per database SQL per offrire consigli su come migliorare le prestazioni del database SQL di Azure. Advisor per database SQL consente di valutare le prestazioni dei database SQL di Azure analizzando la cronologia relativa all'uso. Offre, quindi, i consigli più indicati per l'esecuzione del carico di lavoro tipico del database. 

> [!NOTE]
> Per ottenere consigli è necessario che il database sia stato usato per circa una settimana e che in tale settimana si siano svolte attività costanti. Advisor per database SQL può ottimizzare più facilmente modelli di query coerenti anziché picchi irregolari e casuali di attività.

Per altre informazioni su Advisor per database SQL, vedere [Advisor per database SQL](https://azure.microsoft.com/en-us/documentation/articles/sql-database-advisor/).

![Consigli sul database SQL](./media/advisor-performance-recommendations/advisor-performance-sql.png)

## <a name="redis-cache-recommendations"></a>Consigli su Cache Redis

Azure Advisor identifica le istanze di Cache Redis in cui le prestazioni potrebbero aver subito effetti negativi a causa di uso di memoria, carico del sever, larghezza di banda di rete e connessioni client elevati. Fornisce inoltre consigli sulle procedure consigliate per evitare potenziali problemi. Per altre informazioni relative ai consigli su Cache Redis, vedere [Redis Cache Advisor](https://azure.microsoft.com/en-us/documentation/articles/cache-configure/#redis-cache-advisor).


## <a name="app-services-recommendations"></a>Consigli per Servizi app

Azure Advisor integra consigli sulle procedure consigliate per migliorare l'esperienza di Servizi app e scoprire le funzionalità della piattaforma pertinente. Esempi di consigli per Servizi app sono:
- rilevamento delle istanze in cui le risorse di CPU o memoria vengono esaurite dai runtime delle app con opzioni di migrazione
- rilevamento delle istanze in cui la collocazione delle risorse come App Web e database possono aumentare le prestazioni e ridurre il costo. 

Per altre informazioni sui consigli per Servizi app, vedere [Procedure consigliate per Servizio app di Azure](https://azure.microsoft.com/en-us/documentation/articles/app-service-best-practices/).
![Consigli per Servizi app](./media/advisor-performance-recommendations/advisor-performance-app-service.png)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Come accedere ai consigli sulle prestazioni in Advisor

1. Accedere al [Portale di Azure](https://portal.azure.com).
2. Nel riquadro di navigazione sinistro fare clic su **More services** (Altri servizi), nel riquadro con il menu dei servizi scorrere verso il basso fino a **Monitoring and Management** (Monitoraggio e gestione), quindi fare clic su **Azure Advisor**. Viene avviato il dashboard di Advisor. 
3. Nel dashboard di Advisor fare clic sulla scheda **Prestazioni** e selezionare la sottoscrizione per cui si desidera ricevere consigli.

> [!NOTE]
> Advisor genera consigli per le sottoscrizioni in cui all'utente è assegnato il ruolo di **proprietario, collaboratore o lettore**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere le risorse seguenti:

-  [Introduction to Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
-  [Get started with Advisor](advisor-get-started.md) (Introduzione ad Advisor)
-  [Advisor High Availability recommendations](advisor-high-availability-recommendations.md) (Consigli di Advisor sulla disponibilità elevata)
-  [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)
-  [Advisor Cost recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sui costi)



<!--HONumber=Nov16_HO3-->


