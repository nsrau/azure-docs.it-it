---
title: Requisiti di licenza per l'uso di Privileged Identity Management - Azure Active Directory Documenti Microsoft
description: Illustra i requisiti della licenza per l'uso di Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70696cdb95fffc1e5faa46ca1b5f2180633ed63a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932328"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Requisiti di licenza per usare Privileged Identity Management

Per usare Azure Active Directory (Azure AD) e Privileged Identity Management (PIM), una directory deve disporre di una licenza valida. È anche necessario assegnare le licenze agli amministratori e ai relativi utenti. In questo articolo vengono descritti i requisiti di licenza per utilizzare Gestione identità con privilegi.

## <a name="valid-licenses"></a>Licenze valide

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="how-many-licenses-must-you-have"></a>Quante licenze è necessario disporre?

Verificare che la directory disponga di almeno il numero di licenze di Azure AD Premium P2 rispetto ai dipendenti che eseguiranno le attività seguenti:Ensure that your directory has at least as many Azure AD Premium P2 licenses as you have employees that will be performing the following tasks:

- Utenti assegnati come idonei ai ruoli di Azure AD gestiti tramite PIMUsers assigned as eligible to Azure AD roles managed using PIM
- Utenti in grado di approvare o rifiutare le richieste di attivazione in PIM
- Utenti assegnati a un ruolo di risorse di Azure con assegnazioni Just-In-Time o dirette (basate sul tempo)  
- Utenti assegnati a una verifica di accesso
- Utenti che eseguono verifiche di accesso

Le licenze di Azure AD Premium P2 non sono necessarie per le attività seguenti:Azure AD Premium P2 licenses are **not** required for the following tasks:

- Non sono necessarie licenze per gli utenti con i ruoli Amministratore globale o Amministratore ruoli con privilegi che configurano PIM, configurano criteri, ricevono avvisi e configurano verifiche di accesso.

Per altre informazioni sulle licenze, vedere [Assegnare o rimuovere licenze tramite il portale](../fundamentals/license-users-groups.md)di Azure Active Directory.For more information about licenses, see Assign or remove licenses using the Azure Active Directory portal.

## <a name="example-license-scenarios"></a>Scenari di licenza di esempio

Di seguito sono riportati alcuni scenari di licenza di esempio che consentono di determinare il numero di licenze necessarie.

| Scenario | Calcolo | Numero di licenze |
| --- | --- | --- |
| La Woodgrove Bank dispone di 10 amministratori per i diversi reparti e 2 amministratori globali che configurano e gestiscono PIM. Consentono a cinque amministratori di essere idonei. | Cinque licenze per gli amministratori idonei | 5 |
| Graphic Design Institute ha 25 amministratori di cui 14 gestiti tramite PIM. L'attivazione dei ruoli richiede l'approvazione e nell'organizzazione sono presenti tre utenti diversi che possono approvare le attivazioni. | 14 licenze per i ruoli idonei - tre approvatori | 17 |
| Contoso dispone di 50 amministratori di cui 42 gestiti tramite PIM. L'attivazione dei ruoli richiede l'approvazione e nell'organizzazione sono presenti cinque utenti diversi che possono approvare le attivazioni. Contoso esegue inoltre revisioni mensili degli utenti assegnati ai ruoli di amministratore e i revisori sono i responsabili degli utenti, di cui sei non sono in ruoli di amministratore gestiti da PIM. | 42 licenze per i ruoli idonei - cinque approvatori e sei revisori | 53 |

## <a name="what-happens-when-a-license-expires"></a>Che cosa accade quando una licenza scade?

Se una licenza di Azure AD Premium P2, EMS E5 o di prova scade, le funzionalità di gestione delle identità con privilegi non saranno più disponibili nella directory:If an Azure AD Premium P2, EMS E5, or trial license expires, Privileged Identity Management features will no longer available in your directory:

- Le assegnazioni dei ruoli permanenti ad Azure AD non saranno interessate.
- Il servizio Gestione identità con privilegi nel portale di Azure, nonché i cmdlet dell'API Graph e le interfacce di PowerShell di Gestione identità con privilegi, non sarà più disponibile per gli utenti per attivare ruoli con privilegi, gestire l'accesso con privilegi o eseguire accedere alle revisioni dei ruoli privilegiati.
- Le assegnazioni di ruoli idonei dei ruoli di Azure AD verranno rimosse poiché gli utenti non saranno più in grado di attivare ruoli con privilegi.
- Tutte le verifiche di accesso in corso dei ruoli di Azure AD termineranno e le impostazioni di configurazione di Gestione identità con privilegi verranno rimosse.
- Gestione identità con privilegi non invierà più messaggi di posta elettronica in caso di modifiche alle assegnazioni di ruolo.

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire Privileged Identity Management](pim-deployment-plan.md)
- [Iniziare a usare Privileged Identity Management](pim-getting-started.md)
- [Ruoli che non è possibile gestire in Gestione identità con privilegi](pim-roles.md)
