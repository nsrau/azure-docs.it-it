---
title: Limiti delle risorse del database SQL di Azure per le istanze gestite | Microsoft Docs
description: Questo articolo offre una panoramica sui limiti delle risorse del database SQL di Azure per le istanze gestite.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
manager: craigg
ms.date: 06/26/2019
ms.openlocfilehash: a0846a7d03cc2f63af6747c8b8514b563c1d4a5d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447810"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Cenni preliminari su Azure SQL Database managed i limiti delle risorse di istanza

Questo articolo viene fornita una panoramica dei limiti delle risorse per l'istanza gestita di Database SQL di Azure e fornisce informazioni su come richiedere un aumento di questi limiti.

> [!NOTE]
> Per informazioni sulle differenze nelle funzionalità e nelle istruzioni T-SQL supportate, vedere [Confronto tra le funzionalità](sql-database-features.md) e [Differenze T-SQL tra Istanza gestita del database SQL di Azure e SQL Server](sql-database-managed-instance-transact-sql-information.md).

## <a name="instance-level-resource-limits"></a>Limiti delle risorse a livello di istanza

Istanza gestita ha le caratteristiche e i limiti delle risorse che dipendono l'infrastruttura sottostante e architettura. I limiti dipendono dalla generazione dell'hardware e dal livello di servizio.

### <a name="hardware-generation-characteristics"></a>Caratteristiche delle generazioni dell'hardware

Istanza gestita di Database SQL Azure può essere distribuito in due generazioni di hardware: Gen4 che Gen5. Generazioni di hardware hanno caratteristiche diverse, come descritto nella tabella seguente:

|   | **Quarta generazione** | **Quinta generazione** |
| --- | --- | --- |
| Hardware | Processori Intel E5-2673 v3 (Haswell) a 2,4 GHz, con unità vCore SSD = 1 PP (core fisico) | Processori Intel E5-2673 v4 (Broadwell) a 2,3 GHz, unità SSD NVMe veloce, vCore = 1 LP (hyperthread) |
| vCore | 8, 16, 24 vCore | 4, 8, 16, 24, 32, 40, 64, 80 Vcore |
| Memoria (percentuale di memoria/core) | 7 GB per vCore | 5,1 GB per vCore |
| Memoria di OLTP In memoria max | Limite di istanze: 3 GB per vCore<br/>Limiti del database:<br/> -8 core: 8 GB per ogni database<br/> -16 core: 20 GB per ogni database<br/> -24 core: 36 GB per ogni database | Limite di istanze: 2,5 GB per vCore<br/>Limiti del database:<br/> -8 core: 13 GB per ogni database<br/> -16 core: 32 GB per ogni database |
| Archiviazione istanza massimo (generico) |  8 TB | 8 TB |
| Istanza spazio di archiviazione massimo (Business Critical) | 1 TB | 1 TB, 2 TB o 4 TB in base al numero di core |

> [!IMPORTANT]
> I nuovi database Gen4 non sono più supportati nell'area AustraliaEast.

### <a name="service-tier-characteristics"></a>Caratteristiche del livello di servizio

Istanza gestita ha due livelli di servizio: Utilizzo generico e Business Critical. Questi livelli offrono funzionalità diverse, come descritto nella tabella seguente:

