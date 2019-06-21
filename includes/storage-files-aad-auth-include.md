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
ms.openlocfilehash: ff2ed5abbf2ce67a0b96a5da450b83832403db1f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269345"
---
[File di Azure](../articles/storage/files/storage-files-introduction.md) supporta l'autenticazione basata su identità tramite SMB (Server Message Block) (anteprima) per File di Azure con [Azure Active Directory (Azure AD) Domain Services.](../articles/active-directory-domain-services/overview.md) Le macchine virtuali di Windows aggiunte a un dominio possono così accedere a condivisioni file di Azure tramite credenziali di [Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md). 

È possibile gestire l'accesso a livello di condivisione file di Azure a un'identità, ad esempio un utente, gruppo in Azure AD con [controllo di accesso basato sui ruoli (RBAC)](../articles/role-based-access-control/overview.md). È possibile definire ruoli personalizzati di controllo degli accessi in base al ruolo che comprendono set comuni di autorizzazioni usate per accedere a File di Azure. Quando si assegna il ruolo personalizzato di controllo degli accessi in base al ruolo a un'identità Azure AD, tale identità può accedere a una condivisione file di Azure in base a tali autorizzazioni.

Nell'ambito dell'anteprima, File di Azure supporta anche la conservazione, l'ereditarietà e l'applicazione di [DACL NTFS](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) in tutti i file e in tutte le directory di una condivisione file. Se si copiano i dati da una condivisione file a File di Azure o viceversa, è possibile specificare che i DACL NTFS debbano essere mantenuti. In questo modo è possibile implementare scenari di backup usando File di Azure, preservando i DACL NTFS tra la condivisione file locale e la condivisione file nel cloud. 

> [!NOTE]
> - Autenticazione di servizio del dominio AD Azure per l'accesso SMB non è supportata per le macchine virtuali Linux. Sono supportate solo VM Windows.
> - Autenticazione di servizio del dominio AD Azure per l'accesso SMB non è supportato per computer appartenente al dominio di Active Directory. Nel frattempo, è possibile prendere in considerazione di sfruttare [Azure file Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) per avviare la migrazione dei dati in file di Azure e continuare per imporre il controllo di accesso usando le credenziali di Active Directory da di on-premises computer appartenenti al dominio di Active Directory. 
> - Autenticazione di servizio del dominio AD Azure per l'accesso SMB è disponibile solo per gli account di archiviazione creati dopo il 24 settembre 2018.
> - Autenticazione di Azure AD Domain Services per l'accesso SMB e NTFS DACL permanente non è supportata nelle condivisioni di file di Azure gestite dal servizio di sincronizzazione File di Azure. 
