---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/11/2019
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: b6a8bc083b589463b67f2e25e262b15456355d05
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383858"
---
## <a name="2-assign-access-permissions-to-an-identity"></a>2. Assegnare le autorizzazioni di accesso a un'identità

Per accedere alle risorse File di Azure con autenticazione basata sull'identità, un'identità (un utente, un gruppo o un'entità servizio) deve disporre delle autorizzazioni necessarie a livello di condivisione. Questo processo è simile alla specifica delle autorizzazioni di condivisione di Windows, in cui si specifica il tipo di accesso di un determinato utente a una condivisione file. Le istruzioni dettagliate fornite in questa sezione spiegano come assegnare a un'identità le autorizzazioni di lettura, scrittura o eliminazione per una condivisione file. 

Sono stati introdotti tre ruoli predefiniti di Azure per concedere autorizzazioni a livello di condivisione agli utenti:We have introduced three Azure built-in roles for granting share-level permissions to users:

- Lettore condivisione **SMB dei dati dei file** di archiviazione consente l'accesso in lettura nelle condivisioni file di Archiviazione di Azure tramite SMB.
- **Il collaboratore alla condivisione SMB dei file** di archiviazione consente l'accesso in lettura, scrittura ed eliminazione nelle condivisioni file di Archiviazione di Azure tramite SMB.
- **L'acquisizione** di condivisioni con privilegi elevati dei dati di archiviazione consente la lettura, la scrittura, l'eliminazione e la modifica delle autorizzazioni NTFS nelle condivisioni file di Archiviazione di Azure su SMB.

> [!IMPORTANT]
> Il controllo amministrativo completo di una condivisione file, inclusa la possibilità di assumere la proprietà di un file, richiede l'uso della chiave dell'account di archiviazione. Il controllo amministrativo non è supportato con le credenziali di Azure AD.

È possibile usare il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure per assegnare i ruoli predefiniti all'identità di Azure AD di un utente per concedere autorizzazioni a livello di condivisione.

> [!NOTE]
> Ricordarsi di sincronizzare le credenziali di Active Directory con Azure AD se si prevede di usare l'AD per l'autenticazione. La sincronizzazione dell'hash delle password da AD ad Azure AD è facoltativa. L'autorizzazione a livello di condivisione verrà concessa all'identità di Azure AD sincronizzata da Active Directory.Share level permission will be granted to the Azure AD identity that is synced from AD.

Si consiglia di utilizzare l'autorizzazione a livello di condivisione per la gestione degli accessi di alto livello a un gruppo di Active Directory che rappresenta un gruppo di utenti e identità, quindi sfruttare le autorizzazioni NTFS per il controllo granulare dell'accesso a livello di directory/file. 

#### <a name="azure-portal"></a>Portale di Azure
Per assegnare un ruolo Controllo degli accessi in base al ruolo a un'identità di Azure AD, usando il portale di [Azure,](https://portal.azure.com)eseguire la procedura seguente:To assign an RBAC role to an Azure AD identity, using the Azure portal , follow these steps:

1. Nel portale di Azure passare alla condivisione file o [creare una condivisione file.](../articles/storage/files/storage-how-to-create-file-share.md)
2. Selezionare **Controllo di accesso (IAM)**.
3. Selezionare **Aggiungi un'assegnazione** di ruolo
4. Nel pannello **Aggiungi assegnazione ruolo** selezionare il ruolo predefinito predefinito (Lettura condivisione SMB file di archiviazione, Lettore condivisione SMB dei dati di archiviazione) dall'elenco **Ruolo.** Lasciare **Assegna accesso all'impostazione** predefinita: Utente, gruppo o entità servizio di Azure **AD.** Selezionare l'identità di Azure AD di destinazione in base al nome o all'indirizzo di posta elettronica.
5. Selezionare **Salva** per completare l'operazione di assegnazione dei ruoli.

#### <a name="powershell"></a>PowerShell

L'esempio di PowerShell seguente mostra come assegnare un ruolo Controllo degli accessi in base al ruolo a un'identità di Azure AD, in base al nome di accesso. Per altre informazioni sull'assegnazione dei ruoli RBAC con PowerShell, vedere [Gestire l'accesso tramite il controllo degli accessi in base al ruolo e Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).

Prima di eseguire lo script di esempio seguente, ricordarsi di sostituire i valori segnaposto, incluse le parentesi quadre, con valori personalizzati.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
Il comando CLI 2.0 seguente illustra come assegnare un ruolo Controllo degli accessi in base al ruolo a un'identità di Azure AD, in base al nome di accesso. Per altre informazioni sull'assegnazione di ruoli RBAC con l'interfaccia della riga di comando di Azure, vedere [Gestire l'accesso tramite RBAC e l'interfaccia della riga di comando](../articles/role-based-access-control/role-assignments-cli.md)di Azure.For more information about assigning RBAC roles with Azure CLI, see Manage access by using RBAC and Azure CLI. 

