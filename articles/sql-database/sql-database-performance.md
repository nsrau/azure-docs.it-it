---
title: Monitorare e migliorare le prestazioni - Database SQL di Azure | Microsoft Docs
description: "Il database SQL di Azure offre strumenti per le prestazioni che consentono di identificare le aree in cui è possibile migliorare le prestazioni delle query correnti."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: a60b75ac-cf27-4d73-8322-ee4d4c448aa2
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 07/19/2016
ms.author: sstein
ms.openlocfilehash: 49b24619372d41ff25f815ca493ca7e6ce69ef9c
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="monitor-and-improve-performance"></a>Monitorare e migliorare le prestazioni
Il database SQL di Azure identifica potenziali problemi in un database e propone azioni che consentono di migliorare le prestazioni del carico di lavoro tramite azioni e raccomandazioni per l'ottimizzazione intelligente.

Per esaminare le prestazioni dei database, usare il riquadro **Prestazioni** nella pagina Informazioni generali oppure passare alla sezione "Supporto e risoluzione dei problemi":

   ![Visualizzare le prestazioni](./media/sql-database-performance/entries.png)

Nella sezione "Supporto e risoluzione dei problemi" è possibile usare le pagine seguenti:


1. [Informazioni generali sulle prestazioni](#performance-overview) per monitorare le prestazioni del database. 
2. [Raccomandazioni per le prestazioni](#performance-recommendations) per trovare suggerimenti relativi alle prestazioni che possono migliorare le prestazioni del carico di lavoro.
3. [Informazioni dettagliate prestazioni query](#query-performance-insight) per trovare le query con il maggior consumo di risorse.
4. [Ottimizzazione automatica](#automatic-tuning) per consentire al database SQL di Azure di ottimizzare automaticamente il database.

## <a name="performance-overview"></a>Panoramica sulle prestazioni
Questa vista offre una panoramica delle prestazioni del database e facilita le operazioni di ottimizzazione delle prestazioni e risoluzione dei problemi. 

![Prestazioni](./media/sql-database-performance/performance.png)

* Il riquadro **Raccomandazioni** offre un elenco di raccomandazioni di ottimizzazione per il database (vengono visualizzate le tre raccomandazioni principali se ne sono presenti di più). Se si fa clic su questo riquadro vengono visualizzate le **[Raccomandazioni per le prestazioni](#performance-recommendations)**. 
* Il riquadro **Attività di ottimizzazione** fornisce un riepilogo delle operazioni di ottimizzazione in corso e completate per il database e offre una vista rapida della cronologia delle attività di ottimizzazione. Se si fa clic su questo riquadro viene visualizzata la cronologia di ottimizzazione completa per il database.
* Il riquadro **Ottimizzazione automatica** visualizza la [configurazione di ottimizzazione automatica](sql-database-automatic-tuning-enable.md) per il database, ovvero le opzioni di ottimizzazione applicate automaticamente al database. Se si fa clic su questo riquadro viene visualizzata la finestra di dialogo di configurazione dell'automazione.
* Il riquadro **Query su database** visualizza un riepilogo delle prestazioni delle query per il database (uso complessivo di DTU e query con il maggior consumo di risorse). Se si fa clic su questo riquadro viene visualizzata la pagina **[Informazioni dettagliate prestazioni query](#query-performance-insight)**.

## <a name="performance-recommendations"></a>Raccomandazioni per le prestazioni
Questa pagina offre [raccomandazioni per l'ottimizzazione](sql-database-advisor.md) intelligenti che consentono di migliorare le prestazioni del database. In questa pagina vengono illustrati i tipi seguenti di raccomandazioni:

* Consigli per gli indici da creare o eliminare.
* Consigli su come procedere quando vengono identificati problemi di schema nel database.
* Consigli sui casi in cui trarre vantaggio dalle query con parametri.

![Prestazioni](./media/sql-database-performance/recommendations.png)

È anche possibile trovare la cronologia completa delle azioni di ottimizzazione applicate in precedenza.

Per informazioni su come trovare e applicare le raccomandazioni per le prestazioni, vedere l'articolo [Trovare e applicare raccomandazioni per le prestazioni](sql-database-advisor-portal.md).

## <a name="automatic-tuning"></a>Ottimizzazione automatica
Il database SQL di Azure può ottimizzare automaticamente le prestazioni del database tramite l'applicazione di [raccomandazioni per le prestazioni](sql-database-advisor.md). Per altre informazioni, leggere l'[articolo sull'ottimizzazione automatica](sql-database-automatic-tuning.md) . Per abilitare questa funzionalità, leggere l'[articolo su come abilitare l'ottimizzazione automatica](sql-database-automatic-tuning-enable.md).

## <a name="query-performance-insight"></a>Informazioni dettagliate prestazioni query
[Informazioni dettagliate prestazioni query](sql-database-query-performance.md) consente di dedicare meno tempo alla risoluzione dei problemi di prestazioni del database, offrendo i vantaggi seguenti:​

* Informazioni più approfondite sull'utilizzo delle risorse del database (DTU). 
* Query principali a livello di utilizzo di CPU, che possono essere ottimizzate per migliorare le prestazioni. 
* Capacità di eseguire il drill-down nei dettagli di una query. ​ 

  ![dashboard prestazioni](./media/sql-database-query-performance/performance.png)

Altre informazioni su questa pagina sono disponibili nell'articolo **[Usare Informazioni dettagliate prestazioni query](sql-database-query-performance.md)**.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Indicazioni sulle prestazioni del database SQL di Azure per i singoli database](sql-database-performance-guidance.md)
* [Quando usare un pool elastico](sql-database-elastic-pool-guidance.md)

