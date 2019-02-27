---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8a067474fb172d4ff7a7fdf7eb6d24536bd2d017
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56443312"
---
# <a name="what-disk-types-are-available-in-azure"></a>Quali tipi di dischi sono disponibili in Azure?

I dischi gestiti di Azure offrono attualmente quattro tipi di dischi, tre dei quali sono disponibili a livello generale (GA) e uno è attualmente in anteprima. Ognuno di questi quattro tipi di dischi ha un proprio scenario del cliente appropriato.

## <a name="disk-comparison"></a>Confronto dei dischi

La tabella seguente mette a confronto unità Ultra SSD (anteprima), SSD Premium, SSD Standard e HDD Standard per dischi gestiti in modo da aiutare a scegliere quali usare.

|   | Ultra SSD (anteprima)   | SSD Premium   | SSD Standard   | HDD Standard   |
|---------|---------|---------|---------|---------|
|Tipo di disco   |SSD   |SSD   |SSD   |HDD   |
|Scenario   |Carichi di lavoro con I/O elevato come SAP HANA, database di alto livello (ad esempio SQL, Oracle) e altri carichi di lavoro con numerose transazioni.   |Carichi di lavoro di produzione su cui influiscono le prestazioni   |Server Web, applicazioni aziendali con un utilizzo non intensivo e sviluppo/test   |Backup, carichi di lavoro non critici, accesso poco frequente   |
|Dimensioni disco   |65.536 gibibyte (GiB) (Anteprima)   |4.095 GiB (GA), 32.767 GiB (Anteprima)    |4.095 GiB (GA), 32.767 GiB (Anteprima)   |4.095 GiB (GA), 32.767 GiB (Anteprima)   |
|Velocità effettiva massima   |2.000 MiB/s (Anteprima)   |250 (GA) MiB/s, 750 MiB/s (Anteprima)   |60 MiB/s (GA), 500 MiB/s (Anteprima)   |60 MiB/s (GA), 500 MiB/s (Anteprima)   |
|Operazioni di I/O al secondo max   |160.000 (Anteprima)   |7500 (GA), 20.000 (Anteprima)   |500 (GA), 2.000 (Anteprima)   |500 (GA), 2.000 (Anteprima)   |

## <a name="ultra-ssd-preview"></a>Ultra SSD (anteprima)

Le unità Ultra SSD di Azure (anteprima) offrono una velocità effettiva elevata, un numero elevato di operazioni di I/O al secondo e archiviazione su disco con bassa latenza coerente per le macchine virtuali IaaS di Azure. Alcuni vantaggi aggiuntivi delle unità Ultra SSD includono la possibilità di modificare dinamicamente le prestazioni del disco, in base ai carichi di lavoro, senza dover riavviare le macchine virtuali. Le unità Ultra SSD sono idonee per carichi di lavoro a elevato utilizzo di dati, come SAP HANA, database di alto livello e carichi di lavoro con numerose transazioni. Le unità Ultra SSD possono essere usate solo come dischi dati. È consigliabile usare unità SSD Premium come dischi del sistema operativo.

### <a name="performance"></a>Prestazioni

Quando si effettua il provisioning di un disco Ultra, è possibile configurare in modo indipendente la capacità e le prestazioni del disco. Le unità Ultra SSD sono disponibili con diverse dimensioni fisse, da 4 GiB fino a 64 TiB, e offrono un modello di configurazione delle prestazioni flessibile che consente di configurare in modo indipendente il numero di operazioni di I/O al secondo e la velocità effettiva.

Ecco alcune delle caratteristiche principali delle unità SSD Ultra:

- Capacità disco: la capacità delle unità Ultra SSD è compresa tra 4 GiB e 64 TiB.
- Operazioni di I/O al secondo (IOPS) del disco: le unità Ultra SSD supportano limiti di operazioni di I/O al secondo pari a 300 IOPS/GiB, fino a un massimo di 160.000 IOPS per disco. Per ottenere la quantità di operazioni di I/O al secondo di cui è stato effettuato il provisioning, assicurarsi che il numero di operazioni di I/O al secondo del disco selezionato sia inferiore al numero di operazioni di I/O al secondo della macchina virtuale. Il numero minimo di operazioni di I/O al secondo del disco è 100.
- Velocità effettiva del disco: con le unità Ultra SSD, il limite di velocità effettiva di un singolo disco è di 256 KiB/s per ogni operazione di I/O al secondo di cui è stato effettuato il provisioning, fino a un massimo di 2000 MBps per disco (dove MBps = 10^6 byte al secondo). La velocità effettiva minima del disco è di 1 MiB.

