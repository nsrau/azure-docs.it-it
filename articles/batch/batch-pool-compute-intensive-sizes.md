---
title: Usare macchine virtuali a elevato uso di calcolo con Batch | Microsoft Docs
description: Come sfruttare le dimensioni di una macchina virtuale HPC e GPU in pool di Azure Batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/17/2018
ms.author: lahugh
ms.openlocfilehash: 3c3d534392431e79feabe37fe940ea87f586c660
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051697"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>Usare istanze GPU o RDMA nei pool di Batch

Per eseguire determinati processi Batch, è possibile sfruttare le dimensioni delle macchine virtuali di Azure progettate per il calcolo su larga scala. Ad esempio: 

* Per eseguire [carichi di lavoro MPI](batch-mpi.md) a istanze multiple, scegliere la serie H o altre dimensioni che offrono un'interfaccia di rete per Accesso diretto a memoria remota (RDMA, Remote Direct Memory Access). Le VM di queste dimensioni si connettono a una rete InfiniBand per la comunicazione tra i nodi, che consente di velocizzare le applicazioni MPI. 

* Per le applicazioni CUDA, scegliere le dimensioni della serie N, che includono schede GPU (Graphics Processing Unit, unità di elaborazione grafica) NVIDIA Tesla.

Questo articolo fornisce istruzioni ed esempi per usare alcune delle dimensioni specializzate di Azure nei pool di Batch. Per le specifiche e le informazioni di base, vedere:

