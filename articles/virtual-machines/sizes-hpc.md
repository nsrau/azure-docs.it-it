---
title: Dimensioni delle macchine virtuali di Azure-HPC | Microsoft Docs
description: Elenca le diverse dimensioni disponibili per High Performance Computing macchine virtuali in Azure. Elenca informazioni sul numero di vCPU, dei dischi dati e delle schede di rete, nonché sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per le dimensioni di queste serie.
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
ms.openlocfilehash: dc2086223dea9bff311aac9e7d4771b5273f0e91
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493553"
---
# <a name="high-performance-compute-vm-sizes"></a>Dimensioni delle VM High Performance Computing (HPC)

Le macchine virtuali (VM) della serie H di Azure sono progettate per offrire prestazioni di classe leadership, scalabilità MPI ed efficienza dei costi per un'ampia gamma di carichi di lavoro HPC reali.

[Serie HBv2](hbv2-series.md) Funzionalità VM 200 GB/sec Mellanox HDR InfiniBand, mentre le VM serie HB e HC includono 100 GB/sec Mellanox EDR InfiniBand. Ognuno di questi tipi di VM è connesso in un albero Fat senza blocco per prestazioni RDMA ottimizzate e coerenti. Le macchine virtuali HBv2 supportano il routing adattivo e il trasporto con connessione dinamica (DCT, in aggiunta a trasporti standard RC e UD). Queste funzionalità migliorano le prestazioni, la scalabilità e la coerenza delle applicazioni e l'utilizzo di tali funzionalità è fortemente consigliato.

[Serie HB](hb-series.md) Le macchine virtuali sono ottimizzate per le applicazioni basate sulla larghezza di banda della memoria, ad esempio fluidodinamica, analisi degli elementi finiti espliciti e modellazione meteorologica. Macchine virtuali HB funzionalità 60 processori AMD EPYC 7551 core, 4 GB di RAM per core CPU e senza hyperthreading. La piattaforma EPYC AMD offre più di 260 GB al secondo di larghezza di banda di memoria.

[Serie HC](hc-series.md) Le macchine virtuali sono ottimizzate per le applicazioni basate su un calcolo denso, ad esempio l'analisi implicita degli elementi finiti, le dinamiche molecolari e la chimica computazionale. Macchine virtuali HC funzionalità 44 Core processore Intel Xeon Platinum 8168, 8 GB di RAM per core CPU e nessun hyperthreading. La piattaforma Intel Xeon Platinum supporta il ricco ecosistema di strumenti software di Intel, ad esempio la libreria del kernel matematico di Intel.

[Serie H](h-series.md) Le macchine virtuali sono ottimizzate per le applicazioni basate su frequenze di CPU elevate o memoria di grandi dimensioni per i requisiti di base. Le macchine virtuali serie H dispongono di core del processore Intel Xeon E5 2667 V3, 7 o 14 GB di RAM per core CPU e senza hyperthreading. Le funzionalità della serie H 56 GB/sec Mellanox FDR InfiniBand in una configurazione di albero FAT non bloccante per prestazioni RDMA coerenti. Le macchine virtuali serie H supportano Intel MPI 5. x e MS-MPI.

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

- **Sottoscrizione di Azure**: per distribuire numerose istanze a elevato utilizzo di calcolo, prendere in considerazione una sottoscrizione con pagamento in base al consumo o altre opzioni di acquisto. Con un [account gratuito di Azure](https://azure.microsoft.com/free/)è possibile usare solo un numero limitato di core di calcolo di Azure.

- **Prezzi e disponibilità**: queste dimensioni di VM sono offerte solo con il piano tariffario Standard. Per informazioni sulla disponibilità nelle aree di Azure, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/) .
- **Quota di core**: potrebbe essere necessario aumentare la quota di core nella sottoscrizione di Azure rispetto al valore predefinito. La sottoscrizione può anche limitare il numero di core che è possibile distribuire in alcune famiglie di dimensioni di macchina virtuale, inclusa la serie H. Per richiedere un aumento della quota, è possibile [aprire una richiesta di assistenza clienti online](../azure-supportability/how-to-create-azure-support-request.md) senza alcun addebito. I limiti predefiniti possono variare in base alla categoria della sottoscrizione.

  > [!NOTE]
  > Se si hanno esigenze di capacità su larga scala, contattare il supporto di Azure. Le quote di Azure sono limiti di credito e non garanzie di capacità. A prescindere dalla quota, viene addebitato solo l'uso dei core effettivamente impiegati.
  
