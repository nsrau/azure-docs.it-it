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
ms.topic: how-to
ms.subservice: pim
ms.date: 08/06/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74c9cd1c55f1b0dde173a7ffbeac92e5518db81e
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88005804"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Requisiti di licenza per usare Privileged Identity Management

Per usare Azure Active Directory (Azure AD) e Privileged Identity Management (PIM), una directory deve disporre di una licenza valida. È anche necessario assegnare le licenze agli amministratori e ai relativi utenti. Questo articolo descrive i requisiti di licenza per usare Privileged Identity Management.

## <a name="valid-licenses"></a>Licenze valide

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="licenses-you-must-have"></a>Licenze che è necessario avere

Assicurarsi che la directory includa un numero di licenze Azure AD Premium P2 almeno uguale al numero di dipendenti che eseguiranno le attività seguenti:

- Utenti assegnati come idonei a Azure AD o ruoli di Azure gestiti con PIM
- Utenti assegnati come membri idonei o proprietari dei gruppi di accesso con privilegi
- Utenti in grado di approvare o rifiutare le richieste di attivazione in PIM
- Utenti assegnati a una verifica di accesso
- Utenti che eseguono verifiche di accesso

Le licenze di Azure AD Premium P2 **non** sono necessarie per le attività seguenti:

- Non sono necessarie licenze per gli utenti che hanno configurato PIM, configurare criteri, ricevere avvisi e configurare le verifiche di accesso.

Per altre informazioni sulle licenze, vedere [Assegnare o rimuovere licenze usando il portale di Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="example-license-scenarios"></a>Scenari di licenze di esempio

Ecco alcuni esempi di scenari che consentono di determinare il numero di licenze necessarie.

| Scenario | Calcolo | Numero di licenze |
| --- | --- | --- |
| Woodgrove Bank ha 10 amministratori per diversi reparti e 2 amministratori globali che configurano e gestiscono PIM. Che rendono i cinque amministratori idonei. | Cinque licenze per gli amministratori idonei | 5 |
| Graphic Design Institute dispone di 25 amministratori per i quali 14 vengono gestiti tramite PIM. L'attivazione del ruolo richiede l'approvazione e sono presenti tre utenti diversi nell'organizzazione che possono approvare le attivazioni. | 14 licenze per i ruoli idonei + tre responsabili approvazione | 17 |
| Contoso ha 50 amministratori di cui 42 sono gestiti tramite PIM. L'attivazione del ruolo richiede l'approvazione e sono presenti cinque utenti diversi nell'organizzazione che possono approvare le attivazioni. Contoso esegue anche le verifiche mensili degli utenti assegnati ai ruoli di amministratore e i revisori sono i responsabili degli utenti di cui sei in ruoli di amministratore gestiti da PIM. | 42 licenze per i ruoli idonei + cinque responsabili approvazione + sei revisori | 53 |

## <a name="when-a-license-expires"></a>Scadenza di una licenza

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
