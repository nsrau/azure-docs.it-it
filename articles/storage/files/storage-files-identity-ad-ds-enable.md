---
title: Abilitare l'autenticazione AD DS per le condivisioni file di Azure
description: Informazioni su come abilitare l'autenticazione Active Directory Domain Services su SMB per le condivisioni file di Azure. Le macchine virtuali Windows appartenenti a un dominio possono quindi accedere alle condivisioni file di Azure usando le credenziali di servizi di dominio Active Directory.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/13/2020
ms.author: rogarana
ms.openlocfilehash: b125ae506a9811b8e80a9114e31effc1933c114d
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91821211"
---
# <a name="part-one-enable-ad-ds-authentication-for-your-azure-file-shares"></a>Parte 1: abilitare l'autenticazione di servizi di dominio Active Directory per le condivisioni file di Azure 

Prima di abilitare l'autenticazione Active Directory Domain Services (AD DS), assicurarsi di aver letto l' [articolo introduttivo](storage-files-identity-auth-active-directory-enable.md) per comprendere gli scenari e i requisiti supportati.

Questo articolo descrive il processo necessario per abilitare l'autenticazione Active Directory Domain Services (AD DS) nell'account di archiviazione. Dopo aver abilitato la funzionalità, è necessario configurare l'account di archiviazione e i servizi di dominio Active Directory per usare le credenziali di servizi di dominio Active Directory per l'autenticazione nella condivisione file di Azure. Per abilitare l'autenticazione di servizi di dominio Active Directory tramite SMB per le condivisioni file di Azure, è necessario registrare l'account di archiviazione con servizi di dominio Active Directory e quindi impostare le proprietà del dominio richieste nell'account di archiviazione.

Per registrare l'account di archiviazione con servizi di dominio Active Directory, creare un account che lo rappresenti in servizi di dominio Active Directory. Questo processo può essere considerato come la creazione di un account che rappresenta un file server Windows locale in servizi di dominio Active Directory. Quando la funzionalità è abilitata nell'account di archiviazione, viene applicata a tutte le condivisioni file nuove ed esistenti nell'account.

## <a name="option-one-recommended-use-azfileshybrid-powershell-module"></a>Opzione 1 (scelta consigliata): usare il modulo PowerShell AzFilesHybrid

I cmdlet nel modulo AzFilesHybrid di PowerShell effettuano le modifiche necessarie e abilitano la funzionalità. Poiché alcune parti dei cmdlet interagiscono con Active Directory Domain Services locale, viene illustrato il funzionamento dei cmdlet, pertanto è possibile determinare se le modifiche sono allineate ai criteri di conformità e sicurezza e assicurarsi di disporre delle autorizzazioni appropriate per eseguire i cmdlet. Sebbene sia consigliabile usare il modulo AzFilesHybrid, se non è possibile eseguire questa operazione, vengono forniti i passaggi necessari per eseguirli manualmente.

### <a name="download-azfileshybrid-module"></a>Scaricare il modulo AzFilesHybrid

