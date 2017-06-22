---
title: Informazioni dettagliate sulle prestazioni del database SQL di Azure | Documentazione Microsoft
description: "Il database SQL di Azure offre strumenti per le prestazioni che consentono di identificare le aree in cui è possibile migliorare le prestazioni delle query correnti."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: a60b75ac-cf27-4d73-8322-ee4d4c448aa2
ms.service: sql-database
ms.custom: monitor & manage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 07/19/2016
ms.author: sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 071cc504cc4111e20391af86ffdc7819644ec434
ms.contentlocale: it-it
ms.lasthandoff: 12/14/2016


---
# <a name="sql-database-performance-insight"></a>Informazioni dettagliate sulle prestazioni del database SQL
Il database SQL di Azure offre strumenti per le prestazioni che consentono di identificare e migliorare le prestazioni dei database, con suggerimenti e operazioni per l'ottimizzazione intelligente. 

1. Selezionare il database nel [portale di Azure](http://portal.azure.com) e fare clic su **Tutte le impostazioni** > **Prestazioni ** > **Panoramica** per aprire la pagina **Prestazioni**. 
2. Fare clic su **Raccomandazioni** per aprire [SQL Database Advisor](#sql-database-advisor) e quindi fare clic su **Query** per aprire [Informazioni dettagliate prestazioni query](#query-performance-insight).
   
    ![Visualizzare le prestazioni](./media/sql-database-performance/entries.png)

## <a name="performance-overview"></a>Panoramica sulle prestazioni
Facendo clic su **Panoramica** o sul riquadro **Prestazioni** viene visualizzato il dashboard delle prestazioni del database. Questa vista offre una panoramica delle prestazioni del database e facilita le operazioni di ottimizzazione delle prestazioni e risoluzione dei problemi. 

![Prestazioni](./media/sql-database-performance/performance.png)

* Il riquadro **Raccomandazioni** offre un elenco di raccomandazioni di ottimizzazione per il database (se sono presenti più di 3 raccomandazioni, vengono visualizzati le prime tre). Se si fa clic su questo riquadro viene visualizzato **Advisor per database SQL**. 
* Il riquadro **Attività di ottimizzazione** fornisce un riepilogo delle operazioni di ottimizzazione in corso e completate per il database e offre una vista rapida della cronologia delle attività di ottimizzazione. Se si fa clic su questo riquadro viene visualizzata la cronologia di ottimizzazione completa per il database.
* Il riquadro **Ottimizzazione automatica** visualizza la configurazione di ottimizzazione automatica per il database (le operazioni di ottimizzazione configurate per l'esecuzione automatica nel database). Se si fa clic su questo riquadro viene visualizzata la finestra di dialogo di configurazione dell'automazione.
* Il riquadro **Query su database** visualizza un riepilogo delle prestazioni delle query per il database (uso complessivo di DTU e query con il maggior consumo di risorse). Se si fa clic su questo riquadro vengono visualizzate le **informazioni dettagliate prestazioni query**.

## <a name="sql-database-advisor"></a>Advisor per database SQL
[Advisor per database SQL](sql-database-advisor.md) offre raccomandazioni di ottimizzazione intelligenti che consentono di migliorare le prestazioni del database. 

* Consigli sugli indici da creare o eliminare e un'opzione per applicare i consigli relativi agli indici automaticamente senza alcuna interazione dell'utente e per eseguire il rollback automatico dei consigli che hanno un impatto negativo sulle prestazioni.
* Consigli su come procedere quando vengono identificati problemi di schema nel database.
* Consigli sui casi in cui trarre vantaggio dalle query con parametri.

## <a name="query-performance-insight"></a>Informazioni dettagliate prestazioni query
[Informazioni dettagliate prestazioni query](sql-database-query-performance.md) consente di dedicare meno tempo alla risoluzione dei problemi di prestazioni del database, offrendo i vantaggi seguenti:​

* Informazioni più approfondite sull'utilizzo delle risorse del database (DTU). 
* Query principali a livello di utilizzo di CPU, che possono essere ottimizzate per migliorare le prestazioni. 
* Capacità di eseguire il drill-down nei dettagli di una query. ​ 

## <a name="additional-resources"></a>Risorse aggiuntive
* [Indicazioni sulle prestazioni del database SQL di Azure per i singoli database](sql-database-performance-guidance.md)
* [Quando usare un pool elastico](sql-database-elastic-pool-guidance.md)


