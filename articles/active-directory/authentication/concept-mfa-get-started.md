---
title: Abilitare Multi-Factor Authentication per l'organizzazione-Azure Active Directory
description: Abilitare l'autenticazione a più fattori di Azure per l'organizzazione in base alla licenza
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84da00b7d6ef49fcdcc91bf6374d8c8f48a034da
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848698"
---
# <a name="enable-multi-factor-authentication-for-your-organization"></a>Abilitare Multi-Factor Authentication per l'organizzazione

Esistono diversi modi per abilitare Azure Multi-Factor Authentication (multi-factor authentication) per gli utenti di Azure Active Directory (AD) in base alle licenze di proprietà dell'organizzazione. 

![Esaminare i segnali e applicare l'autenticazione a più fattori se necessario](./media/concept-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

In base ai nostri studi, il tuo account è più del 99,9% meno probabile che venga compromesso se usi l'autenticazione a più fattori.

Quindi, in che modo l'organizzazione attiva la funzionalità di autenticazione a più fattori anche gratuitamente, prima di diventare una statistica?

## <a name="free-option"></a>Opzione gratuita

I clienti che utilizzano i vantaggi gratuiti di Azure AD possono utilizzare le [impostazioni predefinite di sicurezza](../conditional-access/concept-conditional-access-security-defaults.md) per abilitare l'autenticazione a più fattori nel proprio ambiente.

## <a name="office-365"></a>Office 365

Per i clienti con Office 365, sono disponibili due opzioni:

- Le [impostazioni predefinite di sicurezza](../conditional-access/concept-conditional-access-security-defaults.md) possono essere abilitate tramite Azure ad per proteggere tutti gli utenti con multi-factor authentication di Azure.
- Se l'organizzazione richiede una maggiore granularità per la fornitura di multi-factor authentication, le licenze di Office includono funzionalità di autenticazione a più fattori [per utente](../authentication/howto-mfa-userstates.md) . L'autenticazione a più fattori per utente è abilitata e applicata a ogni singolo utente dagli amministratori.

## <a name="azure-ad-premium-p1"></a>Azure AD P1 Premium

Per i clienti con Azure AD Premium P1 o licenze simili che includono questa funzionalità, ad esempio Enterprise Mobility + Security E3, Microsoft 365 F1 o Microsoft 365 E3: 

Si consiglia di usare i [criteri di accesso condizionale](../conditional-access/concept-conditional-access-policy-common.md) per ottimizzare l'esperienza utente.

## <a name="azure-ad-premium-p2"></a>Azure AD P2 Premium

Per i clienti con Azure AD Premium P2 o licenze simili che includono questa funzionalità, ad esempio Enterprise Mobility + Security E5 o Microsoft 365 E5: 

Si consiglia di usare i [criteri di accesso condizionale](../conditional-access/concept-conditional-access-policy-common.md) insieme ai criteri di rischio per la [protezione delle identità](../identity-protection/overview-v2.md) per ottimizzare l'esperienza utente e la flessibilità di applicazione.

## <a name="authentication-methods"></a>Metodi di autenticazione

|   | Impostazioni predefinite di sicurezza | Tutti gli altri metodi |
| --- | --- | --- |
| Notifica tramite app per dispositivi mobili | X | X |
| Codice di verifica dall'app per dispositivi mobili o dal token hardware |   | X |
| SMS al telefono |   | X |
| Chiamata al telefono |   | X |
| Password app |   | X * * |

\* * Le password dell'app sono disponibili solo nell'autenticazione a più fattori per utente con scenari di autenticazione legacy solo se abilitati dagli amministratori.

## <a name="next-steps"></a>Passaggi successivi

[Pagina dei prezzi di Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
