---
title: "Scalabilità automatica dei nodi di calcolo del cluster HPC Pack | Microsoft Docs"
description: Aumentare e ridurre automaticamente il numero di nodi di calcolo del cluster HPC Pack in Azure
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: 
editor: tysonn
ms.assetid: 38762cd1-f917-464c-ae5d-b02b1eb21e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 12/08/2016
ms.author: danlep
ms.openlocfilehash: 0c8a5aacd19d83b26cfeb3750d57dd783687f1c4
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2017
---
# <a name="automatically-grow-and-shrink-the-hpc-pack-cluster-resources-in-azure-according-to-the-cluster-workload"></a>Aumentare e ridurre automaticamente le risorse del cluster HPC Pack in Azure in base al carico di lavoro del cluster
Se si distribuiscono nodi "burst" di Azure nel cluster HPC Pack o si crea un cluster HPC Pack nelle macchine virtuali di Azure, può essere necessario avere a disposizione un modo per aumentare o ridurre automaticamente le risorse del cluster di Azure, ad esempio i nodi o core, in base al carico di lavoro del cluster. Ridimensionando le risorse del cluster in questo modo, è possibile usare le risorse di Azure in modo più efficiente e controllare i costi.

Questo articolo illustra due modalità offerte da HPC Pack per la scalabilità automatica delle risorse di calcolo:

* Proprietà del cluster HPC Pack **AutoGrowShrink**

* Script di HPC PowerShell **AzureAutoGrowShrink.ps1**

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Attualmente è possibile solo aumentare e ridurre automaticamente i nodi di calcolo HPC Pack che eseguono un sistema operativo Windows Server.


## <a name="set-the-autogrowshrink-cluster-property"></a>Impostare la proprietà del cluster AutoGrowShrink
### <a name="prerequisites"></a>prerequisiti

* **Cluster HPC Pack 2012 R2 Update 2 o versione successiva** : il nodo head del cluster può essere distribuito in locale o in una macchina virtuale di Azure. Vedere [Configurare un cluster ibrido con HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) per iniziare con un nodo head locale e i nodi "burst" di Azure. Vedere lo [script di distribuzione IaaS di HPC Pack](hpcpack-cluster-powershell-script.md) per distribuire velocemente un cluster HPC Pack in macchine virtuali di Azure.

* **Per un cluster con un nodo head in Azure (modello di distribuzione di Resource Manager)**: a partire da HPC Pack 2016, l'autenticazione del certificato in un'applicazione Azure Active Directory viene usata per aumentare o ridurre automaticamente le macchine virtuali del cluster distribuite tramite Azure Resource Manager. Configurare un certificato come segue:

  1. Dopo la distribuzione del cluster, connettersi a un solo nodo head da desktop remoto.

  2. Caricare il certificato (formato PFX con chiave privata) su ogni nodo head e installarlo in Cert:\LocalMachine\My and Cert:\LocalMachine\Root.

  3. Avviare Azure PowerShell come amministratore ed eseguire i comandi seguenti in un nodo head:

    ```powershell
        cd $env:CCP_HOME\bin

        Login-AzureRmAccount
    ```
        
    Se l'account si trova in più di un tenant di Azure Active Directory o in più sottoscrizioni di Azure, è possibile eseguire il comando seguente per selezionare il tenant e la sottoscrizione corretti:
  
    ```powershell
        Login-AzureRMAccount -TenantId <TenantId> -SubscriptionId <subscriptionId>
    ```     
       
    Eseguire questo comando per visualizzare il tenant e la sottoscrizione attualmente selezionati:
    
    ```powershell
        Get-AzureRMContext
    ```

  4. Eseguire lo script seguente

    ```powershell
        .\ConfigARMAutoGrowShrinkCert.ps1 -DisplayName “YourHpcPackAppName” -HomePage "https://YourHpcPackAppHomePage" -IdentifierUri "https://YourHpcPackAppUri" -CertificateThumbprint "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" -TenantId xxxxxxxx-xxxxx-xxxxx-xxxxx-xxxxxxxxxxxx
    ```

    dove

    **DisplayName**: nome visualizzato dell'applicazione Azure Active. Se l'applicazione non esiste, viene creata in Azure Active Directory.

    **Home page**: home page dell'applicazione. È possibile configurare un URL fittizio, come nell'esempio precedente.

    **IdentifierUri**: identificatore dell'applicazione. È possibile configurare un URL fittizio, come nell'esempio precedente.

    **CertificateThumbprint**: identificazione personale del certificato installato nel nodo head nel passaggio 1.

    **TenantId**: ID tenant di Azure Active Directory. È possibile ottenere l'ID tenant dalla pagina **Proprietà** del portale di Azure Active Directory.

    Per altre informazioni su **ConfigARMAutoGrowShrinkCert.ps1**, eseguire `Get-Help .\ConfigARMAutoGrowShrinkCert.ps1 -Detailed`.


