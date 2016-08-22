<properties
 pageTitle="Informazioni sulle dimensioni di VM A8, A9, A10, A11 con Linux | Microsoft Azure"
 description="Informazioni generali e considerazioni sull'uso delle dimensioni a elevato utilizzo di calcolo A8, A9, A10 e A11 per VM Linux"
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
 ms.date="08/04/2016"
 ms.author="danlep"/>  

# Informazioni sulle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo 

Informazioni generali e considerazioni sull'uso delle istanze A8, A9, A10 e A11 di Azure, note anche come istanze *a elevato utilizzo di calcolo*. Questo articolo illustrato l'uso di queste istanze per le VM Linux. Questo articolo è disponibile anche per le [VM Windows](virtual-machines-windows-a8-a9-a10-a11-specs.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Accesso alla rete RDMA

All'interno di un servizio cloud singolo o di un set di disponibilità, i cluster di VM Linux di dimensioni A8 e A9 che eseguono una delle distribuzioni Linux HPC supportate seguenti e un'implementazione MPI supportata possono accedere alla rete RDMA in Azure per eseguire applicazioni MPI Linux. Per le opzioni di distribuzione e la procedura di configurazione di esempio, vedere[Configurare un cluster Linux RDMA per eseguire applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md).

* **Distribuzioni**: SUSE Linux Enterprise Server (SLES) 12 per HPC, SLES 12 per HPC (Premium), HPC 7.1 basato su CentOS o HPC 6.5 basato su CentOS, distribuite da un'immagine di Azure Marketplace

* **MPI**: Intel MPI Library 5.x.

    >[AZURE.NOTE] Intel MPI 5.1 è già installato nelle immagini HPC basate su CentOS nel Marketplace. Per usare Intel MPI su VM HPC SLES 12, è necessario installarlo separatamente.

Attualmente, i driver Azure Linux RDMA vengono installati solo quando si distribuiscono immagini SLES 12 HPC e CentOS HPC abilitate per RDMA da Azure Marketplace. Non è possibile installare i driver in altre VM Linux distribuite.

>[AZURE.NOTE]Nelle immagini HPC basate su CentOS dal Marketplace gli aggiornamenti del kernel sono disabilitati nel file di configurazione **yum**. In questo modo i driver Linux RDMA vengono distribuiti come pacchetto RPM e gli aggiornamenti dei driver potrebbero non funzionare se il kernel viene aggiornato.


## Aggiornamenti dei driver RDMA per SLES 12
Dopo la creazione di una VM basata su un'immagine SLES 12 HPC, è necessario aggiornare i driver RDMA nelle VM per la connettività di rete RDMA.

>[AZURE.IMPORTANT]Questo passaggio è **obbligatorio** per le distribuzioni di VM SLES 12 HPC in tutte le aree di Azure. Questo passaggio non è necessario se è stata distribuita una VM un HPC 7.1 o HPC 6.5 basato su CentOS.

Prima di aggiornare i driver, arrestare tutti i processi **zypper** o tutti i processi che bloccano i database repository SUSE nella VM. In caso contrario, i driver rischiano di non essere aggiornati correttamente.

Per aggiornare i driver Linux RDMA in ogni VM, eseguire uno dei seguenti set di comandi dell'interfaccia della riga di comando di Azure seguenti nel computer client.

**Per una VM SLES 12 HPC di cui è stato effettuato il provisioning nel modello di distribuzione classica**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**Per una VM SLES 12 HPC di cui è stato effettuato il provisioning nel modello di distribuzione Resource Manager**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

>[AZURE.NOTE]L'installazione dei driver può richiedere un po' di tempo e il comando viene restituito senza output. Dopo l'aggiornamento, la VM viene riavviata e nel giro di qualche minuto è pronta per l'uso.

### Script di esempio per gli aggiornamenti dei driver

Se è disponibile un cluster di VM SLES 12 HPC, è possibile creare script di aggiornamento del driver in tutti i nodi del cluster. Ad esempio, lo script seguente aggiorna il driver in un cluster a 8 nodi.

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For VMs created in the classic deployment model use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For VMs created in the Resource Manager deployment model, use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```


## Considerazioni per HPC Pack e Linux

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) è una soluzione gratuita di Microsoft per la gestione di processi e cluster HPC. La versione più recente di HPC Pack 2012 R2 supporta varie distribuzioni Linux per l'esecuzione su nodi di calcolo distribuiti in VM di Azure, gestite da un nodo head di Windows Server. I nodi di calcolo Linux distribuiti su VM A8 o A9 e che eseguono un'implementazione MPI supportata possono eseguire applicazioni MPI che accedono alla rete RDMA. Per iniziare, vedere[Introduzione all'uso di nodi di calcolo Linux in un cluster HPC Pack in Azure](virtual-machines-linux-classic-hpcpack-cluster.md)

## Considerazioni sulla topologia di rete

* Nelle macchine virtuali Linux A8 o A9 in Azure Eth1 è riservato per il traffico di rete RDMA. Non modificare le impostazioni di Eth1 o le informazioni nel file di configurazione riferite a questa rete. Eth0 è riservato per il normale traffico di rete di Azure.

* In Azure IP over Infiniband (IB) non è supportato. Solo RDMA over IB è supportato.


## Passaggi successivi

* Per informazioni dettagliate sulla disponibilità e i prezzi delle istanze A8, A9, A10 e A11, vedere [Prezzi di Macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux).

* Per informazioni sulle funzionalità di archiviazione e dettagli relativi ai dischi, vedere [Dimensioni delle macchine virtuali in Azure](virtual-machines-linux-sizes.md).

* Per iniziare a distribuire e usare istanze A8 e A9 con RDMA in Linux, vedere [Configurare un cluster Linux RDMA per eseguire applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md).

<!---HONumber=AcomDC_0810_2016-->