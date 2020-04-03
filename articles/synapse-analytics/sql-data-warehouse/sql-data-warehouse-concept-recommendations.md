---
title: Raccomandazioni per SQL Synapse
description: Informazioni sui consigli di Synapse SQL e su come vengono generati
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: c4bbd98cc28b242be5310fab76521a80de8fcb7c
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584116"
---
# <a name="synapse-sql-recommendations"></a>Raccomandazioni per SQL Synapse

Questo articolo descrive i consigli per Synapse SQL serviti tramite Azure Advisor.This article describes the Synapse SQL recommendations served through Azure Advisor.  

Synapse SQL fornisce consigli per garantire che il carico di lavoro del data warehouse sia ottimizzato in modo coerente per le prestazioni. I suggerimenti sono strettamente integrati con [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) per fornire le procedure consigliate direttamente nel portale di [Azure.](https://aka.ms/Azureadvisor) Synapse SQL raccoglie dati di telemetria e espone i consigli per il carico di lavoro attivo in base a una cadenza giornaliera. Gli scenari di raccomandazione supportati sono descritti di seguito insieme a come applicare le azioni consigliate.

Puoi [controllare i tuoi consigli](https://aka.ms/Azureadvisor) oggi stesso! Questa funzionalità è attualmente applicabile solo ai data warehouse di seconda generazione. 

## <a name="data-skew"></a>Inclinazione dei dati

Durante l'esecuzione del carico di lavoro, l'asimmetria dei dati può causare uno spostamento dati aggiuntivo o colli di bottiglia nelle risorse. La documentazione seguente descrive come identificare le asimmetrie dei dati e come evitarle selezionando una chiave di distribuzione ottimale.

- [Identificare e rimuovere un'asimmetria](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Statistiche non aggiornate o obsolete

La disponibilità di statistiche non ottimali può influire notevolmente sulle prestazioni delle query in quanto può causare la generazione di piani di query non ottimali da parte di SQL Query Optimizer. La documentazione seguente descrive le procedure consigliate per la creazione e l'aggiornamento delle statistiche:

- [Creazione e aggiornamento delle statistiche delle tabelle](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

Per visualizzare l'elenco delle tabelle interessate in base a questi suggerimenti, eseguire il seguente [script T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). Advisor esegue in modo continuo lo stesso script T-SQL per generare tali consigli.

## <a name="replicate-tables"></a>Replicare le tabelle

Per consigli sulla tabella replicata, Advisor rileva candidati di tabella basati sulle caratteristiche fisiche seguenti:

- Dimensione tabella replicata
- Numero di colonne
- Tipo di distribuzione della tabella
- Numero di partizioni

Advisor sfrutta in modo continuativo l'euristica basata sul carico di lavoro, ad esempio frequenza di accesso alla tabella, righe restituite in media e soglie sulle dimensioni e l'attività dei data warehouse, per verificare che vengano generati consigli di alta qualità. 

Di seguito vengono descritti l'euristica basata sul carico di lavoro che è possibile trovare nel portale di Azure per ogni consiglio relativo alla tabella replicata:

- Media analisi: percentuale media di righe restituite dalla tabella per ogni accesso alla tabella negli ultimi sette giorni.
- Lettura frequente, nessun aggiornamento: indica che la tabella non è stata aggiornata negli ultimi sette giorni durante la visualizzazione dell'attività di accesso.
- Percentuale di lettura/aggiornamento: la percentuale della frequenza di accesso alla tabella rispetto all'aggiornamento negli ultimi sette giorni.
- Attività: misura l'utilizzo in base all'attività di accesso. Viene confrontata l'attività di accesso alla tabella rispetto all'attività media di accesso alla tabella nel data warehouse negli ultimi sette giorni. 

Attualmente Advisor visualizza al massimo quattro candidati di tabella replicata alla volta con indici columnstore cluster con priorità per l'attività più elevata.

> [!IMPORTANT]
> I consigli relativi alla tabella replicata non costituiscono una prova completa e non prendono in considerazione operazioni di spostamento dei dati. Microsoft sta lavorando per l'aggiunta di questa opzione come elemento euristico, ma nel frattempo è sempre opportuno convalidare il carico di lavoro dopo aver applicato il consiglio. Contattare sqldwadvisor@service.microsoft.com se si individuano consigli relativi alla tabella replicata che causano una regressione del carico di lavoro. Per altre informazioni sulle tabelle replicate, consultare la [documentazione](https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables#what-is-a-replicated-table) seguente.
