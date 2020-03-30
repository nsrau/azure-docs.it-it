---
title: Dimensioni delle macchine virtuali di Azure - HPC Documenti Microsoft
description: Elenca le diverse dimensioni disponibili per le macchine virtuali di elaborazione ad alte prestazioni in Azure.Lists the different sizes available for high performance computing virtual machines in Azure. Elenca informazioni sul numero di vCPU, dei dischi dati e delle schede di rete, nonché sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per le dimensioni di queste serie.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: b900a95df00ccdd0ad9b5bee3887364195c7d1c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78226723"
---
# <a name="high-performance-compute-vm-sizes"></a>Dimensioni delle VM High Performance Computing (HPC)

Le macchine virtuali (VM) della serie H di Azure sono progettate per offrire prestazioni di livello leader, scalabilità MPI ed efficienza dei costi per un'ampia gamma di carichi di lavoro HPC reali.

[Serie HBv2](hbv2-series.md) Le macchine virtuali dispongono di 200 Gb/sec Mellanox HDR InfiniBand, mentre entrambe le macchine virtuali HB e serie HC dispongono di 100 Gb/sec Mellanox EDR InfiniBand. Ognuno di questi tipi di macchine virtuali è connesso in un albero di grasso non bloccante per prestazioni RDMA ottimizzate e coerenti. Le macchine virtuali HBv2 supportano il routing adattivo e il trasporto connesso dinamico (DCT, in aggiunta ai trasporti RC e UD standard). Queste funzionalità migliorano le prestazioni, la scalabilità e la coerenza delle applicazioni e è consigliabile utilizzarle.

[Serie HB](hb-series.md) Le macchine virtuali sono ottimizzate per le applicazioni guidate dalla larghezza di banda della memoria, ad esempio la fluidodinamica, l'analisi esplicita degli elementi finiti e la modellazione meteorologica. Le macchine virtuali HB sono dotate di 60 core del processore AMD EPYC 7551, 4 GB di RAM per core CPU e nessun hyperthreading. La piattaforma AMD EPYC fornisce più di 260 GB/sec di larghezza di banda della memoria.

[Serie HC](hc-series.md) Le macchine virtuali sono ottimizzate per le applicazioni guidate da un calcolo denso, ad esempio l'analisi implicita degli elementi finiti, la dinamica molecolare e la chimica computazionale. Le macchine virtuali HC dispongono di 44 core del processore Intel Xeon Platinum 8168, 8 GB di RAM per core CPU e nessun hyperthreading. La piattaforma Intel Xeon Platinum supporta il ricco ecosistema di strumenti software Intel come Intel Math Kernel Library.

[Serie H](h-series.md) Le macchine virtuali sono ottimizzate per le applicazioni guidate da frequenze di CPU elevate o da grandi quantità di memoria per core. Le macchine virtuali di serie H sono dotate di 8 o 16 core del processore Intel Xeon E5 2667 v3, 7 o 14 GB di RAM per core CPU e nessun hyperthreading. Serie H dispone di 56 Gb/sec Mellanox FDR InfiniBand in una configurazione ad albero grasso non bloccante per prestazioni RDMA coerenti. Le macchine virtuali della serie H supportano Intel MPI 5.x e MS-MPI.

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

- **Sottoscrizione di Azure**: per distribuire numerose istanze a elevato utilizzo di calcolo, prendere in considerazione una sottoscrizione con pagamento in base al consumo o altre opzioni di acquisto. Con un [account gratuito di Azure](https://azure.microsoft.com/free/)è possibile usare solo un numero limitato di core di calcolo di Azure.

- **Prezzi e disponibilità**: queste dimensioni di VM sono offerte solo con il piano tariffario Standard. Per informazioni sulla disponibilità nelle aree di Azure, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/) .
- **Quota di core**: potrebbe essere necessario aumentare la quota di core nella sottoscrizione di Azure rispetto al valore predefinito. La sottoscrizione può anche limitare il numero di core che è possibile distribuire in alcune famiglie di dimensioni di macchina virtuale, inclusa la serie H. Per richiedere un aumento della quota, apri una richiesta di [assistenza clienti online](../azure-supportability/how-to-create-azure-support-request.md) gratuitamente. I limiti predefiniti possono variare in base alla categoria della sottoscrizione.

  > [!NOTE]
  > Se si hanno esigenze di capacità su larga scala, contattare il supporto di Azure. Le quote di Azure sono limiti di credito e non garanzie di capacità. A prescindere dalla quota, viene addebitato solo l'uso dei core effettivamente impiegati.
  
