---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/25/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: c2b409f0eefe5efa389432cbb007cc08e0c6ae1e
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71078257"
---
Azure fornisce i seguenti ruoli predefiniti di controllo degli accessi in base al ruolo per autorizzare l'accesso ai dati BLOB e di accodamento tramite Azure AD e OAuth:

- [Proprietario dati BLOB di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Usare per impostare la proprietà e gestire il controllo di accesso POSIX per Azure Data Lake Storage Gen2. Per altre informazioni, vedere [Access control in Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md) (Controllo di accesso in Azure Data Lake Storage Gen2).
- [Collaboratore dati BLOB di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Consente di concedere le autorizzazioni di lettura, scrittura ed eliminazione alle risorse di archiviazione BLOB.
- [Lettore dati BLOB di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Consente di concedere le autorizzazioni di sola lettura alle risorse di archiviazione BLOB.
- [Collaboratore dati coda di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Consente di concedere le autorizzazioni di lettura, scrittura ed eliminazione alle code di Azure.
- [Lettore dati della coda di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Consente di concedere le autorizzazioni di sola lettura alle code di Azure.
- [Processore dei messaggi dati della coda di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Usare per concedere le autorizzazioni di lettura, recupero ed eliminazione ai messaggi nelle code di archiviazione di Azure.
- [Mittente messaggio dati coda di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Usare per concedere le autorizzazioni Aggiungi ai messaggi nelle code di archiviazione di Azure.

> [!NOTE]
> Le assegnazioni di ruolo RBAC possono richiedere fino a cinque minuti per la propagazione.
>
> Solo i ruoli definiti in modo esplicito per l'accesso ai dati consentono a un'entità di sicurezza di accedere ai dati BLOB o Queue. I ruoli, ad esempio **proprietario**, **collaboratore**e **collaboratore account di archiviazione** , consentono a un'entità di sicurezza di gestire un account di archiviazione, ma non di fornire l'accesso ai dati del BLOB o della coda all'interno di tale account.
