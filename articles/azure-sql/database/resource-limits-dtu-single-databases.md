---
title: Limite di risorse DTU database singoli
description: Questa pagina descrive alcuni limiti di risorse DTU comuni per i database singoli nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: references_regions, seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/20/2019
ms.openlocfilehash: fd9a811fd1c19d115f3ff15194b7e632114140df
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790254"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model---azure-sql-database"></a>Limiti delle risorse per i singoli database che usano il modello di acquisto DTU-database SQL di Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Questo articolo fornisce i limiti dettagliati delle risorse per i database singoli del database SQL di Azure usando il modello di acquisto DTU.

Per i limiti delle risorse del modello di acquisto DTU per i pool elastici, vedere [limiti delle risorse DTU-pool elastici](resource-limits-dtu-elastic-pools.md). Per i limiti delle risorse vCore, vedere [limiti delle risorse vCore-database singoli](resource-limits-vcore-single-databases.md) e [limiti delle risorse vCore-pool elastici](resource-limits-vcore-elastic-pools.md). Per altre informazioni sui vari modelli di acquisto, vedere l'articolo sui [modelli di acquisto e i livelli di servizio](purchasing-models.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Database singolo: dimensioni di archiviazione e di calcolo

Le tabelle seguenti illustrano le risorse disponibili per un singolo database a ogni livello di servizio e nelle diverse dimensioni di calcolo. È possibile impostare il livello di servizio, le dimensioni di calcolo e lo spazio di archiviazione per un singolo database usando il [portale di Azure](single-database-manage.md#the-azure-portal), [Transact-SQL](single-database-manage.md#transact-sql-t-sql), [PowerShell](single-database-manage.md#powershell), l'[interfaccia della riga di comando di Azure](single-database-manage.md#the-azure-cli) o l'[API REST](single-database-manage.md#rest-api).

> [!IMPORTANT]
> Per indicazioni e considerazioni sulla scalabilità, vedere [ridimensionare un singolo database](single-database-scale.md)

### <a name="basic-service-tier"></a>Livello di servizio Basic

| **Dimensioni di calcolo** | **Base** |
| :--- | --: |
| Max DTU | 5 |
| Risorse di archiviazione incluse (GB) | 2 |
| Spazio di archiviazione massimo (GB) | 2 |
| Spazio di archiviazione OLTP in memoria max (GB) |N/D |
| Numero massimo di ruoli di lavoro simultanei (richieste) | 30 |
| Numero massimo di sessioni simultanee | 300 |
|||

> [!IMPORTANT]
> Il livello di servizio Basic fornisce meno di un vCore (CPU).  Per i carichi di lavoro con utilizzo intensivo della CPU, è consigliabile un livello di servizio di S3 o superiore.
>
>Per quanto riguarda l'archiviazione dei dati, il livello di servizio Basic viene inserito nei BLOB di pagine standard. I BLOB di pagine standard usano supporti di archiviazione basati su unità disco rigido e sono più adatti per lo sviluppo, il test e altri carichi di lavoro ad accesso sporadico, meno sensibili alla variabilità delle prestazioni.
>

### <a name="standard-service-tier"></a>Livello di servizio Standard

| **Dimensioni di calcolo** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Max DTU | 10 | 20 | 50 | 100 |
| Archiviazione inclusa (GB) <sup>1</sup> | 250 | 250 | 250 | 250 |
| Spazio di archiviazione massimo (GB) | 250 | 250 | 250 | 1024 |
| Spazio di archiviazione OLTP in memoria max (GB) | N/D | N/D | N/D | N/D |
| Numero massimo di ruoli di lavoro simultanei (richieste)| 60 | 90 | 120 | 200 |
| Numero massimo di sessioni simultanee |600 | 900 | 1200 | 2400 |
||||||

<sup>1</sup> vedere [Opzioni per i prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/single/) per informazioni dettagliate sui costi aggiuntivi causati da qualsiasi archiviazione aggiuntiva di cui è stato effettuato il provisioning.

> [!IMPORTANT]
> I livelli Standard S0, S1 e S2 forniscono meno di un vCore (CPU).  Per i carichi di lavoro con utilizzo intensivo della CPU, è consigliabile un livello di servizio di S3 o superiore.
>
>Per quanto riguarda l'archiviazione dei dati, i livelli di servizio Standard S0 e S1 sono posizionati in BLOB di pagine standard. I BLOB di pagine standard usano supporti di archiviazione basati su unità disco rigido e sono più adatti per lo sviluppo, il test e altri carichi di lavoro ad accesso sporadico, meno sensibili alla variabilità delle prestazioni.
>

### <a name="standard-service-tier-continued"></a>Livello di servizio Standard (continua)

| **Dimensioni di calcolo** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Max DTU | 200 | 400 | 800 | 1600 | 3000 |
| Archiviazione inclusa (GB) <sup>1</sup> | 250 | 250 | 250 | 250 | 250 |
| Spazio di archiviazione massimo (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
| Spazio di archiviazione OLTP in memoria max (GB) | N/D | N/D | N/D | N/D |N/D |
| Numero massimo di ruoli di lavoro simultanei (richieste)| 400 | 800 | 1600 | 3200 |6000 |
| Numero massimo di sessioni simultanee |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

<sup>1</sup> vedere [Opzioni per i prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/single/) per informazioni dettagliate sui costi aggiuntivi causati da qualsiasi archiviazione aggiuntiva di cui è stato effettuato il provisioning.

### <a name="premium-service-tier"></a>Livello di servizio Premium

| **Dimensioni di calcolo** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Max DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Archiviazione inclusa (GB) <sup>1</sup> | 500 | 500 | 500 | 500 | 4096 <sup>2</sup> | 4096 <sup>2</sup> |
| Spazio di archiviazione massimo (GB) | 1024 | 1024 | 1024 | 1024 | 4096 <sup>2</sup> | 4096 <sup>2</sup> |
| Spazio di archiviazione OLTP in memoria max (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Numero massimo di ruoli di lavoro simultanei (richieste)| 200 | 400 | 800 | 1600 | 2800 | 6400 |
| Numero massimo di sessioni simultanee | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

<sup>1</sup> vedere [Opzioni per i prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/single/) per informazioni dettagliate sui costi aggiuntivi causati da qualsiasi archiviazione aggiuntiva di cui è stato effettuato il provisioning.

<sup>2</sup> da 1024 GB fino a 4096 GB con incrementi di 256 GB.

> [!IMPORTANT]
> Più di 1 TB di spazio di archiviazione nel livello Premium è attualmente disponibile in tutte le aree ad eccezione di: Cina orientale, Cina settentrionale, Germania centrale e Germania nord-orientale. In queste aree la quantità massima di spazio di archiviazione nel livello Premium è limitata a 1 TB.  Per altre informazioni, vedere le [limitazioni correnti di P11 e P15](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).
> [!NOTE]
> Per informazioni sui `tempdb` limiti, vedere [limiti di tempdb](/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

## <a name="next-steps"></a>Passaggi successivi

- Per i limiti delle risorse vCore per un singolo database, vedere [limiti delle risorse per i singoli database usando il modello di acquisto vCore](resource-limits-vcore-single-databases.md)
- Per i limiti delle risorse vCore per i pool elastici, vedere [limiti delle risorse per i pool elastici con il modello di acquisto vCore](resource-limits-vcore-elastic-pools.md)
- Per i limiti delle risorse DTU per i pool elastici, vedere [limiti delle risorse per i pool elastici con il modello di acquisto DTU](resource-limits-dtu-elastic-pools.md)
- Per i limiti delle risorse per le istanze gestite nel Istanza gestita SQL di Azure, vedere [limiti delle risorse di sql istanza gestita](../managed-instance/resource-limits.md).
- Per informazioni sui limiti generici di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- Per informazioni sui limiti delle risorse in un server SQL logico, vedere [Cenni preliminari sui limiti delle risorse in un server SQL logico](resource-limits-logical-server.md) per informazioni sui limiti a livello di server e di sottoscrizione.