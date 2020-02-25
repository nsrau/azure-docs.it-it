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
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565087"
---
[File di Azure](../articles/storage/files/storage-files-introduction.md) supporta l'autenticazione basata su identità su Server Message Block (SMB) tramite [Active Directory (ad)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (anteprima) e [Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md) . Questo articolo è incentrato sul modo in cui File di Azure può sfruttare i servizi di dominio, in locale o in Azure, per supportare l'accesso basato sull'identità ai File di Azure tramite SMB. In questo modo è possibile sostituire facilmente i file server esistenti con File di Azure e continuare a usare il servizio directory esistente, mantenendo l'accesso facile agli utenti alle condivisioni. 

File di Azure impone l'autorizzazione per l'accesso utente alla condivisione e al livello di directory/file. L'assegnazione delle autorizzazioni a livello di condivisione può essere assegnata a Azure AD utenti o gruppi gestiti tramite il modello di [controllo degli accessi in base al ruolo (RBAC)](../articles/role-based-access-control/overview.md) tipico. Con il controllo degli accessi in base al ruolo, le credenziali usate per l'accesso ai file devono essere disponibili o sincronizzate con Azure AD. Per concedere l'accesso in lettura a una condivisione file di Azure, è possibile assegnare ruoli di controllo Azure AD degli accessi in base al ruolo predefiniti come il lettore condivisione SMB per i dati dei file di archiviazione.

A livello di directory/file, File di Azure supporta la conservazione, l'ereditarietà e l'applicazione degli [elenchi DACL di Windows](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) Analogamente a qualsiasi file server Windows. Se si copiano i dati tramite SMB da una condivisione file a File di Azure o viceversa, è possibile scegliere di mantenendo gli elenchi DACL di Windows. Se si prevede di applicare o meno l'autorizzazione, è possibile sfruttare File di Azure per eseguire il backup degli ACL insieme ai dati. 
