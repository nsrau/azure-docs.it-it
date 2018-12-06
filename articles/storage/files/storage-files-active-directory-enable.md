---
title: Abilitare l'autenticazione di Azure Active Directory tramite SMB per File di Azure (anteprima) - Archiviazione di Azure
description: Informazioni su come abilitare l'autenticazione basata su identità tramite SMB (Server Message Block) (anteprima) per File di Azure tramite Azure Active Directory (Azure AD) Domain Services. Le macchine virtuali di Windows aggiunte a dominio possono così accedere a condivisioni file di Azure tramite credenziali di Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/15/2018
ms.author: tamram
ms.openlocfilehash: c898a206322bbc6acb73d582fcb08c8bbba274d0
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2018
ms.locfileid: "52291441"
---
# <a name="enable-azure-active-directory-authentication-over-smb-for-azure-files-preview"></a>Abilitare l'autenticazione di Azure Active Directory tramite SMB per File di Azure (anteprima)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Per una panoramica dell'autenticazione di Azure AD tramite SMB per File di Azure, vedere [Panoramica dell'autenticazione di Azure Active Directory tramite SMB per File di Azure (anteprima)](storage-files-active-directory-overview.md).

## <a name="overview-of-the-workflow"></a>Panoramica del flusso di lavoro
Prima di abilitare l'autenticazione di Azure AD tramite SMB per File di Azure, verificare che Azure AD e gli ambienti di Archiviazione di Azure siano configurati correttamente. È consigliabile ricontrollare i [prerequisiti](#prerequisites) per assicurarsi di avere eseguito tutti i passaggi necessari. 

Successivamente, è possibile concedere l'accesso alle risorse di File di Azure con credenziali di Azure AD seguendo questa procedura: 

1. Abilitare l'autenticazione di Azure AD tramite SMB per l'account di archiviazione per registrare l'account di archiviazione nella distribuzione di Azure AD Domain Services associata.
2. Assegnare le autorizzazioni di accesso per una condivisione a un'identità di Azure AD (utente, gruppo o entità servizio).
3. Configurare le autorizzazioni NTFS su SMB per file e directory.
4. Montare una condivisione file di Azure da una macchina virtuale aggiunta a dominio.

Il diagramma seguente illustra l'intero flusso di lavoro per abilitare l'autenticazione di Azure AD tramite SMB per File di Azure. 

