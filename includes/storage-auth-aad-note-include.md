---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/09/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 0182df40a4e7815560a85e60fe9062ccd8001c18
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52978861"
---
> [!NOTE]
> - L'anteprima dell'autenticazione di Azure AD per i BLOB e le code è intesa solo per uso non di produzione. I contratti di servizio (SLA) di produzione non sono al momento disponibili. Se l'autenticazione di Azure AD non è ancora supportata per il proprio scenario, continuare a usare l'autorizzazione con chiave condivisa o i token di firma di accesso condiviso nelle applicazioni.
>
> - Durante l'anteprima, la propagazione delle assegnazioni dei ruoli Controllo degli accessi in base al ruolo può richiedere fino a cinque minuti.
>
> - Per autorizzare le operazioni di BLOB e accodamento con un token OAuth, è necessario usare HTTPS.
>
> - Il portale di Azure supporta ora l'uso delle credenziali di Azure AD per leggere e scrivere i dati di BLOB e code nella versione di anteprima.
> 
> - [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) attualmente usa la chiave dell'account di archiviazione per accedere ai dati del BLOB e della coda. L'accesso OAuth è supportato per i BLOB.
>
> - File di Azure supporta l'autenticazione con Azure AD su SMB solo per le macchine virtuali aggiunte a un dominio (anteprima). Per informazioni sull'uso di Azure AD su SMB per File di Azure, vedere [Panoramica dell'autenticazione di Azure Active Directory tramite SMB per File di Azure (anteprima)](../articles/storage/files/storage-files-active-directory-overview.md).