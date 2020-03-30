---
title: Eseguire il backup dei file di Azure con PowerShellBack up Azure Files with PowerShell
description: In questo articolo viene illustrato come eseguire il backup di File di Azure usando il servizio Backup di Azure e PowerShell.In this article s in learn how to back up Azure Files using the Azure Backup service and PowerShell.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: f85451e0da6458de34aea936836b46781f4c4a21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273540"
---
# <a name="back-up-azure-files-with-powershell"></a>Eseguire il backup dei file di Azure con PowerShellBack up Azure Files with PowerShell

Questo articolo descrive come usare Azure PowerShell per eseguire il backup di una condivisione file file di Azure usando un insieme di credenziali di Servizi di ripristino di [Azure Backup.This](backup-overview.md) article describes how to use Azure PowerShell to back up an Azure Files file share using an Azure Backup Recovery Services vault.

Questo articolo spiega come:

> [!div class="checklist"]
>
> * Configurare PowerShell e registrare il provider di servizi di ripristino di Azure.Set up PowerShell and register the Azure Recovery Services Provider.
> * Creare un insieme di credenziali dei servizi di ripristino.
> * Configurare il backup per una condivisione file di Azure.Configure backup for an Azure file share.
> * Eseguire un processo di backup.

## <a name="before-you-start"></a>Prima di iniziare

* [Ulteriori informazioni](backup-azure-recovery-services-vault-overview.md) sugli insiemi di credenziali dei servizi di ripristino.
* Informazioni sulle funzionalità di anteprima per [il backup delle condivisioni file di Azure.](backup-afs.md)
* Esaminare la gerarchia di oggetti di PowerShell per Servizi di ripristino.

## <a name="recovery-services-object-hierarchy"></a>Gerarchia di oggetti dei servizi di ripristino

La gerarchia degli oggetti è riepilogata nel diagramma seguente.

