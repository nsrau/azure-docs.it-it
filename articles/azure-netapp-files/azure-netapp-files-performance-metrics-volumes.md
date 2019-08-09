---
title: Suggerimenti per i test di benchmark delle prestazioni per Azure NetApp Files | Microsoft Docs
description: Fornisce consigli di test di benchmark per le metriche e le prestazioni del volume usando Azure NetApp Files.
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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 1969b3c237a4133df6f53bd6426ca4d50581cbcb
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881727"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Suggerimenti per i test di benchmark delle prestazioni per Azure NetApp Files

Questo articolo fornisce consigli per i test di benchmark per le prestazioni e le metriche del volume usando Azure NetApp Files.

## <a name="overview"></a>Panoramica

Per comprendere le caratteristiche di prestazioni di un volume di Azure NetApp Files, è possibile usare lo strumento [fio](https://github.com/axboe/fio) open source per eseguire una serie di benchmark per simulare un'ampia gamma di carichi di lavoro. FIO può essere installato sia nei sistemi operativi Linux che in quelli basati su Windows.  Si tratta di uno strumento eccellente per ottenere uno snapshot rapido di IOPS e velocità effettiva per un volume.

### <a name="vm-instance-sizing"></a>Dimensioni delle istanze di VM

Per ottenere risultati ottimali, assicurarsi di usare un'istanza di macchina virtuale (VM) opportunamente dimensionata per eseguire i test. Negli esempi seguenti viene utilizzata un'istanza di Standard_D32s_v3. Per altre informazioni sulle dimensioni delle istanze di VM, vedere [dimensioni per le macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) per VM basate su Windows e [dimensioni per le macchine virtuali Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per VM basate su Linux.

### <a name="azure-netapp-files-volume-sizing"></a>Azure NetApp Files il ridimensionamento del volume

Assicurarsi di scegliere il livello di servizio e le dimensioni della quota del volume corretti per il livello di prestazioni previsto. Per ulteriori informazioni, vedere [livelli di servizio per Azure NetApp files](azure-netapp-files-service-levels.md) .

### <a name="virtual-network-vnet-recommendations"></a>Raccomandazioni sulla rete virtuale (VNet)

È consigliabile eseguire il test di benchmark nella stessa VNet Azure NetApp Files. Nell'esempio seguente viene illustrata la Raccomandazione:

![Raccomandazioni per VNet](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>Installazione di FIO

FIO è disponibile in formato binario per Linux e Windows. Seguire la sezione pacchetti binari in [fio](https://github.com/axboe/fio) da installare per la piattaforma di propria scelta.

## <a name="fio-examples-for-iops"></a>Esempi FIO per IOPS 

Gli esempi di FIO in questa sezione usano la configurazione seguente:
* Dimensioni istanza macchina virtuale: D32s_v3
* Dimensioni e livello di servizio del pool di capacità: Premium/50 TiB
* Dimensioni quota volume: 48 TiB

Gli esempi seguenti illustrano le letture e le scritture casuali di FIO.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: Dimensioni blocco 8K 100% letture casuali

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Output: 68k di lettura IOPS visualizzati

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: Dimensioni blocco 8K 100% scritture casuali

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Output: 73K di scrittura IOPS visualizzati

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>Esempi di FIO per la larghezza di banda

Negli esempi di questa sezione vengono illustrate le operazioni di lettura e scrittura sequenziali di FIO.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: Dimensioni blocco 64K 100% letture sequenziali

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Output: velocità effettiva 11,8 Gbit/s visualizzata

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: Dimensioni blocco 64K 100% scritture sequenziali

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Output: velocità effettiva 12,2 Gbit/s visualizzata

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Metriche del volume

Azure NetApp Files dati sulle prestazioni sono disponibili tramite i contatori di monitoraggio di Azure. I contatori sono disponibili tramite il portale di Azure e le richieste GET dell'API REST. 

È possibile visualizzare i dati cronologici per le seguenti informazioni:
* Latenza media in lettura 
* Latenza media in scrittura 
* IOPS lettura (media)
* IOPS di scrittura (media)
* Dimensioni logiche volume (media)
* Dimensioni snapshot del volume (media)

### <a name="using-azure-monitor"></a>Uso di Monitoraggio di Azure 

È possibile accedere a Azure NetApp Files contatori in base ai singoli volumi dalla pagina metrica, come illustrato di seguito:

![Metriche di monitoraggio di Azure](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

È anche possibile creare un dashboard in monitoraggio di Azure per Azure NetApp Files passando alla pagina metrica, filtrando per NetApp e specificando i contatori di volume di interesse: 

![Dashboard del monitoraggio di Azure](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Accesso all'API di monitoraggio di Azure

È possibile accedere ai contatori Azure NetApp Files usando le chiamate all'API REST. Vedere [metriche supportate con monitoraggio di Azure: Microsoft. NetApp/netAppAccounts/capacityPools/Volumes](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes) per i contatori per i pool di capacità e i volumi.

Nell'esempio seguente viene illustrato un URL GET per la visualizzazione delle dimensioni del volume logico:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Passaggi successivi

- [Livelli di servizio per Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Benchmark delle prestazioni per Azure NetApp Files](azure-netapp-files-performance-benchmarks.md)