---
title: Abilitare l'autenticazione di Active Directory su SMB per file di AzureEnable Active Directory authentication over SMB for Azure Files
description: Informazioni su come abilitare l'autenticazione basata sull'identità su SMB per le condivisioni file di Azure tramite Active Directory.Learn how to enable identity-based authentication over SMB for Azure file shares through Active Directory. Le macchine virtuali Windows (VM) aggiunte al dominio possono quindi accedere alle condivisioni file di Azure usando le credenziali di Active Directory.Your domain-joined Windows virtual machines (VMs) can then access Azure file shares by using AD credentials.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: rogarana
ms.openlocfilehash: b7820bc3d1b14e87064e4120edcffb6762041db9
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411476"
---
# <a name="enable-active-directory-authentication-over-smb-for-azure-file-shares"></a>Abilitare l'autenticazione di Active Directory su SMB per le condivisioni file di AzureEnable Active Directory authentication over SMB for Azure file shares

[File di](storage-files-introduction.md) Azure supporta l'autenticazione basata sull'identità tramite SMB (Server Message Block) tramite due tipi di Servizi di dominio: Servizi di dominio Azure Active Directory (Azure AD DS) (GA) e Active Directory (AD) (anteprima). Questo articolo è incentrato sul supporto appena introdotto (anteprima) di sfruttare il servizio di dominio Active Directory per l'autenticazione in condivisioni file di Azure.This article focuses on the newly introduced (preview) support of leveraging Active Directory Domain Service for authentication to Azure file shares. Se si è interessati all'abilitazione dell'autenticazione di Azure AD DS (GA) per le condivisioni file di Azure, vedere [l'articolo sull'argomento.](storage-files-identity-auth-active-directory-domain-service-enable.md)

> [!NOTE]
> Le condivisioni file di Azure supportano l'autenticazione solo in un servizio di dominio, ovvero Servizio di dominio Azure Active Directory (Azure AD DS) o Active Directory (AD). 
>
> Le identità di Active Directory usate per l'autenticazione della condivisione file di Azure devono essere sincronizzate con Azure AD. La sincronizzazione dell'hash delle password è facoltativa. 
> 
> L'autenticazione di Active Directory non supporta l'autenticazione in account computer creati in Active Directory. 
> 
> L'autenticazione di Active Directory può essere supportata solo in una foresta di Active Directory in cui è registrato l'account di archiviazione. Per impostazione predefinita, è possibile accedere alle condivisioni file di Azure solo con le credenziali di Active Directory da una singola foresta di Active Directory.You can only access Azure file shares with the AD credentials from a single AD forest by default. Se è necessario accedere alla condivisione file di Azure da una foresta diversa, assicurarsi di avere configurato il trust tra foreste corretto, vedere [le domande frequenti](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) per informazioni dettagliate.  
> 
> L'autenticazione DI Active Directory per l'accesso SMB e la persistenza ACL è supportata per le condivisioni file di Azure gestite da Sincronizzazione file di Azure.AD authentication for SMB access and ACL persistence is supported for Azure file shares managed by Azure File Sync.
>
> File di Azure supporta l'autenticazione Kerberos con Active Directory con crittografia RC4-HMAC. La crittografia Kerberos di AES non è ancora supportata.

Quando si abilita AD per le condivisioni file di Azure su SMB, i computer aggiunti al dominio di Active Directory possono montare le condivisioni file di Azure usando le credenziali di Active Directory esistenti. Questa funzionalità può essere abilitata con un ambiente AD ospitato in computer locali o ospitati in Azure.This capability can be enabled with an AD environment hosted either in prem machines or hosted in Azure.

Le identità di Active Directory usate per accedere alle condivisioni file di Azure devono essere sincronizzate con Azure AD per applicare le autorizzazioni dei file a livello di condivisione tramite il modello di controllo degli [accessi in base](../../role-based-access-control/overview.md) al ruolo standard. [Gli elenchi DACL di tipo Windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) su file/directory trasferiti da file server esistenti verranno mantenuti e applicati. Questa funzionalità offre una perfetta integrazione con l'infrastruttura di dominio AD aziendale. Quando si sostituiscono i file server locali con condivisioni file di Azure, gli utenti esistenti possono accedere alle condivisioni file di Azure dai client correnti con un'esperienza Single Sign-On, senza alcuna modifica alle credenziali in uso.  
 
## <a name="prerequisites"></a>Prerequisiti 

Prima di abilitare l'autenticazione di Active Directory per le condivisioni file di Azure, assicurarsi di aver completato i prerequisiti seguenti:Before you enable AD authentication for Azure file shares, make sure you have completed the following prerequisites: 

