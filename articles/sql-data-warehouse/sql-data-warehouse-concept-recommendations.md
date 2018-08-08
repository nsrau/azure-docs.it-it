---
title: Consigli di SQL Data Warehouse- Concetti | Microsoft Docs
description: Informazioni sui consigli di SQL Data Warehouse e su come vengono generati
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/27/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 2cbd691c29039c65b98d8b0191e9e278d2440f09
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39347958"
---
# <a name="sql-data-warehouse-recommendations"></a>Consigli di SQL Data Warehouse

Questo articolo descrive i consigli offerti da SQL Data Warehouse tramite Azure Advisor.  

SQL Data Warehouse offre consigli in grado di garantire l'ottimizzazione costante delle prestazioni del data warehouse in uso. I consigli di Data Warehouse sono strettamente integrati con [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) per presentare le procedure consigliate all'utente direttamente all'interno del [portale di Azure](https://aka.ms/Azureadvisor). SQL Data Warehouse analizza lo stato corrente del data warehouse, raccoglie i dati di telemetria e visualizza consigli per il carico di lavoro attivo con frequenza giornaliera. Di seguito sono descritti gli scenari supportati per i consigli sul data warehouse dati supportati ed è anche descritto come mettere in pratica le azioni consigliate.

In caso di commenti e suggerimenti su SQL Data Warehouse Advisor o se si riscontrano problemi, contattare l'indirizzo [sqldwadvisor@service.microsoft.com](mailto:sqldwadvisor@service.microsoft.com).   

Fare clic [qui](https://aka.ms/Azureadvisor) per visualizzare subito i consigli. Questa funzionalità è attualmente applicabile solo ai data warehouse di seconda generazione. 

## <a name="data-skew"></a>Asimmetria dei dati

Durante l'esecuzione del carico di lavoro, l'asimmetria dei dati può causare uno spostamento dati aggiuntivo o colli di bottiglia nelle risorse. La documentazione seguente descrive come identificare le asimmetrie dei dati e come evitarle selezionando una chiave di distribuzione ottimale.

- [Identificare e rimuovere un'asimmetria](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Statistiche mancanti o non aggiornate

Statistiche non ottimali possono influire notevolmente sulle prestazioni delle query, poiché possono indurre Query Optimizer di SQL Data Warehouse a generare piani di query non ottimali. La documentazione seguente descrive le procedure consigliate per la creazione e l'aggiornamento delle statistiche:

- [Creazione e aggiornamento delle statistiche delle tabelle](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistic)

Per questi due consigli, Advisor esegue costantemente lo [script T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables) seguente per identificare le tabelle interessate dai consigli sull'asimmetria e sulle statistiche.
