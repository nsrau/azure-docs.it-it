---
title: Test delle prestazioni del volume e le metriche usando i file di Azure NetApp di benchmark | Microsoft Docs
description: Fornisce indicazioni per le prestazioni del volume e le metriche usando i file di NetApp di Azure per il test di benchmark.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: b-juche
ms.openlocfilehash: 12ae9e313655924f11799152b5e58b77776c135c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478810"
---
# <a name="benchmark-testing-for-volume-performance-and-metrics-using-azure-netapp-files"></a>Test di benchmark per le prestazioni dei volumi e le metriche con Azure NetApp Files

Questo articolo fornisce indicazioni per le prestazioni del volume e le metriche usando i file di NetApp di Azure per il test di benchmark.

## <a name="overview"></a>Panoramica

Per comprendere le caratteristiche delle prestazioni di un volume di file di Azure NetApp, è possibile usare lo strumento open source [FIO](https://github.com/axboe/fio) per eseguire una serie di benchmark per simulare un'ampia gamma di carichi di lavoro. FIO può essere installato su entrambi Linux e sistemi operativi basati su Windows.  È uno strumento eccellente per ottenere una rapida panoramica di IOPS e velocità effettiva per un volume.

### <a name="vm-instance-sizing"></a>Dimensionamento dell'istanza VM

Per ottenere risultati ottimali, verificare che si utilizza un'istanza di macchina virtuale (VM) che viene ridimensionata in modo appropriato per eseguire i test. Gli esempi seguenti usano un'istanza Standard_D32s_v3. Per altre informazioni sulle dimensioni delle istanze di macchina virtuale, vedere [macchine virtuali di dimensioni per Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) per le macchine virtuali basate su Windows, e [dimensioni delle macchine virtuali Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per le macchine virtuali basate su Linux.

### <a name="azure-netapp-files-volume-sizing"></a>Dimensioni dei volumi Azure file NetApp

Assicurarsi di aver scelto le dimensioni della quota volume e a livello di servizio corretto per il livello di prestazioni previste. Visualizzare [livelli di servizio per i file di Azure NetApp](azure-netapp-files-service-levels.md) per altre informazioni.

### <a name="virtual-network-vnet-recommendations"></a>Consigli sulla rete virtuale (VNet)

È consigliabile eseguire il test nella stessa rete virtuale come file di Azure NetApp di benchmark. L'esempio seguente illustra la raccomandazione:

![Raccomandazioni di rete virtuale](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>Installazione di FIO

FIO è disponibile in formato binario per Linux e Windows. Seguire la sezione binari dei pacchetti in [FIO](https://github.com/axboe/fio) installare per la piattaforma di propria scelta.

## <a name="fio-examples-for-iops"></a>Esempi FIO per IOPS 

Gli esempi FIO in questa sezione usano la configurazione seguente:
* Dimensione istanza macchina virtuale: D32s_v3
* Il livello di pool di capacità e dimensioni: Premium / 50 TiB
* Dimensioni della quota volume: 48 TiB

Gli esempi seguenti mostrano il FIO casuali di lettura e scrittura.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: 8 KB letture casuali 100% delle dimensioni di blocco

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Output: 68k leggere IOPS visualizzato

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: 8k blocco scritture casuali di dimensione 100%

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Output: k 73 scrivere IOPS visualizzato

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>Esempi FIO per larghezza di banda

Gli esempi in questa sezione illustrano le FIO sequenza letture e scritture.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: blocco di 64 KB letture sequenziali 100% delle dimensioni

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Output: 11.8 Gbit/s di velocità effettiva visualizzata

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: blocco di 64 KB scritture sequenziali 100% delle dimensioni

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Output: 12.2 velocità effettiva Gbit/s visualizzata

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Metriche di volume

I dati sulle prestazioni di Azure i file di NetApp sono disponibili tramite i contatori di monitoraggio di Azure. I contatori sono disponibili tramite il portale di Azure e le richieste GET API REST. 

È possibile visualizzare i dati cronologici per le informazioni seguenti:
* Latenza media in lettura 
* Latenza media in scrittura 
* IOPS di lettura (Media)
* Scrivere IOPS (Media)
* Dimensioni logiche del volume (Media)
* Dimensioni dello snapshot del volume (Media)

### <a name="using-azure-monitor"></a>Uso di Monitoraggio di Azure 

È possibile accedere i contatori di Azure NetApp file in base al volume dalla pagina di metriche, come illustrato di seguito:

![Metriche di monitoraggio di Azure](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

È anche possibile creare un dashboard in Monitoraggio di Azure per file di Azure NetApp passare alla pagina di metriche, il filtro di NetApp e specificando i contatori di volume di interesse: 

![Dashboard del monitoraggio di Azure](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Accesso API di monitoraggio di Azure

È possibile accedere i contatori di file di Azure NetApp tramite chiamate all'API REST. Vedere [metriche supportate con monitoraggio di Azure: Microsoft.NetApp/netAppAccounts/capacityPools/Volumes](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes) dei contatori per i pool di capacità e i volumi.

L'esempio seguente illustra un URL GET per la visualizzazione delle dimensioni del volume logico:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Passaggi successivi

- [Livelli di servizio per Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Benchmark delle prestazioni per file di Azure NetApp](azure-netapp-files-performance-benchmarks.md)