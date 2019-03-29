---
title: Limiti delle risorse basati su DTU del database SQL di Azure per i singoli database| Microsoft Docs
description: Questa pagina descrive alcuni limiti di risorse comuni basati su DTU per i database singoli nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/20/2019
ms.openlocfilehash: a0b565be2edba7dcf1ba4c1b17284ce05b4cea2f
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620547"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>Limiti di risorse per i database singoli usando il modello di acquisto basato su DTU

Questo articolo illustra in modo dettagliato i limiti delle risorse per i database singoli del database SQL di Azure usando il modello di acquisto basato su DTU.

Per i limiti di risorse basati su DTU per pool elastici, vedere [Limiti di risorse basati su DTU - pool elastici](sql-database-dtu-resource-limits-elastic-pools.md). Per i limiti di risorse in base ai VCore, vedere [Limiti delle risorse basati su vCore per i database singoli](sql-database-vcore-resource-limits-single-databases.md) e [Limiti delle risorse basati su vCore per pool elastici](sql-database-vcore-resource-limits-elastic-pools.md). Per altre informazioni sui vari modelli di acquisto, vedere l'articolo sui [modelli di acquisto e i livelli di servizio](sql-database-purchase-models.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Database singolo: dimensioni di archiviazione e di calcolo

Le tabelle seguenti illustrano le risorse disponibili per un singolo database a ogni livello di servizio e dimensione di calcolo. È possibile impostare il livello di servizio, la dimensione del calcolo e la quantità di archiviazione per un singolo database usando il [portale di Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), l'[interfaccia della riga di comando di Azure](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases) o l'[API REST](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Per la scalabilità indicazioni e considerazioni, vedere [ridimensionare un singolo database](sql-database-single-database-scale.md)

### <a name="basic-service-tier"></a>Livello di servizio Basic

| **Dimensione di calcolo** | **Basic** |
| :--- | --: |
| Max DTU | 5 |
| Risorse di archiviazione incluse (GB) | 2 |
| Opzioni per lo spazio di archiviazione massimo (GB) | 2 |
| Spazio di archiviazione OLTP in memoria max (GB) |N/A |
| Numero massimo di ruoli di lavoro simultanei (richieste) | 30 |
| Numero massimo di sessioni simultanee | 300 |
|||

### <a name="standard-service-tier"></a>Livello di servizio Standard

| **Dimensione di calcolo** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Max DTU | 10 | 20 | 50 | 100 |
| Risorse di archiviazione incluse (GB) | 250 | 250 | 250 | 250 |
| Opzioni per lo spazio di archiviazione massimo (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Spazio di archiviazione OLTP in memoria max (GB) | N/A | N/A | N/A | N/A |
| Numero massimo di ruoli di lavoro simultanei (richieste)| 60 | 90 | 120 | 200 |
| Numero massimo di sessioni simultanee |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Livello di servizio Standard (continua)

| **Dimensione di calcolo** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Max DTU | 200 | 400 | 800 | 1600 | 3000 |
| Risorse di archiviazione incluse (GB) | 250 | 250 | 250 | 250 | 250 |
| Opzioni per lo spazio di archiviazione massimo (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Spazio di archiviazione OLTP in memoria max (GB) | N/A | N/A | N/A | N/A |N/A |
| Numero massimo di ruoli di lavoro simultanei (richieste)| 400 | 800 | 1600 | 3200 |6000 |
| Numero massimo di sessioni simultanee |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Livello di servizio Premium

| **Dimensione di calcolo** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Max DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Risorse di archiviazione incluse (GB) | 500 | 500 | 500 | 500 | 4096* | 4096* |
| Opzioni per lo spazio di archiviazione massimo (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096* | 4096* |
| Spazio di archiviazione OLTP in memoria max (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Numero massimo di ruoli di lavoro simultanei (richieste)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Numero massimo di sessioni simultanee | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\* Da 1024 GB fino a 4096 GB con incrementi di 256 GB

> [!IMPORTANT]
> Nel livello Premium è attualmente disponibile uno spazio di archiviazione superiore a 1 TB in tutte le aree tranne Cina orientale, Cina settentrionale, Germania centrale, Germania nord-orientale, Stati Uniti centro-occidentali, aree US DoD e US Government (area centrale). In queste aree la quantità massima di spazio di archiviazione nel livello Premium è limitata a 1 TB.  Per altre informazioni, vedere le [limitazioni correnti di P11 e P15](sql-database-single-database-scale.md#dtu-based-purchasing-model-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> [!NOTE]
> Per la `tempdb` limiti, vedere [tempdb limiti](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

## <a name="next-steps"></a>Passaggi successivi

- Per i limiti delle risorse vCore per un database singolo, vedere [Limiti delle risorse per database singoli usando il modello di acquisto basato su vCore](sql-database-vcore-resource-limits-single-databases.md)
- Per i limiti delle risorse vCore per i pool elastici, vedere [Limiti delle risorse per pool elastici usando il modello di acquisto basato su vCore](sql-database-vcore-resource-limits-elastic-pools.md)
- Per i limiti delle risorse DTU per i pool elastici, vedere [Limiti delle risorse per i pool elastici secondo il modello di acquisto basato su DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Per i limiti delle risorse delle istanze gestite, vedere [Limiti delle risorse dell'istanza gestita](sql-database-managed-instance-resource-limits.md).
- Per informazioni sui limiti generici di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).
- Per informazioni sui limiti delle risorse in un server di database a livello di server e sottoscrizione, vedere [Panoramica dei limiti delle risorse in un server di database SQL](sql-database-resource-limits-database-server.md).
