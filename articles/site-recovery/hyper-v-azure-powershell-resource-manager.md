---
title: Ripristino di emergenza di macchine virtuali Hyper-V con Azure Site Recovery e PowerShell
description: Automatizzare il ripristino di emergenza delle macchine virtuali Hyper-V in Azure con il servizio Azure Site Recovery usando PowerShell e Azure Resource Manager.
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 01/10/2020
ms.author: sutalasi
ms.openlocfilehash: 6499c986bef965848303ee9833fd59f5e3f0889c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78391749"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Configurare il ripristino di emergenza in Azure per le macchine virtuali Hyper-V tramite PowerShell e Azure Resource Manager

[Azure Site Recovery](site-recovery-overview.md) favorisce l'attuazione della strategia di continuità aziendale e ripristino di emergenza orchestrando le operazioni di replica, failover e ripristino delle macchine virtuali di Azure e delle macchine virtuali e dei server fisici locali.

Questo articolo descrive come usare Windows PowerShell, insieme ad Azure Resource Manager, per replicare macchine virtuali Hyper-V in Azure. L'esempio usato in questo articolo illustra come replicare in Azure una singola macchina virtuale in esecuzione su un host Hyper-V.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell offre i cmdlet per gestire Azure con Windows PowerShell. I cmdlet di PowerShell per Site Recovery disponibili con Azure PowerShell per Azure Resource Manager consentono di proteggere e ripristinare i server in Azure.

