---
title: Guida di riferimento per le operazioni Azure Active Directory
description: Questa guida di riferimento per le operazioni descrive i controlli e le azioni da eseguire per proteggere e gestire le attività di gestione delle identità e degli accessi, autenticazione, governance e operazioni
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
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535314"
---
# <a name="azure-active-directory-operations-reference-guide"></a>Guida di riferimento per le operazioni Azure Active Directory

Questa guida di riferimento per le operazioni descrive i controlli e le azioni da eseguire per proteggere e gestire le aree seguenti:

- **[Gestione delle identità e dell'accesso](active-directory-ops-guide-iam.md)** : possibilità di gestire il ciclo di vita delle identità e dei rispettivi diritti.
- **[Gestione dell'autenticazione](active-directory-ops-guide-auth.md)** : possibilità di gestire le credenziali, definire l'esperienza di autenticazione, assegnare i delegati, misurare l'utilizzo e definire criteri di accesso in base al comportamento di sicurezza dell'organizzazione.
- **[Governance](active-directory-ops-guide-govern.md)** : possibilità di valutare e attestare l'accesso concesso a identità senza privilegi e con privilegi, controllare e controllare le modifiche apportate all'ambiente.
- **[Operazioni](active-directory-ops-guide-ops.md)** : ottimizzare le operazioni Azure Active Directory (Azure ad).

Alcune raccomandazioni potrebbero non essere applicabili all'ambiente di tutti i clienti, ad esempio AD FS procedure consigliate potrebbero non essere valide se l'organizzazione usa la sincronizzazione dell'hash delle password.

> [!NOTE]
> Questi consigli sono aggiornati alla data di pubblicazione, ma possono cambiare nel tempo. Le organizzazioni devono valutare continuamente le proprie procedure di identità, in quanto i prodotti e i servizi Microsoft evolvono nel tempo. Le raccomandazioni possono cambiare quando le organizzazioni sottoscrivono una licenza di Azure AD Premium diversa. Ad esempio, Azure AD Premium P2 includerà ulteriori consigli sulla governance.

## <a name="stakeholders"></a>Parti interessate

In ogni sezione di questa guida di riferimento è consigliabile assegnare le parti interessate per pianificare e implementare correttamente le attività principali. La tabella seguente illustra l'elenco di tutte le parti interessate in questa guida:

| Stakeholder | DESCRIZIONE |
| :- | :- |
| Team operativo IAM | Questo team gestisce la gestione delle operazioni quotidiane del sistema di gestione delle identità e degli accessi |
| Team di produttività | Questo team possiede e gestisce le applicazioni di produttività, ad esempio la posta elettronica, la condivisione di file e la collaborazione, la messaggistica immediata e la conferenza. |
| Proprietario dell'applicazione | Questo team è proprietario dell'applicazione specifica di un'azienda e, in genere, un punto di vista tecnico in un'organizzazione. |
| Team di architettura InfoSec | Questo team pianifica e progetta le procedure di sicurezza delle informazioni di un'organizzazione. |
| Team operativo di InfoSec | Questo team esegue e monitora le procedure di sicurezza delle informazioni implementate dal team dell'architettura InfoSec. |

## <a name="next-steps"></a>Passaggi successivi

Introduzione ai [controlli e alle azioni di gestione delle identità e degli accessi](active-directory-ops-guide-iam.md).
