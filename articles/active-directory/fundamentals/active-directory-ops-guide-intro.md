---
title: Guida di riferimento alle operazioni di Azure Active DirectoryAzure Active Directory operations reference guide
description: Questa guida di riferimento alle operazioni descrive i controlli e le azioni da eseguire per proteggere e gestire la gestione di identità e accessi, l'autenticazione, la governance e le operazioni
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: c815256e60e5ca6582220d247d587a4488c4a55a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74535314"
---
# <a name="azure-active-directory-operations-reference-guide"></a>Guida di riferimento alle operazioni di Azure Active DirectoryAzure Active Directory operations reference guide

In questa guida di riferimento alle operazioni vengono descritti i controlli e le azioni da eseguire per proteggere e gestire le aree seguenti:This operations reference guide describes the checks and actions you should take to secure and maintain the following areas:

- **[Gestione di identità e accessi:](active-directory-ops-guide-iam.md)** capacità di gestire il ciclo di vita delle identità e i relativi diritti.
- **[Gestione dell'autenticazione:](active-directory-ops-guide-auth.md)** possibilità di gestire le credenziali, definire l'esperienza di autenticazione, delegare l'assegnazione, misurare l'utilizzo e definire criteri di accesso in base ai requisiti di sicurezza aziendali.
- **[Governance:](active-directory-ops-guide-govern.md)** capacità di valutare e attestare l'accesso concesso a identità senza privilegi e privilegiate, controllare e controllare le modifiche all'ambiente.
- **[Operazioni:](active-directory-ops-guide-ops.md)** ottimizzare le operazioni di Azure Active Directory (Azure AD).

Alcuni consigli potrebbero non essere applicabili all'ambiente di tutti i clienti, ad esempio le procedure consigliate di ADFS potrebbero non essere applicabili se l'organizzazione utilizza la sincronizzazione dell'hash delle password.

> [!NOTE]
> Questi consigli sono aggiornati alla data di pubblicazione, ma possono cambiare nel tempo. Le organizzazioni devono valutare continuamente le proprie procedure di identità man mano che i prodotti e i servizi Microsoft si evolvono nel tempo. I suggerimenti possono cambiare quando le organizzazioni sottoscrivono una licenza di Azure AD Premium diversa. Ad esempio, Azure AD Premium P2 includerà ulteriori consigli sulla governance.

## <a name="stakeholders"></a>Parti interessate

Ogni sezione di questa guida di riferimento consiglia di assegnare le parti interessate per pianificare e implementare correttamente le attività chiave. La tabella seguente descrive l'elenco di tutte le parti interessate in questa guida:

| Stakeholder | Descrizione |
| :- | :- |
| Team operativo IAM | Questo team gestisce la gestione delle operazioni quotidiane del sistema di gestione delle identità e degli accessi |
| Team per la produttività | Questo team possiede e gestisce le applicazioni di produttività, ad esempio posta elettronica, condivisione e collaborazione di file, messaggistica istantanea e conferenze. |
| Proprietario dell'applicazione | Questo team è proprietario dell'applicazione specifica da un'azienda e in genere da una prospettiva tecnica in un'organizzazione. |
| Team di architettura InfoSec | Questo team pianifica e progetta le procedure di sicurezza delle informazioni di un'organizzazione. |
| Team operativo InfoSec | Questo team esegue e monitora le procedure di sicurezza delle informazioni implementate del team InfoSec Architecture. |

## <a name="next-steps"></a>Passaggi successivi

Introduzione ai controlli e alle azioni di gestione di [identità e accessi.](active-directory-ops-guide-iam.md)
