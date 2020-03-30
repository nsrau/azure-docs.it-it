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
ms.openlocfilehash: b248bbb526baf355faf2564358884fd83422b037
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565087"
---
[File di Azure](../articles/storage/files/storage-files-introduction.md) supporta l'autenticazione basata sull'identità tramite SMB (Server Message Block) tramite [Active Directory (anteprima)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) e Servizi di dominio [Azure Active Directory (Azure AD DS)](../articles/active-directory-domain-services/overview.md) (GA). Questo articolo è incentrato su come File di Azure può sfruttare i servizi di dominio, in locale o in Azure, per supportare l'accesso basato sull'identità ai file di Azure tramite SMB. In questo modo è possibile sostituire facilmente i file server esistenti con File di Azure e continuare a usare il servizio directory esistente, mantenendo l'accesso utente trasparente alle condivisioni. 

File di Azure impone l'autorizzazione all'accesso utente sia alla condivisione che al livello di directory/file. L'assegnazione delle autorizzazioni a livello di condivisione può essere assegnata a utenti o gruppi di Azure AD gestiti tramite il modello tipico di controllo degli [accessi in base](../articles/role-based-access-control/overview.md) al ruolo. Con il controllo degli accessi in base al ruolo, le credenziali usate per l'accesso ai file devono essere disponibili o sincronizzate con Azure AD. È possibile assegnare ruoli RBAC predefiniti, ad esempio Lettore condivisione SMB di archiviazione dei dati dei file, a utenti o gruppi in Azure AD per concedere l'accesso in lettura a una condivisione file di Azure.You can assign built-in RBAC roles like Storage File Data SMB Share Reader to users or groups in Azure AD to grant read access to an Azure file share.

A livello di directory/file, File di Azure supporta la conservazione, l'ereditarietà e l'applicazione di [DACL di Windows](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) come qualsiasi file server Windows. Se si copiano dati su SMB da una condivisione file in File di Azure o viceversa, è possibile scegliere di mantenere gli elenco DACL di Windows.If you copy data over SMB from a file share to Azure Files, or viceversa, you can choose to keep Windows DACLs. Indipendentemente dal fatto che si intenda applicare o meno l'autorizzazione, è possibile sfruttare File di Azure per eseguire il backup degli ACL insieme ai dati. 
