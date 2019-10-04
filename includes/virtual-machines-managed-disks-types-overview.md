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
ms.openlocfilehash: 87e130d3a4569971bffb9b1ac2e189babb900225
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997647"
---
# <a name="what-disk-types-are-available-in-azure"></a>Quali tipi di dischi sono disponibili in Azure?

Azure Managed Disks offre attualmente quattro tipi di dischi, ciascuno dei quali è destinato a scenari specifici del cliente.

## <a name="disk-comparison"></a>Confronto dei dischi

La tabella seguente fornisce un confronto tra dischi Ultra, unità SSD Premium, unità SSD standard e unità disco rigido standard (HDD) per Managed disks che consentono di decidere cosa usare.

|   | Disco Ultra   | Unità SSD Premium   | SSD Standard   | HDD Standard   |
|---------|---------|---------|---------|---------|
|Tipo di disco   |SSD   |SSD   |SSD   |HDD   |
|Scenario   |Carichi di lavoro con I/O elevato come SAP HANA, database di alto livello (ad esempio SQL, Oracle) e altri carichi di lavoro con numerose transazioni.   |Carichi di lavoro di produzione su cui influiscono le prestazioni   |Server Web, applicazioni aziendali con un utilizzo non intensivo e sviluppo/test   |Backup, carichi di lavoro non critici, accesso poco frequente   |
|Dimensioni disco   |65.536 gibibyte (GiB)    |32.767 GiB    |32.767 GiB   |32.767 GiB   |
|Velocità effettiva massima   |2\.000 MiB/s    |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|Numero massimo di operazioni di I/O al secondo   |160.000    |20.000   |6000   |2\.000   |

## <a name="ultra-disk"></a>Disco Ultra

I dischi di Azure ultra offrono velocità effettiva elevata, IOPS elevate e archiviazione su disco a bassa latenza coerente per le macchine virtuali IaaS di Azure. Alcuni vantaggi aggiuntivi di ultra disks includono la possibilità di modificare dinamicamente le prestazioni del disco, insieme ai carichi di lavoro, senza dover riavviare le macchine virtuali (VM). I dischi Ultra sono adatti per carichi di lavoro con utilizzo intensivo di dati, ad esempio SAP HANA, database di livello superiore e carichi di lavoro pesanti per le transazioni. I dischi Ultra possono essere usati solo come dischi dati. È consigliabile usare unità SSD Premium come dischi del sistema operativo.

### <a name="performance"></a>Prestazioni

Quando si effettua il provisioning di un disco Ultra, è possibile configurare in modo indipendente la capacità e le prestazioni del disco. I dischi Ultra includono diverse dimensioni fisse, comprese tra 4 GiB e 64 TiB, e dispongono di un modello di configurazione delle prestazioni flessibile che consente di configurare in modo indipendente IOPS e velocità effettiva.

Di seguito sono riportate alcune funzionalità chiave di dischi Ultra:

- Capacità disco: La capacità di dischi Ultra è compresa tra 4 GiB e 64 TiB.
- Operazioni di I/O al secondo (IOPS) del disco: I dischi Ultra supportano i limiti di IOPS di 300 IOPS/GiB, fino a un massimo di 160 K di IOPS per disco. Per ottenere il valore di IOPS di cui è stato effettuato il provisioning, verificare che le operazioni di i/o al secondo del disco selezionate siano inferiori al limite di IOPS Il numero minimo di IOPS per disco è di 2 IOPS/GiB, con una linea di base complessiva minima di 100 IOPS. Se, ad esempio, si dispone di un disco con 4 GiB Ultra, si avrà almeno 100 IOPS, anziché otto IOPS.
- Velocità effettiva del disco: Con dischi Ultra, il limite di velocità effettiva di un singolo disco è 256 KiB/s per ogni IOPS con provisioning, fino a un massimo di 2000 MBps per disco (dove MBps = 10 ^ 6 byte al secondo). La velocità effettiva minima per disco è 4KiB/s per ogni IOPS di cui è stato effettuato il provisioning, con una linea di base complessiva minima di 1 MBps.
- I dischi Ultra supportano la regolazione degli attributi relativi alle prestazioni del disco (IOPS e velocità effettiva) in fase di esecuzione senza scollegare il disco dalla macchina virtuale. Dopo l'esecuzione di un'operazione di ridimensionamento delle prestazioni in un disco, può essere necessario attendere fino a un'ora prima che la modifica abbia effetto. È previsto un limite di quattro operazioni di ridimensionamento delle prestazioni in un intervallo di 24 ore. È possibile che un'operazione di ridimensionamento delle prestazioni abbia esito negativo a causa della mancanza di capacità della larghezza di banda delle prestazioni.

### <a name="disk-size"></a>Dimensioni disco

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

Per il momento, i dischi Ultra presentano limitazioni aggiuntive, come indicato di seguito:

- Sono supportati negli Stati Uniti orientali 2, Asia sudorientale ed Europa settentrionale, in due zone di disponibilità per area  
- Possono essere usati solo con le zone di disponibilità (i set di disponibilità e le distribuzioni di macchine virtuali singole all'esterno delle zone non consentono di collegare un disco Ultra)
- Sono supportati solo nelle macchine virtuali ES/DS v3
- Sono disponibili solo come dischi dati e supportano solo dimensioni di settori fisici di 4k  
- Possono essere creati solo come dischi vuoti  
- Non sono ancora supportati snapshot del disco, immagini di VM, set di disponibilità, set di scalabilità di macchine virtuali e crittografia dischi di Azure
- Non supporta ancora l'integrazione con backup di Azure o Azure Site Recovery
- Il limite massimo corrente per IOPS nelle VM GA è 80.000.