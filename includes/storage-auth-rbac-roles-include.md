---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/17/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4b10955a1d3b85acbcae109836bebc03ec04c72c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76279604"
---
Azure provides the following built-in RBAC roles for authorizing access to blob and queue data using Azure AD and OAuth:

- [Proprietario dei dati BLOB di archiviazione:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner)usare per impostare la proprietà e gestire il controllo di accesso POSIX per Azure Data Lake Storage Gen2.Storage Blob Data Owner : Use to set ownership and manage POSIX access control for Azure Data Lake Storage Gen2. Per altre informazioni, vedere [Access control in Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md) (Controllo di accesso in Azure Data Lake Storage Gen2).
- [Collaboratore dati BLOB di archiviazione:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor)usare per concedere autorizzazioni di lettura/scrittura/eliminazione alle risorse di archiviazione BLOB.
- [Lettore di dati BLOB di archiviazione:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader)usare per concedere autorizzazioni di sola lettura alle risorse di archiviazione BLOB.
- [Collaboratore](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor)dati coda di archiviazione: usare per concedere autorizzazioni di lettura/scrittura/eliminazione alle code di Azure.Storage Queue Data Contributor : Use to grant read/write/delete permissions to Azure queues.
- [Lettore dati coda di](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader)archiviazione: usare per concedere autorizzazioni di sola lettura alle code di Azure.Storage Queue Data Reader : Use to grant read-only permissions to Azure queues.
- [Storage Queue Message Processor:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor)usare per concedere autorizzazioni di visualizzazione, recupero ed eliminazione ai messaggi nelle code di Archiviazione di Azure.Storage Queue Message Processor : Use to grant peek, retrieve, and delete permissions to messages in Azure Storage queues.
- [Storage Queue Data Sender](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Usare per concedere autorizzazioni di aggiunta ai messaggi nelle code di Archiviazione di Azure.Storage Queue Data Message Sender : Use to grant add permissions to messages in Azure Storage queues.

> [!NOTE]
> La propagazione delle assegnazioni di ruolo RBAC può richiedere fino a cinque minuti.
>
> Solo i ruoli definiti in modo esplicito per l'accesso ai dati consentono a un'entità di sicurezza di accedere ai dati BLOB o della coda. Ruoli quali **Proprietario**, **Collaboratore**e **Collaboratore account di archiviazione** consentono a un'entità di sicurezza di gestire un account di archiviazione, ma non forniscono l'accesso ai dati del BLOB o della coda all'interno di tale account.
>
> L'accesso ai dati BLOB o coda nel portale di Azure può essere autorizzato usando l'account Azure AD o la chiave di accesso dell'account di archiviazione. Per altre informazioni, vedere [Usare il portale di Azure per accedere ai dati BLOB o della coda.](../articles/storage/common/storage-access-blobs-queues-portal.md)
