---
title: Eseguire la replica di macchine virtuali Hyper-V con PowerShell e Azure Resource Manager | Documentazione Microsoft
description: Automatizzare la replica di macchine virtuali in Azure con Azure Site Recovery usando PowerShell e Azure Resource Manager.
services: site-recovery
author: bsiva
manager: abhiag
ms.service: site-recovery
ms.topic: article
ms.date: 03/08/2018
ms.author: bsiva
ms.openlocfilehash: 4304cad9dc6aab7eb95885815a3ceb636ca6ff52
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Configurare il ripristino di emergenza in Azure per le macchine virtuali Hyper-V tramite PowerShell e Azure Resource Manager

[Azure Site Recovery](site-recovery-overview.md) favorisce l'attuazione della strategia di continuità aziendale e ripristino di emergenza orchestrando le operazioni di replica, failover e ripristino delle macchine virtuali di Azure e delle macchine virtuali e dei server fisici locali.

Questo articolo descrive come usare Windows PowerShell, insieme ad Azure Resource Manager, per replicare macchine virtuali Hyper-V in Azure. L'esempio usato in questo articolo illustra come replicare in Azure una singola macchina virtuale in esecuzione su un host Hyper-V.

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell offre i cmdlet per gestire Azure con Windows PowerShell. I cmdlet di PowerShell per Site Recovery disponibili con Azure PowerShell per Azure Resource Manager consentono di proteggere e ripristinare i server in Azure.

