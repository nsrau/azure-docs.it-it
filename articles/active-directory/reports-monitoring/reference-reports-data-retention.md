---
title: Quanto tempo Azure AD archiviare i dati dei report? | Microsoft Docs
description: Scopri per quanto tempo Azure archivia i vari tipi di dati di report.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/05/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98b9a2da11ad32e35704a49cfcf1788f95276dda
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93393456"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Quanto tempo Azure AD archiviare i dati dei report?


In questo articolo vengono illustrati i criteri di conservazione dei dati per i diversi report attività in Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Quando Azure AD avvia la raccolta dei dati?

| Edizione di Azure AD | Avvio della raccolta |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Al momento dell'accesso a una sottoscrizione |
| Azure AD Free| La prima volta che si apre il [pannello Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) o si usano le [API di creazione report](./overview-reports.md)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Quando i dati dell'attività sono disponibili nel portale di Azure?

- **Immediatamente** : se si è già lavorato con i report nel portale di Azure.
- **Entro 2 ore** : se non si è attivata la creazione di report nel portale di Azure.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>In quanto tempo è possibile visualizzare i dati di attività dopo aver acquistato una licenza Premium?

Se si dispone già di dati di attività con la licenza gratuita, è possibile visualizzarli immediatamente dopo l'aggiornamento. Se non si dispone di dati, questi verranno visualizzati nei report dopo uno o due giorni dall'acquisto della licenza Premium.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Quando Azure AD avvia la raccolta dei dati dei segnali di sicurezza?  

Per i segnali di sicurezza, il processo di raccolta viene avviato quando si acconsente esplicitamente all'uso di **Identity Protection Center**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Per quanto tempo Azure AD archivia i dati?

**Report attività**    

| Report                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Log di controllo             | 7 giorni        | 30 giorni             | 30 giorni             |
| Accessi               | 7 giorni        | 30 giorni             | 30 giorni             |
| Utilizzo dell'autenticazione a più fattori di Azure        | 30 giorni       | 30 giorni             | 30 giorni             |

È possibile mantenere i dati relativi alle attività di controllo e accesso per più tempo rispetto al periodo di conservazione predefinito descritto in precedenza instradandoli a un account di archiviazione di Azure con Monitoraggio di Azure. Per altre informazioni, vedere [Archiviare i log di Azure AD in un account di archiviazione di Azure](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Segnali di sicurezza**

| Report         | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Utenti a rischio.  | 7 giorni        | 30 giorni             | 90 giorni             |
| Accessi a rischio | 7 giorni        | 30 giorni             | 90 giorni             |

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>È possibile visualizzare i dati del mese precedente dopo avere acquistato una licenza Azure AD Premium?

**No** , non è possibile. Azure archivia fino a sette giorni di dati di attività per una versione gratuita. Ciò significa che quando si passa da una versione gratuita a una a una versione Premium, è possibile visualizzare solo fino a 7 giorni di dati.

---