- Selezionare o creare l'ambiente AD e sincronizzarlo con Azure AD. 

    È possibile abilitare la funzionalità in un ambiente AD nuovo o esistente. Le identità usate per l'accesso devono essere sincronizzate con Azure AD. Il tenant di Azure AD e la condivisione file a cui si accede devono essere associati alla stessa sottoscrizione. 

    Per configurare un ambiente di dominio Active Directory, fare riferimento a [Panoramica](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)di Servizi di dominio Active Directory . Se l'AD non è stata sincronizzata con Azure AD, seguire le indicazioni in [Che cos'è l'identità ibrida con Azure Active Directory?](../../active-directory/hybrid/whatis-hybrid-identity.md) per determinare il metodo di autenticazione preferito e l'opzione di installazione di Azure AD Connect. 

- Aggiunta di un dominio a un computer locale o ad una macchina virtuale di Azure in Active Directory (denominata anche Servizi di dominio Active Directory). 

    To access a file share by using AD credentials from a machine or VM, your device must be domain-joined to AD. Per informazioni su come aggiungere un aggiunta a Un dominio ad Active Directory, fare riferimento a Aggiunta di [un computer a un dominio](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain). 

- Selezionare o creare un account di archiviazione di Azure in [un'area supportata.](#regional-availability) 

    Assicurarsi che l'account di archiviazione contenente le condivisioni file non sia già configurato per l'autenticazione di Servizi di dominio Active Directory di Azure.Make sure that the storage account containing your file shares is not already configured for Azure AD DS Authentication. Se l'autenticazione di Azure AD DS per I file di Azure Azure Azure È abilitata, deve essere disabilitata prima di usare Active Directory.If Azure Files Azure AD DS Authentication is enabled on the storage account, it needs to be disabled before changing to use AD. Ciò implica che gli ACL esistenti configurati nell'ambiente Azure AD DS dovranno essere riconfigurati per un'applicazione delle autorizzazioni appropriata.
    
    Per informazioni sulla creazione di una nuova condivisione file, vedere [Creare una condivisione file in File](storage-how-to-create-file-share.md)di Azure.
    
    Per ottenere prestazioni ottimali, è consigliabile distribuire l'account di archiviazione nella stessa area della macchina virtuale da cui si prevede di accedere alla condivisione. 

- Verificare la connettività montando le condivisioni file di Azure usando la chiave dell'account di archiviazione. 

    Per verificare che il dispositivo e la condivisione file siano configurati correttamente, provare a montare la condivisione file usando la chiave dell'account di archiviazione. Per altre informazioni, vedere Usare una [condivisione file](storage-how-to-use-files-windows.md)di Azure con Windows.For more information, see Use an Azure file share with Windows .

## <a name="regional-availability"></a>Disponibilità a livello di area

L'autenticazione di Azure Files AD (anteprima) è disponibile nella maggior parte delle [aree pubbliche.](https://azure.microsoft.com/global-infrastructure/regions/)

L'autenticazione di Azure Files AD non è disponibile in:Azure Files AD authentication is not available in:
- Stati Uniti occidentali


## <a name="workflow-overview"></a>Panoramica del flusso di lavoro

Prima di abilitare l'autenticazione ad Active Directory su SMB per le condivisioni file di Azure, è consigliabile esaminare i prerequisiti e [assicurarsi](#prerequisites) di aver completato tutti i passaggi. I prerequisiti verificano che gli ambienti AD, Azure AD e Archiviazione di Azure siano configurati correttamente. 

Successivamente, concedere l'accesso alle risorse di File di Azure con credenziali di Active Directory:Next, grant access to Azure Files resources with AD credentials: 

- Abilitare l'autenticazione di File di Azure ad nell'account di archiviazione.  

- Assegnare le autorizzazioni di accesso per una condivisione all'identità di Azure AD (un utente, un gruppo o un'entità servizio) sincronizzata con l'identità di Active Directory di destinazione. 

- Configurare gli ACL su SMB per directory e file. 

- Montare una condivisione file di Azure da una macchina virtuale aggiunta a un dominio active directory. 

Il diagramma seguente illustra il flusso di lavoro end-to-end per abilitare l'autenticazione di Azure AD rispetto a SMB per le condivisioni file di Azure.The following diagram illustrates the end-to-end workflow for enabling Azure AD authentication over SMB for Azure file shares. 