Non è necessario essere un esperto di PowerShell per usare questo articolo, ma si presume che si conoscano i concetti di base, ad esempio moduli, cmdlet e sessioni. Leggere [Introduzione a Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx) e [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

> [!NOTE]
> I partner Microsoft nel programma Cloud Solution Provider (CSP) possono configurare e gestire la protezione dei server dei clienti nelle rispettive sottoscrizioni CSP, ovvero le sottoscrizioni tenant.
>
>

## <a name="before-you-start"></a>Prima di iniziare
Assicurarsi che siano rispettati i prerequisiti seguenti:

* Account [Microsoft Azure](https://azure.microsoft.com/) . È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). Inoltre, è possibile leggere le informazioni sui [prezzi di Azure Site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
* Azure PowerShell 1.0. Per informazioni su questa versione e su come installarla, vedere [Azure PowerShell 1.0.](https://azure.microsoft.com/)
* Moduli [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) e [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/). È possibile ottenere le ultime versioni di questi moduli da [PowerShell Gallery](https://www.powershellgallery.com/)

Inoltre, l'esempio specifico descritto in questo articolo presenta i seguenti prerequisiti:

* Host Hyper-V che esegue Windows Server 2012 R2 o Microsoft Hyper-V Server 2012 R2 contenente una o più macchine virtuali. I server Hyper-V devono essere connessi a Internet, in modo diretto o tramite proxy.
* Le macchine virtuali da replicare devono essere conformi a [questi prerequisiti](hyper-v-azure-support-matrix.md#replicated-vms).

## <a name="step-1-sign-in-to-your-azure-account"></a>Passaggio 1: Accedere al proprio account Azure

1. Aprire una console di PowerShell ed eseguire questo comando per accedere all'account di Azure. Il cmdlet visualizza una pagina Web che richiede le credenziali dell'account: **Login-AzureRmAccount**.
    - In alternativa, è possibile includere le credenziali dell'account come parametro nel cmdlet **Login-AzureRmAccount**, usando il parametro **-Credential**.
    - Se si è un partner CSP che opera per conto di un tenant, è necessario specificare il cliente come tenant usando l'ID tenant o il nome di dominio primario del tenant. Ad esempio: **Login-AzureRmAccount -Tenant "fabrikam.com"**
2. Associare la sottoscrizione che si vuole usare all'account perché un account può avere molte sottoscrizioni:

    `Select-AzureRmSubscription -SubscriptionName $SubscriptionName`

3. Verificare che la sottoscrizione sia registrata per l'uso dei provider di Azure per Servizi di ripristino e Site Recovery usando i comandi seguenti:

    `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices``Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

4. Verificare che nell'output di questi comandi, **RegistrationState** sia impostato su **Registered** e quindi procedere con il passaggio 2. In caso contrario è necessario registrare il provider mancante nella sottoscrizione, eseguendo questi comandi:

    `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery``Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices`

5. Verificare che i provider siano stati registrati correttamente usando i comandi seguenti:

    `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`.

## <a name="step-2-set-up-the-vault"></a>Passaggio 2: Configurare l'insieme di credenziali

1. Creare un gruppo di risorse di Azure Resource Manager in cui creare l'insieme di credenziali o usare un gruppo di risorse esistente. Creare un nuovo gruppo di risorse come segue. La variabile $ResourceGroupName contiene il nome del gruppo di risorse che si vuole creare e la variabile $Geo contiene l'area di Azure in cui creare il gruppo di risorse (ad esempio, Brasile meridionale).

    `New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo` 

2. Per ottenere un elenco di gruppi di risorse disponibili nella sottoscrizione, eseguire il cmdlet **Get-AzureRmResourceGroup**.
2. Creare un nuovo insieme di credenziali di Servizi di ripristino di Azure, come segue:

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    È possibile recuperare un elenco degli insiemi di credenziali esistente con il cmdlet **Get AzureRmRecoveryServicesVault**.


## <a name="step-3-set-the-recovery-services-vault-context"></a>Passaggio 3: Impostare il contesto dell’insieme di credenziali di Servizi di ripristino

Impostare il contesto dell'insieme di credenziali come segue:

`Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault`

## <a name="step-4-create-a-hyper-v-site"></a>Passaggio 4: Creare un sito Hyper-V

1. Creare un nuovo sito Hyper-V, come segue:

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

2. Questo cmdlet avvia un processo di Site Recovery per creare il sito e restituisce un oggetto processo di Site Recovery. Attendere il completamento del processo e verificare che sia stato completato correttamente.
3. Usare **Get-AzureRmSiteRecoveryJob cmdlet** per recuperare l'oggetto processo e controllare lo stato corrente del processo.
4. Generare e scaricare una chiave di registrazione per il sito come segue:

    ```
    $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path
    ```

5. Copiare la chiave scaricata nell'host Hyper-V. La chiave è necessaria per registrare l'host Hyper-V nel sito.

## <a name="step-5-install-the-provider-and-agent"></a>Passaggio 5: Installare provider e agente

1. Scaricare il programma di installazione per l'ultima versione del provider da [Microsoft](https://aka.ms/downloaddra).
2. Eseguire il programma di installazione nell'host Hyper-V.
3. Al termine dell'installazione, continuare con il passaggio di registrazione.
4. Quando richiesto, fornire la chiave scaricata e completare la registrazione dell'host Hyper-V.
5. Verificare che l'host Hyper-V sia stato registrato nel sito come segue:

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy"></a>Passaggio 6: Creare un criterio di replica

Prima di iniziare, tenere presente che l'account di archiviazione specificato deve essere nella stessa area di Azure dell'insieme di credenziali e avere la replica geografica abilitata.

1. Creare un criterio di replica come segue:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

2. Controllare il processo restituito per assicurarsi che la creazione del criterio di replica sia riuscita.

3. Recuperare il contenitore di protezione corrispondente al sito come segue:

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3. Associare il contenitore di protezione al criterio di replica come segue:

     $Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName   $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

4. Attendere il corretto completamento del processo di associazione.

## <a name="step-7-enable-vm-protection"></a>Passaggio 7: Abilitare la protezione della macchina virtuale

1. Recuperare l'entità di protezione corrispondente alla macchina virtuale da proteggere, come segue:

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
2. Proteggere la macchina virtuale. Se alla VM protetta è collegato più di un disco, specificare il disco del sistema operativo usando il parametro *OSDiskName* .

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

3. Attendere che le macchine virtuali raggiungano uno stato protetto dopo la replica iniziale. L’operazione può richiedere un certo tempo a seconda di fattori quali la quantità di dati da replicare e la larghezza di banda upstream disponibile in Azure. Quando viene raggiunto lo stato protetto, i valori State e StateDescription del processo vengono aggiornati come segue: 

        PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed
4. Aggiornare le proprietà di ripristino, ad esempio la dimensione del ruolo macchina virtuale, e la rete di Azure a cui associare la scheda di interfaccia di rete della macchina virtuale dopo il failover.

        PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

        PS C:\> $UpdateJob

        Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
                           s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        Type             : Microsoft.RecoveryServices/vaults/replicationJobs
        JobType          : UpdateVmProperties
        DisplayName      : Update the virtual machine
        ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
        State            : Succeeded
        StateDescription : Completed
        StartTime        : 10-12-2015 17:55:53 +00:00
        EndTime          : 10-12-2015 17:55:54 +00:00
        TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
        TargetObjectType : ProtectionEntity
        TargetObjectName : Fabrikam-App
        AllowedActions   : {Restart}
        Tasks            : {UpdateVmPropertiesTask}
        Errors           : {}



## <a name="step-8-run-a-test-failover"></a>Passaggio 8: Eseguire un failover di test
1. Eseguire un failover di test come segue:

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

        $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
2. Verificare che la VM di test sia creata in Azure. Il processo di failover di test viene sospeso dopo la creazione della macchina virtuale di test in Azure.
3. Per pulire e completare il failover di test, eseguire:

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni](https://msdn.microsoft.com/library/azure/mt637930.aspx) su Azure Site Recovery con i cmdlet PowerShell per Azure Resource Manager.
