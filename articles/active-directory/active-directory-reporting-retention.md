---
title: Criteri di conservazione dei report di Azure Active Directory | Microsoft Docs
description: Criteri di conservazione dei dati di report in Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/10/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 9101b3877f8a011878baeed0d5c23d29fddaeaad
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2018
ms.locfileid: "34055172"
---
# <a name="azure-active-directory-report-retention-policies"></a>Criteri di conservazione dei report di Azure Active Directory


Questo argomento offre le risposte alle domande più comuni sulla conservazione dei dati per i diversi report attività in Azure Active Directory. 

### <a name="q-how-can-you-get-the-collection-of-activity-data-started"></a>D: Come è possibile avviare la raccolta dei dati dell'attività?

**R:**

| Edizione di Azure AD | Avvio della raccolta |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD P2 Premium | Quando ci si iscrive a una sottoscrizione |
| Azure AD Free | La prima volta che si apre il [pannello Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) o si usano le [API di creazione report](https://aka.ms/aadreports)  |

---
### <a name="q-when-is-your-activity-data-available-in-the-azure-portal"></a>D: Quando i dati dell'attività sono disponibili nel portale di Azure?

**R:**

- **Immediatamente**: se si usano già i report nel portale di Azure
- **Entro 2 ore**: se non si è attivata la creazione di report nel portale di Azure

---

### <a name="q-how-can-you-get-the-collection-of-security-signals-started"></a>D: Come è possibile avviare la raccolta dei segnali di sicurezza?  

**R:** Per i segnali di sicurezza, il processo di raccolta viene avviato quando si acconsente esplicitamente all'uso di Identity Protection Center. 


---

### <a name="q-for-how-long-is-the-collected-data-stored"></a>D: Per quanto tempo rimangono archiviati i dati raccolti?

**R:**

**Report attività**    

| Report                 | Azure AD Free | Azure AD P1 Premium | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Directory Audit (Controllo directory)        | 7 giorni        | 30 giorni             | 30 giorni             |
| Attività di accesso       | N/D           | 30 giorni             | 30 giorni             |
| Uso di Azure MFA        | 30 giorni       | 30 giorni             | 30 giorni             |

**Segnali di sicurezza**

| Report         | Azure AD Free | Azure AD P1 Premium | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Utenti a rischio.  | 7 giorni        | 30 giorni             | 90 giorni             |
| Accessi a rischio | 7 giorni        | 30 giorni             | 90 giorni             |

---