- **Rete virtuale**: non è necessaria una [rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/) di Azure per usare le istanze a elevato utilizzo di calcolo. Per molte distribuzioni è tuttavia necessaria almeno una rete virtuale di Azure basata sul cloud. Per l'accesso alle risorse locali, è necessaria anche una connessione da sito a sito. Quando è necessaria, creare una nuova rete virtuale per distribuire le istanze. L'aggiunta di una VM a elevato uso di calcolo a una rete virtuale in un gruppo di affinità non è supportata.
- **Ridimensionamento**: a causa dell'hardware specializzato, è possibile ridimensionare solo le istanze a elevato uso di calcolo nella stessa famiglia di dimensioni (serie H o serie A a elevato uso di calcolo). Ad esempio, è possibile ridimensionare una VM della serie H solo da una dimensione della serie H a un'altra. Inoltre, non è supportato il ridimensionamento da una dimensione non a elevato uso di calcolo.  
## <a name="rdma-capable-instances"></a>Istanze con supporto per RDMA

Un subset delle istanze a elevato utilizzo di calcolo (A8, A9, H16r, H16mr, HB e HC) è caratterizzato da un'interfaccia di rete per la connettività di accesso diretto a memoria remota (RDMA). Anche le dimensioni selezionate della serie N designate con ' r ', ad esempio le configurazioni NC24rs (NC24rs_v2 e NC24rs_v3), sono compatibili con RDMA. Questa interfaccia è un'aggiunta all'interfaccia di rete di Azure standard disponibile per gli altri formati di VM.

Questa interfaccia consente alle istanze con supporto per RDMA di comunicare attraverso una rete InfiniBand (IB), che opera a tariffe EDR per HB, HC, le tariffe FDR per H16r, H16mr e le macchine virtuali serie N con supporto per RDMA e le tariffe QDR per le macchine virtuali a8 e A9. Queste funzionalità RDMA possono migliorare la scalabilità e le prestazioni di determinate applicazioni di interfaccia MPI (Message Passing Interface). Per ulteriori informazioni sulla velocità, vedere i dettagli nelle tabelle in questa pagina.

> [!NOTE]
> In Azure, IP over IB è supportato solo nelle VM abilitate per SR-IOV (SR-IOV per InfiniBand, attualmente HB e HC). RDMA su IB è supportato per tutte le istanze con supporto per RDMA.
- **Sistema operativo** : Windows Server 2016 in tutte le VM serie HPC precedenti. Windows Server 2012 R2, Windows Server 2012 sono supportati anche nelle macchine virtuali abilitate per non SR-IOV, pertanto escludendo HB e HC.

- **MPI** : le dimensioni delle VM abilitate per SR-IOV in Azure (HB, HC) consentono di usare praticamente qualsiasi versione di MPI con Mellanox OFED.
Nelle VM abilitate per non SR-IOV, le implementazioni MPI supportate usano l'interfaccia di Microsoft Network Direct (ND) per la comunicazione tra le istanze. Di conseguenza, sono supportate solo le versioni Microsoft MPI (MS-MPI) 2012 R2 o versioni successive e Intel MPI 5. x. Le versioni successive (2017, 2018) della libreria di runtime Intel MPI possono o non essere compatibili con i driver RDMA di Azure.

