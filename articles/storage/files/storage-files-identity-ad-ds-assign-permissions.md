---
title: Controllare l'accesso alle condivisioni file di Azure-autenticazione locale di servizi di dominio Active Directory
description: Informazioni su come assegnare le autorizzazioni a un'identità Active Directory Domain Services che rappresenta l'account di archiviazione. Questo consente di controllare l'accesso con l'autenticazione basata su identità.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: 287bd02a11c71fbdd29b28b5ec9fc8424a477fea
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320354"
---
# <a name="part-two-assign-share-level-permissions-to-an-identity"></a>Parte 2: assegnare autorizzazioni a livello di condivisione a un'identità

Prima di iniziare questo articolo, assicurarsi di aver completato l'articolo precedente, [abilitare l'autenticazione di servizi di dominio Active Directory per l'account](storage-files-identity-ad-ds-enable.md).

Dopo aver abilitato l'autenticazione Active Directory Domain Services (AD DS) nell'account di archiviazione, è necessario configurare le autorizzazioni a livello di condivisione per ottenere l'accesso alle condivisioni file. L'identità a cui si vuole accedere alle risorse di condivisione file di Azure deve essere un'identità ibrida presente in servizi di dominio Active Directory e Azure AD. Si immagini, ad esempio, di avere un utente in servizi di dominio Active Directory user1@onprem.contoso.com ed è stato sincronizzato con Azure ad come user1@contoso.com usando Azure ad Connect sincronizzazione. Per consentire all'utente di accedere File di Azure, è necessario assegnare le autorizzazioni a livello di condivisione a user1@contoso.com . Lo stesso concetto si applica a gruppi o entità servizio. Per questo motivo, è necessario sincronizzare gli utenti e i gruppi da servizi di dominio Active Directory a Azure AD tramite Azure AD Connect sincronizzazione. 

Per supportare l'autenticazione di servizi di dominio Active Directory per la condivisione file di Azure, è necessario assegnare le autorizzazioni a livello di condivisione all'identità del Azure AD che rappresenta lo stesso utente o gruppo in servizi di dominio Active Directory. L'autenticazione e l'autorizzazione per le identità che esistono solo in Azure AD, ad esempio le identità gestite di Azure (MSI), non sono supportate con l'autenticazione di servizi di dominio Active Directory. Questo articolo illustra come assegnare autorizzazioni a livello di condivisione per una condivisione file a un'identità.


## <a name="share-level-permissions"></a>Autorizzazioni a livello di condivisione

In genere, è consigliabile usare le autorizzazioni a livello di condivisione per la gestione degli accessi di alto livello per un gruppo di Azure AD che rappresenta un gruppo di utenti e identità, quindi sfruttando gli ACL di Windows per il controllo di accesso granulare a livello di directory/file. 

Sono disponibili tre ruoli predefiniti di Azure per la concessione di autorizzazioni a livello di condivisione agli utenti:

- **Storage file data SMB Share Reader** consente l'accesso in lettura alle condivisioni file di archiviazione di Azure tramite SMB.
- Il **collaboratore condivisione SMB per i dati dei file di archiviazione** consente l'accesso in lettura, scrittura ed eliminazione nelle condivisioni file di archiviazione di Azure tramite SMB.
- **Storage file data SMB Share collaboratore con privilegi elevati** consente la lettura, la scrittura, l'eliminazione e la modifica degli ACL di Windows nelle condivisioni file di archiviazione di Azure tramite SMB.

> [!IMPORTANT]
> Il controllo amministrativo completo di una condivisione file, inclusa la possibilità di assumere la proprietà di un file, richiede l'uso della chiave dell'account di archiviazione. Il controllo amministrativo non è supportato con le credenziali di Azure AD.

È possibile usare l'interfaccia della riga di comando di portale di Azure, Azure PowerShell o Azure per assegnare i ruoli predefiniti all'identità Azure AD di un utente per concedere autorizzazioni a livello di condivisione.

## <a name="assign-an-azure-role"></a>Assegnare un ruolo di Azure

### <a name="azure-portal"></a>Portale di Azure

Per assegnare un ruolo di Azure a un'identità di Azure AD, usando il [portale di Azure](https://portal.azure.com), seguire questa procedura:

1. Nella portale di Azure passare alla condivisione file o [creare una condivisione file](storage-how-to-create-file-share.md).
1. Selezionare **Controllo di accesso (IAM)** .
1. Selezionare **Aggiungi un'assegnazione di ruolo**
1. Nel pannello **Aggiungi assegnazione ruolo** selezionare il ruolo predefinito appropriato (Storage file data SMB Share Reader, storage file data SMB condivisione Contributor) dall'elenco dei **ruoli** . Lasciare **assegna accesso a** con l'impostazione predefinita: **Azure ad utente, gruppo o entità servizio**. Selezionare la destinazione Azure AD identità in base al nome o all'indirizzo di posta elettronica. **L'identità del Azure AD selezionata deve essere un'identità ibrida e non può essere un'identità solo cloud.** Ciò significa che la stessa identità viene rappresentata anche in servizi di dominio Active Directory.
1. Selezionare **Salva** per completare l'operazione di assegnazione di ruolo.

### <a name="powershell"></a>PowerShell

L'esempio di PowerShell seguente illustra come assegnare un ruolo di Azure a un'identità di Azure AD, in base al nome di accesso. Per altre informazioni sull'assegnazione di ruoli di Azure con PowerShell, vedere [gestire l'accesso con RBAC e Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Prima di eseguire lo script di esempio seguente, sostituire i valori segnaposto, comprese le parentesi quadre, con i valori.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

### <a name="cli"></a>CLI
  
Il comando seguente dell'interfaccia della riga di comando 2,0 assegna un ruolo di Azure a un'identità di Azure AD, in base al nome di accesso. Per altre informazioni sull'assegnazione di ruoli di Azure con l'interfaccia della [riga di comando di Azure, vedere Manage Access by using RBAC and Azure CLI](../../role-based-access-control/role-assignments-cli.md). 

Prima di eseguire lo script di esempio seguente, ricordarsi di sostituire i valori segnaposto, comprese le parentesi, con valori personalizzati.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="next-steps"></a>Passaggi successivi

Ora che sono state assegnate le autorizzazioni a livello di condivisione, è necessario configurare le autorizzazioni a livello di directory e di file. Continuare con l'articolo successivo.

[Parte 3: configurare le autorizzazioni a livello di directory e di file su SMB](storage-files-identity-ad-ds-configure-permissions.md)
