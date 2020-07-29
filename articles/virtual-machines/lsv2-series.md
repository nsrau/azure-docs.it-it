---
title: Serie Lsv2-macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie Lsv2.
author: sasha-melamed
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: cedb5899b392cb111f9c1bb76949e940ef837252
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284813"
---
# <a name="lsv2-series"></a>Serie Lsv2

La serie Lsv2 offre velocità effettiva elevata, bassa latenza, risorse di archiviazione NVMe locali con mapping diretto in esecuzione sul [processore AMD EPYC<sup>TM</sup> 7551](https://www.amd.com/en/products/epyc-7000-series) con un turbo boost "all core" di 2,55 GHz e un turbo boost massimo di 3 GHz. Le macchine virtuali Serie Lsv2 sono disponibili in dimensioni comprese tra 8 e 80 vCPU in una configurazione multi-thread simultanea.  Sono disponibili 8 GiB di memoria per ogni vCPU e un dispositivo NVMe SSD M.2 da 1,92 TB per 8 vCPU, fino a 19,2 TB (10 x 1,92 TB) disponibili nella versione L80s v2.

> [!NOTE]
> Le macchine virtuali della serie Lsv2 sono ottimizzate per l'uso del disco locale nel nodo collegato direttamente alla macchina virtuale anziché usare dischi dati durevoli. Questo consente un maggior numero di operazioni di I/O al secondo o un maggiore velocità effettiva per i carichi di lavoro. La serie Lsv2 e ls non supporta la creazione di una cache locale per aumentare il valore di IOPs ottenibile dai dischi dati durevoli.
>
> La velocità effettiva elevata e gli IOPs del disco locale rendono le VM serie Lsv2 ideali per gli archivi NoSQL, ad esempio Apache Cassandra e MongoDB, che replicano i dati tra più macchine virtuali per ottenere la persistenza in caso di errore di una singola macchina virtuale.
>
> Per altre informazioni, vedere ottimizzare le prestazioni nelle macchine virtuali della serie Lsv2 per [Windows](../virtual-machines/windows/storage-performance.md) o [Linux](../virtual-machines/linux/storage-performance.md).  

ACU: 150-175

In sequenza: supportato

Archiviazione Premium: supportata

Caching archiviazione Premium: non supportato

Live Migration: Non supportato

Manutenzione con mantenimento della memoria: Non supportato

| Dimensione | vCPU | Memoria (GiB) | Disco temporaneo<sup>1</sup> (GiB) | Dischi NVMe<sup>2</sup> | Velocità effettiva del disco NVMe<sup>3</sup> (letture IOPS/Mbps) | Velocità effettiva del disco dati non memorizzata nella cache (IOPs/MBps)<sup>4</sup> | Velocità effettiva massima del disco dati non memorizzato nella cache (IOPs/MBps)<sup>5</sup>| Numero massimo di dischi dati | Schede di interfaccia di rete max | Larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|---|---|---|
| Standard_L8s_v2   |  8 |  64 |  80 |  1 x 1,92 TB  | 400000/2000  | 8000/160   | 8000/1280 | 16 | 2 | 3200   |
| Standard_L16s_v2  | 16 | 128 | 160 |  2 x 1,92 TB  | 800000/4000  | 16000/320  | 16000/1280 | 32 | 4 | 6400   |
| Standard_L32s_v2  | 32 | 256 | 320 |  4 x 1,92 TB  | 1,5 m/8000    | 32000/640  | 32000/1280 | 32 | 8 | 12800  |
| Standard_L48s_v2  | 48 | 384 | 480 |  6x 1.92 TB  | 2.2 m/14000   | 48000/960  | 48000/2000 | 32 | 8 | 16000 + |
| Standard_L64s_v2  | 64 | 512 | 640 |  8 x 1,92 TB  | 2.9 m/16000   | 64000/1280 | 64000/2000 | 32 | 8 | 16000 + |
| Standard_L80s_v2<sup>6</sup> | 80 | 640 | 800 | 10 x 1,92 TB | 3.8 m/20000 | 80000/1400 | 80000/2000 | 32 | 8 | 16000 + |

<sup>1</sup> Le macchine virtuali serie Lsv2 hanno un disco risorse temporaneo basato su SCSI standard per l'uso di un file di scambio/paging del sistema operativo (D: in Windows, /dev/sdb in Linux). Il disco offre 80 GiB di archiviazione, 4.000 IOPS e una velocità di trasferimento di 80 MBps ogni 8 vCPU (ad esempio, il modello Standard_L80s_v2 fornisce 800 GiB a 40.000 IOPS e 800 MBPS). In questo modo, le unità NVMe possono essere completamente dedicate all'utilizzo dell'applicazione. Questo disco è temporaneo e tutti i dati andranno persi al momento dell'arresto/deallocazione.

<sup>2</sup> I dischi NVMe locali sono temporanei: i dati dei dischi andranno persi se si arresta/dealloca la macchina virtuale.

<sup>3</sup> La tecnologia Hyper-V NVMe Direct offre un accesso senza limitazione alle unità NVMe locali mappate in modo sicuro nello spazio della macchina virtuale guest.  Per ottenere le massime prestazioni è necessario usare la build WS2019 più recente o Ubuntu 18.04 o 16.04 disponibile in Azure Marketplace.  Le prestazioni di scrittura variano in base alle dimensioni di I/O, al caricamento delle unità e all'utilizzo della capacità.

<sup>4</sup> Le macchine virtuali serie Lsv2 non prevedono la cache dell'host per il disco dati perché non genera alcun vantaggio per i carichi di lavoro Lsv2.  Possono tuttavia prevedere l'opzione disco del sistema operativo temporaneo della macchina virtuale di Azure (fino a 30 GiB).

<sup>5</sup> [le macchine](linux/disk-bursting.md) virtuali serie Lsv2 possono incrementare le prestazioni del disco per un massimo di 30 minuti alla volta. 

<sup>6</sup> le macchine virtuali con più di 64 vCPU richiedono uno di questi sistemi operativi guest supportati:

- Windows Server 2016 o versione successiva
- Ubuntu 16,04 LTS o versioni successive, con kernel ottimizzato per Azure (kernel 4,15 o versione successiva)
- SLES 12 SP2 o versione successiva
- RHEL o CentOS dalla versione 6,7 alla 6,10, con il pacchetto LIS fornito da Microsoft (o versioni successive) installato
- RHEL o CentOS versione 7,3, con il pacchetto LIS fornito da Microsoft (o versioni successive) installato
- RHEL o CentOS versione 7,6 o successiva
- Oracle Linux con UEK4 o versione successiva
- Debian 9 con il kernel di backports, Debian 10 o versione successiva
- CoreOS con un kernel 4,14 o versione successiva

## <a name="size-table-definitions"></a>Definizioni delle tabelle delle dimensioni

- La capacità di archiviazione viene visualizzata in unità di GiB o 1.024^3 byte. Quando si confrontano dischi misurati in GB (1.000^3 byte) con dischi misurati in GiB (1.024^3), tenere presente che i valori di capacità specificati in GiB potrebbero apparire inferiori. Ad esempio, 1.023 GiB = 1.098,4 GB
- La velocità effettiva del disco viene misurata in operazioni di input/output al secondo (IOPS) e MBps, dove il valore di MBps corrisponde a 10^6 byte al secondo.
- Se si vogliono ottenere prestazioni ottimali per le macchine virtuali, è necessario limitare il numero di dischi dati a 2 per ogni vCPU.
- La **larghezza di banda della rete prevista** è la [larghezza di banda aggregata massima allocata per ogni tipo di macchina virtuale](../virtual-network/virtual-machine-network-throughput.md) in tutte le schede di interfaccia di rete, per tutte le destinazioni. I limiti massimi non sono garantiti, ma hanno lo scopo di fornire indicazioni per la selezione del tipo di macchina virtuale corretto per l'applicazione desiderata. Le prestazioni di rete effettive dipenderanno da svariati fattori, tra cui congestione della rete, carichi dell'applicazione e impostazioni di rete. Per informazioni sull'ottimizzazione della velocità effettiva della rete, vedere [Ottimizzazione della velocità effettiva di rete per Windows e Linux](../virtual-network/virtual-network-optimize-network-bandwidth.md). Per realizzare le prestazioni di rete previste in Linux o Windows, potrebbe essere necessario selezionare una versione specifica o ottimizzare la macchina virtuale. Per ulteriori informazioni, vedere [Come eseguire test affidabili della velocità effettiva della macchina virtuale](../virtual-network/virtual-network-bandwidth-testing.md).


## <a name="other-sizes-and-information"></a>Altre dimensioni e informazioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

Calcolatore prezzi: [Calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/)

Altre informazioni sui tipi di dischi: [tipi di disco](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types#ultra-ssd-preview/)


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
