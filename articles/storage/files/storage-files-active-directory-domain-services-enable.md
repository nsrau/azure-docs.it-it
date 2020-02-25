---
title: Abilitare l'autenticazione Active Directory su SMB per File di Azure
description: Informazioni su come abilitare l'autenticazione basata su identità su SMB per le condivisioni file di Azure tramite Active Directory. Le macchine virtuali Windows (VM) appartenenti a un dominio possono quindi accedere alle condivisioni file di Azure usando le credenziali di AD.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: rogarana
ms.openlocfilehash: 4a80565df10b470fe6d3fe590578681f85a3bdd9
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566283"
---
# <a name="enable-active-directory-authentication-over-smb-for-azure-file-shares"></a>Abilitare l'autenticazione Active Directory su SMB per le condivisioni file di Azure

[File di Azure](storage-files-introduction.md) supporta l'autenticazione basata su identità su Server Message Block (SMB) tramite due tipi di servizi di dominio: Azure Active Directory Domain Services (Azure AD DS) e Active Directory (ad) (anteprima). Questo articolo è incentrato sul supporto appena introdotto (anteprima) per sfruttare Dominio di Active Directory servizio per l'autenticazione in condivisioni file di Azure. Per abilitare l'autenticazione Azure AD DS (GA) per le condivisioni file di Azure, vedere [l'articolo sull'argomento](storage-files-active-directory-enable.md). 

> [!NOTE]
> Le condivisioni file di Azure supportano solo l'autenticazione per un servizio del dominio, Azure Active Directory servizio del dominio (Azure AD DS) o Active Directory (AD). 
>
> Le identità di Active Directory usate per l'autenticazione della condivisione file di Azure devono essere sincronizzate con Azure AD. La sincronizzazione dell'hash delle password è facoltativa. 
> 
> L'autenticazione di Active Directory non supporta l'autenticazione per gli account computer creati in Azure AD DS. 
> 
> L'autenticazione di Active Directory può essere supportata solo in una foresta di Active Directory in cui è registrato l'account di archiviazione. Per impostazione predefinita, è possibile accedere solo alle condivisioni file di Azure con le credenziali di Active Directory di una singola foresta AD. Se è necessario accedere alla condivisione file di Azure da una foresta diversa, verificare di avere configurato il trust tra insiemi di strutture appropriato.  
> 
> L'autenticazione di Active Directory per l'accesso SMB e la persistenza ACL è supportata per le condivisioni file di Azure gestite da Sincronizzazione file di Azure.

Quando si Abilita AD per le condivisioni file di Azure su SMB, i computer aggiunti al dominio AD possono montare condivisioni file di Azure usando le credenziali di Active Directory esistenti. Questa funzionalità può essere abilitata con un ambiente AD ospitato in computer locali o ospitati in Azure.

Le identità di Active Directory usate per accedere alle condivisioni file di Azure devono essere sincronizzate con Azure AD per applicare le autorizzazioni per i file a livello di condivisione tramite il modello di [controllo degli accessi in base al ruolo (RBAC)](../../role-based-access-control/overview.md) standard. Gli [elenchi DACL di tipo Windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) su file/directory trasferiti da file server esistenti verranno conservati e applicati. Ciò offre una perfetta integrazione con l'infrastruttura di dominio AD aziendale. Quando si sostituiscono file server locali con condivisioni file di Azure, gli utenti esistenti possono accedere alle condivisioni file di Azure dai client correnti con un'esperienza Single Sign-On, senza alcuna modifica alle credenziali in uso.  
 
## <a name="prerequisites"></a>Prerequisites 

Prima di abilitare l'autenticazione di Active Directory per le condivisioni file di Azure, assicurarsi di aver completato i prerequisiti seguenti: 

