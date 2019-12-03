---
title: La risorsa DTU limita i pool elastici
description: Questa pagina descrive alcuni limiti di risorse DTU comuni per i pool elastici nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
ms.date: 03/14/2019
ms.openlocfilehash: 1dd0f90a9844bb3afbd15e1f8c804d3a7c6b7fff
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687683"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-purchasing-model"></a>Limiti delle risorse per i pool elastici con il modello di acquisto DTU

Questo articolo fornisce i limiti dettagliati delle risorse per i pool elastici del database SQL di Azure e i database in pool usando il modello di acquisto DTU.

Per i limiti delle risorse del modello di acquisto DTU per i database singoli, vedere [limiti delle risorse DTU-database singoli](sql-database-vcore-resource-limits-elastic-pools.md). Per i limiti delle risorse vCore, vedere [limiti delle risorse vCore-database singoli](sql-database-vcore-resource-limits-single-databases.md) e [limiti delle risorse vCore-pool elastici](sql-database-vcore-resource-limits-elastic-pools.md).

## <a name="elastic-pool-storage-sizes-and-compute-sizes"></a>Pool elastico: dimensioni di archiviazione e di calcolo

Per i pool elastici del database SQL le tabelle seguenti illustrano le risorse disponibili a ogni livello di servizio e nelle diverse dimensioni di calcolo. È possibile impostare il livello di servizio, le dimensioni di calcolo e lo spazio di archiviazione mediante il [portale di Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), l'[interfaccia della riga di comando di Azure](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases) o l'[API REST](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!IMPORTANT]
> Per indicazioni e considerazioni sulla scalabilità, vedere [ridimensionare un pool elastico](sql-database-elastic-pool-scale.md)
> [!NOTE]
> I limiti delle risorse di database singoli nei pool elastici sono in genere identici a quelli di database singoli all'esterno dei pool in base alle DTU e al livello di servizio. Ad esempio, il numero massimo di thread di lavoro simultanei per un database S2 è 120. Pertanto, anche il numero massimo di thread di lavoro simultanei per un database in un pool Standard è 120 se il numero massimo di DTU per ogni database nel pool è 50 (equivalente a S2).

### <a name="basic-elastic-pool-limits"></a>Limiti del pool elastico Basic

