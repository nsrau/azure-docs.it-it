---
title: Domande frequenti su Azure Active Directory Identity Protection | Microsoft Docs
description: Domande frequenti su Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc02b8b5dccbec106e90430fc1714253df6ad847
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334006"
---
# <a name="azure-active-directory-identity-protection-faq"></a>Domande frequenti su Azure Active Directory Identity Protection

Questo articolo include le risposte alle domande frequenti su Azure Active Directory (Azure AD) Identity Protection. Per altre informazioni, vedere [Azure Active Directory Identity Protection](../active-directory-identityprotection.md). 

## <a name="why-do-some-risk-events-have-closed-system-status"></a>Perché alcuni eventi di rischio hanno lo stato "Closed (system)" (Chiuso (sistema))?

**R:** Questi eventi di rischio sono stati rilevati e successivamente chiusi da Identity Protection perché gli eventi non sono più stati considerati rischiosi. Questi eventi non vengono conteggiati ai fini del livello di rischio dell'utente. 

---

## <a name="do-i-need-to-be-a-global-admin-to-use-identity-protection-in-the-azure-portal"></a>È necessario essere un amministratore globale per usare Identity Protection nel portale di Azure?
**R:**  No. È possibile avere il ruolo con autorizzazioni di lettura per la sicurezza, di amministratore protezione o amministratore globale per usare Identity Protection.

---

## <a name="how-do-i-get-identity-protection"></a>Come si ottiene Identity Protection?

**R:** Per la risposta a questa domanda, vedere [Introduzione ad Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md).

---

## <a name="how-can-i-sort-users-in-users-flagged-for-risk"></a>Come è possibile classificare gli utenti come "Utenti contrassegnati per il rischio"?

**R:** Scaricare gli utenti contrassegnati per il report di rischio facendo clic su **Download**  in alto nella pagina **Utenti contrassegnati per il rischio**. È quindi possibile ordinare i dati scaricati in base ai campi disponibili, incluso Ultimo aggiornamento (UTC).

---
