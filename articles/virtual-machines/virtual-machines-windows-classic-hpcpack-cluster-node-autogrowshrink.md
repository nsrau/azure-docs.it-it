<properties
 pageTitle="Scalabilità automatica delle risorse di calcolo in un cluster HPC | Microsoft Azure"
 description="Aumentare e ridurre automaticamente il numero di nodi di calcolo del cluster HPC Pack in Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="04/14/2016"
 ms.author="danlep"/>

# Aumentare e ridurre automaticamente le risorse del cluster HPC Pack in Azure in base al carico di lavoro del cluster




Se si distribuiscono nodi "burst" di Azure nel cluster HPC Pack o si crea un cluster HPC Pack nelle macchine virtuali di Azure, può essere necessario avere a disposizione un modo per aumentare o ridurre automaticamente i numero di risorse di calcolo di Azure, ad esempio i core, in base al carico di lavoro corrente del cluster. In questo modo è possibile usare le risorse di Azure in modo più efficiente e controllare i costi. A tale scopo, impostare la proprietà del cluster HPC Pack **AutoGrowShrink**. In alternativa, eseguire lo script di HPC PowerShell **AzureAutoGrowShrink.ps1** installato con HPC Pack.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse. Inoltre, attualmente è possibile solo aumentare e ridurre automaticamente i nodi di calcolo HPC Pack che eseguono un sistema operativo Windows Server.

## Impostare la proprietà del cluster AutoGrowShrink

### Prerequisiti

* **Cluster HPC Pack 2012 R2 Update 2 o versione successiva**: il nodo head del cluster può essere distribuito in locale o in una macchina virtuale di Azure. Vedere [Configurare un cluster ibrido con HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) per iniziare con un nodo head locale e i nodi "burst" di Azure. Vedere lo [script di distribuzione IaaS di HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) per distribuire velocemente un cluster HPC Pack in macchine virtuali di Azure.


