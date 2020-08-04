---
title: La risorsa DTU limita i pool elastici
description: Questa pagina descrive alcuni limiti di risorse DTU comuni per i pool elastici nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seo-lt-2019 sqldbrb=1 references_regions
ms.devlang: ''
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
ms.date: 07/28/2020
ms.openlocfilehash: f3c7420e1f33a7c25e7d3bfdffff14019d0b606a
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542671"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-purchasing-model"></a>Limiti delle risorse per i pool elastici con il modello di acquisto DTU
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Questo articolo fornisce i limiti dettagliati delle risorse per i database nel database SQL di Azure che si trovano all'interno di un pool elastico usando il modello di acquisto DTU.

* Per i limiti delle risorse del modello di acquisto di DTU per il database SQL di Azure, vedere [limiti delle risorse DTU-database SQL di Azure](resource-limits-vcore-elastic-pools.md).
* Per i limiti delle risorse vCore, vedere [limiti delle risorse vCore-database SQL di Azure](resource-limits-vcore-single-databases.md) e [limiti delle risorse vCore-pool elastici](resource-limits-vcore-elastic-pools.md).

## <a name="elastic-pool-storage-sizes-and-compute-sizes"></a>Pool elastico: dimensioni di archiviazione e di calcolo

Per i pool elastici del database SQL di Azure, le tabelle seguenti illustrano le risorse disponibili a ogni livello di servizio e di calcolo. È possibile impostare il livello di servizio, le dimensioni di calcolo e la quantità di risorse di archiviazione utilizzando:

* [Portale di Azure](elastic-pool-manage.md#azure-portal)
* [PowerShell](elastic-pool-manage.md#powershell)
* [Interfaccia della riga di comando di Azure](elastic-pool-manage.md#azure-cli)
* [API REST](elastic-pool-manage.md#rest-api).

> [!IMPORTANT]
> Per indicazioni e considerazioni sulla scalabilità, vedere [ridimensionare un pool elastico](elastic-pool-scale.md)

I limiti delle risorse di database singoli nei pool elastici sono in genere identici a quelli di database singoli all'esterno dei pool in base alle DTU e al livello di servizio. Ad esempio, il numero massimo di ruoli di lavoro simultanei per un database S2 è 120. Pertanto, anche il numero massimo di ruoli di lavoro simultanei per un database in un pool Standard è 120 se il numero massimo di DTU per ogni database nel pool è 50 (che è equivalente a S2).
 
Per lo stesso numero di DTU, le risorse fornite a un pool elastico possono superare le risorse fornite a un singolo database all'esterno di un pool elastico. Ciò significa che l'utilizzo di eDTU di un pool elastico può essere inferiore alla somma dell'utilizzo di DTU tra i database all'interno del pool, a seconda dei modelli di carico di lavoro. Ad esempio, in un caso estremo con un solo database in un pool elastico in cui l'utilizzo di DTU del database è pari al 100%, è possibile che l'utilizzo del eDTU del pool sia 50% per determinati modelli di carico di lavoro. Questo problema può verificarsi anche se il numero massimo di DTU per database rimane in corrispondenza del valore massimo supportato per le dimensioni del pool specificate.

> [!NOTE]
> Il limite di risorse di archiviazione per pool in ognuna delle tabelle seguenti non include tempdb e l'archiviazione dei log.

### <a name="basic-elastic-pool-limits"></a>Limiti del pool elastico Basic

| eDTU per pool | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Risorse di archiviazione incluse per pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Spazio di archiviazione massimo per pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Spazio di archiviazione OLTP in memoria massimo per pool (GB) | N/D | N/D | N/D | N/D | N/D | N/D | N/D | N/D |
| Numero massimo di database per pool <sup>1</sup> | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Numero massimo di ruoli di lavoro simultanei (richieste) per pool <sup>2</sup> | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Numero massimo di sessioni simultanee per pool <sup>2</sup> | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Opzioni DTU minime per database | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Numero massimo di DTU per database | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Spazio di archiviazione massimo per database (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
||||||||

<sup>1</sup> per ulteriori considerazioni, vedere [gestione delle risorse in pool elastici densi](elastic-pool-resource-management.md) .

<sup>2</sup> per il numero massimo di ruoli di lavoro simultanei (richieste) per ogni singolo database, vedere [limiti delle risorse del database singolo](resource-limits-vcore-single-databases.md). Ad esempio, se il pool elastico USA quinta generazione e il numero massimo di vCore per database è impostato su 2, il valore numero massimo di ruoli di lavoro simultanei è 200.  Se il numero massimo di vCore per database è impostato su 0,5, il valore numero massimo di ruoli di lavoro simultanei è 50 poiché in quinta generazione sono presenti un massimo di 100 ruoli di lavoro simultanei per vCore. Per le altre impostazioni in cui il numero massimo di vCore per database è pari a 1 vCore o meno, il numero massimo di ruoli di lavoro simultanei viene ridimensionato in modo analogo.

### <a name="standard-elastic-pool-limits"></a>Limiti del pool elastico Standard

| eDTU per pool | **50** | **100** | **200** | **300** | **400** | **800**|
|:---|---:|---:|---:| ---: | ---: | ---: |
| Spazio di archiviazione incluso per pool (GB) <sup>1</sup> | 50 | 100 | 200 | 300 | 400 | 800 |
| Spazio di archiviazione massimo per pool (GB) | 500 | 750 | 1024 | 1280 | 1536 | 2048 |
| Spazio di archiviazione OLTP in memoria massimo per pool (GB) | N/D | N/D | N/D | N/D | N/D | N/D |
| Numero massimo di database per pool <sup>2</sup> | 100 | 200 | 500 | 500 | 500 | 500 |
| Numero massimo di ruoli di lavoro simultanei (richieste) per pool <sup>3</sup> | 100 | 200 | 400 | 600 | 800 | 1600 |
| Numero massimo di sessioni simultanee per pool <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Opzioni DTU minime per database | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Numero massimo di DTU per database | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 |
| Spazio di archiviazione massimo per database (GB) | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

<sup>1</sup> vedere [Opzioni per i prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/elastic/) per informazioni dettagliate sui costi aggiuntivi causati da qualsiasi archiviazione aggiuntiva di cui è stato effettuato il provisioning.

<sup>2</sup> per altre considerazioni, vedere [gestione delle risorse in pool elastici densi](elastic-pool-resource-management.md) .

<sup>3</sup> per il numero massimo di ruoli di lavoro simultanei (richieste) per ogni singolo database, vedere [limiti delle risorse del database singolo](resource-limits-vcore-single-databases.md). Ad esempio, se il pool elastico USA quinta generazione e il numero massimo di vCore per database è impostato su 2, il valore numero massimo di ruoli di lavoro simultanei è 200.  Se il numero massimo di vCore per database è impostato su 0,5, il valore numero massimo di ruoli di lavoro simultanei è 50 poiché in quinta generazione sono presenti un massimo di 100 ruoli di lavoro simultanei per vCore. Per le altre impostazioni in cui il numero massimo di vCore per database è pari a 1 vCore o meno, il numero massimo di ruoli di lavoro simultanei viene ridimensionato in modo analogo.

### <a name="standard-elastic-pool-limits-continued"></a>Limiti del pool elastico Standard (continua)

| eDTU per pool | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Spazio di archiviazione incluso per pool (GB) <sup>1</sup> | 1200 | 1600 | 2000 | 2500 | 3000 |
| Spazio di archiviazione massimo per pool (GB) | 2560 | 3072 | 3584 | 4096 | 4096 |
| Spazio di archiviazione OLTP in memoria massimo per pool (GB) | N/D | N/D | N/D | N/D | N/D |
| Numero massimo di database per pool <sup>2</sup> | 500 | 500 | 500 | 500 | 500 |
| Numero massimo di ruoli di lavoro simultanei (richieste) per pool <sup>3</sup> | 2400 | 3200 | 4000 | 5000 | 6000 |
| Numero massimo di sessioni simultanee per pool <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Opzioni DTU minime per database | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Numero massimo di DTU per database | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Spazio di archiviazione massimo per database (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> vedere [Opzioni per i prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/elastic/) per informazioni dettagliate sui costi aggiuntivi causati da qualsiasi archiviazione aggiuntiva di cui è stato effettuato il provisioning.

<sup>2</sup> per altre considerazioni, vedere [gestione delle risorse in pool elastici densi](elastic-pool-resource-management.md) .

<sup>3</sup> per il numero massimo di ruoli di lavoro simultanei (richieste) per ogni singolo database, vedere [limiti delle risorse del database singolo](resource-limits-vcore-single-databases.md). Ad esempio, se il pool elastico USA quinta generazione e il numero massimo di vCore per database è impostato su 2, il valore numero massimo di ruoli di lavoro simultanei è 200.  Se il numero massimo di vCore per database è impostato su 0,5, il valore numero massimo di ruoli di lavoro simultanei è 50 poiché in quinta generazione sono presenti un massimo di 100 ruoli di lavoro simultanei per vCore. Per le altre impostazioni in cui il numero massimo di vCore per database è pari a 1 vCore o meno, il numero massimo di ruoli di lavoro simultanei viene ridimensionato in modo analogo.

### <a name="premium-elastic-pool-limits"></a>Limiti del pool elastico Premium

| eDTU per pool | **125** | **250** | **500** | **1000** | **1500**|
|:---|---:|---:|---:| ---: | ---: |
| Spazio di archiviazione incluso per pool (GB) <sup>1</sup> | 250 | 500 | 750 | 1024 | 1536 |
| Spazio di archiviazione massimo per pool (GB) | 1024 | 1024 | 1024 | 1024 | 1536 |
| Spazio di archiviazione OLTP in memoria massimo per pool (GB) | 1 | 2 | 4 | 10 | 12 |
| Numero massimo di database per pool <sup>2</sup> | 50 | 100 | 100 | 100 | 100 |
| Numero massimo di ruoli di lavoro simultanei per pool (richieste) <sup>3</sup> | 200 | 400 | 800 | 1600 | 2400 |
| Numero massimo di sessioni simultanee per pool <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Numero minimo di eDTU per database | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000|
| Numero massimo di eDTU per database | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000|
| Spazio di archiviazione massimo per database (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> vedere [Opzioni per i prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/elastic/) per informazioni dettagliate sui costi aggiuntivi causati da qualsiasi archiviazione aggiuntiva di cui è stato effettuato il provisioning.

<sup>2</sup> per altre considerazioni, vedere [gestione delle risorse in pool elastici densi](elastic-pool-resource-management.md) .

<sup>3</sup> per il numero massimo di ruoli di lavoro simultanei (richieste) per ogni singolo database, vedere [limiti delle risorse del database singolo](resource-limits-vcore-single-databases.md). Ad esempio, se il pool elastico USA quinta generazione e il numero massimo di vCore per database è impostato su 2, il valore numero massimo di ruoli di lavoro simultanei è 200.  Se il numero massimo di vCore per database è impostato su 0,5, il valore numero massimo di ruoli di lavoro simultanei è 50 poiché in quinta generazione sono presenti un massimo di 100 ruoli di lavoro simultanei per vCore. Per le altre impostazioni in cui il numero massimo di vCore per database è pari a 1 vCore o meno, il numero massimo di ruoli di lavoro simultanei viene ridimensionato in modo analogo.

### <a name="premium-elastic-pool-limits-continued"></a>Limiti del pool elastico Premium (continua)

| eDTU per pool | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: |
| Spazio di archiviazione incluso per pool (GB) <sup>1</sup> | 2048 | 2560 | 3072 | 3548 | 4096 |
| Spazio di archiviazione massimo per pool (GB) | 2048 | 2560 | 3072 | 3548 | 4096|
| Spazio di archiviazione OLTP in memoria massimo per pool (GB) | 16 | 20 | 24 | 28 | 32 |
| Numero massimo di database per pool <sup>2</sup> | 100 | 100 | 100 | 100 | 100 |
| Numero massimo di ruoli di lavoro simultanei (richieste) per pool <sup>3</sup> | 3200 | 4000 | 4800 | 5600 | 6400 |
| Numero massimo di sessioni simultanee per pool <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Opzioni DTU minime per database | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Numero massimo di DTU per database | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Spazio di archiviazione massimo per database (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> vedere [Opzioni per i prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/elastic/) per informazioni dettagliate sui costi aggiuntivi causati da qualsiasi archiviazione aggiuntiva di cui è stato effettuato il provisioning.

<sup>2</sup> per altre considerazioni, vedere [gestione delle risorse in pool elastici densi](elastic-pool-resource-management.md) .

<sup>3</sup> per il numero massimo di ruoli di lavoro simultanei (richieste) per ogni singolo database, vedere [limiti delle risorse del database singolo](resource-limits-vcore-single-databases.md). Ad esempio, se il pool elastico USA quinta generazione e il numero massimo di vCore per database è impostato su 2, il valore numero massimo di ruoli di lavoro simultanei è 200.  Se il numero massimo di vCore per database è impostato su 0,5, il valore numero massimo di ruoli di lavoro simultanei è 50 poiché in quinta generazione sono presenti un massimo di 100 ruoli di lavoro simultanei per vCore. Per le altre impostazioni in cui il numero massimo di vCore per database è pari a 1 vCore o meno, il numero massimo di ruoli di lavoro simultanei viene ridimensionato in modo analogo.

> [!IMPORTANT]
> Più di 1 TB di spazio di archiviazione nel livello Premium è attualmente disponibile in tutte le aree ad eccezione di: Cina orientale, Cina settentrionale, Germania centrale e Germania nord-orientale. In queste aree la quantità massima di spazio di archiviazione nel livello Premium è limitata a 1 TB.  Per altre informazioni, vedere le [limitazioni correnti di P11 e P15](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).

Se vengono utilizzate tutte le DTU di un pool elastico, ogni database del pool riceve un'uguale quantità di risorse per l'elaborazione di query. Il servizio di database SQL suddivide equamente le risorse fra i database con intervalli equivalenti per i tempi di calcolo. La condivisione equa delle risorse del pool elastico accompagna ogni quantità di risorse altrimenti garantita per ogni database quando il numero minimo di DTU per ogni database è impostato su un valore diverso da zero.

> [!NOTE]
> Per informazioni sui `tempdb` limiti, vedere [limiti di tempdb](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

### <a name="database-properties-for-pooled-databases"></a>Proprietà del database per i database in pool

La tabella seguente descrive le proprietà per i database in pool.

| Proprietà | Descrizione |
|:--- |:--- |
| Numero massimo di eDTU per database |Il numero massimo di eDTU di cui un database può usufruire nel pool se disponibili sulla base dell'uso da parte di altri database nel pool. Il numero massimo di eDTU per database non è una garanzia di risorse per un database. Si tratta di un'impostazione globale che si applica a tutti i database nel pool. Impostare il numero massimo di eDTU per database sufficiente per gestire i picchi di utilizzo dei database. È previsto un certo grado di overcommit perché il pool in genere presuppone modelli di utilizzo dei database a freddo e a caldo in cui i database non raggiungono il picco contemporaneamente. Si pensi al caso in cui il picco di utilizzo per ogni database sia di 20 eDTU e solo il 20% dei 100 database nel pool raggiunga il picco nello stesso momento. Se il numero massimo di eDTU per ogni database è impostato su 20 eDTU, è ragionevole eseguire l'overcommit del pool moltiplicando per 5 e impostare il numero di eDTU su 400. |
| Numero minimo di eDTU per database |Il numero minimo di eDTU garantito a ogni database nel pool. Si tratta di un'impostazione globale che si applica a tutti i database nel pool. Il numero minimo di eDTU per database può essere impostato su 0, che corrisponde anche al valore predefinito. Questa proprietà è impostata su un valore compreso tra 0 e l'utilizzo medio di eDTU per ogni database. Il prodotto tra il numero di database nel pool e il numero minimo di eDTU per database non può superare il numero di eDTU per pool. Ad esempio, se un pool dispone di 20 database e di un numero minimo di eDTU per database impostato su 10 eDTU, il numero di eDTU per pool deve essere almeno pari a 200. |
| Quantità massima di risorse di archiviazione per database |Dimensioni massime del database impostate dall'utente per un database in un pool. Tuttavia, i database in pool condividono lo spazio di archiviazione del pool allocato. Anche se il totale di spazio di archiviazione massimo *per database* è impostato su un valore superiore allo *spazio di*archiviazione totale disponibile del pool, lo spazio totale effettivamente utilizzato da tutti i database non sarà in grado di superare il limite disponibile per il pool. Le dimensioni massime del database indicano le dimensioni massime dei file di dati e non includono lo spazio usato dai file di log. |
|||

## <a name="next-steps"></a>Passaggi successivi

* Per i limiti delle risorse vCore per un singolo database, vedere [limiti delle risorse per i singoli database usando il modello di acquisto vCore](resource-limits-vcore-single-databases.md)
* Per i limiti delle risorse DTU per un singolo database, vedere [limiti delle risorse per i singoli database usando il modello di acquisto DTU](resource-limits-dtu-single-databases.md)
* Per i limiti delle risorse vCore per i pool elastici, vedere [limiti delle risorse per i pool elastici con il modello di acquisto vCore](resource-limits-vcore-elastic-pools.md)
* Per i limiti delle risorse per le istanze gestite nel Istanza gestita SQL di Azure, vedere [limiti delle risorse di sql istanza gestita](../managed-instance/resource-limits.md).
* Per informazioni sui limiti generici di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../../azure-resource-manager/management/azure-subscription-service-limits.md).
* Per informazioni sui limiti delle risorse in un server SQL logico, vedere [Cenni preliminari sui limiti delle risorse in un server SQL logico](resource-limits-logical-server.md) per informazioni sui limiti a livello di server e di sottoscrizione.
