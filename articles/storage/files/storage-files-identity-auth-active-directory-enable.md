---
title: Abilitare l'autenticazione Active Directory su SMB per File di Azure
description: Informazioni su come abilitare l'autenticazione basata su identità su SMB per le condivisioni file di Azure tramite Active Directory. Le macchine virtuali Windows (VM) appartenenti a un dominio possono quindi accedere alle condivisioni file di Azure usando le credenziali di AD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: rogarana
ms.openlocfilehash: 6309219b31c22f1f1d090cc9de9931609e3423f7
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792982"
---
# <a name="enable-on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Abilitare l'autenticazione Active Directory Domain Services locale tramite SMB per le condivisioni file di Azure

[File di Azure](storage-files-introduction.md) supporta l'autenticazione basata su identità su Server Message Block (SMB) tramite due tipi di servizi di dominio: Azure Active Directory Domain Services (Azure AD DS) e Active Directory Domain Services locali (ad DS) (anteprima). Questo articolo è incentrato sul supporto appena introdotto (anteprima) per sfruttare Dominio di Active Directory servizio per l'autenticazione in condivisioni file di Azure. Per abilitare l'autenticazione Azure AD DS (GA) per le condivisioni file di Azure, vedere [l'articolo sull'argomento](storage-files-identity-auth-active-directory-domain-service-enable.md).

> [!NOTE]
> Le condivisioni file di Azure supportano solo l'autenticazione per un servizio del dominio, Azure Active Directory servizio del dominio (Azure AD DS) o Active Directory Domain Services locale (AD DS). 
>
> Le identità di servizi di dominio Active Directory usate per l'autenticazione della condivisione file di Azure devono essere sincronizzate con Azure AD. La sincronizzazione dell'hash delle password è facoltativa. 
> 
> L'autenticazione di servizi di dominio Active Directory locale non supporta l'autenticazione per gli account computer creati in servizi di dominio Active Directory. 
> 
> L'autenticazione di servizi di dominio Active Directory locale può essere supportata solo in una foresta di Active Directory in cui è registrato l'account di archiviazione. Per impostazione predefinita, è possibile accedere solo alle condivisioni file di Azure con le credenziali di servizi di dominio Active Directory di una singola foresta. Se è necessario accedere alla condivisione file di Azure da una foresta diversa, verificare che sia configurato il trust tra foreste appropriato. per informazioni dettagliate, vedere le [domande frequenti](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) .  
> 
> L'autenticazione di Active Directory per l'accesso SMB e la persistenza ACL è supportata per le condivisioni file di Azure gestite da Sincronizzazione file di Azure.
>
> File di Azure supporta l'autenticazione Kerberos con Active Directory con crittografia RC4-HMAC. La crittografia Kerberos AES non è ancora supportata.

Quando si abilita Active Directory Domain Services per le condivisioni file di Azure tramite SMB, i computer aggiunti AD DS possono montare condivisioni file di Azure usando le credenziali di Active Directory esistenti. Questa funzionalità può essere abilitata con un ambiente di servizi di dominio Active Directory ospitato in computer locali o ospitati in Azure.

Le identità usate per accedere alle condivisioni file di Azure devono essere sincronizzate con Azure AD per applicare le autorizzazioni per i file a livello di condivisione tramite il modello di [controllo degli accessi in base al ruolo (RBAC)](../../role-based-access-control/overview.md) . Gli [elenchi DACL di tipo Windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) su file/directory trasferiti da file server esistenti verranno conservati e applicati. Questa funzionalità offre un'integrazione perfetta con l'ambiente di servizi di dominio Active Directory aziendale. Quando si sostituiscono file server locali con condivisioni file di Azure, gli utenti esistenti possono accedere alle condivisioni file di Azure dai client correnti con un'esperienza Single Sign-On, senza alcuna modifica alle credenziali in uso.  