* **Per un cluster con un nodo head in Azure** se usa lo script di distribuzione IaaS di HPC Pack per creare il cluster, abilitare la proprietà del cluster **AutoGrowShrink** impostando l'opzione AutoGrowShrink nel file di configurazione del cluster. Per informazioni dettagliate, vedere la documentazione che accompagna il [download dello script](https://www.microsoft.com/download/details.aspx?id=44949).

    In alternativa, impostare la proprietà del cluster **AutoGrowShrink** dopo aver distribuito il cluster con i comandi di HPC PowerShell descritti nella sezione seguente. Per usare HPC PowerShell per eseguire questa operazione, è necessario completare questa procedura:
    1. Configurare un certificato di gestione di Azure nel nodo head e nella sottoscrizione di Azure. Per una distribuzione di test è possibile usare il certificato autofirmato Default Microsoft HPC Azure installato da HPC Pack nel nodo head. È quindi sufficiente caricare il certificato nella sottoscrizione di Azure. Per le opzioni e i passaggi, vedere le [informazioni aggiuntive nella libreria TechNet](https://technet.microsoft.com/library/gg481759.aspx).
    2. Eseguire **regedit** nel nodo head, passare a HKLM\\SOFTWARE\\Micorsoft\\HPC\\IaasInfo e aggiungere un nuovo valore stringa. Impostare il nome valore su "Identificazione personale" e dati valore sull'identificazione personale del certificato nel passaggio 1.


### Comandi di HPC PowerShell per impostare la proprietà AutoGrowShrink

Di seguito sono riportati i comandi di HPC PowerShell di esempio per impostare **AutoGrowShrink** e ottimizzare il comportamento con parametri aggiuntivi. Vedere [Parametri di AutoGrowShrink ](#AutoGrowShrink-parameters) più avanti in questo articolo per l'elenco completo delle impostazioni.

Per eseguire questi comandi, avviare HPC PowerShell nel nodo head del cluster come amministratore.

**Per abilitare la proprietà AutoGrowShrink**

    Set-HpcClusterProperty –EnableGrowShrink 1

**Per disabilitare la proprietà AutoGrowShrink**

    Set-HpcClusterProperty –EnableGrowShrink 0

**Per modificare l'intervallo di aumento in minuti**

    Set-HpcClusterProperty –GrowInterval <interval>

**Per modificare l'intervallo di riduzione in minuti**

    Set-HpcClusterProperty –ShrinkInterval <interval>

**Per visualizzare la configurazione corrente di AutoGrowShrink**

    Get-HpcClusterProperty –AutoGrowShrink

### Parametri di AutoGrowShrink

Di seguito sono riportati i parametri di AutoGrowShrink che è possibile modificare tramite il comando **Set HpcClusterProperty**.

* **EnableGrowShrink**: opzione per abilitare o disabilitare la proprietà **AutoGrowShrink** .
* **ParamSweepTasksPerCore**: numero di attività di sweep parametrico per l'aumento di un core. Il valore predefinito è l'aumento di un core per ogni attività. 
 
    >[AZURE.NOTE] HPC Pack QFE KB3134307 modifica **ParamSweepTasksPerCore** in **TasksPerResourceUnit**. Si basa sul tipo di risorsa del processo e può essere un nodo, un socket o un core.
    
* **GrowThreshold**: soglia di attività in coda per attivare l'aumento automatico. Il valore predefinito è 1 e significa che se sono presenti una o più attività in coda, i nodi vengono aumentati automaticamente.
* **GrowInterval**: intervallo in minuti per attivare l'aumento automatico. L'intervallo predefinito è 5 minuti.
* **ShrinkInterval**: intervallo in minuti per attivare la riduzione automatica. L'intervallo predefinito è 5 minuti.|
* **ShrinkIdleTimes**: numero di controlli continui da ridurre per indicare i nodi sono inattivi. Il valore predefinito è 3 volte. Ad esempio, se **ShrinkInterval** è di 5 minuti, HPC Pack controlla se il nodo è inattivo ogni 5 minuti. Se i nodi sono in uno stato di inattività dopo 3 controlli continui (15 minuti), HPC Pack riduce quel nodo.
* **ExtraNodesGrowRatio** -percentuale aggiuntiva di nodi da aumentare per i processi MPI (Message Passing Interface). Il valore predefinito è 1 e significa che HPC Pack aumenta l'1% dei nodi per i processi MPI. 
* **GrowByMin**: opzione che indica se i criteri di aumento automatico sono basati sulle risorse minime necessarie per il processo. Il valore predefinito è false e significa che HPC Pack aumenta i nodi per i processi in base alle risorse massime richieste per i processi.
* **SoaJobGrowThreshold**: soglia di richieste SOA in ingresso per attivare il processo di aumento automatico. Il valore predefinito è 50000.  
    
    >[AZURE.NOTE] Questo parametro è supportato a partire da HPC Pack 2012 R2 Update 3.
    
* **SoaRequestsPerCore**: numero di richieste SOA in ingresso per l'aumento di un core. The default value is 20000.

    >[AZURE.NOTE] Questo parametro è supportato a partire da HPC Pack 2012 R2 Update 3.

### Esempio MPI

Per impostazione predefinita HPC Pack aumenta di 1% i nodi aggiuntivi per i processi MPI. **ExtraNodesGrowRatio** è impostato su 1. Il motivo è che MPI può richiedere più nodi e il processo può essere eseguito solo quando tutti i nodi sono pronti. Quando Azure avvia i nodi, in alcuni casi l'avvio di un nodo può richiedere più tempo rispetto ad altri, causando l'inattività degli altri nodi in attesa che quel nodo sia pronto. Aumentando i nodi supplementari, HPC Pack riduce il tempo di attesa delle risorse, riducendo potenzialmente i costi. Per aumentare la percentuale di nodi aggiuntivi per i processi MPI, ad esempio del 10%, eseguire un comando simile a

    Set-HpcClusterProperty -ExtraNodesGrowRatio 10

### Esempio SOA

Per impostazione predefinita, **SoaJobGrowThreshold** è impostata su 50000 e **SoaRequestsPerCore** è impostato su 200000. Se si invia un processo SOA con 70000 richieste, ci sarà una sola attività in coda e le richieste in ingresso saranno 70000. In questo caso HPC Pack aumenta 1 core per l'attività in coda e per le richieste in ingresso aumenterà (70000 - 50000)/20000 = 1 core, in modo da aumentare in totale 2 core per questo processo SOA.

## Eseguire lo script AzureAutoGrowShrink.ps1

### Prerequisiti

* **Cluster HPC Pack 2012 R2 Update 1 o versione successiva** - Script **AzureAutoGrowShrink.ps1** installato nella cartella %CCP\_HOME%bin. Il nodo head del cluster può essere distribuito in locale o in una macchina virtuale di Azure. Vedere [Configurare un cluster ibrido con HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) per iniziare con un nodo head locale e i nodi "burst" di Azure. Vedere lo [script di distribuzione IaaS di HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) per distribuire velocemente un cluster HPC Pack in macchine virtuali di Azure o usare un [modello di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/).

* **Azure PowerShell 0.8.12**: lo script attualmente dipende da questa versione specifica di Azure PowerShell. Se si esegue una versione successiva nel nodo head, potrebbe essere necessario effettuare il downgrade di Azure PowerShell alla [versione 0.8.12](http://az412849.vo.msecnd.net/downloads03/azure-powershell.0.8.12.msi) per eseguire lo script.

* **Per un cluster con nodi burst di Azure** - Eseguire lo script in un computer client in cui è installato HPC Pack o nel nodo head. In caso di esecuzione in un computer client, assicurarsi di impostare la variabile $env:CCP\_SCHEDULER correttamente in modo che punti al nodo head. I nodi "burst" di Azure devono essere già stati aggiunti al cluster, ma possono essere nello stato Non distribuito.


* **Per un cluster distribuito in macchine virtuali di Azure** - Eseguire lo script nella macchina virtuale del nodo head, perché dipende dagli script **Start-HpcIaaSNode.ps1** e **Stop-HpcIaaSNode.ps1** installati in tale posizione. Per questi script è inoltre necessario un certificato di gestione di Azure o un file delle impostazioni di pubblicazione (vedere [Gestire i nodi di calcolo in un cluster HPC Pack in Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)). Assicurarsi che tutte le macchine virtuali del nodo di calcolo necessarie siano già aggiunte al cluster. Potrebbero essere in stato arrestato.

### Sintassi

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
### Parametri

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

* **ShrinkCheckIdleTimes**: numero di controlli continui della riduzione, separati da **ShrinkCheckIntervalMins**, per indicare che i nodi sono inattivi.

* **UseLastConfigurations**: configurazioni precedenti salvate nel file di argomenti.

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

<!---HONumber=AcomDC_0420_2016-->