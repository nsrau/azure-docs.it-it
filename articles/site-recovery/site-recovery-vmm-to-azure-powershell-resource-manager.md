---
title: Replicare le macchine virtuali Hyper-V nei cloud VMM usando Azure Site Recovery e PowerShell (Resource Manager) | Documentazione Microsoft
description: Replicare le macchine virtuali Hyper-V nei cloud VMM usando Azure Site Recovery e PowerShell
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: rochakm
editor: raynew
ms.assetid: 6ac509ad-5024-43d8-b621-d8fec019b9a9
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2017
ms.author: rajanaki
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 933cb8692abea1f66192605208ddd9c0b9057235
ms.contentlocale: it-it
ms.lasthandoff: 04/27/2017


---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell-and-azure-resource-manager"></a>Replicare macchine virtuali Hyper-V nei cloud VMM in Azure con PowerShell e Azure Resource Manager
> [!div class="op_single_selector"]
> * [Portale di Azure](site-recovery-vmm-to-azure.md)
> * [PowerShell - Gestione risorse](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [Portale classico](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell - Classico](site-recovery-deploy-with-powershell.md)
>
>

## <a name="overview"></a>Overview
Azure Site Recovery favorisce la strategia di continuità aziendale e ripristino di emergenza (BCDR) gestendo la replica, il failover e il ripristino delle macchine virtuali in diversi scenari di distribuzione. Per un elenco completo degli scenari di distribuzione, vedere [Panoramica di Azure Site Recovery](site-recovery-overview.md).

In questo articolo viene illustrato come utilizzare PowerShell per automatizzare attività comuni, che è necessario eseguire quando si configura Azure Site Recovery per replicare le macchine virtuali Hyper-V nei cloud VMM di System Center VMM nell'archiviazione di Azure.

Questo articolo include i prerequisiti per lo scenario e illustra le operazioni seguenti:

* Come configurare un insieme di credenziali dei Servizi di ripristino
* Installare il provider di Azure Site Recovery nel server VMM di origine
* Registrare il server nell'insieme di credenziali e aggiungere un account di archiviazione di Azure
* Installare l'agente di Servizi di ripristino di Azure in server host Hyper-V
* Configurare le impostazioni di protezione per cloud VMM da applicare a tutte le macchine virtuali protette
* Abilitare la protezione di queste macchine virtuali
* Eseguire il test del failover per accertarsi che tutti gli elementi funzionino come previsto

Nel caso di problemi di configurazione di questo scenario, inviare le proprie domande al [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [!NOTE]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l’utilizzo del modello di distribuzione Gestione risorse.
>
>

## <a name="before-you-start"></a>Prima di iniziare
Assicurarsi che siano rispettati i prerequisiti seguenti:

### <a name="azure-prerequisites"></a>Prerequisiti di Azure
* È necessario un account [Microsoft Azure](https://azure.microsoft.com/) . Se non si ha un account, creare un [account gratuito](https://azure.microsoft.com/free). È anche possibile leggere le informazioni sui [prezzi di Azure Site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
* Se si prova a usare la replica in uno scenario di sottoscrizione di CSP, sarà necessaria una sottoscrizione di CSP. Per altre informazioni sul programma CSP, vedere [Iscriversi al programma CSP (Cloud Solution Provider)](https://msdn.microsoft.com/library/partnercenter/mt156995.aspx).
* Per archiviare dati replicati in Azure, è necessario un account di archiviazione di Azure v2 (Resource Manager). Nell'account deve essere abilitata la replica geografica. L'account deve trovarsi nella stessa area geografica in cui si trova il servizio Azure Site Recovery e deve essere associato alla stessa sottoscrizione o alla sottoscrizione di CSP. Per altre informazioni sulla configurazione dell'archiviazione di Azure, vedere [Introduzione ad Archiviazione di Microsoft Azure](../storage/storage-introduction.md) come riferimento.
* È necessario assicurarsi che le macchine virtuali da proteggere soddisfino i [prerequisiti delle macchine virtuali di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

> [!NOTE]
> Attualmente è possibile eseguire tramite Powershell solo le operazioni a livello di VM. Il supporto per le operazioni a livello di piano di ripristino sarà presto disponibile.  Attualmente è possibile eseguire i failover solo a livello di granularità di "VM protetta", non a livello di piano di ripristino.
>
>

### <a name="vmm-prerequisites"></a>Prerequisiti di VMM
* È necessario un server VMM in esecuzione su System Center 2012 R2.
* Nei server VMM contenenti macchine virtuali che si desidera proteggere deve essere in esecuzione il provider di Azure Site Recovery. Viene installato durante la distribuzione di Azure Site Recovery.
* È necessario almeno un cloud nel server VMM da proteggere. Il cloud deve contenere:
  * Uno o più gruppi host VMM.
  * Uno o più cluster o server host Hyper-V in ogni gruppo host.
  * Una o più macchine virtuali nel server Hyper-V di origine.
* Per altre informazioni sulla configurazione dei cloud VMM:
  * Per altre informazioni sui cloud privati VMM, leggere [Novità del cloud privato con System Center 2012 R2 VMM](http://go.microsoft.com/fwlink/?LinkId=324952) e [VMM 2012 e i cloud](http://go.microsoft.com/fwlink/?LinkId=324956).
  * Per altre informazioni, vedere [Configurare l'infrastruttura cloud VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
  * Dopo avere definito gli elementi dell'infrastruttura cloud, leggere le informazioni su come creare i cloud privati in [Creazione di un cloud privato in VMM](http://go.microsoft.com/fwlink/p/?LinkId=324953) e [Procedura dettagliata: creazione di cloud privati con System Center 2012 SP1 VMM](http://go.microsoft.com/fwlink/p/?LinkId=324954).

### <a name="hyper-v-prerequisites"></a>Prerequisiti di Hyper-V
* I server Hyper-V host devono eseguire almeno **Windows Server 2012** con ruolo Hyper-V oppure **Microsoft Hyper-V Server 2012** e disporre degli ultimi aggiornamenti installati.
* Se si esegue Hyper-V in un cluster, si noti che il gestore del cluster non viene creato automaticamente se viene usato un cluster basato su indirizzi IP statici. Sarà necessario configurare manualmente il broker del cluster. Ad
* Per istruzioni, vedere [How to Configure Hyper-V Replica Broker](http://blogs.technet.com/b/haroldwong/archive/2013/03/27/server-virtualization-series-hyper-v-replica-broker-explained-part-15-of-20-by-yung-chou.aspx)(Come configurare il Gestore di replica Hyper).
* Qualsiasi server o cluster Hyper-V per cui si vuole gestire la protezione deve essere incluso in un cloud VMM.

### <a name="network-mapping-prerequisites"></a>Prerequisiti di mapping di rete
Quando si proteggono le macchine virtuali in Azure, il mapping di rete collega le reti di macchine virtuali nel server VMM di origine e le reti di Azure in un server VMM di destinazione per eseguire le operazioni seguenti:

* Tutte le macchine virtuali di cui viene eseguito il failover nella stessa rete possono connettersi tra loro, indipendentemente dal piano di ripristino di appartenenza.
* Se nella rete di Azure di destinazione è configurato un gateway di rete, le macchine virtuali possono connettersi ad altre macchine virtuali locali.
* Se non si configura il mapping delle reti, solo le macchine virtuali di cui viene eseguito il failover nello stesso piano di ripristino possono connettersi tra loro dopo il failover in Azure.

Per distribuire il mapping di rete sarà necessario quanto segue:

* Le macchine virtuali che si vuole proteggere nel server VMM di origine devono essere connesse a una rete VM. È necessario che tale rete sia collegata a una rete logica associata al cloud.
* Una rete di Azure a cui le macchine virtuali replicate possono connettersi dopo il failover. Questa rete viene selezionata al momento del failover. La rete deve trovarsi nella stessa area della sottoscrizione di Azure Site Recovery.

Altre informazioni sul mapping di rete sono disponibili negli articoli seguenti:

* [Come configurare le reti logiche in VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
* [Come configurare reti VM e gateway in VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)
* [Come configurare e monitorare le reti virtuali in Azure](https://azure.microsoft.com/documentation/services/virtual-network/)

### <a name="powershell-prerequisites"></a>Prerequisiti di PowerShell
Assicurarsi che Azure PowerShell sia pronto all’uso. Se già si utilizza PowerShell, è necessario eseguire l'aggiornamento alla versione 0.8.10 o successiva. Per informazioni sulla configurazione di PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs). Dopo avere impostato e configurato PowerShell, è possibile vedere tutti i cmdlet disponibili per il servizio [qui](/powershell/azure/overview).

Per informazioni sui suggerimenti che facilitano l'uso dei cmdlet, ad esempio i valori dei parametri, sugli input e sugli output che vengono in genere gestiti in Azure PowerShell, vedere [Iniziare a utilizzare i cmdlet di Azure](/powershell/azure/get-started-azureps).

## <a name="step-1-set-the-subscription"></a>Passaggio 1: impostare la sottoscrizione
1. Da Azure PowerShell accedere all'account di Azure con i cmdlet seguenti

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred
2. Ottenere un elenco delle sottoscrizioni. Verranno elencati anche i valori subscriptionID per ogni sottoscrizione. Annotare il valore subscriptionID della sottoscrizione in cui si vuole creare l'insieme di credenziali dei Servizi di ripristino

        Get-AzureRmSubscription
3. Configurare la sottoscrizione in cui deve essere creato l'insieme di credenziali dei Servizi di ripristino, indicando l'ID della sottoscrizione

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="step-2-create-a-recovery-services-vault"></a>Passaggio 2: Creare l'insieme di credenziali di Servizi di ripristino
1. Creare un gruppo di risorse di Azure Resource Manager, qualora non ce ne sia già uno disponibile

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. Creare un nuovo insieme di credenziali di Servizi di ripristino e salvare l'oggetto insieme di credenziali di ASR creato in una variabile. Verrà usato in seguito. È anche possibile recuperare l'oggetto insieme di credenziali di ASR dopo la creazione usando il cmdlet Get-AzureRMRecoveryServicesVault:-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

## <a name="step-3-set-the-recovery-services-vault-context"></a>Passaggio 3: Impostare il contesto dell'insieme di credenziali di Servizi di ripristino

Impostare il contesto dell'insieme eseguendo il comando seguente.

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-install-the-azure-site-recovery-provider"></a>Passaggio 4: installare il provider di Azure Site Recovery
1. Nel computer VMM, creare una directory eseguendo il comando seguente:

       New-Item c:\ASR -type directory
2. Estrarre i file utilizzando il provider scaricato eseguendo il comando seguente:

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Installare il provider utilizzando i comandi seguenti:

       .\SetupDr.exe /i
       $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
       do
       {
         $isNotInstalled = $true;
         if(Test-Path $installationRegPath)
         {
           $isNotInstalled = $false;
         }
       }While($isNotInstalled)

   Attendere la fine dell'installazione.
4. Registrare il server nell'insieme di credenziali utilizzando il comando seguente:

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="step-5-create-an-azure-storage-account"></a>Passaggio 5: creare un account di archiviazione di Azure

Se non si ha un account di archiviazione di Azure, creare un account con la replica geografica abilitata nella stessa area in cui si trova l'insieme di credenziali eseguendo il comando seguente:

        $StorageAccountName = "teststorageacc1"    #StorageAccountname
        $StorageAccountGeo  = "Southeast Asia"     
        $ResourceGroupName =  “myRG”             #ResourceGroupName
        $RecoveryStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Type “StandardGRS” -Location $StorageAccountGeo

Si noti che l'account di archiviazione deve trovarsi nella stessa area geografica in cui si trova il servizio Azure Site Recovery e deve essere associato alla stessa sottoscrizione.

## <a name="step-6-install-the-azure-recovery-services-agent"></a>Passaggio 6: installare l'agente di Servizi di ripristino di Azure
1. Scaricare l'agente di Servizi di ripristino di Azure all'indirizzo [http:/aka.ms/latestmarsagent](http://aka.ms/latestmarsagent) e installarlo in ogni server host Hyper-V disponibile nei cloud VMM da proteggere.
2. Eseguire il comando seguente in tutti gli host VMM:

       marsagentinstaller.exe /q /nu

## <a name="step-7-configure-cloud-protection-settings"></a>Passaggio 7: configurare le impostazioni di protezione del cloud
1. Creare un criterio di replica per Azure eseguendo il comando seguente:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points

        $policryresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider HyperVReplicaAzure -ReplicationFrequencyInSeconds $replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId "/subscriptions/q1345667/resourceGroups/test/providers/Microsoft.Storage/storageAccounts/teststorageacc1"

1. Ottenere un contenitore di protezione eseguendo i comandi seguenti:

       $PrimaryCloud = "testcloud"
       $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  
2. Ottenere i dettagli del criterio per una variabile usando il processo creato e indicando il nome descrittivo del criterio:

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
3. Avviare l'associazione del contenitore di protezione al criterio di replica:

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $protectionContainer  
4. Al termine del processo, eseguire il comando seguente:

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }
5. Al termine del processo, eseguire il comando seguente:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Per verificare il completamento dell'operazione, attenersi alla procedura descritta in [Monitorare l'attività](#monitor).

## <a name="step-8-configure-network-mapping"></a>Passaggio 8: configurare il mapping di rete
Prima di iniziare il mapping di rete, verificare che le macchine virtuali nel server VMM di origine siano connesse a una rete VM. Inoltre, creare una o più rete virtuali di Azure.

Per altre informazioni su come creare una rete virtuale con Azure Resource Manager e PowerShell, vedere [Creare una rete virtuale con una connessione VPN da sito a sito usando PowerShell e Azure Resource Manager](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

Si noti che è possibile eseguire il mapping di più reti di macchine virtuali a una singola rete di Azure. Se la rete di destinazione ha più subnet e una di esse ha lo stesso nome di una subnet in cui si trova la macchina virtuale di origine, la macchina virtuale di replica sarà connessa a tale subnet di destinazione dopo il failover. Se non è presente una subnet di destinazione con un nome corrispondente, la macchina virtuale sarà connessa alla prima subnet della rete.

1. Il primo comando ottiene i server per l’insieme di credenziali corrente di Azure Site Recovery. Il comando archivia i server di Microsoft Azure Site Recovery nella variabile di matrice $Servers.

        $Servers = Get-AzureRmSiteRecoveryServer
2. Il secondo comando ottiene la rete di ripristino del sito per il primo server nella matrice $Servers. Il comando archivia le reti nella variabile $Networks.

        $Networks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]

1. Il terzo comando ottiene le reti virtuali di Azure e quindi il valore nella variabile $AzureVmNetworks.

        $AzureVmNetworks =  Get-AzureRmVirtualNetwork
2. Il cmdlet finale crea un mapping tra la rete primaria e la rete delle macchine virtuali di Azure. Il cmdlet specifica la rete primaria come primo elemento di $Networks. Il cmdlet specifica una rete di macchine virtuali come primo elemento di $AzureVmNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureVMNetworkId $AzureVmNetworks[0]

## <a name="step-9-enable-protection-for-virtual-machines"></a>Passaggio 9: abilitare la protezione per le macchine virtuali
Dopo la configurazione corretta di server, cloud e reti, sarà possibile abilitare la protezione per le macchine virtuali sul cloud.

 Tenere presente quanto segue:

* Le macchine virtuali devono essere conformi ai requisiti di Azure. Vedere tali requisiti nei collegamenti relativi a [prerequisiti e supporto](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) nella guida alla pianificazione.
* Per abilitare la protezione, è necessario che le proprietà del sistema operativo e del disco del sistema operativo siano impostate per la macchina virtuale. Quando si crea una macchina virtuale basata su un modello di macchina virtuale VMM è possibile impostare la proprietà. È possibile impostare queste proprietà anche per le macchine virtuali esistenti nelle schede **Generale** e **Configurazione hardware** delle proprietà delle macchine virtuali. Se queste proprietà non vengono impostate in VMM, sarà possibile configurarle nel portale di Azure Site Recovery.

1. Per abilitare la protezione, eseguire il comando seguente per ottenere il contenitore di protezione:

          $ProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $CloudName
2. Ottenere l'entità di protezione (VM) eseguendo il comando seguente:

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $protectionContainer
3. Abilitare il ripristino di emergenza per la macchina virtuale eseguendo il comando seguente:

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable –Force -Policy $policy -RecoveryAzureStorageAccountId  $storageID "/subscriptions/217653172865hcvkchgvd/resourceGroups/rajanirgps/providers/Microsoft.Storage/storageAccounts/teststorageacc1

## <a name="test-your-deployment"></a>Testare la distribuzione
Per testare la distribuzione, è possibile eseguire un failover di test per una singola macchina virtuale oppure creare un piano di ripristino costituito da più macchine virtuali ed eseguire un failover di test per il piano. Il failover di test consente di simulare il meccanismo di failover e di ripristino in una rete isolata. Si noti che:

* Per eseguire la connessione alla macchina virtuale in Azure tramite Desktop remoto dopo il failover, abilitare Connessione Desktop remoto sulla macchina virtuale prima di eseguire il failover di test.
* Dopo il failover si userà un indirizzo IP pubblico per connettersi alla macchina virtuale in Azure tramite Desktop remoto. Se si vuole procedere in questo senso, assicurarsi che non siano presenti criteri di dominio che impediscono la connessione a una macchina virtuale mediante un indirizzo pubblico.

Per verificare il completamento dell'operazione, attenersi alla procedura descritta in [Monitorare l'attività](#monitor).

### <a name="run-a-test-failover"></a>Eseguire un failover di test
- Avviare il failover di test eseguendo il comando seguente:

       $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-a-planned-failover"></a>Eseguire un failover pianificato
- Avviare il failover pianificato eseguendo il comando seguente:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-an-unplanned-failover"></a>Eseguire un failover non pianificato
- Avviare il failover non pianificato eseguendo il comando seguente:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

## <a name=monitor></a> Monitorare l'attività
Utilizzare i comandi seguenti per monitorare l'attività. Si noti che è necessario attendere l’esecuzione dei processi per il completamento dell'elaborazione.

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

    if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni](/powershell/module/azurerm.recoveryservices.backup/#recovery) su Azure Site Recovery con i cmdlet PowerShell per Azure Resource Manager.

