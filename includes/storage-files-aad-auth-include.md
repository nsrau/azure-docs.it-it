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
ms.openlocfilehash: 84fa97ec964d490eb9571c7e030704562a4a81d0
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95555108"
---
[File di Azure](../articles/storage/files/storage-files-introduction.md) supporta l'autenticazione basata su identità su Server Message Block (SMB) tramite [Active Directory Domain Services locale (ad DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) e [Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md). Questo articolo è incentrato sul modo in cui le condivisioni file di Azure possono usare i servizi di dominio, in locale o in Azure, per supportare l'accesso basato sull'identità alle condivisioni file di Azure tramite SMB. L'abilitazione dell'accesso basato su identità per le condivisioni file di Azure consente di sostituire i file server esistenti con le condivisioni file di Azure senza sostituire il servizio directory esistente, mantenendo l'accesso utente facile alle condivisioni. 

File di Azure impone l'autorizzazione per l'accesso utente ai livelli condivisione e directory/file. L'assegnazione delle autorizzazioni a livello di condivisione può essere eseguita su utenti o gruppi di Azure Active Directory (Azure AD) gestiti tramite il modello di [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../articles/role-based-access-control/overview.md) . Con il controllo degli accessi in base al ruolo, le credenziali usate per l'accesso ai file devono essere disponibili o sincronizzate con Azure AD. Per concedere l'accesso in lettura a una condivisione file di Azure, è possibile assegnare ruoli predefiniti di Azure, come il lettore di condivisione SMB per i dati dei file di archiviazione, a utenti o gruppi in Azure AD.

A livello di directory/file, File di Azure supporta la conservazione, l'ereditarietà e l'applicazione degli [elenchi DACL di Windows](/windows/win32/secauthz/access-control-lists) Analogamente a qualsiasi file server Windows. È possibile scegliere di evitare gli elenchi DACL di Windows per la copia di dati su SMB tra la condivisione file esistente e le condivisioni file di Azure. Se si prevede di applicare o meno l'autorizzazione, è possibile usare le condivisioni file di Azure per eseguire il backup degli ACL insieme ai dati.