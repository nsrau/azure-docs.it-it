---
title: Ritiro del livello di servizio Premium RS del database SQL di Azure | Microsoft Docs
description: Il livello di servizio Premium RS verrà ritirato e il relativo supporto non sarà più disponibile - Vedere le opzioni per la migrazione.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: aa9f3b42bbbe8255be328240d26ed4b8740cc5f7
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55958491"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>Ritiro del livello di servizio Premium RS (anteprima) del database SQL di Azure - Opzioni per la migrazione

Nel mese di febbraio 2018 Microsoft ha annunciato che il livello di servizio Premium RS nel database SQL di Azure non verrà rilasciato per la disponibilità generale e non sarà più supportato dopo il 31 gennaio 2019. Questa data di fine supporto è stata estesa al 30 giugno 2019. Questo articolo illustra le opzioni disponibili per eseguire la migrazione dal livello di servizio Premium RS a un altro livello di servizio. Dopo il 30 giugno 2019 Microsoft eseguirà automaticamente la migrazione dei database Premium RS a un livello di servizio disponibile a livello generale in grado di soddisfare al meglio i requisiti del database Premium RS in termini di prestazioni.

Di seguito sono riportate le destinazioni di migrazione e le opzioni di prezzo più adeguate per i clienti Premium RS:

- Livelli di servizio vCore

  Livelli di servizio **Utilizzo generico** e **Business critical** nel [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md). Questi due livelli di servizio sono in disponibilità generale. Il modello di acquisto basato su vCore offre anche il livello di servizio **Con iperscalabilità** (in anteprima pubblica) che si adatta on demand alle esigenze del carico di lavoro con scalabilità automatica fino a 100 TB per ogni database. Il livello di servizio Con iperscalabilità garantisce prestazioni I/O paragonabili a quelle del livello di servizio Premium nel [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) a un prezzo più vicino a quello del livello di servizio Premium RS.
