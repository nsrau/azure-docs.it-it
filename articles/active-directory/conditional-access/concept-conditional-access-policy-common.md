---
title: Criteri di accesso condizionale comuni - Azure Active DirectoryCommon Conditional Access policies - Azure Active Directory
description: Criteri di accesso condizionale comunemente utilizzati per le organizzazioniCommonly used Conditional Access policies for organizations
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20b699f0672b49dd2f947e0cf00d0ffcef7961e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295297"
---
# <a name="common-conditional-access-policies"></a>Criteri comuni di accesso condizionale

[Le impostazioni predefinite](../fundamentals/concept-fundamentals-security-defaults.md) di sicurezza sono ottime per alcune organizzazioni, ma molte organizzazioni hanno bisogno di maggiore flessibilità rispetto a quella offerta. Ad esempio, molti hanno bisogno della possibilità di escludere account specifici come l'accesso di emergenza o gli account di amministrazione break-glass dai criteri di accesso condizionale che richiedono l'autenticazione a più fattori. Per tali organizzazioni, i criteri comuni a cui si fa riferimento in questo articolo possono essere utilizzabili.

![Criteri di accesso condizionale nel portale di AzureConditional Access policies in the Azure portal](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Account di accesso di emergenza

Ulteriori informazioni sugli account di accesso di emergenza e perché sono importanti sono disponibili nei seguenti articoli: 

* [Gestire gli account di accesso di emergenza in Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Creare una strategia di gestione di controllo di accesso resiliente con Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Criteri tipici distribuiti dalle organizzazioniTypical policies deployed by organizations

* [Bloccare l'autenticazione legacyBlock legacy authentication](howto-conditional-access-policy-block-legacy.md)\*
* [Richiedere l'autenticazione a più fattori per gli amministratoriRequire MFA for administrators](howto-conditional-access-policy-admin-mfa.md)\*
* [Richiedere l'autenticazione a più fattori per la gestione di AzureRequire MFA for Azure management](howto-conditional-access-policy-azure-management.md)\*
* [Richiedere l'autenticazione a più fattori per tutti gli utentiRequire MFA for all users](howto-conditional-access-policy-all-users-mfa.md)\*

\*Questi quattro criteri, se configurati insieme, rilevono le funzionalità abilitate dai valori predefiniti di [sicurezza.](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="additional-policies"></a>Criteri aggiuntivi

* [Accesso condizionale basato sul rischio (richiede Azure AD Premium P2)](howto-conditional-access-policy-risk.md)
* [Richiedere una località attendibile per la registrazione dell'autenticazione a più fattori](howto-conditional-access-policy-registration.md)
* [Bloccare l'accesso in base alla località](howto-conditional-access-policy-location.md)
* [Richiedere un dispositivo conforme](howto-conditional-access-policy-compliant-device.md)
* [Bloccare l'accesso tranne app specifiche](howto-conditional-access-policy-block-access.md)

## <a name="next-steps"></a>Passaggi successivi

- [Simulare il comportamento di accesso utilizzando lo strumento Elementi di ricerca condizionali.](troubleshoot-conditional-access-what-if.md)

- [Utilizzare la modalità solo report per l'accesso condizionale per determinare l'impatto delle nuove decisioni relative ai criteri.](concept-conditional-access-report-only.md)
