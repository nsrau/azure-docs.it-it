---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d1b10b55481b41f42c6c872522d3dd4dd4be0e77
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570584"
---
[File di Azure](../articles/storage/files/storage-files-introduction.md) supporta l'autenticazione basata su identità su Server Message Block (SMB) (anteprima) tramite [Azure Active Directory Domain Services (ad DS)](../articles/active-directory-domain-services/overview.md). Le macchine virtuali (VM) Windows appartenenti a un dominio possono accedere alle condivisioni file di Azure usando le credenziali [Azure Active Directory (Azure ad)](../articles/active-directory/fundamentals/active-directory-whatis.md) .

È possibile gestire File di Azure l'accesso a livello di condivisione a un'identità, ad esempio un utente o un gruppo in Azure AD tramite il controllo degli accessi in [base al ruolo (RBAC)](../articles/role-based-access-control/overview.md). È possibile definire ruoli RBAC personalizzati che includono set comuni di autorizzazioni utilizzate per accedere File di Azure. Quando si assegna il ruolo personalizzato di controllo degli accessi in base al ruolo a un'identità Azure AD, tale identità può accedere a una condivisione file di Azure in base a tali autorizzazioni.

Nell'ambito dell'anteprima, File di Azure supporta anche la conservazione, l'ereditarietà e l'applicazione di [DACL NTFS](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) in tutti i file e in tutte le directory di una condivisione file. Se si copiano i dati da una condivisione file a File di Azure o viceversa, è possibile specificare che i DACL NTFS debbano essere mantenuti. In questo modo è possibile implementare scenari di backup usando File di Azure, mantenendo gli elenchi DACL NTFS tra la condivisione file locale e la condivisione file cloud. 

> [!NOTE]
> - L'autenticazione Azure AD DS per l'accesso Server Message Block (SMB) non è supportata per le macchine virtuali Linux. Sono supportate solo VM Windows.
> - L'autenticazione Azure AD DS per l'accesso SMB non è supportata per i computer Active Directory aggiunti a un dominio. Nel frattempo, prendere in considerazione l'uso di [sincronizzazione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) per avviare la migrazione dei dati file di Azure e per continuare ad applicare il controllo di accesso usando le credenziali di Active Directory dei computer locali Active Directory aggiunti a un dominio. 
> - Azure AD autenticazione DS per l'accesso SMB è disponibile solo per gli account di archiviazione creati dopo il 24 settembre 2018.
> - Azure AD autenticazione DS per l'accesso SMB e la persistenza DACL NTFS non è supportata nelle condivisioni file di Azure gestite da Sincronizzazione file di Azure.
