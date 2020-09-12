---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: c74f2543e96087378741d6388b7c27dc4d05ddc8
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89381039"
---
Key Vault supporta fino a 1024 voci di criteri di accesso, con ogni voce che concede un set di autorizzazioni distinto a una determinata entità di sicurezza. A causa di questa limitazione, è consigliabile assegnare i criteri di accesso a gruppi di utenti, laddove possibile, anziché a singoli utenti. L'uso dei gruppi semplifica la gestione delle autorizzazioni per più utenti dell'organizzazione. Per altre informazioni, vedere [gestire l'accesso alle app e alle risorse usando gruppi di Azure Active Directory](/azure/active-directory/fundamentals/active-directory-manage-groups)

Per i dettagli completi sul controllo di accesso di Key Vault, vedere [Sicurezza di Azure Key Vault: Gestione delle identità e dell'accesso](/azure/key-vault/general/overview-security#identity-and-access-management). 
