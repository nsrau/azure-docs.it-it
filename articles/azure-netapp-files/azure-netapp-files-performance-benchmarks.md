---
title: Risultati dei test di benchmark delle prestazioni per Azure NetApp Files | Microsoft Docs
description: Descrive i risultati dei test di benchmark delle prestazioni per Azure NetApp Files a livello di volume.
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
ms.openlocfilehash: 1d6b43110046f26d8c8070b19587366588eee7b6
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881745"
---
# <a name="performance-benchmark-test-results-for-azure-netapp-files"></a>Risultati dei test di benchmark delle prestazioni per Azure NetApp Files

Questo articolo descrive i risultati dei test di benchmark delle prestazioni per Azure NetApp Files a livello di volume. 

## <a name="sample-application-used-for-the-tests"></a>Applicazione di esempio usata per i test

I test delle prestazioni sono stati eseguiti con un'applicazione di esempio che usa Azure NetApp Files. L'applicazione presenta le caratteristiche seguenti: 

* Un'applicazione basata su Linux compilata per il cloud
* Può essere ridimensionato in modo lineare con le macchine virtuali aggiunte (VM) per aumentare la potenza di calcolo in base alle esigenze
* Richiede l'accessibilità rapida di data Lake
* Include modelli di I/O a volte casuali e talvolta sequenziali 
    * Un modello casuale richiede una bassa latenza per grandi quantità di I/O. 
    * Un modello sequenziale richiede grandi quantità di larghezza di banda. 

## <a name="about-the-workload-generator"></a>Informazioni sul generatore di carico di lavoro

I risultati provengono dai file di riepilogo di Vdbench. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) è un'utilità da riga di comando che genera carichi di lavoro di I/O su disco per convalidare le prestazioni di archiviazione. La configurazione client-server utilizzata è scalabile.  Include un singolo master/client misto e 14 VM client dedicate.

## <a name="about-the-tests"></a>Informazioni sui test

I test sono stati progettati per identificare i limiti che l'applicazione di esempio potrebbe avere e il tempo di risposta che curva fino ai limiti.  

Sono stati eseguiti i test seguenti: 

* 100% 8-KiB lettura casuale
* 100% 8-KiB scrittura casuale
* 100% 64-KiB lettura sequenziale
* 100% 64-KiB scrittura sequenziale
* 50% 64-KiB lettura sequenziale, 50% 64-KiB scrittura sequenziale
* 50% 8-KiB lettura casuale, 50% 8-KiB scrittura casuale

## <a name="bandwidth"></a>Larghezza di banda

Azure NetApp Files offre più [livelli di servizio](azure-netapp-files-service-levels.md). Ogni livello di servizio offre una quantità di larghezza di banda diversa per TiB della capacità con provisioning (quota del volume). Viene eseguito il provisioning del limite della larghezza di banda per un volume in base alla combinazione del livello di servizio e della quota del volume. Il limite di larghezza di banda è solo un fattore per determinare la quantità effettiva di velocità effettiva che verrà realizzata.  

Attualmente, 4.500 MiB è la velocità effettiva massima che è stata realizzata da un carico di lavoro su un singolo volume nel test.  Con il livello di servizio Premium, una quota del volume di 70,31 TiB effettuerà il provisioning di una larghezza di banda sufficiente per tenere conto della velocità effettiva in base al calcolo 