| **Funzionalità** | **Utilizzo generico** | **Business Critical** |
| --- | --- | --- |
| Numero di vCore\* | Quarta generazione: 8, 16, 24<br/>Quinta generazione: 4, 8, 16, 24, 32, 40, 64, 80 | Quarta generazione: 8, 16, 24, 32 <br/> Quinta generazione: 4, 8, 16, 24, 32, 40, 64, 80 |
| Memoria | Quarta generazione: 56 GB - 168 GB (7GB/vCore)<br/>Quinta generazione: GB 40,8-408 GB (5.1 GB/vCore) | Quarta generazione: 56 GB - 168 GB (7GB/vCore)<br/>Quinta generazione: GB 40,8-408 GB (5.1 GB/vCore) |
| Spazio di archiviazione massimo istanza | -2 TB per 4 Vcore per utilizzo (solo Gen5)<br/>-8 TB per gli altri formati | Quarta generazione: 1 TB <br/> Quinta generazione: <br/>-1 TB per 4, 8, 16 Vcore<br/>- 2 TB per 24 vCore<br/>- 4 TB per 32, 40, 64, 80 vCore |
| Quantità massima di risorse di archiviazione per database | Determinata dalla dimensione massima di archiviazione per ogni istanza | Determinata dalla dimensione massima di archiviazione per ogni istanza |
| Numero massimo di database per istanza | 100 | 100 |
| Numero massimo di file di database per istanza | Fino a 280 | 32.767 file per ogni database |
| Dati/Log di IOPS (approssimativi) | 500 - 7.500 per file<br/>\*[In base alle dimensioni del file di dati](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes).| 11 K - 110 K (1375/vCore) |
| Velocità effettiva di log | 3 MB/s per ogni vCore<br/>Max 22 MB/s per ogni istanza | 4 MB al secondo per vCore<br/>Numero massimo 48 MB/s per ogni istanza|
| Dati effettivi (approssimativi) | 100 - 250 MB/s per ogni file<br/>\*[In base alle dimensioni del file di dati](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes). | N/D |
| Latenza di I/O (approssimativa) | 5-10 ms | 1-2 ms |
| Dimensioni max di tempDB | 192 - 1.920 GB (24 GB per vCore) | Nessun vincolo; limitato dalla dimensione massima di archiviazione dell'istanza |
| Numero massimo di sessioni | 30000 | 30000 |

> [!NOTE]
> - La dimensione di archiviazione dell'istanza, che viene confrontata con la dimensione massima di archiviazione, include la dimensione dei dati e dei file di log presenti sia nel database utenti che in quello di sistema. Usare la vista di sistema <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> per determinare lo spazio totale usato dai database. I log degli errori non vengono salvati in modo permanente e non sono inclusi nella dimensione. I backup non sono inclusi nella dimensione di archiviazione.
> - IOPS e velocità effettiva dipendono anche le dimensioni della pagina che non sono limitata in modo esplicito da istanza gestita.

## <a name="supported-regions"></a>Aree supportate

