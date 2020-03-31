---
title: Autorizzazione di operazioni sui dati
titleSuffix: Azure Storage
description: Informazioni sui diversi modi per autorizzare l'accesso ad Archiviazione di Azure, tra cui Azure Active Directory, autorizzazione con chiave condivisa o firme di accesso condiviso.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b0f2ad7566d0204871a9c6441315d6201662d92b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616298"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Autorizzazione dell'accesso ai dati in Archiviazione di AzureAuthorizing access to data in Azure Storage

Ogni volta che si accede ai dati nell'account di archiviazione, il client effettua una richiesta tramite HTTP/HTTPS ad Archiviazione di Azure. Ogni richiesta a una risorsa sicura deve essere autorizzata, in modo che il servizio possa verificare che il client ha le autorizzazioni necessarie per accedere ai dati.

Nella tabella seguente vengono descritte le opzioni offerte da Archiviazione di Azure per autorizzare l'accesso alle risorse:The following table describes the options that Azure Storage offers for authorizing access to resources:

|  |Chiave condivisa (chiave dell'account di archiviazione)Shared Key (storage account key)  |Firma di accesso condiviso  |Azure Active Directory (Azure AD)  |Active Directory (anteprima) |Accesso in lettura pubblico anonimo  |
|---------|---------|---------|---------|---------|---------|
|BLOB di Azure     |[Supportato](/rest/api/storageservices/authorize-with-shared-key/)         |[Supportato](storage-sas-overview.md)         |[Supportato](storage-auth-aad.md)         |Non supportate|[Supportato](../blobs/storage-manage-access-to-resources.md)         |
|File di Azure (SMB)Azure Files (SMB)     |[Supportato](/rest/api/storageservices/authorize-with-shared-key/)         |Non supportate         |[Supportato, solo con Servizi di dominio AAD](../files/storage-files-active-directory-overview.md)         |[Supportato, le credenziali devono essere sincronizzate con Azure ADSupported, credentials must be synced to Azure AD](../files/storage-files-active-directory-overview.md)|Non supportate         |
|File di Azure (REST)Azure Files (REST)     |[Supportato](/rest/api/storageservices/authorize-with-shared-key/)         |[Supportato](storage-sas-overview.md)         |Non supportate         |Non supportate |Non supportate         |
|Code di Azure     |[Supportato](/rest/api/storageservices/authorize-with-shared-key/)         |[Supportato](storage-sas-overview.md)         |[Supportato](storage-auth-aad.md)         |Non supportato | Non supportate         |
|Tabelle di Azure     |[Supportato](/rest/api/storageservices/authorize-with-shared-key/)         |[Supportato](storage-sas-overview.md)         |Non supportate         |Non supportate| Non supportate         |

Ogni opzione di autorizzazione è brevemente descritta di seguito:Each authorization option is briefly described below:

- **Integrazione di Azure Active Directory (Azure AD)** per BLOB e code. Azure AD fornisce controllo degli accessi in base al ruolo per un controllo granulare dell'accesso del client alle risorse in un account di archiviazione. Per altre informazioni sull'integrazione di Azure AD per BLOB e code, vedere [Autorizzare l'accesso a BLOB e code](storage-auth-aad.md)di Azure con Azure Active Directory.For more information regarding Azure AD integration for blobs and queues, see Authorize access to Azure blobs and queues using Azure Active Directory.

- **Autenticazione di Servizi di dominio Azure Active Directory (Azure AD DS)** per File di Azure.Azure Active Directory Domain Services (Azure AD DS) authentication for Azure DS. File di Azure supporta l'autorizzazione basata sull'identità tramite SMB (Server Message Block) tramite Servizi di dominio Active Directory. È possibile usare il controllo degli accessi in base al ruolo per un controllo granulare sull'accesso di un client alle risorse File di Azure in un account di archiviazione. Per altre informazioni sull'autenticazione dei file di Azure tramite i servizi di dominio, vedere [Panoramica.](../files/storage-files-active-directory-overview.md)

- **Autenticazione di Active Directory (AD) (anteprima)** per i file di Azure.Active Directory (AD) authentication (preview) for Azure Files. Azure Files supports identity-based authorization over SMB through AD. Il servizio di dominio Active Directory può essere ospitato in computer locali o in macchine virtuali di Azure.Your AD domain service can be hosted on on-premises machines or in Azure VMs. SMB access to Files is supported using AD credentials from domain joined machines, either on-premises or in Azure. È possibile utilizzare il controllo degli accessi in base al ruolo per il controllo dell'accesso a livello di condivisione e gli elenchi DACL NTFS per l'imposizione delle autorizzazioni a livello di directory/file. Per altre informazioni sull'autenticazione dei file di Azure tramite i servizi di dominio, vedere [Panoramica.](../files/storage-files-active-directory-overview.md)

- **Autorizzazione con chiave condivisa** per BLOB, file, code e tabelle. Un client che usa una chiave condivisa passa con ogni richiesta un'intestazione che viene firmata usando la chiave di accesso dell'account di archiviazione. Per altre informazioni, vedere [Authorize with Shared Key](/rest/api/storageservices/authorize-with-shared-key/) (Autorizzazione con chiave condivisa).
- **Firme di accesso condiviso** per BLOB, file, code e tabelle. Le firme di accesso condiviso concedono accesso delegato limitato alle risorse in un account di archiviazione. L'aggiunta di vincoli per l'intervallo di tempo durante il quale la firma è valida o per le autorizzazioni concesse fornisce flessibilità di gestione dell'accesso. Per altre informazioni, vedere [Uso delle firme di accesso condiviso](storage-sas-overview.md).
- **Accesso in lettura pubblico anonimo** per contenitori e BLOB. L'autorizzazione non è necessaria. Per altre informazioni, vedere [Gestire l'accesso in lettura anonimo a contenitori e BLOB](../blobs/storage-manage-access-to-resources.md).  

Per impostazione predefinita, tutte le risorse in Archiviazione di Azure sono protette e sono disponibili solo per il proprietario dell'account. Anche se è possibile usare una delle strategie di autorizzazione descritte in precedenza per concedere ai client l'accesso alle risorse nell'account di archiviazione, Microsoft consiglia di usare Azure AD ogni volta che è possibile per garantire la massima sicurezza e semplicità d'uso.

## <a name="next-steps"></a>Passaggi successivi

- [Autorizzare l'accesso ai BLOB e alle code di Azure usando Azure Active DirectoryAuthorize access to Azure blobs and queues using Azure Active Directory](storage-auth-aad.md)
- [Autorizzare con la chiave condivisa](/rest/api/storageservices/authorize-with-shared-key/)
- [Concedere l'accesso limitato alle risorse di Archiviazione di Azure usando le firme di accesso condivisoGrant limited access to Azure Storage resources using shared access signatures (SAS)](storage-sas-overview.md)
