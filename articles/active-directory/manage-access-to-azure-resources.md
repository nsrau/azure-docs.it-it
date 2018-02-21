---
title: Gestire l'accesso alle risorse di Azure con Azure Active Directory
description: "Informazioni sulle modalità di gestione dell'accesso alle risorse di Azure tramite diverse funzionalità di Azure Active Directory."
services: active-directory
documentationcenter: 
author: skwan
manager: mtillman
editor: bryanla
ms.assetid: f66abf54-3809-436c-92b6-018e1179780e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2017
ms.author: skwan
ms.openlocfilehash: eee4353c183aeec19f72f8e1dec6c20b6c5bb226
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="manage-access-to-azure-resources-with-azure-active-directory"></a>Gestire l'accesso alle risorse di Azure con Azure Active Directory

La gestione delle identità e dell'accesso per le risorse cloud è una funzione essenziale per qualsiasi organizzazione che usa il cloud. Azure Active Directory (Azure AD) è il sistema di gestione delle identità e dell'accesso per Microsoft Azure.  

Prima di esplorare le aree di funzionalità di supporto di Azure AD, fare riferimento a questo video: "Locking down access to the Azure Cloud using SSO, Roles Based Access Control, and Conditional" (Blocco dell'accesso al cloud di Azure con SSO, il controllo degli accessi in base al ruolo e l'accesso condizionale) Il video offre le informazioni seguenti:

- Procedure consigliate per la configurazione di Single Sign-On nel portale di Azure, con Active Directory locale.
- Uso del controllo degli accessi in base al ruolo per il controllo accurato alle risorse nelle sottoscrizioni.
- Applicazione di regole di autenticazione avanzata con l'accesso condizionale di Azure AD.
- Concetto di identità del servizio gestito, in cui le risorse di Azure possono essere autenticate automaticamente per i servizi di Azure, senza che gli sviluppatori debbano gestire le chiavi o i segreti API.

> [!VIDEO https://www.youtube.com/embed/FKBoWWKRnvI]

## <a name="feature-areas"></a>Aree di funzionalità
Azure AD offre le funzionalità seguenti per la gestione dell'accesso alle risorse di Azure:

|||
|---|---|
| [Relazione tra i tenant di Azure AD e le sottoscrizioni](active-directory-understanding-resource-access.md) | Informazioni sui motivi per cui Azure AD è l'origine attendibile di utenti e gruppi per una sottoscrizione di Azure. |
| [Controllo degli accessi in base al ruolo](role-based-access-control-what-is.md) | È possibile offrire la gestione granulare dell'accesso tramite i ruoli assegnati a utenti, gruppi o entità servizio. |
| [Privileged Identity Management con Controllo degli accessi in base al ruolo](pim-azure-resource.md) | È possibile controllare l'accesso con privilegi elevati assegnando ruoli con privilegi in modalità JIT (Just-In-Time). |
| [Accesso condizionale per la gestione di Azure](conditional-access-azure-management.md) | È possibile configurare criteri di accesso condizionale per consentire o bloccare l'accesso agli endpoint di gestione di Azure. |
| [Identità del servizio gestito](msi-overview.md) | È possibile concedere alle risorse di Azure, ad esempio alle macchine virtuali, un'identità specifica, in modo che non sia necessario inserire le credenziali nel codice. |

 