### <a name="disk-size"></a>Dimensioni disco

|Dimensioni disco (GiB)  |Limiti operazioni di I/O al secondo  |Limite velocità effettiva (MBps)  |
|---------|---------|---------|
|4     |1200         |300         |
|8     |2400         |600         |
|16     |4800         |1200         |
|32     |9.600         |2.000         |
|64     |19.200         |2.000         |
|128     |38.400         |2.000         |
|256     |76.800         |2.000         |
|512     |80.000         |2.000         |
|1.024-65.536 (dimensioni in questo intervallo con aumento con incrementi di 1 TiB)     |160.000         |2.000         |

### <a name="preview-scope-and-limitations"></a>Limitazioni e ambito della versione di anteprima

Durante la fase di anteprima, i dischi Ultra SSD:

- Sono supportati nell'area Stati Uniti orientali 2 in una singola zona di disponibilità  
- Possono essere usati solo con le zone di disponibilità (i set di disponibilità e le distribuzioni di macchine virtuali singole all'esterno delle zone non consentono di collegare un disco Ultra)
- Sono supportati solo nelle macchine virtuali ES/DS v3
- Sono disponibili solo come dischi dati e supportano solo dimensioni di settori fisici di 4k  
- Possono essere creati solo come dischi vuoti  
- Attualmente possono essere distribuiti solo usando modelli di Azure Resource Manager, l'interfaccia della riga di comando e Python SDK.
- Non supportano ancora snapshot di dischi, immagini di macchine virtuali, set di disponibilità, set di scalabilità di macchine virtuali e Crittografia dischi di Azure.
- Non supportano ancora l'integrazione con Backup di Azure o Azure Site Recovery.
- Come per la  [maggior parte delle versioni di anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), questa funzionalità non deve essere usata per i carichi di lavoro in produzione fino al momento della disponibilità generale (GA).

## <a name="premium-ssd"></a>SSD Premium

I dischi SSD Premium di Azure offrono prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali (VM) con carichi di lavoro con I/O intensivo. È possibile migrare i dischi delle VM esistenti in SSD Premium per trarre vantaggio dalla velocità e dalle prestazioni di questi dischi. I dischi SSD Premium sono idonei per applicazioni di produzione cruciali.

### <a name="disk-size"></a>Dimensioni disco

Le dimensioni contrassegnate con un asterisco sono attualmente in anteprima.

| Dimensioni SSD Premium  | P4               | P6               | P10             | P15 | P20              | P30              | P40              | P50              | P60*              | P70*              | P80*              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Dimensioni disco in GiB           | 32             | 64             | 128            | 256  | 512            | 1.024    | 2.048     | 4.095    | 8,192     | 16.384     | 32.767     |
| IOPS per disco       | Fino a 120 | Fino a 240              | Fino a 500              | Fino a 1.100 | Fino a 2.300              | Fino a 5.000              | Fino a 7.500             | Fino a 7.500              | Fino a 12.500              | Fino a 15.000              | Fino a 20.000              |
| Velocità effettiva per disco | Fino a 25 MiB/sec | Fino a 50 MiB/sec | Fino a 100 MiB/sec | Fino a 125 MiB/sec | Fino a 150 MiB/sec | Fino a 200 MiB/sec | Fino a 250 MiB/sec | Fino a 250 MiB/sec| Fino a 480 MiB/sec | Fino a 750 MiB/sec | Fino a 750 MiB/sec |

## <a name="standard-ssd"></a>SSD Standard

Le unità SSD Standard di Azure sono un'opzione di archiviazione conveniente, ottimizzata per carichi di lavoro che richiedono prestazioni coerenti a livelli più bassi di I/O al secondo. Le unità SSD Standard offrono una buona esperienza iniziale per coloro che vogliono passare al cloud, soprattutto se riscontrano problemi di varianza dei carichi di lavoro in esecuzione nelle soluzioni HDD locali. Le unità SSD Standard offrono disponibilità, coerenza, affidabilità e latenza migliori rispetto ai dischi HDD. Le unità SSD Standard sono idonee per server Web, server applicazioni con un numero ridotto di operazioni di I/O al secondo, applicazioni aziendali con un utilizzo non intensivo e carichi di lavoro di sviluppo/test.

### <a name="disk-size"></a>Dimensioni disco

Le dimensioni contrassegnate con un asterisco sono attualmente in anteprima.

| Dimensioni SSD Standard  | E10               | E15               | E20             | E30 | E40              | E50              | E60*              | E70*              | E80*              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Dimensioni disco in GiB           | 128             | 256             | 512            | 1.024  | 2.048            | 4.095     | 8,192     | 16.384     | 32.767    |
| IOPS per disco       | Fino a 500              | Fino a 500              | Fino a 500              | Fino a 500 | Fino a 500              | Fino a 500              | Fino a 500             | Fino a 500              | Fino a 1.300              | Fino a 2.000              | Fino a 2.000              |
| Velocità effettiva per disco | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec| Fino a 300 MiB/sec |  Fino a 500 MiB/sec | Fino a 500 MiB/sec |

## <a name="standard-hdd"></a>HDD Standard

Le unità HDD Standard di Azure offrono un supporto dei dischi affidabile e a basso costo per le VM che eseguono carichi di lavoro non sensibili alla latenza. Supporta anche BLOB, tabelle, code e file. Con l'archiviazione standard, i dati vengono archiviati in unità disco rigido (HDD). Per le macchine virtuali è possibile usare dischi SSD e HDD Standard per scenari di sviluppo/test e carichi di lavoro meno critici. L'archiviazione standard è disponibile in tutte le aree di Azure.

### <a name="disk-size"></a>Dimensioni disco

Le dimensioni contrassegnate con un asterisco sono attualmente in anteprima.

| Tipo di disco Standard  | S4               | S6               | S10             | S15 | S20              | S30              | S40              | S50              | S60*              | S70*              | S80*              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Dimensioni disco in GiB          | 32             | 64             | 128            | 256  | 512            | 1.024    | 2.048     | 4.095    | 8,192     | 16.384     | 32.767     |
| IOPS per disco       | Fino a 500              | Fino a 500              | Fino a 500              | Fino a 500 | Fino a 500              | Fino a 500              | Fino a 500             | Fino a 500              | Fino a 1.300              | Fino a 2.000              | Fino a 2.000              |
| Velocità effettiva per disco | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec| Fino a 300 MiB/sec | Fino a 500 MiB/sec | Fino a 500 MiB/sec |

## <a name="billing"></a>Fatturazione

Quando si usano dischi gestiti, tenere conto delle considerazioni seguenti relative alla fatturazione:

- Tipo di disco
- Dimensioni dei dischi gestiti
- Snapshot
- Trasferimenti di dati in uscita
- Numero di transazioni

**Dimensioni dei dischi gestiti**: la fatturazione dei dischi gestiti è basata sulle dimensioni di cui è stato effettuato il provisioning. Azure associa le dimensioni di cui è stato effettuato il provisioning (arrotondate per eccesso) all'offerta di dimensioni dei dischi più vicina. Per informazioni dettagliate sulle dimensioni dei dischi disponibili, vedere le tabelle precedenti. Viene eseguito il mapping di ogni disco gestito a un'offerta relativa alle dimensioni di provisioning dei dischi supportate e viene eseguita la relativa fatturazione. Se ad esempio è stato effettuato il provisioning di un'unità SSD Standard da 200 GiB, tale unità viene associata all'offerta relativa alle dimensioni dei dischi E15 (256 GiB). La fatturazione per qualsiasi disco di cui sia stato effettuato il provisioning viene ripartita in modo proporzionale in base alle ore usando il prezzo mensile dell'offerta di Archiviazione Premium. Se ad esempio è stato effettuato il provisioning di un disco E10 e lo si è eliminato dopo 20 ore, verranno fatturate 20 ore per l'offerta E10, indipendentemente dalla quantità di dati effettivamente scritti sul disco.

**Snapshot**: Gli snapshot vengono fatturati in base alle dimensioni usate. Ad esempio, se si crea uno snapshot di un disco gestito con una capacità di provisioning di 64 GiB e una dimensione di dati effettivamente usata di 10 GiB, viene addebitato solo lo snapshot relativo alla dimensione di dati usata di 10 GiB.