- Selezionare o creare l'ambiente di Active Directory e sincronizzarlo con Azure AD. 

    È possibile abilitare la funzionalità in un ambiente AD nuovo o esistente. Le identità usate per l'accesso devono essere sincronizzate con Azure AD. Il tenant Azure AD e la condivisione file a cui si accede devono essere associati alla stessa sottoscrizione. 

    Per configurare un ambiente di dominio Active Directory, vedere [Active Directory Domain Services Panoramica](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Se l'annuncio non è stato sincronizzato con il Azure AD, seguire le istruzioni in [che cos'è l'identità ibrida con Azure Active Directory?](../../active-directory/hybrid/whatis-hybrid-identity.md) per determinare il metodo di autenticazione preferito e l'opzione di configurazione Azure ad Connect. 

- Aggiungere un dominio a un computer locale o a una VM di Azure con servizi di dominio Active Directory o Active Directory. 

    Per accedere a una condivisione file usando le credenziali di Active Directory da un computer o una macchina virtuale, il dispositivo deve essere aggiunto a un dominio di servizi di dominio Active Directory. Per informazioni su come aggiungere un dominio ad Active Directory, vedere [aggiungere un computer a un dominio](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain). 

- Selezionare o creare un account di archiviazione di Azure in [un'area supportata](#regional-availability). 

    Verificare che l'account di archiviazione che contiene le condivisioni file non sia già configurato per l'autenticazione Azure AD DS. Se per l'account di archiviazione è abilitata l'autenticazione File di Azure Azure AD DS, è necessario disabilitarla prima di cambiare per usare AD. Ciò implica che gli ACL esistenti configurati nell'ambiente Azure AD DS dovranno essere riconfigurati per l'imposizione delle autorizzazioni appropriate.
    
    Per informazioni sulla creazione di una nuova condivisione file, vedere [creare una condivisione file in file di Azure](storage-how-to-create-file-share.md).
    
    Per ottenere prestazioni ottimali, è consigliabile che l'account di archiviazione si trovi nella stessa area della macchina virtuale da cui si prevede di accedere alla condivisione. 

- Verificare la connettività montando condivisioni file di Azure usando la chiave dell'account di archiviazione. 

    Per verificare che il dispositivo e la condivisione file siano configurati correttamente, provare a montare la condivisione file usando la chiave dell'account di archiviazione. Per altre informazioni, vedere [usare una condivisione file di Azure con Windows](storage-how-to-use-files-windows.md).

## <a name="regional-availability"></a>Disponibilità internazionale

File di Azure autenticazione di Active Directory è disponibile nella [maggior parte delle aree pubbliche](https://azure.microsoft.com/global-infrastructure/regions/), il subset di aree in cui non è ancora disponibile in è:

- Stati Uniti occidentali
- Stati Uniti occidentali 2
- Stati Uniti orientali
- Stati Uniti orientali 2
- Europa occidentale
- Europa settentrionale

## <a name="workflow-overview"></a>Panoramica del flusso di lavoro

Prima di abilitare l'autenticazione di Active Directory tramite SMB per le condivisioni file di Azure, è consigliabile esaminare i [prerequisiti](#prerequisites) e assicurarsi di aver completato tutti i passaggi. I prerequisiti convalidano che gli ambienti di Active Directory, Azure AD e di archiviazione di Azure siano configurati correttamente. 

Concedere quindi l'accesso alle risorse File di Azure con le credenziali di Active Directory: 

- Abilitare l'autenticazione di Active Directory File di Azure nell'account di archiviazione.  

- Assegnare le autorizzazioni di accesso per una condivisione all'identità del Azure AD (un utente, un gruppo o un'entità servizio) sincronizzata con l'identità di Active Directory di destinazione. 

- Configurare gli ACL su SMB per directory e file. 

- Montare una condivisione file di Azure da una macchina virtuale aggiunta a un dominio AD. 

Il diagramma seguente illustra il flusso di lavoro end-to-end per l'abilitazione dell'autenticazione Azure AD su SMB per le condivisioni file di Azure. 

![Diagramma del flusso di lavoro file AD](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> L'autenticazione di Active Directory su SMB per le condivisioni file di Azure è supportata solo in computer o macchine virtuali in esecuzione in versioni del sistema operativo più recenti di Windows 7 o Windows Server 2008 R2. 

## <a name="enable-ad-authentication-for-your-account"></a>Abilitare l'autenticazione di Active Directory per l'account 

Per abilitare l'autenticazione di Active Directory su SMB per le condivisioni file di Azure, è prima necessario registrare l'account di archiviazione con Active Directory e quindi impostare le proprietà di dominio necessarie nell'account di archiviazione. Quando la funzionalità è abilitata nell'account di archiviazione, viene applicata a tutte le condivisioni file nuove ed esistenti nell'account. Usare `join-AzStorageAccountForAuth` per abilitare la funzionalità. La descrizione dettagliata del flusso di lavoro end-to-end è reperibile nella sezione seguente. 

> [!IMPORTANT]
> Il cmdlet `join-AzStorageAccountForAuth` apporterà modifiche all'ambiente di Active Directory. Leggere la seguente spiegazione per comprendere meglio cosa sta facendo per assicurarsi di disporre delle autorizzazioni appropriate per eseguire il comando e che le modifiche applicate siano allineate con i criteri di conformità e sicurezza. 

Il cmdlet `join-AzStorageAccountForAuth` eseguirà l'equivalente di un aggiunta a un dominio offline per conto dell'account di archiviazione indicato. Verrà creato un account nel dominio di Active Directory, ovvero un account [computer](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) o un [account di accesso al servizio](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). L'account di Active Directory creato rappresenta l'account di archiviazione nel dominio AD. Se l'account di Active Directory viene creato in un'unità organizzativa (OU) di Active Directory che impone la scadenza della password, è necessario aggiornare la password prima della validità massima della password. Se non si aggiorna la password dell'account di Active Directory, si verificano errori di autenticazione quando si accede a condivisioni file di Azure Per informazioni su come aggiornare la password, vedere aggiornare la password dell' [account Active Directory](#update-ad-account-password).

È possibile utilizzare lo script seguente per eseguire la registrazione e abilitare la funzionalità oppure, in alternativa, è possibile eseguire manualmente le operazioni che lo script avrebbe. Tali operazioni sono descritte nella sezione successiva allo script. Non è necessario eseguire entrambe le operazioni.

### <a name="script-prerequisites"></a>Prerequisiti degli script

- [Scaricare e decomprimere il modulo AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases)
- Installare ed eseguire il modulo in un dispositivo aggiunto a un dominio ad Active Directory con le credenziali di Active Directory che dispongono delle autorizzazioni per creare un account di accesso al servizio o un account computer nell'annuncio di destinazione.
-  Eseguire lo script con una credenziale di Active Directory sincronizzata con la Azure AD. Per le credenziali di Active Directory devono essere disponibili il proprietario dell'account di archiviazione o le autorizzazioni del ruolo Collaboratore RBAC.
- Verificare che l'account di archiviazione si trovi in un' [area supportata](#regional-availability).

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope Currentuser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Select the target subscription for the current session
Select-AzureSubscription -SubscriptionId "<yourSubscriptionIdHere>"

#Register the target storage account with your active directory environment under the target OU
join-AzStorageAccountForAuth -ResourceGroupName "<resource-group-name-here>" -Name "<storage-account-name-here>" -DomainAccountType "<ServiceLogonAccount|ComputerAccount>" -OrganizationUnitName "<ou-name-here>"
```


Di seguito viene riportata una descrizione delle azioni eseguite quando si utilizza il comando `join-AzStorageAccountForAuth`. È possibile eseguire questi passaggi manualmente, se si preferisce non usare il comando:

### <a name="checking-environment"></a>Controllo dell'ambiente

Innanzitutto, verifica l'ambiente. In particolare, verifica se il [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) è installato e se la shell viene eseguita con privilegi di amministratore. Verifica quindi se il [modulo AZ. Storage 1.11.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) è installato e lo installa in caso contrario. Se il controllo viene superato, l'annuncio verrà controllato per verificare se è presente un [account computer](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (impostazione predefinita) o un [account di accesso al servizio](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) che è già stato creato con SPN/UPN come "CIFS/your-storage-account-name-here. file. Core. Windows. NET" e crearne uno se non esiste.

### <a name="creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>Creazione manuale di un'identità che rappresenta l'account di archiviazione nell'annuncio

Per creare questo account manualmente, creare una nuova chiave Kerberos per l'account di archiviazione usando `New-AzStorageAccountKey -Keynam "yourKeyName"`. Usare quindi la chiave Kerberos come password per l'account. Questa chiave viene usata solo durante l'installazione e non può essere usata per le operazioni di controllo o di piano dati nell'account di archiviazione.

Una volta creata la chiave, creare un account del servizio o del computer nell'unità organizzativa. Usare la specifica seguente: SPN: "CIFS/your-storage-account-name-here. file. Core. Windows. NET" password: chiave Kerberos per l'account di archiviazione.

Se l'unità organizzativa impone la scadenza della password, è necessario aggiornare la password prima della validità massima della password per evitare errori di autenticazione durante l'accesso alle condivisioni file di Azure. Per informazioni dettagliate, vedere [aggiornare la password dell'account Active Directory](#update-ad-account-password) .

Tenere il SID dell'account appena creato, sarà necessario per il passaggio successivo.

### <a name="enable-the-feature-on-your-storage-account"></a>Abilitare la funzionalità nell'account di archiviazione

Lo script Abilita quindi la funzionalità nell'account di archiviazione. Per eseguire questa operazione manualmente, fornire alcuni dettagli di configurazione per le proprietà del dominio nel comando seguente, quindi eseguirlo. Il SID dell'account di archiviazione richiesto nel comando seguente è il SID dell'identità creata in Active Directory.

```PowerShell
#Set the feature flag on the target storage account and provide the required AD domain information

Set-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>" -EnableActiveDirectoryDomainServiesForFile $true -ActiveDirectoryDomainName "<your-domain-name-here>" -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" -ActiveDirectoryForestName "<your-forest-name-here>" -ActiveDirectoryDomainGuid "<your-guid-here>" -ActiveDirectoryDomainsid "<your-domain-sid-here>" -ActiveDirectoryAzureStirageSid "<your-storage-account-sid>"
```


### <a name="check-if-the-feature-is-enabled"></a>Controllare se la funzionalità è abilitata

Se si vuole verificare se la funzionalità è abilitata nell'account di archiviazione, è possibile usare lo script seguente:

```PowerShell
#Get the target storage account
$storageaccount = Get-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>"

#List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

#List the directory domain information if the storage account has enabled AD authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

A questo punto la funzionalità è stata abilitata nell'account di archiviazione. Anche se la funzionalità è abilitata, è comunque necessario completare azioni aggiuntive per poter utilizzare correttamente la funzionalità.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

A questo punto è stata abilitata l'autenticazione Azure AD su SMB e è stato assegnato un ruolo personalizzato che fornisce l'accesso a una condivisione file di Azure con un'identità di Active Directory. Per concedere ad altri utenti l'accesso alla condivisione file, seguire le istruzioni riportate nelle sezioni [assegnare le autorizzazioni di accesso](#assign-access-permissions-to-an-identity) per l'uso di un'identità e configurare le [autorizzazioni NTFS su SMB](#configure-ntfs-permissions-over-smb) .

## <a name="update-ad-account-password"></a>Aggiornare la password dell'account Active Directory

Se è stato registrato l'account di Active Directory che rappresenta l'account di archiviazione in un'unità organizzativa che impone l'ora di scadenza della password, è necessario ruotare la password prima della validità massima della password. Se non si aggiorna la password dell'account di Active Directory, si verificano errori di autenticazione per accedere alle condivisioni file di Azure.  

Per attivare la rotazione della password, è possibile eseguire il comando `Update-AzStorageAccountADObjectPassword` dal [modulo AzFilesHybrid](#script-prerequisites). Il cmdlet esegue azioni simili alla rotazione della chiave dell'account di archiviazione. Ottiene la seconda chiave Kerberos dell'account di archiviazione e la usa per aggiornare la password dell'account registrato in Active Directory. Rigenera quindi la chiave Kerberos di destinazione dell'account di archiviazione e aggiorna la password dell'account registrato in Active Directory. È necessario eseguire questo cmdlet in un ambiente AD dominio aggiunto.

```PowerShell
#Update the password of the AD account registered for the storage account
Update-AzStorageAccountADObjectPassword -RotateToKerbKey kerb2 -ResourceGroupName "your-resource-group-name-here" -StorageAccountName "your-storage-account-name-here"
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su File di Azure e su come utilizzare AD tramite SMB, vedere le risorse seguenti:

- [Panoramica del supporto dell'autenticazione basata su identità File di Azure per l'accesso SMB](storage-files-active-directory-overview.md)
- [Domande frequenti](storage-files-faq.md)