---
title: Requisiti di licenza per l'uso di Privileged Identity Management Azure Active Directory | Microsoft Docs
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

Per usare Azure Active Directory (Azure AD) e Privileged Identity Management (PIM), una directory deve disporre di una licenza valida. È anche necessario assegnare le licenze agli amministratori e ai relativi utenti. Questo articolo descrive i requisiti di licenza per usare Privileged Identity Management.

## <a name="valid-licenses"></a>Licenze valide

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="how-many-licenses-must-you-have"></a>Quante licenze è necessario avere?

Assicurarsi che la directory includa almeno il numero di licenze Azure AD Premium P2 disponibili per i dipendenti che eseguiranno le attività seguenti:

- Utenti assegnati come idonei ai ruoli di Azure AD gestiti mediante PIM
- Utenti in grado di approvare o rifiutare le richieste di attivazione in PIM
- Utenti assegnati a un ruolo di risorse di Azure con assegnazioni Just-In-Time o dirette (basate sul tempo)  
- Utenti assegnati a una verifica di accesso
- Utenti che eseguono verifiche di accesso

Le licenze Azure AD Premium P2 **non** sono necessarie per le attività seguenti:

- Non sono necessarie licenze per gli utenti con ruoli di amministratore globale o amministratore del ruolo con privilegi che configurano PIM, configurano criteri, ricevono avvisi e configurano le verifiche di accesso.

Per altre informazioni sulle licenze, vedere [assegnare o rimuovere licenze usando il portale di Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="example-license-scenarios"></a>Scenari di licenza di esempio

Di seguito sono riportati alcuni scenari di licenza di esempio che consentono di determinare il numero di licenze che è necessario avere.

| Scenario | Calcolo | Numero di licenze |
| --- | --- | --- |
| Woodgrove Bank ha 10 amministratori per diversi reparti e 2 amministratori globali che configurano e gestiscono PIM. Che rendono i cinque amministratori idonei. | Cinque licenze per gli amministratori idonei | 5 |
| Graphic Design Institute dispone di 25 amministratori per i quali 14 vengono gestiti tramite PIM. L'attivazione del ruolo richiede l'approvazione e sono presenti tre utenti diversi nell'organizzazione che possono approvare le attivazioni. | 14 licenze per i ruoli idonei + tre responsabili approvazione | 17 |
| Contoso ha 50 amministratori di cui 42 sono gestiti tramite PIM. L'attivazione del ruolo richiede l'approvazione e sono presenti cinque utenti diversi nell'organizzazione che possono approvare le attivazioni. Contoso esegue anche le verifiche mensili degli utenti assegnati ai ruoli di amministratore e i revisori sono i responsabili degli utenti di cui sei in ruoli di amministratore gestiti da PIM. | 42 licenze per i ruoli idonei + cinque responsabili approvazione + sei revisori | 53 |

## <a name="what-happens-when-a-license-expires"></a>Che cosa accade quando una licenza scade?

Se un Azure AD Premium P2, EMS E5 o una licenza di valutazione scade, Privileged Identity Management funzionalità non saranno più disponibili nella directory:

- Le assegnazioni dei ruoli permanenti ad Azure AD non saranno interessate.
- Il servizio Privileged Identity Management nel portale di Azure, nonché i cmdlet API Graph e le interfacce PowerShell di Privileged Identity Management, non saranno più disponibili per gli utenti per l'attivazione di ruoli con privilegi, la gestione dell'accesso con privilegi o l'esecuzione di verifiche di accesso dei ruoli con privilegi.
- Le assegnazioni di ruoli idonei dei ruoli di Azure AD verranno rimosse poiché gli utenti non saranno più in grado di attivare ruoli con privilegi.
- Tutte le verifiche di accesso in corso dei ruoli Azure AD verranno terminate e verranno rimosse Privileged Identity Management impostazioni di configurazione.
- Privileged Identity Management non invierà più messaggi di posta elettronica sulle modifiche alle assegnazioni di ruolo.

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire Privileged Identity Management](pim-deployment-plan.md)
- [Iniziare a usare Privileged Identity Management](pim-getting-started.md)
- [Ruoli che non è possibile gestire in Privileged Identity Management](pim-roles.md)
