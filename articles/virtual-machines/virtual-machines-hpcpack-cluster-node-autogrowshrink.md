<properties
 pageTitle="Scalabilità automatica delle risorse di calcolo in un cluster HPC | Microsoft Azure"
 description="Informazioni sui modi per aumentare e ridurre automaticamente le risorse di calcolo in un cluster HPC Pack in Azure"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# Aumentare e ridurre automaticamente le risorse di calcolo di Azure in un cluster HPC Pack in base al carico di lavoro del cluster

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]In questo articolo viene illustrata la gestione di una risorsa creata con il modello di distribuzione classica.

Se si distribuiscono nodi "burst" di Azure nel cluster HPC Pack o si crea un cluster HPC Pack nelle macchine virtuali di Azure, può essere necessario avere a disposizione un modo per aumentare o ridurre automaticamente le risorse di calcolo di Azure in base al carico di lavoro corrente dei processi e delle attività del cluster. In questo modo è possibile usare le risorse di Azure in modo più efficiente e controllare i costi. A tale scopo, usare lo script di PowerShell per HPC **AzureAutoGrowShrink.ps1** installato con HPC Pack.

>[AZURE.TIP]A partire da HPC Pack 2012 R2 Update 2, HPC Pack include un servizio incorporato per aumentare e ridurre automaticamente i nodi burst di Azure o i nodi di calcolo delle macchine virtuali di Azure. È possibile configurare il servizio con un'impostazione nello [script di distribuzione IaaS di HPC](virtual-machines-hpcpack-cluster-powershell-script.md) o impostare manualmente la proprietà del cluster **AutoGrowShrink**. Vedere le [novità di Microsoft HPC Pack 2012 R2 Update 2](https://technet.microsoft.com/library/mt269417.aspx).

## Prerequisiti

* **Cluster HPC Pack 2012 R2 Update 1 o versione successiva** - Script **AzureAutoGrowShrink.ps1** installato nella cartella %CCP\_HOME%bin. Il nodo head del cluster può essere distribuito in locale o in una macchina virtuale di Azure. Vedere [Configurare un cluster ibrido con HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) per iniziare con un nodo head locale e i nodi "burst" di Azure. Vedere lo [script di distribuzione Iaas di HPC Pack](virtual-machines-hpcpack-cluster-powershell-script.md) per distribuire velocemente un cluster HPC Pack in macchine virtuali di Azure.

* **Per un cluster con nodi burst di Azure** - Eseguire lo script in un computer client in cui è installato HPC Pack o nel nodo head. In caso di esecuzione in un computer client, assicurarsi di impostare la variabile $env:CCP\_SCHEDULER correttamente in modo che punti al nodo head. I nodi "burst" di Azure devono essere già stati aggiunti al cluster, ma possono essere nello stato Non distribuito.


* **Per un cluster distribuito in macchine virtuali di Azure** - Eseguire lo script nella macchina virtuale del nodo head, perché dipende dagli script **Start-HpcIaaSNode.ps1** e **Stop-HpcIaaSNode.ps1** installati in tale posizione. Per questi script è inoltre necessario un certificato di gestione di Azure o un file delle impostazioni di pubblicazione (vedere [Gestire i nodi di calcolo in un cluster HPC Pack in Azure](virtual-machines-hpcpack-cluster-node-manage.md)). Accertarsi che tutte le macchine virtuali del nodo di calcolo necessarie siano già state aggiunte al cluster, anche se possono essere nello stato Arrestato.

## Sintassi