* **Per un cluster con un nodo head in Azure (modello di distribuzione classica)** se usa lo script di distribuzione IaaS di HPC Pack per creare il cluster nel modello di distribuzione classica, abilitare la proprietà del cluster **AutoGrowShrink** impostando l'opzione AutoGrowShrink nel file di configurazione del cluster. Per informazioni dettagliate, vedere la documentazione che accompagna il [download dello script](https://www.microsoft.com/download/details.aspx?id=44949).

    In alternativa, abilitare la proprietà del cluster **AutoGrowShrink** dopo aver distribuito il cluster con i comandi di HPC PowerShell descritti nella sezione seguente. Per prepararsi, completare prima i passaggi seguenti:

  1. Configurare un certificato di gestione di Azure nel nodo head e nella sottoscrizione di Azure. Per una distribuzione di prova, è possibile usare il certificato autofirmato Microsoft HPC Azure predefinito che consente di installare HPC Pack nel nodo head e quindi caricare il certificato nella sottoscrizione di Azure. Per le opzioni e i passaggi, vedere le [informazioni aggiuntive nella libreria TechNet](https://technet.microsoft.com/library/gg481759.aspx).

  2. Eseguire **regedit** nel nodo head, passare a HKLM\SOFTWARE\Micorsoft\HPC\IaasInfo e aggiungere un valore stringa. Impostare il nome valore su "Identificazione personale" e dati valore sull'identificazione personale del certificato nel passaggio 1.

### <a name="hpc-powershell-commands-to-set-the-autogrowshrink-property"></a>Comandi di HPC PowerShell per impostare la proprietà AutoGrowShrink
Di seguito sono riportati i comandi di HPC PowerShell di esempio per impostare **AutoGrowShrink** e ottimizzare il comportamento con parametri aggiuntivi. Vedere [Parametri di AutoGrowShrink](#AutoGrowShrink-parameters) più avanti in questo articolo per l'elenco completo delle impostazioni.

Per eseguire questi comandi, avviare HPC PowerShell nel nodo head del cluster come amministratore.

**Per abilitare la proprietà AutoGrowShrink**

```powershell
Set-HpcClusterProperty –EnableGrowShrink 1
```

**Per disabilitare la proprietà AutoGrowShrink**

```powershell
Set-HpcClusterProperty –EnableGrowShrink 0
```

**Per modificare l'intervallo di aumento in minuti**

```powershell
Set-HpcClusterProperty –GrowInterval <interval>
```

**Per modificare l'intervallo di riduzione in minuti**

```powershell
Set-HpcClusterProperty –ShrinkInterval <interval>
```

**Per visualizzare la configurazione corrente di AutoGrowShrink**

```powershell
Get-HpcClusterProperty –AutoGrowShrink
```

**Per escludere i gruppi di nodi da AutoGrowShrink**

```powershell
Set-HpcClusterProperty –ExcludeNodeGroups <group1,group2,group3>
```

>[!NOTE] 
> Questo parametro è supportato a partire da HPC Pack 2016
>

### <a name="autogrowshrink-parameters"></a>Parametri di AutoGrowShrink 
Di seguito sono riportati i parametri di AutoGrowShrink che è possibile modificare tramite il comando **Set HpcClusterProperty** .

* **EnableGrowShrink**: opzione per abilitare o disabilitare la proprietà **AutoGrowShrink**.
* **ParamSweepTasksPerCore** : numero di attività di sweep parametrico per l'aumento di un core. Il valore predefinito è l'aumento di un core per ogni attività.

  > [!NOTE]
  > HPC Pack QFE KB3134307 modifica **ParamSweepTasksPerCore** in **TasksPerResourceUnit**. Si basa sul tipo di risorsa del processo e può essere un nodo, un socket o un core.
  >
  >
* **GrowThreshold** : soglia di attività in coda per attivare l'aumento automatico. Il valore predefinito è 1 e significa che se sono presenti una o più attività in coda, i nodi vengono aumentati automaticamente.
* **GrowInterval** : intervallo in minuti per attivare l'aumento automatico. L'intervallo predefinito è 5 minuti.
* **ShrinkInterval** : intervallo in minuti per attivare la riduzione automatica. L'intervallo predefinito è 5 minuti.|
* **ShrinkIdleTimes** : numero di controlli continui da ridurre per indicare i nodi sono inattivi. Il valore predefinito è 3 volte. Ad esempio, se **ShrinkInterval** è di 5 minuti, HPC Pack controlla se il nodo è inattivo ogni 5 minuti. Se i nodi sono in uno stato di inattività dopo 3 controlli continui (15 minuti), HPC Pack riduce quel nodo.
* **ExtraNodesGrowRatio** -percentuale aggiuntiva di nodi da aumentare per i processi MPI (Message Passing Interface). Il valore predefinito è 1 e significa che HPC Pack aumenta l'1% dei nodi per i processi MPI.
* **GrowByMin** : opzione che indica se i criteri di aumento automatico sono basati sulle risorse minime necessarie per il processo. Il valore predefinito è false e significa che HPC Pack aumenta i nodi per i processi in base alle risorse massime richieste per i processi.
* **SoaJobGrowThreshold** : soglia di richieste SOA in ingresso per attivare il processo di aumento automatico. Il valore predefinito è 50000.

  > [!NOTE]
  > Questo parametro è supportato a partire da HPC Pack 2012 R2 Update 3.
  >
  >
* **SoaRequestsPerCore** : numero di richieste SOA in ingresso per l'aumento di un core. The default value is 20000.

  > [!NOTE]
  > Questo parametro è supportato a partire da HPC Pack 2012 R2 Update 3.
  >
* **ExcludeNodeGroups**: i nodi nei gruppi di nodi specificati non vengono aumentati e ridotti in automatico.
  
  > [!NOTE]
  > Questo parametro è supportato a partire da HPC Pack 2016.
  >

### <a name="mpi-example"></a>Esempio MPI
Per impostazione predefinita HPC Pack aumenta di 1% i nodi aggiuntivi per i processi MPI. **ExtraNodesGrowRatio** è impostato su 1. Il motivo è che MPI può richiedere più nodi e il processo può essere eseguito solo quando tutti i nodi sono pronti. Quando Azure avvia i nodi, in alcuni casi l'avvio di un nodo può richiedere più tempo rispetto ad altri, causando l'inattività degli altri nodi in attesa che quel nodo sia pronto. Aumentando i nodi supplementari, HPC Pack riduce il tempo di attesa delle risorse, riducendo potenzialmente i costi. Per aumentare la percentuale di nodi aggiuntivi per i processi MPI, ad esempio del 10%, eseguire un comando simile a

    Set-HpcClusterProperty -ExtraNodesGrowRatio 10

### <a name="soa-example"></a>Esempio SOA
Per impostazione predefinita, **SoaJobGrowThreshold** è impostata su 50000 e **SoaRequestsPerCore** è impostato su 20000. Se si invia un processo SOA con 70000 richieste, ci sarà una sola attività in coda e le richieste in ingresso saranno 70000. In questo caso HPC Pack aumenta 1 core per l'attività in coda e per le richieste in ingresso aumenta (70000 - 50000)/20000 = 1 core, in modo da aumentare in totale 2 core per questo processo SOA.

## <a name="run-the-azureautogrowshrinkps1-script"></a>Eseguire lo script AzureAutoGrowShrink.ps1
### <a name="prerequisites"></a>prerequisiti

* **Cluster HPC Pack 2012 R2 Update 1 o versione successiva**: lo script **AzureAutoGrowShrink.ps1** è installato nella cartella %CCP_HOME%bin. Il nodo head del cluster può essere distribuito in locale o in una macchina virtuale di Azure. Vedere [Configurare un cluster ibrido con HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) per iniziare con un nodo head locale e i nodi "burst" di Azure. Vedere lo [script di distribuzione IaaS di HPC Pack](hpcpack-cluster-powershell-script.md) per distribuire velocemente un cluster HPC Pack in macchine virtuali di Azure o usare un [modello di avvio rapido di Azur](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/).
* **Azure PowerShell 1.4.0**: lo script attualmente dipende da questa versione specifica di Azure PowerShell.
* **Per un cluster con nodi burst di Azure** - Eseguire lo script in un computer client in cui è installato HPC Pack o nel nodo head. In caso di esecuzione in un computer client, assicurarsi di impostare la variabile $env:CCP_SCHEDULER in modo che punti al nodo head. I nodi "burst" di Azure devono essere aggiunti al cluster, ma possono essere nello stato Non distribuito.
* **Per un cluster distribuito in macchine virtuali di Azure (modello di distribuzione di Resource Manager)**: per un cluster di macchine virtuali di Azure distribuite nel modello di distribuzione di Resource Manager, lo script supporta due metodi per l'autenticazione di Azure: accedere al proprio account Azure per eseguire lo script ogni volta, eseguendo `Login-AzureRmAccount`, oppure configurare un'entità servizio per l'autenticazione con un certificato. HPC Pack fornisce lo script **ConfigARMAutoGrowShrinkCert.ps** per creare un'entità servizio con certificato. Lo script crea un'applicazione Azure Active Directory (Azure AD) e un'entità servizio e assegna il ruolo di collaboratore all'entità servizio. Per eseguire lo script, avviare Azure PowerShell come amministratore ed eseguire i comandi seguenti:

    ```powershell
    cd $env:CCP_HOME\bin

    Login-AzureRmAccount

    .\ConfigARMAutoGrowShrinkCert.ps1 -DisplayName “YourHpcPackAppName” -HomePage "https://YourHpcPackAppHomePage" -IdentifierUri "https://YourHpcPackAppUri" -PfxFile "d:\yourcertificate.pfx"
    ```

    Per altre informazioni su **ConfigARMAutoGrowShrinkCert.ps1**, eseguire `Get-Help .\ConfigARMAutoGrowShrinkCert.ps1 -Detailed`.

* **Per un cluster distribuito in macchine virtuali di Azure (modello di distribuzione classica)**: eseguire lo script nella macchina virtuale del nodo head, perché dipende dagli script **Start-HpcIaaSNode.ps1** e **Stop-HpcIaaSNode.ps1** installati in tale posizione. Per questi script è inoltre necessario un certificato di gestione di Azure o un file delle impostazioni di pubblicazione (vedere [Gestire i nodi di calcolo in un cluster HPC Pack in Azure](hpcpack-cluster-node-manage.md)). Assicurarsi che tutte le macchine virtuali del nodo di calcolo necessarie siano già aggiunte al cluster. Potrebbero essere in stato arrestato.



### <a name="syntax"></a>Sintassi
```powershell
AzureAutoGrowShrink.ps1 [-NodeTemplates <String[]>] [-JobTemplates <String[]>] [-NodeType <String>]
    -NumOfActiveQueuedTasksPerNodeToGrow <Single> [-NumOfActiveQueuedTasksToGrowThreshold <Int32>]
    [-NumOfInitialNodesToGrow <Int32>] [-GrowCheckIntervalMins <Int32>] [-ShrinkCheckIntervalMins <Int32>]
    [-ShrinkCheckIdleTimes <Int32>] [-ExtraNodesGrowRatio <Int32>] [-ArgFile <String>] [-LogFilePrefix <String>]
    [<CommonParameters>]

AzureAutoGrowShrink.ps1 [-NodeTemplates <String[]>] [-JobTemplates <String[]>] [-NodeType <String>]
    -NumOfQueuedJobsPerNodeToGrow <Single> [-NumOfQueuedJobsToGrowThreshold <Int32>] [-NumOfInitialNodesToGrow
    <Int32>] [-GrowCheckIntervalMins <Int32>] [-ShrinkCheckIntervalMins <Int32>] [-ShrinkCheckIdleTimes <Int32>]
    [-ExtraNodesGrowRatio <Int32>] [-ArgFile <String>] [-LogFilePrefix <String>] [<CommonParameters>]

AzureAutoGrowShrink.ps1 -UseLastConfigurations [-ArgFile <String>] [-LogFilePrefix <String>] [<CommonParameters>]
```
### <a name="parameters"></a>Parametri
* **NodeTemplates** - Nomi dei modelli di nodo per definire l'ambito per i nodi da ingrandire o ridurre. Se non specificato (il valore predefinito è @()), tutti i nodi nel gruppo di nodi **AzureNodes** rientrano nell'ambito quando il valore di **NodeType** è AzureNodes e tutti i nodi nel gruppo di nodi **ComputeNodes** rientrano nell'ambito quando il valore di **NodeType** è ComputeNodes.
* **JobTemplates** : nomi dei modelli di processo per definire l'ambito per i nodi da ingrandire.
* **NodeType** - Il tipo di nodo da ingrandire o ridurre. I valori supportati sono:

  * **AzureNodes** - Per i nodi di Azure PaaS (burst) in un cluster locale o IaaS di Azure.
  * **ComputeNodes** - Solo per le macchine virtuali dei nodi di calcolo in un cluster IaaS di Azure.

* **NumOfQueuedJobsPerNodeToGrow** - Numero di processi in coda richiesti per ingrandire un nodo.
* **NumOfQueuedJobsToGrowThreshold** - Numero di processi in coda di soglia per avviare il processo di ingrandimento.
* **NumOfActiveQueuedTasksPerNodeToGrow** - Numero di attività in coda attive richieste per ingrandire un nodo. Se si specifica un valore maggiore di 0 per **NumOfQueuedJobsPerNodeToGrow** , questo parametro viene ignorato.
* **NumOfActiveQueuedTasksToGrowThreshold** - Numero di attività in coda attive di soglia per avviare il processo di ingrandimento.
* **NumOfInitialNodesToGrow** - Numero minimo iniziale dei nodi, da ingrandire se lo stato di tutti i nodi nell'ambito è **Non distribuito** o **Arrestato (deallocato)**.
* **GrowCheckIntervalMins** - Intervallo in minuti tra i controlli dell'ingrandimento.
* **ShrinkCheckIntervalMins** - Intervallo in minuti tra i controlli della riduzione.
* **ShrinkCheckIdleTimes**: numero di controlli continui della riduzione, separati da **ShrinkCheckIntervalMins**, per indicare che i nodi sono inattivi.
* **UseLastConfigurations** : configurazioni precedenti salvate nel file di argomenti.
* **ArgFile**- Nome del file di argomenti usato per salvare e aggiornare le configurazioni per eseguire lo script.
* **LogFilePrefix** : prefisso del nome del file di log. È possibile specificare un percorso. Per impostazione predefinita il log viene scritto nella directory di lavoro corrente.

### <a name="example-1"></a>Esempio 1
Nell'esempio seguente, i nodi burst di Azure distribuiti con il modello Default AzureNode Template vengono configurati per l'ingrandimento e la riduzione automatici. Se tutti i nodi sono inizialmente nello stato **Non distribuito** vengono avviati almeno 3 nodi. Se il numero di processi in coda è maggiore di 8, lo script avvia i nodi fino a quando il numero non supera il rapporto tra processi in coda e **NumOfQueuedJobsPerNodeToGrow**. Se viene trovato un nodo inattivo in 3 tempi di inattività consecutivi, il nodo viene arrestato.

```powershell
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### <a name="example-2"></a>Esempio 2
Nell'esempio seguente, le macchine virtuali dei nodi di calcolo di Azure distribuite con il modello Default ComputeNode Template vengono configurate per l'ingrandimento e la riduzione automatici.
I processi configurati dal modello di processo predefinito definiscono l'ambito del carico di lavoro nel cluster. Se tutti i nodi sono inizialmente arrestati, vengono avviati almeno 5 nodi. Se il numero delle attività in coda attive è maggiore di 15, lo script avvia i nodi fino a quando il numero non supera il rapporto tra le attività in coda attive e **NumOfActiveQueuedTasksPerNodeToGrow**. Se viene trovato un nodo inattivo in 10 tempi di inattività consecutivi, il nodo viene arrestato.

```powershell
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```
