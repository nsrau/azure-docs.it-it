---
title: Consigli di Azure Advisor sulle prestazioni | Microsoft Docs
description: Usare Advisor per ottimizzare le prestazioni delle distribuzioni di Azure.
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: makohli
ms.openlocfilehash: 9516534216c4a2c0f61e33ea3cbf1bbcb2ab58c7
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2018
ms.locfileid: "44301311"
---
# <a name="advisor-performance-recommendations"></a>Consigli di Advisor sulle prestazioni

Grazie ai consigli di Azure Advisor sulle prestazioni, è possibile migliorare e aumentare la velocità e la reattività delle applicazioni aziendali critiche. È possibile ottenere da Advisor consigli sulle prestazioni nella scheda **Prestazioni** scheda del dashboard di Advisor.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Ridurre la durata DNS nel profilo di Gestione traffico per eseguire più rapidamente il failover a endpoint integri

Le [Impostazioni della durata (TTL)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) nel profilo di Gestione traffico consentono di specificare la velocità con cui viene cambiato endpoint, nel caso in cui un determinato endpoint smette di rispondere alle query. Ridurre i valori di durata (TTL) significa che i client verranno indirizzati più rapidamente agli endpoint funzionanti.

Azure Advisor identifica i profili di Gestione Traffico con una durata configurata maggiore e consiglia di configurare la durata (TTL) su 20 o 60 secondi a seconda che il profilo sia configurato o no per il [failover rapido](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Migliorare le prestazioni del database con Advisor per database SQL

Advisor fornisce una visualizzazione coerente e consolidata dei consigli per tutte le risorse di Azure. Si integra con Advisor per database SQL per offrire consigli su come migliorare le prestazioni del database SQL di Azure. Advisor per database SQL consente di valutare le prestazioni dei database SQL di Azure analizzando la cronologia relativa all'uso. Offre quindi consigli ideali per l'esecuzione del carico di lavoro tipico del database. 

> [!NOTE]
> Per ottenere consigli, è necessario che un database sia stato in uso per circa una settimana, nel corso della quale deve essersi verificata attività coerente. Advisor per database SQL può essere più facilmente ottimizzato per modelli di query coerenti anziché picchi irregolari casuali di attività.

Per altre informazioni su Advisor per database SQL, vedere [Advisor per database SQL](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="improve-redis-cache-performance-and-reliability"></a>Migliorare affidabilità e prestazioni di Cache Redis

Advisor identifica le istanze di Cache Redis in cui le prestazioni potrebbero aver subito effetti negativi a causa di uso di memoria, carico del server, larghezza di banda di rete e numero di connessioni client elevati. Advisor fornisce inoltre consigli sulle procedure consigliate che consentono di evitare potenziali problemi. Per altre informazioni relative ai consigli su Cache Redis, vedere [Redis Cache Advisor](https://azure.microsoft.com/documentation/articles/cache-configure/#redis-cache-advisor).


## <a name="improve-app-service-performance-and-reliability"></a>Migliorare affidabilità e prestazioni di Servizi app

Azure Advisor integra consigli sulle procedure consigliate per migliorare l'esperienza di Servizi app e scoprire le funzionalità della piattaforma pertinente. Esempi di consigli per Servizi app sono:
* Rilevamento delle istanze in cui le risorse di CPU o memoria vengono esaurite dai runtime delle app con opzioni di migrazione.
* Rilevamento delle istanze in cui la collocazione delle risorse come App Web e database può aumentare le prestazioni e ridurre il costo. 

Per altre informazioni sui consigli per Servizi app, vedere [Procedure consigliate per Servizio app di Azure](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Rimuovere l'asimmetria dei dati sulla tabella di SQL data warehouse per aumentare le prestazioni delle query

Durante l'esecuzione del carico di lavoro, l'asimmetria dei dati può causare uno spostamento dati inutile o colli di bottiglia nelle risorse. Advisor rileverà un'asimmetria dei dati della distribuzione maggiore del 15% e consiglierà di ridistribuire i dati e rivedere le selezioni della chiave di distribuzione della tabella. Per altre informazioni sull'identificazione e la rimozione dell'asimmetria, vedere [risoluzione dei problemi di asimmetria](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Creare o aggiornare le statistiche della tabella obsolete nella tabella di SQL data warehouse per aumentare le prestazioni delle query

Advisor identifica le tabelle che non hanno [statistiche della tabella](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) aggiornate e consiglia di crearle o di aggiornarle. Query Optimizer di SQL data warehouse usa le statistiche aggiornate per stimare la cardinalità o il numero di righe nel risultato della query al fine di creare un piano di query di qualità elevata per prestazioni ottimali.

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Eseguire la migrazione dell'account di archiviazione ad Azure Resource Manager per ottenere tutte le funzionalità di Azure più recenti

Eseguire la migrazione del modello di distribuzione dell'account di archiviazione ad Azure Resource Manager (ARM) per usufruire delle distribuzioni di modelli, di maggiori opzioni di sicurezza e della possibilità di eseguire l'aggiornamento a un account GPv2 per l'utilizzo delle funzionalità più recenti di Archiviazione di Azure. Advisor identifica eventuali account di archiviazione autonomi che usano il modello di distribuzione classica e consiglia di eseguire la migrazione al modello di distribuzione ARM. 

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Come accedere ai consigli sulle prestazioni in Advisor

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi aprire [Advisor](https://aka.ms/azureadvisordashboard).

2.  Nel dashboard di Advisor fare clic sulla scheda **Prestazioni**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:

* [Introduction to Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [Introduzione ad Advisor](advisor-get-started.md)
* [Advisor Cost recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sui costi)
* [Advisor High Availability recommendations](advisor-high-availability-recommendations.md) (Consigli di Advisor sulla disponibilità elevata)
* [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)

