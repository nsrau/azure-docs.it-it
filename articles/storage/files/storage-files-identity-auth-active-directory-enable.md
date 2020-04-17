---
title: Abilitare l'autenticazione di Active Directory su SMB per file di AzureEnable Active Directory authentication over SMB for Azure Files
description: Informazioni su come abilitare l'autenticazione basata sull'identità su SMB per le condivisioni file di Azure tramite Active Directory.Learn how to enable identity-based authentication over SMB for Azure file shares through Active Directory. Le macchine virtuali Windows (VM) aggiunte al dominio possono quindi accedere alle condivisioni file di Azure usando le credenziali di Active Directory.Your domain-joined Windows virtual machines (VMs) can then access Azure file shares by using AD credentials.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: rogarana
ms.openlocfilehash: 8d1e1262c592f0120b191e18a5c16b97b887a6a2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536532"
---
# <a name="enable-on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Abilitare l'autenticazione di Servizi di dominio Active Directory locale tramite SMB per le condivisioni file di AzureEnable on-premises Active Directory Domain Services authentication over SMB for Azure file shares

[File di](storage-files-introduction.md) Azure supporta l'autenticazione basata sull'identità tramite SMB (Server Message Block) tramite due tipi di Servizi di dominio: Servizi di dominio Azure Active Directory (Azure AD DS) e Servizi di dominio Active Directory (anteprima) locali. Questo articolo è incentrato sul supporto appena introdotto (anteprima) di sfruttare il servizio di dominio Active Directory per l'autenticazione in condivisioni file di Azure.This article focuses on the newly introduced (preview) support of leveraging Active Directory Domain Service for authentication to Azure file shares. Se si è interessati all'abilitazione dell'autenticazione di Azure AD DS (GA) per le condivisioni file di Azure, vedere [l'articolo sull'argomento.](storage-files-identity-auth-active-directory-domain-service-enable.md)

> [!NOTE]
> Le condivisioni file di Azure supportano l'autenticazione solo in un servizio di dominio, ovvero Servizio di dominio Azure Active Directory (Azure AD DS) o Servizi di dominio Active Directory locale. 
>
> Le identità di Servizi di dominio Active Directory usate per l'autenticazione della condivisione file di Azure devono essere sincronizzate con Azure AD. La sincronizzazione dell'hash delle password è facoltativa. 
> 
> L'autenticazione di Servizi di dominio Active Directory locale non supporta l'autenticazione in base agli account computer creati in Servizi di dominio Active Directory. 
> 
> L'autenticazione di Servizi di dominio Active Directory locale può essere supportata solo in una foresta di Active Directory in cui è registrato l'account di archiviazione. Per impostazione predefinita, è possibile accedere alle condivisioni file di Azure solo con le credenziali di Servizi di dominio Active Directory da una singola foresta. Se è necessario accedere alla condivisione file di Azure da una foresta diversa, assicurarsi di avere configurato il trust tra foreste corretto, vedere [le domande frequenti](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) per informazioni dettagliate.  
> 
> L'autenticazione di Servizi di dominio Active Directory per l'accesso SMB e la persistenza ACL è supportata per le condivisioni file di Azure gestite da Sincronizzazione file di Azure.AD DS authentication for SMB access and ACL persistence is supported for Azure file shares managed by Azure File Sync.
>
> File di Azure supporta l'autenticazione Kerberos con Active Directory con crittografia RC4-HMAC. La crittografia Kerberos di AES non è ancora supportata.

Quando si abilita le condivisioni file di Servizi di dominio Active Directory per Azure tramite SMB, i computer aggiunti a Servizi di dominio Active Directory possono montare condivisioni file di Azure usando le credenziali di Active Directory esistenti. Questa funzionalità può essere abilitata con un ambiente Servizi di dominio Active Directory ospitato in computer locali o ospitati in Azure.This capability can be enabled with an AD DS environment hosted either in prem machines or hosted in Azure.

Le identità usate per accedere alle condivisioni file di Azure devono essere sincronizzate con Azure AD per applicare le autorizzazioni dei file a livello di condivisione tramite il modello di controllo degli [accessi in base](../../role-based-access-control/overview.md) al ruolo. [Gli elenchi DACL di tipo Windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) su file/directory trasferiti da file server esistenti verranno mantenuti e applicati. Questa funzionalità offre una perfetta integrazione con l'ambiente di Servizi di dominio Active Directory aziendale. Quando si sostituiscono i file server locali con condivisioni file di Azure, gli utenti esistenti possono accedere alle condivisioni file di Azure dai client correnti con un'esperienza Single Sign-On, senza alcuna modifica alle credenziali in uso.  

