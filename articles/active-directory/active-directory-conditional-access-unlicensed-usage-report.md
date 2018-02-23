---
title: Report Utilizzo senza licenza | Microsoft Docs
description: "Il report Utilizzo senza licenza semplifica l'identificazione di utenti senza licenza che usano funzionalità a pagamento di Azure AD."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 92138f43-9528-4c8a-b834-66a47da476e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.openlocfilehash: 8bcd814ee7b7bfc158e62ad26e6cc23781f5352d
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2018
---
# <a name="unlicensed-usage-report"></a>Report Utilizzo senza licenza
Il report Utilizzo senza licenza semplifica l'identificazione di utenti senza licenza che usano funzionalità a pagamento di Azure AD. Ciò consente di usare in modo ottimale le licenze acquistate e di identificare le situazioni in cui potrebbero essere necessarie licenze aggiuntive. 

Il report mostra l'utilizzo attivo effettivo delle funzionalità a pagamento negli ultimi 30 giorni. 

## <a name="report-structure"></a>Struttura del report
| Nome colonna | DESCRIZIONE |
|:--- |:--- |
| Utente senza licenza |Nome dell'utente |
| Funzionalità |Nome della funzionalità. Ad esempio: accesso condizionale. |
| Applicazioni a cui è stato eseguito l'accesso |Nome dell'applicazione a cui viene eseguito l'accesso con la funzionalità. Ad esempio: Office 365 SharePoint Online. |

> [!NOTE]
> Se l'account utente è stato eliminato, la colonna "Utente senza licenza" verrò popolata con un ID, ad esempio 1003000090D8B285.
> 
> 

## <a name="conditional-access-feature"></a>Funzionalità di accesso condizionale
Gli utenti senza licenza verranno contrassegnati quando accedono a un servizio a cui sono applicati criteri di accesso, se non hanno una licenza di Azure AD Premium. 

Questo approccio viene applicato ai criteri di MFA e di posizione, oltre ai dispositivi dei criteri che usano Intune.

## <a name="see-also"></a>Vedere anche 
* [Protezione dell'accesso a Office 365 e ad altre app connesse ad Azure Active Directory](active-directory-conditional-access-azure-portal.md)
* [Guida introduttiva all'accesso condizionale ad Azure AD](active-directory-conditional-access-azure-portal-get-started.md) 

