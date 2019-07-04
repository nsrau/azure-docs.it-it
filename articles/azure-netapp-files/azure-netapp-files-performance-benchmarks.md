---
title: Benchmark delle prestazioni per file di Azure NetApp | Microsoft Docs
description: Descrive i risultati dei test di benchmark delle prestazioni per file di NetApp Azure a livello di volume.
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
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: 14081daf1f45a84bc8ad19bf0239db1281d9e624
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449512"
---
# <a name="performance-benchmarks-for-azure-netapp-files"></a>Benchmark delle prestazioni per Azure NetApp Files

Questo articolo descrive i risultati dei test di benchmark delle prestazioni per file di NetApp Azure a livello di volume. 

## <a name="sample-application-used-for-the-tests"></a>Applicazione di esempio usata per i test

Test delle prestazioni sono stati eseguiti con un'applicazione di esempio usando i file di Azure NetApp. L'applicazione presenta le caratteristiche seguenti: 

* Un'applicazione basata su Linux creata per il cloud
* Può essere ridimensionato in modo lineare con aggiuntive macchine virtuali (VM) per aumentare la potenza di calcolo in base alle esigenze
* Richiede l'accesso facilitato rapido di data lake
* Include modelli dei / o che sono a volte talvolta sequenziale e casuale 
    * Un modello casuale richiede a bassa latenza per grandi quantità dei / o. 
    * Un modello sequenza richiede grandi quantità di larghezza di banda. 

## <a name="about-the-workload-generator"></a>Sul generatore di carico di lavoro

I risultati provengono dai file di riepilogo Vdbench. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) è un'utilità della riga di comando che genera i carichi di lavoro dei / o disco per la convalida delle prestazioni di archiviazione. La configurazione del client-server utilizzata è scalabile.  Include un singolo misto master/client e client dedicati 14 macchine virtuali.

## <a name="about-the-tests"></a>Sui test

I test sono stati progettati per identificare i limiti che potrebbe avere l'applicazione di esempio e il tempo di risposta che curve fino ai limiti.  

Sono stati eseguiti i test seguenti: 

* 100% 8-KiB casuali letti
* Scrittura casuali 8 KiB 100%
* 100% 64-KiB sequenziali di lettura
* Scrittura sequenziale 64 KiB 100%
* 50% 64-KiB sequenziali di lettura, scrittura sequenziale 64 KiB 50%
* 50% 8-KiB casuali di lettura, scrittura casuali 8 KiB 50%

## <a name="bandwidth"></a>Larghezza di banda

File di Azure NetApp offre più [livelli di servizio](azure-netapp-files-service-levels.md). Ogni livello di servizio offre una quantità diversa di larghezza di banda per ogni TiB di capacità con provisioning (la quota del volume). Il limite di larghezza di banda per un volume viene eseguito il provisioning in base alla combinazione del livello di servizio e la quota per il volume. Il limite di larghezza di banda è solo un fattore che determina la quantità effettiva di velocità effettiva che verrà realizzata.  

Attualmente, MiB 4.500 è la velocità effettiva massima che il risultato è stata ottenuta da un carico di lavoro in un singolo volume nel test.  Con il livello di servizio Premium, una quota di volume di TiB 70.31 verrà effettuato il provisioning di larghezza di banda sufficiente per realizzare questa velocità effettiva per il calcolo seguente: 

![Formula della larghezza di banda](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![A livello di quota e il servizio](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Carichi di lavoro a elevato utilizzo di velocità effettiva

Il test di velocità effettiva usato Vdbench e una combinazione di macchine virtuali di archiviazione di 12xD32s V3. Il volume di esempio del test di ottenuta i numeri di velocità effettiva seguenti:

![Test di velocità effettiva](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>Carichi di lavoro dei / O intensivo

Il test dei / o usati Vdbench e una combinazione di macchine virtuali di archiviazione di 12xD32s V3. Il volume di esempio del test di ottenuta i numeri dei / o seguenti:

![Test dei / o](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Latenza

La distanza tra le VM di test e il volume di file di NetApp Azure ha un impatto sulle prestazioni i/o.  Il grafico seguente confronta il numero di IOPS e curve di risposta della latenza per due diversi set di macchine virtuali.  Un set di macchine virtuali è in prossimità di file di Azure NetApp e l'altro è ulteriormente da subito.  L'aumento della latenza per le altre VM del set influisce sulla quantità di operazioni IOPS ottenuta a un determinato livello di parallelismo.  Indipendentemente dal fatto che, operazioni di lettura di un volume può superare 300.000 IOPS, come illustrato di seguito: 

![Studio di latenza](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Riepilogo

I carichi di lavoro sensibili alla latenza (database) può avere un tempo di risposta di un millisecondo. Le prestazioni transazionali possono essere oltre 300 KB IOPS per un singolo volume.

Le applicazioni sensibili alla velocità effettiva (per lo streaming e la creazione di immagini) possono avere 4.5GiB / s di velocità effettiva.

## <a name="example-scripts"></a>Script di esempio

Gli script di esempio seguenti sono solo a scopo dimostrativo.  Non sono può essere utilizzato per scopi di produzione.  

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