- **Estensione della macchina virtuale InfiniBandDriverWindows** : nelle VM con supporto per RDMA aggiungere l'estensione InfiniBandDriverWindows per abilitare InfiniBand. Questa estensione VM Windows installa i driver di rete diretta di Windows (in VM non SR-IOV) o i driver OFED di Mellanox (su VM SR-IOV) per la connettività RDMA.
In alcune distribuzioni di istanze A8 e A9 l'estensione HpcVmDrivers viene aggiunta automaticamente. Si noti che l'estensione della macchina virtuale HpcVmDrivers è deprecata. non verrà aggiornata. Per aggiungere l'estensione macchina virtuale a una macchina virtuale, è possibile usare i cmdlet di [Azure PowerShell](/powershell/azure/overview). 

  Il comando seguente installa la versione più recente dell'estensione InfiniBandDriverWindows 1,0 in una macchina virtuale esistente con supporto per RDMA denominata *myVM* distribuita nel gruppo di risorse denominato *myResourceGroup* nell'area *Stati Uniti occidentali* :

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```

  In alternativa, le estensioni della macchina virtuale possono essere incluse nei modelli di Azure Resource Manager per semplificare la distribuzione, con l'elemento JSON seguente:

  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  Il comando seguente installa la versione più recente dell'estensione InfiniBandDriverWindows 1,0 in tutte le VM con supporto per RDMA in un set di scalabilità di macchine virtuali esistente denominato *myVMSS* distribuito nel gruppo di risorse denominato *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  Per altre informazioni, vedere [Estensioni e funzionalità della macchina virtuale](/extensions/overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). È anche possibile usare estensioni delle macchine virtuali nel [modello di distribuzione classico](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

- **Spazio degli indirizzi della rete RDMA** : la rete RDMA in Azure riserva lo spazio degli indirizzi 172.16.0.0/16. Per eseguire applicazioni MPI in istanze distribuite in una rete virtuale di Azure, assicurarsi che lo spazio degli indirizzi di rete virtuale non si sovrapponga alla rete RDMA.

## <a name="cluster-configuration-options"></a>Opzioni di configurazione del cluster

Azure offre varie opzioni per la creazione di cluster di macchine virtuali HPC Windows in grado di comunicare con la rete RDMA, tra cui: 

- **Macchine virtuali**: distribuire le macchine virtuali HPC con supporto per RDMA nello stesso set di disponibilità (se si usa il modello di distribuzione Azure Resource Manager). Se si usa il modello di distribuzione classico, distribuire le macchine virtuali nello stesso servizio cloud. 

- **Set di scalabilità di macchine virtuali** : in un set di scalabilità di macchine virtuali, assicurarsi di limitare la distribuzione a un singolo gruppo di posizionamento. In un modello di Resource Manager, ad esempio, impostare la proprietà `singlePlacementGroup` su `true`. 

- **MPI tra le macchine virtuali** : se è necessario comunicare MPI tra macchine virtuali (VM), verificare che le macchine virtuali si trovino nello stesso set di disponibilità o nello stesso set di scalabilità.

- **Azure CycleCloud**: creare un cluster HPC in [Azure CycleCloud](/azure/cyclecloud/) per eseguire i processi MPI nei nodi Windows.

- **Azure Batch**: creare un pool di [Azure Batch](/azure/batch/) per eseguire i carichi di lavoro MPI nei nodi di calcolo di Windows Server. Per altre informazioni, vedere [Usare istanze con supporto per RDMA o abilitate per GPU in pool di Batch](../batch/batch-pool-compute-intensive-sizes.md). Vedere anche il progetto [Batch Shipyard](https://github.com/Azure/batch-shipyard) per l'esecuzione di carichi di lavoro basati su contenitore in Batch.

- **Microsoft HPC pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) include un ambiente di runtime per MS-MPI che usa la rete RDMA di Azure in caso di distribuzione in VM Linux con supporto per RDMA. Per le distribuzioni di esempio, vedere [configurare un cluster Linux RDMA con HPC Pack per eseguire applicazioni MPI](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per il calcolo](sizes-compute.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

- Per gli elenchi di controllo per usare le istanze a elevato utilizzo di calcolo con HPC Pack in Windows Server, vedere [configurare un cluster Linux RDMA con HPC Pack per eseguire applicazioni MPI](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

- Per usare istanze a elevato uso di calcolo quando si eseguono applicazioni MPI con Azure Batch, vedere [Usare le attività a istanze multiple per eseguire applicazioni MPI (Message Passing Interface) in Azure Batch](../batch/batch-mpi.md).

- Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.