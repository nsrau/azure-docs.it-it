---
title: Limiti delle risorse vCore - database singolo
description: Questa pagina descrive alcuni limiti comuni delle risorse vCore per un singolo database nel database SQL di Azure.This page describes some common vCore resource limits for a single database in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/11/2020
ms.openlocfilehash: 38d44f5a2ce22de15cb14ea5aa7a9ca1ea7c03cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481066"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>Limiti delle risorse per singoli database che usano il modello di acquisto vCoreResource limits for single databases using the vCore purchasing model

Questo articolo fornisce i limiti dettagliati delle risorse per i singoli database del database SQL di Azure usando il modello di acquisto di vCore.This article provides the detailed resource limits for Azure SQL Database single databases using the vCore purchasing model.

Per i limiti del modello di acquisto DTU per singoli database in un server di database SQL, vedere [Panoramica dei limiti delle risorse in un server di database SQL.](sql-database-resource-limits-database-server.md)

È possibile impostare il livello di servizio, le dimensioni di calcolo e lo spazio di archiviazione per un singolo database usando il [portale di Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), l'[interfaccia della riga di comando di Azure](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases) o l'[API REST](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Per indicazioni e considerazioni sulla scalabilità, consultate [Ridimensionare un singolo database.](sql-database-single-database-scale.md)

## <a name="general-purpose---serverless-compute---gen5"></a>Scopo generale - calcolo senza server - Gen5

Il livello di [calcolo senza server](sql-database-serverless.md) è attualmente disponibile solo nell'hardware Gen5.

### <a name="gen5-compute-generation-part-1"></a>Generazione di elaborazione Gen5 (parte 1)

|Dimensioni di calcolo|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|Generazione di calcolo|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|
|VCore min-max|0.5-1|0.5-2|0.5-4|0.75-6|1.0-8|
|Memoria min-max (GB)|2.02-3|2.05-6|2.10-12|2.25-18|3.00-24|
|Ritardo di pausa automatica in min (minuti)|60|60|60|60|60|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|512|1024|1024|1024|1536|
|Dimensioni massime del log (GB)|154|307|307|307|461|
|Dimensioni massime dati TempDB (GB)|32|64|128|192|256|
|Tipo di archiviazione|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Numero massimo di operazioni di I/O al secondo per i dati|320|640|1280|1920|2560|
|Frequenza massima registro (MBps)|3.8|7.5|15|22,5|30|
|Numero massimo di ruoli di lavoro simultanei (richieste)|75|150|300|450|600|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|
|Numero di repliche|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

\*Il valore massimo per le dimensioni di I/O compreso tra 8 KB e 64 KB. Le operazioni di I/O al secondo effettive dipendono dal carico di lavoro. Per informazioni [dettagliate,](sql-database-resource-limits-database-server.md#resource-governance)vedere Data IO Governance .

### <a name="gen5-compute-generation-part-2"></a>Generazione di elaborazione Gen5 (parte 2)

|Dimensioni di calcolo|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Generazione di calcolo|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|
|VCore min-max|1.25-10|1.50-12|1.75-14|2.00-16|
|Memoria min-max (GB)|3.75-30|4.50-36|5.25-42|6.00-48|
|Ritardo di pausa automatica in min (minuti)|60|60|60|60|
|Supporto per columnstore|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|1536|3072|3072|3072|
|Dimensioni massime del log (GB)|461|461|461|922|
|Dimensioni massime dati TempDB (GB)|320|384|448|512|
|Tipo di archiviazione|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Numero massimo di operazioni di I/O al secondo per i dati|3200|3840|4480|5120|
|Frequenza massima registro (MBps)|30|30|30|30|
|Numero massimo di ruoli di lavoro simultanei (richieste)|750|900|1050|1200|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|
|Numero di repliche|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

\*Il valore massimo per le dimensioni di I/O compreso tra 8 KB e 64 KB. Le operazioni di I/O al secondo effettive dipendono dal carico di lavoro. Per informazioni [dettagliate,](sql-database-resource-limits-database-server.md#resource-governance)vedere Data IO Governance .

## <a name="hyperscale---provisioned-compute---gen4"></a>Hyperscale - provisioned compute - Gen4

### <a name="gen4-compute-generation-part-1"></a>Generazione di elaborazione Gen4 (parte 1)

|Livello di prestazioni|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|Generazione di calcolo|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|
|vCore|1|2|3|4|5|6|
|Memoria (GB)|7|14|21|28|35|42|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Dimensione|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (TB)|100 |100 |100 |100 |100 |100|
|Dimensioni massime del log (TB)|1 |1 |1 |1 |1 |1 |
|Dimensioni massime dati TempDB (GB)|32|64|96|128|160|192|
|Tipo di archiviazione| [Nota 1](#notes) |[Nota 1](#notes)|[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |
|Numero massimo di operazioni di I/O al secondo per i dati|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|
|Frequenza massima registro (MBps)|100 |100 |100 |100 |100 |100 |
|Latenza di I/O (approssimativa)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|600|800|1000|1200|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|
|Repliche secondarie|0-4|0-4|0-4|0-4|0-4|0-4|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|
|Conservazione dell'archiviazione dei backup|7 giorni|7 giorni|7 giorni|7 giorni|7 giorni|7 giorni|
|||

### <a name="gen4-compute-generation-part-2"></a>Generazione di elaborazione Gen4 (parte 2)

|Livello di prestazioni|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|Generazione di calcolo|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|
|vCore|7|8|9|10|16|24|
|Memoria (GB)|49|56|63|70|112|159.5|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Dimensione|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (TB)|100 |100 |100 |100 |100 |100 |
|Dimensioni massime del log (TB)|1 |1 |1 |1 |1 |1 |
|Dimensioni massime dati TempDB (GB)|224|256|288|320|512|768|
|Tipo di archiviazione| [Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |
|Numero massimo di operazioni di I/O al secondo per i dati|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|
|Frequenza massima registro (MBps)|100 |100 |100 |100 |100 |100 |
|Latenza di I/O (approssimativa)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|
|Numero massimo di ruoli di lavoro simultanei (richieste)|1400|1600|1800|2000|3200|4800|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|
|Repliche secondarie|0-4|0-4|0-4|0-4|0-4|0-4|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|
|Conservazione dell'archiviazione dei backup|7 giorni|7 giorni|7 giorni|7 giorni|7 giorni|7 giorni|
|||

\*Il valore massimo per le dimensioni di I/O compreso tra 8 KB e 64 KB. Le operazioni di I/O al secondo effettive dipendono dal carico di lavoro. Per informazioni [dettagliate,](sql-database-resource-limits-database-server.md#resource-governance)vedere Data IO Governance .

## <a name="hyperscale---provisioned-compute---gen5"></a>Hyperscale - provisioned compute - Gen5

### <a name="gen5-compute-generation-part-1"></a>Generazione di elaborazione Gen5 (parte 1)

|Livello di prestazioni|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Generazione di calcolo|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|
|vCore|2|4|6|8|10|12|14|
|Memoria (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Dimensione|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (TB)|100 |100 |100 |100 |100 |100 |100|
|Dimensioni massime del log (TB)|1 |1 |1 |1 |1 |1 |1 |
|Dimensioni massime dati TempDB (GB)|64|128|192|256|320|384|448|
|Tipo di archiviazione| [Nota 1](#notes) |[Nota 1](#notes)|[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |
|Numero massimo di operazioni di I/O al secondo per i dati|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|
|Frequenza massima registro (MBps)|100 |100 |100 |100 |100 |100 |100 |
|Latenza di I/O (approssimativa)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|600|800|1000|1200|1400|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Repliche secondarie|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Conservazione dell'archiviazione dei backup|7 giorni|7 giorni|7 giorni|7 giorni|7 giorni|7 giorni|7 giorni|
|||

\*Il valore massimo per le dimensioni di I/O compreso tra 8 KB e 64 KB. Le operazioni di I/O al secondo effettive dipendono dal carico di lavoro. Per informazioni [dettagliate,](sql-database-resource-limits-database-server.md#resource-governance)vedere Data IO Governance .

### <a name="gen5-compute-generation-part-2"></a>Generazione di elaborazione Gen5 (parte 2)

|Livello di prestazioni|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|Generazione di calcolo|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|
|vCore|16|18|20|24|32|40|80|
|Memoria (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Dimensione|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (TB)|100 |100 |100 |100 |100 |100 |100 |
|Dimensioni massime del log (TB)|1 |1 |1 |1 |1 |1 |1 |
|Dimensioni massime dati TempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo di archiviazione| [Nota 1](#notes) |[Nota 1](#notes)|[Nota 1](#notes)|[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |
|Numero massimo di operazioni di I/O al secondo per i dati|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|
|Frequenza massima registro (MBps)|100 |100 |100 |100 |100 |100 |100 |
|Latenza di I/O (approssimativa)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|
|Numero massimo di ruoli di lavoro simultanei (richieste)|1600|1800|2000|2400|3200|4000|8000|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Repliche secondarie|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Conservazione dell'archiviazione dei backup|7 giorni|7 giorni|7 giorni|7 giorni|7 giorni|7 giorni|7 giorni|
|||

\*Il valore massimo per le dimensioni di I/O compreso tra 8 KB e 64 KB. Le operazioni di I/O al secondo effettive dipendono dal carico di lavoro. Per informazioni [dettagliate,](sql-database-resource-limits-database-server.md#resource-governance)vedere Data IO Governance .

#### <a name="notes"></a>Note

**Nota 1:** Hyperscale è un'architettura a più livelli con componenti di elaborazione e archiviazione separati: [Hyperscale Service Tier Architecture](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)

**Nota 2:** l'architettura a più livelli hyperscale è la memorizzazione nella cache a più livelli. Le operazioni di I/O al secondo valide dipenderanno dal carico di lavoro.

**Nota 3**: La latenza è di 1-2 ms per i dati nella cache basata su SSD RBPEX nelle repliche di calcolo, che memorizza nella cache la maggior parte delle pagine di dati utilizzate. Latenza più elevata per i dati recuperati dai server di paging.

## <a name="general-purpose---provisioned-compute---gen4"></a>Scopo generale - elaborazione di cui è stato eseguito il provisioning - Gen4

> [!IMPORTANT]
> I nuovi database Gen4 non sono più supportati nelle regioni meridionali dell'Australia orientale o del Brasile.

### <a name="gen4-compute-generation-part-1"></a>Generazione di elaborazione Gen4 (parte 1)

|Dimensioni di calcolo|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione di calcolo|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|
|vCore|1|2|3|4|5|6|
|Memoria (GB)|7|14|21|28|35|42|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|1024|1024|1536|1536|1536|3072|
|Dimensioni massime del log (GB)|307|307|461|461|461|922|
|Dimensioni massime dati TempDB (GB)|32|64|96|128|160|192|
|Tipo di archiviazione|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Numero massimo di operazioni di I/O al secondo per i dati|320|640|960|1280|1600|1920|
|Frequenza massima registro (MBps)|3,75|7.5|11,25|15|18.75|22,5|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|600|800|1000|1200|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|
|Numero di repliche|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

\*Il valore massimo per le dimensioni di I/O compreso tra 8 KB e 64 KB. Le operazioni di I/O al secondo effettive dipendono dal carico di lavoro. Per informazioni [dettagliate,](sql-database-resource-limits-database-server.md#resource-governance)vedere Data IO Governance .

### <a name="gen4-compute-generation-part-2"></a>Generazione di elaborazione Gen4 (parte 2)

|Dimensioni di calcolo|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione di calcolo|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|
|vCore|7|8|9|10|16|24|
|Memoria (GB)|49|56|63|70|112|159.5|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|3072|3072|3072|3072|4096|4096|
|Dimensioni massime del log (GB)|922|922|922|922|1229|1229|
|Dimensioni massime dati TempDB (GB)|224|256|288|320|512|768|
|Tipo di archiviazione|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)
|Numero massimo di operazioni di I/O al secondo per i dati|2240|2560|2880|3200|5120|7680|
|Frequenza massima registro (MBps)|26.3|30|30|30|30|30|
|Numero massimo di ruoli di lavoro simultanei (richieste)|1400|1600|1800|2000|3200|4800|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|
|Numero di repliche|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

\*Il valore massimo per le dimensioni di I/O compreso tra 8 KB e 64 KB. Le operazioni di I/O al secondo effettive dipendono dal carico di lavoro. Per informazioni [dettagliate,](sql-database-resource-limits-database-server.md#resource-governance)vedere Data IO Governance .

## <a name="general-purpose---provisioned-compute---gen5"></a>Scopo generale - elaborazione di cui è stato eseguito il provisioning - Gen5General purpose - provisioned compute - Gen5

### <a name="gen5-compute-generation-part-1"></a>Generazione di elaborazione Gen5 (parte 1)

|Dimensioni di calcolo|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generazione di calcolo|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|
|vCore|2|4|6|8|10|12|14|
|Memoria (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|1024|1024|1536|1536|1536|3072|3072|
|Dimensioni massime del log (GB)|307|307|461|461|461|922|922|
|Dimensioni massime dati TempDB (GB)|64|128|192|256|320|384|384|
|Tipo di archiviazione|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Numero massimo di operazioni di I/O al secondo per i dati|640|1280|1920|2560|3200|3840|4480|
|Frequenza massima registro (MBps)|7.5|15|22,5|30|30|30|30|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|600|800|1000|1200|1400|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Numero di repliche|1|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

\*Il valore massimo per le dimensioni di I/O compreso tra 8 KB e 64 KB. Le operazioni di I/O al secondo effettive dipendono dal carico di lavoro. Per informazioni [dettagliate,](sql-database-resource-limits-database-server.md#resource-governance)vedere Data IO Governance .

### <a name="gen5-compute-generation-part-2"></a>Generazione di elaborazione Gen5 (parte 2)

|Dimensioni di calcolo|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generazione di calcolo|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|
|vCore|16|18|20|24|32|40|80|
|Memoria (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|3072|3072|3072|4096|4096|4096|4096|
|Dimensioni massime del log (GB)|922|922|922|1229|1229|1229|1229|
|Dimensioni massime dati TempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo di archiviazione|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Numero massimo di operazioni di I/O al secondo per i dati|5120|5760|6400|7680|10240|12800|25600|
|Frequenza massima registro (MBps)|30|30|30|30|30|30|30|
|Numero massimo di ruoli di lavoro simultanei (richieste)|1600|1800|2000|2400|3200|4000|8000|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Numero di repliche|1|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

\*Il valore massimo per le dimensioni di I/O compreso tra 8 KB e 64 KB. Le operazioni di I/O al secondo effettive dipendono dal carico di lavoro. Per informazioni [dettagliate,](sql-database-resource-limits-database-server.md#resource-governance)vedere Data IO Governance .

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Scopo generale - calcolo di cui è stato eseguito il provisioning - Serie Fsv2

### <a name="fsv2-series-compute-generation-preview"></a>Generazione del calcolo della serie Fsv2 (anteprima)Fsv2-series compute generation (preview)

|Dimensioni di calcolo|GP_Fsv2_72|
|:--- | --: |
|Generazione di calcolo|Serie Fsv2|
|vCore|72|
|Memoria (GB)|136.2|
|Supporto per columnstore|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|
|Dimensioni massime dei dati (GB)|4096|
|Dimensioni massime del log (GB)|1024|
|Dimensioni massime dati TempDB (GB)|333|
|Tipo di archiviazione|SSD remoto|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Numero massimo di operazioni di I/O al secondo per i dati|12.800|
|Frequenza massima registro (MBps)|30|
|Numero massimo di ruoli di lavoro simultanei (richieste)|3600|
|Numero massimo di accessi simultanei|3600|
|Numero massimo di sessioni simultanee|30.000|
|Numero di repliche|1|
|Zone di disponibilità multiple|N/D|
|Scalabilità orizzontale in lettura|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|

\*Il valore massimo per le dimensioni di I/O compreso tra 8 KB e 64 KB. Le operazioni di I/O al secondo effettive dipendono dal carico di lavoro. Per informazioni [dettagliate,](sql-database-resource-limits-database-server.md#resource-governance)vedere Data IO Governance .

## <a name="business-critical---provisioned-compute---gen4"></a>Business critical - elaborazione di cui è stato eseguito il provisioning - Gen4

> [!IMPORTANT]
> I nuovi database Gen4 non sono più supportati nelle regioni meridionali dell'Australia orientale o del Brasile.

### <a name="gen4-compute-generation-part-1"></a>Generazione di elaborazione Gen4 (parte 1)

|Dimensioni di calcolo|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione di calcolo|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|
|vCore|1|2|3|4|5|6|
|Memoria (GB)|7|14|21|28|35|42|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|1|2|3|4|5|6|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Dimensioni massime dei dati (GB)|1024|1024|1024|1024|1024|1024|
|Dimensioni massime del log (GB)|307|307|307|307|307|307|
|Dimensioni massime dati TempDB (GB)|32|64|96|128|160|192|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Numero massimo di operazioni di I/O al secondo per i dati|4.000|8.000|12000|16.000|20.000|24,000|
|Frequenza massima registro (MBps)|8|16|24|32|40|48|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|600|800|1000|1200|
|Numero massimo di accessi simultanei|200|400|600|800|1000|1200|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|
|Numero di repliche|4|4|4|4|4|4|
|Zone di disponibilità multiple|Sì|Sì|Sì|Sì|Sì|Sì|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

\*Il valore massimo per le dimensioni di I/O compreso tra 8 KB e 64 KB. Le operazioni di I/O al secondo effettive dipendono dal carico di lavoro. Per informazioni [dettagliate,](sql-database-resource-limits-database-server.md#resource-governance)vedere Data IO Governance .

### <a name="gen4-compute-generation-part-2"></a>Generazione di elaborazione Gen4 (parte 2)

|Dimensioni di calcolo|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione di calcolo|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|
|vCore|7|8|9|10|16|24|
|Memoria (GB)|49|56|63|70|112|159.5|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|7|8|9,5|11|20|36|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Dimensioni massime dei dati (GB)|1024|1024|1024|1024|1024|1024|
|Dimensioni massime del log (GB)|307|307|307|307|307|307|
|Dimensioni massime dati TempDB (GB)|224|256|288|320|512|768|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Numero massimo di operazioni di I/O al secondo dei datiMax data |28.000|32.000|36,000|40.000|64.000|76.800|
|Frequenza massima registro (MBps)|56|64|64|64|64|64|
|Numero massimo di ruoli di lavoro simultanei (richieste)|1400|1600|1800|2000|3200|4800|
|Numero massimo di accessi simultanei (richieste)Max concurrent logins (requests)|1400|1600|1800|2000|3200|4800|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|
|Numero di repliche|4|4|4|4|4|4|
|Zone di disponibilità multiple|Sì|Sì|Sì|Sì|Sì|Sì|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

\*Il valore massimo per le dimensioni di I/O compreso tra 8 KB e 64 KB. Le operazioni di I/O al secondo effettive dipendono dal carico di lavoro. Per informazioni [dettagliate,](sql-database-resource-limits-database-server.md#resource-governance)vedere Data IO Governance .

## <a name="business-critical---provisioned-compute---gen5"></a>Business critical - elaborazione di cui è stato eseguito il provisioning - Gen5

### <a name="gen5-compute-generation-part-1"></a>Generazione di elaborazione Gen5 (parte 1)

|Dimensioni di calcolo|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generazione di calcolo|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|
|vCore|2|4|6|8|10|12|14|
|Memoria (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|1.57|3,14|4.71|6.28|8.65|11.02|13.39|
|Dimensioni massime dei dati (GB)|1024|1024|1536|1536|1536|3072|3072|
|Dimensioni massime del log (GB)|307|307|461|461|461|922|922|
|Dimensioni massime dati TempDB (GB)|64|128|192|256|320|384|448|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Numero massimo di operazioni di I/O al secondo per i dati|8000|16.000|24,000|32.000|40.000|48,000|56,000|
|Frequenza massima registro (MBps)|24|48|72|96|96|96|96|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|600|800|1000|1200|1400|
|Numero massimo di accessi simultanei|200|400|600|800|1000|1200|1400|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Numero di repliche|4|4|4|4|4|4|4|
|Zone di disponibilità multiple|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

\*Il valore massimo per le dimensioni di I/O compreso tra 8 KB e 64 KB. Le operazioni di I/O al secondo effettive dipendono dal carico di lavoro. Per informazioni [dettagliate,](sql-database-resource-limits-database-server.md#resource-governance)vedere Data IO Governance .

### <a name="gen5-compute-generation-part-2"></a>Generazione di elaborazione Gen5 (parte 2)

|Dimensioni di calcolo|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generazione di calcolo|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|
|vCore|16|18|20|24|32|40|80|
|Memoria (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|15,77|18.14|20,51|25.25|37.94|52.23|131.64|
|Dimensioni massime dei dati (GB)|3072|3072|3072|4096|4096|4096|4096|
|Dimensioni massime del log (GB)|922|922|922|1229|1229|1229|1229|
|Dimensioni massime dati TempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Numero massimo di operazioni di I/O al secondo per i dati|64.000|72,000|80.000|96,000|128,000|160.000|204,800|
|Frequenza massima registro (MBps)|96|96|96|96|96|96|96|
|Numero massimo di ruoli di lavoro simultanei (richieste)|1600|1800|2000|2400|3200|4000|8000|
|Numero massimo di accessi simultanei|1600|1800|2000|2400|3200|4000|8000|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Numero di repliche|4|4|4|4|4|4|4|
|Zone di disponibilità multiple|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

\*Il valore massimo per le dimensioni di I/O compreso tra 8 KB e 64 KB. Le operazioni di I/O al secondo effettive dipendono dal carico di lavoro. Per informazioni [dettagliate,](sql-database-resource-limits-database-server.md#resource-governance)vedere Data IO Governance .

## <a name="business-critical---provisioned-compute---m-series"></a>Business critical - elaborazione con provisioning - serie M

### <a name="m-series-compute-generation-preview"></a>Generazione del calcolo della serie M (anteprima)M-series compute generation (preview)

|Dimensioni di calcolo|BC_M_128|
|:--- | --: |
|Generazione di calcolo|Serie M|
|vCore|128|
|Memoria (GB)|3767.1|
|Supporto per columnstore|Sì|
|Archiviazione OLTP in memoria (GB)|1768|
|Dimensioni massime dei dati (GB)|4096|
|Dimensioni massime del log (GB)|2048|
|Dimensioni massime dati TempDB (GB)|4096|
|Tipo di archiviazione|SSD locale|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Numero massimo di operazioni di I/O al secondo per i dati|160.000|
|Frequenza massima registro (MBps)|264|
|Numero massimo di ruoli di lavoro simultanei (richieste)|12.800|
|Numero massimo di accessi simultanei|12.800|
|Numero massimo di sessioni simultanee|30000|
|Numero di repliche|4|
|Zone di disponibilità multiple|Sì|
|Scalabilità orizzontale in lettura|Sì|
|Archivio di backup incluso|Dimensioni del database 1X|

\*Il valore massimo per le dimensioni di I/O compreso tra 8 KB e 64 KB. Le operazioni di I/O al secondo effettive dipendono dal carico di lavoro. Per informazioni [dettagliate,](sql-database-resource-limits-database-server.md#resource-governance)vedere Data IO Governance .

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere Gestire lo spazio file nel database SQL di Azure.For more information, see [Manage file space in Azure SQL Database.](sql-database-file-space-management.md)

## <a name="next-steps"></a>Passaggi successivi

- Per i limiti delle risorse DTU per un singolo database, vedere Limiti delle risorse per singoli database utilizzando il modello di [acquisto DTUFor DTU resource](sql-database-dtu-resource-limits-single-databases.md) limits for a single database, see resource limits for single databases using the DTU purchasing model
- Per i limiti delle risorse vCore per i pool elastici, vedere Limiti delle risorse per i pool elastici con il modello di [acquisto di vCoreFor vCore resource](sql-database-vcore-resource-limits-elastic-pools.md) limits for elastic pools, see resource limits for elastic pools using the vCore purchasing model
- Per i limiti delle risorse DTU per i pool elastici, vedere Limiti delle risorse per i pool elastici che usano il modello di [acquisto DTUFor DTU resource](sql-database-dtu-resource-limits-elastic-pools.md) limits for elastic pools, see resource limits for elastic pools using the DTU purchasing model
- Per i limiti delle risorse delle istanze gestite, vedere [Limiti delle risorse dell'istanza gestita](sql-database-managed-instance-resource-limits.md).
- Per informazioni sui limiti generici di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Per informazioni sui limiti delle risorse in un server di database a livello di server e sottoscrizione, vedere [Panoramica dei limiti delle risorse in un server di database SQL](sql-database-resource-limits-database-server.md).
