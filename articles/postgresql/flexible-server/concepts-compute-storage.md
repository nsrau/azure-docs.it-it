---
title: Opzioni di calcolo e archiviazione-database di Azure per PostgreSQL-server flessibile
description: Questo articolo descrive le opzioni di calcolo e archiviazione nel database di Azure per PostgreSQL-server flessibile.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: ca60c44d1e167367e2c138af1e7bfd4ba1a69417
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710074"
---
# <a name="compute-and-storage-options-in-azure-database-for-postgresql---flexible-server"></a>Opzioni di calcolo e archiviazione nel database di Azure per PostgreSQL-server flessibile

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

È possibile creare un database di Azure per il server PostgreSQL in uno dei tre diversi piani tariffari: in sequenza, per utilizzo generico e con ottimizzazione per la memoria. I piani tariffari si differenziano per le risorse di calcolo in vCore di cui è possibile effettuare il provisioning, per la memoria in ogni vCore e per la tecnologia usata per l'archiviazione dei dati. Il provisioning di tutte le risorse viene effettuato a livello di server PostgreSQL. Un server può avere uno o più database.

| Risorsa/livello | **Burst** | **Utilizzo generico** | **Con ottimizzazione per la memoria** |
|:---|:----------|:--------------------|:---------------------|
| vCore | 1, 2 | 2, 4, 8, 16, 32, 48, 64 | 2, 4, 8, 16, 32, 48, 64 |
| Memoria per vCore | Variabile | 4 GB | da 6,75 a 8 GB |
| Dimensioni dello spazio di archiviazione | da 32 GB a 16 TB | da 32 GB a 16 TB | da 32 GB a 16 TB |
| Periodo di conservazione dei backup dei database | Da 7 a 35 giorni | Da 7 a 35 giorni | Da 7 a 35 giorni |

Per scegliere un piano tariffario, usare la tabella seguente come punto di partenza.

| Piano tariffario | Carichi di lavoro di destinazione |
|:-------------|:-----------------|
| Burst | Migliore per i carichi di lavoro che non necessitano di una CPU completa in modo continuo. |
| Utilizzo generico | La maggior parte dei carichi di lavoro aziendali che richiedono risorse di calcolo e di memoria bilanciate con velocità effettiva di I/O scalabile. Ad esempio, server per l'hosting di app Web e di app per dispositivi mobili e altre applicazioni aziendali.|
| Con ottimizzazione per la memoria | Carichi di lavoro di database ad alte prestazioni che richiedono prestazioni in memoria per l'elaborazione più rapida delle transazioni e una concorrenza maggiore. Ad esempio, server per l'elaborazione di dati in tempo reale e app transazionali o analitiche a prestazioni elevate.|