Le istanze gestite possono essere create solo in [aree supportate](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Per creare un'istanza gestita in un'area che non è attualmente supportata, è possibile [invia una richiesta di supporto tramite il portale di Azure](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Tipi di sottoscrizioni supportati

Istanza gestita supporta attualmente la distribuzione solo nei seguenti tipi di sottoscrizioni:

- [Contratto Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Provider di servizi cloud (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Sviluppo/test Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Sottoscrizioni con credito Azure mensile per sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Limiti delle risorse a livello di area

I tipi di sottoscrizioni supportati possono contenere un numero limitato di risorse per area. Istanza gestita presenta due limiti predefiniti per ogni area di Azure in base a un tipo di sottoscrizione di tipo:

- **Limite di subnet**: numero massimo di subnet in cui vengono distribuite le istanze gestite in una singola area.
- **limite di vCore**: Il numero massimo di Vcore che possono essere distribuite in tutte le istanze in una singola area.

> [!Note]
> Questi limiti sono le impostazioni predefinite e limiti tecnici non. I limiti possono essere maggiori su richiesta tramite la creazione di una speciale [richiesta di supporto nel portale di Azure](#obtaining-a-larger-quota-for-sql-managed-instance) se sono necessarie più istanze gestite nell'area corrente. In alternativa, è possibile creare nuove istanze gestite in un'altra area di Azure senza inviare le richieste di supporto.

La tabella seguente illustra i limiti a livello di area predefinita per le sottoscrizioni supportati:

|Tipo di sottoscrizione| Numero massimo di subnet di istanza gestita | Numero massimo di unità vCore * |
| :---| :--- | :--- |
|Pagamento in base al consumo|3|320|
|CSP |8 (15 in alcune aree * *)|960 (1440 in alcune aree * *)|
|Sviluppo/test con pagamento in base al consumo|3|320|
|Sviluppo/test Enterprise|3|320|
|Contratto Enterprise|8 (15 in alcune aree * *)|960 (1440 in alcune aree * *)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional e MSDN Platforms|2|32|

\* Quando si pianificano le distribuzioni, è consigliabile che un vCore aziendali critici (BC) (a causa dell'aggiunta di ridondanza) utilizza 4 volte maggiore capacità rispetto a un Vcore per utilizzo generico generali (GP). In questo caso, per i calcoli, 1 GP vCore = 1 vCore di unit test e BC 1 vCore = 4 unità vCore. Per semplificare l'analisi di utilizzo con i limiti predefiniti, riepilogare le unità vCore tra tutte le subnet nell'area in cui le istanze gestite vengono distribuite e confrontano i risultati con i limiti di unità di istanza per il tipo di sottoscrizione. **Numero massimo di unità vCore** limite si applica a ogni sottoscrizione in un'area. Non sono previsti limiti per ogni singola subnet, ad eccezione del fatto che la somma di tutti i Vcore distribuito su più subnet deve essere inferiore o uguale a **: numero massimo di unità vCore**.

\*\* Limiti di vCore e subnet più grandi sono disponibili nelle aree seguenti: Australia orientale, Stati Uniti orientali, Stati Uniti orientali 2, Europa settentrionale, Stati Uniti centro-meridionali, Asia sud-orientale, Regno Unito meridionale, Europa occidentale, Stati Uniti occidentali 2.

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Istanza gestita di ottenere una quota maggiore di quella per SQL

Se non è presente più istanze gestite di aree correnti, inviare una richiesta di supporto per estendere la quota tramite il portale di Azure.
Per avviare il processo di acquisizione di una quota maggiore:

1. Aprire **Guida e supporto** e fare clic su **Nuova richiesta di supporto**.

   ![Guida e supporto](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Nella scheda Generale per la nuova richiesta di supporto:
   - Per **Tipo di problema** selezionare **Limiti del servizio e della sottoscrizione (quote)** .
   - In **Sottoscrizione** selezionare la propria sottoscrizione.
   - Per **Tipo di quota** selezionare **Istanza gestita di database SQL**.
   - Per **Piano di supporto** selezionare il piano di supporto in uso.

     ![Tipo di problema: Quota](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Fare clic su **Avanti**.
4. Nel **scheda problema** per la nuova richiesta di supporto:
   - Per **Gravità** selezionare il livello di gravità del problema.
   - In **Dettagli** inserire informazioni aggiuntive relative al problema riscontrato, inclusi i messaggi di errore.
   - In **Caricamento file** allegare un file con ulteriori informazioni (fino a 4 MB).

     ![Dettagli del problema](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Una richiesta valida deve includere:
     > - Area nella sottoscrizione di cui deve essere aumentato limite.
     > - Numero di Vcore, per ogni livello di servizio nella subnet esistenti dopo la quota necessario aumenta (se nessuna delle subnet esistente deve essere espanso.
     > - Richiesto numero di nuove subnet e il numero totale di Vcore per ogni livello di servizio all'interno di nuove subnet (se è necessario distribuire le istanze gestite nelle nuove subnet).

5. Fare clic su **Avanti**.
6. Nella scheda Informazioni contatto della nuova richiesta di supporto immettere la modalità di contatto preferita (posta elettronica o telefono) e i dettagli di contatto.
7. Fare clic su **Create**(Crea).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sull'istanza gestita, vedere [che cos'è un'istanza gestita?](sql-database-managed-instance.md).
- Per informazioni sui prezzi, vedere [Prezzi dell'istanza gestita di database SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Per informazioni su come creare la prima istanza gestita, vedere [la Guida introduttiva](sql-database-managed-instance-get-started.md).
