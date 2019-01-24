---
title: Limiti delle risorse basate su vCore del database SQL di Azure - database singolo | Microsoft Docs
description: Questa pagina descrive alcuni limiti delle risorse comuni basate su vCore per un database singolo nel database SQL di Azure.
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
ms.date: 01/09/2019
ms.openlocfilehash: 894922a80ab874e5304ef441571e03ef559a34b0
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2019
ms.locfileid: "54215423"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-a-single-database"></a>Limiti del modello di acquisto basato su vCore per il database SQL di Azure per un database singolo

Questo articolo illustra in modo dettagliato i limiti delle risorse per i database singoli del database SQL di Azure usando il modello di acquisto basato su vCore.

Per i limiti del modello di acquisto basati su DTU per i singoli database in un server logico, vedere [Panoramica dei limiti delle risorse in un server logico](sql-database-resource-limits-logical-server.md).

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md).

È possibile impostare il livello di servizio, la dimensione del calcolo e la quantità di archiviazione per un singolo database usando il [portale di Azure](sql-database-single-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-logical-servers-and-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-logical-servers-and-databases), l'[interfaccia della riga di comando di Azure](sql-database-single-databases-manage.md#azure-cli-manage-logical-servers-and-databases) o l'[API REST](sql-database-single-databases-manage.md#rest-api-manage-logical-servers-and-databases).

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Livello di servizio Utilizzo generico: dimensioni di archiviazione e di calcolo

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Livello di servizio Utilizzo generico: Piattaforma di calcolo Generazione 4 (parte 1)

|Dimensione di calcolo|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione hardware|4|4|4|4|4|4|
|vCore|1|2|3|4|5|6|
|Memoria (GB)|7|14|21|28|35|42|
|Supporto per columnstore|Yes|Sì|Sì|Sì|Sì|Yes|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|1024|1024|1024|1536|1536|1536|
|Dimensioni massime del log (GB)|307|307|307|461|461|461|
|Dimensioni di TempDB (GB)|32|64|96|128|160|192|
|Tipo di archiviazione|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Target IOPS (64 KB)|500|1000|1500|2000|2500|3000|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|600|800|1000|1200|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|
|Numero di repliche|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|7000
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Livello di servizio Utilizzo generico: Piattaforma di calcolo Generazione 4 (parte 2)

|Dimensione di calcolo|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione hardware|4|4|4|4|4|4|
|vCore|7|8|9|10|16|24|
|Memoria (GB)|49|56|63|70|112|168|
|Supporto per columnstore|Yes|Sì|Sì|Sì|Sì|Yes|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|1536|3072|3072|3072|4096|4096|
|Dimensioni massime del log (GB)|461|922|922|922|1229|1229|
|Dimensioni di TempDB (GB)|224|256|288|320|384|384|
|Tipo di archiviazione|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)
|Target IOPS (64 KB)|3500|4000|4500|5000|7000|7000|
|Numero massimo di ruoli di lavoro simultanei (richieste)|1400|1600|1800|2000|3200|4800|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|
|Numero di repliche|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Livello di servizio Utilizzo generico: Piattaforma di calcolo Generazione 5 (parte 1)

|Dimensione di calcolo|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Generazione hardware|5|5|5|5|5|5|5|
|vCore|2|4|6|8|10|12|14|
|Memoria (GB)|10.2|20.4|30.6|40.8|51|61.2|71.4|
|Supporto per columnstore|Yes|Sì|Sì|Sì|Sì|Sì|Yes|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|1024|1024|1024|1536|1536|1536|1536|
|Dimensioni massime del log (GB)|307|307|307|461|461|461|461|
|Dimensioni di TempDB (GB)|64|128|192|256|320|384|384|
|Tipo di archiviazione|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Target IOPS (64 KB)|500|1000|1500|2000|2500|3000|3500|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|600|800|1000|1200|1400|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|30000|
|Numero di repliche|1|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Livello di servizio Utilizzo generico: Piattaforma di calcolo Generazione 5 (parte 2)

