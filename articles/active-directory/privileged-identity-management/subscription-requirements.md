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
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6e336ec40ba2b9f6d3018e6a4f5b2ac721077aa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895123"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Requisiti di licenza da usare Privileged Identity Management

Per usare Azure Active Directory (Azure AD) e Privileged Identity Management (PIM), una directory deve disporre di una licenza valida. È anche necessario assegnare le licenze agli amministratori e ai relativi utenti. Questo articolo descrive i requisiti di licenza per usare Privileged Identity Management.

## <a name="prerequisites"></a>Prerequisiti

Per usare Privileged Identity Management, la directory deve avere una delle licenze a pagamento o di valutazione seguenti:

- Azure AD P2 Premium
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Per altre informazioni, vedere [Informazioni su Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="which-users-must-have-licenses"></a>Quali utenti devono avere le licenze?

Ogni amministratore o utente che interagisce con o riceve un vantaggio da Privileged Identity Management deve disporre di una licenza. Ad esempio:

- Amministratori con ruoli di Azure AD gestiti tramite PIM
- Amministratori con ruoli di risorse di Azure gestiti tramite PIM
- Amministratori assegnati al ruolo Amministratore dei ruoli con privilegi
- Utenti assegnati come idonei ai ruoli di Azure AD gestiti mediante PIM
- Utenti in grado di approvare o rifiutare le richieste in PIM
- Utenti assegnati a un ruolo di risorse di Azure con assegnazioni Just-In-Time o dirette (basate sul tempo)  
- Utenti assegnati a una verifica di accesso
- Utenti che eseguono verifiche di accesso

Per informazioni su come assegnare licenze agli utenti, vedere [Assegnare o rimuovere licenze usando il portale di Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="what-happens-when-a-license-expires"></a>Che cosa accade quando una licenza scade?

Se un Azure AD Premium P2, EMS E5 o una licenza di valutazione scade, Privileged Identity Management funzionalità non saranno più disponibili nella directory:

- Le assegnazioni dei ruoli permanenti ad Azure AD non saranno interessate.
- Il servizio Privileged Identity Management nel portale di Azure, nonché i cmdlet API Graph e le interfacce PowerShell di Privileged Identity Management, non saranno più disponibili per gli utenti per l'attivazione di ruoli con privilegi, la gestione dell'accesso con privilegi o l'esecuzione di verifiche di accesso dei ruoli con privilegi.
- Le assegnazioni di ruoli idonei dei ruoli di Azure AD verranno rimosse poiché gli utenti non saranno più in grado di attivare ruoli con privilegi.
- Tutte le verifiche di accesso in corso dei ruoli Azure AD verranno terminate e verranno rimosse Privileged Identity Management impostazioni di configurazione.
- Privileged Identity Management non invierà più messaggi di posta elettronica sulle modifiche alle assegnazioni di ruolo.

## <a name="next-steps"></a>Passaggi successivi

- [Distribuisci Privileged Identity Management](pim-deployment-plan.md)
- [Inizia a usare Privileged Identity Management](pim-getting-started.md)
- [Ruoli che non è possibile gestire in Privileged Identity Management](pim-roles.md)
