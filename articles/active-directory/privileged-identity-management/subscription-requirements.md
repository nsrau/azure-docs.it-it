---
title: Requisiti della licenza per usare PIM - Azure | Microsoft Docs
description: Illustra i requisiti della licenza per l'uso di Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/16/2019
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: ad64fba0288aa5663e008484d6f0d2cbdff0bca6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55187284"
---
# <a name="license-requirements-to-use-pim"></a>Requisiti della licenza per usare PIM

Per usare Azure Active Directory (Azure AD) e Privileged Identity Management (PIM), una directory deve disporre di una licenza valida. È anche necessario assegnare le licenze agli amministratori e ai relativi utenti. Questo articolo descrive i requisiti della licenza per usare PIM.

## <a name="prerequisites"></a>Prerequisiti

Per usare PIM, la directory deve avere una delle licenze di valutazione o a pagamento seguenti:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Per altre informazioni, vedere [Informazioni su Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="which-users-must-have-licenses"></a>Quali utenti devono avere le licenze?

Ogni amministratore o utente che interagisce con o riceve un vantaggio da PIM deve avere una licenza. Tra gli esempi sono inclusi:

- Amministratori con ruoli di Azure AD gestiti tramite PIM
- Amministratori con ruoli di risorse di Azure gestiti tramite PIM
- Amministratori assegnati al ruolo Amministratore dei ruoli con privilegi
- Utenti assegnati come idonei per ruoli della directory gestiti tramite PIM
- Utenti in grado di approvare o rifiutare le richieste in PIM
- Utenti assegnati a un ruolo di risorse di Azure con assegnazioni Just-In-Time o dirette (basate sul tempo)  
- Utenti assegnati a una verifica di accesso
- Utenti che eseguono verifiche di accesso

Per informazioni su come assegnare licenze agli utenti, vedere [Assegnare o rimuovere licenze usando il portale di Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="what-happens-when-a-license-expires"></a>Che cosa accade quando una licenza scade?

Se una licenza Azure AD Premium P2, EMS E5 o di valutazione scade, le funzionalità di PIM non saranno più disponibili nella directory:

- Le assegnazioni dei ruoli permanenti ad Azure AD non saranno interessate.
- Il servizio PIM nel portale di Azure, nonché i cmdlet dell'API Graph e le interfacce PowerShell di PIM, non saranno più disponibili per gli utenti per l'attivazione di ruoli con privilegi, la gestione di accessi con privilegi o l'esecuzione di verifiche di accesso dei ruoli con privilegi.
- Le assegnazioni di ruoli idonei dei ruoli di Azure AD verranno rimosse poiché gli utenti non saranno più in grado di attivare ruoli con privilegi.
- Eventuali verifiche di accesso dei ruoli di Azure AD in corso termineranno e le impostazioni di configurazione di PIM verranno rimosse.
- PIM non invierà più messaggi di posta elettronica sulle modifiche apportate all'assegnazione di ruolo.

## <a name="next-steps"></a>Passaggi successivi

- [Iniziare a usare PIM](pim-getting-started.md)
- [Ruoli che non è possibile gestire in PIM](pim-roles.md)
