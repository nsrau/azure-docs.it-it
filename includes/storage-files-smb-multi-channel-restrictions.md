---
title: includere file
description: includere file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/16/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e2a1eb8524c9d9a4b0ae603da3c05fbb20900111
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995482"
---
SMB multicanale per le condivisioni file di Azure presenta attualmente le restrizioni seguenti:
- Può essere utilizzato solo con account di archiviazione con ridondanza locale.
- Supportato solo per i client Windows. 
- Il numero massimo di canali è quattro.
- SMB diretto non è supportato.
- Per gli account di archiviazione con autenticazione Active Directory Domain Services locale (AD DS) o Azure AD DS [basata sull'identità](../articles/storage/files/storage-files-active-directory-overview.md) abilitata per file di Azure, i client SMB non saranno in grado di utilizzare Esplora file di Windows per configurare le autorizzazioni NTFS per directory e file.
    - Usare invece lo strumento [icacls](/windows-server/administration/windows-commands/icacls) di Windows o il comando [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl?view=powershell-7) per configurare le autorizzazioni.