Prima di eseguire lo script di esempio seguente, ricordarsi di sostituire i valori segnaposto, incluse le parentesi quadre, con valori personalizzati.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="3-configure-ntfs-permissions-over-smb"></a>3. Configurare le autorizzazioni NTFS su SMB 
Dopo aver assegnato le autorizzazioni a livello di condivisione con RBAC, è necessario assegnare le autorizzazioni NTFS appropriate a livello di radice, directory o file. Considerare le autorizzazioni a livello di condivisione come gatekeeper di alto livello che determina se un utente può accedere alla condivisione. Mentre le autorizzazioni NTFS agiscono a un livello più granulare per determinare le operazioni che l'utente può eseguire a livello di directory o di file.

File di Azure supporta il set completo di autorizzazioni NTFS di base e avanzate. È possibile visualizzare e configurare le autorizzazioni NTFS per directory e file in una condivisione file di Azure montando la condivisione e quindi utilizzando Esplora file di Windows o eseguendo il comando [Icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) o [Set-ACL di](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) Windows. 

Per configurare NTFS con autorizzazioni di utente avanzato, è necessario montare la condivisione usando la chiave dell'account di archiviazione dalla macchina virtuale aggiunta al dominio. Seguire le istruzioni nella sezione successiva per montare una condivisione file di Azure dal prompt dei comandi e configurare le autorizzazioni NTFS di conseguenza.

I set di autorizzazioni seguenti sono supportati per la directory radice di una condivisione file:

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="configure-ntfs-permissions-with-icacls"></a>Configurare le autorizzazioni NTFS con icacls
Usare il comando seguente di Windows per concedere autorizzazioni complete a tutti i file e le sottodirectory nella condivisione file, inclusa la directory radice. Ricordarsi di sostituire i valori segnaposto nell'esempio con valori personalizzati.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Per ulteriori informazioni sull'utilizzo dei icacl per impostare le autorizzazioni NTFS e sui diversi tipi di autorizzazioni supportate, vedere le informazioni di riferimento sulla riga di [comando per icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

### <a name="mount-a-file-share-from-the-command-prompt"></a>Montare una condivisione file dal prompt dei comandi

Usare il comando di Windows **net use** per montare la condivisione file di Azure. Ricordarsi di sostituire i valori segnaposto nell'esempio seguente con valori personalizzati. Per altre informazioni sul montaggio di condivisioni file, vedere Usare una condivisione file di [Azure con Windows.](../articles/storage/files/storage-how-to-use-files-windows.md) 

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Configurare le autorizzazioni NTFS con Esplora file di Windows
Utilizzare Esplora file di Windows per concedere l'autorizzazione completa a tutte le directory e i file nella condivisione file, inclusa la directory radice.

1. Aprire Esplora file di Windows e fare clic con il pulsante destro del mouse sul file/directory e selezionare **Proprietà**
2. Fare clic sulla scheda **Sicurezza**
3. Fare clic su **Modifica..**. per modificare le autorizzazioni
4. È possibile modificare l'autorizzazione degli utenti esistenti o fare clic su **Aggiungi...** per concedere autorizzazioni ai nuovi utenti
5. Nella finestra di richiesta per l'aggiunta di nuovi utenti, immettere il nome utente di destinazione a cui si desidera concedere l'autorizzazione nella casella Immettere i nomi degli **oggetti da selezionare** e fare clic su Controlla **nomi** per trovare il nome UPN completo dell'utente di destinazione.
7.  Fare clic su **OK**
8.  Nella scheda Sicurezza, selezionare tutte le autorizzazioni che si desidera concedere all'utente appena aggiunto
9.  Fare clic su **Applica**

## <a name="4-mount-a-file-share-from-a-domain-joined-vm"></a>4. Montare una condivisione file da una macchina virtuale aggiunta a un dominio

Il processo seguente verifica che le autorizzazioni di condivisione file e accesso siano state configurate correttamente e che sia possibile accedere a una condivisione file di Azure da una macchina virtuale aggiunta a un dominio. Tenere presente che l'assegnazione di ruolo RBAC a livello di condivisione può richiedere del tempo per essere attiva. 

Accedere alla macchina virtuale usando l'identità di Azure AD a cui sono state concesse le autorizzazioni, come illustrato nell'immagine seguente. Se è stata abilitata l'autenticazione di Active Directory per i file di Azure, usare le credenziali di Active Directory.If you have enabled AD authentication for Azure Files, use the AD credential. Per l'autenticazione di Azure AD DS, accedere con le credenziali di Azure AD.

![Screenshot che mostra la schermata di accesso ad Azure AD per l'autenticazione utente](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Usare il comando seguente per montare la condivisione file di Azure.Use the following command to mount the Azure file share. Ricordarsi di sostituire i valori segnaposto con valori personalizzati. Poiché è stato autenticato, non è necessario fornire la chiave dell'account di archiviazione, le credenziali di Active Directory o le credenziali di Azure AD. L'esperienza Single Sign-On è supportata per l'autenticazione con SERVIZI di dominio Active Directory o Azure AD. Se si verificano problemi durante l'installazione con le credenziali di Active Directory, vedere Risolvere i problemi relativi [ai file](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) di Azure in Windows per indicazioni sulla diagnostica automatica.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```