- [Scaricare e decomprimere il modulo AzFilesHybrid (modulo GA: v 0.2.0 +)](https://github.com/Azure-Samples/azure-files-samples/releases) Si noti che la crittografia Kerberos AES 256 è supportata in 0.2.2 o versione successiva. Se è stata abilitata la funzionalità con una versione di AzFilesHybrid inferiore a v 0.2.2 e si vuole eseguire l'aggiornamento per supportare la crittografia Kerberos AES 256, fare riferimento a [questo articolo](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption). 
- Installare ed eseguire il modulo in un dispositivo che è aggiunto a un dominio ad DS locale con le credenziali di servizi di dominio Active Directory che dispongono delle autorizzazioni per creare un account di accesso al servizio o un account computer nell'annuncio di destinazione.
-  Eseguire lo script con una credenziale AD DS locale sincronizzata con la Azure AD. Le credenziali di servizi di dominio Active Directory locali devono avere il proprietario dell'account di archiviazione o le autorizzazioni del ruolo di Azure collaboratore.

### <a name="run-join-azstorageaccountforauth"></a>Eseguire join-AzStorageAccountForAuth

Il `Join-AzStorageAccountForAuth` cmdlet esegue l'equivalente di un join di dominio offline per conto dell'account di archiviazione specificato. Lo script usa il cmdlet per creare un [account computer](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) nel dominio ad. Se per qualsiasi motivo non è possibile utilizzare un account computer, è possibile modificare lo script per creare un [account di accesso al servizio](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) . Se si sceglie di eseguire il comando manualmente, è necessario selezionare l'account più appropriato per l'ambiente in uso.

L'account di Active Directory Domain Services creato dal cmdlet rappresenta l'account di archiviazione. Se l'account di servizi di dominio Active Directory viene creato in un'unità organizzativa (OU) che impone la scadenza della password, è necessario aggiornare la password prima della validità massima della password. Se non si aggiorna la password dell'account prima di tale data, si verificano errori di autenticazione durante l'accesso alle condivisioni file di Azure. Per informazioni su come aggiornare la password, vedere aggiornare la password dell' [account Active Directory](storage-files-identity-ad-ds-update-password.md)Domain Services.

Sostituire i valori segnaposto con quelli personalizzati nei parametri riportati di seguito prima di eseguirli in PowerShell.
> [!IMPORTANT]
> Il cmdlet Domain Join creerà un account AD per rappresentare l'account di archiviazione (condivisione file) in Active Directory. È possibile scegliere di eseguire la registrazione come account computer o account di accesso al servizio. per informazioni dettagliate, vedere le [domande frequenti](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) . Per gli account computer, esiste una scadenza della password predefinita impostata in Active Directory a 30 giorni. Analogamente, è possibile che l'account di accesso del servizio disponga di una data di scadenza della password predefinita impostata sul dominio AD o sull'unità organizzativa (OU).
> Per entrambi i tipi di account, è consigliabile verificare la scadenza della password configurata nell'ambiente di Active Directory e pianificare [l'aggiornamento della password dell'identità dell'account di archiviazione](storage-files-identity-ad-ds-update-password.md) dell'account di Active Directory prima della validità massima della password. È possibile prendere [in considerazione la creazione di una nuova unità organizzativa ad (OU) in Active Directory e la](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) disabilitazione dei criteri di scadenza delle password per [account computer](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN) o account di accesso del servizio di conseguenza. 

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer Azure role assignment
Connect-AzAccount

#Define parameters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account (default parameter value), depends on the AD permission you have and preference. 
# Run Get-Help Join-AzStorageAccountForAuth for more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -StorageAccountName $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" <# Default is set as ComputerAccount #> `
        -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" <# If you don't provide the OU name as an input parameter, the AD identity that represents the storage account is created under the root directory. #> `
        -EncryptionType "<AES,RC4/AES/RC4>" <# Specify the encryption agorithm used for Kerberos authentication. Default is configured as "'RC4','AES256'" which supports both 'RC4' and 'AES256' encryption. #>

#Run the command below if you want to enable AES 256 authentication. If you plan to use RC4, you can skip this step.
Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, see Azure Files Windows troubleshooting guide.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="option-2-manually-perform-the-enablement-actions"></a>Opzione 2: eseguire manualmente le azioni di abilitazione

