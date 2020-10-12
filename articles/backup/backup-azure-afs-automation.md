---
title: Eseguire il backup di una condivisione file di Azure usando PowerShell
description: Questo articolo illustra come eseguire il backup di una condivisione file di File di Azure usando il servizio backup di Azure e PowerShell.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 948931764769bc967b88e7942b7e8384b0f93dff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87077009"
---
# <a name="back-up-an-azure-file-share-by-using-powershell"></a>Eseguire il backup di una condivisione file di Azure usando PowerShell

Questo articolo descrive come usare Azure PowerShell per eseguire il backup di una condivisione file File di Azure tramite un insieme di credenziali di servizi di ripristino di [backup di Azure](backup-overview.md) .

Questo articolo spiega come:

> [!div class="checklist"]
>
> * Configurare PowerShell e registrare il provider di servizi di ripristino.
> * Creare un insieme di credenziali dei servizi di ripristino.
> * Configurare il backup per una condivisione file di Azure.
> * Eseguire un processo di backup.

## <a name="before-you-start"></a>Prima di iniziare

* [Altre](backup-azure-recovery-services-vault-overview.md) informazioni sugli insiemi di credenziali dei servizi di ripristino.
* Esaminare il riferimento al [cmdlet](/powershell/module/az.recoveryservices) AZ. RecoveryServices nella libreria di Azure.
* Esaminare la gerarchia di oggetti di PowerShell seguente per i servizi di ripristino:

  ![Gerarchia di oggetti dei servizi di ripristino](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

## <a name="set-up-powershell"></a>Configurare PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Configurare PowerShell nel modo seguente:

1. [Scaricare la versione più recente di Azure PowerShell](/powershell/azure/install-az-ps).

    > [!NOTE]
    > La versione minima di PowerShell necessaria per il backup delle condivisioni file di Azure è AZ. RecoveryServices 2.6.0. Utilizzando la versione più recente o almeno la versione minima, consente di evitare problemi con gli script esistenti. Installare la versione minima usando il comando PowerShell seguente:
    >
    > ```powershell
    > Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
    > ```

2. Trovare i cmdlet di PowerShell per backup di Azure usando questo comando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Esaminare gli alias e i cmdlet per backup di Azure, Azure Site Recovery e l'insieme di credenziali di servizi di ripristino. Ecco un esempio di ciò che è possibile vedere. Non si tratta di un elenco completo di cmdlet.

    ![Elenco di cmdlet dei Servizi di ripristino](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Accedere all'account Azure usando **Connect-AzAccount**.
5. Nella pagina Web visualizzata verrà richiesto di immettere le credenziali dell'account.

    In alternativa, è possibile includere le credenziali dell'account come parametro nel cmdlet **Connect-AzAccount** tramite **-Credential**.

    Se si è un partner CSP che lavora per conto di un tenant, specificare il cliente come tenant. Usare l'ID tenant o il nome di dominio primario del tenant. Un esempio è **Connect-AzAccount-tenant "fabrikam.com"**.

6. Associare la sottoscrizione che si desidera utilizzare con l'account, perché un account può disporre di più sottoscrizioni:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Se si usa backup di Azure per la prima volta, usare il cmdlet **Register-AzResourceProvider** per registrare il provider di servizi di ripristino di Azure con la sottoscrizione:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Verificare che i provider siano stati registrati correttamente:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Nell'output del comando verificare che **RegistrationState** venga modificato in **registrato**. In caso contrario, eseguire di nuovo il cmdlet **Register-AzResourceProvider** .

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

L'insieme di credenziali di servizi di ripristino è una risorsa Gestione risorse, quindi è necessario inserirla in un gruppo di risorse. È possibile usare un gruppo di risorse esistente oppure è possibile creare un gruppo di risorse usando il cmdlet **New-AzResourceGroup** . Quando si crea un gruppo di risorse, specificarne il nome e il percorso.

Per creare un insieme di credenziali di servizi di ripristino, seguire questa procedura:

1. Se non si dispone di un gruppo di risorse esistente, crearne uno nuovo usando il cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . In questo esempio viene creato un gruppo di risorse nell'area Stati Uniti occidentali:

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

1. Usare il cmdlet [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) per creare l'insieme di credenziali. Specificare lo stesso percorso per l'insieme di credenziali usato per il gruppo di risorse.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Visualizzare gli insiemi di credenziali in un abbonamento

Per visualizzare tutti gli insiemi di credenziali nella sottoscrizione, usare [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault):

```powershell
Get-AzRecoveryServicesVault
```

L'output è simile al seguente. Si noti che l'output fornisce il gruppo di risorse e la località associati.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Impostare il contesto dell'insieme di credenziali

Archiviare l'oggetto insieme di credenziali in una variabile e impostare il contesto dell'insieme di credenziali.

Molti cmdlet di backup di Azure richiedono l'oggetto insieme di credenziali dei servizi di ripristino come input, quindi è consigliabile archiviare l'oggetto insieme di credenziali in una variabile.

Il contesto dell'insieme di credenziali definisce il tipo di dati protetti nell'insieme di credenziali. Impostarlo utilizzando [set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext). Una volta impostato il contesto, si applica a tutti i cmdlet successivi.

Nell'esempio seguente viene impostato il contesto dell'insieme di credenziali per **testvault**:

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Recuperare l'ID dell'insieme di credenziali

Si prevede di deprecare l'impostazione del contesto dell'insieme di credenziali in base alle linee guida Azure PowerShell. È invece possibile archiviare o recuperare l'ID dell'insieme di credenziali e passarlo ai comandi pertinenti. Se il contesto dell'insieme di credenziali non è stato impostato o si vuole specificare il comando da eseguire per un determinato insieme di credenziali, passare l'ID dell'insieme di credenziali come `-vaultID` tutti i comandi pertinenti, come indicato di seguito:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Configurare un criterio di backup

Un criterio di backup specifica la pianificazione per i backup e il periodo di tempo in cui devono essere conservati i punti di ripristino del backup.

I criteri di backup sono associati ai criteri di conservazione. Un criterio di conservazione definisce per quanto tempo un punto di recupero viene mantenuto prima dell'eliminazione. È possibile configurare i backup con conservazione giornaliera, settimanale, mensile o annuale.

Di seguito sono riportati alcuni cmdlet per i criteri di backup:

* Visualizzare il periodo di conservazione predefinito dei criteri di backup usando [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject).
* Visualizzare la pianificazione predefinita dei criteri di backup usando [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject).
* Creare un nuovo criterio di backup usando [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy). Immettere gli oggetti Criteri di pianificazione e conservazione come input.

Per impostazione predefinita, nell'oggetto Criteri di pianificazione è definita un'ora di inizio. Usare l'esempio seguente per modificare l'ora di inizio con l'ora di inizio desiderata. L'ora di inizio desiderata deve essere in formato UTC (Universal Coordinated Time). Nell'esempio si presuppone che l'ora di inizio desiderata sia 01:00 UTC per i backup giornalieri.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> È necessario specificare l'ora di inizio solo in più di 30 minuti. Nell'esempio precedente può essere solo "01:00:00" o "02:30:00". L'ora di inizio non può essere "01:15:00".

Nell'esempio seguente i criteri di pianificazione e i criteri di conservazione vengono archiviati nelle variabili. USA quindi tali variabili come parametri per un nuovo criterio (**NewAFSPolicy**). **NewAFSPolicy** effettua un backup giornaliero e lo conserva per 30 giorni.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

L'output è simile al seguente:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Abilita backup

Dopo aver definito i criteri di backup, è possibile abilitare la protezione per la condivisione file di Azure usando il criterio.

### <a name="retrieve-a-backup-policy"></a>Recuperare un criterio di backup

Per recuperare l'oggetto criteri pertinente, usare [Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy). Utilizzare questo cmdlet per visualizzare i criteri associati a un tipo di carico di lavoro o per ottenere un criterio specifico.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Recuperare i criteri per un tipo di carico di lavoro

Nell'esempio seguente vengono recuperati i criteri per il tipo di carico di lavoro **risorsa**:

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

L'output è simile al seguente:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> Il fuso orario del campo **BackupTime** in PowerShell è UTC. L'orario di backup nel portale di Azure è allineato al fuso orario locale.

#### <a name="retrieve-a-specific-policy"></a>Recuperare un criterio specifico

Il criterio seguente recupera il criterio di backup denominato **dailyafs**:

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-protection-and-apply-the-policy"></a>Abilitare la protezione e applicare il criterio

Abilitare la protezione usando [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Dopo che il criterio è stato associato all'insieme di credenziali, i backup vengono attivati in base alla pianificazione dei criteri.

L'esempio seguente abilita la protezione per la condivisione file di Azure **testAzureFileShare** nell'account di archiviazione **testStorageAcct**, con i criteri **dailyafs**:

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

Il comando attende fino al termine del processo di configurazione della protezione e fornisce un output simile all'esempio seguente:

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

Per altre informazioni su come ottenere un elenco di condivisioni file per un account di archiviazione, vedere [questo articolo](/powershell/module/az.storage/get-azstorageshare).

## <a name="important-notice-backup-item-identification"></a>Avviso importante: identificazione dell'elemento di backup

In questa sezione viene illustrata una modifica importante nei backup delle condivisioni file di Azure in preparazione per la disponibilità generale.

Quando si Abilita un backup per le condivisioni file di Azure, l'utente assegna al cliente un nome di condivisione file come nome dell'entità e viene creato un elemento di backup. Il nome dell'elemento di backup è un identificatore univoco creato dal servizio backup di Azure. L'identificatore è in genere un nome descrittivo. Tuttavia, per gestire lo scenario di eliminazione temporanea, in cui è possibile eliminare una condivisione file ed è possibile creare un'altra condivisione file con lo stesso nome, l'identità univoca di una condivisione file di Azure è ora un ID.

Per individuare l'ID univoco di ogni elemento, eseguire il comando **Get-AzRecoveryServicesBackupItem** con i filtri rilevanti per **backupManagementType** e **WorkloadType** per ottenere tutti gli elementi pertinenti. Osservare quindi il campo nome nell'oggetto/risposta PowerShell restituito.

Si consiglia di elencare gli elementi e recuperare il nome univoco dal campo nome nella risposta. Utilizzare questo valore per filtrare gli elementi con il parametro *Name* . In caso contrario, usare il parametro *FriendlyName* per recuperare l'elemento con il relativo ID.

> [!IMPORTANT]
> Assicurarsi che PowerShell sia aggiornato alla versione minima (AZ. RecoveryServices 2.6.0) per i backup delle condivisioni file di Azure. Con questa versione, il filtro *FriendlyName* è disponibile per il comando **Get-AzRecoveryServicesBackupItem** .
>
> Passare il nome della condivisione file di Azure al parametro *FriendlyName* . Se si passa il nome della condivisione file al parametro *Name* , questa versione genera un avviso per passare il nome al parametro *FriendlyName* .
>
> Se non si installa la versione minima, potrebbe verificarsi un errore di script esistenti. Installare la versione minima di PowerShell usando il comando seguente:
>
>```powershell
>Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
>```

## <a name="trigger-an-on-demand-backup"></a>Attivare un backup su richiesta

Usare [backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) per eseguire un backup su richiesta per una condivisione file di Azure protetta:

1. Recuperare l'account di archiviazione dal contenitore nell'insieme di credenziali che include i dati di backup usando [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Per avviare un processo di backup, ottenere informazioni sulla condivisione file di Azure usando [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Eseguire un backup su richiesta con [backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Eseguire il backup su richiesta nel modo seguente:

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

Il comando restituisce un processo con un ID da rilevare, come illustrato nell'esempio seguente:

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Gli snapshot di condivisione file di Azure vengono usati durante l'esecuzione dei backup. In genere il processo viene completato nel momento in cui il comando restituisce l'output.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul [backup di file di Azure nel portale di Azure](backup-afs.md).
* Vedere lo [script di esempio in GitHub](https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup) per l'uso di un Runbook di automazione di Azure per pianificare i backup.