![Gerarchia di oggetti dei servizi di ripristino](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Esaminare le informazioni di riferimento sui cmdlet **Az.RecoveryServices** nella libreria di Azure.Review the Az.RecoveryServices [cmdlet reference](/powershell/module/az.recoveryservices) reference in the Azure library.

## <a name="set-up-and-install"></a>Configurare e installare

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Configurare PowerShell come segue:Set up PowerShell as follows:

1. [Scaricare la versione più recente di Az PowerShell](/powershell/azure/install-az-ps). La versione 1.0.0 è la versione minima necessaria.

> [!WARNING]
> La versione minima di PS richiesta per l'anteprima era 'Az 1.0.0'. A causa delle modifiche imminenti per GA, la versione minima di PS richiesta sarà 'Az.RecoveryServices 2.6.0'. È molto importante aggiornare tutte le versioni PS esistenti a questa versione. In caso contrario, gli script esistenti verranno interrotti dopo GA. Installare la versione minima con i seguenti comandi PS

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

2. Trovare i cmdlet di Azure Backup PowerShell con questo comando:Find the Azure Backup PowerShell cmdlets with this command:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Esaminare gli alias e i cmdlet per Azure Backup, Azure Site Recovery e l'insieme di credenziali di Servizi di ripristino. Ecco un esempio di ciò che potresti vedere. Non è un elenco completo di cmdlet.

    ![Elenco di cmdlet dei Servizi di ripristino](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Accedere all'account Azure con **Connect-AzAccount**.
5. Nella pagina Web visualizzata viene richiesto di immettere le credenziali dell'account.

    * In alternativa, è possibile includere le credenziali dell'account come parametro nel cmdlet **Connect-AzAccount** con **-Credential**.
    * Se si è un partner CSP che lavora per conto di un tenant, specificare il cliente come tenant, usando il relativo tenantID o il nome di dominio primario del tenant. Un esempio è **Connect-AzAccount -Tenant** fabrikam.com.

6. Associare la sottoscrizione che si vuole usare all'account, perché un account può avere più sottoscrizioni.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Se si sta usando Backup di Azure per la prima volta, usare il cmdlet **Register-AzResourceProvider** per registrare il provider di Servizi di ripristino di Azure con la propria sottoscrizione.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Verificare che i provider siano stati registrati correttamente:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Nell'output del comando verificare che **RegistrationState** sia modificato in **Registered**. In caso contrario, eseguire nuovamente il cmdlet **Register-AzResourceProvider.**

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

L'insieme di credenziali di Servizi di ripristino è una risorsa di Resource Manager, quindi è necessario inserirlo all'interno di un gruppo di risorse. È possibile usare un gruppo di risorse esistente o crearne uno con il cmdlet **New-AzResourceGroup**. Quando si crea un nuovo gruppo di risorse, è necessario specificare il nome e il percorso per il gruppo di risorse.

Seguire questa procedura per creare un insieme di credenziali di Servizi di ripristino.

1. Un insieme di credenziali viene inserito in un gruppo di risorse. Se non si dispone di un gruppo di risorse esistente, crearne uno nuovo con [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). In questo esempio viene creato un nuovo gruppo di risorse nell'area Stati Uniti occidentali.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. Utilizzare il cmdlet [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) per creare l'insieme di credenziali. Specificare per l'insieme di credenziali lo stesso percorso usato per il gruppo di risorse.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Specificare il tipo di ridondanza da utilizzare per l'archiviazione dell'insieme di credenziali.

   * È possibile usare l'[archiviazione con ridondanza locale](../storage/common/storage-redundancy-lrs.md) o l'[archiviazione con ridondanza geografica](../storage/common/storage-redundancy-grs.md).
   * Nell'esempio seguente viene impostata l'opzione **-BackupStorageRedundancy** per il cmd[Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) per **testvault** impostato su **GeoRedundant**.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Visualizzare gli insiemi di credenziali in un abbonamento

Per visualizzare tutti gli insiemi di credenziali disponibili nella sottoscrizione, usare [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

L'output è simile al seguente. Si noti che vengono forniti il gruppo di risorse e la posizione associati.

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

Memorizzare l'oggetto del vault in una variabile e impostare il contesto del vault.

* Molti cmdlet di Backup di Azure richiedono l'oggetto insieme di credenziali di Servizi di ripristino come input, pertanto è conveniente archiviare l'oggetto insieme di credenziali in una variabile.
* Il contesto dell'insieme di credenziali definisce il tipo di dati protetti nell'insieme di credenziali. Impostarlo con [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Dopo aver impostato, il contesto viene applicato a tutti i cmdlet successivi.

L'esempio seguente imposta il contesto per **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Recuperare l'ID del vault

Si prevede di deprecare l'impostazione del contesto dell'insieme di credenziali in conformità con le linee guida di Azure PowerShell.We plan to deprecating the vault context setting in accordance with Azure PowerShell guidelines. È invece possibile memorizzare o recuperare l'ID dell'insieme di credenziali e passarlo ai comandi pertinenti. Pertanto, se non è stato impostato il contesto del vault o si desidera specificare il comando da eseguire per un determinato vault, passare l'ID del vault come "-vaultID" a tutti i comandi pertinenti come segue:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Configurare un criterio di backup

Un criterio di backup specifica la pianificazione per i backup e per quanto tempo devono essere mantenuti i punti di ripristino del backup:

* I criteri di backup sono associati ai criteri di conservazione. Un criterio di conservazione definisce per quanto tempo un punto di recupero viene mantenuto prima dell'eliminazione.
* Visualizzare la conservazione dei criteri di backup predefinita utilizzando [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Visualizzare la pianificazione predefinita dei criteri di backup utilizzando [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Utilizzare il cmdlet [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) per creare un nuovo criterio di backup. Immettere gli oggetti criteri di pianificazione e conservazione.

Per impostazione predefinita, un'ora di inizio è definita nell'oggetto Criteri di pianificazione. Utilizzare l'esempio seguente per modificare l'ora di inizio con l'ora di inizio desiderata. L'ora di inizio desiderata dovrebbe essere in UTC pure. Nell'esempio seguente si presuppone che l'ora di inizio desiderata sia 01:00 AM UTC per i backup giornalieri.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> È necessario fornire l'ora di inizio solo in multipli di 30 minuti. Nell'esempio precedente, può essere solo "01:00:00" o "02:30:00". L'ora di inizio non può essere "01:15:00"

Nell'esempio seguente i criteri di pianificazione e i criteri di conservazione vengono archiviati nelle variabili. Tali variabili vengono quindi utilizzate come parametri per un nuovo criterio (**NewAFSPolicy**). **NewAFSPolicy** effettua un backup giornaliero e lo conserva per 30 giorni.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

L'output è simile al seguente.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Abilita backup

Dopo aver definito i criteri di backup, è possibile abilitare la protezione per la condivisione file di Azure usando i criteri.

### <a name="retrieve-a-backup-policy"></a>Recuperare un criterio di backupRetrieve a backup policy

Recuperare l'oggetto criterio pertinente con [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Utilizzare questo cmdlet per ottenere un criterio specifico o per visualizzare i criteri associati a un tipo di carico di lavoro.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Recuperare un criterio per un tipo di carico di lavoroRetrieve a policy for a workload type

Nell'esempio seguente vengono recuperati i criteri per il tipo di carico di lavoro AzureFiles.The following example retrieves policies for the workload type **AzureFiles.**

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

L'output è simile al seguente.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> Il fuso orario del campo **BackupTime** in PowerShell corrisponde all'ora UTC (Coordinated Universal Time). L'orario di backup nel portale di Azure è allineato al fuso orario locale.

### <a name="retrieve-a-specific-policy"></a>Recuperare un criterio specificoRetrieve a specific policy

Il criterio seguente recupera il criterio di backup denominato **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Abilitare il backup e applicare i criteri

Abilitare la protezione con [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Dopo che il criterio è associato all'insieme di credenziali, i backup vengono attivati in base alla pianificazione del criterio.

Nell'esempio seguente viene abilitata la protezione per il test della condivisione file di **AzureAzureFileShare** nell'account di archiviazione **testStorageAcct**, con i criteri **dailyafs**.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

Il comando attende fino a quando il processo di protezione della configurazione non viene completato e fornisce un output simile al seguente.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="important-notice---backup-item-identification-for-afs-backups"></a>Avviso importante - Identificazione elemento di backup per i backup AFS

In questa sezione viene descritta un'importante modifica nel backup AFS in preparazione per GA.

Durante l'abilitazione del backup per AFS, l'utente fornisce il nome di condivisione file descrittivo del cliente come nome dell'entità e viene creato un elemento di backup. Il 'nome' dell'elemento di backup è un identificatore univoco creato dal servizio Backup di Azure.The backup item's 'name' is a unique identifier created by Azure Backup service. In genere l'identificatore comporta il nome descrittivo dell'utente. Ma per gestire lo scenario importante di eliminazione temporanea, in cui è possibile eliminare una condivisione file e creare un'altra condivisione file con lo stesso nome, l'identità univoca della condivisione file di Azure sarà ora un ID anziché un nome descrittivo del cliente. Per conoscere l'identità/nome univoco di ogni ```Get-AzRecoveryServicesBackupItem``` elemento, è sufficiente eseguire il comando con i filtri pertinenti per backupManagementType e WorkloadType per ottenere tutti gli elementi rilevanti e quindi osservare il campo del nome nell'oggetto/risposta PS restituito. È sempre consigliabile elencare gli elementi e quindi recuperare il nome univoco dal campo 'nome' in risposta. Utilizzare questo valore per filtrare gli elementi con il parametro 'Name'. In caso contrario, utilizzare il parametro FriendlyName per recuperare l'elemento con il nome descrittivo del cliente o l'identificatore.

> [!WARNING]
> Assicurarsi che la versione PS sia aggiornata alla versione minima per 'Az.RecoveryServices 2.6.0' per i backup AFS. Con questa versione, il filtro 'friendlyName' è disponibile per ```Get-AzRecoveryServicesBackupItem``` il comando. Passare il nome della condivisione file di Azure al parametro friendlyName. Se si passa il nome della condivisione file di Azure al parametro 'Name', questa versione genera un avviso per passare questo nome descrittivo al parametro del nome descrittivo. La mancata installazione di questa versione minima potrebbe causare il mancato rispetto degli script esistenti. Installare la versione minima di PS con il seguente comando.

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

## <a name="trigger-an-on-demand-backup"></a>Attivare un backup su richiesta

Usare [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) per eseguire un backup su richiesta per una condivisione file di Azure protetta.

1. Recuperare l'account di archiviazione dal contenitore nell'insieme di credenziali che contiene i dati di backup con [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Per avviare un processo di backup, ottenere informazioni sulla condivisione file di Azure con [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Eseguire un backup su richiesta con [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Eseguire il backup su richiesta come segue:

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

Il comando restituisce un processo con un ID di cui tenere traccia, come mostrato nell'esempio seguente.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Durante l'esecuzione dei backup vengono usati gli snapshot di condivisione file di Azure, quindi in genere il processo viene completato nel momento in cui il comando restituisce l'output.

### <a name="using-on-demand-backups-to-extend-retention"></a>Utilizzo di backup su richiesta per estendere la conservazione

I backup su richiesta possono essere utilizzati per conservare gli snapshot per 10 anni. Le utilità di pianificazione possono essere utilizzate per eseguire script PowerShell su richiesta con la conservazione scelta e quindi scattare snapshot a intervalli regolari ogni settimana, mese o anno. Durante l'acquisizione di snapshot regolari, fare riferimento alle limitazioni dei backup su richiesta tramite il backup di Azure.While taking regular snapshots, refer to the [limitations of on-demand backups](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share) using Azure backup.

Se si cercano script di esempio, è possibile fare<https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup>riferimento allo script di esempio in GitHub ( ) usando il runbook di Automazione di Azure che consente di pianificare i backup su base periodica e di conservarli anche fino a 10 anni.

> [!WARNING]
> Assicurarsi che la versione PS sia aggiornata alla versione minima per 'Az.RecoveryServices 2.6.0' per i backup AFS nei runbook di automazione. Sarà necessario sostituire il vecchio modulo 'AzureRM' con il modulo 'Az'. Con questa versione, il filtro 'friendlyName' è disponibile per ```Get-AzRecoveryServicesBackupItem``` il comando. Passare il nome della condivisione file di azure al parametro friendlyName. Se si passa il nome della condivisione file di azure al parametro 'Name', questa versione genera un avviso per passare questo nome descrittivo al parametro del nome descrittivo.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sul](backup-afs.md) backup di File di Azure nel portale di Azure.Learn about backing up Azure Files in the Azure portal.
