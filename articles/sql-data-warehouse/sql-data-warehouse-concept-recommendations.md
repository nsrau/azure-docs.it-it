---
title: Consigli di SQL Data Warehouse- Concetti | Microsoft Docs
description: Informazioni sui consigli di SQL Data Warehouse e su come vengono generati
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/05/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b275f23209979e1a8068ecd99465f7b52392bc6c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082909"
---
# <a name="sql-data-warehouse-recommendations"></a>Consigli di SQL Data Warehouse

Questo articolo descrive i consigli offerti da SQL Data Warehouse tramite Azure Advisor.  

SQL Data Warehouse offre consigli in grado di garantire l'ottimizzazione costante delle prestazioni del data warehouse in uso. Gli elementi consigliati di data warehouse sono strettamente integrati con [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) per presentare le procedure consigliate direttamente all'interno del [portale di Azure](https://aka.ms/Azureadvisor). SQL Data Warehouse analizza lo stato corrente del data warehouse, raccoglie i dati di telemetria e visualizza consigli per il carico di lavoro attivo con frequenza giornaliera. Di seguito sono descritti gli scenari supportati per i consigli sul data warehouse dati supportati ed è anche descritto come mettere in pratica le azioni consigliate.

In caso di feedback su Azure SQL Data Warehouse Advisor o se si riscontrano problemi, contattare[sqldwadvisor@service.microsoft.com](mailto:sqldwadvisor@service.microsoft.com).   

Fare clic [qui](https://aka.ms/Azureadvisor) per visualizzare subito i consigli. Questa funzionalità è attualmente applicabile solo ai data warehouse di seconda generazione. 

## <a name="data-skew"></a>Asimmetria dei dati

Durante l'esecuzione del carico di lavoro, l'asimmetria dei dati può causare uno spostamento dati aggiuntivo o colli di bottiglia nelle risorse. La documentazione seguente descrive come identificare le asimmetrie dei dati e come evitarle selezionando una chiave di distribuzione ottimale.

- [Identificare e rimuovere un'asimmetria](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Statistiche mancanti o non aggiornate

Statistiche non ottimali possono influire notevolmente sulle prestazioni delle query, poiché possono indurre Query Optimizer di SQL Data Warehouse a generare piani di query non ottimali. La documentazione seguente descrive le procedure consigliate per la creazione e l'aggiornamento delle statistiche:

- [Creazione e aggiornamento delle statistiche delle tabelle](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

Per visualizzare l'elenco delle tabelle interessate da questi consigli, eseguire il comando [script T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables) seguente. Advisor esegue in modo continuo lo stesso script T-SQL per generare tali consigli.

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
