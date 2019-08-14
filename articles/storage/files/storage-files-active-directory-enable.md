---
title: Abilitare l'autenticazione Azure Active Directory su SMB per File di Azure-archiviazione di Azure
description: Informazioni su come abilitare l'autenticazione basata su identità su Server Message Block (SMB) per File di Azure tramite Azure Active Directory Domain Services. Le macchine virtuali Windows (VM) appartenenti a un dominio possono quindi accedere alle condivisioni file di Azure usando le credenziali Azure AD.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: rogarana
ms.openlocfilehash: 2b5ebc9f35dd207e8e530b7d74acc5517125fbf4
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934993"
---
# <a name="enable-azure-active-directory-domain-services-authentication-over-smb-for-azure-files"></a>Abilitare l'autenticazione Azure Active Directory Domain Services su SMB per File di Azure
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Per una panoramica dell'autenticazione Azure AD su SMB per File di Azure, vedere [Panoramica dell'autenticazione di Azure Active Directory su SMB per file di Azure](storage-files-active-directory-overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>Panoramica del flusso di lavoro
Prima di abilitare l'autenticazione di Azure AD DS su SMB per File di Azure, verificare che il Azure AD e gli ambienti di archiviazione di Azure siano configurati correttamente. Si consiglia di esaminare i [prerequisiti](#prerequisites) per assicurarsi di aver completato tutti i passaggi necessari.

Successivamente, è possibile concedere l'accesso alle risorse di File di Azure con credenziali di Azure AD seguendo questa procedura: 

1. Abilitare l'autenticazione di Azure AD DS su SMB per l'account di archiviazione per registrare l'account di archiviazione con la distribuzione di Azure AD DS associata.
2. Assegnare le autorizzazioni di accesso per una condivisione a un'identità di Azure AD (utente, gruppo o entità servizio).
3. Configurare le autorizzazioni NTFS su SMB per file e directory.
4. Montare una condivisione file di Azure da una macchina virtuale aggiunta a dominio.

Il diagramma seguente illustra il flusso di lavoro end-to-end per l'abilitazione dell'autenticazione di Azure AD DS su SMB per File di Azure. 

![Diagramma che mostra il flusso di lavoro per l'autenticazione di Azure AD tramite SMB per File di Azure](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Prerequisiti

Prima di abilitare Azure AD tramite SMB per File di Azure, verificare che siano soddisfatti i prerequisiti seguenti:

1.  **Selezionare o creare un tenant di Azure AD.**

    È possibile usare un tenant nuovo o esistente per l'autenticazione di Azure AD tramite SMB. Il tenant e la condivisione file a cui si vuole accedere devono essere associati alla stessa sottoscrizione.

    Per creare un nuovo tenant di Azure AD, è possibile [aggiungere un tenant di Azure AD e una sottoscrizione di Azure AD](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Se è disponibile un tenant di Azure AD esistente, ma si vuole creare un nuovo tenant per l'uso con File di Azure, vedere [Creare un tenant di Azure Active Directory](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

2.  **Abilitare Azure AD Domain Services nel tenant di Azure AD.**

    Per supportare l'autenticazione con Azure AD credenziali, è necessario abilitare Azure AD DS per il tenant di Azure AD. Se non si è l'amministratore del tenant di Azure AD, contattare l'amministratore e seguire le istruzioni dettagliate per [abilitare Azure Active Directory Domain Services tramite il portale di Azure](../../active-directory-domain-services/create-instance.md).

    Il completamento di una distribuzione di Azure AD DS richiede in genere circa 15 minuti. Verificare che lo stato di integrità di Azure AD DS sia **in esecuzione**, con la sincronizzazione dell'hash delle password abilitata, prima di procedere al passaggio successivo.

3.  **Aggiungere un dominio a una macchina virtuale di Azure con Azure AD DS.**

    Per accedere a una condivisione file usando le credenziali Azure AD da una macchina virtuale, è necessario che la macchina virtuale sia aggiunta a un dominio per Azure AD DS. Per altre informazioni su come aggiungere una macchina virtuale a un dominio, vedere [Aggiungere una macchina virtuale Windows Server a un dominio gestito](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > L'autenticazione Azure AD DS su SMB con File di Azure è supportata solo in macchine virtuali di Azure in esecuzione in versioni del sistema operativo precedenti a Windows 7 o Windows Server 2008 R2.

4.  **Selezionare o creare una condivisione file di Azure.**

    Selezionare una condivisione file nuova o esistente associata alla stessa sottoscrizione del tenant di Azure AD. Per informazioni sulla creazione di una nuova condivisione file, vedere [Creare una condivisione file in File di Azure](storage-how-to-create-file-share.md). 
    Per ottenere prestazioni ottimali, è consigliabile che la condivisione file si trovi nella stessa area della macchina virtuale da cui si prevede di accedere alla condivisione.

5.  **Verificare la connettività di File di Azure tramite il montaggio di condivisioni file di Azure usando la chiave dell'account di archiviazione.**

    Per verificare che la macchina virtuale e la condivisione file siano configurate correttamente, provare a montare la condivisione file usando la chiave dell'account di archiviazione. Per altre informazioni, vedere [Montare una condivisione file di Azure e accedere alla condivisione in Windows](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Abilitare l'autenticazione di Azure AD DS per l'account

Per abilitare l'autenticazione Azure AD DS su SMB per File di Azure, è possibile impostare una proprietà per gli account di archiviazione creati dopo il 24 settembre 2018 usando l'interfaccia della riga di comando portale di Azure, Azure PowerShell o Azure. L'impostazione di questa proprietà registra l'account di archiviazione con la distribuzione di Azure AD DS associata. Azure AD autenticazione DS su SMB viene quindi abilitata per tutte le condivisioni file nuove ed esistenti nell'account di archiviazione.

Tenere presente che è possibile abilitare l'autenticazione di Azure AD DS su SMB solo dopo aver distribuito correttamente Azure AD DS nel tenant di Azure AD. Per ulteriori informazioni, vedere i [prerequisiti](#prerequisites).

### <a name="azure-portal"></a>Portale di Azure

Per abilitare l'autenticazione Azure AD DS su SMB con la [portale di Azure](https://portal.azure.com), attenersi alla seguente procedura:

1. Nel portale di Azure passare all'account di archiviazione esistente o [creare un account di archiviazione](../common/storage-quickstart-create-account.md).
2. Nella sezione **Impostazioni** selezionare **Configurazione**.
3. Selezionare **Azure Active Directory Domain Services (Azure AD DS)** dall'elenco **a discesa servizio directory basato su identità per autenticazione file di Azure** .

La figura seguente illustra come abilitare l'autenticazione di Azure AD DS su SMB per l'account di archiviazione.

![Abilitare l'autenticazione di Azure AD tramite SMB nel portale di Azure](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)
  
### <a name="powershell"></a>PowerShell  

Per abilitare l'autenticazione di Azure AD DS su SMB con Azure PowerShell, installare la versione più recente di AZ Module (2,4 o versione successiva) o il modulo AZ. Storage (1,5 o versione successiva). Per altre informazioni sull'installazione di PowerShell, vedere [installare Azure PowerShell in Windows con PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps).

Per creare un nuovo account di archiviazione, chiamare [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0), quindi impostare il parametro **EnableAzureActiveDirectoryDomainServicesForFile** su **true**. Nell'esempio seguente ricordare di sostituire i valori segnaposto con i propri valori. Se si usa il modulo di anteprima precedente, il parametro per l'abilitazione della funzionalità è **EnableAzureFilesAadIntegrationForSMB**.

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

Per abilitare questa funzionalità negli account di archiviazione esistenti, usare il comando seguente:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per abilitare l'autenticazione Azure AD su SMB con l'interfaccia della riga di comando di Azure, installare la versione più recente dell'interfaccia della riga di comando (versione 2.0.70 o Per altre informazioni sull'installazione dell'interfaccia della riga di comando di Azure, vedere [Install the Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Per creare un nuovo account di archiviazione, chiamare[AZ storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)e impostare la `--enable-files-aadds` proprietà su **true**. Nell'esempio seguente ricordare di sostituire i valori segnaposto con i propri valori. (Se si usa il modulo di anteprima precedente, il parametro per l'abilitazione della funzionalità è **file-AAD**).

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Per abilitare questa funzionalità negli account di archiviazione esistenti, usare il comando seguente:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```


## <a name="assign-access-permissions-to-an-identity"></a>Assegnare le autorizzazioni di accesso a un'identità

Per accedere alle risorse File di Azure con Azure AD credenziali, un'identità (un utente, un gruppo o un'entità servizio) deve disporre delle autorizzazioni necessarie a livello di condivisione. Questo processo è simile alla specifica delle autorizzazioni di condivisione di Windows, in cui è possibile specificare il tipo di accesso di un determinato utente a una condivisione file. Le istruzioni dettagliate fornite in questa sezione spiegano come assegnare a un'identità le autorizzazioni di lettura, scrittura o eliminazione per una condivisione file.

Sono stati introdotti due ruoli predefiniti di Azure per la concessione di autorizzazioni a livello di condivisione agli utenti:

- **Storage file data SMB Share Reader** consente l'accesso in lettura alle condivisioni file di archiviazione di Azure tramite SMB.
- Il **collaboratore condivisione SMB per i dati dei file di archiviazione** consente l'accesso in lettura, scrittura ed eliminazione nelle condivisioni file di archiviazione di Azure tramite SMB.
- **Storage file data SMB Share collaboratore con privilegi elevati** consente di leggere, scrivere, eliminare e modificare le autorizzazioni NTFS nelle condivisioni file di archiviazione di Azure tramite SMB.

> [!IMPORTANT]
> Per il controllo amministrativo completo di una condivisione file, inclusa la possibilità di assegnare un ruolo a un'identità, è necessario usare la chiave dell'account di archiviazione. Il controllo amministrativo non è supportato con le credenziali di Azure AD.

È possibile usare la portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure per assegnare i ruoli predefiniti all'identità Azure AD di un utente per concedere autorizzazioni a livello di condivisione.

#### <a name="azure-portal"></a>Portale di Azure
Per assegnare un ruolo controllo degli accessi in base al ruolo a un'identità Azure AD, usando il [portale di Azure](https://portal.azure.com), attenersi alla procedura seguente:

1. Nella portale di Azure passare alla condivisione file o [creare una condivisione file in file di Azure](storage-how-to-create-file-share.md).
2. Selezionare **Controllo di accesso (IAM)** .
3. Selezionare **Aggiungi un'assegnazione di ruolo**
4. Nel pannello **Aggiungi assegnazione ruolo** selezionare il ruolo predefinito appropriato (Storage file data SMB Share Reader, storage file data SMB condivisione Contributor) dall'elenco dei **ruoli** . Lasciare l'opzione **assegna accesso a** con l'impostazione predefinita: **Azure ad utente, gruppo o entità servizio**. Selezionare la destinazione Azure AD identità in base al nome o all'indirizzo di posta elettronica.
5. Selezionare **Salva** per completare l'operazione di assegnazione di ruolo.

#### <a name="powershell"></a>PowerShell

Il comando di PowerShell seguente illustra come assegnare un ruolo RBAC a un'identità di Azure AD, in base al nome di accesso. Per altre informazioni sull'assegnazione dei ruoli RBAC con PowerShell, vedere [Gestire l'accesso tramite il controllo degli accessi in base al ruolo e Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Prima di eseguire lo script di esempio seguente, ricordarsi di sostituire i valori segnaposto, comprese le parentesi, con valori personalizzati.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>Interfaccia della riga di comando
  
Il comando seguente dell'interfaccia della riga di comando 2,0 illustra come assegnare un ruolo RBAC a un'identità Azure AD, in base al nome di accesso. Per ulteriori informazioni sull'assegnazione di ruoli RBAC con l'interfaccia della riga di comando di Azure, vedere [Manage Access by using RBAC and Azure CLI](../../role-based-access-control/role-assignments-cli.md). 

Prima di eseguire lo script di esempio seguente, ricordarsi di sostituire i valori segnaposto, comprese le parentesi, con valori personalizzati.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>Configurare le autorizzazioni NTFS tramite SMB 
Dopo aver assegnato le autorizzazioni a livello di condivisione con RBAC, è necessario assegnare le autorizzazioni NTFS appropriate a livello di radice, directory o file. Si pensi a autorizzazioni a livello di condivisione come Gatekeeper di alto livello che determina se un utente può accedere alla condivisione. Mentre le autorizzazioni NTFS agiscono a un livello più granulare per determinare le operazioni che l'utente può eseguire a livello di directory o di file.

File di Azure supporta il set completo di autorizzazioni NTFS di base e avanzate. È possibile visualizzare e configurare le autorizzazioni NTFS per directory e file in una condivisione file di Azure montando la condivisione e quindi usando Esplora file di Windows o eseguendo il comando Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) o [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) . 

Per configurare NTFS con autorizzazioni superuser, è necessario montare la condivisione usando la chiave dell'account di archiviazione della macchina virtuale aggiunta al dominio. Seguire le istruzioni nella sezione successiva per montare una condivisione file di Azure dal prompt dei comandi e configurare di conseguenza le autorizzazioni NTFS.

I set di autorizzazioni seguenti sono supportati per la directory radice di una condivisione file:

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Montare una condivisione file dal prompt dei comandi

Usare il comando di Windows **net use** per montare la condivisione file di Azure. Ricordarsi di sostituire i valori segnaposto nell'esempio seguente con valori personalizzati. Per altre informazioni sul montaggio delle condivisioni file, vedere [Montare una condivisione file di Azure e accedere alla condivisione in Windows](storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Configurare le autorizzazioni NTFS con Esplora file di Windows
Utilizzare Esplora file di Windows per concedere l'autorizzazione completa a tutte le directory e i file nella condivisione file, inclusa la directory radice.

1. Aprire Esplora file di Windows e fare clic con il pulsante destro del mouse su file/directory e selezionare **Proprietà** .
2. Fare clic sulla scheda **sicurezza** .
3. Fare clic su **modifica..** . pulsante per modificare le autorizzazioni
4. È possibile modificare l'autorizzazione degli utenti esistenti oppure fare clic su **Aggiungi...** per concedere le autorizzazioni ai nuovi utenti
5. Nella finestra di messaggio di richiesta per l'aggiunta di nuovi utenti, immettere il nome utente di destinazione a cui si vuole concedere l'autorizzazione nella casella **immettere i nomi degli oggetti da selezionare** , quindi fare clic su **Controlla nomi** per trovare il nome UPN completo dell'utente di destinazione.
7.  Fare clic su **OK**
8.  Nella scheda sicurezza selezionare tutte le autorizzazioni che si desidera concedere al nuovo utente
9.  Fare clic su **Applica**

### <a name="configure-ntfs-permissions-with-icacls"></a>Configurare le autorizzazioni NTFS con icacls
Usare il comando seguente di Windows per concedere autorizzazioni complete a tutti i file e le sottodirectory nella condivisione file, inclusa la directory radice. Ricordarsi di sostituire i valori segnaposto nell'esempio con valori personalizzati.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Per ulteriori informazioni su come utilizzare icacls per impostare le autorizzazioni NTFS e sui diversi tipi di autorizzazioni supportate, vedere [la Guida di riferimento alla riga di comando per icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Montare una condivisione file da una macchina virtuale aggiunta a dominio

Il processo seguente verifica che le credenziali di Azure AD siano state configurate correttamente e che sia possibile accedere a una condivisione file di Azure da una macchina virtuale aggiunta a un dominio: 

Accedere alla macchina virtuale usando l'identità Azure AD a cui sono state concesse le autorizzazioni, come illustrato nella figura seguente.

![Screenshot che mostra la schermata di accesso ad Azure AD per l'autenticazione utente](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

Usare il comando seguente per montare la condivisione file di Azure. Ricordarsi di sostituire i valori segnaposto con valori personalizzati. Poiché è già stata eseguita l'autenticazione, non è necessario specificare la chiave dell'account di archiviazione o il nome utente e la password del Azure AD. Azure AD su SMB supporta un'esperienza Single Sign-on con le credenziali Azure AD.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

A questo punto è stata abilitata l'autenticazione Azure AD su SMB e è stato assegnato un ruolo personalizzato che fornisce l'accesso a una condivisione file di Azure con un'identità Azure AD. Per concedere ad altri utenti l'accesso alla condivisione file, seguire le istruzioni riportate nelle sezioni [assegnare le autorizzazioni di accesso a un'identità](#assign-access-permissions-to-an-identity) e [configurare le autorizzazioni NTFS su SMB](#configure-ntfs-permissions-over-smb) .

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su File di Azure e su come utilizzare Azure AD su SMB, vedere le risorse seguenti:

- [Introduzione a File di Azure](storage-files-introduction.md)
- [Panoramica dell'autenticazione Azure Active Directory su SMB per File di Azure](storage-files-active-directory-overview.md)
- [Domande frequenti](storage-files-faq.md)
