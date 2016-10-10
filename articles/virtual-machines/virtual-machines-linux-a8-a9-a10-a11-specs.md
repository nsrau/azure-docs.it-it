<properties
 pageTitle="Informazioni sulle macchine virtuali a elevato utilizzo di calcolo con Linux | Microsoft Azure"
 description="Informazioni generali e considerazioni sull'uso delle dimensioni serie H e A8, A9, A10 e A11 a elevato utilizzo di calcolo per macchine virtuali Linux"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>  
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>  

# Informazioni sulle macchine virtuali serie H e serie A a elevato utilizzo di calcolo 

Informazioni generali e considerazioni sull'uso delle nuove dimensioni di Azure serie H e delle precedenti dimensioni A8, A9, A10 e A11, note anche come istanze *a elevato utilizzo di calcolo*. Questo articolo illustra l'uso di queste dimensioni per macchine virtuali Linux. Questo articolo è disponibile anche per le [VM Windows](virtual-machines-windows-a8-a9-a10-a11-specs.md).




[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Accesso alla rete RDMA

È possibile creare cluster di macchine virtuali Linux con supporto per RDMA che eseguono una delle distribuzioni HPC Linux riportate di seguito e un'implementazione MPI supportata per sfruttare i vantaggi di una rete RDMA di Azure. Per le opzioni di distribuzione e la procedura di configurazione di esempio, vedere[Configurare un cluster Linux RDMA per eseguire applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md).

* **Distribuzioni**: è necessario distribuire macchine virtuali da immagini SLES (SUSE Linux Enterprise Server) con supporto per RDMA o da immagini HPC OpenLogic basate su CentOS in Azure Marketplace. I driver Linux RDMA necessari sono supportati solo dalle immagini Marketplace seguenti:

    * SLES 12 SP1 per HPC, SLES 12 SP1 per HPC (Premium)
    
    * SLES 12 per HPC, SLES 12 per HPC (Premium)
    
    * HPC basata su CentOS 7.1
    
    * HPC basata su CentOS 6.5
    
    >[AZURE.NOTE]Per le macchine virtuali serie H è consigliabile un'immagine SLES 12 SP1 per HPC o un'immagine HPC basata su CentOS 7.1.
    >
    >Nelle immagini HPC basate su CentOS gli aggiornamenti del kernel sono disabilitati nel file di configurazione **yum**. In questo modo i driver Linux RDMA vengono distribuiti come pacchetto RPM e gli aggiornamenti dei driver potrebbero non funzionare se il kernel viene aggiornato.

* **MPI**: Intel MPI Library 5.x.

    A seconda dell'immagine Marketplace scelta, può essere necessario separare licenze, installazione o configurazione di Intel MPI, come illustrato di seguito:
    
    * **SLES 12 SP1 per immagine HPC**: installare i pacchetti Intel MPI distribuiti nella macchina virtuale eseguendo il comando seguente:
    
            sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm

    * **SLES 12 per immagine HPC**: è necessario eseguire una registrazione separata per scaricare e installare Intel MPI. Vedere la [Intel MPI Library installation guide](https://software.intel.com/sites/default/files/managed/7c/2c/intelmpi-2017-installguide-linux.pdf) (Guida all'installazione di Intel MPI Library).
    
    * **Immagini HPC basate su CentOS**: Intel MPI 5.1 è già installato.

    Per eseguire processi MPI su macchine virtuali n cluster, è necessaria una configurazione di sistema aggiuntiva. In un cluster di macchine virtuali, ad esempio, è necessario stabilire una relazione di trust tra i nodi di calcolo. Per le impostazioni tipiche, vedere [Configurazione di un cluster Linux RDMA per eseguire applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md).


## Considerazioni per HPC Pack e Linux

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), la soluzione Microsoft gratuita per la gestione di cluster e processi, fornisce un'opzione per l'uso di istanze a elevato utilizzo di calcolo con Linux. La versione più recente di HPC Pack 2012 R2 supporta varie distribuzioni Linux per l'esecuzione su nodi di calcolo distribuiti in VM di Azure, gestite da un nodo head di Windows Server. Grazie ai nodi di calcolo Linux con supporto per RDMA che eseguono Intel MPI, HPC Pack può pianificare ed eseguire applicazioni Linux MPI che hanno accesso alla rete RDMA. Per iniziare, vedere[Introduzione all'uso di nodi di calcolo Linux in un cluster HPC Pack in Azure](virtual-machines-linux-classic-hpcpack-cluster.md)

## Considerazioni sulla topologia di rete

* Nelle macchine virtuali Linux con supporto per RDMA in Azure, Eth1 è riservato al traffico di rete RDMA. Non modificare le impostazioni di Eth1 o le informazioni nel file di configurazione riferite a questa rete. Eth0 è riservato per il normale traffico di rete di Azure.

* In Azure, IP over InfiniBand (IB) non è supportato. Solo RDMA over IB è supportato.

## Aggiornamenti dei driver RDMA per SLES 12

Dopo la creazione di una macchina virtuale basata su un'immagine SLES 12 HPC, può essere necessario aggiornare i driver RDMA nelle macchine virtuali per la connettività di rete RDMA.

>[AZURE.IMPORTANT]Questo passaggio è **obbligatorio** per le distribuzioni di macchine virtuali SLES 12 per HPC in tutte le aree di Azure. Questo passaggio non è necessario se si distribuisce una macchina virtuale SLES 12 SP1 per HPC, una macchina virtuale HPC basata su CentOS 7.1 o una macchina virtuale HPC basata su CentOS 6.5.

Prima di aggiornare i driver, arrestare tutti i processi **zypper** o tutti i processi che bloccano i database repository SUSE nella VM. In caso contrario, i driver rischiano di non essere aggiornati correttamente.

Per aggiornare i driver Linux RDMA in ogni VM, eseguire uno dei seguenti set di comandi dell'interfaccia della riga di comando di Azure seguenti nel computer client.

**Macchina virtuale SLES 12 per HPC di cui è stato effettuato il provisioning nel modello di distribuzione classica**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**Macchina virtuale SLES 12 per HPC di cui è stato effettuato il provisioning nel modello di distribuzione Resource Manager**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

>[AZURE.NOTE]L'installazione dei driver può richiedere un po' di tempo e il comando non restituisce un output. Dopo l'aggiornamento, la VM viene riavviata e nel giro di qualche minuto è pronta per l'uso.

### Script di esempio per gli aggiornamenti dei driver

Se è disponibile un cluster di macchine virtuali SLES 12 per HPC, è possibile creare script di aggiornamento del driver in tutti i nodi del cluster. Ad esempio, lo script seguente aggiorna il driver in un cluster a 8 nodi.

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For VMs created in the classic deployment model, use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For VMs created in the Resource Manager deployment model, use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```


## Passaggi successivi

* Per informazioni dettagliate sulla disponibilità e i prezzi delle dimensioni a elevato utilizzo di calcolo, vedere [Macchine virtuali - Prezzi](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux).

* Per informazioni sulle funzionalità di archiviazione e dettagli relativi ai dischi, vedere [Dimensioni delle macchine virtuali in Azure](virtual-machines-linux-sizes.md).

* Per iniziare a distribuire e usare dimensioni a elevato utilizzo di calcolo con RDMA in Linux, vedere [Configurare un cluster Linux RDMA per eseguire applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md).

<!---HONumber=AcomDC_0928_2016-->