---
title: Limiti delle risorse basate su vCore del database SQL di Azure - database singolo | Microsoft Docs
description: Questa pagina descrive alcuni limiti delle risorse comuni basate su vCore per un database singolo nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 04/22/2019
ms.openlocfilehash: 15aedaa7eedad4940945e9b1e7eff0e1780da796
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790393"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-based-purchasing-model"></a>Limiti delle risorse per database singoli con il modello di acquisto basato su vCore

Questo articolo illustra in modo dettagliato i limiti delle risorse per i database singoli del database SQL di Azure usando il modello di acquisto basato su vCore.

Per i limiti del modello di acquisto basato su DTU per i singoli database in un server di database SQL, vedere [Panoramica dei limiti delle risorse in un server di database SQL](sql-database-resource-limits-database-server.md).

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md).

È possibile impostare il livello di servizio, le dimensioni di calcolo e lo spazio di archiviazione per un singolo database usando il [portale di Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), l'[interfaccia della riga di comando di Azure](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases) o l'[API REST](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Per la scalabilità indicazioni e considerazioni, vedere [ridimensionare un singolo database](sql-database-single-database-scale.md).

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Livello di servizio Utilizzo generico: dimensioni di archiviazione e di calcolo

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Livello di servizio Utilizzo generico: Piattaforma di calcolo Generazione 4 (parte 1)

|Dimensioni di calcolo|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione hardware|4|4|4|4|4|4|
|vCore|1|2|3|4|5|6|
|Memoria (GB)|7|14|21|28|35|42|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|1024|1024|1024|1536|1536|1536|
|Dimensioni massime del log (GB)|307|307|307|461|461|461|
|Dimensioni di TempDB (GB)|32|64|96|128|160|192|
|Tipo di archiviazione|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Target IOPS (64 KB)|500|1000|1500|2000|2500|3000|
|Registrare i limiti di velocità (MBps)|3,75|7.5|11,25|15|18.75|22,5|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|600|800|1000|1200|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|
|Numero di repliche|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Livello di servizio Utilizzo generico: Piattaforma di calcolo Generazione 4 (parte 2)

|Dimensioni di calcolo|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione hardware|4|4|4|4|4|4|
|vCore|7|8|9|10|16|24|
|Memoria (GB)|49|56|63|70|112|168|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|1536|3072|3072|3072|4096|4096|
|Dimensioni massime del log (GB)|461|922|922|922|1229|1229|
|Dimensioni di TempDB (GB)|224|256|288|320|384|384|
|Tipo di archiviazione|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)
|Target IOPS (64 KB)|3500|4000|4500|5000|7000|7000|
|Registrare i limiti di velocità (MBps)|26.25|30|30|30|30|30|
|Numero massimo di ruoli di lavoro simultanei (richieste)|1400|1600|1800|2000|3200|4800|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|
|Numero di repliche|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Livello di servizio Utilizzo generico: Piattaforma di calcolo Generazione 5 (parte 1)

