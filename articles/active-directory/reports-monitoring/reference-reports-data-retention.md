---
title: Criteri di conservazione dei report di Azure Active Directory | Documentazione Microsoft
description: Criteri di conservazione dei dati di report in Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: abd64b7d2fa7930f5b6177c7ac037840da34dc18
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52333582"
---
# <a name="azure-active-directory-report-retention-policies"></a>Criteri di conservazione dei report di Azure Active Directory

In questo articolo vengono illustrati i criteri di conservazione dei dati per i diversi report attività in Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Quando Azure AD avvia la raccolta dei dati?

| Edizione di Azure AD | Avvio della raccolta |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Al momento dell'accesso a una sottoscrizione |
| Azure AD Free <br /> Azure AD Basic | La prima volta che si apre il [pannello Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) o si usano le [API di creazione report](https://aka.ms/aadreports)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Quando i dati dell'attività sono disponibili nel portale di Azure?

- **Immediatamente**: se si usano già i report nel portale di Azure.
- **Entro 2 ore**: se non si è attivata la creazione di report nel portale di Azure.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Quando Azure AD avvia la raccolta dei dati dei segnali di sicurezza?  

Per i segnali di sicurezza, il processo di raccolta viene avviato quando si acconsente esplicitamente all'uso di **Identity Protection Center**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Per quanto tempo Azure AD archivia i dati?

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