```
AzureAutoGrowShrink.ps1
[[-NodeTemplates] <String[]>] [[-JobTemplates] <String[]>] [[-NodeType] <String>]
[[-NumOfQueuedJobsPerNodeToGrow] <Int32>]
[[-NumOfQueuedJobsToGrowThreshold] <Int32>]
[[-NumOfActiveQueuedTasksPerNodeToGrow] <Int32>]
[[-NumOfActiveQueuedTasksToGrowThreshold] <Int32>]
[[-NumOfInitialNodesToGrow] <Int32>] [[-GrowCheckIntervalMins] <Int32>]
[[-ShrinkCheckIntervalMins] <Int32>] [[-ShrinkCheckIdleTimes] <Int32>]
[-UseLastConfigurations] [[-ArgFile] <String>] [[-LogFilePrefix] <String>]
[<CommonParameters>]

```
## Parametri

 * **NodeTemplates** - Nomi dei modelli di nodo per definire l'ambito per i nodi da ingrandire o ridurre. Se non specificato (il valore predefinito è @()), tutti i nodi nel gruppo di nodi **AzureNodes** rientrano nell'ambito quando il valore di **NodeType** è AzureNodes e tutti i nodi nel gruppo di nodi **ComputeNodes** rientrano nell'ambito quando il valore di **NodeType** è valore ComputeNodes.

 * **JobTemplates** - Nomi dei modelli di processo per definire l'ambito per i nodi da ingrandire.

 * **NodeType** - Il tipo di nodo da ingrandire o ridurre. I valori supportati sono:

     * **AzureNodes** - Per i nodi di Azure PaaS (burst) in un cluster locale o IaaS di Azure.

     * **ComputeNodes** - Solo per le macchine virtuali dei nodi di calcolo in un cluster IaaS di Azure.

* **NumOfQueuedJobsPerNodeToGrow** - Numero di processi in coda richiesti per ingrandire un nodo.

* **NumOfQueuedJobsToGrowThreshold** - Numero di processi in coda di soglia per avviare il processo di ingrandimento.

* **NumOfActiveQueuedTasksPerNodeToGrow** - Numero di attività in coda attive richieste per ingrandire un nodo. Se si specifica un valore maggiore di 0 per **NumOfQueuedJobsPerNodeToGrow**, questo parametro viene ignorato.

* **NumOfActiveQueuedTasksToGrowThreshold** - Numero di attività in coda attive di soglia per avviare il processo di ingrandimento.

* **NumOfInitialNodesToGrow** - Numero minimo iniziale dei nodi, da ingrandire se lo stato di tutti i nodi nell'ambito è **Non distribuito** o **Arrestato (deallocato)**.

* **GrowCheckIntervalMins** - Intervallo in minuti tra i controlli dell'ingrandimento.

* **ShrinkCheckIntervalMins** - Intervallo in minuti tra i controlli della riduzione.

* **ShrinkCheckIdleTimes** - Numero di controlli della riduzione continui (separati da **ShrinkCheckIntervalMins**) per indicare che i nodi sono inattivi.

* **UseLastConfigurations*** Le configurazioni precedenti salvate nel file di argomenti.

* **ArgFile** - Nome del file di argomenti usato per salvare e aggiornare le configurazioni per eseguire lo script.

* **LogFilePrefix**- Prefisso del nome del file di log. È possibile specificare un percorso. Per impostazione predefinita il log viene scritto nella directory di lavoro corrente.

### Esempio 1

Nell'esempio seguente, i nodi burst di Azure distribuiti con il modello Default AzureNode Template vengono configurati per l'ingrandimento e la riduzione automatici. Se tutti i nodi sono inizialmente nello stato **Non distribuito** vengono avviati almeno 3 nodi. Se il numero di processi in coda è maggiore di 8, lo script avvia i nodi fino a quando il numero non supera il rapporto tra processi in coda e **NumOfQueuedJobsPerNodeToGrow**. Se viene trovato un nodo inattivo in 3 tempi di inattività consecutivi, il nodo viene arrestato.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### Esempio 2

Nell'esempio seguente, le macchine virtuali dei nodi di calcolo di Azure distribuite con il modello Default ComputeNode Template vengono configurate per l'ingrandimento e la riduzione automatici. I processi configurati dal modello di processo predefinito definiscono l'ambito del carico di lavoro nel cluster. Se tutti i nodi sono inizialmente arrestati, vengono avviati almeno 5 nodi. Se il numero delle attività in coda attive è maggiore di 15, lo script avvia i nodi fino a quando il numero non supera il rapporto tra le attività in coda attive e **NumOfActiveQueuedTasksPerNodeToGrow**. Se viene trovato un nodo inattivo in 10 tempi di inattività consecutivi, il nodo viene arrestato.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```

<!---HONumber=Oct15_HO1-->