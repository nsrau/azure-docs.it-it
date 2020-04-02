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
ms.openlocfilehash: d73bab76860abf3e9fa442dad44e1ddb66d147a3
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519554"
---
Azure provides the following built-in RBAC roles for authorizing access to blob and queue data using Azure AD and OAuth:

- [Proprietario dei dati BLOB di archiviazione:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner)usare per impostare la proprietà e gestire il controllo di accesso POSIX per Azure Data Lake Storage Gen2.Storage Blob Data Owner : Use to set ownership and manage POSIX access control for Azure Data Lake Storage Gen2. Per altre informazioni, vedere [Access control in Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md) (Controllo di accesso in Azure Data Lake Storage Gen2).
- [Collaboratore dati BLOB di archiviazione:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor)usare per concedere autorizzazioni di lettura/scrittura/eliminazione alle risorse di archiviazione BLOB.
- [Lettore di dati BLOB di archiviazione:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader)usare per concedere autorizzazioni di sola lettura alle risorse di archiviazione BLOB.
- [Collaboratore](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor)dati coda di archiviazione: usare per concedere autorizzazioni di lettura/scrittura/eliminazione alle code di Azure.Storage Queue Data Contributor : Use to grant read/write/delete permissions to Azure queues.
- [Lettore dati coda di](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader)archiviazione: usare per concedere autorizzazioni di sola lettura alle code di Azure.Storage Queue Data Reader : Use to grant read-only permissions to Azure queues.
- [Storage Queue Message Processor:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor)usare per concedere autorizzazioni di visualizzazione, recupero ed eliminazione ai messaggi nelle code di Archiviazione di Azure.Storage Queue Message Processor : Use to grant peek, retrieve, and delete permissions to messages in Azure Storage queues.
- [Storage Queue Data Sender](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Usare per concedere autorizzazioni di aggiunta ai messaggi nelle code di Archiviazione di Azure.Storage Queue Data Message Sender : Use to grant add permissions to messages in Azure Storage queues.

Per informazioni dettagliate sui ruoli RBAC incorporati per Archiviazione di Azure sia per i servizi dati che per il servizio di gestione, vedere la sezione Archiviazione in Ruoli predefiniti di Azure per Il controllo degli accessi in base al ruolo di Azure.For detailed information about built-in RBAC roles for Azure Storage for both the data services and the management service, see the **Storage** section in [Azure built-in roles for Azure RBAC.](../articles/role-based-access-control/built-in-roles.md#storage) Inoltre, per informazioni sui diversi tipi di ruoli che forniscono autorizzazioni in Azure, vedere Ruoli di amministratore della sottoscrizione classica, ruoli rbac di [Azure e ruoli di Azure AD.](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)

> [!NOTE]
> La propagazione delle assegnazioni di ruolo RBAC può richiedere fino a cinque minuti.
>
> Solo i ruoli definiti in modo esplicito per l'accesso ai dati consentono a un'entità di sicurezza di accedere ai dati BLOB o della coda. Ruoli quali **Proprietario**, **Collaboratore**e **Collaboratore account di archiviazione** consentono a un'entità di sicurezza di gestire un account di archiviazione, ma non forniscono l'accesso ai dati del BLOB o della coda all'interno di tale account.
>
> L'accesso ai dati BLOB o coda nel portale di Azure può essere autorizzato usando l'account Azure AD o la chiave di accesso dell'account di archiviazione. Per altre informazioni, vedere [Usare il portale di Azure per accedere ai dati BLOB o della coda.](../articles/storage/common/storage-access-blobs-queues-portal.md)
