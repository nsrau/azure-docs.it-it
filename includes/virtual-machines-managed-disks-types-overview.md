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
ms.openlocfilehash: 45168c23964c735a4bd51ba11c2340d4ff95fed4
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012407"
---
Azure Managed Disks offre attualmente quattro tipi di dischi, ciascuno dei quali è destinato a scenari specifici del cliente.

## <a name="disk-comparison"></a>Confronto dei dischi

La tabella seguente fornisce un confronto tra dischi Ultra, unità SSD Premium, unità SSD standard e unità disco rigido standard (HDD) per Managed disks che consentono di decidere cosa usare.

|   | Disco Ultra   | SSD Premium   | SSD Standard   | HDD Standard   |
|---------|---------|---------|---------|---------|
|Tipo di disco   |SSD   |SSD   |SSD   |HDD   |
|Scenario   |Carichi di lavoro intensivo di i/o, come [SAP Hana](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md), database di livello superiore (ad esempio, SQL, Oracle) e altri carichi di lavoro con transazioni pesanti.   |Carichi di lavoro di produzione su cui influiscono le prestazioni   |Server Web, applicazioni aziendali con un utilizzo non intensivo e sviluppo/test   |Backup, carichi di lavoro non critici, accesso poco frequente   |
|Dimensioni massime del disco   |65.536 gibibyte (GiB)    |32.767 GiB    |32.767 GiB   |32.767 GiB   |
|Velocità effettiva massima   |2\.000 MiB/s    |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|Operazioni di I/O al secondo max   |160.000    |20.000   |6000   |2\.000   |

## <a name="ultra-disk"></a>Disco Ultra

I dischi di Azure ultra offrono velocità effettiva elevata, IOPS elevate e archiviazione su disco a bassa latenza coerente per le macchine virtuali IaaS di Azure. Alcuni vantaggi aggiuntivi di ultra disks includono la possibilità di modificare dinamicamente le prestazioni del disco, insieme ai carichi di lavoro, senza dover riavviare le macchine virtuali (VM). I dischi Ultra sono adatti per carichi di lavoro con utilizzo intensivo di dati, ad esempio SAP HANA, database di livello superiore e carichi di lavoro pesanti per le transazioni. I dischi Ultra possono essere usati solo come dischi dati. È consigliabile usare unità SSD Premium come dischi del sistema operativo.

### <a name="performance"></a>Prestazioni

Quando si effettua il provisioning di un disco Ultra, è possibile configurare in modo indipendente la capacità e le prestazioni del disco. I dischi Ultra includono diverse dimensioni fisse, comprese tra 4 GiB e 64 TiB, e dispongono di un modello di configurazione delle prestazioni flessibile che consente di configurare in modo indipendente IOPS e velocità effettiva.

Di seguito sono riportate alcune funzionalità chiave di dischi Ultra:

- Capacità disco: la capacità dei dischi Ultra è compresa tra 4 GiB e 64 TiB.
- IOPS del disco: i dischi Ultra supportano i limiti di IOPS di 300 IOPS/GiB, fino a un massimo di 160 K di IOPS per disco. Per ottenere il valore di IOPS di cui è stato effettuato il provisioning, verificare che le operazioni di i/o al secondo del disco selezionate siano inferiori al limite di IOPS Il numero minimo di IOPS per disco è di 2 IOPS/GiB, con una linea di base complessiva minima di 100 IOPS. Se, ad esempio, si dispone di un disco con 4 GiB Ultra, si avrà almeno 100 IOPS, anziché otto IOPS.
- Velocità effettiva del disco: con dischi Ultra, il limite di velocità effettiva di un singolo disco è 256 KiB/s per ogni IOPS di cui è stato effettuato il provisioning, fino a un massimo di 2000 MBps per disco (dove MBps = 10 ^ 6 byte al secondo). La velocità effettiva minima per disco è 4KiB/s per ogni IOPS di cui è stato effettuato il provisioning, con una linea di base complessiva minima di 1 MBps.
- I dischi Ultra supportano la regolazione degli attributi relativi alle prestazioni del disco (IOPS e velocità effettiva) in fase di esecuzione senza scollegare il disco dalla macchina virtuale. Dopo l'esecuzione di un'operazione di ridimensionamento delle prestazioni in un disco, può essere necessario attendere fino a un'ora prima che la modifica abbia effetto. È previsto un limite di quattro operazioni di ridimensionamento delle prestazioni in un intervallo di 24 ore. È possibile che un'operazione di ridimensionamento delle prestazioni abbia esito negativo a causa della mancanza di capacità della larghezza di banda delle prestazioni.

### <a name="disk-size"></a>Dimensione disco

|Dimensioni disco (GiB)  |Limite IOPS  |Limite velocità effettiva (MBps)  |
|---------|---------|---------|
|4     |1200         |300         |
|8     |2400         |600         |
|16     |4800         |1200         |
|32     |9\.600         |2\.000         |
|64     |19.200         |2\.000         |
|128     |38.400         |2\.000         |
|256     |76.800         |2\.000         |
|512     |80.000         |2\.000         |
|1\.024-65.536 (dimensioni in questo intervallo con aumento con incrementi di 1 TiB)     |160.000         |2\.000         |

### <a name="ga-scope-and-limitations"></a>Ambito e limitazioni di GA

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]