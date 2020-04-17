---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/30/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 167d50e5c7f3049f46fd8540630e47044240809f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536583"
---
[File di Azure](../articles/storage/files/storage-files-introduction.md) supporta l'autenticazione basata sull'identità tramite SMB (Server Message Block) tramite Servizi di dominio Active Directory (anteprima) [locale](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) e Servizi di dominio Azure Active [Directory (Azure AD DS).](../articles/active-directory-domain-services/overview.md) Questo articolo è incentrato su come le condivisioni file di Azure possono usare i servizi di dominio, in locale o in Azure, per supportare l'accesso basato sull'identità alle condivisioni file di Azure tramite SMB. L'abilitazione dell'accesso basato sull'identità per le condivisioni file di Azure consente di sostituire i file server esistenti con le condivisioni file di Azure senza sostituire il servizio directory esistente, mantenendo l'accesso utente trasparente alle condivisioni. 

File di Azure impone l'autorizzazione all'accesso utente sia a livello di condivisione che a livello di directory/file. L'assegnazione delle autorizzazioni a livello di condivisione può essere eseguita su utenti o gruppi di Azure Active Directory (Azure AD) gestiti tramite il modello di controllo degli [accessi in base](../articles/role-based-access-control/overview.md) al ruolo. Con il controllo degli accessi in base al ruolo, le credenziali usate per l'accesso ai file devono essere disponibili o sincronizzate con Azure AD. È possibile assegnare ruoli RBAC predefiniti, ad esempio Lettore condivisione SMB di archiviazione dei dati dei file, a utenti o gruppi in Azure AD per concedere l'accesso in lettura a una condivisione file di Azure.You can assign built-in RBAC roles like Storage File Data SMB Share Reader to users or groups in Azure AD to grant read access to an Azure file share.

A livello di directory/file, File di Azure supporta la conservazione, l'ereditarietà e l'applicazione di [DACL di Windows](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) come qualsiasi file server Windows. È possibile scegliere di mantenere gli elenco DI autorizzazioni di Windows durante la copia dei dati su SMB tra la condivisione file esistente e le condivisioni file di Azure.You can choose to keep Windows DACLs when copying over SMB between your existing file share and your Azure file shares. Indipendentemente dal fatto che si intenda applicare o meno l'autorizzazione, è possibile usare le condivisioni file di Azure per eseguire il backup degli ACL insieme ai dati. 