![File Diagramma del flusso di lavoro di AD](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> L'autenticazione di Active Directory tramite SMB per le condivisioni file di Azure è supportata solo nei computer o nelle macchine virtuali in esecuzione in versioni del sistema operativo più recenti di Windows 7 o Windows Server 2008 R2. 

## <a name="enable-ad-authentication-for-your-account"></a>Abilitare l'autenticazione di Active Directory per l'accountEnable AD authentication for your account 

Per abilitare l'autenticazione di Active Directory su SMB per le condivisioni file di Azure, è innanzitutto necessario registrare l'account di archiviazione con Active Directory e quindi impostare le proprietà di dominio necessarie nell'account di archiviazione. Quando la funzionalità è abilitata nell'account di archiviazione, si applica a tutte le condivisioni file nuove ed esistenti nell'account. Utilizzare `join-AzStorageAccountForAuth` per abilitare la funzionalità. La descrizione dettagliata del flusso di lavoro end-to-end è riportata nella sezione seguente. 

> [!IMPORTANT]
> Il `Join-AzStorageAccountForAuth` cmdlet apporterà modifiche all'ambiente Ad. Leggere la spiegazione seguente per comprendere meglio cosa sta facendo per assicurarsi di disporre delle autorizzazioni appropriate per eseguire il comando e che le modifiche applicate siano in linea con i criteri di conformità e sicurezza. 

Il `Join-AzStorageAccountForAuth` cmdlet eseguirà l'equivalente di un aggiunta a un dominio offline per conto dell'account di archiviazione indicato. Verrà creato un account nel dominio di Active Directory, un [account computer](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) o un account di accesso al [servizio](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). L'account di Active Directory creato rappresenta l'account di archiviazione nel dominio di Active Directory. Se l'account di Active Directory viene creato in un'unità organizzativa di Active Directory che impone la scadenza della password, è necessario aggiornare la password prima della validità massima della password. Se non si aggiorna la password dell'account di Active Directory, si verificano errori di autenticazione durante l'accesso alle condivisioni file di Azure.Sefailing to update AD account password will result in authentication failures when accessing Azure file shares. Per informazioni su come aggiornare la password, vedere [Aggiornare](#update-ad-account-password)la password dell'account AD .

È possibile utilizzare lo script seguente per eseguire la registrazione e abilitare la funzionalità oppure, in alternativa, è possibile eseguire manualmente le operazioni eseguite dallo script. Tali operazioni sono descritte nella sezione che segue lo script. Non è necessario eseguire entrambe le cose.

### <a name="1-check-prerequisites"></a>1. Verificare i prerequisiti
- [Scaricare e decomprimere il modulo AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases)
- Installare ed eseguire il modulo in un dispositivo aggiunto a Un dominio ad Active Directory con credenziali di Active Directory che dispongono delle autorizzazioni per creare un account di accesso al servizio o un account computer in Active Directory di destinazione.
-  Eseguire lo script usando una credenziale AD sincronizzata con Azure AD. Le autorizzazioni del ruolo RBAC dell'account di archiviazione o del ruolo RBAC del collaboratore devono disporre delle autorizzazioni del ruolo RBAC del collaboratore.
- Assicurarsi che l'account di archiviazione si trova in [un'area supportata.](#regional-availability)

### <a name="2-domain-join-your-storage-account"></a>2. Aggiungere un account di archiviazione all'account di archiviazione
Ricordarsi di sostituire i valori segnaposto con i propri nei parametri seguenti prima di eseguirlo in PowerShell.Remember to replace the placeholder values with your own in the parameters below before executing it in PowerShell.

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId "<your-subscription-id-here>"

# Register the target storage account with your active directory environment under the target OU (for example: "OU=ComputersOU,DC=prod,DC=corp,DC=contoso,DC=com")
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account, depends on the AD permission you have and preference. 
Join-AzStorageAccountForAuth `
        -ResourceGroupName "<resource-group-name-here>" `
        -Name "<storage-account-name-here>" `
        -DomainAccountType "ComputerAccount" `
        -OrganizationalUnitName "<ou-name-here>"
```

Nella descrizione seguente vengono riepilogate tutte le azioni eseguite quando il `Join-AzStorageAccountForAuth` cmdlet viene eseguito. Se si preferisce non utilizzare il comando, è possibile eseguire questi passaggi manualmente:

> [!NOTE]
> Se lo `Join-AzStorageAccountForAuth` script è già stato eseguito correttamente, passare alla sezione successiva "3. Verificare che la funzionalità sia abilitata". Non è necessario eseguire nuovamente le operazioni riportate di seguito.

#### <a name="a-checking-environment"></a>a. Controllo dell'ambiente

In primo luogo, controlla l'ambiente. In particolare controlla se [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) è installato e se la shell viene eseguita con privilegi di amministratore. Quindi controlla se è installato il [modulo Az.Storage 1.11.1-preview](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) e lo installa se non lo è. Se tali controlli passano, controllerà Active Directory per verificare se è presente un [account computer](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (predefinito) o un account di accesso al [servizio](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) che è già stato creato con SPN/UPN come "cifs/your-storage-account-name-here.file.core.windows.net". Se l'account non esiste, ne creerà uno come descritto nella sezione b di seguito.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Creazione manuale di un'identità che rappresenta l'account di archiviazione in Active DirectoryCreating an identity representing the storage account in your AD manually

Per creare manualmente questo account, creare una nuova `New-AzStorageAccountKey -KeyName kerb1`chiave kerberos per l'account di archiviazione usando . Quindi, utilizzare tale chiave kerberos come password per l'account. Questa chiave viene utilizzata solo durante la configurazione e non può essere utilizzata per operazioni di controllo o piano dati sull'account di archiviazione.

Una volta che hai questa chiave, creare un account di servizio o computer sotto l'unità organizzativa. Usare la specifica seguente: SPN: "cifs/your-storage-account-name-here.file.core.windows.net" Password: chiave Kerberos per l'account di archiviazione.

Se l'unità organizzativa impone la scadenza della password, è necessario aggiornare la password prima della validità massima della password per evitare errori di autenticazione durante l'accesso alle condivisioni file di Azure.If your OU enforces password expiration, you must update the password before the maximum password age to prevent authentication failures when accessing Azure file shares. Per informazioni dettagliate, vedere Aggiornare la [password dell'account AD.](#update-ad-account-password)

Mantenere il SID dell'account appena creato, sarà necessario per il passaggio successivo. Non è necessario sincronizzare l'identità di Active Directory appena creata che rappresenta l'account di archiviazione con Azure AD.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. Abilitare la funzionalità nell'account di archiviazioneEnable the feature on your storage account

Lo script abiliterebbe quindi la funzionalità nell'account di archiviazione. Per eseguire questa installazione manualmente, fornire alcuni dettagli di configurazione per le proprietà del dominio nel comando seguente, quindi eseguirlo. Il SID dell'account di archiviazione richiesto nel comando seguente è il SID dell'identità creata in Active Directory (sezione b precedente).

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


### <a name="3-confirm-that-the-feature-is-enabled"></a>3. Verificare che la funzione sia abilitata

È possibile verificare se la funzionalità è abilitata nell'account di archiviazione, è possibile usare lo script seguente:You can check to confirm whether the feature is enabled on your storage account, you can use the following script:

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

La funzionalità è stata abilitata correttamente nell'account di archiviazione. Anche se la funzionalità è abilitata, è comunque necessario completare azioni aggiuntive per poter utilizzare correttamente la funzionalità.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

L'autenticazione di Active Directory è stata abilitata correttamente tramite SMB e ha assegnato un ruolo personalizzato che fornisce l'accesso a una condivisione file di Azure con un'identità di Active Directory.You have successfully enabled AD authentication over SMB and assigned a custom role that provides access to an Azure file share with an AD identity. Per concedere ad altri utenti l'accesso alla condivisione file, seguire le istruzioni nelle sezioni [Assegnare le autorizzazioni](#assign-access-permissions-to-an-identity) di accesso per utilizzare un'identità e [Configurare le autorizzazioni NTFS su SMB.](#configure-ntfs-permissions-over-smb)

## <a name="update-ad-account-password"></a>Aggiornare la password dell'account AD

Se l'identità/account di Active Directory che rappresenta l'account di archiviazione in un'unità organizzativa che applica l'ora di scadenza della password, è necessario ruotare la password prima della validità massima della password. Se non si aggiorna la password dell'account ad Active Directory, si verificano errori di autenticazione per accedere alle condivisioni file di Azure.Senon update the password of the AD account will result in authentication failures to access Azure file shares.  

Per attivare la rotazione `Update-AzStorageAccountADObjectPassword` delle password, è possibile eseguire il comando dal modulo AzFilesHybrid. Il cmdlet esegue azioni simili alla rotazione delle chiavi dell'account di archiviazione. Ottiene la seconda chiave Kerberos dell'account di archiviazione e la usa per aggiornare la password dell'account registrato in Active Directory. Quindi rigenera la chiave Kerberos di destinazione dell'account di archiviazione e aggiorna la password dell'account registrato in Active Directory. È necessario eseguire questo cmdlet in un ambiente aggiunto a un dominio Active Directory.

```PowerShell
#Update the password of the AD account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui file di Azure e su come usare Active Directory rispetto a SMB, vedere queste risorse:For more information about Azure Files and how to use AD over SMB, see these resources:

- [Panoramica del supporto dell'autenticazione basata sull'identità di File di Azure per l'accesso SMBOverview of Azure Files identity-based authentication support for SMB access](storage-files-active-directory-overview.md)
- [DOMANDE FREQUENTI](storage-files-faq.md)
