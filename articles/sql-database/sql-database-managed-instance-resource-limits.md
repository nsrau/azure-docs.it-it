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
ms.reviewer: carlrab, jovanpop, sachinp
manager: craigg
ms.date: 02/05/2019
ms.openlocfilehash: 799f47b291b3943dcb4d6b31bc8df732c28990cd
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816595"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Panoramica dei limiti delle risorse del database SQL di Azure per le istanze gestite

Questo articolo offre una panoramica sui limiti delle risorse del database SQL di Azure per le istanze gestite e fornisce informazioni su come creare richieste per aumentare i limiti delle sottoscrizioni a livello di area predefinite.

> [!NOTE]
> Per informazioni su altri limiti delle istanze gestite, vedere [Modello di acquisto in base ai vCore](sql-database-managed-instance.md#vcore-based-purchasing-model) e [Livelli di servizio di Istanza gestita](sql-database-managed-instance.md#managed-instance-service-tiers). Per informazioni sulle differenze nelle funzionalità e nelle istruzioni T-SQL supportate, vedere [Confronto tra le funzionalità](sql-database-features.md) e [Differenze T-SQL tra Istanza gestita del database SQL di Azure e SQL Server](sql-database-managed-instance-transact-sql-information.md).

## <a name="instance-level-resource-limits"></a>Limiti delle risorse a livello di istanza

Istanza gestita ha caratteristiche e limiti delle risorse che dipendono dall'infrastruttura e dall'architettura sottostanti. I limiti dipendono dalla generazione dell'hardware e dal livello di servizio.

### <a name="hardware-generation-characteristics"></a>Caratteristiche delle generazioni dell'hardware

Istanza gestita di database SQL può essere distribuita in due generazioni hardware (Quarta generazione e Quinta generazione). Le generazioni hardware hanno caratteristiche diverse descritte nella tabella seguente:

|   | **Generazione 4** | **Generazione 5** |
| --- | --- | --- |
| Hardware | Processori Intel E5-2673 v3 (Haswell) a 2,4 GHz, con unità vCore SSD = 1 PP (core fisico) | Processori Intel E5-2673 v4 (Broadwell) a 2,3 GHz, unità SSD NVMe veloce, vCore = 1 LP (hyperthread) |
| Calcolo | 8, 16, 24 vCore | 8, 16, 24, 32, 40, 64, 80 vCore |
| Memoria | 7 GB per vCore | 5,1 GB per vCore |
| Memoria OLTP in memoria | 3 GB per vCore | 2,6 GB per vCore |
| Spazio di archiviazione massimo (utilizzo generico) |  8 TB | 1 TB |
| Spazio di archiviazione massimo (Business critical) | 1 TB | 1 TB, 2 TB o 4 TB in base al numero di core |

### <a name="service-tier-characteristics"></a>Caratteristiche del livello di servizio

Istanza gestita ha due livelli di servizio: per utilizzo generico e business critical. Questi livelli offrono funzionalità diverse, come descritto nella tabella seguente:

| **Funzionalità** | **Utilizzo generico** | **Business Critical** |
| --- | --- | --- |
| Numero di vCore\* | Quarta generazione: 8, 16, 24<br/>Quinta generazione: 8, 16, 24, 32, 40, 64, 80 | Quarta generazione: 8, 16, 24, 32 <br/> Quinta generazione: 8, 16, 24, 32, 40, 64, 80 |
| Memoria | Quarta generazione: 56 GB - 168 GB<br/>Quinta generazione: 40,8 GB - 408 GB<br/>\*Proporzionale al numero di vCore | Quarta generazione: 56 GB - 168 GB <br/> Quinta generazione: 40,8 GB - 408 GB<br/>\*Proporzionale al numero di vCore |
| Dimensione massima archiviazione | 8 TB | Quarta generazione: 1 TB <br/> 5° generazione: <br/>- 1 TB per 8, 16 vCore<br/>- 2 TB per 24 vCore<br/>- 4 TB per 32, 40, 64, 80 vCore |
| Quantità massima di risorse di archiviazione per database | Determinata dalla dimensione massima di archiviazione per ogni istanza | Determinata dalla dimensione massima di archiviazione per ogni istanza |
| Numero massimo di database per istanza | 100 | 100 |
| Numero massimo di file di database per istanza | Fino a 280 | 32.767 file per ogni database |
| Dati/Log di IOPS (approssimativi) | 500 - 7.500 per file<br/>\*[In base alle dimensioni del file di dati](https://docs.microsoft.com/azure/virtual-machines).| 11 K - 110 K (1.375 per vCore) |
|Velocità effettiva di log | 22 MB/s per ogni istanza | 3 MB/s per ogni vCore<br/>Max 48 MB/s |
| Dati effettivi (approssimativi) | 100 - 250 MB/s per ogni file<br/>\*[In base alle dimensioni del file di dati](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes). | 24 - 48 MB/s per ogni vCore |
| Latenza di I/O (approssimativa) | 5-10 ms | 1-2 ms |
| Dimensioni max di tempDB | 192 - 1.920 GB (24 GB per vCore) | Nessun vincolo; limitato dalla dimensione massima di archiviazione dell'istanza |

**Note**:

- La dimensione di archiviazione dell'istanza, che viene confrontata con la dimensione massima di archiviazione, include la dimensione dei dati e dei file di log presenti sia nel database utenti che in quello di sistema. Usare la vista di sistema <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> per determinare lo spazio totale usato dai database. I log degli errori non vengono salvati in modo permanente e non sono inclusi nella dimensione. I backup non sono inclusi nella dimensione di archiviazione.
- Anche IOPS e velocità effettiva dipendono dalle dimensioni pagina che non vengono limitate in modo esplicito dall'Istanza gestita di database SQL di Azure.

## <a name="supported-regions"></a>Aree supportate

Le istanze gestite possono essere create solo in [aree supportate](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Se si vuole creare un'istanza gestita in un'area attualmente non supportata, è possibile [inviare una richiesta di supporto tramite il portale di Azure](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Tipi di sottoscrizioni supportati

Istanza gestita supporta attualmente solo la distribuzione dei tipi di sottoscrizioni seguenti:

- [Contratto Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Provider di servizi cloud (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Sviluppo/test Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0023p/)

> [!NOTE]
> Questa limitazione è temporanea. In futuro verranno abilitati nuovi tipi di sottoscrizioni.

## <a name="regional-resource-limitations"></a>Limiti delle risorse a livello di area

I tipi di sottoscrizioni supportati possono contenere un numero limitato di risorse per area. Istanza gestita ha due limiti predefiniti per area di Azure a seconda del tipo di sottoscrizione:

- **Limite di subnet**: numero massimo di subnet in cui vengono distribuite le istanze gestite in una singola area.
- **Limite del numero di istanze**: numero massimo di istanze che possono essere distribuite in una singola area.

La tabella seguente indica i limiti predefiniti a livello di area per le sottoscrizioni supportate:

|Tipo di sottoscrizione| Numero massimo di subnet dell'istanza gestita | Numero massimo di istanze |Numero massimo di istanze gestite GP*|Numero massimo di istanze gestite BC*|
| :---| :--- | :--- |:--- |:--- |
|Pagamento in base al consumo|1*|4*|4*|1*|
|CSP |1*|4*|4*|1*|
|Sviluppo/test con pagamento in base al consumo|1*|4*|4*|1*|
|Sviluppo/test Enterprise|1*|4*|4*|1*|
|Contratto Enterprise|3**|12**|12**|3**|

\* È possibile distribuire 1 istanza business critical (BC) o 4 istanze per utilizzo generico (GP) in una subnet, in modo che il numero totale di "unità di istanza" non sia mai superiore a 4.

** Viene applicato il numero massimo di istanze in un livello di servizio se non sono presenti istanze in un altro livello di servizio. Se si prevede di combinare istanze per utilizzo generico e business critical nella stessa subnet, usare la sezione seguente come riferimento per le combinazioni consentite. Il numero totale di subnet non può essere superiore a 3 e il numero totale di unità di istanza non può essere superiore a 12.

Questi limiti possono essere aumentati creando speciali [richieste di supporto nel portale di Azure](#obtaining-a-larger-quota-for-sql-managed-instance), se servono più istanze gestite nell'area corrente. In alternativa, è possibile creare nuove istanze gestite in un'altra area di Azure senza inviare richieste di supporto.

> [!IMPORTANT]
> Durante la pianificazione delle distribuzioni, tenere presente che un'istanza business critical (BC) (a causa della maggiore ridondanza) generalmente consuma 4 volte la capacità di un'istanza per utilizzo generico (GP). Quindi, ai fini dei calcoli, 1 istanza per utilizzo generico = 1 unità di istanza e 1 istanza business critical = 4 unità di istanza. Per semplificare l'analisi del consumo in base ai limiti predefiniti, riepilogare le unità di istanza in tutte le subnet nell'area in cui vengono distribuite istanze gestite e confrontare i risultati con i limiti di unità di istanza per il tipo di sottoscrizione.

## <a name="strategies-for-deploying-mixed-general-purpose-and-business-critical-instances"></a>Strategie per la distribuzione mista di istanze per utilizzo generico e business critical

Le sottoscrizioni di tipo [Contratto Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) possono avere combinazioni di istanze per utilizzo generico e business critical. Esistono tuttavia alcuni vincoli relativi al posizionamento delle istanze nelle subnet.

> [!Note]
> I tipi di sottoscrizione [con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) e [Provider di servizi cloud (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources) possono avere un'istanza business critical o fino a 4 istanze per utilizzo generico.

Gli esempi seguenti illustrano casi di distribuzione con subnet non vuote e livelli di servizio misti Utilizzo generico e Business critical.

|Numero di subnet|Subnet 1|Subnet 2|Subnet 3|
|:---|:---|:---|:---|
|1|1 BC e fino a 8 GP<br>2 BC e fino a 4 GP|N/D| N/D|
|2|0 BC, fino a 4 GP|1 BC, fino a 4 GP<br>2 BC, 0 GP|N/D|
|2|1 BC, 0 GP|0 BC, fino a 8 GP<br>1 BC, fino a 4 GP|N/D|
|2|2 BC, 0 GP|0 BC, fino a 4 GP|N/D|
|3|1 BC, 0 GP|1 BC, 0 GP|0 BC, fino a 4 GP|
|3|1 BC, 0 GP|0 BC, fino a 4 GP|0 BC, fino a 4 GP|

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Come ottenere una quota maggiore per Istanza gestita di SQL

Se servono più istanze gestite nelle aree correnti, è possibile inviare una richiesta di supporto per estendere la quota tramite il portale di Azure.
Per avviare il processo di acquisizione di una quota maggiore:

1. Aprire **Guida e supporto** e fare clic su **Nuova richiesta di supporto**.

   ![Guida e supporto](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Nella scheda Generale per la nuova richiesta di supporto:
   - Per **Tipo di problema** selezionare **Limiti del servizio e della sottoscrizione (quote)**.
   - In **Sottoscrizione** selezionare la propria sottoscrizione.
   - Per **Tipo di quota** selezionare **Istanza gestita di database SQL**.
   - Per **Piano di supporto** selezionare il piano di supporto in uso.

     ![Tipo di problema: Quota](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Fare clic su **Avanti**.
4. Nella scheda Problema per la nuova richiesta di supporto:
   - Per **Gravità** selezionare il livello di gravità del problema.
   - In **Dettagli** inserire informazioni aggiuntive relative al problema riscontrato, inclusi i messaggi di errore.
   - In **Caricamento file** allegare un file con ulteriori informazioni (fino a 4 MB).

     ![Dettagli del problema](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Una richiesta valida deve includere:
     > - L'area in cui è necessario aumentare il limite della sottoscrizione
     > - Il numero di istanze necessario, per livello di servizio nelle subnet esistenti dopo l'aumento della quota (in caso occorra espandere una o più delle subnet esistenti)
     > - Il numero di nuove subnet necessario e il numero totale di istanze per livello di servizio nelle nuove subnet (in caso occorra distribuire istanze gestite in nuove subnet).

5. Fare clic su **Avanti**.
6. Nella scheda Informazioni contatto della nuova richiesta di supporto immettere la modalità di contatto preferita (posta elettronica o telefono) e i dettagli di contatto.
7. Fare clic su **Create**(Crea).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su Istanza gestita, vedere [Informazioni su Istanza gestita](sql-database-managed-instance.md).
- Per informazioni sui prezzi, vedere [Prezzi di Istanza gestita di database SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Per informazioni su come creare la prima istanza gestita, vedere [Guida introduttiva](sql-database-managed-instance-get-started.md).
