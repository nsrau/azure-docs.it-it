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
ms.date: 10/15/2018
ms.openlocfilehash: 12074ad28e27a249a6dc378986f014ede1cd2ab3
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353581"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-a-single-database"></a>Limiti del modello di acquisto basato su vCore per il database SQL di Azure per un database singolo

Questo articolo illustra in modo dettagliato i limiti delle risorse per i database singoli del database SQL di Azure usando il modello di acquisto basato su vCore.

Per i limiti del modello di acquisto basati su DTU per i singoli database in un server logico, vedere [Panoramica dei limiti delle risorse in un server logico](sql-database-resource-limits-logical-server.md).

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md).

È possibile impostare il livello di servizio, la dimensione del calcolo e la quantità di archiviazione per un singolo database usando il [portale di Azure](sql-database-single-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-logical-servers-and-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-logical-servers-and-databases), l'[interfaccia della riga di comando di Azure](sql-database-single-databases-manage.md#azure-cli-manage-logical-servers-and-databases) o l'[API REST](sql-database-single-databases-manage.md#rest-api-manage-logical-servers-and-databases).

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Livello di servizio Utilizzo generico: dimensioni di archiviazione e di calcolo

### <a name="generation-4-compute-platform"></a>Piattaforma di calcolo Generazione 4

|Dimensione di calcolo|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione hardware|4|4|4|4|4|4|
|vCore|1|2|4|8|16|24|
|Memoria (GB)|7|14|28|56|112|168|
|Supporto per columnstore|Yes|Yes|Yes|Yes|Yes|Yes|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tipo di archiviazione|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Dimensioni massime dei dati (GB)|1024|1024|1536|3072|4096|4096|
|Dimensioni massime del log (GB)|307|307|461|922|1229|1229|
|Dimensioni di TempDB (GB)|32|64|128|256|384|384|
|Target IOPS (64 KB)|500|1000|2000|4000|7000|7000|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|800|1600|3200|4800|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|
|Numero di repliche|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|7000
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|
|||

### <a name="generation-5-compute-platform"></a>Piattaforma di calcolo Generazione 5

|Dimensione di calcolo|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40| GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |
|Generazione hardware|5|5|5|5|5|5|5|
|vCore|2|4|8|16|24|32|40|80|
|Memoria (GB)|11|22|44|88|132|176|220|440|
|Supporto per columnstore|Yes|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tipo di archiviazione|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Dimensioni massime dei dati (GB)|1024|1024|1536|3072|4096|4096|4096|4096|
|Dimensioni massime del log (GB)|307|307|461|614|1229|1229|1229|1229|
|Dimensioni di TempDB (GB)|64|128|256|384|384|384|384|384|
|Target IOPS (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|800|1600|2400|3200|4000|8000|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|30000|30000|
|Numero di repliche|1|1|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|
|||

## <a name="business-critical-service-tier-storage-sizes-and-compute-sizes"></a>Livello di servizio business critical: dimensioni di archiviazione e di calcolo

### <a name="generation-4-compute-platform"></a>Piattaforma di calcolo Generazione 4

|Dimensione di calcolo|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione hardware|4|4|4|4|4|4|
|vCore|1|2|4|8|16|24|
|Memoria (GB)|7|14|28|56|112|168|
|Supporto per columnstore|N/D|N/D|N/D|N/D|N/D|N/D|
|Archiviazione OLTP in memoria (GB)|1|2|4|8|20|36|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Dimensioni massime dei dati (GB)|1024|1024|1024|1024|1024|1024|
|Dimensioni massime del log (GB)|307|307|307|307|307|307|
|Dimensioni di TempDB (GB)|32|64|128|256|384|384|
|Target IOPS (64 KB)|5000|10000|20000|40000|80000|120000|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|800|1600|3200|4800|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|
|Numero di repliche|3|3|3|3|3|3|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|Yes|Yes|Yes|Yes|Yes|Yes|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|
|||

### <a name="generation-5-compute-platform"></a>Piattaforma di calcolo Generazione 5

|Dimensione di calcolo|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|Generazione hardware|5|5|5|5|5|5|5|5|
|vCore|2|4|8|16|24|32|40|80|
|Memoria (GB)|11|22|44|88|132|176|220|440|
|Supporto per columnstore|Yes|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Archiviazione OLTP in memoria (GB)|1.571|3.142|6.284|15.768|25.252|37.936|52.22|131.64|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Dimensioni massime dei dati (GB)|1024|1024|1024|1024|2048|4096|4096|4096|
|Dimensioni massime del log (GB)|307|307|307|307|614|1229|1229|1229|
|Dimensioni di TempDB (GB)|64|128|256|384|384|384|384|384|
|Target IOPS (64 KB)|5000|10000|20000|40000|60000|80000|100000|200000
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|800|1600|2400|3200|4000|8000|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|30000|30000|
|Numero di repliche|3|3|3|3|3|3|3|3|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|
|||

## <a name="hyperscale-service-tier-preview"></a>Livello di servizio con iperscalabilità (anteprima)

### <a name="generation-4-compute-platform-storage-sizes-and-compute-sizes"></a>Piattaforma di calcolo Generazione 4: dimensioni di archiviazione e di calcolo

|Livello di prestazioni|HS_Gen4_1|HS_Gen4_2|HS_Gen4_4|HS_Gen4_8|HS_Gen4_16|HS_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |--: |
|Generazione hardware|4|4|4|4|4|4|
|vCore|1|2|4|8|16|24|
|Memoria (GB)|7|14|28|56|112|168|
|Supporto per columnstore|Yes|Yes|Yes|Yes|Yes|Yes|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Dimensioni massime dei dati (TB)|100 |100 |100 |100 |100 |100 |
|Dimensioni massime del log (TB)|1 |1 |1 |1 |1 |1 |
|Dimensioni di TempDB (GB)|32|64|128|256|384|384|
|Target IOPS (64 KB)|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|
|Latenza di I/O (approssimativa)|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|800|1600|3200|4800|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|
|Numero di repliche|2|2|2|2|2|2|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|Yes|Yes|Yes|Yes|Yes|Yes|
|Archivio di backup incluso|7|7|7|7|7|7|
|||

### <a name="generation-5-compute-platform"></a>Piattaforma di calcolo Generazione 5

|Livello di prestazioni|HS_Gen5_2|HS_Gen5_4|HS_Gen5_8|HS_Gen5_16|HS_Gen5_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|Generazione hardware|5|5|5|5|5|5|5|5|
|vCore|2|4|8|16|24|32|40|80|
|Memoria (GB)|11|22|44|88|132|176|220|440|
|Supporto per columnstore|Yes|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Dimensioni massime dei dati (TB)|100 |100 |100 |100 |100 |100 |100 |100 |
|Dimensioni massime del log (TB)|1 |1 |1 |1 |1 |1 |1 |1 |
|Dimensioni di TempDB (GB)|64|128|256|384|384|384|384|384|
|Target IOPS (64 KB)|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|
|Latenza di I/O (approssimativa)|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|Deve essere determinato|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|800|1600|2400|3200|4000|8000|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|30000|30000|
|Numero di repliche|2|2|2|2|2|2|2|2|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|Yes|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Archivio di backup incluso (limite di anteprima)|7|7|7|7|7|7|7|7|
|||

## <a name="next-steps"></a>Passaggi successivi

- Per le risposte alle domande più frequenti, vedere [Domande frequenti sul database SQL](sql-database-faq.md).
- Per informazioni sui limiti generici di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).
