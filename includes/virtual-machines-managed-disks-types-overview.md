---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/15/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 72d87142f9b9c1f7bcb2b02281851bd1e29bc9c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262530"
---
I dischi gestiti di Azure attualmente offrono quattro tipi di dischi, ognuno dei quali è destinato a scenari specifici dei clienti.

## <a name="disk-comparison"></a>Confronto dei dischi

La tabella seguente fornisce un confronto tra dischi ultra, unità SSD (Solid-State Drive) premium, SSD standard e unità disco rigido standard (HDD) per dischi gestiti che consentono di decidere cosa utilizzare.

|   | Disco Ultra   | SSD Premium   | SSD Standard   | HDD Standard   |
|---------|---------|---------|---------|---------|
|Tipo di disco   |SSD   |SSD   |SSD   |HDD   |
|Scenario   |Carichi di lavoro che richiedono un utilizzo intensivo di I/O, ad esempio [SAP HANA](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md), database di livello superiore (ad esempio, SQL, Oracle) e altri carichi di lavoro con un elevato carico di transazioni.   |Carichi di lavoro di produzione su cui influiscono le prestazioni   |Server Web, applicazioni aziendali con un utilizzo non intensivo e sviluppo/test   |Backup, carichi di lavoro non critici, accesso poco frequente   |
|Dimensioni massime disco   |65.536 gibibyte (GiB)    |32.767 GiB    |32.767 GiB   |32.767 GiB   |
|Velocità effettiva massima   |2.000 MiB/s    |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|Operazioni di I/O al secondo max   |160.000    |20.000   |6000   |2.000   |

## <a name="ultra-disk"></a>Disco Ultra

I dischi Ultra di Azure offrono una velocità effettiva elevata, un numero elevato di operazioni di I/O al secondo e archiviazione su disco con bassa latenza coerente per le macchine virtuali IaaS di Azure. Alcuni vantaggi aggiuntivi dei dischi ultra includono la possibilità di modificare dinamicamente le prestazioni del disco, insieme ai carichi di lavoro, senza la necessità di riavviare le macchine virtuali (VM). I dischi Ultra sono idonei per carichi di lavoro a elevato utilizzo di dati, come SAP HANA, database di alto livello e carichi di lavoro con numerose transazioni. I dischi Ultra possono essere usati solo come dischi dati. È consigliabile usare unità SSD Premium come dischi del sistema operativo.

### <a name="performance"></a>Prestazioni

Quando si effettua il provisioning di un disco Ultra, è possibile configurare in modo indipendente la capacità e le prestazioni del disco. I dischi ultra sono disponibili in diverse dimensioni fisse, che vanno da 4 GiB fino a 64 TiB, e dispongono di un modello di configurazione delle prestazioni flessibile che consente di configurare in modo indipendente operazioni di I/O al secondo e velocità effettiva.

Alcune funzionalità chiave dei dischi ultra sono:

- Capacità del disco: la capacità di ultradischi varia da 4 GiB fino a 64 TiB.
- Operazioni di I/O al secondo del disco: i dischi ultra supportano limiti di IOPS/GiB di 300 IOPS/GiB, fino a un massimo di 160 IOPS K per disco. Per ottenere le operazioni di I/O al secondo di cui è stato eseguito il provisioning, verificare che le operazioni di I/O al secondo su disco selezionate siano inferiori al limite delle operazioni di I/O al secondo della macchina virtuale. Il numero minimo garantito di operazioni di I/O al secondo per disco è 2 IOPS/GiB, con un minimo di base complessivo di 100 operazioni di I/O al secondo. Ad esempio, se si dispone di un ultradisco 4 GiB, si disporrà di un minimo di 100 operazioni di I/O al secondo, anziché otto operazioni di I/O al secondo.
- Velocità effettiva del disco: con ultradischi, il limite di velocità effettiva di un singolo disco è di 256 KiB/s per ogni operazioni di I/O al secondo di cui è stato eseguito il provisioning, fino a un massimo di 2000 MBps per disco (dove MBps è pari a 10 x 6 byte al secondo). La velocità effettiva minima garantita per disco è 4 KiB/s per ogni IOPS di cui è stato eseguito il provisioning, con un minimo di riferimento complessivo di 1 MBps.
- I dischi ultra supportano la regolazione degli attributi delle prestazioni del disco (IOPS e velocità effettiva) in fase di esecuzione senza scollegare il disco dalla macchina virtuale. Dopo l'esecuzione di un'operazione di ridimensionamento delle prestazioni in un disco, può essere necessario attendere fino a un'ora prima che la modifica abbia effetto. Esiste un limite di quattro operazioni di ridimensionamento delle prestazioni durante una finestra di 24 ore. È possibile che un'operazione di ridimensionamento delle prestazioni non riesca a causa della mancanza di capacità della larghezza di banda delle prestazioni.

### <a name="disk-size"></a>Dimensioni disco

|Dimensioni disco (GiB)  |Cap Operazioni IAl  |Limite velocità effettiva (MBps)  |
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

### <a name="ga-scope-and-limitations"></a>Ambito GA e limitazioni

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]