> [!NOTE]
> Per semplificare la configurazione dell'autenticazione di Active Directory File di Azure per alcuni casi d'uso comuni, sono stati pubblicati [due video](https://docs.microsoft.com/azure/storage/files/storage-files-introduction#videos) con istruzioni dettagliate:
> - Sostituzione dei file server locali con File di Azure (inclusa la configurazione del collegamento privato per i file e l'autenticazione di Active Directory)
> - Utilizzo di File di Azure come contenitore di profili per desktop virtuale Windows (incluso l'installazione di AD Authentication e la configurazione di FsLogix)

## <a name="prerequisites"></a>Prerequisiti 

Prima di abilitare l'autenticazione di servizi di dominio Active Directory per le condivisioni file di Azure, assicurarsi di aver completato i prerequisiti seguenti: 

- Selezionare o creare l'ambiente di servizi di dominio Active Directory e [sincronizzarlo con Azure ad](../../active-directory/hybrid/how-to-connect-install-roadmap.md). 

    È possibile abilitare la funzionalità in un ambiente AD DS locale nuovo o esistente. Le identità usate per l'accesso devono essere sincronizzate con Azure AD. Il tenant Azure AD e la condivisione file a cui si accede devono essere associati alla stessa sottoscrizione. 

    Per configurare un ambiente di dominio Active Directory, vedere [Active Directory Domain Services Panoramica](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Se l'annuncio non è stato sincronizzato con il Azure AD, seguire le istruzioni riportate in [Azure ad Connect e Azure ad Connect Health roadmap](../../active-directory/hybrid/how-to-connect-install-roadmap.md) per l'installazione per configurare e configurare Azure ad Connect. 

- Aggiungere un dominio a un computer locale o una macchina virtuale di Azure in servizi di dominio Active Directory locale. 

    Per accedere a una condivisione file usando le credenziali di Active Directory da un computer o una macchina virtuale, il dispositivo deve essere aggiunto a un dominio di servizi di dominio Active Directory. Per informazioni su come aggiungere un dominio, vedere [aggiungere un computer a un dominio](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain). 

- Selezionare o creare un account di archiviazione di Azure.  Per ottenere prestazioni ottimali, si consiglia di distribuire l'account di archiviazione nella stessa area della macchina virtuale da cui si prevede di accedere alla condivisione.

    Verificare che l'account di archiviazione che contiene le condivisioni file non sia già configurato per l'autenticazione Azure AD DS. Se per l'account di archiviazione è abilitata l'autenticazione File di Azure Azure AD DS, è necessario disabilitarla prima di cambiare per usare servizi di dominio Active Directory locale. Ciò implica che gli ACL esistenti configurati nell'ambiente Azure AD DS dovranno essere riconfigurati per l'imposizione delle autorizzazioni appropriate.
    
    Per informazioni sulla creazione di una nuova condivisione file, vedere [creare una condivisione file in file di Azure](storage-how-to-create-file-share.md).

- Verificare la connettività montando condivisioni file di Azure usando la chiave dell'account di archiviazione. 

    Per verificare che il dispositivo e la condivisione file siano configurati correttamente, provare a [montare la condivisione file](storage-how-to-use-files-windows.md) usando la chiave dell'account di archiviazione. Se si verificano problemi durante la connessione a File di Azure, vedere [lo strumento per la risoluzione dei problemi pubblicato per file di Azure errori di montaggio in Windows](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5). Vengono inoltre fornite [indicazioni](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access) per aggirare gli scenari in cui la porta 445 è bloccata. 

## <a name="regional-availability"></a>Disponibilità a livello di area

File di Azure autenticazione con servizi di dominio Active Directory (anteprima) è disponibile in [tutte le aree pubbliche e nelle aree di Azure gov](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="overview"></a>Panoramica

Se si prevede di abilitare tutte le configurazioni di rete nella condivisione file, è consigliabile valutare la [considerazione della rete](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) e completare la configurazione correlata prima di abilitare l'autenticazione di servizi di dominio Active Directory.

L'abilitazione dell'autenticazione di servizi di dominio Active Directory per le condivisioni file di Azure consente di autenticare le condivisioni file di Azure con le credenziali di servizi di dominio Active Directory locali. Inoltre, consente di gestire meglio le autorizzazioni per consentire un controllo di accesso granulare. Questa operazione richiede la sincronizzazione delle identità da servizi di dominio Active Directory locali a Azure AD con AD Connect. È possibile controllare l'accesso a livello di condivisione con le identità sincronizzate con Azure AD durante la gestione dell'accesso a livello di file/condivisione con le credenziali di servizi di dominio Active Directory locali.

Quindi, attenersi alla procedura seguente per configurare File di Azure per l'autenticazione di servizi di dominio Active Directory: 

1. [Abilitare l'autenticazione File di Azure servizi di dominio Active Directory nell'account di archiviazione](#1-enable-ad-ds-authentication-for-your-account)

1. [Assegnare le autorizzazioni di accesso per una condivisione all'identità del Azure AD (un utente, un gruppo o un'entità servizio) sincronizzata con l'identità di Active Directory di destinazione](#2-assign-access-permissions-to-an-identity)

1. [Configurare gli ACL su SMB per directory e file](#3-configure-ntfs-permissions-over-smb)
 
1. [Montare una condivisione file di Azure in una macchina virtuale aggiunta a servizi di dominio Active Directory](#4-mount-a-file-share-from-a-domain-joined-vm)

1. [Aggiornare la password dell'identità dell'account di archiviazione in servizi di dominio Active Directory](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)

Il diagramma seguente illustra il flusso di lavoro end-to-end per l'abilitazione dell'autenticazione Azure AD su SMB per le condivisioni file di Azure. 

![Diagramma del flusso di lavoro file AD](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> L'autenticazione di servizi di dominio Active Directory su SMB per le condivisioni file di Azure è supportata solo in computer o macchine virtuali in esecuzione in versioni del sistema operativo più recenti di Windows 7 o Windows Server 2008 R2. 

## <a name="1-enable-ad-ds-authentication-for-your-account"></a>1 abilitare l'autenticazione di servizi di dominio Active Directory per l'account 

Per abilitare l'autenticazione di servizi di dominio Active Directory tramite SMB per le condivisioni file di Azure, è prima necessario registrare l'account di archiviazione con servizi di dominio Active Directory e quindi impostare le proprietà di dominio necessarie nell'account di archiviazione. Quando la funzionalità è abilitata nell'account di archiviazione, viene applicata a tutte le condivisioni file nuove ed esistenti nell'account. Scaricare il modulo PowerShell di AzFilesHybrid e `join-AzStorageAccountForAuth` usare per abilitare la funzionalità. È possibile trovare la descrizione dettagliata del flusso di lavoro end-to-end nello script contenuto in questa sezione. 

> [!IMPORTANT]
> Il `Join-AzStorageAccountForAuth` cmdlet apporterà modifiche all'ambiente di Active Directory. Leggere la seguente spiegazione per comprendere meglio cosa sta facendo per assicurarsi di disporre delle autorizzazioni appropriate per eseguire il comando e che le modifiche applicate siano allineate con i criteri di conformità e sicurezza. 

Il `Join-AzStorageAccountForAuth` cmdlet eseguirà l'equivalente di un aggiunta a un dominio offline per conto dell'account di archiviazione indicato. Lo script usa il cmdlet per creare un account nel dominio di Active Directory, ovvero un account [computer](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (impostazione predefinita) o un [account di accesso al servizio](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). Se si sceglie di eseguire questa operazione manualmente, è necessario selezionare l'account più appropriato per l'ambiente in uso.

L'account di Active Directory Domain Services creato dal cmdlet rappresenta l'account di archiviazione nel dominio AD. Se l'account di servizi di dominio Active Directory viene creato in un'unità organizzativa (OU) che impone la scadenza della password, è necessario aggiornare la password prima della validità massima della password. Se non si aggiorna la password dell'account, si verificano errori di autenticazione durante l'accesso alle condivisioni file di Azure. Per informazioni su come aggiornare la password, vedere aggiornare la password dell' [account Active Directory](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)Domain Services.

È possibile utilizzare lo script seguente per eseguire la registrazione e abilitare la funzionalità oppure, in alternativa, è possibile eseguire manualmente le operazioni che lo script avrebbe. Tali operazioni sono descritte nella sezione successiva allo script. Non è necessario eseguire entrambe le operazioni.

### <a name="11-script-prerequisites"></a>Prerequisiti per gli script di 1,1
- [Scaricare e decomprimere il modulo AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases)
- Installare ed eseguire il modulo in un dispositivo che è aggiunto a un dominio ad DS locale con le credenziali di servizi di dominio Active Directory che dispongono delle autorizzazioni per creare un account di accesso al servizio o un account computer nell'annuncio di destinazione.
-  Eseguire lo script con una credenziale AD DS locale sincronizzata con la Azure AD. Le credenziali di servizi di dominio Active Directory locali devono avere il proprietario dell'account di archiviazione o le autorizzazioni del ruolo di collaboratore RBAC.
- Verificare che l'account di archiviazione si trovi in un' [area supportata](#regional-availability).

### <a name="12-domain-join-your-storage-account"></a>1,2 aggiunta di un dominio all'account di archiviazione
Ricordarsi di sostituire i valori segnaposto con quelli personalizzati nei parametri riportati di seguito prima di eseguirli in PowerShell.
> [!IMPORTANT]
> Il cmdlet Domain Join creerà un account AD per rappresentare l'account di archiviazione (condivisione file) in Active Directory. È possibile scegliere di eseguire la registrazione come account computer o account di accesso al servizio. per informazioni dettagliate, vedere le [domande frequenti](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) . Per gli account computer, esiste una scadenza della password predefinita impostata in Active Directory a 30 giorni. Analogamente, è possibile che l'account di accesso del servizio disponga di una data di scadenza della password predefinita impostata sul dominio AD o sull'unità organizzativa (OU).
> Per entrambi i tipi di account, è consigliabile verificare qual è la scadenza della password configurata nell'ambiente di Active Directory e pianificare [l'aggiornamento della password dell'identità dell'account di archiviazione in ad](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) dell'account di Active Directory precedente alla validità massima della password. È possibile prendere [in considerazione la creazione di una nuova unità organizzativa ad (OU) in ad](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) e disabilitare di conseguenza i criteri di scadenza delle password per gli account [computer](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN) o gli account di accesso al servizio. 

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
# You can run Get-Help Join-AzStorageAccountForAuth to find more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -Name $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" ` # Default set to "ComputerAccount" if this parameter is not provided
        -OrganizationalUnitName "<ou-name-here>" #You can also use -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" instead. If you don't provide the OU name as an input parameter, the AD identity that represents the storage account will be created under the root directory.

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, go to Azure Files FAQ.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose

```

La descrizione seguente riepiloga tutte le azioni eseguite quando viene `Join-AzStorageAccountForAuth` eseguito il cmdlet. È possibile eseguire questi passaggi manualmente, se si preferisce non usare il comando:

> [!NOTE]
> Se lo `Join-AzStorageAccountForAuth` script è già stato eseguito correttamente, passare alla sezione successiva "1,3 Verificare che la funzionalità sia abilitata". Non è necessario eseguire di nuovo le operazioni seguenti.

#### <a name="a-checking-environment"></a>a. Controllo dell'ambiente

In primo luogo, lo script controlla l'ambiente. In particolare, controlla se [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) è installato e se la shell viene eseguita con privilegi di amministratore. Verifica quindi se il [modulo AZ. Storage 1.11.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) è installato e lo installa in caso contrario. Se questi controlli vengono superati, verranno controllati i servizi di dominio Active Directory per verificare se è presente un [account computer](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (impostazione predefinita) o un [account di accesso al servizio](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) che è già stato creato con SPN/UPN come "CIFS/your-storage-account-name-here. file. Core. Windows. NET". Se l'account non esiste, ne verrà creato uno come descritto nella sezione b riportata di seguito.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Creazione manuale di un'identità che rappresenta l'account di archiviazione nell'annuncio

Per creare questo account manualmente, creare una nuova chiave Kerberos per l'account di archiviazione `New-AzStorageAccountKey -KeyName kerb1`usando. Usare quindi la chiave Kerberos come password per l'account. Questa chiave viene usata solo durante la configurazione e non può essere usata per le operazioni di controllo o di piano dati nell'account di archiviazione.

Una volta creata la chiave, creare un account del servizio o del computer nell'unità organizzativa. Usare la specifica seguente: SPN: "CIFS/your-storage-account-name-here. file. Core. Windows. NET" password: chiave Kerberos per l'account di archiviazione.

Se l'unità organizzativa impone la scadenza della password, è necessario aggiornare la password prima della validità massima della password per evitare errori di autenticazione durante l'accesso alle condivisioni file di Azure. Per informazioni dettagliate, vedere [aggiornare la password dell'identità dell'account di archiviazione in servizi di dominio Active Directory](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) .

Tenere il SID dell'account appena creato, sarà necessario per il passaggio successivo. L'identità creata che rappresenta l'account di archiviazione non deve essere sincronizzata con Azure AD.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. Abilitare la funzionalità nell'account di archiviazione

Lo script Abilita quindi la funzionalità nell'account di archiviazione. Per eseguire questa installazione manualmente, fornire alcuni dettagli di configurazione per le proprietà del dominio nel comando seguente, quindi eseguirlo. Il SID dell'account di archiviazione richiesto nel comando seguente è il SID dell'identità creata in servizi di dominio Active Directory nella [sezione precedente](#b-creating-an-identity-representing-the-storage-account-in-your-ad-manually).

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


### <a name="13-confirm-that-the-feature-is-enabled"></a>1,3 Verificare che la funzionalità sia abilitata

È possibile controllare per verificare se la funzionalità è abilitata nell'account di archiviazione, è possibile usare lo script seguente:

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

A questo punto la funzionalità è stata abilitata nell'account di archiviazione. Ora che la funzionalità è abilitata, è necessario eseguire altri passaggi per usare la funzionalità.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

È stata abilitata l'autenticazione di servizi di dominio Active Directory su SMB ed è stato assegnato un ruolo personalizzato che fornisce l'accesso a una condivisione file di Azure con un'identità di servizi di dominio Active Directory. Per concedere ad altri utenti l'accesso alla condivisione file, seguire le istruzioni riportate nelle sezioni [assegnare le autorizzazioni di accesso](#2-assign-access-permissions-to-an-identity) per l'uso di un'identità e configurare le [autorizzazioni NTFS su SMB](#3-configure-ntfs-permissions-over-smb) .

## <a name="5-update-the-password-of-your-storage-account-identity-in-ad-ds"></a>5 aggiornare la password dell'identità dell'account di archiviazione in servizi di dominio Active Directory

Se è stata registrata l'identità o l'account di servizi di dominio Active Directory che rappresenta l'account di archiviazione in un'unità organizzativa che impone l'ora di scadenza della password, è necessario ruotare la password prima della validità massima della password. Se non si aggiorna la password dell'account di Active Directory Domain Services, si verificano errori di autenticazione per accedere alle condivisioni file di Azure.  

Per attivare la rotazione della password, è possibile `Update-AzStorageAccountADObjectPassword` eseguire il comando dal modulo AzFilesHybrid. Il cmdlet esegue azioni simili alla rotazione della chiave dell'account di archiviazione. Ottiene la seconda chiave Kerberos dell'account di archiviazione e la usa per aggiornare la password dell'account registrato in servizi di dominio Active Directory. Rigenera quindi la chiave Kerberos di destinazione dell'account di archiviazione e aggiorna la password dell'account registrato in servizi di dominio Active Directory. È necessario eseguire questo cmdlet in un ambiente di dominio AD DS locale.

```PowerShell
# Update the password of the AD DS account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su File di Azure e su come utilizzare AD tramite SMB, vedere le risorse seguenti:

- [Panoramica del supporto dell'autenticazione basata su identità File di Azure per l'accesso SMB](storage-files-active-directory-overview.md)
- [DOMANDE FREQUENTI](storage-files-faq.md)
