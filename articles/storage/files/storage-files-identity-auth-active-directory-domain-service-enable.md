---
title: Usare Azure AD Domain Services per autorizzare l'accesso ai dati dei file tramite SMB
description: Informazioni su come abilitare l'autenticazione basata su identità su Server Message Block (SMB) per File di Azure tramite Azure Active Directory Domain Services. Le macchine virtuali Windows (VM) appartenenti a un dominio possono quindi accedere alle condivisioni file di Azure usando le credenziali Azure AD.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: fe2bbc1d6f42819354f48812a34371a49e5acbac
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "86999633"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Abilitare l'autenticazione Azure Active Directory Domain Services in File di Azure

[File di Azure](storage-files-introduction.md)   supporta l'autenticazione basata su identità su Server Message Block (SMB) tramite due tipi di servizi del dominio: Active Directory Domain Services locale (AD DS) e Azure Active Directory Domain Services (Azure AD DS). Si consiglia vivamente di esaminare la [sezione come funziona](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview#how-it-works) per selezionare il servizio del dominio appropriato per authentcation. Il programma di installazione è diverso a seconda del servizio del dominio scelto. Questo articolo illustra come abilitare e configurare Azure AD DS per l'autenticazione con le condivisioni file di Azure.

Se non si ha familiarità con le condivisioni file di Azure, è consigliabile leggere la [Guida alla pianificazione](storage-files-planning.md) prima di leggere la serie di articoli riportata di seguito.

> [!NOTE]
> File di Azure supporta l'autenticazione Kerberos con Azure AD DS con la crittografia RC4-HMAC. La crittografia Kerberos AES non è ancora supportata.
> File di Azure supporta l'autenticazione per Azure AD DS con sincronizzazione completa con Azure AD. Se è stata abilitata la sincronizzazione con ambito in Azure AD DS che sincronizzano solo un set limitato di identità da Azure AD, non è supportata l'autenticazione e l'autorizzazione.

## <a name="prerequisites"></a>Prerequisiti

Prima di abilitare Azure AD su SMB per le condivisioni file di Azure, assicurarsi di aver completato i prerequisiti seguenti:

1.  **Selezionare o creare un tenant di Azure AD.**

    È possibile usare un tenant nuovo o esistente per l'autenticazione di Azure AD tramite SMB. Il tenant e la condivisione file a cui si vuole accedere devono essere associati alla stessa sottoscrizione.

    Per creare un nuovo tenant di Azure AD, è possibile [aggiungere un tenant di Azure AD e una sottoscrizione di Azure AD](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Se si dispone di un tenant di Azure AD esistente ma si vuole creare un nuovo tenant da usare con le condivisioni file di Azure, vedere [creare un tenant di Azure Active Directory](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

1.  **Abilitare Azure AD Domain Services nel tenant di Azure AD.**

    Per supportare l'autenticazione con credenziali di Azure AD, è necessario abilitare Azure AD Domain Services per il tenant di Azure AD. Se non si è l'amministratore del tenant di Azure AD, contattare l'amministratore e seguire le istruzioni dettagliate per [Abilitare Azure Active Directory Domain Services tramite il portale di Azure](../../active-directory-domain-services/tutorial-create-instance.md).

    Il completamento di una distribuzione di Azure AD DS richiede in genere circa 15 minuti. Verificare che lo stato di integrità di Azure AD DS sia **in esecuzione**, con la sincronizzazione dell'hash delle password abilitata, prima di procedere al passaggio successivo.

1.  **Aggiungere un dominio a una macchina virtuale di Azure con Azure AD DS.**

    Per accedere a una condivisione file usando le credenziali Azure AD da una macchina virtuale, è necessario che la macchina virtuale sia aggiunta a un dominio per Azure AD DS. Per altre informazioni su come aggiungere una macchina virtuale a un dominio, vedere [Aggiungere una macchina virtuale Windows Server a un dominio gestito](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > Azure AD l'autenticazione DS su SMB con le condivisioni file di Azure è supportata solo nelle macchine virtuali di Azure in esecuzione in versioni del sistema operativo precedenti a Windows 7 o Windows Server 2008 R2.

1.  **Selezionare o creare una condivisione file di Azure.**

    Selezionare una condivisione file nuova o esistente associata alla stessa sottoscrizione del tenant di Azure AD. Per informazioni sulla creazione di una nuova condivisione file, vedere [Creare una condivisione file in File di Azure](storage-how-to-create-file-share.md).
    Per ottenere prestazioni ottimali, è consigliabile che la condivisione file si trovi nella stessa area della macchina virtuale da cui si prevede di accedere alla condivisione.

1.  **Verificare la connettività di File di Azure tramite il montaggio di condivisioni file di Azure usando la chiave dell'account di archiviazione.**

    Per verificare che la macchina virtuale e la condivisione file siano configurate correttamente, provare a montare la condivisione file usando la chiave dell'account di archiviazione. Per altre informazioni, vedere [Montare una condivisione file di Azure e accedere alla condivisione in Windows](storage-how-to-use-files-windows.md).

## <a name="regional-availability"></a>Disponibilità a livello di area

File di Azure autenticazione con Azure AD DS è disponibile in [tutte le aree pubbliche di Azure](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="overview-of-the-workflow"></a>Panoramica del flusso di lavoro

Prima di abilitare l'autenticazione di Azure AD DS su SMB per le condivisioni file di Azure, verificare che i Azure AD e gli ambienti di archiviazione di Azure siano configurati correttamente. Si consiglia di esaminare i [prerequisiti](#prerequisites) per assicurarsi di aver completato tutti i passaggi necessari.

Eseguire quindi le operazioni seguenti per concedere l'accesso alle risorse File di Azure con Azure AD credenziali:

1. Abilitare l'autenticazione di Azure AD DS su SMB per l'account di archiviazione per registrare l'account di archiviazione con la distribuzione di Azure AD DS associata.
2. Assegnare le autorizzazioni di accesso per una condivisione a un'identità di Azure AD (utente, gruppo o entità servizio).
3. Configurare le autorizzazioni NTFS su SMB per file e directory.
4. Montare una condivisione file di Azure da una macchina virtuale aggiunta a dominio.

Il diagramma seguente illustra il flusso di lavoro end-to-end per l'abilitazione dell'autenticazione di Azure AD DS su SMB per File di Azure.

![Diagramma che mostra il flusso di lavoro per l'autenticazione di Azure AD tramite SMB per File di Azure](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="1-enable-azure-ad-ds-authentication-for-your-account"></a>1. abilitare l'autenticazione Azure AD DS per l'account

Per abilitare l'autenticazione di Azure AD DS su SMB per File di Azure, è possibile impostare una proprietà negli account di archiviazione usando l'interfaccia della riga di comando portale di Azure, Azure PowerShell o Azure. Impostando questa proprietà in modo implicito "Domain joins", l'account di archiviazione con la distribuzione di Azure AD DS associata. Azure AD autenticazione DS su SMB viene quindi abilitata per tutte le condivisioni file nuove ed esistenti nell'account di archiviazione.

Tenere presente che è possibile abilitare l'autenticazione di Azure AD DS su SMB solo dopo aver distribuito correttamente Azure AD DS nel tenant di Azure AD. Per ulteriori informazioni, vedere i [prerequisiti](#prerequisites).

### <a name="azure-portal"></a>Portale di Azure

Per abilitare l'autenticazione Azure AD DS su SMB con la [portale di Azure](https://portal.azure.com), attenersi alla seguente procedura:

1. Nel portale di Azure passare all'account di archiviazione esistente o [creare un account di archiviazione](../common/storage-account-create.md).
1. Nella sezione **Impostazioni** selezionare **Configurazione**.
1. In **accesso basato su identità per le condivisioni file** impostare l'interruttore per **Azure Active Directory servizio del dominio (AAD DS)** su **abilitato**.
1. Selezionare **Salva**.

La figura seguente illustra come abilitare l'autenticazione di Azure AD DS su SMB per l'account di archiviazione.

![Abilitare l'autenticazione di Azure AD DS su SMB nella portale di Azure](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

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

Per creare un nuovo account di archiviazione, chiamare [AZ storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)e impostare la `--enable-files-aadds` proprietà su **true**. Nell'esempio seguente ricordare di sostituire i valori segnaposto con i propri valori. (Se si usa il modulo di anteprima precedente, il parametro per l'abilitazione della funzionalità è **file-AAD**).

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Per abilitare questa funzionalità negli account di archiviazione esistenti, usare il comando seguente:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

È stata abilitata l'autenticazione di Azure AD DS su SMB ed è stato assegnato un ruolo personalizzato che fornisce l'accesso a una condivisione file di Azure con un'identità Azure AD. Per concedere ad altri utenti l'accesso alla condivisione file, seguire le istruzioni riportate nelle sezioni [assegnare le autorizzazioni di accesso](#2-assign-access-permissions-to-an-identity) per l'uso di un'identità e configurare le [autorizzazioni NTFS su SMB](#3-configure-ntfs-permissions-over-smb).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su File di Azure e su come utilizzare Azure AD su SMB, vedere le risorse seguenti:

- [Panoramica del supporto dell'autenticazione basata su identità File di Azure per l'accesso SMB](storage-files-active-directory-overview.md)
- [Domande frequenti](storage-files-faq.md)