|Dimensione di calcolo|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Generazione hardware|5|5|5|5|5|5|5|
|vCore|16|18|20|24|32|40|80|
|Memoria (GB)|81.6|91.8|102|122.4|163.2|204|408|
|Supporto per columnstore|Yes|Sì|Sì|Sì|Sì|Sì|Yes|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|3072|3072|3072|4096|4096|4096|4096|
|Dimensioni massime del log (GB)|922|922|922|1229|1229|1229|1229|
|Dimensioni di TempDB (GB)|384|384|384|384|384|384|384|
|Tipo di archiviazione|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Target IOPS (64 KB)|4000|4500|5000|6000|7000|7000|7000|
|Numero massimo di ruoli di lavoro simultanei (richieste)|1600|1800|2000|2400|3200|4000|8000|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|30000|
|Numero di repliche|1|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

## <a name="business-critical-service-tier-storage-sizes-and-compute-sizes"></a>Livello di servizio business critical: dimensioni di archiviazione e di calcolo

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Livello di servizio business critical: Piattaforma di calcolo Generazione 4 (parte 1)

|Dimensione di calcolo|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione hardware|4|4|4|4|4|4|
|vCore|1|2|3|4|5|6|
|Memoria (GB)|7|14|21|28|35|42|
|Supporto per columnstore|N/D|N/D|N/D|N/D|N/D|N/D|
|Archiviazione OLTP in memoria (GB)|1|2|3|4|5|6|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Dimensioni massime dei dati (GB)|1024|1024|1024|1024|1024|1024|
|Dimensioni massime del log (GB)|307|307|307|307|307|307|
|Dimensioni di TempDB (GB)|32|64|96|128|160|192|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Target IOPS (64 KB)|5000|10000|15000|20000|25000|30000|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|600|800|1000|1200|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|
|Numero di repliche|4|4|4|4|4|4|
|Zone di disponibilità multiple|Yes|Sì|Sì|Sì|Sì|Yes|
|Scalabilità orizzontale in lettura|Yes|Sì|Sì|Sì|Sì|Yes|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Livello di servizio business critical: Piattaforma di calcolo Generazione 4 (parte 2)

|Dimensione di calcolo|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione hardware|4|4|4|4|4|4|
|vCore|7|8|9|10|16|24|
|Memoria (GB)|49|56|63|70|112|168|
|Supporto per columnstore|N/D|N/D|N/D|N/D|N/D|N/D|
|Archiviazione OLTP in memoria (GB)|7|8|9.5|11|20|36|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Dimensioni massime dei dati (GB)|1024|1024|1024|1024|1024|1024|
|Dimensioni massime del log (GB)|307|307|307|307|307|307|
|Dimensioni di TempDB (GB)|224|256|288|320|384|384|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Target IOPS (64 KB)|35000|40000|45000|50000|80000|120000|
|Numero massimo di ruoli di lavoro simultanei (richieste)|1400|1600|1800|2000|3200|4800|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|
|Numero di repliche|4|4|4|4|4|4|
|Zone di disponibilità multiple|Yes|Sì|Sì|Sì|Sì|Yes|
|Scalabilità orizzontale in lettura|Yes|Sì|Sì|Sì|Sì|Yes|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Livello di servizio business critical: Piattaforma di calcolo Generazione 5 (parte 1)

|Dimensione di calcolo|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |
|Generazione hardware|5|5|5|5|5|5|5|
|vCore|2|4|6|8|10|12|14|
|Memoria (GB)|11|22|33|44|55|66|77|
|Supporto per columnstore|Yes|Sì|Sì|Sì|Sì|Sì|Yes|
|Archiviazione OLTP in memoria (GB)|1.571|3.142|4.713|6.284|8.655|11.026|13.397|
|Dimensioni massime dei dati (GB)|1024|1024|1024|1536|1536|1536|1536|
|Dimensioni massime del log (GB)|307|307|307|461|461|461|461|
|Dimensioni di TempDB (GB)|64|128|192|256|320|384|384|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Target IOPS (64 KB)|5000|10000|15000|20000|25000|30000|35000|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|600|800|1000|1200|1400|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|30000|
|Numero di repliche|4|4|4|4|4|4|4|
|Zone di disponibilità multiple|Yes|Sì|Sì|Sì|Sì|Sì|Yes|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Livello di servizio business critical: Piattaforma di calcolo Generazione 5 (parte 2)

