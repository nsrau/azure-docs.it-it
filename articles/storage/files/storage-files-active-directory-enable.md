---
title: Abilitare l'autenticazione di Azure Active Directory tramite SMB per File di Azure (anteprima) - Archiviazione di Azure
description: Informazioni su come abilitare l'autenticazione basata su identità tramite SMB (Server Message Block) (anteprima) per File di Azure tramite Azure Active Directory (Azure AD) Domain Services. Le macchine virtuali di Windows aggiunte a dominio possono così accedere a condivisioni file di Azure tramite credenziali di Azure AD.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 07/05/2019
ms.author: rogarana
ms.openlocfilehash: cd4c952caa336f2602d3c30e0db3e10ebee59cb9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67696116"
---
# <a name="enable-azure-active-directory-domain-service-authentication-over-smb-for-azure-files-preview"></a>Abilitare l'autenticazione di Azure Active Directory Domain Service su SMB per i file di Azure (anteprima)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Per una panoramica dell'autenticazione di Azure AD tramite SMB per File di Azure, vedere [Panoramica dell'autenticazione di Azure Active Directory tramite SMB per File di Azure (anteprima)](storage-files-active-directory-overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>Panoramica del flusso di lavoro
Prima di abilitare l'autenticazione di Azure AD DS tramite SMB per i file di Azure, verificare che Azure AD e gli ambienti di archiviazione di Azure sono configurati correttamente. È consigliabile ricontrollare i [prerequisiti](#prerequisites) per assicurarsi di avere eseguito tutti i passaggi necessari. 

Successivamente, è possibile concedere l'accesso alle risorse di File di Azure con credenziali di Azure AD seguendo questa procedura: 

1. Abilitare l'autenticazione di Azure Active Directory Domain Services tramite SMB per l'account di archiviazione per registrare l'account di archiviazione con la distribuzione di Azure AD Domain Services associato.
2. Assegnare le autorizzazioni di accesso per una condivisione a un'identità di Azure AD (utente, gruppo o entità servizio).
3. Configurare le autorizzazioni NTFS su SMB per file e directory.
4. Montare una condivisione file di Azure da una macchina virtuale aggiunta a dominio.

Il diagramma seguente illustra il flusso di lavoro end-to-end per abilitare l'autenticazione di Azure Active Directory Domain Services tramite SMB per i file di Azure. 