- **Rete virtuale**: non è necessaria una [rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/) di Azure per usare le istanze a elevato utilizzo di calcolo. Per molte distribuzioni è tuttavia necessaria almeno una rete virtuale di Azure basata sul cloud. Per l'accesso alle risorse locali, è necessaria anche una connessione da sito a sito. Quando è necessaria, creare una nuova rete virtuale per distribuire le istanze. L'aggiunta di una VM a elevato uso di calcolo a una rete virtuale in un gruppo di affinità non è supportata.
- **Ridimensionamento**: a causa dell'hardware specializzato, è possibile ridimensionare solo le istanze a elevato uso di calcolo nella stessa famiglia di dimensioni (serie H o serie A a elevato uso di calcolo). Ad esempio, è possibile ridimensionare una VM della serie H solo da una dimensione della serie H a un'altra. Inoltre, non è supportato il ridimensionamento da una dimensione non a elevato uso di calcolo.  

> [!NOTE]
> Le macchine virtuali A8 – A11 sono previste per il ritiro il 3/2021. Per ulteriori informazioni, consultate Guida alla [migrazione HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

## <a name="rdma-capable-instances"></a>Istanze con supporto per RDMA

Un sottoinsieme delle istanze ad alta intensità di calcolo (A8, A9, H16r, H16mr, HB e HC) dispongono di un'interfaccia di rete per la connettività RDMA (Remote Direct Memory Access). Anche le dimensioni selezionate della serie N designate con 'r' come le configurazioni NC24rs (NC24rs_v2 e NC24rs_v3) sono compatibili con RDMA. Questa interfaccia è un'aggiunta all'interfaccia di rete di Azure standard disponibile per gli altri formati di VM.

Questa interfaccia consente alle istanze che supportano RDMA di comunicare tramite una rete IB (InfiniBand), operando alle velocità EDR per le macchine virtuali HB, HC, FDR per H16r, H16mr e RDMA e le velocità QDR per le macchine virtuali A8 e A9. Queste funzionalità RDMA possono migliorare la scalabilità e le prestazioni di determinate applicazioni di interfaccia MPI (Message Passing Interface). Per ulteriori informazioni sulla velocità, vedere i dettagli nelle tabelle in questa pagina.

> [!NOTE]
> In Azure, IP su IB è supportato solo nelle macchine virtuali abilitate per SR-IOV (SR-IOV per InfiniBand, attualmente HB e HC). RDMA su IB è supportato per tutte le istanze che supportano RDMA.
- **Sistema operativo** - Windows Server 2016 su tutte le macchine virtuali della serie HPC di cui sopra. Windows Server 2012 R2, Windows Server 2012 sono supportati anche nelle macchine virtuali non abilitate per SR-IOV (ad esempio, HB e HC).

- **MPI** - Le dimensioni delle macchine virtuali abilitate per SR-IOV in Azure (HB, HC) consentono di usare quasi tutte le dimensioni di MPI con Mellanox OFED.
Nelle macchine virtuali non abilitate per SR-IOV, le implementazioni MPI supportate usano l'interfaccia Microsoft Network Direct (ND) per comunicare tra le istanze. Di conseguenza, sono supportate solo le versioni Microsoft MPI (MS-MPI) 2012 R2 o versioni successive e Intel MPI 5.x. Versioni successive (2017, 2018) della libreria di runtime Intel MPI possono o non possono essere compatibili con i driver RDMA di Azure.

- **Estensione VM InfiniBandDriverWindows:** nelle macchine virtuali che supportano RDMA, aggiungere l'estensione InfiniBandDriverWindows per abilitare InfiniBand. Questa estensione di Windows VM installa i driver Di rete Diretta di Windows (in macchine virtuali non SR-IOV) o i driver OFED Mellanox (nelle macchine virtuali SR-IOV) per la connettività RDMA.
In alcune distribuzioni di istanze A8 e A9, l'estensione HpcVmDrivers viene aggiunta automaticamente. Si noti che l'estensione della macchina virtuale HpcVmDrivers è deprecata. non verrà aggiornato. Per aggiungere l'estensione macchina virtuale a una macchina virtuale, è possibile usare i cmdlet di [Azure PowerShell](/powershell/azure/overview). 

  Il comando seguente installa l'estensione InfiniBandDriverWindows della versione 1.0 più recente in una macchina virtuale con supporto RDMA esistente denominata myVM distribuita nel gruppo di risorse denominato myResourceGroup nell'area *Stati Uniti occidentali:The* following command installs the latest version 1.0 InfiniBandDriverWindows extension on an existing RDMA-capable VM named *myVM* deployed in the resource group named *myResourceGroup* in the West US region:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```

  In alternativa, le estensioni della macchina virtuale possono essere incluse nei modelli di Azure Resource Manager per semplificare la distribuzione, con il seguente elemento JSON:

  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  Il comando seguente installa l'estensione InfiniBandDriverWindows della versione 1.0 più recente in tutte le macchine virtuali che supportano RDMA in un set di scalabilità di macchine virtuali esistente denominato *myVMSS* distribuito nel gruppo di risorse denominato *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  Per altre informazioni, vedere [Estensioni e funzionalità della macchina virtuale](./extensions/overview.md). È anche possibile usare estensioni delle macchine virtuali nel [modello di distribuzione classico](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

- **Spazio degli indirizzi della rete RDMA** : la rete RDMA in Azure riserva lo spazio degli indirizzi 172.16.0.0/16. Per eseguire applicazioni MPI in istanze distribuite in una rete virtuale di Azure, assicurarsi che lo spazio degli indirizzi di rete virtuale non si sovrapponga alla rete RDMA.

## <a name="cluster-configuration-options"></a>Opzioni di configurazione del cluster

Azure offre varie opzioni per la creazione di cluster di macchine virtuali HPC Windows in grado di comunicare con la rete RDMA, tra cui: 

- **Macchine virtuali**: distribuire le macchine virtuali HPC con supporto per RDMA nello stesso set di disponibilità (se si usa il modello di distribuzione Azure Resource Manager). Se si usa il modello di distribuzione classico, distribuire le macchine virtuali nello stesso servizio cloud. 

- Set di scalabilità di **macchine virtuali:** in un set di scalabilità di macchine virtuali assicurarsi di limitare la distribuzione a un singolo gruppo di posizionamento. In un modello di Resource Manager, ad esempio, impostare la proprietà `singlePlacementGroup` su `true`. 

- **MPI tra macchine virtuali:** se è necessaria la comunicazione MPI tra macchine virtuali (VM), assicurarsi che le macchine virtuali si trovino nello stesso set di disponibilità o nello stesso set di scalabilità della macchina virtuale.

- **Azure CycleCloud**: creare un cluster HPC in [Azure CycleCloud](/azure/cyclecloud/) per eseguire i processi MPI nei nodi Windows.

- **Azure Batch**: creare un pool di [Azure Batch](/azure/batch/) per eseguire i carichi di lavoro MPI nei nodi di calcolo di Windows Server. Per altre informazioni, vedere [Usare istanze con supporto per RDMA o abilitate per GPU in pool di Batch](../batch/batch-pool-compute-intensive-sizes.md). Vedere anche il progetto [Batch Shipyard](https://github.com/Azure/batch-shipyard) per l'esecuzione di carichi di lavoro basati su contenitore in Batch.

- **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) include un ambiente di runtime per MS-MPI che utilizza la rete RDMA di Azure quando viene distribuito in macchine virtuali Linux con funzionalità RDMA. Per distribuzioni di esempio, vedere [Configurare un cluster RDMA Linux con HPC Pack per l'esecuzione](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam)di applicazioni MPI .

## <a name="other-sizes"></a>Altre dimensioni

- [Finalità generale](sizes-general.md)
- [Ottimizzate per il calcolo](sizes-compute.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

- Per gli elenchi di controllo per l'utilizzo delle istanze ad alta intensità di calcolo con HPC Pack in Windows Server, vedere [Configurare un cluster RDMA Linux con HPC Pack per l'esecuzione](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam)di applicazioni MPI .

- Per usare istanze a elevato uso di calcolo quando si eseguono applicazioni MPI con Azure Batch, vedere [Usare le attività a istanze multiple per eseguire applicazioni MPI (Message Passing Interface) in Azure Batch](../batch/batch-mpi.md).

- Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.