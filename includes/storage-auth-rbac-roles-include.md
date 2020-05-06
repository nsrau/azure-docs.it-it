---
title: includere il file
description: Includere file
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/17/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d73bab76860abf3e9fa442dad44e1ddb66d147a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80519554"
---
Azure fornisce i seguenti ruoli predefiniti di controllo degli accessi in base al ruolo per autorizzare l'accesso ai dati BLOB e di accodamento tramite Azure AD e OAuth:

- [Storage BLOB data Owner](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): usare per impostare la proprietà e gestire il controllo di accesso POSIX per Azure Data Lake storage Gen2. Per altre informazioni, vedere [Access control in Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md) (Controllo di accesso in Azure Data Lake Storage Gen2).
- [Collaboratore dati BLOB di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): usare per concedere le autorizzazioni di lettura/scrittura/eliminazione per le risorse di archiviazione BLOB.
- [Lettore dati BLOB di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): usare per concedere autorizzazioni di sola lettura alle risorse di archiviazione BLOB.
- [Collaboratore dati della coda di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): usare per concedere le autorizzazioni di lettura/scrittura/eliminazione per le code di Azure.
- [Lettore dati della coda di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): usare per concedere le autorizzazioni di sola lettura alle code di Azure.
- [Processore dei messaggi dati della coda di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): usare per concedere le autorizzazioni di lettura, recupero ed eliminazione ai messaggi nelle code di archiviazione di Azure.
- [Mittente del messaggio di dati della coda di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): usare per concedere le autorizzazioni Aggiungi ai messaggi nelle code di archiviazione di Azure.

Per informazioni dettagliate sui ruoli predefiniti del controllo degli accessi in base al ruolo per archiviazione di Azure per i servizi dati e per il servizio di gestione, vedere la sezione **archiviazione** in [ruoli predefiniti](../articles/role-based-access-control/built-in-roles.md#storage)di Azure per il controllo degli accessi in base al ruolo Per informazioni sui diversi tipi di ruoli che forniscono le autorizzazioni in Azure, vedere ruoli di [amministratore della sottoscrizione classica, ruoli RBAC di Azure e ruoli Azure ad](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!NOTE]
> Le assegnazioni di ruolo RBAC possono richiedere fino a cinque minuti per la propagazione.
>
> Solo i ruoli definiti in modo esplicito per l'accesso ai dati consentono a un'entità di sicurezza di accedere ai dati BLOB o Queue. I ruoli, ad esempio **proprietario**, **collaboratore**e **collaboratore account di archiviazione** , consentono a un'entità di sicurezza di gestire un account di archiviazione, ma non di fornire l'accesso ai dati del BLOB o della coda all'interno di tale account.
>
> L'accesso ai dati BLOB o della coda nel portale di Azure può essere autorizzato usando l'account Azure AD o la chiave di accesso dell'account di archiviazione. Per altre informazioni, vedere [usare la portale di Azure per accedere ai dati di BLOB o di Accodamento](../articles/storage/common/storage-access-blobs-queues-portal.md).
