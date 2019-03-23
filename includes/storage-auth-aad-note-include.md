---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 50217829be7aff3bcdbf417f19fff9f1fd7c6901
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372666"
---
> [!NOTE]
> - Le assegnazioni di ruolo RBAC possono richiedere fino a due minuti per propagare.
>
> Per autorizzare le operazioni di BLOB e accodamento con un token OAuth, è necessario usare HTTPS.
>
> - Il portale di Azure supporta ora l'uso delle credenziali di Azure AD per leggere e scrivere i dati di BLOB e code nella versione di anteprima. Per accedere a dati blob e coda nel portale di Azure, è necessario assegnare a un utente di Azure Resource Manager **lettore** ruolo, oltre al ruolo appropriato per l'accesso di accodamento o blob. Per altre informazioni, vedere [concedere l'accesso a contenitori di Azure e code con accessi nel portale di Azure](../articles/storage/common/storage-auth-aad-rbac.md). 
> 
> - [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) attualmente usa la chiave dell'account di archiviazione per accedere ai dati del BLOB e della coda. Se la chiave non è disponibile, autorizzazione di Azure AD viene utilizzato per l'accesso ai BLOB. Autorizzazione di Azure AD non è attualmente supportato per le code. 
>
> - File di Azure supporta l'autenticazione con Azure AD su SMB solo per le macchine virtuali aggiunte a un dominio (anteprima). Per informazioni sull'uso di Azure AD su SMB per File di Azure, vedere [Panoramica dell'autenticazione di Azure Active Directory tramite SMB per File di Azure (anteprima)](../articles/storage/files/storage-files-active-directory-overview.md).