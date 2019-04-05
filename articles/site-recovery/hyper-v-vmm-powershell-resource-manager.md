---
title: Configurare il ripristino di emergenza di macchine virtuali Hyper-V di cloud VMM in un sito secondario con Azure Site Recovery e PowerShell | Microsoft Docs
description: Questo articolo descrive come configurare il ripristino di emergenza di macchine virtuali Hyper-V di cloud VMM in un sito VMM secondario usando Azure Site Recovery e PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: 78bd077b5491b093510b9c55bf7b5a42ee9cb578
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045629"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Configurare il ripristino di emergenza di macchine virtuali Hyper-V in un sito secondario con PowerShell (Resource Manager)

Questo articolo illustra come automatizzare la procedura per la replica di macchine virtuali Hyper-V in cloud System Center Virtual Machine Manager in un cloud Virtual Machine Manager in un sito locale secondario usando [Azure Site Recovery](site-recovery-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

- Esaminare [l'architettura e i componenti dello scenario](hyper-v-vmm-architecture.md).
- Verificare i [requisiti di supporto](site-recovery-support-matrix-to-sec-site.md) per tutti i componenti.
- Assicurarsi che i server Virtual Machine Manager e gli host Hyper-V rispettino i [requisiti di supporto](site-recovery-support-matrix-to-sec-site.md).
- Assicurarsi che le macchine virtuali da replicare siano conformi al [supporto del computer replicato](site-recovery-support-matrix-to-sec-site.md).


## <a name="prepare-for-network-mapping"></a>Preparare il mapping di rete

[Mapping di rete](hyper-v-vmm-network-mapping.md) esegue il mapping tra le reti delle macchine virtuali di Virtual Machine Manager locali nei cloud di origine e destinazione. Il mapping esegue queste operazioni:

- Connette le macchine virtuali a reti di macchine virtuali di destinazione appropriate dopo il failover. 
- Posiziona in modo ottimale le macchine virtuali di replica nei server host Hyper-V di destinazione. 
- Se non si configura il mapping di rete, le macchine virtuali di replica non verranno connesse alla rete delle macchine virtuali dopo il failover.

Preparare Virtual Machine Manager come segue:

* Assicurarsi di avere [reti logiche di Virtual Machine Manager](https://docs.microsoft.com/system-center/vmm/network-logical) nei server di Virtual Machine Manager di origine e di destinazione:

    - La rete logica nel server di origine deve essere associata al cloud di origine in cui si trovano gli host Hyper-V.
    - La rete logica nel server di destinazione deve essere associata al cloud di destinazione.
* Assicurarsi di avere [reti delle macchine virtuali](https://docs.microsoft.com/system-center/vmm/network-virtual) nei server di Virtual Machine Manager di origine e di destinazione. Le reti VM devono essere collegate alla rete logica in ogni posizione.
* Connettere le macchine virtuali negli host Hyper-V di origine alla rete VM di origine. 

## <a name="prepare-for-powershell"></a>Eseguire le operazioni preliminari per PowerShell

Assicurarsi che Azure PowerShell sia pronto all'uso:

- Se si usa già PowerShell, eseguire l'aggiornamento alla versione 0.8.10 o versione successiva. [Altre informazioni](/powershell/azureps-cmdlets-docs) su come configurare PowerShell.
- Dopo aver impostato e configurato PowerShell, visualizzare i [cmdlet dei servizi](/powershell/azure/overview).
- Per altre informazioni su come usare i valori dei parametri, gli input e gli output in Azure PowerShell, leggere la guida [Introduzione](/powershell/azure/get-started-azureps).

## <a name="set-up-a-subscription"></a>Configurare una sottoscrizione
1. Da PowerShell accedere al proprio account di Azure.

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Connect-AzAccount #-Credential $Cred
2. Recuperare un elenco delle sottoscrizioni con i relativi ID. Prendere nota dell'ID della sottoscrizione in cui si vuole creare l'insieme di credenziali di Servizi di ripristino. 

        Get-AzSubscription
3. Configurare la sottoscrizione per l'insieme di credenziali.

        Set-AzContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino
1. Creare un gruppo di risorse di Azure Resource Manager, se non ne è già disponibile uno.

        New-AzResourceGroup -Name #ResourceGroupName -Location #location
2. Creare un nuovo insieme di credenziali di Servizi di ripristino. Salvare l'oggetto insieme di credenziali in una variabile da usare in seguito. 

        $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   
    È possibile recuperare l'oggetto dell'insieme di credenziali dopo averlo creato usando il cmdlet Get-AzRecoveryServicesVault.

## <a name="set-the-vault-context"></a>Impostare il contesto dell'insieme di credenziali
1. Recuperare un insieme di credenziali esistente.

       $vault = Get-AzRecoveryServicesVault -Name #vaultname
2. Impostare il contesto dell'insieme di credenziali.

       Set-AzSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-site-recovery-provider"></a>Installare il provider di Site Recovery
1. Nel computer Virtual Machine Manager creare una directory eseguendo il comando seguente:

       New-Item c:\ASR -type directory
2. Estrarre i file usando il file di installazione del provider scaricato.

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Installare il provider e attendere il completamento dell'installazione.

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

4. Registrare il server nell'insieme di credenziali.

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="create-and-associate-a-replication-policy"></a>Creare e associare criteri di replica
1. Creare i criteri di replica, in questo caso per Hyper-V 2012 R2, come indicato di seguito:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > Il cloud Virtual Machine Manager può contenere host Hyper-V che eseguono versioni diverse di Windows Server, ma i criteri di replica sono specifici di una determinata versione di un sistema operativo. Se sono disponibili vari host in esecuzione su sistemi operativi diversi, creare criteri di replica separati per ogni sistema. Ad esempio, se sono disponibili cinque host in esecuzione su Windows Server 2012 e tre su Windows Server 2012 R2, creare due criteri di replica, uno per ogni tipo di sistema operativo.

2. Recuperare il contenitore di protezione principale (cloud Virtual Machine Manager primario) e il contenitore di protezione di ripristino (cloud Virtual Machine Manager di ripristino).

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
3. Recuperare i criteri di replica creati usando il nome descrittivo.

       $policy = Get-AzSiteRecoveryPolicy -FriendlyName $policyname
4. Avviare l'associazione del contenitore di protezione (cloud Virtual Machine Manager) al criterio di replica.

       $associationJob  = Start-AzSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
5. Attendere la fine del processo di associazione dei criteri. Per controllare se il processo è stato completato, usare il frammento di PowerShell seguente:

       $job = Get-AzSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

6. Al termine dell'elaborazione del processo, eseguire il comando seguente:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Per verificare il completamento dell'operazione, attenersi alla procedura descritta in [Monitorare l'attività](#monitor-activity).

##  <a name="configure-network-mapping"></a>Configurare il mapping di rete
1. Usare questo comando per recuperare i server per l'insieme di credenziali corrente. Il comando archivia i server di Site Recovery nella variabile di matrice $Servers.

        $Servers = Get-AzSiteRecoveryServer
2. Eseguire questo comando per recuperare le reti per il server Virtual Machine Manager di origine e il server Virtual Machine Manager di destinazione.

        $PrimaryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > Il server Virtual Machine Manager di origine può essere il primo o secondo nella matrice di server. Controllare i nomi dei server Virtual Machine Manager e recuperare le reti in modo appropriato.


3. Il cmdlet crea un mapping tra la rete primaria e la rete di ripristino. Specifica la rete primaria come primo elemento di $PrimaryNetworks. Specifica la rete di ripristino come primo elemento di $RecoveryNetworks.

        New-AzSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>Abilitare la protezione per le macchine virtuali
Dopo aver configurato correttamente server, cloud e reti, abilitare la protezione per le macchine virtuali nel cloud.

1. Per abilitare la protezione, eseguire il comando seguente per recuperare il contenitore di protezione:

          $PrimaryProtectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Ottenere l'entità di protezione (macchina virtuale) come segue:

           $protectionEntity = Get-AzSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Abilitare la replica per la macchina virtuale.

          $jobResult = Set-AzSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>Eseguire un failover di test

Per testare la distribuzione, eseguire un failover di test per una singola macchina virtuale. È anche possibile creare un piano di ripristino che contiene più macchine virtuali ed eseguire un failover di test per il piano. Il failover di test consente di simulare il meccanismo di failover e di ripristino in una rete isolata.

1. Recuperare la macchina virtuale in cui verrà eseguito il failover delle macchine virtuali.

       $Servers = Get-AzSiteRecoveryServer
       $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

2. Eseguire un failover di test.

   Per una singola macchina virtuale:

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
   Per un piano di ripristino:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

Per verificare il completamento dell'operazione, attenersi alla procedura descritta in [Monitorare l'attività](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Eseguire failover pianificati e non pianificati

1. Eseguire un failover pianificato.

   Per una singola macchina virtuale:

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Per un piano di ripristino:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

2. Eseguire un failover non pianificato.

   Per una singola macchina virtuale:
        
        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Per un piano di ripristino:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>Monitorare l'attività
Usare i comandi seguenti per monitorare l'attività di failover. Attendere la fine dell'elaborazione tra i processi.

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

[Altre informazioni](/powershell/module/az.recoveryservices) su Site Recovery con i cmdlet di PowerShell per Resource Manager.
