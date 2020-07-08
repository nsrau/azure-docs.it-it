---
title: Limiti delle risorse di vCore per pool elastico
description: Questa pagina descrive alcuni limiti di risorse vCore comuni per i pool elastici nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab, sstein
ms.date: 06/10/2020
ms.openlocfilehash: 4ffd92c0641b74682a74ffd2898e226999ac2dd4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668458"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-purchasing-model"></a>Limiti delle risorse per i pool elastici con il modello di acquisto vCore
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Questo articolo fornisce i limiti dettagliati delle risorse per i pool elastici del database SQL di Azure e i database in pool usando il modello di acquisto vCore.

Per i limiti del modello di acquisto DTU, vedere [limiti delle risorse DTU del database SQL-pool elastici](resource-limits-dtu-elastic-pools.md).

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [gestire lo spazio di file nel database SQL di Azure](file-space-manage.md).

È possibile impostare il livello di servizio, le dimensioni di calcolo (obiettivo di servizio) e la quantità di risorse di archiviazione usando il [portale di Azure](elastic-pool-manage.md#azure-portal), [PowerShell](elastic-pool-manage.md#powershell), l'interfaccia della riga di comando di [Azure](elastic-pool-manage.md#azure-cli)o l' [API REST](elastic-pool-manage.md#rest-api).

> [!IMPORTANT]
> Per indicazioni e considerazioni sulla scalabilità, vedere [ridimensionare un pool elastico](elastic-pool-scale.md).

## <a name="general-purpose---provisioned-compute---gen4"></a>Calcolo per utilizzo generico-Gen4

> [!IMPORTANT]
> I nuovi database Gen4 non sono più supportati nelle aree dell'Australia orientale o del Brasile meridionale.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Livello di servizio per utilizzo generico: piattaforma di calcolo di generazione 4 (parte 1)

|Dimensioni di calcolo (obiettivo di servizio)|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione di calcolo|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|
|vCore|1|2|3|4|5|6|
|Memoria (GB)|7|14|21|28|35|42|
|Numero massimo di database per pool <sup>1</sup>|100|200|500|500|500|500|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|512|756|1536|1536|1536|2048|
|Dimensioni massime dei log|154|227|461|461|461|614|
|Dimensioni massime dati TempDB (GB)|32|64|96|128|160|192|
|Tipo di archiviazione|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Numero massimo di IOPS dati per pool <sup>2</sup> |400|800|1200|1600|2000|2400|
|Velocità massima di log per pool (MBps)|4.7|9.4|14,1|18,8|23,4|28,1|
|Numero massimo di ruoli di lavoro simultanei per pool (richieste) <sup>3</sup> |210|420|630|840|1050|1260|
|Numero massimo di accessi simultanei per pool <sup>3</sup> |210|420|630|840|1050|1260|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|
|Numero minimo/massimo di opzioni vCore del pool elastico per database|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1...3|0, 0,25, 0,5, 1...4|0, 0,25, 0,5, 1...5|0, 0,25, 0,5, 1...6|
|Numero di repliche|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

<sup>1</sup> per ulteriori considerazioni, vedere [gestione delle risorse in pool elastici densi](elastic-pool-resource-management.md) .

<sup>2</sup> il valore massimo per le dimensioni di i/o comprese tra 8 kb e 64 KB. Gli IOPS effettivi sono dipendenti dal carico di lavoro. Per informazioni dettagliate, vedere [governance io di dati](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> per il numero massimo di ruoli di lavoro simultanei (richieste) per ogni singolo database, vedere [limiti delle risorse del database singolo](resource-limits-vcore-single-databases.md). Ad esempio, se il pool elastico USA quinta generazione e il numero massimo di vCore per database è impostato su 2, il valore numero massimo di ruoli di lavoro simultanei è 200.  Se il numero massimo di vCore per database è impostato su 0,5, il valore numero massimo di ruoli di lavoro simultanei è 50 poiché in quinta generazione sono presenti un massimo di 100 ruoli di lavoro simultanei per vCore. Per le altre impostazioni in cui il numero massimo di vCore per database è pari a 1 vCore o meno, il numero massimo di ruoli di lavoro simultanei viene ridimensionato in modo analogo.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Livello di servizio per utilizzo generico: piattaforma di calcolo di generazione 4 (parte 2)

|Dimensioni di calcolo (obiettivo di servizio)|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione di calcolo|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|
|vCore|7|8|9|10|16|24|
|Memoria (GB)|49|56|63|70|112|159,5|
|Numero massimo di database per pool <sup>1</sup>|500|500|500|500|500|500|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|2048|2048|2048|2048|3584|4096|
|Dimensioni massime del log (GB)|614|614|614|614|1075|1229|
|Dimensioni massime dati TempDB (GB)|224|256|288|320|512|768|
|Tipo di archiviazione|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Numero massimo di IOPS dati per pool <sup>2</sup>|2800|3200|3600|4000|6400|9600|
|Velocità massima di log per pool (MBps)|32,8|37,5|37,5|37,5|37,5|37,5|
|Numero massimo di ruoli di lavoro simultanei per pool (richieste) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Numero massimo di accessi simultanei (richieste) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|
|Numero minimo/massimo di opzioni vCore del pool elastico per database|0, 0,25, 0,5, 1...7|0, 0,25, 0,5, 1...8|0, 0,25, 0,5, 1...9|0, 0,25, 0,5, 1...10|0, 0,25, 0,5, 1...10, 16|0, 0,25, 0,5, 1...10, 16, 24|
|Numero di repliche|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

<sup>1</sup> per ulteriori considerazioni, vedere [gestione delle risorse in pool elastici densi](elastic-pool-resource-management.md) .

<sup>2</sup> il valore massimo per le dimensioni di i/o comprese tra 8 kb e 64 KB. Gli IOPS effettivi sono dipendenti dal carico di lavoro. Per informazioni dettagliate, vedere [governance io di dati](resource-limits-logical-server.md#resource-governance).    

<sup>3</sup> per il numero massimo di ruoli di lavoro simultanei (richieste) per ogni singolo database, vedere [limiti delle risorse del database singolo](resource-limits-vcore-single-databases.md). Ad esempio, se il pool elastico USA quinta generazione e il numero massimo di vCore per database è impostato su 2, il valore numero massimo di ruoli di lavoro simultanei è 200.  Se il numero massimo di vCore per database è impostato su 0,5, il valore numero massimo di ruoli di lavoro simultanei è 50 poiché in quinta generazione sono presenti un massimo di 100 ruoli di lavoro simultanei per vCore. Per le altre impostazioni in cui il numero massimo di vCore per database è pari a 1 vCore o meno, il numero massimo di ruoli di lavoro simultanei viene ridimensionato in modo analogo.

## <a name="general-purpose---provisioned-compute---gen5"></a>Calcolo per utilizzo generico-quinta generazione

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Livello di servizio utilizzo generico: piattaforma di calcolo di generazione 5 (parte 1)

|Dimensioni di calcolo (obiettivo di servizio)|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generazione di calcolo|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|
|vCore|2|4|6|8|10|12|14|
|Memoria (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Numero massimo di database per pool <sup>1</sup>|100|200|500|500|500|500|500|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|512|756|1536|1536|1536|2048|2048|
|Dimensioni massime del log (GB)|154|227|461|461|461|614|614|
|Dimensioni massime dati TempDB (GB)|64|128|192|256|320|384|448|
|Tipo di archiviazione|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Numero massimo di IOPS dati per pool <sup>2</sup>|800|1600|2400|3200|4000|4800|5600|
|Velocità massima di log per pool (MBps)|9.4|18,8|28,1|37,5|37,5|37,5|37,5|
|Numero massimo di ruoli di lavoro simultanei per pool (richieste) <sup>3</sup>|210|420|630|840|1050|1260|1470|
|Numero massimo di accessi simultanei per pool (richieste) <sup>3</sup>|210|420|630|840|1050|1260|1470|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Numero minimo/massimo di opzioni vCore del pool elastico per database|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1...4|0, 0,25, 0,5, 1...6|0, 0,25, 0,5, 1...8|0, 0,25, 0,5, 1...10|0, 0,25, 0,5, 1...12|0, 0,25, 0,5, 1...14|
|Numero di repliche|1|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

<sup>1</sup> per ulteriori considerazioni, vedere [gestione delle risorse in pool elastici densi](elastic-pool-resource-management.md) .

<sup>2</sup> il valore massimo per le dimensioni di i/o comprese tra 8 kb e 64 KB. Gli IOPS effettivi sono dipendenti dal carico di lavoro. Per informazioni dettagliate, vedere [governance io di dati](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> per il numero massimo di ruoli di lavoro simultanei (richieste) per ogni singolo database, vedere [limiti delle risorse del database singolo](resource-limits-vcore-single-databases.md). Ad esempio, se il pool elastico USA quinta generazione e il numero massimo di vCore per database è impostato su 2, il valore numero massimo di ruoli di lavoro simultanei è 200.  Se il numero massimo di vCore per database è impostato su 0,5, il valore numero massimo di ruoli di lavoro simultanei è 50 poiché in quinta generazione sono presenti un massimo di 100 ruoli di lavoro simultanei per vCore. Per le altre impostazioni in cui il numero massimo di vCore per database è pari a 1 vCore o meno, il numero massimo di ruoli di lavoro simultanei viene ridimensionato in modo analogo.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Livello di servizio per utilizzo generico: piattaforma di calcolo di generazione 5 (parte 2)

|Dimensioni di calcolo (obiettivo di servizio)|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generazione di calcolo|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|
|vCore|16|18|20|24|32|40|80|
|Memoria (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Numero massimo di database per pool <sup>1</sup>|500|500|500|500|500|500|500|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Dimensioni massime dei dati (GB)|2048|3072|3072|3072|4096|4096|4096|
|Dimensioni massime del log (GB)|614|922|922|922|1229|1229|1229|
|Dimensioni massime dati TempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo di archiviazione|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Numero massimo di IOPS dati per pool <sup>2</sup> |6.400|7.200|8\.000|9.600|12.800|16.000|16.000|
|Velocità massima di log per pool (MBps)|37,5|37,5|37,5|37,5|37,5|37,5|37,5|
|Numero massimo di ruoli di lavoro simultanei per pool (richieste) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Numero massimo di accessi simultanei per pool (richieste) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Numero minimo/massimo di opzioni vCore del pool elastico per database|0, 0,25, 0,5, 1...16|0, 0,25, 0,5, 1...18|0, 0,25, 0,5, 1...20|0, 0,25, 0,5, 1...20, 24|0, 0,25, 0,5, 1...20, 24, 32|0, 0,25, 0,5, 1...16, 24, 32, 40|0, 0,25, 0,5, 1...16, 24, 32, 40, 80|
|Numero di repliche|1|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

<sup>1</sup> per ulteriori considerazioni, vedere [gestione delle risorse in pool elastici densi](elastic-pool-resource-management.md) .

<sup>2</sup> il valore massimo per le dimensioni di i/o comprese tra 8 kb e 64 KB. Gli IOPS effettivi sono dipendenti dal carico di lavoro. Per informazioni dettagliate, vedere [governance io di dati](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> per il numero massimo di ruoli di lavoro simultanei (richieste) per ogni singolo database, vedere [limiti delle risorse del database singolo](resource-limits-vcore-single-databases.md). Ad esempio, se il pool elastico USA quinta generazione e il numero massimo di vCore per database è impostato su 2, il valore numero massimo di ruoli di lavoro simultanei è 200.  Se il numero massimo di vCore per database è impostato su 0,5, il valore numero massimo di ruoli di lavoro simultanei è 50 poiché in quinta generazione sono presenti un massimo di 100 ruoli di lavoro simultanei per vCore. Per le altre impostazioni in cui il numero massimo di vCore per database è pari a 1 vCore o meno, il numero massimo di ruoli di lavoro simultanei viene ridimensionato in modo analogo.

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Fsv2-serie di calcolo con provisioning per utilizzo generico

### <a name="fsv2-series-compute-generation-preview"></a>Generazione di calcolo della serie Fsv2 (anteprima)

|Dimensioni di calcolo (obiettivo di servizio)|GP_Fsv2_72|
|:--- | --: |
|Generazione di calcolo|Serie Fsv2|
|vCore|72|
|Memoria (GB)|136,2|
|Numero massimo di database per pool <sup>1</sup>|500|
|Supporto per columnstore|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|
|Dimensioni massime dei dati (GB)|4096|
|Dimensioni massime del log (GB)|1024|
|Dimensioni massime dati TempDB (GB)|333|
|Tipo di archiviazione|Archiviazione (remota) Premium|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Numero massimo di IOPS dati per pool <sup>2</sup>|16.000|
|Velocità massima di log per pool (MBps)|37,5|
|Numero massimo di ruoli di lavoro simultanei per pool (richieste) <sup>3</sup>|3780|
|Numero massimo di accessi simultanei per pool (richieste) <sup>3</sup>|3780|
|Numero massimo di sessioni simultanee|30.000|
|Numero minimo/massimo di opzioni vCore del pool elastico per database|0-72|
|Numero di repliche|1|
|Zone di disponibilità multiple|N/D|
|Scalabilità orizzontale in lettura|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|

<sup>1</sup> per ulteriori considerazioni, vedere [gestione delle risorse in pool elastici densi](elastic-pool-resource-management.md) .

<sup>2</sup> il valore massimo per le dimensioni di i/o comprese tra 8 kb e 64 KB. Gli IOPS effettivi sono dipendenti dal carico di lavoro. Per informazioni dettagliate, vedere [governance io di dati](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> per il numero massimo di ruoli di lavoro simultanei (richieste) per ogni singolo database, vedere [limiti delle risorse del database singolo](resource-limits-vcore-single-databases.md). Ad esempio, se il pool elastico USA quinta generazione e il numero massimo di vCore per database è impostato su 2, il valore numero massimo di ruoli di lavoro simultanei è 200.  Se il numero massimo di vCore per database è impostato su 0,5, il valore numero massimo di ruoli di lavoro simultanei è 50 poiché in quinta generazione sono presenti un massimo di 100 ruoli di lavoro simultanei per vCore. Per le altre impostazioni in cui il numero massimo di vCore per database è pari a 1 vCore o meno, il numero massimo di ruoli di lavoro simultanei viene ridimensionato in modo analogo.

## <a name="business-critical---provisioned-compute---gen4"></a>Calcolo con provisioning critico per il business-Gen4

> [!IMPORTANT]
> I nuovi database Gen4 non sono più supportati nelle aree dell'Australia orientale o del Brasile meridionale.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Livello di servizio business critical: piattaforma di calcolo di generazione 4 (parte 1)

|Dimensioni di calcolo (obiettivo di servizio)|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione di calcolo|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|
|vCore|2|3|4|5|6|
|Memoria (GB)|14|21|28|35|42|
|Numero massimo di database per pool <sup>1</sup>|50|100|100|100|100|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|2|3|4|5|6|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Dimensioni massime dei dati (GB)|1024|1024|1024|1024|1024|
|Dimensioni massime del log (GB)|307|307|307|307|307|
|Dimensioni massime dati TempDB (GB)|64|96|128|160|192|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Numero massimo di IOPS dati per pool <sup>2</sup>|9000|13.500|18.000|22.500|27.000|
|Velocità massima di log per pool (MBps)|20|30|40|50|60|
|Numero massimo di ruoli di lavoro simultanei per pool (richieste) <sup>3</sup>|420|630|840|1050|1260|
|Numero massimo di accessi simultanei per pool (richieste) <sup>3</sup>|420|630|840|1050|1260|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|
|Numero minimo/massimo di opzioni vCore del pool elastico per database|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1...3|0, 0,25, 0,5, 1...4|0, 0,25, 0,5, 1...5|0, 0,25, 0,5, 1...6|
|Numero di repliche|4|4|4|4|4|
|Zone di disponibilità multiple|Sì|Sì|Sì|Sì|Sì|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

<sup>1</sup> per ulteriori considerazioni, vedere [gestione delle risorse in pool elastici densi](elastic-pool-resource-management.md) .

<sup>2</sup> il valore massimo per le dimensioni di i/o comprese tra 8 kb e 64 KB. Gli IOPS effettivi sono dipendenti dal carico di lavoro. Per informazioni dettagliate, vedere [governance io di dati](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> per il numero massimo di ruoli di lavoro simultanei (richieste) per ogni singolo database, vedere [limiti delle risorse del database singolo](resource-limits-vcore-single-databases.md). Ad esempio, se il pool elastico USA quinta generazione e il numero massimo di vCore per database è impostato su 2, il valore numero massimo di ruoli di lavoro simultanei è 200.  Se il numero massimo di vCore per database è impostato su 0,5, il valore numero massimo di ruoli di lavoro simultanei è 50 poiché in quinta generazione sono presenti un massimo di 100 ruoli di lavoro simultanei per vCore. Per le altre impostazioni in cui il numero massimo di vCore per database è pari a 1 vCore o meno, il numero massimo di ruoli di lavoro simultanei viene ridimensionato in modo analogo.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Livello di servizio business critical: piattaforma di calcolo di generazione 4 (parte 2)

|Dimensioni di calcolo (obiettivo di servizio)|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione di calcolo|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|Quarta generazione|
|vCore|7|8|9|10|16|24|
|Memoria (GB)|49|56|63|70|112|159,5|
|Numero massimo di database per pool <sup>1</sup>|100|100|100|100|100|100|
|Supporto per columnstore|N/D|N/D|N/D|N/D|N/D|N/D|
|Archiviazione OLTP in memoria (GB)|7|8|9,5|11|20|36|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Dimensioni massime dei dati (GB)|1024|1024|1024|1024|1024|1024|
|Dimensioni massime del log (GB)|307|307|307|307|307|307|
|Dimensioni massime dati TempDB (GB)|224|256|288|320|512|768|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Numero massimo di IOPS dati per pool <sup>2</sup>|31.500|36.000|40.500|45,000|72.000|96.000|
|Velocità massima di log per pool (MBps)|70|80|80|80|80|80|
|Numero massimo di ruoli di lavoro simultanei per pool (richieste) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Numero massimo di accessi simultanei per pool (richieste) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|
|Numero minimo/massimo di opzioni vCore del pool elastico per database|0, 0,25, 0,5, 1...7|0, 0,25, 0,5, 1...8|0, 0,25, 0,5, 1...9|0, 0,25, 0,5, 1...10|0, 0,25, 0,5, 1...10, 16|0, 0,25, 0,5, 1...10, 16, 24|
|Numero di repliche|4|4|4|4|4|4|
|Zone di disponibilità multiple|Sì|Sì|Sì|Sì|Sì|Sì|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

<sup>1</sup> per ulteriori considerazioni, vedere [gestione delle risorse in pool elastici densi](elastic-pool-resource-management.md) .

<sup>2</sup> il valore massimo per le dimensioni di i/o comprese tra 8 kb e 64 KB. Gli IOPS effettivi sono dipendenti dal carico di lavoro. Per informazioni dettagliate, vedere [governance io di dati](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> per il numero massimo di ruoli di lavoro simultanei (richieste) per ogni singolo database, vedere [limiti delle risorse del database singolo](resource-limits-vcore-single-databases.md). Ad esempio, se il pool elastico USA quinta generazione e il numero massimo di vCore per database è impostato su 2, il valore numero massimo di ruoli di lavoro simultanei è 200.  Se il numero massimo di vCore per database è impostato su 0,5, il valore numero massimo di ruoli di lavoro simultanei è 50 poiché in quinta generazione sono presenti un massimo di 100 ruoli di lavoro simultanei per vCore. Per le altre impostazioni in cui il numero massimo di vCore per database è pari a 1 vCore o meno, il numero massimo di ruoli di lavoro simultanei viene ridimensionato in modo analogo.

## <a name="business-critical---provisioned-compute---gen5"></a>Calcolo con provisioning critico per il business-quinta generazione

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Livello di servizio business critical: generazione 5 piattaforma di calcolo (parte 1)

|Dimensioni di calcolo (obiettivo di servizio)|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generazione di calcolo|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|
|vCore|4|6|8|10|12|14|
|Memoria (GB)|20,8|31,1|41,5|51,9|62,3|72,7|
|Numero massimo di database per pool <sup>1</sup>|50|100|100|100|100|100|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|3,14|4.71|6,28|8,65|11,02|13,39|
|Dimensioni massime dei dati (GB)|1024|1536|1536|1536|3072|3072|
|Dimensioni massime del log (GB)|307|307|461|461|922|922|
|Dimensioni massime dati TempDB (GB)|128|192|256|320|384|448|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Numero massimo di IOPS dati per pool <sup>2</sup>|18.000|27.000|36.000|45,000|54.000|63.000|
|Velocità massima di log per pool (MBps)|60|90|120|120|120|120|
|Numero massimo di ruoli di lavoro simultanei per pool (richieste) <sup>3</sup>|420|630|840|1050|1260|1470|
|Numero massimo di accessi simultanei per pool (richieste) <sup>3</sup>|420|630|840|1050|1260|1470|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|
|Numero minimo/massimo di opzioni vCore del pool elastico per database|0, 0,25, 0,5, 1...4|0, 0,25, 0,5, 1...6|0, 0,25, 0,5, 1...8|0, 0,25, 0,5, 1...10|0, 0,25, 0,5, 1...12|0, 0,25, 0,5, 1...14|
|Numero di repliche|4|4|4|4|4|4|
|Zone di disponibilità multiple|Sì|Sì|Sì|Sì|Sì|Sì|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

<sup>1</sup> per ulteriori considerazioni, vedere [gestione delle risorse in pool elastici densi](elastic-pool-resource-management.md) .

<sup>2</sup> il valore massimo per le dimensioni di i/o comprese tra 8 kb e 64 KB. Gli IOPS effettivi sono dipendenti dal carico di lavoro. Per informazioni dettagliate, vedere [governance io di dati](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> per il numero massimo di ruoli di lavoro simultanei (richieste) per ogni singolo database, vedere [limiti delle risorse del database singolo](resource-limits-vcore-single-databases.md). Ad esempio, se il pool elastico USA quinta generazione e il numero massimo di vCore per database è impostato su 2, il valore numero massimo di ruoli di lavoro simultanei è 200.  Se il numero massimo di vCore per database è impostato su 0,5, il valore numero massimo di ruoli di lavoro simultanei è 50 poiché in quinta generazione sono presenti un massimo di 100 ruoli di lavoro simultanei per vCore. Per le altre impostazioni in cui il numero massimo di vCore per database è pari a 1 vCore o meno, il numero massimo di ruoli di lavoro simultanei viene ridimensionato in modo analogo.

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Livello di servizio business critical: piattaforma di calcolo di generazione 5 (parte 2)

|Dimensioni di calcolo (obiettivo di servizio)|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generazione di calcolo|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|Quinta generazione|
|vCore|16|18|20|24|32|40|80|
|Memoria (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Numero massimo di database per pool <sup>1</sup>|100|100|100|100|100|100|100|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|15,77|18,14|20,51|25,25|37,94|52,23|131,68|
|Dimensioni massime dei dati (GB)|3072|3072|3072|4096|4096|4096|4096|
|Dimensioni massime del log (GB)|922|922|922|1229|1229|1229|1229|
|Dimensioni massime dati TempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Numero massimo di IOPS dati per pool <sup>2</sup>|72.000|81.000|90.000|108.000|144.000|180,000|256.000|
|Velocità massima di log per pool (MBps)|120|120|120|120|120|120|120|
|Numero massimo di ruoli di lavoro simultanei per pool (richieste) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Numero massimo di accessi simultanei per pool (richieste) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Numero massimo di sessioni simultanee|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Numero minimo/massimo di opzioni vCore del pool elastico per database|0, 0,25, 0,5, 1...16|0, 0,25, 0,5, 1...18|0, 0,25, 0,5, 1...20|0, 0,25, 0,5, 1...20, 24|0, 0,25, 0,5, 1...20, 24, 32|0, 0,25, 0,5, 1...20, 24, 32, 40|0, 0,25, 0,5, 1...20, 24, 32, 40, 80|
|Numero di repliche|4|4|4|4|4|4|4|
|Zone di disponibilità multiple|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|

<sup>1</sup> per ulteriori considerazioni, vedere [gestione delle risorse in pool elastici densi](elastic-pool-resource-management.md) .

<sup>2</sup> il valore massimo per le dimensioni di i/o comprese tra 8 kb e 64 KB. Gli IOPS effettivi sono dipendenti dal carico di lavoro. Per informazioni dettagliate, vedere [governance io di dati](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> per il numero massimo di ruoli di lavoro simultanei (richieste) per ogni singolo database, vedere [limiti delle risorse del database singolo](resource-limits-vcore-single-databases.md). Ad esempio, se il pool elastico USA quinta generazione e il numero massimo di vCore per database è impostato su 2, il valore numero massimo di ruoli di lavoro simultanei è 200.  Se il numero massimo di vCore per database è impostato su 0,5, il valore numero massimo di ruoli di lavoro simultanei è 50 poiché in quinta generazione sono presenti un massimo di 100 ruoli di lavoro simultanei per vCore. Per le altre impostazioni in cui il numero massimo di vCore per database è pari a 1 vCore o meno, il numero massimo di ruoli di lavoro simultanei viene ridimensionato in modo analogo.

## <a name="business-critical---provisioned-compute---m-series"></a>Business critical-serie di calcolo con provisioning-M

### <a name="m-series-compute-generation-preview"></a>Generazione di calcolo della serie M (anteprima)

|Dimensioni di calcolo (obiettivo di servizio)|BC_M_128|
|:--- | --: |
|Generazione di calcolo|Serie M|
|vCore|128|
|Memoria (GB)|3767,1|
|Numero massimo di database per pool <sup>1</sup>|100|
|Supporto per columnstore|Sì|
|Archiviazione OLTP in memoria (GB)|1768|
|Dimensioni massime dei dati (GB)|4096|
|Dimensioni massime del log (GB)|2048|
|Dimensioni massime dati TempDB (GB)|4096|
|Tipo di archiviazione|SSD locale|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Numero massimo di IOPS dati per pool <sup>2</sup>|200.000|
|Velocità massima di log per pool (MBps)|333|
|Numero massimo di ruoli di lavoro simultanei per pool (richieste) <sup>3</sup>|13.440|
|Numero massimo di accessi simultanei per pool (richieste) <sup>3</sup>|13.440|
|Numero massimo di sessioni simultanee|30.000|
|Numero minimo/massimo di opzioni vCore del pool elastico per database|0-128|
|Numero di repliche|4|
|Zone di disponibilità multiple|Sì|
|Scalabilità orizzontale in lettura|Sì|
|Archivio di backup incluso|Dimensioni del database 1X|

<sup>1</sup> per ulteriori considerazioni, vedere [gestione delle risorse in pool elastici densi](elastic-pool-resource-management.md) .

<sup>2</sup> il valore massimo per le dimensioni di i/o comprese tra 8 kb e 64 KB. Gli IOPS effettivi sono dipendenti dal carico di lavoro. Per informazioni dettagliate, vedere [governance io di dati](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> per il numero massimo di ruoli di lavoro simultanei (richieste) per ogni singolo database, vedere [limiti delle risorse del database singolo](resource-limits-vcore-single-databases.md). Ad esempio, se il pool elastico USA quinta generazione e il numero massimo di vCore per database è impostato su 2, il valore numero massimo di ruoli di lavoro simultanei è 200.  Se il numero massimo di vCore per database è impostato su 0,5, il valore numero massimo di ruoli di lavoro simultanei è 50 poiché in quinta generazione sono presenti un massimo di 100 ruoli di lavoro simultanei per vCore. Per le altre impostazioni in cui il numero massimo di vCore per database è pari a 1 vCore o meno, il numero massimo di ruoli di lavoro simultanei viene ridimensionato in modo analogo.

Se tutti i vCore in un pool elastico sono occupati, ogni database nel pool riceve una quantità identica di risorse di calcolo per elaborare le query. Il database SQL di Azure offre equità di condivisione delle risorse tra i database garantendo la parità delle sezioni del tempo di calcolo. L'equa condivisione delle risorse del pool elastico avviene in aggiunta a qualsiasi quantità di risorse altrimenti garantita per ogni database quando il numero minimo di vCore per database è impostato su un valore diverso da zero.

## <a name="database-properties-for-pooled-databases"></a>Proprietà del database per i database in pool

La tabella seguente descrive le proprietà per i database in pool.

> [!NOTE]
> I limiti delle risorse dei singoli database nei pool elastici sono in genere identici a quelli di database singoli all'esterno dei pool con le stesse dimensioni di calcolo (obiettivo di servizio). Ad esempio, il numero massimo di ruoli di lavoro simultanei per un database GP_Gen4_1 è 200. Di conseguenza, anche il numero massimo di ruoli di lavoro per un database in un pool GP_Gen4_1 è 200. Si noti che il numero totale di ruoli di lavoro simultanei in un pool GP_Gen4_1 è 210.

| Proprietà | Descrizione |
|:--- |:--- |
| Numero massimo di vCore per database |Numero massimo di vCore che qualsiasi database nel pool può usare, se disponibile in base all'utilizzo da parte di altri database nel pool. Il numero massimo di vCore per database non è una garanzia di risorse per un database. Si tratta di un'impostazione globale che si applica a tutti i database nel pool. Impostare il numero massimo di vCore per database su un valore sufficientemente elevato per gestire i picchi di utilizzo dei database. È previsto un certo grado di overcommit perché il pool in genere presuppone modelli di utilizzo dei database a freddo e a caldo in cui i database non raggiungono il picco contemporaneamente.|
| Numero minimo di vCore per database |Numero minimo di vCore garantito per ogni database nel pool. Si tratta di un'impostazione globale che si applica a tutti i database nel pool. Il numero minimo di vCore per database può essere impostato su 0, che è anche il valore predefinito. Questa proprietà è impostata su qualsiasi valore compreso tra 0 e l'utilizzo medio di vCore per ogni database. Il prodotto tra il numero di database nel pool e il numero minimo di vCore per database non può superare il numero di vCore per pool.|
| Quantità massima di risorse di archiviazione per database |Dimensioni massime del database impostate dall'utente per un database in un pool. I database in pool condividono l'archiviazione allocata del pool e di conseguenza le dimensioni che un database può raggiungere sono limitate al valore più basso tra le restanti dimensioni del database e quelle di archiviazione del pool. Le dimensioni massime del database indicano le dimensioni massime dei file di dati e non includono lo spazio usato dai file di log. |
|||

## <a name="next-steps"></a>Passaggi successivi

- Per i limiti delle risorse vCore per un singolo database, vedere [limiti delle risorse per i singoli database usando il modello di acquisto vCore](resource-limits-vcore-single-databases.md)
- Per i limiti delle risorse DTU per un singolo database, vedere [limiti delle risorse per i singoli database usando il modello di acquisto DTU](resource-limits-dtu-single-databases.md)
- Per i limiti delle risorse DTU per i pool elastici, vedere [limiti delle risorse per i pool elastici con il modello di acquisto DTU](resource-limits-dtu-elastic-pools.md)
- Per i limiti delle risorse delle istanze gestite, vedere [Limiti delle risorse dell'istanza gestita](../managed-instance/resource-limits.md).
- Per informazioni sui limiti generici di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- Per informazioni sui limiti delle risorse in un server SQL logico, vedere [Cenni preliminari sui limiti delle risorse in un server SQL logico](resource-limits-logical-server.md) per informazioni sui limiti a livello di server e di sottoscrizione.