![Diagramma che mostra il flusso di lavoro per l'autenticazione di Azure AD tramite SMB per File di Azure](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Prerequisiti 

Prima di abilitare Azure AD tramite SMB per File di Azure, verificare che siano soddisfatti i prerequisiti seguenti:

1.  **Selezionare o creare un tenant di Azure AD.**

    È possibile usare un tenant nuovo o esistente per l'autenticazione di Azure AD tramite SMB. Il tenant e la condivisione file a cui si vuole accedere devono essere associati alla stessa sottoscrizione.

    Per creare un nuovo tenant di Azure AD, è possibile [aggiungere un tenant di Azure AD e una sottoscrizione di Azure AD](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Se è disponibile un tenant di Azure AD esistente, ma si vuole creare un nuovo tenant per l'uso con File di Azure, vedere [Creare un tenant di Azure Active Directory](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

2.  **Abilitare Azure AD Domain Services nel tenant di Azure AD.**

    Per supportare l'autenticazione con credenziali di Azure AD, è necessario abilitare Azure AD Domain Services per il tenant di Azure AD. Se non si è l'amministratore del tenant di Azure AD, contattare l'amministratore e seguire le istruzioni dettagliate per [Abilitare Azure Active Directory Domain Services tramite il portale di Azure](../../active-directory-domain-services/create-instance.md).

    Per completare una distribuzione di Azure AD Domain Services sono richiesti in genere 15 minuti. Verificare che lo stato di integrità di Azure AD Domain Services sia **In esecuzione**, con sincronizzazione dell'hash delle password abilitata, prima di procedere al passaggio successivo.

3.  **Aggiungere una macchina virtuale di Azure a un dominio con Azure AD Domain Services.**

    Per accedere a una condivisione file usando le credenziali di Azure AD da una macchina virtuale, la macchina virtuale deve essere aggiunta a un dominio con Azure AD Domain Services. Per altre informazioni su come aggiungere una macchina virtuale a un dominio, vedere [Aggiungere una macchina virtuale Windows Server a un dominio gestito](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > L'autenticazione di Azure Active Directory Domain Services tramite SMB con file di Azure è supportata solo nelle macchine virtuali di Azure in esecuzione in versioni del sistema operativo precedente a Windows 7 o Windows Server 2008 R2.

4.  **Selezionare o creare una condivisione file di Azure.**

    Selezionare una condivisione file nuova o esistente associata alla stessa sottoscrizione del tenant di Azure AD. Per informazioni sulla creazione di una nuova condivisione file, vedere [Creare una condivisione file in File di Azure](storage-how-to-create-file-share.md). 
    Per ottenere prestazioni ottimali, Microsoft consiglia che la condivisione file si trovi nella stessa area della macchina virtuale da cui si prevede di accedere alla condivisione.

5.  **Verificare la connettività di File di Azure tramite il montaggio di condivisioni file di Azure usando la chiave dell'account di archiviazione.**

    Per verificare che la macchina virtuale e la condivisione file siano configurate correttamente, provare a montare la condivisione file usando la chiave dell'account di archiviazione. Per altre informazioni, vedere [Montare una condivisione file di Azure e accedere alla condivisione in Windows](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Abilitare l'autenticazione di Azure Active Directory Domain Services per l'account

Per abilitare l'autenticazione di Azure Active Directory Domain Services tramite SMB per i file di Azure, è possibile impostare una proprietà in account di archiviazione creati dopo il 24 settembre 2018, usando il portale di Azure, Azure PowerShell o CLI di Azure. L'impostazione di questa proprietà consente di registrare l'account di archiviazione nella distribuzione di Azure AD Domain Services associata. L'autenticazione di Azure Active Directory Domain Services tramite SMB viene quindi abilitata per tutte le condivisioni di file nuovi ed esistenti nell'account di archiviazione. 

Tenere presente che è possibile abilitare l'autenticazione di Azure Active Directory Domain Services tramite SMB solo dopo aver distribuito Azure AD Domain Services per il tenant di Azure AD. Per altre informazioni, vedere i [prerequisiti](#prerequisites).

### <a name="azure-portal"></a>Portale di Azure

Per abilitare l'autenticazione di Azure Active Directory Domain Services tramite SMB usando il [portale di Azure](https://portal.azure.com), seguire questa procedura:

1. Nel portale di Azure passare all'account di archiviazione esistente o [creare un account di archiviazione](../common/storage-quickstart-create-account.md).
2. Nella sezione **Impostazioni** selezionare **Configurazione**.
3. Abilitare **Autenticazione di Azure Active Directory per File di Azure (anteprima)** .

L'immagine seguente mostra come abilitare l'autenticazione di Azure AD tramite SMB per l'account di archiviazione.

![Abilitare l'autenticazione di Azure AD tramite SMB nel portale di Azure](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)
  
### <a name="powershell"></a>PowerShell  

Per abilitare l'autenticazione di Azure Active Directory Domain Services tramite SMB usando Azure PowerShell: Prima di tutto installare il modulo di Az più recente (2.4 o versioni successive) o il modulo Az.Storage (1.5 o successiva). Per altre informazioni sull'installazione di PowerShell, vedere [installare Azure PowerShell su Windows con PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps):

```powershell
Install-Module -Name Az.Storage -AllowPrerelease -Force -AllowClobber
```

Successivamente, creare una nuova risorsa di archiviazione dell'account, quindi chiamare [Set-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) e impostare le **EnableAzureActiveDirectoryDomainServicesForFile** parametro **true**. Nell'esempio seguente ricordarsi di sostituire i valori segnaposto con i valori personalizzati. (Se si usa il modulo di anteprima precedente, il parametro per l'abilitazione di funzionalità viene **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```
Per abilitare questa funzionalità per gli account di archiviazione esistenti, usare il comando seguente:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per abilitare l'autenticazione di Azure AD tramite SMB dall'interfaccia della riga di comando di Azure 2.0, installare prima l'estensione `storage-preview`:

```cli-interactive
az extension add --name storage-preview
```
  
Creare un nuovo account di archiviazione, quindi chiamare [az storage account update](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) e impostare la proprietà `--file-aad` su **true**. Nell'esempio seguente ricordarsi di sostituire i valori segnaposto con i valori personalizzati.

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --file-aad true
```

## <a name="assign-access-permissions-to-an-identity"></a>Assegnare le autorizzazioni di accesso a un'identità 

Per accedere alle risorse di File di Azure tramite le credenziali di Azure AD, un'identità (utente, gruppo o entità servizio) deve avere le autorizzazioni necessarie a livello di condivisione. Questo processo è simile alla specifica delle autorizzazioni per le condivisioni di Windows, in cui si specifica il tipo di accesso che ha un determinato utente per una condivisione file. Le istruzioni dettagliate fornite in questa sezione spiegano come assegnare a un'identità le autorizzazioni di lettura, scrittura o eliminazione per una condivisione file.

Abbiamo introdotto due ruoli predefiniti di Azure per concedere autorizzazioni a livello di condivisione agli utenti: Lettore di condivisione SMB di archiviazione File dei dati e collaboratore di condivisione SMB di archiviazione File Data. 

- **Lettore di condivisione SMB di archiviazione File di dati** consente l'accesso in lettura in condivisioni file di archiviazione di Azure tramite SMB
- **Collaboratore di condivisione SMB di archiviazione File di dati** consente l'accesso in lettura, scrittura e delete in condivisioni file di archiviazione di Azure tramite SMB

> [!IMPORTANT]
> Per il controllo amministrativo completo di una condivisione file, inclusa la possibilità di assegnare un ruolo a un'identità, è necessario usare la chiave dell'account di archiviazione. Il controllo amministrativo non è supportato con le credenziali di Azure AD. 

È possibile usare il portale di Azure, PowerShell o CLI di Azure per assegnare i ruoli predefiniti per l'identità di Azure AD di un utente per concedere autorizzazioni a livello di condivisione. 

#### <a name="azure-portal"></a>Portale di Azure
Per assegnare un ruolo RBAC a un'identità di Azure AD, usando il [portale di Azure](https://portal.azure.com), seguire questa procedura:

1. Nel portale di Azure, passare alla condivisione file, oppure [creare una condivisione file in file di Azure](storage-how-to-create-file-share.md).
2. Selezionare **Controllo di accesso (IAM)** .
3. Selezionare **aggiungere un'assegnazione di ruolo**
4. Nel **aggiungere un'assegnazione di ruolo** pannello, selezionare il ruolo predefinito appropriato (lettore di condivisione SMB di archiviazione File di dati, il collaboratore condivisione SMB di archiviazione File di dati) tra il **ruolo** nell'elenco a discesa. Mantenere il **assegna accesso a** come valore predefinito: "Utente di azure AD, gruppo o entità servizio", quindi selezionare la destinazione di identità di Azure AD dall'indirizzo di posta elettronica o nome. 
5. Infine, selezionare **salvare** per completare l'operazione di assegnazione di ruolo.

#### <a name="powershell"></a>PowerShell

Il comando PowerShell seguente illustra come assegnare un ruolo RBAC a un'identità di Azure AD, in base al nome di accesso. Per altre informazioni sull'assegnazione dei ruoli RBAC con PowerShell, vedere [Gestire l'accesso tramite il controllo degli accessi in base al ruolo e Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Quando si esegue lo script di esempio seguente, ricordarsi di sostituire i valori segnaposto, incluse le parentesi, con i valori personalizzati.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
Il comando CLI 2.0 seguente illustra come assegnare un ruolo RBAC a un'identità di Azure AD, in base al nome di accesso. Per altre informazioni sull'assegnazione dei ruoli RBAC con l'interfaccia della riga di comando di Azure, vedere [Gestire l'accesso tramite il controllo degli accessi in base al ruolo e l'interfaccia della riga di comando di Azure](../../role-based-access-control/role-assignments-cli.md). 

Quando si esegue lo script di esempio seguente, ricordarsi di sostituire i valori segnaposto, incluse le parentesi, con i valori personalizzati.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>Configurare le autorizzazioni NTFS tramite SMB 
Dopo aver assegnato le autorizzazioni a livello di condivisione con RBAC, è necessario assegnare le autorizzazioni NTFS appropriate a livello di radice, directory o file. Le autorizzazioni a livello di condivisione possono essere paragonate al cancello principale che determina se un utente può accedere alla condivisione, mentre le autorizzazioni NTFS operano a un livello più granulare per determinare quali operazioni l'utente può eseguire a livello di file o directory. 

File di Azure supporta il set completo di autorizzazioni NTFS di base e avanzate. È possibile visualizzare e configurare le autorizzazioni NTFS per le directory e i file in una condivisione file di Azure montando la condivisione e quindi eseguendo il comando di Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) oppure [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl). 

> [!NOTE]
> La versione di anteprima supporta la visualizzazione delle autorizzazioni solo con Esplora file di Windows. La modifica delle autorizzazioni non è ancora supportata.

Per configurare le autorizzazioni NTFS con privilegi di utente con privilegi avanzati, è necessario montare la condivisione con la chiave dell'account di archiviazione dalla macchina virtuale aggiunta al dominio. Seguire le istruzioni nella sezione successiva per montare una condivisione file di Azure dal prompt dei comandi e configurare le autorizzazioni NTFS in modo appropriato.

I set di autorizzazioni seguenti sono supportati per la directory radice di una condivisione file:

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Montare una condivisione file dal prompt dei comandi

Usare il comando di Windows **net use** per montare la condivisione file di Azure. Ricordarsi di sostituire i valori segnaposto nell'esempio con valori personalizzati. Per altre informazioni sul montaggio delle condivisioni file, vedere [Montare una condivisione file di Azure e accedere alla condivisione in Windows](storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```

### <a name="configure-ntfs-permissions-with-icacls"></a>Configurare le autorizzazioni NTFS con icacls
Usare il comando seguente di Windows per concedere autorizzazioni complete a tutti i file e le sottodirectory nella condivisione file, inclusa la directory radice. Ricordarsi di sostituire i valori segnaposto racchiusi tra parentesi nell'esempio con valori personalizzati.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Per altre informazioni su come usare icacls per impostare le autorizzazioni NTFS e sui diversi tipi di autorizzazioni supportate, vedere le [informazioni di riferimento della riga di comando per icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Montare una condivisione file da una macchina virtuale aggiunta a dominio 

A questo punto è possibile verificare di avere completato correttamente i passaggi precedenti usando le credenziali di Azure AD per accedere a una condivisione file di Azure da una macchina virtuale aggiunta a un dominio. Prima di tutto, accedere alla macchina virtuale usando l'identità di Azure AD a cui sono state concesse le autorizzazioni, come illustrato nell'immagine seguente.

![Screenshot che mostra la schermata di accesso ad Azure AD per l'autenticazione utente](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

Usare poi il comando seguente per montare la condivisione file di Azure. Ricordarsi di sostituire i valori segnaposto con valori personalizzati. Dato che l'autenticazione è già avvenuta, non è necessario fornire la chiave dell'account di archiviazione o il nome utente e la password di Azure AD. Azure AD su SMB supporta un'esperienza Single Sign-On usando le credenziali di Azure AD.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

A questo punto è stata abilitata l'autenticazione di AD Azure tramite SMB ed è stato assegnato un ruolo personalizzato che fornisce l'accesso a una condivisione file a un'identità di Azure AD. Per concedere l'accesso alla condivisione file ad altri utenti, seguire le istruzioni indicate nel passaggio 2 e 3.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su File di Azure e sull'uso di Azure AD tramite SMB, vedere queste risorse:

- [Introduzione a File di Azure](storage-files-introduction.md)
- [Panoramica dell'autenticazione di Azure Active Directory tramite SMB per File di Azure (anteprima)](storage-files-active-directory-overview.md)
- [Domande frequenti](storage-files-faq.md)
