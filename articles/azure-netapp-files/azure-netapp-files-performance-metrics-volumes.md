---
title: Test di benchmark delle prestazioni consigliati - File NetApp di AzureRecommended performance benchmark tests - Azure NetApp Files
description: Informazioni sui consigli sui test di benchmark per le prestazioni e le metriche dei volumi usando i file NetApp di Azure.Learn about benchmark testing recommendations for volume performance and metrics using Azure NetApp Files.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 08/07/2019
ms.openlocfilehash: 8f354152c23dd7ad0413f27585d724f8070ca003
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551523"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Raccomandazioni sui test di benchmark delle prestazioni per Azure NetApp Files

Questo articolo fornisce consigli sui test di benchmark per le prestazioni e le metriche dei volumi usando i file netapp di Azure.This article provides benchmark testing recommendations for volume performance and metrics using Azure NetApp Files.

## <a name="overview"></a>Panoramica

Per comprendere le caratteristiche delle prestazioni di un volume file NetApp di Azure, è possibile usare lo strumento open source [FIO](https://github.com/axboe/fio) per eseguire una serie di benchmark per simulare un'ampia gamma di carichi di lavoro. FIO può essere installato su entrambi i sistemi operativi basati su Linux e Windows.  Si tratta di uno strumento eccellente per ottenere un rapido snapshot di entrambi i OPS e la velocità effettiva per un volume.

### <a name="vm-instance-sizing"></a>Dimensionamento dell'istanza della macchina virtualeVM instance sizing

Per ottenere risultati ottimali, assicurarsi di usare un'istanza di macchina virtuale (VM) con dimensioni appropriate per eseguire i test. Negli esempi seguenti viene usata un'istanza di Standard_D32s_v3. Per altre informazioni sulle dimensioni delle istanze di macchine virtuali, vedere [Dimensioni per macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) per macchine virtuali basate su Windows e Dimensioni per macchine virtuali Linux in Azure per macchine virtuali basate su Linux.For more information about VM instance sizes, see Sizes for Windows virtual machines in Azure for Windows-based VMs, and Sizes for Linux virtual machines in [Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) for Linux-based VMs.

### <a name="azure-netapp-files-volume-sizing"></a>Dimensionamento del volume dei file NetApp di AzureAzure NetApp Files volume sizing

Assicurarsi di scegliere il livello di servizio e la dimensione della quota del volume corretti per il livello di prestazioni previsto. Per altre informazioni, vedere Livelli di servizio per i file NetApp di [Azure.See Service levels for Azure NetApp Files](azure-netapp-files-service-levels.md) for more information.

### <a name="virtual-network-vnet-recommendations"></a>Consigli per la rete virtuale (VNet)

È necessario eseguire il test di benchmark nella stessa rete virtuale dei file NetApp di Azure.You should perform the benchmark testing in the same VNet as Azure NetApp Files. L'esempio seguente illustra la raccomandazione:The example below demonstrates the recommendation:

![Raccomandazioni sulla rete virtuale](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>Installazione di FIO

FIO è disponibile in formato binario sia per Linux che per Windows. Seguire la sezione Pacchetti binari in [FIO](https://github.com/axboe/fio) per installare per la piattaforma di vostra scelta.

## <a name="fio-examples-for-iops"></a>FIO examples for IOPS 

Negli esempi FIO di questa sezione viene utilizzata la configurazione seguente:
* Dimensione dell'istanza della macchina virtuale: D32s_v3
* Livello e dimensioni del pool di capacità: Premium / 50 TiB
* Dimensione della quota volume: 48 TiB

Gli esempi seguenti mostrano le letture e le scritture casuali FIO.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: dimensione blocco 8k 100% letture casuali

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Uscita: 68k lettura IOPS visualizzati

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: dimensione blocco 8k 100% scritture casuali

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Output: 73k scrittura IOPS visualizzati

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>Esempi FIO per la larghezza di banda

Gli esempi in questa sezione mostrano le letture e le scritture sequenziali FIO.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: dimensione blocco 64k 100% letture sequenziali

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Uscita: 11,8 Gbit/s velocità effettiva visualizzata

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: dimensione blocco 64k 100% scritture sequenziali

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Uscita: 12,2 Gbit/s velocità effettiva visualizzata

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Metriche del volume

I dati sulle prestazioni dei file NetApp di Azure sono disponibili tramite i contatori di Monitoraggio di Azure.Azure NetApp Files performance data is available through Azure Monitor counters. I contatori sono disponibili tramite il portale di Azure e le richieste GET dell'API REST. 

È possibile visualizzare i dati cronologici per le seguenti informazioni:
* Latenza media in lettura 
* Latenza media in scrittura 
* Lettura IOPS (media)
* Scrittura IOPS (media)Write IOPS (average)
* Dimensioni logiche del volume (media)
* Dimensioni snapshot volume (media)

### <a name="using-azure-monitor"></a>Uso di Monitoraggio di Azure 

È possibile accedere ai contatori dei file NetApp di Azure per volume dalla pagina Metriche, come illustrato di seguito:You can access Azure NetApp Files counters on a per-volume basis from the Metrics page, as shown below:

![Metriche di Monitoraggio di AzureAzure Monitor metrics](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

È anche possibile creare un dashboard in Monitoraggio di Azure per i file NetApp di Azure accedendo alla pagina Metriche, filtrando per NetApp e specificando i contatori del volume di interesse:You can also create a dashboard in Azure Monitor for Azure NetApp Files by going to the Metrics page, filtering for NetApp, and specifying the volume counters of interest: 

![Dashboard del monitoraggio di Azure](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Accesso alle API di Azure MonitorAzure Monitor API access

È possibile accedere ai contatori File NetApp di Azure usando le chiamate all'API REST. Vedere [Metriche supportate con Monitoraggio di Azure: Microsoft.NetApp/netAppAccounts/capacityPools/Volumes](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes) per i contatori per pool di capacità e volumi.

L'esempio seguente mostra un URL GET per la visualizzazione delle dimensioni del volume logico:The following example shows a GET URL for viewing logical volume size:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Passaggi successivi

- [Livelli di servizio per Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Benchmark delle prestazioni per Azure NetApp Files](azure-netapp-files-performance-benchmarks.md)