Dopo aver creato un server, il livello di calcolo, il numero di Vcore e le dimensioni di archiviazione possono essere modificati in pochi secondi. È anche possibile modificare in modo indipendente il periodo di conservazione dei backup. Per ulteriori informazioni, vedere la sezione [scale Resources](#scale-resources) .

## <a name="compute-tiers-vcores-and-server-types"></a>Livelli di calcolo, Vcore e tipi di server

Le risorse di calcolo possono essere selezionate in base al livello, Vcore e le dimensioni della memoria. VCore rappresentano la CPU logica dell'hardware sottostante.

Le specifiche dettagliate dei tipi di server disponibili sono le seguenti:

| Nome SKU             | vCore | Dimensione della memoria | Numero massimo di IOPS supportati | Larghezza di banda I/O massima supportata |
|----------------------|--------|-------------|--------------------|-----------------------------|
| **Burst**        |        |             |                    |                             |
| B1ms                 | 1      | 2 GiB       | 640                | 15 MiB/sec                  |
| B2S                  | 2      | 4 GiB       | 1280               | 15 MiB/sec                  |
| **Utilizzo generico**  |        |             |                    |                             |
| D2s_v3               | 2      | 8 GiB       | 3200               | 48 MiB/sec                  |
| D4s_v3               | 4      | 16 GiB      | 6400               | 96 MiB/sec                  |
| D8s_v3               | 8      | 32 GiB      | 12800              | 192 MiB/sec                 |
| D16s_v3              | 16     | 64 GiB      | 18000              | 384 MiB/sec                 |
| D32s_v3              | 32     | 128 GiB     | 18000              | 750 MiB/sec                 |
| D48s_v3              | 48     | 192 GiB     | 18000              | 750 MiB/sec                 |
| D64s_v3              | 64     | 256 GiB     | 18000              | 750 MiB/sec                 |
| **Con ottimizzazione per la memoria** |        |             |                    |                             |
| E2s_v3               | 2      | 16 GiB      | 3200               | 48 MiB/sec                  |
| E4s_v3               | 4      | 32 GiB      | 6400               | 96 MiB/sec                  |
| E8s_v3               | 8      | 64 GiB      | 12800              | 192 MiB/sec                 |
| E16s_v3              | 16     | 128 GiB     | 18000              | 384 MiB/sec                 |
| E32s_v3              | 32     | 256 GiB     | 18000              | 750 MiB/sec                 |
| E48s_v3              | 48     | GiB 384     | 18000              | 750 MiB/sec                 |
| E64s_v3              | 64     | 432 GiB     | 18000              | 750 MiB/sec                 |

## <a name="storage"></a>Archiviazione:

Lo spazio di archiviazione di cui si esegue il provisioning è la capacità di archiviazione disponibile per il server Database di Azure per PostgreSQL. Lo spazio di archiviazione viene usato per i file del database, i file temporanei, i log delle transazioni e i log del server PostgreSQL. Lo spazio di archiviazione totale di cui si effettua il provisioning definisce anche la capacità di I/O disponibile per il server.

Lo spazio di archiviazione è disponibile nelle dimensioni fisse seguenti:

| Dimensioni del disco | Operazioni di I/O al secondo |
|:---|:---|
| 32 GiB | Provisioning 120, fino a 3.500 |
| 64 GiB | Provisioning 240, fino a 3.500 |
| 128 GiB | Provisioning 500, fino a 3.500 |
| 256 GiB | Provisioning 1100, fino a 3.500 |
| 512 GiB | Provisioning 2300, fino a 3.500 |
| 1 TiB | 5\.000 |
| 2 TiB | 7.500 |
| 4 TiB | 7\.500 |
| 8 TiB | 16.000 |
| 16 TiB | 18.000 |

Si noti che gli IOPS sono limitati anche dal tipo di macchina virtuale. Anche se è possibile selezionare qualsiasi dimensione di archiviazione indipendente dal tipo di server, potrebbe non essere possibile utilizzare tutte le operazioni di i/o al secondo fornite dall'archiviazione, soprattutto quando si sceglie un server con un numero ridotto di vcore.

È possibile aggiungere capacità di archiviazione durante e dopo la creazione del server.

>[!NOTE]
> Lo spazio di archiviazione può essere scalato solo, non inattivo.

È possibile monitorare il consumo di I/O nel portale di Azure oppure usando i comandi dell'interfaccia della riga di comando di Azure. Le metriche rilevanti per il monitoraggio sono il [limite di archiviazione, la percentuale di archiviazione, lo spazio di archiviazione usato e la percentuale di io](concepts-monitoring.md).

### <a name="maximum-iops-for-your-configuration"></a>Numero massimo di IOPS per la configurazione

|Nome SKU            |Dimensioni di archiviazione in GiB                       |32 |64 |128 |256 |512  |1\.024|2\.048|4\.096|8,192 |16.384|
|--------------------|------------------------------------------|---|---|----|----|-----|-----|-----|-----|------|------|
|                    |Numero massimo di IOPS                              |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|**Burst**       |                                          |   |   |    |    |     |     |     |     |      |      |
|B1ms                |640 IOPS                                  |120|240|500 |640 *|640 * |640 * |640 * |640 * |640 *  |640 *  |
|B2S                 |1280 IOPS                                 |120|240|500 |1100|1280 *|1280 *|1280 *|1280 *|1280 * |1280 * |
|**Utilizzo generico** |                                          |   |   |    |    |     |     |     |     |      |      |
|D2s_v3              |3200 IOPS                                 |120|240|500 |1100|2300 |3200 *|3200 *|3200 *|3200 * |3200 * |
|D4s_v3              |6.400 IOPS                                |120|240|500 |1100|2300 |5000 |6400 *|6400 *|6400 * |6400 * |
|D8s_v3              |12.800 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |12800 *|12800 *|
|D16s_v3             |18.000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|D32s_v3             |18.000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|D48s_v3             |18.000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|D64s_v3             |18.000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|**Con ottimizzazione per la memoria**|                                          |   |   |    |    |     |     |     |     |      |      |
|E2s_v3              |3200 IOPS                                 |120|240|500 |1100|2300 |3200 *|3200 *|3200 *|3200 * |3200 * |
|E4s_v3              |6.400 IOPS                                |120|240|500 |1100|2300 |5000 |6400 *|6400 *|6400 * |6400 * |
|E8s_v3              |12.800 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |12800 *|12800 *|
|E16s_v3             |18.000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|E32s_v3             |18.000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|E48s_v3             |18.000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|E64s_v3             |18.000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |

Quando contrassegnato con un \* , i valori di IOPS sono limitati dal tipo di macchina virtuale selezionato. In caso contrario, i valori di IOPS sono limitati dalle dimensioni di archiviazione selezionate.

### <a name="maximum-io-bandwidth-mibsec-for-your-configuration"></a>Larghezza di banda di I/O massima (MiB/sec) per la configurazione

|Nome SKU            |Dimensioni di archiviazione, GiB                             |32 |64 |128 |256 |512  |1\.024|2\.048|4\.096|8,192 |16.384|
|--------------------|----------------------------------------------|---|---|----|----|-----|-----|-----|-----|------|------|
|                    |**Larghezza di banda di archiviazione, MiB/sec**                |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|**Burst**       |                                              |   |   |    |    |     |     |     |     |      |      |
|B1ms                |10 MiB/sec                                    |10|10|10 |10 |10  |10  |10  |10  |10   |10   |
|B2S                 |15 MiB/sec                                    |15|15|15 |15 |15  |15  |15  |15  |15   |15   |
|**Utilizzo generico** |                                              |   |   |    |    |     |     |     |     |      |      |
|D2s_v3              |48 MiB/sec                                    |25 |48 *|48 * |48 * |48 *  |48 *  |48 *  |48 *  |48 *   |48 *   |
|D4s_v3              |96 MiB/sec                                    |25 |50 |96 * |96 * |96 *  |96 *  |96 *  |96 *  |96 *   |96 *   |
|D8s_v3              |192 MiB/sec                                   |25 |50 |100 |125 |150  |192 * |192 * |192 * |192 *  |192 *  |
|D16s_v3             |384 MiB/sec                                   |25 |50 |100 |125 |150  |200  |250  |250  |384 *  |384 *  |
|D32s_v3             |750 MiB/sec                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|D48s_v3             |750 MiB/sec                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|D64s_v3             |750 MiB/sec                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|**Con ottimizzazione per la memoria**|                                              |   |   |    |    |     |     |     |     |      |      |
|E2s_v3              |48 MiB/sec                                    |25 |48 *|48 * |48 * |48 *  |48 *  |48 *  |48 *  |48 *   |48 *   |
|E4s_v3              |96 MiB/sec                                    |25 |50 |96 * |96 * |96 *  |96 *  |96 *  |96 *  |96 *   |96 *   |
|E8s_v3              |192 MiB/sec                                   |25 |50 |100 |125 |150  |192 * |192 * |192 * |192 *  |192 *  |
|E16s_v3             |384 MiB/sec                                   |25 |50 |100 |125 |150  |200  |250  |250  |384 *  |384 *  |
|E32s_v3             |750 MiB/sec                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|E48s_v3             |750 MiB/sec                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|E64s_v3             |750 MiB/sec                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |

Quando contrassegnato con un \* , la larghezza di banda di i/O è limitata dal tipo di macchina virtuale selezionato. In caso contrario, la larghezza di banda di I/O è limitata dalle dimensioni di archiviazione selezionate.

### <a name="reaching-the-storage-limit"></a>Raggiungimento del limite di archiviazione

Quando si raggiunge il limite di archiviazione, il server inizierà a restituire errori e impedirà ulteriori modifiche. Questo può anche causare problemi con altre attività operative, ad esempio i backup e l'archiviazione WAL.

Si consiglia di monitorare attivamente lo spazio su disco in uso e aumentare le dimensioni del disco in anticipo rispetto alla situazione di archiviazione. È possibile impostare un avviso per ricevere una notifica quando l'archiviazione del server si avvicina all'esterno del disco, in modo da evitare eventuali problemi di esaurimento del disco. Per altre informazioni, vedere la documentazione sulla [procedura di configurazione di un avviso](howto-alert-on-metrics.md).

### <a name="storage-auto-grow"></a>Aumento automatico dell'archiviazione

L'aumento automatico delle dimensioni dell'archiviazione non è ancora disponibile per il server flessibile.

## <a name="backup"></a>Backup

Il servizio esegue automaticamente il backup del server. È possibile selezionare un periodo di conservazione compreso tra 7 e 35 giorni. Per altre informazioni sui backup, vedere l' [articolo concetti](concepts-backup-restore.md).

## <a name="scale-resources"></a>Ridimensionare le risorse

Dopo aver creato il server, è possibile modificare in modo indipendente il vcore, il livello di calcolo, la quantità di spazio di archiviazione e il periodo di conservazione dei backup. Il numero di vCore può essere aumentato o ridotto. Il periodo di conservazione dei backup può essere aumentato o ridotto da 7 a 35 giorni. Le dimensioni dello spazio di archiviazione possono essere solo aumentate. Il ridimensionamento delle risorse può essere eseguito tramite il portale o l'interfaccia della riga di comando di Azure.

> [!NOTE]
> Le dimensioni dello spazio di archiviazione possono essere solo aumentate. Non è possibile tornare a una dimensione di archiviazione inferiore dopo l'incremento.

Quando si modifica il numero di Vcore o del livello di calcolo, il server viene riavviato per rendere effettivo il nuovo tipo di server. Durante l'intervallo nel quale il sistema passa al nuovo server, non è possibile stabilire nuove connessioni e viene effettuato il rollback di tutte le transazioni di cui non è stato eseguito il commit. Questo intervallo è variabile, ma nella maggior parte dei casi è inferiore al minuto. Il ridimensionamento dello spazio di archiviazione funziona allo stesso modo e richiede anche un breve riavvio.

La modifica del periodo di conservazione dei backup è un'operazione online.

## <a name="pricing"></a>Prezzi

Per le informazioni più aggiornate sui prezzi, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/PostgreSQL/). Per informazioni sui costi della configurazione desiderata, consultare la scheda **Piano tariffario** del [portale di Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) che illustra il costo mensile in base alle opzioni selezionate. Se non è disponibile una sottoscrizione di Azure, è possibile usare il calcolatore dei prezzi di Azure per ottenere una stima. Passare al sito Web del [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/), selezionare **Aggiungi elementi**, espandere la categoria **Database** e scegliere **Database di Azure per PostgreSQL** per personalizzare le opzioni.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su come [creare un server PostgreSQL nel portale](how-to-manage-server-portal.md).
- Informazioni sui [limiti dei servizi](concepts-limits.md).