Se lo script è già stato eseguito `Join-AzStorageAccountForAuth` correttamente, passare alla sezione verificare che la [funzionalità sia abilitata](#confirm-the-feature-is-enabled) . Non è necessario eseguire i passaggi manuali seguenti.

### <a name="checking-environment"></a>Controllo dell'ambiente

Prima di tutto, è necessario controllare lo stato dell'ambiente. In particolare, è necessario controllare se [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) è installato e se la shell viene eseguita con privilegi di amministratore. Verificare quindi se è installato il [modulo Az.Storage 2.0](https://www.powershellgallery.com/packages/Az.Storage/2.0.0), se non lo è installarlo. Al termine di questi controlli, controllare i servizi di dominio Active Directory per verificare se è presente un [account computer](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (impostazione predefinita) o un [account di accesso al servizio](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) che è già stato creato con SPN/UPN come "CIFS/your-storage-account-name-here. file. Core. Windows. NET". Se l'account non esiste, crearne uno come descritto nella sezione seguente.

### <a name="creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>Creazione manuale di un'identità che rappresenta l'account di archiviazione nell'annuncio

Per creare questo account manualmente, creare una nuova chiave Kerberos per l'account di archiviazione. Usare quindi la chiave Kerberos come password per l'account con i cmdlet di PowerShell riportati di seguito. Questa chiave viene usata solo durante l'installazione e non può essere usata per le operazioni di controllo o di piano dati nell'account di archiviazione. 

```PowerShell
# Create the Kerberos key on the storage account and get the Kerb1 key as the password for the AD identity to represent the storage account
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

New-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -KeyName kerb1
Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -ListKerbKey | where-object{$_.Keyname -contains "kerb1"}
```

Una volta creata la chiave, creare un account del servizio o del computer nell'unità organizzativa. Usare la specifica seguente (ricordare di sostituire il testo di esempio con il nome dell'account di archiviazione):

Nome SPN: "CIFS/your-storage-account-name-here. file. Core. Windows. NET" password: chiave Kerberos per l'account di archiviazione.

Se l'unità organizzativa impone la scadenza della password, è necessario aggiornare la password prima della validità massima della password per evitare errori di autenticazione durante l'accesso alle condivisioni file di Azure. Per informazioni dettagliate, vedere [aggiornare la password dell'identità dell'account di archiviazione in Active Directory](storage-files-identity-ad-ds-update-password.md) .

Conserva il SID dell'identità appena creata, sarà necessario per il passaggio successivo. L'identità creata che rappresenta l'account di archiviazione non deve essere sincronizzata con Azure AD.

### <a name="enable-the-feature-on-your-storage-account"></a>Abilitare la funzionalità nell'account di archiviazione

A questo punto è possibile abilitare la funzionalità nell'account di archiviazione. Fornire alcuni dettagli di configurazione per le proprietà del dominio nel comando seguente, quindi eseguirlo. Il SID dell'account di archiviazione richiesto nel comando seguente è il SID dell'identità creata in servizi di dominio Active Directory nella [sezione precedente](#creating-an-identity-representing-the-storage-account-in-your-ad-manually).

```PowerShell
# Set the feature flag on the target storage account and provide the required AD domain information
Set-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>" `
        -EnableActiveDirectoryDomainServicesForFile $true `
        -ActiveDirectoryDomainName "<your-domain-name-here>" `
        -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" `
        -ActiveDirectoryForestName "<your-forest-name-here>" `
        -ActiveDirectoryDomainGuid "<your-guid-here>" `
        -ActiveDirectoryDomainsid "<your-domain-sid-here>" `
        -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```

### <a name="debugging"></a>Debug

È possibile eseguire il cmdlet debug-AzStorageAccountAuth per eseguire un set di controlli di base sulla configurazione di Active Directory con l'utente di Active Directory connesso. Questo cmdlet è supportato nella versione AzFilesHybrid v 0.1.2+. Per ulteriori informazioni sui controlli eseguiti in questo cmdlet, vedere [non è possibile montare file di Azure con le credenziali di Active Directory](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) nella Guida alla risoluzione dei problemi di Windows.

```PowerShell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="confirm-the-feature-is-enabled"></a>Verificare che la funzionalità sia abilitata

È possibile controllare per verificare se la funzionalità è abilitata nell'account di archiviazione con lo script seguente:

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD DS authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

## <a name="next-steps"></a>Passaggi successivi

A questo punto la funzionalità è stata abilitata nell'account di archiviazione. Per utilizzare questa funzionalità, è necessario assegnare le autorizzazioni a livello di condivisione. Passare alla sezione successiva.

[Parte 2: assegnare autorizzazioni a livello di condivisione a un'identità](storage-files-identity-ad-ds-assign-permissions.md)