| eDTU per pool | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Risorse di archiviazione incluse per pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Opzioni per lo spazio di archiviazione massimo per pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Spazio di archiviazione OLTP in memoria massimo per pool (GB) | N/D | N/D | N/D | N/D | N/D | N/D | N/D | N/D |
| Numero massimo di database per pool | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Numero massimo di ruoli di lavoro simultanei (richieste) per pool | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Numero massimo di sessioni simultanee per pool | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Opzioni per il numero minimo di eDTU per database | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Opzioni per il numero massimo di eDTU per database | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Spazio di archiviazione massimo per database (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
||||||||

### <a name="standard-elastic-pool-limits"></a>Limiti del pool elastico Standard

| eDTU per pool | **50** | **100** | **200** | **300** | **400** | **800**|
|:---|---:|---:|---:| ---: | ---: | ---: |
| Risorse di archiviazione incluse per pool (GB) | 50 | 100 | 200 | 300 | 400 | 800 |
| Opzioni per lo spazio di archiviazione massimo per pool (GB) | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 |
| Spazio di archiviazione OLTP in memoria massimo per pool (GB) | N/D | N/D | N/D | N/D | N/D | N/D |
| Numero massimo di database per pool | 100 | 200 | 500 | 500 | 500 | 500 |
| Numero massimo di ruoli di lavoro simultanei (richieste) per pool | 100 | 200 | 400 | 600 | 800 | 1600 |
| Numero massimo di sessioni simultanee per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Opzioni per il numero minimo di eDTU per database | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Opzioni per il numero massimo di eDTU per database | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 |
| Spazio di archiviazione massimo per database (GB) | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Limiti del pool elastico Standard (continua)

| eDTU per pool | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Risorse di archiviazione incluse per pool (GB) | 1200 | 1600 | 2000 | 2500 | 3000 |
| Opzioni per lo spazio di archiviazione massimo per pool (GB) | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| Spazio di archiviazione OLTP in memoria massimo per pool (GB) | N/D | N/D | N/D | N/D | N/D |
| Numero massimo di database per pool | 500 | 500 | 500 | 500 | 500 |
| Numero massimo di ruoli di lavoro simultanei (richieste) per pool | 2400 | 3200 | 4000 | 5000 | 6000 |
| Numero massimo di sessioni simultanee per pool | 30000 | 30000 | 30000 | 30000 | 30000 |
| Opzioni per il numero minimo di eDTU per database | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Opzioni per il numero massimo di eDTU per database | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Opzioni per lo spazio di archiviazione massimo per database (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

### <a name="premium-elastic-pool-limits"></a>Limiti del pool elastico Premium

| eDTU per pool | **125** | **250** | **500** | **1000** | **1500**|
|:---|---:|---:|---:| ---: | ---: |
| Risorse di archiviazione incluse per pool (GB) | 250 | 500 | 750 | 1024 | 1536 |
| Opzioni per lo spazio di archiviazione massimo per pool (GB) | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| Spazio di archiviazione OLTP in memoria massimo per pool (GB) | 1 | 2 | 4 | 10 | 12 |
| Numero massimo di database per pool | 50 | 100 | 100 | 100 | 100 |
| Numero massimo di ruoli di lavoro simultanei per pool (richieste) | 200 | 400 | 800 | 1600 | 2400 |
| Numero massimo di sessioni simultanee per pool | 30000 | 30000 | 30000 | 30000 | 30000 |
| Numero minimo di eDTU per database | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000, 1500 |
| Numero massimo di eDTU per database | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000|
| Spazio di archiviazione massimo per database (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

### <a name="premium-elastic-pool-limits-continued"></a>Limiti del pool elastico Premium (continua)

| eDTU per pool | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: |
| Risorse di archiviazione incluse per pool (GB) | 2048 | 2560 | 3072 | 3548 | 4096 |
| Opzioni per lo spazio di archiviazione massimo per pool (GB) | 2048 | 2560 | 3072 | 3548 | 4096|
| Spazio di archiviazione OLTP in memoria massimo per pool (GB) | 16 | 20 | 24 | 28 | 32 |
| Numero massimo di database per pool | 100 | 100 | 100 | 100 | 100 |
| Numero massimo di ruoli di lavoro simultanei (richieste) per pool | 3200 | 4000 | 4800 | 5600 | 6400 |
| Numero massimo di sessioni simultanee per pool | 30000 | 30000 | 30000 | 30000 | 30000 |
| Opzioni per il numero minimo di eDTU per database | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Opzioni per il numero massimo di eDTU per database | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Spazio di archiviazione massimo per database (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

> [!IMPORTANT]
> Più di 1 TB di spazio di archiviazione nel livello Premium è attualmente disponibile in tutte le aree, ad eccezione di: Cina orientale, Cina settentrionale, Germania centrale, Germania nord-orientale, Stati Uniti centro-occidentali, US DoD aree e Stati Uniti centrali. In queste aree la quantità massima di risorse di archiviazione nel livello Premium è limitata a 1 TB.  Per altre informazioni, vedere le [limitazioni correnti di P11 e P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).

Se vengono utilizzate tutte le DTU di un pool elastico, ogni database del pool riceve un'uguale quantità di risorse per l'elaborazione di query. Il servizio di database SQL suddivide equamente le risorse fra i database con intervalli equivalenti per i tempi di calcolo. La condivisione equa delle risorse del pool elastico accompagna ogni quantità di risorse altrimenti garantita per ogni database quando il numero minimo di DTU per ogni database è impostato su un valore diverso da zero.

> [!NOTE]
> Per `tempdb` limiti, vedere [limiti di tempdb](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

### <a name="database-properties-for-pooled-databases"></a>Proprietà del database per i database in pool

La tabella seguente descrive le proprietà per i database in pool.

| Proprietà | Description |
|:--- |:--- |
| Numero massimo di eDTU per database |Il numero massimo di eDTU di cui un database può usufruire nel pool se disponibili sulla base dell'uso da parte di altri database nel pool. Il numero massimo di eDTU per database non è una garanzia di risorse per un database. Si tratta di un'impostazione globale che si applica a tutti i database nel pool. Impostare il numero massimo di eDTU per database sufficiente per gestire i picchi di utilizzo dei database. È previsto un certo grado di overcommit perché il pool in genere presuppone modelli di utilizzo dei database a freddo e a caldo in cui i database non raggiungono il picco contemporaneamente. Si pensi al caso in cui il picco di utilizzo per ogni database sia di 20 eDTU e solo il 20% dei 100 database nel pool raggiunga il picco nello stesso momento. Se il numero massimo di eDTU per ogni database è impostato su 20 eDTU, è ragionevole eseguire l'overcommit del pool moltiplicando per 5 e impostare il numero di eDTU su 400. |
| Numero minimo di eDTU per database |Il numero minimo di eDTU garantito a ogni database nel pool. Si tratta di un'impostazione globale che si applica a tutti i database nel pool. Il numero minimo di eDTU per database può essere impostato su 0, che corrisponde anche al valore predefinito. Questa proprietà è impostata su un valore compreso tra 0 e l'utilizzo medio di eDTU per ogni database. Il prodotto tra il numero di database nel pool e il numero minimo di eDTU per database non può superare il numero di eDTU per pool. Ad esempio, se un pool dispone di 20 database e di un numero minimo di eDTU per database impostato su 10 eDTU, il numero di eDTU per pool deve essere almeno pari a 200. |
| Quantità massima di risorse di archiviazione per database |Dimensioni massime del database impostate dall'utente per un database in un pool. Tuttavia, i database in pool condividono lo spazio di archiviazione del pool allocato. Anche se lo spazio di archiviazione massimo totale *per ogni database* è impostato per essere maggiore dello spazio di archiviazione totale *del pool*, lo spazio totale effettivamente usato da tutti i database non potrà superare il limite del pool disponibile. Le dimensioni massime del database indicano le dimensioni massime dei file di dati e non includono lo spazio usato dai file di log. |
|||

## <a name="next-steps"></a>Passaggi successivi

- Per i limiti delle risorse vCore per un singolo database, vedere [limiti delle risorse per i singoli database usando il modello di acquisto vCore](sql-database-vcore-resource-limits-single-databases.md)
- Per i limiti delle risorse DTU per un singolo database, vedere [limiti delle risorse per i singoli database usando il modello di acquisto DTU](sql-database-dtu-resource-limits-single-databases.md)
- Per i limiti delle risorse vCore per i pool elastici, vedere [limiti delle risorse per i pool elastici con il modello di acquisto vCore](sql-database-vcore-resource-limits-elastic-pools.md)
- Per i limiti delle risorse delle istanze gestite, vedere [Limiti delle risorse dell'istanza gestita](sql-database-managed-instance-resource-limits.md).
- Per informazioni sui limiti generici di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).
- Per informazioni sui limiti delle risorse in un server di database a livello di server e sottoscrizione, vedere [Panoramica dei limiti delle risorse in un server di database SQL](sql-database-resource-limits-database-server.md).
