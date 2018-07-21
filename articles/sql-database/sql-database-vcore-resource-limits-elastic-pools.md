---
title: Limiti delle risorse basate su vCore per il database SQL di Azure - pool elastici | Microsoft Docs
description: Questa pagina descrive alcuni limiti delle risorse comuni basate su vCore per i pool elastici nel database SQL di Azure.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: carlrab
ms.openlocfilehash: 5503ffaf8a429221a0a0730fc999cb7a90f43785
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092121"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-elastic-pools"></a>Limiti del modello di acquisto in base ai vCore per il database SQL di Azure per i pool elastici

Questo articolo illustra in modo dettagliato i limiti delle risorse per i pool elastici e i database in pool del database SQL di Azure usando il modello di acquisto basato su vCore.

Per informazioni sui limiti del modello di acquisto basato su DTU, vedere [Limiti delle risorse basate su DTU per il database SQL - pool elastici](sql-database-dtu-resource-limits-elastic-pools.md).


## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Pool elastico: dimensioni di archiviazione e livelli delle prestazioni

Per i pool elastici del database SQL le tabelle seguenti illustrano le risorse disponibili a ogni livello di servizio e prestazioni. È possibile impostare il livello di servizio, il livello di prestazioni e la quantità di risorse di archiviazione mediante il [portale di Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), l'[interfaccia della riga di comando di Azure](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases) o l'[API REST](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!NOTE]
> I limiti di risorse di singoli database nei pool elastici sono in genere gli stessi di quelli per i database singoli al di fuori dei pool che hanno lo stesso livello di prestazioni. Ad esempio, il numero massimo di ruoli di lavoro simultanei per un database GP_Gen4_1 è 200. Di conseguenza, anche il numero massimo di ruoli di lavoro per un database in un pool GP_Gen4_1 è 200. Si noti che il numero totale di ruoli di lavoro simultanei in un pool GP_Gen4_1 è 210.

### <a name="general-purpose-service-tier"></a>Livello di servizio Utilizzo generico

#### <a name="generation-4-compute-platform"></a>Piattaforma di calcolo Generazione 4
|Livello di prestazioni|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione hardware|4|4|4|4|4|4|
|vCore|1|2|4|8|16|24|
|Memoria (GB)|7|14|28|56|112|168|
|Supporto per columnstore|sì|sì|sì|sì|sì|sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tipo di archiviazione|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|
|Dimensioni massime dei dati (GB)|512|756|1536|2048|3584|4096|
|Dimensioni massime dei log|154|227|461|614|1075|1229|
|Dimensioni di TempDB (database)|32|64|128|256|384|384|
|Target IOPS (64 KB)|500|1000|2000|4000|7000|7000|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Numero massimo di ruoli di lavoro simultanei (richieste)|210|420|840|1680|3360|5040|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|
|Massima densità dei pool|100|200|500|500|500|500|
|Numero minimo/massimo di arresti del pool elastico|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|
|Numero di repliche|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|
|||