|Dimensioni di calcolo|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generazione hardware|5|5|5|5|5|5|5|
|vCore|2|4|6|8|10|12|14|
|Memoria (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|1024|1024|1536|1536|1536|3072|3072|
|Dimensioni massime del log (GB)|307|307|307|461|461|461|461|
|Dimensioni di TempDB (GB)|64|128|192|256|320|384|384|
|Tipo di archiviazione|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Target IOPS (64 KB)|1000|2000|3000|4000|5000|6000|7000|
|Registrare i limiti di velocità (MBps)|3,75|7.5|11,25|15|18.75|22,5|26.25|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|600|800|1000|1200|1400|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|30000|
|Numero di repliche|1|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Livello di servizio Utilizzo generico: Piattaforma di calcolo Generazione 5 (parte 2)

|Dimensioni di calcolo|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generazione hardware|5|5|5|5|5|5|5|
|vCore|16|18|20|24|32|40|80|
|Memoria (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|3072|3072|3072|4096|4096|4096|4096|
|Dimensioni massime del log (GB)|922|922|922|1229|1229|1229|1229|
|Dimensioni di TempDB (GB)|384|384|384|384|384|384|384|
|Tipo di archiviazione|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Target IOPS (64 KB)|7000|7000|7000|7000|7000|7000|7000|
|Registrare i limiti di velocità (MBps)|30|30|30|30|30|30|30|
|Numero massimo di ruoli di lavoro simultanei (richieste)|1600|1800|2000|2400|3200|4000|8000|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|30000|
|Numero di repliche|1|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

### <a name="serverless-compute-tier"></a>Livello di elaborazione serverless

Il [livello di calcolo senza server](sql-database-serverless.md) è disponibile in anteprima ed è solo per database singoli con l'acquisto di vCore del modello.

#### <a name="generation-5-compute-platform"></a>Piattaforma di calcolo Generazione 5

|Dimensioni di calcolo|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|
|:--- | --: |--: |--: |
|Generazione hardware|5|5|5|
|Numero di Vcore min-max|0.5-1|0.5-2|0.5-4|
|Min-max memoria (GB)|2.02-3|2.05-6|2.10-12|
|Sospensione automatica min ritardo (ore)|6|6|6|
|Supporto per columnstore|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|512|1024|1024|
|Dimensioni massime del log (GB)|12|24|48|
|Dimensioni di TempDB (GB)|32|64|128|
|Tipo di archiviazione|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Target IOPS (64 KB)|250|500|1000|
|Registrare i limiti di velocità (MBps)|2.5|5.6|10|
|Numero massimo di ruoli di lavoro simultanei (richieste)|75|150|300|
|Numero massimo di sessioni consentite|30000|30000|30000|
|Numero di repliche|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

## <a name="business-critical-service-tier-for-provisioned-compute-tier"></a>Livello di servizio critici di business per il livello di calcolo sottoposte a provisioning

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Livello di servizio business critical: Piattaforma di calcolo Generazione 4 (parte 1)

|Dimensioni di calcolo|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione hardware|4|4|4|4|4|4|
|vCore|1|2|3|4|5|6|
|Memoria (GB)|7|14|21|28|35|42|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|1|2|3|4|5|6|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Dimensioni massime dei dati (GB)|650|650|650|650|650|650|
|Dimensioni massime del log (GB)|195|195|195|195|195|195|
|Dimensioni di TempDB (GB)|32|64|96|128|160|192|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Target IOPS (64 KB)|5000|10000|15000|20000|25000|30000|
|Registrare i limiti di velocità (MBps)|8|16|24|32|40|48|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|600|800|1000|1200|
|Numero massimo di accessi simultanei|200|400|600|800|1000|1200|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|
|Numero di repliche|4|4|4|4|4|4|
|Zone di disponibilità multiple|Sì|Sì|Sì|Sì|Sì|Sì|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Livello di servizio business critical: Piattaforma di calcolo Generazione 4 (parte 2)

|Dimensioni di calcolo|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione hardware|4|4|4|4|4|4|
|vCore|7|8|9|10|16|24|
|Memoria (GB)|49|56|63|70|112|168|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|7|8|9,5|11|20|36|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Dimensioni massime dei dati (GB)|650|650|650|650|1024|1024|
|Dimensioni massime del log (GB)|195|195|195|195|307|307|
|Dimensioni di TempDB (GB)|224|256|288|320|384|384|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Target IOPS (64 KB)|35000|40000|45000|50000|80000|120000|
|Registrare i limiti di velocità (MBps)|56|64|64|64|64|64|
|Numero massimo di ruoli di lavoro simultanei (richieste)|1400|1600|1800|2000|3200|4800|
|Numero massimo di accessi simultanei (richieste)|1400|1600|1800|2000|3200|4800|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|
|Numero di repliche|4|4|4|4|4|4|
|Zone di disponibilità multiple|Sì|Sì|Sì|Sì|Sì|Sì|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Livello di servizio business critical: Piattaforma di calcolo Generazione 5 (parte 1)

|Dimensioni di calcolo|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generazione hardware|5|5|5|5|5|5|5|
|vCore|2|4|6|8|10|12|14|
|Memoria (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|1.571|3.142|4,713|6.284|8,655|11,026|13,397|
|Dimensioni massime dei dati (GB)|1024|1024|1536|1536|1536|3072|3072|
|Dimensioni massime del log (GB)|307|307|307|461|461|922|922|
|Dimensioni di TempDB (GB)|64|128|192|256|320|384|384|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Target IOPS (64 KB)|8000|16000|24000|32000|40000|48000|56000|
|Registrare i limiti di velocità (MBps)|12|24|36|48|60|72|84|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|600|800|1000|1200|1400|
|Numero massimo di accessi simultanei|200|400|600|800|1000|1200|1400|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|30000|
|Numero di repliche|4|4|4|4|4|4|4|
|Zone di disponibilità multiple|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Livello di servizio business critical: Piattaforma di calcolo Generazione 5 (parte 2)

|Dimensioni di calcolo|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generazione hardware|5|5|5|5|5|5|5|
|vCore|16|18|20|24|32|40|80|
|Memoria (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|15.768|18,139|20,51|25.252|37.936|52.22|131.64|
|Dimensioni massime dei dati (GB)|3072|3072|3072|4096|4096|4096|4096|
|Dimensioni massime del log (GB)|922|922|922|1229|1229|1229|1229|
|Dimensioni di TempDB (GB)|384|384|384|384|384|384|384|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Target IOPS (64 KB)|64000|72000|80000|96000|128000|160000|320000|
|Registrare i limiti di velocità (MBps)|96|96|96|96|96|96|96|
|Numero massimo di ruoli di lavoro simultanei (richieste)|1600|1800|2000|2400|3200|4000|8000|
|Numero massimo di accessi simultanei|1600|1800|2000|2400|3200|4000|8000|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|30000|
|Numero di repliche|4|4|4|4|4|4|4|
|Zone di disponibilità multiple|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

## <a name="hyperscale-service-tier"></a>Livello di servizio Hyperscale

### <a name="generation-5-compute-platform"></a>Piattaforma di calcolo Generazione 5

|Livello di prestazioni|HS_Gen5_2|HS_Gen5_4|HS_Gen5_8|HS_Gen5_16|HS_Gen5_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Generazione hardware|5|5|5|5|5|5|5|5|
|vCore|2|4|8|16|24|32|40|80|
|Memoria (GB)|10.2|20.4|40.8|81.6|122.4|163,2|204|408|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
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
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archivio di backup incluso |7|7|7|7|7|7|7|7|
|||

## <a name="next-steps"></a>Passaggi successivi

- Per i limiti delle risorse DTU per un database singolo, vedere [Limiti delle risorse per database singoli usando il modello di acquisto basato su DTU](sql-database-dtu-resource-limits-single-databases.md)
- Per i limiti delle risorse vCore per i pool elastici, vedere [Limiti delle risorse per pool elastici usando il modello di acquisto basato su vCore](sql-database-vcore-resource-limits-elastic-pools.md)
- Per i limiti delle risorse DTU per i pool elastici, vedere [Limiti delle risorse per i pool elastici secondo il modello di acquisto basato su DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Per i limiti delle risorse delle istanze gestite, vedere [Limiti delle risorse dell'istanza gestita](sql-database-managed-instance-resource-limits.md).
- Per informazioni sui limiti generici di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).
- Per informazioni sui limiti delle risorse in un server di database a livello di server e sottoscrizione, vedere [Panoramica dei limiti delle risorse in un server di database SQL](sql-database-resource-limits-database-server.md).