![Formula della larghezza di banda](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Quota e livello di servizio](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Carichi di lavoro a elevato utilizzo di velocità effettiva

Il test della velocità effettiva ha usato Vdbench e una combinazione di VM di archiviazione 12xD32s V3. Il volume di esempio nel test ha effettuato i seguenti numeri di velocità effettiva:

![Test della velocità effettiva](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>Carichi di lavoro con utilizzo intensivo di I/O

Il test di I/O ha usato Vdbench e una combinazione di VM di archiviazione 12xD32s V3. Il volume di esempio nel test ha effettuato i seguenti numeri di I/O:

![Test di I/O](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Latenza

La distanza tra le VM di test e il volume Azure NetApp Files influisca sulle prestazioni di I/O.  Il grafico seguente confronta le curve di risposta IOPS rispetto alla latenza per due diversi set di macchine virtuali.  Un set di macchine virtuali è vicino Azure NetApp Files e l'altro set è più lontano.  L'aumento della latenza per l'ulteriore set di macchine virtuali influisca sulla quantità di IOPS raggiunto a un determinato livello di parallelismo.  Indipendentemente dalle letture eseguite su un volume è possibile superare 300.000 IOPS, come illustrato di seguito: 

![Studio della latenza](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Riepilogo

I carichi di lavoro sensibili alla latenza (database) possono avere un tempo di risposta di un millisecondo. Le prestazioni transazionali possono essere superiori a 300K IOPS per un singolo volume.

Le applicazioni sensibili alla velocità effettiva (per flussi e immagini) possono avere una velocità effettiva di 4.5 GiB/s.

## <a name="example-scripts"></a>Script di esempio

Gli script di esempio seguenti sono solo a scopo dimostrativo.  Non devono essere usati per scopi di produzione.  

    #
    #This script makes the following assumptions about the environment
    #VM Naming Convention:
    #   VM naming convention: vdbench-vnet1-1 .. vdbench-vnet1-x
    #
    #VM Count:
    #   The script is written for 24 VM's, 
    #   If you wish to test with a different number of vm's replace {1..24} with {1..Some Number}
    #
    #Volume mount point:
    #    The volumes in this script are mounted at the following mount points on all virtual machines
    #    /mnt/vdb1 ... /mnt/vdb6
    #   
    #Virtual machines must have
    #   make sure that vdbench is present on all vms in the environment and that java has been installed on each vm as well as nfs-utils
    #
    #The following tunables were configured on all virtual machines in the environment
    #    No special tunables were used to extract the results identified in this paper.
    #    Even rsize and wsize were left at the default (64K)
    #
    #Special Notes (thread counts) 
    #   You can hone in on the amount of I/O you hope to achieve in your testing using littles law,
    #   Run each test with a thread count of 1 to determnine the best possible latency.
    #   Thread count equals latency in seconds * desired I/O rate.  
    #   If you find a minimum latency of 1.14ms for example, divide that by 1000 to convert 1.3ms to .0013 seconds.
    #   
    #   If your goal from a single machine is for example 3,500 IOPS, a thread count of 4.0 is required.
    
    #   thread count 4.0 == .0013s * 5,000ops
    #
    #    
    #Special Notes: Increased window size:
    #   As round-trip time increases; corresponding increases are required in TCP Window Size – think Littles law as shown above. 
    #   Check the values of the following and adjust as needed: net.core.rmem_max, net.core.rmem_default, net.ipv4.tcp_rmem
    #
    #Run the utility like this:
    #    vdbench -f vnet1-SeqMix-workload
    #
    #When the utility is finished its run, grep 'avg' from the summary file to see overall run performance.
     
    
    
    #LUN CONFIG FILES
    for vnet in 1; do
        echo "sd=default,size=693g" > vnet${vnet}-luns-nfs
        for vm in {1..24}; do
            for vol in {1..6}; do
                echo "sd=sd-${vm}-${vol},host=vm${vm},lun=/mnt/vdb${vol}/file-${vm}-${vol},openflags=o_direct" >> vnet${vnet}-luns-nfs
            done
        done
    done
    
    
    #HOST CONFIG FILES
    for vnet in 1; do
        echo "hd=default,jvms=1,shell=ssh" > vnet${vnet}-hosts-nfs
        for vm in {1..24}; do
            echo "hd=vm${vm},system=vdbench-vnet${vnet}-${vm},user=root" >> vnet${vnet}-hosts-nfs
        done
    done
    
    #VDBENCH WORK SCRIPTS
    for vnet in 1; do
        for pattern in FillWrite SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "include=vnet${vnet}-hosts-nfs" > vnet${vnet}-${pattern}-workload
            echo "include=vnet${vnet}-luns-nfs" >> vnet${vnet}-${pattern}-workload
        done 
        #Fill Write File
        echo "wd=FillWrite,sd=sd*,rdpct=0,seekpct=eof,xfersize=64k" >> vnet${vnet}-FillWrite-workload
        echo "* Run Defaults" >> vnet${vnet}-FillWrite-workload
        echo 'rd=Initialize-LUNs-Full,wd=FillWrite,sd=("sd*"),iorate=max,interval=1,forthreads=1,elapsed=259200' >> vnet${vnet}-FillWrite-workload
    
        #The actual workload files - feel free to add more or use less.
        #   The thread counts shown below were used to generate the performance collateral, 
        #   feel free to change to suite your needs.
    
        echo "wd=SeqWrite,sd=sd*,rdpct=0,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqWrite-workload
        echo "wd=SeqRead,sd=sd*,rdpct=100,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqRead-workload
        echo "wd=RndRead,sd=sd*,rdpct=100,seekpct=100,xfersize=8k" >> vnet${vnet}-RndRead-workload
        echo "wd=RndWrite,sd=sd*,rdpct=0,seekpct=100,xfersize=8k" >> vnet${vnet}-RndWrite-workload
        echo "wd=SeqMix,sd=sd*,rdpct=50,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqMix-workload
        echo "wd=RndMix,sd=sd*,rdpct=50,seekpct=100,xfersize=8k" >> vnet${vnet}-RndMix-workload
        for pattern in SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "* Run Defaults" >> vnet${vnet}-${pattern}-workload
            #echo "rd=default,curve=(50,100),iorate=curve,warmup=60,elapsed=600,iorate=curve" >> vnet${vnet}-${pattern}-workload
            echo "rd=default,warmup=60,elapsed=600,iorate=max" >> vnet${vnet}-${pattern}-workload
            echo "* Run Definitions" >> vnet${vnet}-${pattern}-workload
        done
        echo 'rd=RndRead,wd=RndRead,sd=("sd*"),threads=25' >> vnet${vnet}-RndRead-workload
        echo 'rd=RndWrite,wd=RndWrite,sd=("sd*"),threads=20' >> vnet${vnet}-RndWrite-workload
        echo 'rd=SeqRead-4,wd=SeqRead,sd=("sd*"),threads=20' >> vnet${vnet}-SeqRead-workload
        echo 'rd=SeqWrite-26,wd=SeqWrite,sd=("sd*"),threads=26' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-32,wd=SeqWrite,sd=("sd*"),threads=32' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-40,wd=SeqWrite,sd=("sd*"),threads=40' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=RndMix,wd=RndMix,sd=("sd*"),threads=25' >> vnet${vnet}-RndMix-workload
        echo 'rd=SeqMix-6,wd=SeqMix,sd=("sd*"),threads=6' >> vnet${vnet}-SeqMix-workload
        echo 'rd=SeqMix-7,wd=SeqMix,sd=("sd*"),threads=7' >> vnet${vnet}-SeqMix-workload
    done
