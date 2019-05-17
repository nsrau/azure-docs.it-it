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
ms.openlocfilehash: d2daafa6bf5f9a28ad2b61a97e7a8bd2246ae18d
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65538375"
---
# <a name="what-disk-types-are-available-in-azure"></a>Quali tipi di dischi sono disponibili in Azure?

I dischi gestiti di Azure offrono attualmente quattro tipi di dischi, tre dei quali sono disponibili a livello generale (GA) e uno è attualmente in anteprima. Ognuno di questi quattro tipi di dischi ha un proprio scenario del cliente appropriato.

## <a name="disk-comparison"></a>Confronto dei dischi

La tabella seguente mette a confronto unità Ultra SSD (anteprima), SSD Premium, SSD Standard e HDD Standard per dischi gestiti in modo da aiutare a scegliere quali usare.

|   | Ultra SSD (anteprima)   | Unità SSD Premium   | SSD Standard   | HDD Standard   |
|---------|---------|---------|---------|---------|
|Tipo di disco   |SSD   |SSD   |SSD   |HDD   |
|Scenario   |Carichi di lavoro con I/O elevato come SAP HANA, database di alto livello (ad esempio SQL, Oracle) e altri carichi di lavoro con numerose transazioni.   |Carichi di lavoro di produzione su cui influiscono le prestazioni   |Server Web, applicazioni aziendali con un utilizzo non intensivo e sviluppo/test   |Backup, carichi di lavoro non critici, accesso poco frequente   |
|Dimensioni disco   |65.536 gibibyte (GiB) (Anteprima)   |32.767 GiB    |32.767 GiB   |32.767 GiB   |
|Velocità effettiva massima   |2.000 MiB/s (Anteprima)   |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|Numero massimo di operazioni di I/O al secondo   |160.000 (Anteprima)   |20.000   |6000   |2.000   |

## <a name="ultra-ssd-preview"></a>Ultra SSD (anteprima)

Le unità Ultra SSD di Azure (anteprima) offrono una velocità effettiva elevata, un numero elevato di operazioni di I/O al secondo e archiviazione su disco con bassa latenza coerente per le macchine virtuali IaaS di Azure. Alcuni vantaggi aggiuntivi delle unità Ultra SSD includono la possibilità di modificare dinamicamente le prestazioni del disco, in base ai carichi di lavoro, senza dover riavviare le macchine virtuali. Le unità Ultra SSD sono idonee per carichi di lavoro a elevato utilizzo di dati, come SAP HANA, database di alto livello e carichi di lavoro con numerose transazioni. Le unità Ultra SSD possono essere usate solo come dischi dati. È consigliabile usare unità SSD Premium come dischi del sistema operativo.

### <a name="performance"></a>Prestazioni

Quando si effettua il provisioning di un disco Ultra, è possibile configurare in modo indipendente la capacità e le prestazioni del disco. Le unità Ultra SSD sono disponibili con diverse dimensioni fisse, da 4 GiB fino a 64 TiB, e offrono un modello di configurazione delle prestazioni flessibile che consente di configurare in modo indipendente il numero di operazioni di I/O al secondo e la velocità effettiva.

Ecco alcune delle caratteristiche principali delle unità SSD Ultra:

- Capacità disco: la capacità delle unità Ultra SSD è compresa tra 4 GiB e 64 TiB.
- Operazioni di I/O al secondo (IOPS) del disco: le unità Ultra SSD supportano limiti di operazioni di I/O al secondo pari a 300 IOPS/GiB, fino a un massimo di 160.000 IOPS per disco. Per ottenere la quantità di operazioni di I/O al secondo di cui è stato effettuato il provisioning, assicurarsi che il numero di operazioni di I/O al secondo del disco selezionato sia inferiore al numero di operazioni di I/O al secondo della macchina virtuale. Il numero minimo di operazioni di I/O al secondo del disco è 100.
- Velocità effettiva del disco: con le unità Ultra SSD, il limite di velocità effettiva di un singolo disco è di 256 KiB/s per ogni operazione di I/O al secondo di cui è stato effettuato il provisioning, fino a un massimo di 2000 MBps per disco (dove MBps = 10^6 byte al secondo). La velocità effettiva minima del disco è di 1 MiB.
- Unità SSD extra supportano modificando gli attributi delle prestazioni disco (IOPS e velocità effettiva) in fase di esecuzione senza scollegare il disco dalla macchina virtuale. Dopo l'esecuzione di un'operazione di ridimensionamento delle prestazioni in un disco, può essere necessario attendere fino a un'ora prima che la modifica abbia effetto.

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

### <a name="transactions"></a>Transazioni

Per le unità SSD extra, ogni operazione dei / o minore o uguale a 256 KiB della velocità effettiva viene considerato una singola operazione dei / o. Operazioni dei / o superiore a 256 KB di velocità effettiva sono considerate diversi i/o di dimensione 256 KB.

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