#### <a name="generation-5-compute-platform"></a>Piattaforma di calcolo Generazione 5
|Livello di prestazioni|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|Generazione hardware|5|5|5|5|5|5|5|5|
|vCore|2|4|8|16|24|32|40|80|
|Memoria (GB)|11|22|44|88|132|176|220|440|
|Supporto per columnstore|sì|sì|sì|sì|sì|sì|sì|sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tipo di archiviazione|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|
|Dimensioni massime dei dati (GB)|512|756|1536|2048|3072|4096|4096|4096|
|Dimensioni massime dei log|154|227|461|614|922|1229|1229|1229|
|Dimensioni di TempDB (database)|64|128|256|384|384|384|384|384|
|Target IOPS (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Numero massimo di ruoli di lavoro simultanei (richieste)|210|420|840|1680|2520|3360|4200|8400
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|30000|30000|
|Massima densità dei pool|100|200|500|500|500|500|500|500|
|Numero minimo/massimo di arresti del pool elastico|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|0, 0.5, 1, 2, 4, 8, 16, 24, 32|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 40|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 40, 80|
|Numero di repliche|1|1|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|
|||

### <a name="business-critical-service-tier"></a>Livello di servizio business critical

#### <a name="generation-4-compute-platform"></a>Piattaforma di calcolo Generazione 4
|Livello di prestazioni|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione hardware|4|4|4|4|4|4|
|vCore|1|2|4|8|16|24|
|Memoria (GB)|7|14|28|56|112|168|
|Supporto per columnstore|sì|sì|sì|sì|sì|sì|
|Archiviazione OLTP in memoria (GB)|1|2|4|8|20|36|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Dimensioni massime dei dati (GB)|1024|1024|1024|1024|1024|1024|
|Dimensioni massime dei log|307|307|307|307|307|307|
|Dimensioni di TempDB (database)|32|64|128|256|384|384|
|Target IOPS (64 KB)|5000|10000|20000|40000|80000|120000|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Numero massimo di ruoli di lavoro simultanei (richieste)|210|420|840|1680|3360|5040|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|
|Massima densità dei pool|N/D|50|100|100|100|100|
|Numero minimo/massimo di arresti del pool elastico|N/D|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|
|Numero di repliche|3|3|3|3|3|3|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|sì|sì|sì|sì|sì|sì|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|
|||

#### <a name="generation-5-compute-platform"></a>Piattaforma di calcolo Generazione 5
|Livello di prestazioni|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|Generazione hardware|5|5|5|5|5|5|5|5|
|vCore|2|4|8|16|24|32|40|80|
|Memoria (GB)|11|22|44|88|132|176|220|440|
|Supporto per columnstore|sì|sì|sì|sì|sì|sì|sì|sì|
|Archiviazione OLTP in memoria (GB)|1.571|3.142|6.284|15.768|25.252|37.936|52.22|131.64|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Dimensioni massime dei dati (GB)|1024|1024|1024|1024|2048|4096|4096|4096|
|Dimensioni massime dei log|307|307|307|307|614|1229|1229|1229|
|Dimensioni di TempDB (database)|64|128|256|384|384|384|384|384|
|Target IOPS (64 KB)|5000|10000|20000|40000|60000|80000|100000|200000
|Numero massimo di ruoli di lavoro simultanei (richieste)|210|420|840|1680|2520|3360|5040|8400|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|30000|30000|
|Massima densità dei pool|N/D|50|100|100|100|100|100|100|
|Numero minimo/massimo di arresti del pool elastico|N/D|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|0, 0.5, 1, 2, 4, 8, 16, 24, 32|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 40|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 40, 80|
|Numero di repliche|3|3|3|3|3|3|3|3|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|sì|sì|sì|sì|sì|sì|sì|sì|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|
|||

Se tutti i vCore in un pool elastico sono occupati, ogni database nel pool riceve una quantità identica di risorse di calcolo per elaborare le query. Il servizio di database SQL suddivide equamente le risorse fra i database con intervalli equivalenti per i tempi di calcolo. L'equa condivisione delle risorse del pool elastico avviene in aggiunta a qualsiasi quantità di risorse altrimenti garantita per ogni database quando il numero minimo di vCore per database è impostato su un valore diverso da zero.

### <a name="database-properties-for-pooled-databases"></a>Proprietà del database per i database in pool

La tabella seguente descrive le proprietà per i database in pool.

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| Numero massimo di vCore per database |Numero massimo di vCore che qualsiasi database nel pool può usare, se disponibile in base all'utilizzo da parte di altri database nel pool. Il numero massimo di vCore per database non è una garanzia di risorse per un database. Si tratta di un'impostazione globale che si applica a tutti i database nel pool. Impostare il numero massimo di vCore per database su un valore sufficientemente elevato per gestire i picchi di utilizzo dei database. È previsto un certo grado di overcommit perché il pool in genere presuppone modelli di utilizzo dei database a freddo e a caldo in cui i database non raggiungono il picco contemporaneamente.|
| Numero minimo di vCore per database |Numero minimo di vCore garantito per ogni database nel pool. Si tratta di un'impostazione globale che si applica a tutti i database nel pool. Il numero minimo di vCore per database può essere impostato su 0, che è anche il valore predefinito. Questa proprietà è impostata su qualsiasi valore compreso tra 0 e l'utilizzo medio di vCore per ogni database. Il prodotto tra il numero di database nel pool e il numero minimo di vCore per database non può superare il numero di vCore per pool.|
| Quantità massima di risorse di archiviazione per database |Dimensioni massime del database impostate dall'utente per un database in un pool. I database in pool condividono l'archiviazione allocata del pool e di conseguenza le dimensioni che un database può raggiungere sono limitate al valore più basso tra le restanti dimensioni del database e quelle di archiviazione del pool. Le dimensioni massime del database indicano le dimensioni massime dei file di dati e non includono lo spazio usato dai file di log. |
|||
 
## <a name="next-steps"></a>Passaggi successivi

- Per le risposte alle domande più frequenti, vedere [Domande frequenti sul database SQL](sql-database-faq.md).
- Per informazioni sui limiti generici di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).