> [!NOTE]
> Per facilitare l'installazione dell'autenticazione di Azure Files AD per alcuni casi d'uso comuni, sono stati pubblicati due video con istruzioni dettagliate:To help you setup Azure Files AD authentication for some common use cases, we published [two videos](https://docs.microsoft.com/azure/storage/files/storage-files-introduction#videos) with step by step guidance:
> - Sostituzione di file server locali con File di Azure (inclusa la configurazione sul collegamento privato per i file e l'autenticazione di Active Directory)
> - Uso di File di Azure come contenitore di profili per Desktop virtuale di Windows (inclusa l'installazione in Autenticazione AD e configurazione FsLogix)Using Azure Files as the profile container for Windows Virtual Desktop (including setup on AD authentication and FsLogix configuration)

## <a name="prerequisites"></a>Prerequisiti 

Prima di abilitare l'autenticazione di Servizi di dominio Active Directory per le condivisioni file di Azure, assicurarsi di aver completato i prerequisiti seguenti:Before you enable AD DS authentication for Azure file shares, make sure you have completed the following prerequisites: 

- Selezionare o creare l'ambiente Servizi di dominio Active Directory e [sincronizzarlo con Azure AD.](../../active-directory/hybrid/how-to-connect-install-roadmap.md) 

    È possibile abilitare la funzionalità in un ambiente di Servizi di dominio Active Directory locale nuovo o esistente. Le identità usate per l'accesso devono essere sincronizzate con Azure AD. Il tenant di Azure AD e la condivisione file a cui si accede devono essere associati alla stessa sottoscrizione. 

    Per configurare un ambiente di dominio Active Directory, fare riferimento a [Panoramica](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)di Servizi di dominio Active Directory . Se l'AD non è stata sincronizzata con Azure AD, seguire le indicazioni in Azure AD Connect e la roadmap di installazione di Azure AD Connect Health per configurare e configurare Azure AD Connect.If you have not synced your AD to your Azure AD, follow the guidance in [Azure AD Connect and Azure AD Connect installation roadmap](../../active-directory/hybrid/how-to-connect-install-roadmap.md) to configure and set up Azure AD Connect. 

- Aggiunta di un dominio a un computer locale o a una macchina virtuale di Azure in locale. 

    To access a file share by using AD credentials from a machine or VM, your device must be domain-joined to AD DS. Per informazioni sull'aggiunta a un dominio, vedere [Aggiunta di un computer a un dominio](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain). 

- Selezionare o creare un account di archiviazione di Azure in [un'area supportata.](#regional-availability) 

    Assicurarsi che l'account di archiviazione contenente le condivisioni file non sia già configurato per l'autenticazione di Servizi di dominio Active Directory di Azure.Make sure that the storage account containing your file shares is not already configured for Azure AD DS Authentication. Se l'autenticazione di Azure AD DS per I file di Azure è abilitata nell'account di archiviazione, deve essere disabilitata prima di usare Servizi di dominio Active Directory locale. Ciò implica che gli ACL esistenti configurati nell'ambiente Azure AD DS dovranno essere riconfigurati per un'applicazione delle autorizzazioni appropriata.
    
    Per informazioni sulla creazione di una nuova condivisione file, vedere [Creare una condivisione file in File](storage-how-to-create-file-share.md)di Azure.
    
    Per ottenere prestazioni ottimali, è consigliabile distribuire l'account di archiviazione nella stessa area della macchina virtuale da cui si prevede di accedere alla condivisione. 

- Verificare la connettività montando le condivisioni file di Azure usando la chiave dell'account di archiviazione. 

    Per verificare che il dispositivo e la condivisione file siano configurati correttamente, provare a montare la condivisione file usando la chiave dell'account di archiviazione. Per altre informazioni, vedere Usare una [condivisione file](storage-how-to-use-files-windows.md)di Azure con Windows.For more information, see Use an Azure file share with Windows .

## <a name="regional-availability"></a>Disponibilità a livello di area

L'autenticazione dei file di Azure con Servizi di dominio Active Directory (anteprima) è disponibile nella maggior parte delle [aree pubbliche.](https://azure.microsoft.com/global-infrastructure/regions/)

L'autenticazione dei file di Azure con Servizi di dominio Active Directory locale non è disponibile in:Azure Files authentication with on-premises AD DS is not available in:
- Stati Uniti occidentali


## <a name="workflow-overview"></a>Panoramica del flusso di lavoro

Prima di abilitare l'autenticazione di Servizi di dominio Active Directory su SMB per le condivisioni file di Azure, è consigliabile leggere e completare la sezione [dei prerequisiti.](#prerequisites) I prerequisiti verificano che gli ambienti AD, Azure AD e Archiviazione di Azure siano configurati correttamente. 

Se si prevede di abilitare le configurazioni di rete nella condivisione file, è consigliabile valutare la considerazione di [rete](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) e completare prima la configurazione correlata prima di abilitare l'autenticazione di Servizi di dominio Active Directory.

Eseguire quindi la procedura seguente per configurare i file di Azure per l'autenticazione di Active Directory:Next, follow the steps below to set up Azure Files for AD Authentication: 

1. Abilitare l'autenticazione di Servizi di dominio Active Directory di Azure nell'account di archiviazione. 

1. Assegnare le autorizzazioni di accesso per una condivisione all'identità di Azure AD (un utente, un gruppo o un'entità servizio) sincronizzata con l'identità di Active Directory di destinazione. 

1. Configurare gli ACL su SMB per directory e file. 
 
1. Montare una condivisione file di Azure in una macchina virtuale aggiunta a Servizi di dominio Active Directory.Mount an Azure file share to a VM joined to your AD DS. 

1. Aggiornare la password dell'identità dell'account di archiviazione in Servizi di dominio Active Directory.

Il diagramma seguente illustra il flusso di lavoro end-to-end per abilitare l'autenticazione di Azure AD rispetto a SMB per le condivisioni file di Azure.The following diagram illustrates the end-to-end workflow for enabling Azure AD authentication over SMB for Azure file shares. 

![File Diagramma del flusso di lavoro di AD](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> L'autenticazione di Servizi di dominio Active Directory tramite SMB per le condivisioni file di Azure è supportata solo nei computer o nelle macchine virtuali in esecuzione in versioni del sistema operativo più recenti di Windows 7 o Windows Server 2008 R2. 

## <a name="1-enable-ad-authentication-for-your-account"></a>1. Abilitare l'autenticazione AD per l'account 

Per abilitare l'autenticazione di Servizi di dominio Active Directory su SMB per le condivisioni file di Azure, è innanzitutto necessario registrare l'account di archiviazione con Servizi di dominio Active Directory e quindi impostare le proprietà di dominio necessarie nell'account di archiviazione. Quando la funzionalità è abilitata nell'account di archiviazione, si applica a tutte le condivisioni file nuove ed esistenti nell'account. Utilizzare `join-AzStorageAccountForAuth` per abilitare la funzionalità. È possibile trovare la descrizione dettagliata del flusso di lavoro end-to-end nello script all'interno di questa sezione. 

> [!IMPORTANT]
> Il `Join-AzStorageAccountForAuth` cmdlet apporterà modifiche all'ambiente Ad. Leggere la spiegazione seguente per comprendere meglio cosa sta facendo per assicurarsi di disporre delle autorizzazioni appropriate per eseguire il comando e che le modifiche applicate siano in linea con i criteri di conformità e sicurezza. 

Il `Join-AzStorageAccountForAuth` cmdlet eseguirà l'equivalente di un aggiunta a un dominio offline per conto dell'account di archiviazione indicato. Lo script utilizza il cmdlet per creare un account nel dominio di Active Directory, un [account computer](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (predefinito) o un account di accesso al [servizio.](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) Se si sceglie di eseguire questa operazione manualmente, è necessario selezionare l'account più adatto per l'ambiente.

L'account di Servizi di dominio Active Directory creato dal cmdlet rappresenta l'account di archiviazione nel dominio di Active Directory. Se l'account di Servizi di dominio Active Directory viene creato in un'unità organizzativa (OU) che impone la scadenza della password, è necessario aggiornare la password prima della validità massima della password. Se non si aggiorna la password dell'account, si verificano errori di autenticazione durante l'accesso alle condivisioni file di Azure.Failing to update the account password will result in authentication failures when accessing Azure file shares. Per informazioni su come aggiornare la password, vedere [Aggiornare](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)la password dell'account Servizi di dominio Active Directory .

È possibile utilizzare lo script seguente per eseguire la registrazione e abilitare la funzionalità oppure, in alternativa, è possibile eseguire manualmente le operazioni eseguite dallo script. Tali operazioni sono descritte nella sezione che segue lo script. Non è necessario eseguire entrambe le cose.

### <a name="11-script-prerequisites"></a>1.1 Prerequisiti dello script
- [Scaricare e decomprimere il modulo AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases)
- Installare ed eseguire il modulo in un dispositivo aggiunto a un dominio di Servizi di dominio Active Directory locale con credenziali di Servizi di dominio Active Directory che dispongono delle autorizzazioni per creare un account di accesso al servizio o un account computer in Active Directory di destinazione.
-  Eseguire lo script usando una credenziale di Servizi di dominio Active Directory locale sincronizzata con Azure AD. Le credenziali di Servizi di dominio Active Directory locali devono disporre delle autorizzazioni del proprietario dell'account di archiviazione o del ruolo RBAC del collaboratore.
- Assicurarsi che l'account di archiviazione si trova in [un'area supportata.](#regional-availability)

### <a name="12-domain-join-your-storage-account"></a>1.2 Aggiunta all'account di archiviazione per il dominio
Ricordarsi di sostituire i valori segnaposto con i propri nei parametri seguenti prima di eseguirlo in PowerShell.Remember to replace the placeholder values with your own in the parameters below before executing it in PowerShell.
> [!IMPORTANT]
> Il cmdlet di aggiunta al dominio creerà un account di Active Directory per rappresentare l'account di archiviazione (condivisione file) in Active Directory. Per informazioni dettagliate, vedere [Domande frequenti](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) su di un account computer o di accesso al servizio. Per gli account computer, è impostata una validità di scadenza della password predefinita in Active Directory a 30 giorni. Analogamente, l'account di accesso al servizio potrebbe avere una validità di scadenza della password predefinita impostata nel dominio di Active Directory o unità organizzativa (OU).
> Per entrambi i tipi di account, è consigliabile verificare la validità di scadenza della password configurata nell'ambiente Ad e pianificare [l'aggiornamento della password dell'identità dell'account di archiviazione in Active Directory](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) dell'account di Active Directory riportato di seguito prima della validità massima della password. Se non si aggiorna la password dell'account di Active Directory, si verificano errori di autenticazione durante l'accesso alle condivisioni file di Azure.Sefailing to update AD account password will result in authentication failures when accessing Azure file shares. È possibile prendere in considerazione la creazione di [una nuova unità organizzativa di Active Directory (OU) in Active Directory](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) e disattivare di conseguenza i criteri di scadenza delle password per gli account [computer](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN) o gli account di accesso al servizio. 

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Define parameters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account, depends on the AD permission you have and preference. 
#You can run Get-Help Join-AzStorageAccountForAuth to find more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -Name $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" ` #Default set to "ComputerAccount"
        -OrganizationalUnitName "<ou-name-here>" #You can also use -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" instead. If you don't provide the OU name as an input parameter, the AD identity that represents the storage account will be created under the root directory.

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, go to Azure Files FAQ.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose

```

Nella descrizione seguente vengono riepilogate tutte le azioni eseguite quando il `Join-AzStorageAccountForAuth` cmdlet viene eseguito. Se si preferisce non utilizzare il comando, è possibile eseguire questi passaggi manualmente:

> [!NOTE]
> Se lo `Join-AzStorageAccountForAuth` script precedente è già stato eseguito correttamente, passare alla sezione successiva "1.3 Verificare che la funzionalità sia abilitata". Non è necessario eseguire nuovamente le operazioni riportate di seguito.

#### <a name="a-checking-environment"></a>a. Controllo dell'ambiente

In primo luogo, lo script controlla l'ambiente. In particolare, controlla se [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) è installato e se la shell viene eseguita con privilegi di amministratore. Quindi controlla se è installato il [modulo Az.Storage 1.11.1-preview](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) e lo installa se non lo è. Se tali controlli passano, controllerà il dominio Active Directory per verificare se è presente un [account computer](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (predefinito) o un account di accesso al [servizio](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) che è già stato creato con SPN/UPN come "cifs/your-storage-account-name-here.file.core.windows.net". Se l'account non esiste, ne creerà uno come descritto nella sezione b di seguito.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Creazione manuale di un'identità che rappresenta l'account di archiviazione in Active DirectoryCreating an identity representing the storage account in your AD manually

Per creare manualmente questo account, creare una nuova `New-AzStorageAccountKey -KeyName kerb1`chiave Kerberos per l'account di archiviazione usando . Quindi, utilizzare tale chiave Kerberos come password per l'account. Questa chiave viene utilizzata solo durante la configurazione e non può essere utilizzata per operazioni di controllo o piano dati sull'account di archiviazione.

Una volta che hai questa chiave, creare un account di servizio o computer sotto l'unità organizzativa. Usare la specifica seguente: SPN: "cifs/your-storage-account-name-here.file.core.windows.net" Password: chiave Kerberos per l'account di archiviazione.

Se l'unità organizzativa impone la scadenza della password, è necessario aggiornare la password prima della validità massima della password per evitare errori di autenticazione durante l'accesso alle condivisioni file di Azure.If your OU enforces password expiration, you must update the password before the maximum password age to prevent authentication failures when accessing Azure file shares. Per informazioni [dettagliate, vedere Aggiornare la password dell'identità dell'account di archiviazione in Servizi di dominio Active Directory.See Update password of your storage account identity in AD DS](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) for details.

Mantenere il SID dell'account appena creato, sarà necessario per il passaggio successivo. Non è necessario sincronizzare l'identità creata che rappresentano l'account di archiviazione con Azure AD.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. Abilitare la funzionalità nell'account di archiviazioneEnable the feature on your storage account

Lo script abiliterebbe quindi la funzionalità nell'account di archiviazione. Per eseguire questa installazione manualmente, fornire alcuni dettagli di configurazione per le proprietà del dominio nel comando seguente, quindi eseguirlo. Il SID dell'account di archiviazione richiesto nel comando seguente è il SID dell'identità creata in Servizi di dominio Active Directory [nella sezione precedente.](#b-creating-an-identity-representing-the-storage-account-in-your-ad-manually)

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


### <a name="13-confirm-that-the-feature-is-enabled"></a>1.3 Verificare che la funzionalità sia abilitata

È possibile verificare se la funzionalità è abilitata nell'account di archiviazione, è possibile usare lo script seguente:You can check to confirm whether the feature is enabled on your storage account, you can use the following script:

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

La funzionalità è stata abilitata correttamente nell'account di archiviazione. Ora che la funzionalità è abilitata, è necessario eseguire altri passaggi per utilizzare la funzionalità.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

L'autenticazione di Servizi di dominio Active Directory è stata abilitata correttamente tramite SMB e ha assegnato un ruolo personalizzato che fornisce l'accesso a una condivisione file di Azure con un'identità di Servizi di dominio Active Directory.You have successfully enabled AD DS authentication over SMB and assigned a custom role that provides access to an Azure file share with an AD DS identity. Per concedere ad altri utenti l'accesso alla condivisione file, seguire le istruzioni nelle sezioni [Assegnare le autorizzazioni](#2-assign-access-permissions-to-an-identity) di accesso per utilizzare un'identità e [Configurare le autorizzazioni NTFS su SMB.](#3-configure-ntfs-permissions-over-smb)

## <a name="5-update-the-password-of-your-storage-account-identity-in-ad-ds"></a>5. Aggiornare la password dell'identità dell'account di archiviazione in Servizi di dominio Active Directory

Se è stata registrata l'identità/account di Servizi di dominio Active Directory che rappresenta l'account di archiviazione in un'unità organizzativa che applica l'ora di scadenza della password, è necessario ruotare la password prima della validità massima della password. Se non si aggiorna la password dell'account di Servizi di dominio Active Directory, si verificano errori di autenticazione per accedere alle condivisioni file di Azure.Senon to update the password of the AD DS account will result in authentication failures to access Azure file shares.  

Per attivare la rotazione `Update-AzStorageAccountADObjectPassword` delle password, è possibile eseguire il comando dal modulo AzFilesHybrid. Il cmdlet esegue azioni simili alla rotazione delle chiavi dell'account di archiviazione. Ottiene la seconda chiave Kerberos dell'account di archiviazione e la usa per aggiornare la password dell'account registrato in Servizi di dominio Active Directory. Quindi rigenera la chiave Kerberos di destinazione dell'account di archiviazione e aggiorna la password dell'account registrato in Servizi di dominio Active Directory. È necessario eseguire questo cmdlet in un ambiente aggiunto a un dominio di Servizi di dominio Active Directory locale.

```PowerShell
# Update the password of the AD DS account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui file di Azure e su come usare Active Directory rispetto a SMB, vedere queste risorse:For more information about Azure Files and how to use AD over SMB, see these resources:

- [Panoramica del supporto dell'autenticazione basata sull'identità di File di Azure per l'accesso SMBOverview of Azure Files identity-based authentication support for SMB access](storage-files-active-directory-overview.md)
- [DOMANDE FREQUENTI](storage-files-faq.md)