![Diagramma che mostra il flusso di lavoro per l'autenticazione di Azure AD tramite SMB per File di Azure](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Prerequisiti 

Prima di abilitare Azure AD tramite SMB per File di Azure, verificare che siano soddisfatti i prerequisiti seguenti:

1.  **Selezionare o creare un tenant di Azure AD.**

    È possibile usare un tenant nuovo o esistente per l'autenticazione di Azure AD tramite SMB. Il tenant e la condivisione file a cui si vuole accedere devono essere associati alla stessa sottoscrizione.

    Per creare un nuovo tenant di Azure AD, è possibile [aggiungere un tenant di Azure AD e una sottoscrizione di Azure AD](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Se è disponibile un tenant di Azure AD esistente, ma si vuole creare un nuovo tenant per l'uso con File di Azure, vedere [Creare un tenant di Azure Active Directory](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

2.  **Abilitare Azure AD Domain Services nel tenant di Azure AD.**

    Per supportare l'autenticazione con credenziali di Azure AD, è necessario abilitare Azure AD Domain Services per il tenant di Azure AD. Se non si è l'amministratore del tenant di Azure AD, contattare l'amministratore e seguire le istruzioni dettagliate per [Abilitare Azure Active Directory Domain Services tramite il portale di Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md).

    Per completare una distribuzione di Azure AD Domain Services sono richiesti in genere 15 minuti. Verificare che lo stato di integrità di Azure AD Domain Services sia **In esecuzione**, con sincronizzazione dell'hash delle password abilitata, prima di procedere al passaggio successivo.

3.  **Aggiungere una macchina virtuale di Azure a un dominio con Azure AD Domain Services.**

    Per accedere a una condivisione file usando le credenziali di Azure AD da una macchina virtuale, la macchina virtuale deve essere aggiunta a un dominio con Azure AD Domain Services. Per altre informazioni su come aggiungere una macchina virtuale a un dominio, vedere [Aggiungere una macchina virtuale Windows Server a un dominio gestito](../../active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal.md).

    > [!NOTE]
    > L'autenticazione di Azure AD tramite SMB con File di Azure è supportata solo nelle macchine virtuali di Azure in esecuzione in versioni del sistema operativo successive a Windows 7 o Windows Server 2008 R2.

4.  **Selezionare o creare una condivisione file di Azure.**

    Selezionare una condivisione file nuova o esistente associata alla stessa sottoscrizione del tenant di Azure AD. Per informazioni sulla creazione di una nuova condivisione file, vedere [Creare una condivisione file in File di Azure](storage-how-to-create-file-share.md). 

    Il tenant di Azure AD deve essere distribuito in un'area supportata per l'anteprima di Azure AD tramite SMB. L'anteprima è disponibile in tutte le aree pubbliche, ad eccezione di: Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti centro-meridionali, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centrali, Stati Uniti centro-settentrionali, Australia orientale, Europa occidentale, Europa settentrionale.

    Per ottenere prestazioni ottimali, Microsoft consiglia che la condivisione file si trovi nella stessa area della macchina virtuale da cui si prevede di accedere alla condivisione.

5.  **Verificare la connettività di File di Azure tramite il montaggio di condivisioni file di Azure usando la chiave dell'account di archiviazione.**

    Per verificare che la macchina virtuale e la condivisione file siano configurate correttamente, provare a montare la condivisione file usando la chiave dell'account di archiviazione. Per altre informazioni, vedere [Montare una condivisione file di Azure e accedere alla condivisione in Windows](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-authentication-for-your-account"></a>Abilitare l'autenticazione di Azure AD per l'account

Per abilitare l'autenticazione di Azure AD tramite SMB per File di Azure, è possibile impostare una proprietà per gli account di archiviazione creati dopo il 24 settembre 2018, usando il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure. L'impostazione di questa proprietà consente di registrare l'account di archiviazione nella distribuzione di Azure AD Domain Services associata. L'autenticazione di Azure AD tramite SMB viene quindi abilitata per tutte le condivisioni file nuove ed esistenti nell'account di archiviazione. 

Tenere presente che è possibile abilitare l'autenticazione di Azure AD tramite SMB solo dopo aver distribuito Azure AD Domain Services nel tenant di Azure AD. Per altre informazioni, vedere i [prerequisiti](#prerequisites).

### <a name="azure-portal"></a>Portale di Azure

Per abilitare l'autenticazione di Azure AD tramite SMB usando il [portale di Azure](https://portal.azure.com), eseguire le operazioni seguenti:

1. Nel portale di Azure passare all'account di archiviazione esistente o [creare un account di archiviazione](../common/storage-quickstart-create-account.md).
2. Nella sezione **Impostazioni** selezionare **Configurazione**.
3. Abilitare **Autenticazione di Azure Active Directory per File di Azure (anteprima)**.

L'immagine seguente mostra come abilitare l'autenticazione di Azure AD tramite SMB per l'account di archiviazione.

![Abilitare l'autenticazione di Azure AD tramite SMB nel portale di Azure](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)
  
### <a name="powershell"></a>PowerShell  

Per abilitare l'autenticazione di Azure AD tramite SMB da Azure PowerShell, installare prima il modulo `AzureRM.Storage`, versione `6.0.0-preview`, come indicato di seguito. Per altre informazioni sull'installazione di PowerShell, vedere [Installare Azure PowerShell in Windows con PowerShellGet](https://docs.microsoft.com/powershell/azure/install-azurerm-ps):

```powershell
Install-Module -Name AzureRM.Storage -RequiredVersion 6.0.0-preview -AllowPrerelease
```

Creare un nuovo account di archiviazione, quindi chiamare [Set-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/set-azurermstorageaccount) e impostare il parametro **EnableAzureFilesAadIntegrationForSMB** su **true**. Nell'esempio seguente ricordarsi di sostituire i valori segnaposto con i valori personalizzati.

```powershell
# Create a new storage account
New-AzureRmStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureFilesAadIntegrationForSMB $true

# Update an existing storage account
# Supported for storage accounts created after September 24, 2018 only
Set-AzureRmStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureFilesAadIntegrationForSMB $true```
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

# Update an existing storage account
# Supported for storage accounts created after September 24, 2018 only
az storage account update -n <storage-account-name> -g <resource-group-name> --file-aad true
```

## <a name="assign-access-permissions-to-an-identity"></a>Assegnare le autorizzazioni di accesso a un'identità 

Per accedere alle risorse di File di Azure tramite le credenziali di Azure AD, un'identità (utente, gruppo o entità servizio) deve avere le autorizzazioni necessarie a livello di condivisione. Le istruzioni dettagliate fornite in questa sezione spiegano come assegnare a un'identità le autorizzazioni di lettura, scrittura o eliminazione per una condivisione file.

> [!IMPORTANT]
> Per il controllo amministrativo completo di una condivisione file, inclusa la possibilità di assegnare un ruolo a un'identità, è necessario usare la chiave dell'account di archiviazione. Il controllo amministrativo non è supportato con le credenziali di Azure AD. 

### <a name="define-a-custom-role"></a>Definire un ruolo personalizzato

Per concedere autorizzazioni a livello di condivisione, definire un ruolo RBAC personalizzato e assegnarlo a un'identità, definendo come ambito una condivisione file specifica. Questo processo è simile alla specifica delle autorizzazioni per le condivisioni di Windows, in cui si specifica il tipo di accesso che ha un determinato utente per una condivisione file.  

I modelli illustrati nelle sezioni seguenti forniscono le autorizzazioni di lettura o modifica per una condivisione file. Per definire un ruolo personalizzato, creare un file JSON e copiare il modello appropriato in tale file. Per altre informazioni sulla definizione di ruoli RBAC personalizzati, vedere [Ruoli personalizzati in Azure](../../role-based-access-control/custom-roles.md).

**Definizione del ruolo per le autorizzazioni di modifica a livello di condivisione**  
Il modello di ruolo personalizzato seguente fornisce le autorizzazioni di modifica a livello di condivisione, concedendo a un'identità l'accesso per lettura, scrittura ed eliminazione alla condivisione.

```json
{
  "Name": "<Custom-Role-Name>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read, write and delete access to Azure File Share",
  "Actions": [
    "*"
  ],
  "NotActions": [
      "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ],
  "DataActions": [
    "*"
  ],
  "AssignableScopes": [
        "/subscriptions/<Subscription-ID>"
  ]
}
```

**Definizione del ruolo per le autorizzazioni di lettura a livello di condivisione**  
Il modello di ruolo personalizzato seguente fornisce le autorizzazioni di lettura a livello di condivisione, concedendo a un'identità l'accesso in lettura alla condivisione.

```json
{
  "Name": "<Custom-Role-Name>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure File Share",
  "Actions": [
    "*/read"
  ],
  "DataActions": [
    "*/read"
  ],
  "AssignableScopes": [
        "/subscriptions/<Subscription-ID>"
  ]
}
```

### <a name="create-the-custom-role"></a>Creare il ruolo personalizzato

Per creare il ruolo personalizzato, usare PowerShell o l'interfaccia della riga di comando di Azure. 

#### <a name="powershell"></a>PowerShell

Il comando seguente di PowerShell consente di creare un ruolo personalizzato basato su uno dei modelli di esempio.

```powershell
#Create a custom role based on the sample template above
New-AzureRmRoleDefinition -InputFile "<custom-role-def-json-path>"
```

#### <a name="cli"></a>CLI 

Il comando seguente dell'interfaccia della riga di comando di Azure consente di creare un ruolo personalizzato basato su uno dei modelli di esempio.

```azurecli-interactive
#Create a custom role based on the sample templates above
az role definition create --role-definition "<Custom-role-def-JSON-path>"
```

### <a name="assign-the-custom-role-to-the-target-identity"></a>Assegnare il ruolo personalizzato all'identità di destinazione

A questo punto, usare PowerShell o l'interfaccia della riga di comando di Azure per assegnare il ruolo personalizzato a un'identità di Azure AD. 

#### <a name="powershell"></a>PowerShell

Il comando seguente di PowerShell mostra come visualizzare l'elenco dei ruoli personalizzati disponibili e quindi assegna un ruolo personalizzato a un'identità di Azure AD, in base al nome di accesso. Per altre informazioni sull'assegnazione dei ruoli RBAC con PowerShell, vedere [Gestire l'accesso tramite il controllo degli accessi in base al ruolo e Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Quando si esegue lo script di esempio seguente, ricordarsi di sostituire i valori segnaposto, incluse le parentesi, con i valori personalizzati.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzureRmRoleDefinition "<role-name>"
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzureRmRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
Il comando seguente dell'interfaccia della riga di comando 2.0 mostra come visualizzare l'elenco dei ruoli personalizzati disponibili e quindi assegna un il ruolo a un'identità di Azure AD, in base al nome di accesso. Per altre informazioni sull'assegnazione dei ruoli RBAC con l'interfaccia della riga di comando di Azure, vedere [Gestire l'accesso tramite il controllo degli accessi in base al ruolo e l'interfaccia della riga di comando di Azure](../../role-based-access-control/role-assignments-cli.md). 

Quando si esegue lo script di esempio seguente, ricordarsi di sostituire i valori segnaposto, incluse le parentesi, con i valori personalizzati.

```azurecli-interactive
#List the custom roles
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
#Assign the custom role to the target identity
az role assignment create --role "<custome-role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
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