Non è necessario essere un esperto di PowerShell per usare questo articolo, ma si presume che si conoscano i concetti di base, ad esempio moduli, cmdlet e sessioni. Per ulteriori informazioni, vedere la [documentazione di PowerShell](/powershell) e [utilizzo di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

> [!NOTE]
> I partner Microsoft nel programma Cloud Solution Provider (CSP) possono configurare e gestire la protezione dei server dei clienti nelle rispettive sottoscrizioni CSP, ovvero le sottoscrizioni tenant.

## <a name="before-you-start"></a>Prima di iniziare

Assicurarsi che siano rispettati i prerequisiti seguenti:

- Account [Microsoft Azure](https://azure.microsoft.com/) . È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). Inoltre, è possibile leggere le informazioni sui [prezzi di Azure Site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
- Azure PowerShell. Per informazioni su questa versione e su come installarla, vedere [install Azure PowerShell](/powershell/azure/install-az-ps).

Inoltre, l'esempio specifico descritto in questo articolo presenta i seguenti prerequisiti:

- Host Hyper-V che esegue Windows Server 2012 R2 o Microsoft Hyper-V Server 2012 R2 contenente una o più macchine virtuali. I server Hyper-V devono essere connessi a Internet, in modo diretto o tramite proxy.
- Le macchine virtuali da replicare devono essere conformi a [questi prerequisiti](hyper-v-azure-support-matrix.md#replicated-vms).

## <a name="step-1-sign-in-to-your-azure-account"></a>Passaggio 1: Accedere al proprio account Azure

1. Aprire una console di PowerShell ed eseguire questo comando per accedere all'account di Azure. Il cmdlet visualizza una pagina Web in cui vengono richieste le credenziali dell'account: `Connect-AzAccount`.
   - In alternativa, è possibile includere le credenziali dell'account come parametro nel cmdlet di `Connect-AzAccount`, usando il parametro **Credential** .
   - Se si è un partner CSP che lavora per conto di un tenant, specificare il cliente come tenant, usando il nome di dominio primario tenantID o tenant. Ad esempio: `Connect-AzAccount -Tenant "fabrikam.com"`
1. Associare la sottoscrizione che si vuole usare all'account perché un account può avere molte sottoscrizioni:

   ```azurepowershell
   Set-AzContext -Subscription $SubscriptionName
   ```

1. Verificare che la sottoscrizione sia registrata per l'uso dei provider di Azure per Servizi di ripristino e Site Recovery usando i comandi seguenti:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

1. Verificare che nell'output di questi comandi, **RegistrationState** sia impostato su **Registered** e quindi procedere con il passaggio 2. In caso contrario è necessario registrare il provider mancante nella sottoscrizione, eseguendo questi comandi:

   ```azurepowershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

1. Verificare che i provider siano stati registrati correttamente usando i comandi seguenti:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

## <a name="step-2-set-up-the-vault"></a>Passaggio 2: Configurare l'insieme di credenziali

1. Creare un gruppo di risorse di Azure Resource Manager in cui creare l'insieme di credenziali o usare un gruppo di risorse esistente. Creare un nuovo gruppo di risorse come segue. La variabile `$ResourceGroupName` contiene il nome del gruppo di risorse che si vuole creare e la variabile $Geo contiene l'area di Azure in cui creare il gruppo di risorse (ad esempio, "Brasile meridionale").

   ```azurepowershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Geo
   ```

1. Per ottenere un elenco di gruppi di risorse nella sottoscrizione, eseguire il cmdlet `Get-AzResourceGroup`.
1. Creare un nuovo insieme di credenziali di Servizi di ripristino di Azure, come segue:

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>
   ```

È possibile recuperare un elenco di insiemi di credenziali esistenti con il cmdlet `Get-AzRecoveryServicesVault`.

## <a name="step-3-set-the-recovery-services-vault-context"></a>Passaggio 3: Impostare il contesto dell’insieme di credenziali di Servizi di ripristino

Impostare il contesto dell'insieme di credenziali come segue:

```azurepowershell
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="step-4-create-a-hyper-v-site"></a>Passaggio 4: Creare un sito Hyper-V

1. Creare un nuovo sito Hyper-V, come segue:

   ```azurepowershell
   $sitename = "MySite"                #Specify site friendly name
   New-AzRecoveryServicesAsrFabric -Type HyperVSite -Name $sitename
   ```

1. Questo cmdlet avvia un processo di Site Recovery per creare il sito e restituisce un oggetto processo di Site Recovery. Attendere il completamento del processo e verificare che sia stato completato correttamente.
1. Usare il cmdlet `Get-AzRecoveryServicesAsrJob` per recuperare l'oggetto processo e controllare lo stato corrente del processo.
1. Generare e scaricare una chiave di registrazione per il sito come segue:

   ```azurepowershell
   $SiteIdentifier = Get-AzRecoveryServicesAsrFabric -Name $sitename | Select-Object -ExpandProperty SiteIdentifier
   $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
   ```

1. Copiare la chiave scaricata nell'host Hyper-V. La chiave è necessaria per registrare l'host Hyper-V nel sito.

## <a name="step-5-install-the-provider-and-agent"></a>Passaggio 5: Installare provider e agente

1. Scaricare il programma di installazione per l'ultima versione del provider da [Microsoft](https://aka.ms/downloaddra).
1. Eseguire il programma di installazione nell'host Hyper-V.
1. Al termine dell'installazione, continuare con il passaggio di registrazione.
1. Quando richiesto, fornire la chiave scaricata e completare la registrazione dell'host Hyper-V.
1. Verificare che l'host Hyper-V sia stato registrato nel sito come segue:

   ```azurepowershell
   $server = Get-AzRecoveryServicesAsrFabric -Name $siteName | Get-AzRecoveryServicesAsrServicesProvider -FriendlyName $server-friendlyname
   ```

Se è in esecuzione un server Hyper-V Core, scaricare il file di installazione e completare questi passaggi:

1. Estrarre i file da _AzureSiteRecoveryProvider. exe_ in una directory locale eseguendo questo comando:

   ```console
   AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Eseguire il comando seguente:

   ```console
   .\setupdr.exe /i
   ```

   I risultati vengono registrati in _%ProgramData%\ASRLogs\DRASetupWizard.log_.

1. Registrare il server eseguendo questo comando:

   ```console
   cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
   ```

## <a name="step-6-create-a-replication-policy"></a>Passaggio 6: Creare un criterio di replica

Prima di iniziare, l'account di archiviazione specificato deve trovarsi nella stessa area di Azure dell'insieme di credenziali e deve avere la replica geografica abilitata.

1. Creare un criterio di replica come segue:

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $Recoverypoints = 6                    #specify the number of recovery points
   $storageaccountID = Get-AzStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select-Object -ExpandProperty Id

   $PolicyResult = New-AzRecoveryServicesAsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds -NumberOfRecoveryPointsToRetain $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID
   ```

1. Controllare il processo restituito per assicurarsi che la creazione del criterio di replica sia riuscita.

1. Recuperare il contenitore di protezione corrispondente al sito come segue:

   ```azurepowershell
   $protectionContainer = Get-AzRecoveryServicesAsrProtectionContainer
   ```

1. Associare il contenitore di protezione al criterio di replica come segue:

   ```azurepowershell
   $Policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $PolicyName
   $associationJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name $mappingName -Policy $Policy -PrimaryProtectionContainer $protectionContainer[0]
   ```

1. Attendere il corretto completamento del processo di associazione.

1. Recuperare il mapping del contenitore di protezione.

   ```azurepowershell
   $ProtectionContainerMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $protectionContainer
   ```

## <a name="step-7-enable-vm-protection"></a>Passaggio 7: Abilitare la protezione della macchina virtuale

1. Recuperare l'elemento di protezione corrispondente alla macchina virtuale da proteggere, come segue:

   ```azurepowershell
   $VMFriendlyName = "Fabrikam-app"          #Name of the VM
   $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
   ```

1. Proteggere la macchina virtuale. Se alla macchina virtuale da proteggere è collegato più di un disco, specificare il disco del sistema operativo usando il parametro **OSDiskName** .

   ```azurepowershell
   $OSType = "Windows"          # "Windows" or "Linux"
   $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID
   ```

1. Attendere che le macchine virtuali raggiungano uno stato protetto dopo la replica iniziale. L’operazione può richiedere un certo tempo a seconda di fattori quali la quantità di dati da replicare e la larghezza di banda upstream disponibile in Azure. Quando viene raggiunto lo stato protetto, i valori State e StateDescription del processo vengono aggiornati come segue:

   ```console
   PS C:\> $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob

   PS C:\> $DRjob | Select-Object -ExpandProperty State
   Succeeded

   PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
   Completed
   ```

1. Aggiornare le proprietà di ripristino, ad esempio le dimensioni del ruolo VM, e la rete di Azure a cui alleghi la scheda di interfaccia di rete della macchina virtuale dopo il failover.

   ```console
   PS C:\> $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

   PS C:\> $VMFriendlyName = "Fabrikam-App"

   PS C:\> $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   PS C:\> $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

   PS C:\> $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

   PS C:\> $UpdateJob | Select-Object -ExpandProperty state

   PS C:\> Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state

   Succeeded
   ```

> [!NOTE]
> Se si vuole eseguire la replica in dischi gestiti abilitati per CMK in Azure, seguire questa procedura con AZ PowerShell 3.3.0 e versioni successive:
>
> 1. Abilitare il failover a Managed disks aggiornando le proprietà della macchina virtuale
> 1. Usare il cmdlet `Get-AzRecoveryServicesAsrReplicationProtectedItem` per recuperare l'ID del disco per ogni disco dell'elemento protetto
> 1. Creare un oggetto Dictionary usando `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` cmdlet per contenere il mapping dell'ID disco al set di crittografia del disco. Questi set di crittografia del disco devono essere creati in precedenza dall'utente nell'area di destinazione.
> 1. Aggiornare le proprietà della macchina virtuale usando il cmdlet `Set-AzRecoveryServicesAsrReplicationProtectedItem` passando l'oggetto dizionario nel parametro **DiskIdToDiskEncryptionSetMap** .

## <a name="step-8-run-a-test-failover"></a>Passaggio 8: Eseguire un failover di test

1. Eseguire un failover di test come segue:

   ```azurepowershell
   $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

   $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   $TFjob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
   ```

1. Verificare che la VM di test sia creata in Azure. Il processo di failover di test viene sospeso dopo la creazione della macchina virtuale di test in Azure.
1. Per pulire e completare il failover di test, eseguire:

   ```azurepowershell
   $TFjob = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"
   ```

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](/powershell/module/az.recoveryservices) su Azure Site Recovery con i cmdlet PowerShell per Azure Resource Manager.