|Dimensione di calcolo|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |
|Generazione hardware|5|5|5|5|5|5|5|
|vCore|16|18|20|24|32|40|80|
|Memoria (GB)|88|99|110|132|176|220|440|
|Supporto per columnstore|Yes|Sì|Sì|Sì|Sì|Sì|Yes|
|Archiviazione OLTP in memoria (GB)|15.768|18.139|20.51|25.252|37.936|52.22|131.64|
|Dimensioni massime dei dati (GB)|3072|3072|3072|4096|4096|4096|4096|
|Dimensioni massime del log (GB)|922|922|922|1229|1229|1229|1229|
|Dimensioni di TempDB (GB)|384|384|384|384|384|384|384|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Target IOPS (64 KB)|40000|45000|50000|60000|80000|100000|200000|
|Numero massimo di ruoli di lavoro simultanei (richieste)|1600|1800|2000|2400|3200|4000|8000|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|30000|
|Numero di repliche|4|4|4|4|4|4|
|Zone di disponibilità multiple|Yes|Sì|Sì|Sì|Sì|Yes|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

## <a name="hyperscale-service-tier-preview"></a>Livello di servizio con iperscalabilità (anteprima)

### <a name="generation-4-compute-platform-storage-sizes-and-compute-sizes"></a>Piattaforma di calcolo Generazione 4: dimensioni di archiviazione e di calcolo

|Livello di prestazioni|HS_Gen4_1|HS_Gen4_2|HS_Gen4_4|HS_Gen4_8|HS_Gen4_16|HS_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |--: |
|Generazione hardware|4|4|4|4|4|4|
|vCore|1|2|4|8|16|24|
|Memoria (GB)|7|14|28|56|112|168|
|Supporto per columnstore|Yes|Sì|Sì|Sì|Sì|Yes|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (TB)|100 |100 |100 |100 |100 |100 |
|Dimensioni massime del log (TB)|1 |1 |1 |1 |1 |1 |
|Dimensioni di TempDB (GB)|32|64|128|256|384|384|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Target IOPS (64 KB)|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|
|Latenza di I/O (approssimativa)|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|800|1600|3200|4800|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|
|Numero di repliche|2|2|2|2|2|2|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|Yes|Sì|Sì|Sì|Sì|Yes|
|Archivio di backup incluso|7|7|7|7|7|7|
|||

### <a name="generation-5-compute-platform"></a>Piattaforma di calcolo Generazione 5

|Livello di prestazioni|HS_Gen5_2|HS_Gen5_4|HS_Gen5_8|HS_Gen5_16|HS_Gen5_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|Generazione hardware|5|5|5|5|5|5|5|5|
|vCore|2|4|8|16|24|32|40|80|
|Memoria (GB)|10.2|20.4|40.8|81.6|122.4|163.2|204|408|
|Supporto per columnstore|Yes|Sì|Sì|Sì|Sì|Sì|Sì|Yes|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (TB)|100 |100 |100 |100 |100 |100 |100 |100 |
|Dimensioni massime del log (TB)|1 |1 |1 |1 |1 |1 |1 |1 |
|Dimensioni di TempDB (GB)|64|128|256|384|384|384|384|384|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Target IOPS (64 KB)|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|
|Latenza di I/O (approssimativa)|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|800|1600|2400|3200|4000|8000|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|30000|30000|
|Numero di repliche|2|2|2|2|2|2|2|2|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|Yes|Sì|Sì|Sì|Sì|Sì|Sì|Yes|
|Archivio di backup incluso (limite di anteprima)|7|7|7|7|7|7|7|7|
|||

## <a name="next-steps"></a>Passaggi successivi

- Per le risposte alle domande più frequenti, vedere [Domande frequenti sul database SQL](sql-database-faq.md).
- Per informazioni sui limiti generici di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).
