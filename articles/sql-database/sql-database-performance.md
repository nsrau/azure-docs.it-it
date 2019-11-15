---
title: Monitorare e migliorare le prestazioni
description: Il database SQL di Azure offre strumenti per le prestazioni che consentono di identificare le aree in cui è possibile migliorare le prestazioni delle query correnti.
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 11/14/2019
ms.openlocfilehash: c710e2b7c4376fc1725032c86cd02d9a6c8cafac
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74090358"
---
# <a name="monitor-and-improve-performance"></a>Monitorare e migliorare le prestazioni

Il database SQL di Azure identifica potenziali problemi in un database e propone azioni che consentono di migliorare le prestazioni del carico di lavoro tramite azioni e raccomandazioni per l'ottimizzazione intelligente.

## <a name="performance-tuning-options"></a>Opzioni di ottimizzazione delle prestazioni

Opzioni di ottimizzazione delle prestazioni il database SQL di Azure è disponibile nel menu di spostamento del database in "prestazioni intelligenti":

| Opzione di ottimizzazione delle prestazioni | Supporto dei database singoli e in pool | Supporto del database dell'istanza |
| :----------------------------- | ----- | ----- |
| **[Panoramica delle prestazioni](#performance-overview)** : monitorare tutte le attività di prestazioni per il database. | Sì | No | 
| **[Suggerimenti sulle prestazioni](#performance-recommendations)** : Mostra i consigli sulle prestazioni che consentono di migliorare le prestazioni del carico di lavoro. | Sì | No | 
| **[Informazioni dettagliate prestazioni query](#query-performance-insight)** : Mostra le prestazioni delle prime query che utilizzano il database. | Sì | No | 
| **[Ottimizzazione automatica](#automatic-tuning)** : usare il database SQL di Azure per ottimizzare automaticamente le prestazioni del database. | Sì | No | 

## <a name="performance-overview"></a>Panoramica delle prestazioni

Questa vista offre una panoramica delle prestazioni del database e facilita le operazioni di ottimizzazione delle prestazioni e risoluzione dei problemi. 

![Prestazioni](./media/sql-database-performance/performance.png)

* Il riquadro **Raccomandazioni** offre un elenco di raccomandazioni di ottimizzazione per il database (vengono visualizzate le tre raccomandazioni principali se ne sono presenti di più). Se si fa clic su questo riquadro vengono visualizzate le **[Raccomandazioni per le prestazioni](#performance-recommendations)** . 
* Il riquadro **Attività di ottimizzazione** fornisce un riepilogo delle operazioni di ottimizzazione in corso e completate per il database e offre una vista rapida della cronologia delle attività di ottimizzazione. Se si fa clic su questo riquadro viene visualizzata la cronologia di ottimizzazione completa per il database.
* Il riquadro **Ottimizzazione automatica** visualizza la [configurazione di ottimizzazione automatica](sql-database-automatic-tuning-enable.md) per il database, ovvero le opzioni di ottimizzazione applicate automaticamente al database. Se si fa clic su questo riquadro viene visualizzata la finestra di dialogo di configurazione dell'automazione.
* Il riquadro **Query su database** visualizza un riepilogo delle prestazioni delle query per il database (uso complessivo di DTU e query con il maggior consumo di risorse). Se si fa clic su questo riquadro viene visualizzata la pagina **[Informazioni dettagliate prestazioni query](#query-performance-insight)** .

## <a name="performance-recommendations"></a>Raccomandazioni per le prestazioni

Questa pagina offre [raccomandazioni per l'ottimizzazione](sql-database-advisor.md) intelligenti che consentono di migliorare le prestazioni del database. In questa pagina vengono illustrati i tipi seguenti di raccomandazioni:

* Consigli per gli indici da creare o eliminare.
* Consigli su come procedere quando vengono identificati problemi di schema nel database.
* Consigli sui casi in cui trarre vantaggio dalle query con parametri.

![Prestazioni](./media/sql-database-performance/recommendations.png)

È anche possibile trovare la cronologia completa delle azioni di ottimizzazione applicate in precedenza.

Per informazioni su come trovare e applicare le raccomandazioni per le prestazioni, vedere l'articolo [Trovare e applicare raccomandazioni per le prestazioni](sql-database-advisor-portal.md).

## <a name="query-performance-insight"></a>Informazioni dettagliate sulle prestazioni delle query

[Informazioni dettagliate prestazioni query](sql-database-query-performance.md) consente di dedicare meno tempo alla risoluzione dei problemi di prestazioni del database, offrendo i vantaggi seguenti:​

* Informazioni più approfondite sull'utilizzo delle risorse del database (DTU). 
* Query principali a livello di utilizzo di CPU, che possono essere ottimizzate per migliorare le prestazioni. 
* Capacità di eseguire il drill-down nei dettagli di una query. ​ 

  ![dashboard prestazioni](./media/sql-database-query-performance/performance.png)

Altre informazioni su questa pagina sono disponibili nell'articolo **[Usare Informazioni dettagliate prestazioni query](sql-database-query-performance.md)** .

## <a name="automatic-tuning"></a>Ottimizzazione automatica

I database SQL di Azure possono ottimizzare automaticamente le prestazioni del database tramite l'applicazione di [consigli per le prestazioni](sql-database-advisor.md). Per altre informazioni, leggere l'[articolo sull'ottimizzazione automatica](sql-database-automatic-tuning.md) . Per abilitare questa funzionalità, leggere l'[articolo su come abilitare l'ottimizzazione automatica](sql-database-automatic-tuning-enable.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Indicazioni sulle prestazioni del database SQL di Azure per i singoli database](sql-database-performance-guidance.md)
* [Quando usare un pool elastico](sql-database-elastic-pool-guidance.md)
