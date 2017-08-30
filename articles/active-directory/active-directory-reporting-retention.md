---
title: Criteri di conservazione dei report di Azure Active Directory | Documentazione Microsoft
description: Criteri di conservazione dei dati di report in Azure Active Directory
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/05/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: aa7a69c933abfda3bf4d1ac1a298c4ba684efd7e
ms.contentlocale: it-it
ms.lasthandoff: 04/06/2017

---
# <a name="azure-active-directory-report-retention-policies"></a>Criteri di conservazione dei report di Azure Active Directory


Questo argomento offre le risposte alle domande più comuni sulla conservazione dei dati per i diversi report attività in Azure Active Directory. 

**D: Come è possibile avviare la raccolta dei dati dell'attività?**

**R:**

| Edizione di Azure AD | Avvio della raccolta |
| :--              | :--   |
| Azure AD P1 Premium <br /> Azure AD P2 Premium | Quando ci si iscrive a una sottoscrizione |
| Azure AD Free | La prima volta che si apre il [pannello Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) o si usano le [API di creazione report](https://aka.ms/aadreports)  |

---
**D: Quando i dati dell'attività sono disponibili nel portale di Azure?**

**R:**

- **Immediatamente**: se si usano già i report nel portale di Azure classico
- **Entro 2 ore**: se non si è attivata la creazione di report nel portale di Azure classico

---
**D: Come è possibile avviare la raccolta dei segnali di sicurezza?**  

**R:** Per i segnali di sicurezza, il processo di raccolta viene avviato quando si acconsente esplicitamente all'uso di Identity Protection Center. 


---
**D: Per quanto tempo rimangono archiviati i dati raccolti?**

**R:**

**Report attività**    

| Report                 | Azure AD Free | Azure AD P1 Premium | Azure AD P2 Premium |
| :--                    | :--           | :--                 | :--                 |
| Directory Audit (Controllo directory)        | 7 giorni        | 30 giorni             | 30 giorni             |
| Attività di accesso       | 7 giorni        | 30 giorni             | 30 giorni             |

**Segnali di sicurezza**

| Report         | Azure AD Free | Azure AD P1 Premium | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Utenti a rischio.  | 7 giorni        | 30 giorni             | 90 giorni             |
| Accessi a rischio | 7 giorni        | 30 giorni             | 90 giorni             |

---
