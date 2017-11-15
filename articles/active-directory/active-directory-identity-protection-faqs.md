---
title: Domande frequenti su Azure Active Directory Identity Protection | Microsoft Docs
description: Domande frequenti su Azure AD Identity Protection
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 3d0505caf75a3e6433e2c9fd9795b3176d66d297
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2017
---
# <a name="azure-active-directory-identity-protection-faq"></a>Domande frequenti su Azure Active Directory Identity Protection

Questo articolo include le risposte alle domande frequenti su Azure Active Directory (Azure AD) Identity Protection. Per altre informazioni, vedere [Azure Active Directory Identity Protection](active-directory-identityprotection.md). 


## <a name="why-do-some-risk-events-have-closed-system-status"></a>Perché alcuni eventi di rischio hanno lo stato "Closed (system)" (Chiuso (sistema))?

**R:** si tratta di eventi che Azure Active Directory Identity Protection ha rilevato e successivamente chiusi perché gli eventi non venivano più considerati rischiosi. Questi eventi non vengono conteggiati ai fini del livello di rischio dell'utente. 

---

## <a name="do-i-need-to-be-a-global-admin-to-use-identity-protection-in-the-azure-portal"></a>È necessario essere un amministratore globale per usare Identity Protection nel portale di Azure?
**R:** No. È possibile avere il ruolo con autorizzazioni di lettura per la sicurezza, di amministratore protezione o amministratore globale per usare Identity Protection.

---

## <a name="how-do-i-get-identity-protection"></a>Come si ottiene Identity Protection?

**R:** Per la risposta a questa domanda, vedere [Introduzione ad Azure Active Directory Premium](active-directory-get-started-premium.md).

---

## <a name="how-can-i-sort-users-in-users-flagged-for-risk"></a>Come è possibile classificare gli utenti come "Utenti contrassegnati per il rischio"?

**R:** Scaricare gli utenti contrassegnati per il report di rischio facendo clic su **Scaricare** in alto nella pagina **Utenti contrassegnati per il rischio**. È quindi possibile ordinare i dati scaricati in base ai campi disponibili, incluso Ultimo aggiornamento (UTC).

---
