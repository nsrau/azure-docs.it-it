---
title: Report Utilizzo senza licenza | Microsoft Docs
description: Il report Utilizzo senza licenza semplifica l'identificazione di utenti senza licenza che usano funzionalità a pagamento di Azure AD.
services: active-directory
documentationcenter: ''
author: markusvi
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: markvi

---
# Report Utilizzo senza licenza
Il report Utilizzo senza licenza semplifica l'identificazione di utenti senza licenza che usano funzionalità a pagamento di Azure AD. Ciò consente di usare in modo ottimale le licenze acquistate e di identificare le situazioni in cui potrebbero essere necessarie licenze aggiuntive.

Il report mostra l'utilizzo attivo effettivo delle funzionalità a pagamento negli ultimi 30 giorni.

## Struttura del report
| Nome colonna | Descrizione |
|:--- |:--- |
| Utente senza licenza |Nome dell'utente |
| Funzionalità |Nome della funzionalità. Ad esempio: accesso condizionale. |
| Applicazioni a cui è stato eseguito l'accesso |Nome dell'applicazione a cui viene eseguito l'accesso con la funzionalità. Ad esempio: Office 365 SharePoint Online. |

> [!NOTE]
> Se l'account utente è stato eliminato, la colonna "Utente senza licenza" verrò popolata con un ID, ad esempio 1003000090D8B285.
> 
> 

## Funzionalità di accesso condizionale
Gli utenti senza licenza verranno contrassegnati quando accedono a un servizio a cui sono applicati criteri di accesso, se non hanno una licenza di Azure AD Premium.

Questo approccio viene applicato ai criteri di MFA e di posizione, oltre ai dispositivi dei criteri che usano Intune.

## Vedere anche
* [Protezione dell'accesso a Office 365 e ad altre app connesse ad Azure Active Directory](active-directory-conditional-access.md)
* [Guida introduttiva all'accesso condizionale ad Azure AD](active-directory-conditional-access-azuread-connected-apps.md)

<!---HONumber=AcomDC_0727_2016-->