---
title: Usare macchine virtuali a elevato uso di calcolo con Batch | Microsoft Docs
description: Come sfruttare GPU con supporto per RDMA o dimensioni di VM abilitate per GPU in pool di Azure Batch
services: batch
documentationcenter: 
author: dlepow
manager: timlt
editor: 
ms.assetid: 
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: danlep
ms.openlocfilehash: 7624a905f81024fa87f15164efc56a300843972d
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2017
---
# <a name="use-rdma-capable-or-gpu-enabled-instances-in-batch-pools"></a>Usare istanze con supporto per RDMA o abilitate per GPU in pool di Batch

Per eseguire determinati processi Batch, è consigliabile sfruttare le dimensioni delle macchine virtuali di Azure progettate per il calcolo su larga scala. Per eseguire ad esempio [carichi di lavoro MPI](batch-mpi.md) a istanze multiple è possibile scegliere le dimensioni A8, A9 o H che offrono un'interfaccia di rete per RDMA (Remote Direct Memory Access, accesso diretto a memoria remota). Le VM di queste dimensioni si connettono a una rete InfiniBand per la comunicazione tra i nodi, che consente di velocizzare le applicazioni MPI. Per le applicazioni CUDA è possibile scegliere le dimensioni della serie N, che includono schede GPU (Graphics Processing Unit, unità di elaborazione grafica) NVIDIA Tesla.

Questo articolo fornisce istruzioni ed esempi per usare alcune delle dimensioni specializzate di Azure nei pool di Batch. Per le specifiche e le informazioni di base, vedere:

* Dimensioni delle VM High Performance Computing ([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* Dimensioni delle VM con supporto per GPU ([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 


## <a name="subscription-and-account-limits"></a>Limiti della sottoscrizione e dell'account

* **Quote**: la [quota di core dedicati per ogni account Batch](batch-quota-limit.md#resource-quotas) può limitare il numero o il tipo di nodi che possono essere aggiunti a un pool di Batch. È più probabile raggiungere una quota quando si scelgono dimensioni di VM con supporto per RDMA, abilitate per GPU o multicore. Per impostazione predefinita, la quota è pari a 20 core. Si applica una quota separata alle [macchine virtuali a bassa priorità](batch-low-pri-vms.md), se usate. 

Se è necessario un aumento della quota, è possibile aprire una [richiesta di assistenza clienti online](../azure-supportability/how-to-create-azure-support-request.md) senza alcun addebito.

* **Disponibilità a livello di area**: le VM a elevato utilizzo di calcolo potrebbero non essere disponibili nelle aree in cui si crea l'account Batch. Per verificare la disponibilità di una dimensione, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).


## <a name="dependencies"></a>Dipendenze

Le funzionalità RDMA e GPU delle dimensioni a elevato utilizzo di calcolo sono supportate solo in alcuni sistemi operativi. A seconda del sistema operativo, potrebbe essere necessario installare o configurare altri driver o altro software. Le tabelle seguenti riepilogano queste dipendenze. Vedere gli articoli correlati per informazioni dettagliate. Per le opzioni per la configurazione di pool di Batch, vedere più avanti in questo articolo.


### <a name="linux-pools---virtual-machine-configuration"></a>Pool Linux - Configurazione della macchina virtuale

| Dimensione | Funzionalità | Sistemi operativi | Requisiti software | Impostazioni pool |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances) | RDMA | Ubuntu 16.04 LTS,<br/>HPC SUSE Linux Enterprise Server 12 oppure<br/>HPC basato su CentOS<br/>(Azure Marketplace) | Intel MPI 5 | Abilitare la comunicazione tra i nodi, disabilitare l'esecuzione di attività simultanee |
| [Serie NC*](../virtual-machines/linux/n-series-driver-setup.md#install-cuda-drivers-for-nc-vms) | GPU NVIDIA Tesla K80 | Ubuntu 16.04 LTS,<br/>Red Hat Enterprise Linux 7.3 oppure<br/>CentOS-based 7.3<br/>(Azure Marketplace) | Driver NVIDIA CUDA Toolkit 9.0 | N/D | 
| [Serie NV](../virtual-machines/linux/n-series-driver-setup.md#install-grid-drivers-for-nv-vms) | GPU NVIDIA Tesla M60 | Ubuntu 16.04 LTS,<br/>Red Hat Enterprise Linux 7.3 oppure<br/>CentOS-based 7.3<br/>(Azure Marketplace) | Driver NVIDIA GRID 4.3 | N/D |

*La connettività RDMA nelle macchine virtuali NC24r è supportata in Ubuntu 16.04 LTS o in HPC basato su CentOS 7.3 (da Azure Marketplace) con Intel MPI.



### <a name="windows-pools---virtual-machine-configuration"></a>Pool Windows - Configurazione della macchina virtuale

| Dimensione | Funzionalità | Sistemi operativi | Requisiti software | Impostazioni pool |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2012 R2 oppure<br/>Windows Server 2012 (Azure Marketplace) | Microsoft MPI 2012 R2 o versioni successive oppure<br/> Intel MPI 5<br/><br/>Estensione di VM Azure HpcVMDrivers | Abilitare la comunicazione tra i nodi, disabilitare l'esecuzione di attività simultanee |
| [Serie NC*](../virtual-machines/windows/n-series-driver-setup.md) | GPU NVIDIA Tesla K80 | Windows Server 2016 oppure <br/>Windows Server 2012 R2 (Azure Marketplace) | Driver NVIDIA Tesla o CUDA Toolkit 9.0| N/D | 
| [Serie NV](../virtual-machines/windows/n-series-driver-setup.md) | GPU NVIDIA Tesla M60 | Windows Server 2016 oppure<br/>Windows Server 2012 R2 (Azure Marketplace) | Driver NVIDIA GRID 4.3 | N/D |

*La connettività RDMA nelle macchine virtuali NC24r è supportata in Windows Server 2012 R2 (da Azure Marketplace) con estensione HpcVMDrivers e Microsoft MPI o Intel MPI.

### <a name="windows-pools---cloud-services-configuration"></a>Pool Windows - Configurazione servizi cloud

> [!NOTE]
> Le dimensioni serie N non sono supportate nei pool di Batch con la configurazione Servizi cloud.
>

| Dimensione | Funzionalità | Sistemi operativi | Requisiti software | Impostazioni pool |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2012 R2,<br/>Windows Server 2012 oppure<br/>Windows Server 2008 R2 (famiglia di sistemi operativi guest) | Microsoft MPI 2012 R2 o versioni successive oppure<br/>Intel MPI 5<br/><br/>Estensione di VM Azure HpcVMDrivers | Abilitare la comunicazione tra i nodi,<br/> disabilitare l'esecuzione di attività simultanee |





## <a name="pool-configuration-options"></a>Opzioni di configurazione pool

Per configurare una dimensione di macchina virtuale specializzata per il pool di Batch, le API e gli strumenti di Batch offrono diverse opzioni per installare il software o i driver, tra cui:

* [Attività di avvio](batch-api-basics.md#start-task): caricare un pacchetto di installazione come file di risorse in un account di archiviazione di Azure nella stessa area dell'account Batch. Creare una riga di comando di attività di avvio per installare automaticamente il file di risorse all'avvio del pool. Per altre informazioni, vedere la [documentazione dell'API Rest](/rest/api/batchservice/add-a-pool-to-an-account#bk_starttask).

  > [!NOTE] 
  > L'attività di avvio deve essere eseguita con autorizzazioni con privilegi elevati (amministratore) ed è necessario attendere l'esito positivo.
  >

* [Pacchetto dell'applicazione](batch-application-packages.md): aggiungere un pacchetto di installazione compresso all'account Batch e configurare un riferimento al pacchetto nel pool. Questa impostazione consente di caricare e decomprime il pacchetto in tutti i nodi del pool. Se il pacchetto è un programma di installazione, creare una riga di comando di attività di avvio per installare automaticamente l'app in tutti i nodi del pool. È anche possibile installare il pacchetto quando un'attività è pianificata per l'esecuzione in un nodo.

* [Immagine pool personalizzata](batch-custom-images.md): creare un'immagine personalizzata di VM Windows o Linux contenente driver, software o altre impostazioni necessarie per le dimensioni della VM. 

* [Batch Shipyard](https://github.com/Azure/batch-shipyard) configura automaticamente GPU e RDMA per usare in modo trasparente carichi di lavoro in contenitori in Azure Batch. Batch Shipyard si basa interamente su file di configurazione. Sono disponibili molte configurazioni di recipe di esempio che consentono carichi di lavoro GPU e RDMA, ad esempio il [recipe CNTK GPU](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) che preconfigura i driver GPU nelle macchine virtuali serie N e carica il software Microsoft Cognitive Toolkit come immagine Docker.


## <a name="example-microsoft-mpi-on-an-a8-vm-pool"></a>Esempio: Microsoft MPI in un pool di VM A8

Per eseguire applicazioni Windows MPI in un pool di nodi A8 di Azure è necessario installare un'implementazione MPI supportata. Di seguito è riportata la procedura per installare [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) in un pool di Windows con un pacchetto di applicazione Batch.

1. Scaricare il [pacchetto di installazione](http://go.microsoft.com/FWLink/p/?LinkID=389556) (MSMpiSetup.exe) per la versione più recente di Microsoft MPI.
2. Creare un file zip del pacchetto.
3. Caricare il pacchetto nell'account Batch. Per istruzioni, vedere il materiale sussidiario sui [pacchetti di applicazioni](batch-application-packages.md). Specificare un ID applicazione, ad esempio *MSMPI*, e una versione come *8.1*. 
4. Usando l'API Batch o il portale di Azure, creare un pool nella configurazione dei servizi cloud con il numero di nodi e la scalabilità desiderati. La tabella seguente illustra le impostazioni di esempio per installare MPI in modalità automatica con un'attività di avvio:

| Impostazione | Valore |
| ---- | ----- | 
| **Tipo di immagine** | Servizi cloud |
| **Famiglia del sistema operativo** | Windows Server 2012 R2 (famiglia del sistema operativo 4) |
| **Dimensioni nodo** | A8 Standard |
| **Comunicazione tra nodi abilitata** | True  |
| **Numero massimo di attività per ogni nodo** | 1 |
| **Riferimenti ai pacchetti dell'applicazione** | MSMPI |
| **Attività di avvio abilitate** | True <br>**Riga di comando** - `"cmd /c %AZ_BATCH_APP_PACKAGE_MSMPI#8.1%\\MSMpiSetup.exe -unattend -force"`<br/>**Identità utente**: utente automatico pool, amministratore<br/>**Attendere il completamento dell'operazione**: True

## <a name="example-nvidia-tesla-drivers-on-nc-vm-pool"></a>Esempio: Driver NVIDIA Tesla in pool di VM NC

Per eseguire applicazioni CUDA in un pool di nodi NC di Linux, è necessario installare CUDA Toolkit 9.0 nei nodi. Il Toolkit installa i driver GPU NVIDIA Tesla necessari. Ecco i passaggi di esempio per distribuire un'immagine personalizzata Ubuntu 16.04 LTS con i driver GPU:

1. Distribuire una macchina virtuale NC6 di Azure che esegue Ubuntu 16.04 LTS. È ad esempio possibile creare la macchina virtuale nell'area Stati Uniti centro-meridionali. Assicurarsi di creare la macchina virtuale con un disco gestito.
2. Seguire questa procedura per connettersi alla macchina virtuale e [installare i driver CUDA](../virtual-machines/linux/n-series-driver-setup.md#install-cuda-drivers-for-nc-vms).
3. Effettuare il deprovisioning dell'agente Linux e quindi [acquisire l'immagine della VM Linux](../virtual-machines/linux/capture-image.md).
4. Creare un account in un'area che supporta le VM NC.
5. Usando l'API Batch o il portale di Azure, creare un pool [usando l'immagine personalizzata](batch-custom-images.md) e con il numero di nodi e la scalabilità desiderati. La tabella seguente illustra le impostazioni di esempio del pool per l'immagine:

| Impostazione | Valore |
| ---- | ---- |
| **Tipo di immagine** | Immagine personalizzata |
| **Immagine personalizzata** | Nome dell'immagine |
| **SKU agente nodo** | batch.node.ubuntu 16.04 |
| **Dimensioni nodo** | NC6 Standard |



## <a name="next-steps"></a>Passaggi successivi

* Per eseguire processi MPI in un pool di Azure Batch, vedere gli esempi per [Windows](batch-mpi.md) o [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/).

* Per esempi di carichi di lavoro GPU in Batch, vedere i recipe [Batch Shipyard](https://github.com/Azure/batch-shipyard/).