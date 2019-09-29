---
title: Autorizzazione dell'accesso ad Archiviazione di Azure | Microsoft Docs
description: Informazioni sui diversi metodi per autorizzare l'accesso ad Archiviazione di Azure, tra cui Azure Active Directory, l'autenticazione con chiave condivisa o firme di accesso condiviso.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c20f699a2d2270d11935b0216b1655390ece211c
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671067"
---
# <a name="authorizing-access-to-azure-storage"></a>Autorizzazione dell'accesso ad Archiviazione di Azure

Ogni volta che si accede ai dati nell'account di archiviazione, il client effettua una richiesta tramite HTTP/HTTPS ad Archiviazione di Azure. Ogni richiesta a una risorsa sicura deve essere autorizzata, in modo che il servizio possa verificare che il client ha le autorizzazioni necessarie per accedere ai dati.

La tabella seguente descrive le opzioni offerte da archiviazione di Azure per autorizzare l'accesso alle risorse:

|  |Chiave condivisa (chiave dell'account di archiviazione)  |Firma di accesso condiviso  |Azure Active Directory (Azure AD)  |Accesso in lettura pubblico Anonimo  |
|---------|---------|---------|---------|---------|
|BLOB di Azure     |[Supportato](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Supportato](storage-sas-overview.md)         |[Supportato](storage-auth-aad.md)         |[Supportato](../blobs/storage-manage-access-to-resources.md)         |
|File di Azure (SMB)     |[Supportato](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |Non supportate         |[Supportato solo con servizi di dominio AAD](../files/storage-files-active-directory-overview.md)         |Non supportate         |
|File di Azure (REST)     |[Supportato](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Supportato](storage-sas-overview.md)         |Non supportate         |Non supportate         |
|Code di Azure     |[Supportato](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Supportato](storage-sas-overview.md)         |[Supportato](storage-auth-aad.md)         |Non supportate         |
|Tabelle di Azure     |[Supportato](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Supportato](storage-sas-overview.md)         |Non supportate         |Non supportate         |

Ogni opzione di autorizzazione viene descritta brevemente di seguito:

- **Integrazione di Azure Active Directory (Azure ad)** per i BLOB e le code. Azure AD fornisce controllo degli accessi in base al ruolo per un controllo granulare dell'accesso del client alle risorse in un account di archiviazione. Per altre informazioni sull'integrazione Azure AD per i BLOB e le code, vedere [autenticazione delle richieste ad archiviazione di Azure tramite Azure Active Directory](storage-auth-aad.md).

- **Integrazione di Azure ad Domain Services (DS) (anteprima)** per i file. File di Azure supporta l'autenticazione basata su identità su Server Message Block (SMB) tramite Azure AD DS. Questo fornisce il controllo degli accessi in base al ruolo per un controllo accurato sull'accesso di un client alle risorse in un account di archiviazione. Per altre informazioni sull'integrazione di Azure AD per i file che usano servizi di dominio, vedere [Panoramica del supporto per l'autenticazione di File di Azure Azure Active Directory Domain Service (AAD DS) per l'accesso SMB (anteprima)](../files/storage-files-active-directory-overview.md).

- **Autorizzazione con chiave condivisa** per BLOB, file, code e tabelle. Un client che usa una chiave condivisa passa con ogni richiesta un'intestazione che viene firmata usando la chiave di accesso dell'account di archiviazione. Per altre informazioni, vedere [Authorize with Shared Key](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/) (Autorizzazione con chiave condivisa).
- **Firme di accesso condiviso** per BLOB, file, code e tabelle. Le firme di accesso condiviso concedono accesso delegato limitato alle risorse in un account di archiviazione. L'aggiunta di vincoli per l'intervallo di tempo durante il quale la firma è valida o per le autorizzazioni concesse fornisce flessibilità di gestione dell'accesso. Per altre informazioni, vedere [Uso delle firme di accesso condiviso](storage-sas-overview.md).
- **Accesso in lettura pubblico anonimo** per contenitori e BLOB. L'autorizzazione non è necessaria. Per altre informazioni, vedere [Gestire l'accesso in lettura anonimo a contenitori e BLOB](../blobs/storage-manage-access-to-resources.md).  

Per impostazione predefinita, tutte le risorse in Archiviazione di Azure sono protette e sono disponibili solo per il proprietario dell'account. Anche se è possibile usare una delle strategie di autorizzazione descritte in precedenza per concedere ai client l'accesso alle risorse nell'account di archiviazione, Microsoft consiglia di usare Azure AD ogni volta che è possibile per garantire la massima sicurezza e semplicità d'uso. 