- Prezzi di sviluppo/test

  I [prezzi di sviluppo/test](https://azure.microsoft.com/pricing/dev-test/) consentono un risparmio fino al 55% rispetto ai prezzi con licenza inclusa per la sottoscrizione di Visual Studio.
- Prezzi di Vantaggio Azure Hybrid e capacità di riserva

  I [prezzi delle offerte Vantaggio Azure Hybrid e capacità di riserva](https://azure.microsoft.com/pricing/details/sql-database/) consentono un risparmio fino all'80% rispetto ai prezzi con licenza inclusa. Per altre informazioni su queste opzioni, vedere [Vantaggio Azure Hybrid per SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) e [Capacità di riserva del database SQL di Azure](sql-database-reserved-capacity.md).

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>Prepararsi ora per eseguire la migrazione dei database Premium RS a livelli di servizio alternativi del database SQL

Leggere le indicazioni contenute in questo articolo insieme alle informazioni sui prezzi e alla documentazione per determinare le destinazioni di migrazione appropriate per i carichi di lavoro Premium RS di cui si dispone.

## <a name="migrate-compute-intensive-workloads-and-save"></a>Eseguire la migrazione di carichi di lavoro a elevato utilizzo di calcolo senza rinunciare al risparmio

Per i carichi di lavoro Premium RS a elevato utilizzo di calcolo, è consigliabile eseguire la migrazione al livello di servizio Utilizzo generico basato su vCore disponibile a livello generale e ottenere un risparmio ancora maggiore rispetto ai prezzi con licenza inclusa optando per le offerte Vantaggio Azure Hybrid per SQL Server e capacità di riserva. Se invece si preferisce un'opzione di acquisto basata su DTU, è possibile eseguire la migrazione dei database Premium RS a elevato utilizzo di calcolo a un livello di servizio Standard e risparmiare comunque rispetto ai prezzi di disponibilità generale di Premium RS (se fosse andato in disponibilità generale).

> [!WARNING]
> La migrazione dei carichi di lavoro Premium RS a livelli di servizio Premium basati su DTU può comportare un aumento dei costi mensili rispetto ai prezzi correnti di Premium RS. È consigliabile prendere in considerazione i livelli Con iperscalabilità e Business critical con i prezzi delle offerte Vantaggio Azure Hybrid e capacità di riserva per avere costi analoghi o inferiori a quelli del livello Premium RS.

### <a name="premium-rs-databases"></a>Database Premium RS

|**Se attualmente si usa…**|**Eseguire la migrazione a livelli basati su vCore comparabili…**|**Eseguire la migrazione a livelli basati su DTU comparabili…**|
|---|---|---|
|Premium RS 1|Uso generico 1 vCore (Gen4)|Standard 3|
|Premium RS 2|Uso generico 2 vCore (Gen 4)|Standard 4|
|Premium RS 4|Utilizzo generico 4 vCore (Gen4)|Standard 6|
|Premium RS 6|Utilizzo generico 6 vCore (Gen4)|Standard 7|

### <a name="premium-rs-pools"></a>Pool Premium RS

|**Se attualmente si usa…**|**Eseguire la migrazione a livelli basati su vCore comparabili…**|**Eseguire la migrazione a livelli basati su DTU comparabili…**|
|---|---|---|
|Pool Premium RS 125 DTU|Uso generico 1 vCore (Gen4)|Pool Standard 100 eDTU|
|Pool Premium RS 250 DTU|Uso generico 2 vCore (Gen 4)|Pool Standard 250 eDTU|
|Pool Premium RS 500 DTU|Utilizzo generico 4 vCore (Gen4)|Pool Standard 500 eDTU|
|Pool Premium RS 1000 DTU|Utilizzo generico 8 vCore (Gen4)|Pool Standard 1000 eDTU|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>Massimizzare il risparmio e le prestazioni per i carichi di lavoro con I/O elevato

Per ottenere una combinazione ideale di prestazioni e costi, è consigliabile eseguire la migrazione dei database singoli con I/O elevato al livello Con iperscalabilità basato su vCore, attualmente in anteprima, e i pool di database con I/O elevato al livello Business critical disponibile a livello generale.  Le opzioni basate su vCore riportate di seguito consentono di mantenere o migliorare le prestazioni correnti, ma anche di risparmiare se combinate con le offerte Vantaggio Azure Hybrid e capacità di riserva.

|**Se attualmente si usa…**|**Eseguire la migrazione a livelli basati su vCore comparabili…**|**Eseguire la migrazione a livelli basati su DTU comparabili…**|
|---|---|---|
|Premium RS 1|(Anteprima) Con iperscalabilità 1 vCore (Gen 4) o Business critical 1 vCore (Gen 4)|Premium 1|
|Premium RS 2|(Anteprima) Con iperscalabilità 2 vCore (Gen 4) o Business critical 2 vCore (Gen 4)|Premium 2|
|Premium RS 4|(Anteprima) Con iperscalabilità 4 vCore (Gen 4) o Business critical 4 vCore (Gen 4)|Premium 4
|Premium RS 6|(Anteprima) Con iperscalabilità 6 vCore (Gen 4) o Business critical 6 vCore (Gen 4)|Premium 6|

|**Se attualmente si usa…**|**Eseguire la migrazione a livelli basati su vCore comparabili…**|**Eseguire la migrazione a livelli basati su DTU comparabili…**|
|---|---|---|
|Pool Premium RS 125 DTU|Business critical 2 vCore (Gen4)|Pool Premium 125 eDTU|
|Pool Premium RS 250 DTU|Business critical 2 vCore (Gen4)|Pool Premium 250 eDTU|
|Pool Premium RS 500 DTU|Business critical 4 vCore (Gen4)|Pool Premium 500 eDTU|
|Pool Premium RS 1000 DTU|Business critical 8 vCore (Gen4)|Pool Premium 1000 eDTU|

## <a name="take-advantage-of-our-new-offers"></a>Approfittare delle nuove offerte

I livelli di servizio del modello di acquisto basato su vCore danno diritto a offerte speciali che consentono di risparmiare fino all'80% rispetto ai prezzi con licenza inclusa. Usare le licenze SQL Server Standard o Enterprise Edition di cui si dispone con Software Assurance attiva per risparmiare fino al 55% rispetto ai prezzi con licenza inclusa con l'offerta [Vantaggio Azure Hybrid per SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). È possibile combinare il Vantaggio Azure Hybrid con l'offerta [Capacità di riserva del database SQL di Azure](sql-database-reserved-capacity.md) e risparmiare fino all'80% impegnandosi in anticipo per un periodo di uno o tre anni.  È possibile attivare entrambi i vantaggi già da oggi sul portale di Azure.

In caso di dubbi o problemi riguardanti questo cambiamento o per assistenza relativa alla migrazione, contattare [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>Migrazione da un livello di servizio Premium RS a un livello di servizio del modello DTU o vCore

### <a name="migration-of-a-database"></a>Migrazione di un database

La migrazione di un database da un livello di servizio Premium RS a un livello di servizio incluso nel modello DTU o vCore è simile all'aggiornamento o al downgrade tra livelli di servizio nel livello di servizio Premium RS.

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>Uso della copia di database per convertire un database Premium RS in un database basato su DTU o su vCore

È possibile copiare un database con dimensioni di calcolo Premium RS in un database con dimensioni di calcolo basate su DTU o vCore senza restrizioni o particolari regole di sequenziazione, a condizione che le dimensioni di calcolo del database di destinazione supportino le dimensioni massime del database di origine. L'operazione di copia del database crea inizialmente uno snapshot dei dati e non esegue la sincronizzazione dei dati tra l'origine e la destinazione.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate sulle dimensioni di calcolo specifiche e sulle opzioni relative alle dimensioni di archiviazione disponibili per database singoli, vedere [Limiti delle risorse basate su vCore del database SQL per database singoli](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)
- Per informazioni dettagliate sulle dimensioni di calcolo specifiche e sulle opzioni relative alle dimensioni di archiviazione disponibili per i pool elastici, vedere [Limiti delle risorse basate su vCore del database SQL per i pool elastici](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
