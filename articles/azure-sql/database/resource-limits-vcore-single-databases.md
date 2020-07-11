---
title: Limiti delle risorse di vCore per database singolo
description: Questa pagina descrive alcuni limiti di risorse vCore comuni per un singolo database nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 07/09/2020
ms.openlocfilehash: add2e0cc2852f9ab0b63565841f670ed6c53d9a7
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206127"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>Limiti delle risorse per i singoli database che usano il modello di acquisto vCore
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Questo articolo fornisce i limiti dettagliati delle risorse per i singoli database nel database SQL di Azure usando il modello di acquisto vCore.

Per i limiti del modello di acquisto di DTU per database singoli in un server, vedere [Panoramica dei limiti delle risorse in un server](resource-limits-logical-server.md).

È possibile impostare il livello di servizio, le dimensioni di calcolo (obiettivo di servizio) e la quantità di spazio di archiviazione per un singolo database usando il [portale di Azure](single-database-manage.md#the-azure-portal), [Transact-SQL](single-database-manage.md#transact-sql-t-sql), [PowerShell](single-database-manage.md#powershell), l'interfaccia della riga di comando di [Azure](single-database-manage.md#the-azure-cli)o l' [API REST](single-database-manage.md#rest-api).

> [!IMPORTANT]
> Per indicazioni e considerazioni sulla scalabilità, vedere [ridimensionare un singolo database](single-database-scale.md).

## <a name="general-purpose---serverless-compute---gen5"></a>Utilizzo generico-calcolo senza server-quinta generazione

Il [livello di calcolo senza server](serverless-tier-overview.md) è attualmente disponibile solo nell'hardware quinta generazione.

### <a name="gen5-compute-generation-part-1"></a>Generazione di calcolo quinta generazione (parte 1)

|Dimensioni di calcolo (obiettivo di servizio)|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|Generazione di calcolo|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|
|VCore min-max|0.5-1|0.5-2|0,5-4|0,75-6|1.0-8|
|Memoria min-max (GB)|2.02-3|2.05-6|2.10-12|2,25-18|3.00-24|
|Ritardo di sospensione automatico min-max (minuti)|60-10080|60-10080|60-10080|60-10080|60-10080|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|512|1024|1024|1024|1536|
|Dimensioni massime del log (GB)|154|307|307|307|461|
|Dimensioni massime dati TempDB (GB)|32|64|128|192|256|
|Tipo di archiviazione|Unità SSD remota|Unità SSD remota|Unità SSD remota|Unità SSD remota|Unità SSD remota|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Numero massimo di IOPS dati *|320|640|1280|1920|2560|
|Velocità massima log (MBps)|3.8|7,5|15|22,5|30|
|Numero massimo di ruoli di lavoro simultanei (richieste)|75|150|300|450|600|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|
|Numero di repliche|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

\*Il valore massimo per le dimensioni di i/o comprese tra 8 KB e 64 KB. Gli IOPS effettivi sono dipendenti dal carico di lavoro. Per informazioni dettagliate, vedere [governance io di dati](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Generazione di calcolo quinta generazione (parte 2)

|Dimensioni di calcolo (obiettivo di servizio)|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Generazione di calcolo|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|
|VCore min-max|1,25-10|1,50-12|1,75-14|2.00-16|
|Memoria min-max (GB)|3,75-30|4,50-36|5,25-42|6.00-48|
|Ritardo di sospensione automatico min-max (minuti)|60-10080|60-10080|60-10080|60-10080|
|Supporto per columnstore|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|1536|3072|3072|3072|
|Dimensioni massime del log (GB)|461|461|461|922|
|Dimensioni massime dati TempDB (GB)|320|384|448|512|
|Tipo di archiviazione|Unità SSD remota|Unità SSD remota|Unità SSD remota|Unità SSD remota|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Numero massimo di IOPS dati *|3200|3840|4480|5120|
|Velocità massima log (MBps)|30|30|30|30|
|Numero massimo di ruoli di lavoro simultanei (richieste)|750|900|1050|1200|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|
|Numero di repliche|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

\*Il valore massimo per le dimensioni di i/o comprese tra 8 KB e 64 KB. Gli IOPS effettivi sono dipendenti dal carico di lavoro. Per informazioni dettagliate, vedere [governance io di dati](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-3"></a>Generazione calcolo quinta generazione (parte 3)

|Dimensioni di calcolo (obiettivo di servizio)|GP_S_Gen5_18|GP_S_Gen5_20|GP_S_Gen5_24|GP_S_Gen5_32|GP_S_Gen5_40|
|:--- | --: |--: |--: |--: |--:|
|Generazione di calcolo|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|
|VCore min-max|2,25-18|2,5-20|3-24|4-32|5-40|
|Memoria min-max (GB)|6.75-54|7.5-60|9-72|12-96|15-120|
|Ritardo di sospensione automatico min-max (minuti)|60-10080|60-10080|60-10080|60-10080|60-10080|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|3072|3072|4096|4096|4096|
|Dimensioni massime del log (GB)|922|922|1229|1229|1229|
|Dimensioni massime dati TempDB (GB)|576|640|768|1024|1280|
|Tipo di archiviazione|Unità SSD remota|Unità SSD remota|Unità SSD remota|Unità SSD remota|Unità SSD remota|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Numero massimo di IOPS dati *|5760|6400|7680|10240|12800|
|Velocità massima log (MBps)|30|30|30|30|30|
|Numero massimo di ruoli di lavoro simultanei (richieste)|1350|1500|1800|2400|3000|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|
|Numero di repliche|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

\*Il valore massimo per le dimensioni di i/o comprese tra 8 KB e 64 KB. Gli IOPS effettivi sono dipendenti dal carico di lavoro. Per informazioni dettagliate, vedere [governance io di dati](resource-limits-logical-server.md#resource-governance).


## <a name="hyperscale---provisioned-compute---gen4"></a>Calcolo con provisioning iperscalare-Gen4

### <a name="gen4-compute-generation-part-1"></a>Generazione di calcolo Gen4 (parte 1)

|Dimensioni di calcolo (obiettivo di servizio)|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|Generazione di calcolo|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|
|vCore|1|2|3|4|5|6|
|Memoria (GB)|7|14|21|28|35|42|
|[RBPEX](service-tier-hyperscale.md#compute) Dimensioni|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (TB)|100 |100 |100 |100 |100 |100|
|Dimensioni massime del log (TB)|Nessuna limitazione |Nessuna limitazione |Nessuna limitazione |Nessuna limitazione |Nessuna limitazione |Nessuna limitazione |
|Dimensioni massime dati TempDB (GB)|32|64|96|128|160|192|
|Tipo di archiviazione| [Nota 1](#notes) |[Nota 1](#notes)|[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |
|Numero massimo di IOPS SSD locali *|4000 |8000 |12000 |16000 |20000 |24000 |
|Velocità massima log (MBps)|100 |100 |100 |100 |100 |100 |
|Latenza di I/O (approssimativa)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|600|800|1000|1200|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|
|Repliche secondarie|0-4|0-4|0-4|0-4|0-4|0-4|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|
|Conservazione dell'archiviazione di backup|7 giorni|7 giorni|7 giorni|7 giorni|7 giorni|7 giorni|
|||

\*Oltre all'i/o SSD locale, i carichi di lavoro utilizzeranno i/o del [server della pagina](service-tier-hyperscale.md#page-server) remota I IOPS effettivi dipendono dal carico di lavoro. Per informazioni dettagliate, vedere la pagina relativa alla [governance](resource-limits-logical-server.md#resource-governance)dei dati e i/o [dei dati nelle statistiche sull'utilizzo delle risorse](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

### <a name="gen4-compute-generation-part-2"></a>Generazione di calcolo Gen4 (parte 2)

|Dimensioni di calcolo (obiettivo di servizio)|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|Generazione di calcolo|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|
|vCore|7|8|9|10|16|24|
|Memoria (GB)|49|56|63|70|112|159,5|
|[RBPEX](service-tier-hyperscale.md#compute) Dimensioni|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (TB)|100 |100 |100 |100 |100 |100 |
|Dimensioni massime del log (TB)|Nessuna limitazione |Nessuna limitazione |Nessuna limitazione |Nessuna limitazione |Nessuna limitazione |Nessuna limitazione |
|Dimensioni massime dati TempDB (GB)|224|256|288|320|512|768|
|Tipo di archiviazione| [Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |
|Numero massimo di IOPS SSD locali *|28000 |32000 |36000 |40000 |64000 |76800 |
|Velocità massima log (MBps)|100 |100 |100 |100 |100 |100 |
|Latenza di I/O (approssimativa)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|
|Numero massimo di ruoli di lavoro simultanei (richieste)|1400|1600|1800|2000|3200|4800|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|
|Repliche secondarie|0-4|0-4|0-4|0-4|0-4|0-4|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|
|Conservazione dell'archiviazione di backup|7 giorni|7 giorni|7 giorni|7 giorni|7 giorni|7 giorni|
|||

\*Oltre all'i/o SSD locale, i carichi di lavoro utilizzeranno i/o del [server della pagina](service-tier-hyperscale.md#page-server) remota I IOPS effettivi dipendono dal carico di lavoro. Per informazioni dettagliate, vedere la pagina relativa alla [governance](resource-limits-logical-server.md#resource-governance)dei dati e i/o [dei dati nelle statistiche sull'utilizzo delle risorse](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

## <a name="hyperscale---provisioned-compute---gen5"></a>Calcolo con provisioning iperscalare-quinta generazione

### <a name="gen5-compute-generation-part-1"></a>Generazione di calcolo quinta generazione (parte 1)

|Dimensioni di calcolo (obiettivo di servizio)|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Generazione di calcolo|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|
|vCore|2|4|6|8|10|12|14|
|Memoria (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|[RBPEX](service-tier-hyperscale.md#compute) Dimensioni|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (TB)|100 |100 |100 |100 |100 |100 |100|
|Dimensioni massime del log (TB)|Nessuna limitazione |Nessuna limitazione |Nessuna limitazione |Nessuna limitazione |Nessuna limitazione |Nessuna limitazione |Nessuna limitazione |
|Dimensioni massime dati TempDB (GB)|64|128|192|256|320|384|448|
|Tipo di archiviazione| [Nota 1](#notes) |[Nota 1](#notes)|[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |
|Numero massimo di IOPS SSD locali *|8000 |16000 |24000 |32000 |40000 |48000 |56000 |
|Velocità massima log (MBps)|100 |100 |100 |100 |100 |100 |100 |
|Latenza di I/O (approssimativa)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|600|800|1000|1200|1400|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Repliche secondarie|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Conservazione dell'archiviazione di backup|7 giorni|7 giorni|7 giorni|7 giorni|7 giorni|7 giorni|7 giorni|
|||

\*Oltre all'i/o SSD locale, i carichi di lavoro utilizzeranno i/o del [server della pagina](service-tier-hyperscale.md#page-server) remota I IOPS effettivi dipendono dal carico di lavoro. Per informazioni dettagliate, vedere la pagina relativa alla [governance](resource-limits-logical-server.md#resource-governance)dei dati e i/o [dei dati nelle statistiche sull'utilizzo delle risorse](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

### <a name="gen5-compute-generation-part-2"></a>Generazione di calcolo quinta generazione (parte 2)

|Dimensioni di calcolo (obiettivo di servizio)|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|Generazione di calcolo|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|
|vCore|16|18|20|24|32|40|80|
|Memoria (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|[RBPEX](service-tier-hyperscale.md#compute) Dimensioni|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|Memoria 3X|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (TB)|100 |100 |100 |100 |100 |100 |100 |
|Dimensioni massime del log (TB)|Nessuna limitazione |Nessuna limitazione |Nessuna limitazione |Nessuna limitazione |Nessuna limitazione |Nessuna limitazione |Nessuna limitazione |
|Dimensioni massime dati TempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo di archiviazione| [Nota 1](#notes) |[Nota 1](#notes)|[Nota 1](#notes)|[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |
|Numero massimo di IOPS SSD locali *|64000 |72000 |80000 |96000 |160000 |192000 |204800 |
|Velocità massima log (MBps)|100 |100 |100 |100 |100 |100 |100 |
|Latenza di I/O (approssimativa)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|
|Numero massimo di ruoli di lavoro simultanei (richieste)|1600|1800|2000|2400|3200|4000|8000|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Repliche secondarie|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Conservazione dell'archiviazione di backup|7 giorni|7 giorni|7 giorni|7 giorni|7 giorni|7 giorni|7 giorni|
|||

\*Oltre all'i/o SSD locale, i carichi di lavoro utilizzeranno i/o del [server della pagina](service-tier-hyperscale.md#page-server) remota I IOPS effettivi dipendono dal carico di lavoro. Per informazioni dettagliate, vedere la pagina relativa alla [governance](resource-limits-logical-server.md#resource-governance)dei dati e i/o [dei dati nelle statistiche sull'utilizzo delle risorse](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

#### <a name="notes"></a>Note

**Nota 1**: l'iperscalabilità è un'architettura a più livelli con componenti di calcolo e archiviazione distinti: [architettura del livello di servizio con iperscalabilità](service-tier-hyperscale.md#distributed-functions-architecture)

**Nota 2**: la latenza è 1-2 ms per i dati nell'unità SSD della replica di calcolo locale, che memorizza nella cache le pagine di dati più usate. Latenza più elevata per i dati recuperati dai server delle pagine.

## <a name="general-purpose---provisioned-compute---gen4"></a>Calcolo per utilizzo generico-Gen4

> [!IMPORTANT]
> I nuovi database Gen4 non sono più supportati nelle aree dell'Australia orientale o del Brasile meridionale.

### <a name="gen4-compute-generation-part-1"></a>Generazione di calcolo Gen4 (parte 1)

|Dimensioni di calcolo (obiettivo di servizio)|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione di calcolo|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|
|vCore|1|2|3|4|5|6|
|Memoria (GB)|7|14|21|28|35|42|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|1024|1024|1536|1536|1536|3072|
|Dimensioni massime del log (GB)|307|307|461|461|461|922|
|Dimensioni massime dati TempDB (GB)|32|64|96|128|160|192|
|Tipo di archiviazione|Unità SSD remota|Unità SSD remota|Unità SSD remota|Unità SSD remota|Unità SSD remota|Unità SSD remota|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Numero massimo di IOPS dati *|320|640|960|1280|1600|1920|
|Velocità massima log (MBps)|3,75|7,5|11,25|15|18,75|22,5|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|600|800|1000|1200|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|
|Numero di repliche|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

\*Il valore massimo per le dimensioni di i/o comprese tra 8 KB e 64 KB. Gli IOPS effettivi sono dipendenti dal carico di lavoro. Per informazioni dettagliate, vedere [governance io di dati](resource-limits-logical-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Generazione di calcolo Gen4 (parte 2)

|Dimensioni di calcolo (obiettivo di servizio)|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione di calcolo|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|
|vCore|7|8|9|10|16|24|
|Memoria (GB)|49|56|63|70|112|159,5|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|3072|3072|3072|3072|4096|4096|
|Dimensioni massime del log (GB)|922|922|922|922|1229|1229|
|Dimensioni massime dati TempDB (GB)|224|256|288|320|512|768|
|Tipo di archiviazione|Unità SSD remota|Unità SSD remota|Unità SSD remota|Unità SSD remota|Unità SSD remota|Unità SSD remota|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)
|Numero massimo di IOPS dati *|2240|2560|2880|3200|5120|7680|
|Velocità massima log (MBps)|26,3|30|30|30|30|30|
|Numero massimo di ruoli di lavoro simultanei (richieste)|1400|1600|1800|2000|3200|4800|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|
|Numero di repliche|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

\*Il valore massimo per le dimensioni di i/o comprese tra 8 KB e 64 KB. Gli IOPS effettivi sono dipendenti dal carico di lavoro. Per informazioni dettagliate, vedere [governance io di dati](resource-limits-logical-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---gen5"></a>Calcolo per utilizzo generico-quinta generazione

### <a name="gen5-compute-generation-part-1"></a>Generazione di calcolo quinta generazione (parte 1)

|Dimensioni di calcolo (obiettivo di servizio)|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generazione di calcolo|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|
|vCore|2|4|6|8|10|12|14|
|Memoria (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|1024|1024|1536|1536|1536|3072|3072|
|Dimensioni massime del log (GB)|307|307|461|461|461|922|922|
|Dimensioni massime dati TempDB (GB)|64|128|192|256|320|384|384|
|Tipo di archiviazione|Unità SSD remota|Unità SSD remota|Unità SSD remota|Unità SSD remota|Unità SSD remota|Unità SSD remota|Unità SSD remota|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Numero massimo di IOPS dati *|640|1280|1920|2560|3200|3840|4480|
|Velocità massima log (MBps)|7,5|15|22,5|30|30|30|30|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|600|800|1000|1200|1400|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Numero di repliche|1|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

\*Il valore massimo per le dimensioni di i/o comprese tra 8 KB e 64 KB. Gli IOPS effettivi sono dipendenti dal carico di lavoro. Per informazioni dettagliate, vedere [governance io di dati](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Generazione di calcolo quinta generazione (parte 2)

|Dimensioni di calcolo (obiettivo di servizio)|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generazione di calcolo|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|
|vCore|16|18|20|24|32|40|80|
|Memoria (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|3072|3072|3072|4096|4096|4096|4096|
|Dimensioni massime del log (GB)|922|922|922|1229|1229|1229|1229|
|Dimensioni massime dati TempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo di archiviazione|Unità SSD remota|Unità SSD remota|Unità SSD remota|Unità SSD remota|Unità SSD remota|Unità SSD remota|Unità SSD remota|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Numero massimo di IOPS dati *|5120|5760|6400|7680|10240|12800|12800|
|Velocità massima log (MBps)|30|30|30|30|30|30|30|
|Numero massimo di ruoli di lavoro simultanei (richieste)|1600|1800|2000|2400|3200|4000|8000|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Numero di repliche|1|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

\*Il valore massimo per le dimensioni di i/o comprese tra 8 KB e 64 KB. Gli IOPS effettivi sono dipendenti dal carico di lavoro. Per informazioni dettagliate, vedere [governance io di dati](resource-limits-logical-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Fsv2-serie di calcolo con provisioning per utilizzo generico

### <a name="fsv2-series-compute-generation-preview"></a>Generazione di calcolo della serie Fsv2 (anteprima)

|Dimensioni di calcolo (obiettivo di servizio)|GP_Fsv2_72|
|:--- | --: |
|Generazione di calcolo|Serie Fsv2|
|vCore|72|
|Memoria (GB)|136,2|
|Supporto per columnstore|Sì|
|Archiviazione OLTP in memoria (GB)|N/A|
|Dimensioni massime dei dati (GB)|4096|
|Dimensioni massime del log (GB)|1024|
|Dimensioni massime dati TempDB (GB)|333|
|Tipo di archiviazione|Unità SSD remota|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Numero massimo di IOPS dati *|12.800|
|Velocità massima log (MBps)|30|
|Numero massimo di ruoli di lavoro simultanei (richieste)|3600|
|Numero massimo di accessi simultanei|3600|
|Numero massimo di sessioni simultanee|30.000|
|Numero di repliche|1|
|Zone di disponibilità multiple|N/A|
|Scalabilità orizzontale in lettura|N/A|
|Archivio di backup incluso|Dimensioni del database 1X|

\*Il valore massimo per le dimensioni di i/o comprese tra 8 KB e 64 KB. Gli IOPS effettivi sono dipendenti dal carico di lavoro. Per informazioni dettagliate, vedere [governance io di dati](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen4"></a>Calcolo con provisioning critico per il business-Gen4

> [!IMPORTANT]
> I nuovi database Gen4 non sono più supportati nelle aree dell'Australia orientale o del Brasile meridionale.

### <a name="gen4-compute-generation-part-1"></a>Generazione di calcolo Gen4 (parte 1)

|Dimensioni di calcolo (obiettivo di servizio)|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
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
|Numero massimo di IOPS dati *|4.000|8\.000|12000|16.000|20.000|24,000|
|Velocità massima log (MBps)|8|16|24|32|40|48|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|600|800|1000|1200|
|Numero massimo di accessi simultanei|200|400|600|800|1000|1200|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|
|Numero di repliche|4|4|4|4|4|4|
|Zone di disponibilità multiple|Sì|Sì|Sì|Sì|Sì|Sì|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

\*Il valore massimo per le dimensioni di i/o comprese tra 8 KB e 64 KB. Gli IOPS effettivi sono dipendenti dal carico di lavoro. Per informazioni dettagliate, vedere [governance io di dati](resource-limits-logical-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Generazione di calcolo Gen4 (parte 2)

|Dimensioni di calcolo (obiettivo di servizio)|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione di calcolo|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|
|vCore|7|8|9|10|16|24|
|Memoria (GB)|49|56|63|70|112|159,5|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|7|8|9,5|11|20|36|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Dimensioni massime dei dati (GB)|1024|1024|1024|1024|1024|1024|
|Dimensioni massime del log (GB)|307|307|307|307|307|307|
|Dimensioni massime dati TempDB (GB)|224|256|288|320|512|768|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Numero massimo di IOPS dati |28.000|32.000|36.000|40.000|64.000|76.800|
|Velocità massima log (MBps)|56|64|64|64|64|64|
|Numero massimo di ruoli di lavoro simultanei (richieste)|1400|1600|1800|2000|3200|4800|
|Numero massimo di accessi simultanei (richieste)|1400|1600|1800|2000|3200|4800|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|
|Numero di repliche|4|4|4|4|4|4|
|Zone di disponibilità multiple|Sì|Sì|Sì|Sì|Sì|Sì|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

\*Il valore massimo per le dimensioni di i/o comprese tra 8 KB e 64 KB. Gli IOPS effettivi sono dipendenti dal carico di lavoro. Per informazioni dettagliate, vedere [governance io di dati](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen5"></a>Calcolo con provisioning critico per il business-quinta generazione

### <a name="gen5-compute-generation-part-1"></a>Generazione di calcolo quinta generazione (parte 1)

|Dimensioni di calcolo (obiettivo di servizio)|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generazione di calcolo|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|
|vCore|2|4|6|8|10|12|14|
|Memoria (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|1,57|3,14|4,71|6,28|8,65|11,02|13,39|
|Dimensioni massime dei dati (GB)|1024|1024|1536|1536|1536|3072|3072|
|Dimensioni massime del log (GB)|307|307|461|461|461|922|922|
|Dimensioni massime dati TempDB (GB)|64|128|192|256|320|384|448|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Numero massimo di IOPS dati *|8000|16.000|24,000|32.000|40.000|48.000|56.000|
|Velocità massima log (MBps)|24|48|72|96|96|96|96|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|600|800|1000|1200|1400|
|Numero massimo di accessi simultanei|200|400|600|800|1000|1200|1400|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Numero di repliche|4|4|4|4|4|4|4|
|Zone di disponibilità multiple|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

\*Il valore massimo per le dimensioni di i/o comprese tra 8 KB e 64 KB. Gli IOPS effettivi sono dipendenti dal carico di lavoro. Per informazioni dettagliate, vedere [governance io di dati](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Generazione di calcolo quinta generazione (parte 2)

|Dimensioni di calcolo (obiettivo di servizio)|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generazione di calcolo|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|
|vCore|16|18|20|24|32|40|80|
|Memoria (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|15,77|18,14|20,51|25,25|37,94|52,23|131.64|
|Dimensioni massime dei dati (GB)|3072|3072|3072|4096|4096|4096|4096|
|Dimensioni massime del log (GB)|922|922|922|1229|1229|1229|1229|
|Dimensioni massime dati TempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Numero massimo di IOPS dati *|64.000|72.000|80.000|96.000|128.000|160.000|204.800|
|Velocità massima log (MBps)|96|96|96|96|96|96|96|
|Numero massimo di ruoli di lavoro simultanei (richieste)|1600|1800|2000|2400|3200|4000|8000|
|Numero massimo di accessi simultanei|1600|1800|2000|2400|3200|4000|8000|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Numero di repliche|4|4|4|4|4|4|4|
|Zone di disponibilità multiple|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

\*Il valore massimo per le dimensioni di i/o comprese tra 8 KB e 64 KB. Gli IOPS effettivi sono dipendenti dal carico di lavoro. Per informazioni dettagliate, vedere [governance io di dati](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---m-series"></a>Business critical-serie di calcolo con provisioning-M

### <a name="m-series-compute-generation-preview"></a>Generazione di calcolo della serie M (anteprima)

|Dimensioni di calcolo (obiettivo di servizio)|BC_M_128|
|:--- | --: |
|Generazione di calcolo|Serie M|
|vCore|128|
|Memoria (GB)|3767,1|
|Supporto per columnstore|Sì|
|Archiviazione OLTP in memoria (GB)|1768|
|Dimensioni massime dei dati (GB)|4096|
|Dimensioni massime del log (GB)|2048|
|Dimensioni massime dati TempDB (GB)|4096|
|Tipo di archiviazione|SSD locale|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Numero massimo di IOPS dati *|160.000|
|Velocità massima log (MBps)|264|
|Numero massimo di ruoli di lavoro simultanei (richieste)|12.800|
|Numero massimo di accessi simultanei|12.800|
|Numero massimo di sessioni simultanee|30000|
|Numero di repliche|4|
|Zone di disponibilità multiple|Sì|
|Scalabilità orizzontale in lettura|Sì|
|Archivio di backup incluso|Dimensioni del database 1X|

\*Il valore massimo per le dimensioni di i/o comprese tra 8 KB e 64 KB. Gli IOPS effettivi sono dipendenti dal carico di lavoro. Per informazioni dettagliate, vedere [governance io di dati](resource-limits-logical-server.md#resource-governance).

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [gestire lo spazio di file nel database SQL di Azure](file-space-manage.md).

## <a name="next-steps"></a>Passaggi successivi

- Per i limiti delle risorse DTU per un singolo database, vedere [limiti delle risorse per i singoli database usando il modello di acquisto DTU](resource-limits-dtu-single-databases.md)
- Per i limiti delle risorse vCore per i pool elastici, vedere [limiti delle risorse per i pool elastici con il modello di acquisto vCore](resource-limits-vcore-elastic-pools.md)
- Per i limiti delle risorse DTU per i pool elastici, vedere [limiti delle risorse per i pool elastici con il modello di acquisto DTU](resource-limits-dtu-elastic-pools.md)
- Per i limiti delle risorse per Istanza gestita SQL, vedere [limiti delle risorse di sql istanza gestita](../managed-instance/resource-limits.md).
- Per informazioni sui limiti generici di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- Per informazioni sui limiti delle risorse in un server, vedere [Panoramica dei limiti delle risorse in un server](resource-limits-logical-server.md) per informazioni sui limiti a livello di server e di sottoscrizione.
