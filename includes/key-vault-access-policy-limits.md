---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: 8c8c9563c954e3d1a84ea2b9f411187d5fb9f226
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026348"
---
L'insieme di credenziali delle chiavi supporta fino a 1024 voci di criteri di accesso, con ogni voce che concede un set di autorizzazioni distinte a una particolare entità di sicurezza. A causa di questa limitazione, è consigliabile assegnare i criteri di accesso a gruppi di utenti, laddove possibile, anziché a singoli utenti. L'uso dei gruppi semplifica la gestione delle autorizzazioni per più utenti dell'organizzazione. Per altre informazioni, vedere [gestire l'accesso alle app e alle risorse usando gruppi di Azure Active Directory](../articles/active-directory/fundamentals/active-directory-manage-groups.md)

Per i dettagli completi sul controllo di accesso di Key Vault, vedere [Sicurezza di Azure Key Vault: Gestione delle identità e dell'accesso](../articles/key-vault/general/overview-security.md#identity-and-access-management).