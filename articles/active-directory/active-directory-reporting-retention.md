---
title: Criteri di conservazione dei report di Azure Active Directory | Documentazione Microsoft
description: Criteri di conservazione dei dati di report in Azure Active Directory
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 61d3e8fbe26ab24ba0b551e52be0769228f09a11
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2017
---
# <a name="azure-active-directory-report-retention-policies"></a>Criteri di conservazione dei report di Azure Active Directory


Questo argomento offre le risposte alle domande più comuni sulla conservazione dei dati per i diversi report attività in Azure Active Directory. 

**D: Come è possibile avviare la raccolta dei dati dell'attività?**

**R:**

| Edizione di Azure AD | Avvio della raccolta |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD P2 Premium | Quando ci si iscrive a una sottoscrizione |
| Azure AD Free | La prima volta che si apre il [pannello Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) o si usano le [API di creazione report](https://aka.ms/aadreports)  |

---
**D: Quando i dati dell'attività sono disponibili nel portale di Azure?**

**R:**

- **Immediatamente** : se sta già utilizzando i report nel portale di Azure
- **Entro 2 ore** : se è stato attivato reporting nel portale di Azure

---
**D: Come è possibile avviare la raccolta dei segnali di sicurezza?**  

**R:** Per i segnali di sicurezza, il processo di raccolta viene avviato quando si acconsente esplicitamente all'uso di Identity Protection Center. 


---
**D: Per quanto tempo rimangono archiviati i dati raccolti?**

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