* Dimensioni delle VM High Performance Computing ([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* Dimensioni delle VM con supporto per GPU ([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 

> [!NOTE]
> Determinate dimensioni di macchine virtuali potrebbero non essere disponibili nelle aree in cui si crea l'account Batch. Per verificare la disponibilità di una dimensione, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/) e [Scegliere una dimensione di macchina virtuale per un pool di Batch](batch-pool-vm-sizes.md).

## <a name="dependencies"></a>Dependencies

Le funzionalità RDMA o GPU delle dimensioni a elevato utilizzo di calcolo in Batch sono supportate solo in alcuni sistemi operativi. L'elenco di sistemi operativi supportati è un subset di quelli supportati per le macchine virtuali create in queste dimensioni. A seconda di come si crea il pool di Batch, potrebbe essere necessario installare o configurare altri driver o altro software nei nodi. Le tabelle seguenti riepilogano queste dipendenze. Vedere gli articoli correlati per informazioni dettagliate. Per le opzioni per la configurazione di pool di Batch, vedere più avanti in questo articolo.

### <a name="linux-pools---virtual-machine-configuration"></a>Pool Linux - Configurazione della macchina virtuale

| Dimensione | Funzionalità | Sistemi operativi | Requisiti software | Impostazioni pool |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r, NC24rs_v2, NC24rs_v3 e ND24rs<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Ubuntu 16.04 LTS o<br/>HPC basato su CentOS<br/>(Azure Marketplace) | Intel MPI 5<br/><br/>Driver Linux RDMA | Abilitare la comunicazione tra i nodi, disabilitare l'esecuzione di attività simultanee |
| [Serie NC, NCv2, NCv3 e NDv2](../virtual-machines/linux/n-series-driver-setup.md) | GPU NVIDIA Tesla (varia in base alla serie) | Ubuntu 16.04 LTS o<br/>CentOS 7.3 o 7.4<br/>(Azure Marketplace) | Driver NVIDIA CUDA o CUDA Toolkit | N/D | 
| [Serie NV e NVv2](../virtual-machines/linux/n-series-driver-setup.md) | GPU NVIDIA Tesla M60 | Ubuntu 16.04 LTS o<br/>CentOS 7.3<br/>(Azure Marketplace) | Driver NVIDIA GRID | N/D |
<sup>*</sup>Le dimensioni della serie N idonee per RDMA includono anche GPU NVIDIA Tesla

### <a name="windows-pools---virtual-machine-configuration"></a>Pool Windows - Configurazione della macchina virtuale

| Dimensione | Funzionalità | Sistemi operativi | Requisiti software | Impostazioni pool |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r, NC24rs_v2, NC24rs_v3 e ND24rs<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Windows Server 2016, 2012 R2 o<br/>2012 (Azure Marketplace) | Microsoft MPI 2012 R2 o versioni successive oppure<br/> Intel MPI 5<br/><br/>Driver Windows RDMA | Abilitare la comunicazione tra i nodi, disabilitare l'esecuzione di attività simultanee |
| [Serie NC, NCv2, NCv3, ND e NDv2](../virtual-machines/windows/n-series-driver-setup.md) | GPU NVIDIA Tesla (varia in base alla serie) | Windows Server 2016 oppure <br/>2012 R2 (Azure Marketplace) | Driver NVIDIA CUDA o CUDA Toolkit| N/D | 
| [Serie NV e NVv2](../virtual-machines/windows/n-series-driver-setup.md) | GPU NVIDIA Tesla M60 | Windows Server 2016 oppure<br/>2012 R2 (Azure Marketplace) | Driver NVIDIA GRID | N/D |
<sup>*</sup>Le dimensioni della serie N idonee per RDMA includono anche GPU NVIDIA Tesla

### <a name="windows-pools---cloud-services-configuration"></a>Pool Windows - Configurazione servizi cloud

> [!NOTE]
> Le dimensioni serie N non sono supportate nei pool di Batch con la configurazione Servizi cloud.
>

| Dimensione | Funzionalità | Sistemi operativi | Requisiti software | Impostazioni pool |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2016, 2012 R2, 2012 o<br/>2008 R2 (famiglia del sistema operativo guest) | Microsoft MPI 2012 R2 o versioni successive oppure<br/>Intel MPI 5<br/><br/>Driver Windows RDMA | Abilitare la comunicazione tra i nodi,<br/> disabilitare l'esecuzione di attività simultanee |

## <a name="pool-configuration-options"></a>Opzioni di configurazione pool

Per configurare una dimensione di macchina virtuale specializzata per il pool di Batch, sono disponibili diverse opzioni per installare il software o i driver necessari:

* Per i pool nella configurazione della macchina virtuale, scegliere un'immagine di macchina virtuale di [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) preconfigurata con i driver e il software preinstallato. Esempi: 

  * [HPC basato su CentOS 7.4](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview): include i driver RDMA e Intel MPI 5.1

  * [Data Science Virtual Machine](../machine-learning/data-science-virtual-machine/overview.md) per Linux o Windows: include i driver NVIDIA CUDA

  * Immagini Linux per i carichi di lavoro dei contenitori di Batch che includono anche i driver GPU e RDMA:

    * [CentOS (con driver GPU e RDMA) per pool di contenitori di Azure Batch](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [Ubuntu Server (con driver GPU e RDMA) per pool di contenitori di Azure Batch](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* Creare un'[immagine personalizzata di macchina virtuale Windows o Linux](batch-custom-images.md) in cui sono stati installati driver, software o altre impostazioni necessarie per le dimensioni della macchina virtuale. 

* Creare un [pacchetto dell'applicazione](batch-application-packages.md) Batch da un programma di installazione dell'applicazione o dei driver compresso e configurare Batch per distribuire il pacchetto nei nodi del pool e installarlo una sola volta quando viene creato ogni nodo. Se ad esempio il pacchetto dell'applicazione è un programma di installazione, creare una riga di comando di [attività di avvio](batch-api-basics.md#start-task) per installare automaticamente l'app in tutti i nodi del pool. Valutare la possibilità di usare un pacchetto dell'applicazione e un'attività di avvio del pool se il carico di lavoro dipende da una versione particolare dei driver.

  > [!NOTE] 
  > L'attività di avvio deve essere eseguita con autorizzazioni con privilegi elevati (amministratore) ed è necessario attendere l'esito positivo. Le attività a esecuzione prolungata aumenteranno il tempo necessario per effettuare il provisioning di un pool di Batch.
  >

* [Batch Shipyard](https://github.com/Azure/batch-shipyard) configura automaticamente i driver GPU e RDMA per usare in modo trasparente carichi di lavoro in contenitori in Azure Batch. Batch Shipyard si basa interamente su file di configurazione. Sono disponibili molte configurazioni di recipe di esempio che consentono carichi di lavoro GPU e RDMA, ad esempio il [recipe CNTK GPU](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) che preconfigura i driver GPU nelle macchine virtuali serie N e carica il software Microsoft Cognitive Toolkit come immagine Docker.


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>Esempio: driver NVIDIA GPU in pool di macchine virtuali Windows NC

Per eseguire applicazioni CUDA in un pool di nodi NC di Windows, è necessario installare i driver NVDIA GPU. La procedura di esempio seguente usa un pacchetto dell'applicazione per installare i driver NVIDIA GPU. È possibile scegliere questa opzione se il carico di lavoro dipende da una versione specifica dei driver GPU.

1. Scaricare un pacchetto di installazione per i driver GPU in Windows Server 2016 dal [sito Web di NVIDIA](https://www.nvidia.com/Download/index.aspx), ad esempio la [versione 411.82](http://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe). Salvare il file in locale usando un nome breve, ad esempio *GPUDriverSetup.exe*.
2. Creare un file zip del pacchetto.
3. Caricare il pacchetto nell'account Batch. Per istruzioni, vedere il materiale sussidiario sui [pacchetti di applicazioni](batch-application-packages.md). Specificare un ID applicazione, ad esempio *GPUDriver*, e una versione, ad esempio *411.82*.
1. Usando l'API Batch o il portale di Azure, creare un pool nella configurazione della macchina virtuale con il numero di nodi e la scalabilità desiderati. La tabella seguente illustra le impostazioni di esempio per installare i driver NVIDIA GPU automaticamente usando un'attività di avvio:

| Impostazione | Valore |
| ---- | ----- | 
| **Tipo di immagine** | Marketplace (Linux/Windows) |
| **Autore** | MicrosoftWindowsServer |
| **Offerta** | WindowsServer |
| **Sku** | 2016-Datacenter |
| **Dimensioni nodo** | NC6 Standard |
| **Riferimenti ai pacchetti dell'applicazione** | GPUDriver, versione 411.82 |
| **Attività di avvio abilitate** | True <br>**Riga di comando** - `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**Identità utente**: utente automatico pool, amministratore<br/>**Attendere il completamento dell'operazione**: True

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>Esempio: driver NVIDIA GPU in pool di macchine virtuali Linux NC

Per eseguire applicazioni CUDA in un pool di nodi NC di Linux, si devono installare i driver NVIDIA Tesla GPU necessari da CUDA Toolkit. La procedura di esempio seguente crea e distribuisce un'immagine personalizzata Ubuntu 16.04 LTS con i driver GPU:

1. Distribuire una macchina virtuale serie NC di Azure che esegue Ubuntu 16.04 LTS. È ad esempio possibile creare la macchina virtuale nell'area Stati Uniti centro-meridionali. 
2. Aggiungere l'[estensione di driver NVIDIA GPU](../virtual-machines/extensions/hpccompute-gpu-linux.md
) alla macchina virtuale usando il portale di Azure, un computer client che si connette alla sottoscrizione di Azure o Azure Cloud Shell. In alternativa, seguire questa procedura per connettersi alla macchina virtuale e [installare i driver CUDA](../virtual-machines/linux/n-series-driver-setup.md) manualmente.
3. Seguire la procedura per creare uno [snapshot e un'immagine di macchina virtuale Linux personalizzata](batch-custom-images.md) per Batch.
4. Creare un account in un'area che supporta le VM NC.
5. Usando l'API Batch o il portale di Azure, creare un pool [usando l'immagine personalizzata](batch-custom-images.md) e con il numero di nodi e la scalabilità desiderati. La tabella seguente illustra le impostazioni di esempio del pool per l'immagine:

| Impostazione | Valore |
| ---- | ---- |
| **Tipo di immagine** | Immagine personalizzata |
| **Immagine personalizzata** | *Nome dell'immagine* |
| **SKU agente nodo** | batch.node.ubuntu 16.04 |
| **Dimensioni nodo** | NC6 Standard |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>Esempio: Microsoft MPI in un pool di macchine virtuali Windows H16r

Per eseguire le applicazioni Windows MPI in un pool di nodi di macchine virtuali di Azure H16r, è necessario configurare l'estensione HpcVmDrivers e installare [Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi). Di seguito è riportata la procedura di esempio per distribuire un'immagine personalizzata di Windows Server 2016 con il software e i driver necessari:

1. Distribuire una macchina virtuale di Azure H16r che esegue Windows Server 2016. È ad esempio possibile creare la macchina virtuale nell'area Stati Uniti occidentali. 
2. Aggiungere l'estensione HpcVmDrivers alla macchina virtuale [eseguendo un comando di Azure PowerShell](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances
) da un computer client che si connette alla sottoscrizione di Azure o usando Azure Cloud Shell. 
1. Stabilire una connessione Desktop remoto alla macchina virtuale.
1. Scaricare il [pacchetto di installazione](https://www.microsoft.com/download/details.aspx?id=57467) (MSMpiSetup.exe) per la versione più recente di Microsoft MPI e installare Microsoft MPI.
1. Seguire la procedura per creare uno [snapshot e un'immagine di macchina virtuale Windows personalizzata](batch-custom-images.md) per Batch.
1. Usando l'API Batch o il portale di Azure, creare un pool [usando l'immagine personalizzata](batch-custom-images.md) e con il numero di nodi e la scalabilità desiderati. La tabella seguente illustra le impostazioni di esempio del pool per l'immagine:

| Impostazione | Valore |
| ---- | ---- |
| **Tipo di immagine** | Immagine personalizzata |
| **Immagine personalizzata** | *Nome dell'immagine* |
| **SKU agente nodo** | batch.node.windows amd64 |
| **Dimensioni nodo** | H16r Standard |
| **Comunicazione tra nodi abilitata** | True  |
| **Numero massimo di attività per ogni nodo** | 1 |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>Esempio: Intel MPI in un pool di macchine virtuali Linux H16r

Per eseguire le applicazioni MPI in un pool di nodi Linux della serie H, è possibile usare l'immagine [HPC basato su CentOS 7.4](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) di Azure Marketplace. I driver Linux RDMA e Intel MPI sono preinstallati. Questa immagine supporta anche i carichi di lavoro dei contenitori Docker.

Usando l'API Batch o il portale di Azure, creare un pool usando questa immagine, con il numero di nodi e la scalabilità desiderati. La tabella seguente illustra le impostazioni di esempio del pool:

| Impostazione | Valore |
| ---- | ---- |
| **Tipo di immagine** | Marketplace (Linux/Windows) |
| **Autore** | OpenLogic |
| **Offerta** | CentOS-HPC |
| **Sku** | 7.4 |
| **Dimensioni nodo** | H16r Standard |
| **Comunicazione tra nodi abilitata** | True  |
| **Numero massimo di attività per ogni nodo** | 1 |

## <a name="next-steps"></a>Passaggi successivi

* Per eseguire processi MPI in un pool di Azure Batch, vedere gli esempi per [Windows](batch-mpi.md) o [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/).

* Per esempi di carichi di lavoro GPU in Batch, vedere i recipe [Batch Shipyard](https://github.com/Azure/batch-shipyard/).