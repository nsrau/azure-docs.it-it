---
title: Che cos'è il provisioning di identità con Azure AD? | Microsoft Docs
description: Descrive una panoramica del provisioning di identità.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5d5528865fc910751b8052dcc78a82131e17290
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91266242"
---
# <a name="what-is-identity-provisioning"></a>Che cos'è il provisioning di identità?

Oggi nelle aziende di piccole e grandi dimensioni si ricorre sempre più spesso a una combinazione di applicazioni locali e cloud e  gli utenti devono poter accedere alle applicazioni sia in locale che nel cloud. È necessario avere una singola identità tra le varie applicazioni (in locale e nel cloud).

Il provisioning è il processo di creazione di un oggetto in base a determinate condizioni, mantenendo l'oggetto aggiornato ed eliminandolo quando le condizioni non vengono più soddisfatte. Ad esempio, un nuovo utente che entra a far parte dell'organizzazione viene immesso nel sistema di gestione delle risorse umane.  A quel punto, il provisioning può creare un account utente corrispondente nel cloud, in Active Directory e nelle varie applicazioni a cui l'utente deve poter accedere.  In questo modo l'utente può iniziare a lavorare e avrà accesso fin dal primo giorno alle applicazioni e ai sistemi necessari. 

![Diagramma che mostra il provisioning nel cloud con Azure Active Directory.](media/what-is-provisioning/cloud1.png)

Per quanto riguarda Azure Active Directory, il provisioning può essere suddiviso negli scenari chiave seguenti.  

- **[Provisioning basato su risorse umane](#hr-driven-provisioning)**  
- **[Provisioning di app](#app-provisioning)**  
- **[Provisioning della directory](#directory-provisioning)** 

## <a name="hr-driven-provisioning"></a>Provisioning basato su risorse umane

![Diagramma che mostra il provisioning basato su risorse umane con il sistema di gestione delle risorse umane nel cloud, in locale e in Azure Active Directory.](media/what-is-provisioning/cloud2.png)

Il provisioning dalle risorse umane al cloud comporta la creazione di oggetti (utenti, ruoli, gruppi e così via) in base alle informazioni incluse nel sistema di gestione delle risorse umane.  

Lo scenario più comune è quando un nuovo dipendente entra a far parte dell'azienda e viene immesso nel sistema di gestione delle risorse umane.  A quel punto, ne viene effettuato il provisioning nel cloud,  in questo caso, Azure AD.  Il provisioning dalle risorse umane può coprire gli scenari seguenti. 

- **Assunzione di nuovi dipendenti**: quando viene aggiunto un nuovo dipendente al sistema di gestione delle risorse umane nel cloud, viene creato automaticamente un account utente in Active Directory, in Azure Active Directory e, facoltativamente, in Microsoft 365 e in altre applicazioni SaaS supportate da Azure AD, con il writeback dell'indirizzo di posta elettronica al sistema di gestione delle risorse umane nel cloud.
- **Aggiornamenti di attributi e profili dei dipendenti**: se il record di un dipendente viene aggiornato nel sistema di gestione delle risorse umane nel cloud (ad esempio, il nome, la qualifica o il manager), il relativo account utente verrà aggiornato automaticamente in Active Directory, in Azure Active Directory e, facoltativamente, in Microsoft 365 e in altre applicazioni SaaS supportate da Azure AD.
- **Termine del rapporto con i dipendenti**: quando un dipendente viene eliminato dal sistema di gestione delle risorse umane nel cloud, il relativo account utente viene disabilitato automaticamente in Active Directory, Azure Active Directory e, facoltativamente, in Office 365 e in altre applicazioni SaaS supportate da Azure AD.
- **Riassunzioni di dipendenti**: quando un dipendente viene nuovamente aggiunto nel sistema di gestione delle risorse umane nel cloud, il relativo account utente precedente può essere automaticamente riattivato o sottoposto di nuovo a provisioning (a seconda delle preferenze) in Active Directory, in Azure Active Directory e, facoltativamente, in Microsoft 365 e in altre applicazioni SaaS supportate da Azure AD.


## <a name="app-provisioning"></a>Provisioning di app

![Diagramma che mostra il provisioning delle app con app locali, app nel cloud non Microsoft e Azure Active Directory.](media/what-is-provisioning/cloud3.png)

In Azure Active Directory (Azure AD) il termine **[provisioning dell'app](../app-provisioning/user-provisioning.md)** si riferisce alla creazione automatica di identità e ruoli utente nelle applicazioni cloud a cui gli utenti devono accedere. Oltre a creare le identità utente, il provisioning automatico include la manutenzione e la rimozione delle identità utente quando lo stato o i ruoli cambiano. Gli scenari comuni includono il provisioning di un utente di Azure AD in applicazioni come [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md) e altre ancora.

## <a name="directory-provisioning"></a>Provisioning della directory

![Provisioning cloud](media/what-is-provisioning/cloud4.png)

Il provisioning locale comporta il provisioning da origini locali (ad esempio Active Directory) ad Azure AD.  

Lo scenario più comune è quando viene effettuato il provisioning di un utente di Active Directory (AD) in Azure AD.

Questo risultato si ottiene con il servizio di sincronizzazione di Azure AD Connect, il provisioning cloud di Azure AD Connect e Microsoft Identity Manager. 
 
## <a name="next-steps"></a>Passaggi successivi 

- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)
- [Installare il provisioning cloud](how-to-install.md)