---
title: Usare Servizi di dominio Azure AD per autorizzare l'accesso ai dati dei file tramite SMBUse Azure AD Domain Services to authorize access to file data over SMB
description: Informazioni su come abilitare l'autenticazione basata sull'identità su SMB (Server Message Block) per file di Azure tramite Servizi di dominio Azure Active Directory.Learn how to enable identity-based authentication over Server Message Block (SMB) for Azure Files through Azure Active Directory Domain Services. Le macchine virtuali Windows (VM) aggiunte al dominio possono quindi accedere alle condivisioni file di Azure usando le credenziali di Azure AD.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cb173bcbf7cd163dca16c211d45018e0fe056edd
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666843"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Abilitare l'autenticazione di Servizi di dominio Azure Active Directory nei file di AzureEnable Azure Active Directory Domain Services authentication on Azure Files

[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Per una panoramica dell'autenticazione di Azure AD su SMB per condivisioni file di Azure, vedere [Panoramica dell'autenticazione di Azure Active Directory su SMB per file di Azure.](storage-files-active-directory-overview.md) Questo articolo è incentrato su come abilitare l'autenticazione con Servizi di dominio Azure Active Directory (Azure AD DS) in File di Azure.This article is focus on how to enable authentication with Azure Active Directory Domain Services (Azure AD DS) on Azure Files.

> [!NOTE]
> File di Azure supporta l'autenticazione Kerberos con Azure AD DS con crittografia RC4-HMAC. La crittografia Kerberos di AES non è ancora supportata.

## <a name="prerequisites"></a>Prerequisiti

Prima di abilitare Azure AD su SMB per le condivisioni file di Azure, assicurarsi di aver completato i prerequisiti seguenti:Before you enable Azure AD over SMB for Azure file shares, make sure you have completed the following prerequisites:

1.  **Selezionare o creare un tenant di Azure AD.**

    È possibile usare un tenant nuovo o esistente per l'autenticazione di Azure AD tramite SMB. Il tenant e la condivisione file a cui si vuole accedere devono essere associati alla stessa sottoscrizione.

    Per creare un nuovo tenant di Azure AD, è possibile [aggiungere un tenant di Azure AD e una sottoscrizione di Azure AD](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Se si dispone di un tenant di Azure AD esistente ma si vuole creare un nuovo tenant da usare con le condivisioni file di Azure, vedere Creare un tenant di [Azure Active Directory.](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant)

1.  **Abilitare Azure AD Domain Services nel tenant di Azure AD.**

    Per supportare l'autenticazione con credenziali di Azure AD, è necessario abilitare Azure AD Domain Services per il tenant di Azure AD. Se non si è l'amministratore del tenant di Azure AD, contattare l'amministratore e seguire le istruzioni dettagliate per [Abilitare Azure Active Directory Domain Services tramite il portale di Azure](../../active-directory-domain-services/tutorial-create-instance.md).

    Il completamento di una distribuzione di Servizi di dominio Active Directory di Azure richiede in genere circa 15 minuti. Verificare che lo stato di integrità di Servizi di dominio Active Directory di Azure sia **Visualizzato ,** con la sincronizzazione dell'hash delle password abilitata, prima di procedere al passaggio successivo.

1.  **Aggiunta di un dominio a una macchina virtuale di Azure con Azure AD DS.Domain-join an Azure VM with Azure AD DS.**

    To access a file share by using Azure AD credentials from a VM, your VM must be domain-joined to Azure AD DS. Per altre informazioni su come aggiungere una macchina virtuale a un dominio, vedere [Aggiungere una macchina virtuale Windows Server a un dominio gestito](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > L'autenticazione di Servizi di dominio Active Directory tramite SMB con condivisioni file di Azure è supportata solo nelle macchine virtuali di Azure in esecuzione su versioni del sistema operativo superiori a Windows 7 o Windows Server 2008 R2.

1.  **Selezionare o creare una condivisione file di Azure.**

    Selezionare una condivisione file nuova o esistente associata alla stessa sottoscrizione del tenant di Azure AD. Per informazioni sulla creazione di una nuova condivisione file, vedere [Creare una condivisione file in File di Azure](storage-how-to-create-file-share.md).
    Per ottenere prestazioni ottimali, è consigliabile che la condivisione file si trovi nella stessa area della macchina virtuale da cui si prevede di accedere alla condivisione.

1.  **Verificare la connettività di File di Azure tramite il montaggio di condivisioni file di Azure usando la chiave dell'account di archiviazione.**

    Per verificare che la macchina virtuale e la condivisione file siano configurate correttamente, provare a montare la condivisione file usando la chiave dell'account di archiviazione. Per altre informazioni, vedere [Montare una condivisione file di Azure e accedere alla condivisione in Windows](storage-how-to-use-files-windows.md).

## <a name="overview-of-the-workflow"></a>Panoramica del flusso di lavoro

Prima di abilitare l'autenticazione di Servizi di dominio Active Directory di Azure su SMB per le condivisioni file di Azure, verificare che gli ambienti Azure AD e Archiviazione di Azure siano configurati correttamente. È consigliabile esaminare i [prerequisiti](#prerequisites) per assicurarsi di aver completato tutti i passaggi necessari.

Eseguire quindi le operazioni seguenti per concedere l'accesso alle risorse File di Azure con credenziali di Azure AD:Next, do the following things to grant access to Azure Files resources with Azure AD credentials:

1. Abilitare l'autenticazione di Servizi di dominio Active Directory di Azure su SMB per l'account di archiviazione per registrare l'account di archiviazione con la distribuzione di Servizi di dominio Azure AD associata.
2. Assegnare le autorizzazioni di accesso per una condivisione a un'identità di Azure AD (utente, gruppo o entità servizio).
3. Configurare le autorizzazioni NTFS su SMB per file e directory.
4. Montare una condivisione file di Azure da una macchina virtuale aggiunta a dominio.

The following diagram illustrates the end-to-end workflow for enabling Azure AD DS authentication over SMB for Azure Files.

![Diagramma che mostra il flusso di lavoro per l'autenticazione di Azure AD tramite SMB per File di Azure](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="1-enable-azure-ad-ds-authentication-for-your-account"></a>1. Abilitare l'autenticazione di Servizi di dominio Active Directory di Azure per l'account

Per abilitare l'autenticazione di Servizi di dominio Active Directory di Azure su SMB per file di Azure, è possibile impostare una proprietà negli account di archiviazione usando il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure.To enable Azure AD DS authentication over SMB for Azure Files, you can set a property on storage accounts by using the Azure portal, Azure PowerShell, or Azure CLI. L'impostazione implicita di questa proprietà "aggiunta di domini" all'account di archiviazione con la distribuzione di Servizi di dominio Azure AD associata. L'autenticazione di Servizi di dominio Active Directory di Azure su SMB viene quindi abilitata per tutte le condivisioni file nuove ed esistenti nell'account di archiviazione.

Tenere presente che è possibile abilitare l'autenticazione di Azure AD DS su SMB solo dopo aver distribuito correttamente Azure AD DS nel tenant di Azure AD. Per ulteriori informazioni, vedere i [prerequisiti](#prerequisites).

### <a name="azure-portal"></a>Portale di Azure

Per abilitare l'autenticazione di Servizi di dominio Active Directory su SMB con il portale di [Azure,](https://portal.azure.com)eseguire la procedura seguente:To enable Azure AD DS authentication over SMB with the Azure portal , follow these steps:

1. Nel portale di Azure passare all'account di archiviazione esistente o [creare un account di archiviazione.](../common/storage-account-create.md)
1. Nella sezione **Impostazioni** selezionare **Configurazione**.
1. In **Accesso basato sull'identità per le condivisioni file** passare l'interruttore per Servizio di dominio Azure Active Directory **(AAD DS)** su **Abilitato**.
1. Selezionare **Salva**.

L'immagine seguente mostra come abilitare l'autenticazione di Servizi di dominio Active Directory di Azure su SMB per l'account di archiviazione.

![Abilitare l'autenticazione di Servizi di dominio Active Directory di Azure tramite SMB nel portale di AzureEnable Azure AD DS authentication over SMB in the Azure portal](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

### <a name="powershell"></a>PowerShell  

Per abilitare l'autenticazione di Servizi di dominio Azure su SMB con Azure PowerShell, installare il modulo Az più recente (2.4 o versione più recente) o il modulo Az.Storage (1.5 o versione più recente). Per altre informazioni sull'installazione di PowerShell, vedere [Installare Azure PowerShell in Windows con PowerShellGet.](https://docs.microsoft.com/powershell/azure/install-Az-ps)

Per creare un nuovo account di archiviazione, chiamare [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0), quindi impostare il parametro **EnableAzureActiveDirectoryDomainServicesForFile** su **true**. Nell'esempio seguente ricordarsi di sostituire i valori segnaposto con valori personalizzati. Se si usa il modulo di anteprima precedente, il parametro per l'abilitazione delle funzionalità è **EnableAzureFilesAadIntegrationForSMB.**

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

Per abilitare questa funzionalità negli account di archiviazione esistenti, usare il comando seguente:To enable this feature on existing storage accounts, use the following command:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per abilitare l'autenticazione di Azure AD tramite SMB con l'interfaccia della riga di comando di Azure, installare la versione dell'interfaccia della riga di comando più recente (versione 2.0.70 o successiva). Per altre informazioni sull'installazione dell'interfaccia della riga di comando di Azure, vedere [Installare l'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)di Azure.For more information about installing Azure CLI, see Install the Azure CLI .

Per creare un nuovo account di archiviazione, chiamare `--enable-files-aadds` az storage account[create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)e impostare la proprietà su **true**. Nell'esempio seguente ricordarsi di sostituire i valori segnaposto con valori personalizzati. Se si utilizzava il modulo di anteprima precedente, il parametro per l'abilitazione delle funzioni è **file-aad.**

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Per abilitare questa funzionalità negli account di archiviazione esistenti, usare il comando seguente:To enable this feature on existing storage accounts, use the following command:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

L'autenticazione di Servizi di dominio Active Directory di Azure è stata abilitata su SMB e ha assegnato un ruolo personalizzato che fornisce l'accesso a una condivisione file di Azure con un'identità di Azure AD. Per concedere ad altri utenti l'accesso alla condivisione file, seguire le istruzioni nelle sezioni [Assegnare le autorizzazioni](#2-assign-access-permissions-to-an-identity) di accesso per utilizzare un'identità e [Configurare le autorizzazioni NTFS su SMB](#3-configure-ntfs-permissions-over-smb).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui file di Azure e su come usare Azure AD su SMB, vedere queste risorse:For more information about Azure Files and how to use Azure AD over SMB, see these resources:

- [Panoramica del supporto dell'autenticazione basata sull'identità di File di Azure per l'accesso SMBOverview of Azure Files identity-based authentication support for SMB access](storage-files-active-directory-overview.md)
- [DOMANDE FREQUENTI](storage-files-faq.md)
