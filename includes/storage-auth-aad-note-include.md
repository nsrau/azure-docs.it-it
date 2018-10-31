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
ms.openlocfilehash: 2d641287bcf095f13719667a91b7f61295309b5d
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430358"
---
> [!NOTE]
> - L'anteprima dell'autenticazione di Azure AD per i BLOB e le code è intesa solo per uso non di produzione. I contratti di servizio (SLA) di produzione non sono al momento disponibili. Se l'autenticazione di Azure AD non è ancora supportata per il proprio scenario, continuare a usare l'autorizzazione con chiave condivisa o i token di firma di accesso condiviso nelle applicazioni.
>
> - Durante l'anteprima, la propagazione delle assegnazioni dei ruoli Controllo degli accessi in base al ruolo può richiedere fino a cinque minuti.
>
> - È necessario usare HTTPS per l'autenticazione tramite Azure AD quando si chiamano le operazioni del BLOB e delle code.
>
> - Il portale di Azure supporta ora l'uso delle credenziali di Azure AD per leggere e scrivere i dati del BLOB nella versione di anteprima.
> 
> - Attualmente il portale di Azure non supporta l'uso delle credenziali di Azure AD per leggere e scrivere i dati della coda. I dati della coda sono accessibile tramite le chiavi dell'account di archiviazione.
>
> - [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) attualmente usa la chiave dell'account di archiviazione per accedere ai dati del BLOB e della coda.
>
> - File di Azure supporta l'autenticazione con Azure AD su SMB solo per le macchine virtuali aggiunte a un dominio (anteprima). Per informazioni sull'uso di Azure AD su SMB per File di Azure, vedere [Panoramica dell'autenticazione di Azure Active Directory tramite SMB per File di Azure (anteprima)](../articles/storage/files/storage-files-active-directory-overview.md).



