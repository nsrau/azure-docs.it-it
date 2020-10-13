---
title: Azure Multi-Factor Authentication per l'organizzazione - Azure Active Directory
description: Informazioni sulle funzionalità disponibili di Azure Multi-Factor Authentication per l'organizzazione in base al modello di licenza
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13bb7782f6396466200fbb3e2df77158bb75d8a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90705402"
---
# <a name="overview-of-azure-multi-factor-authentication-for-your-organization"></a>Panoramica di Azure Multi-Factor Authentication per l'organizzazione

Esistono diversi modi per abilitare Azure Multi-Factor Authentication per gli utenti di Azure Active Directory (AD) in base alle licenze di proprietà dell'organizzazione. 

![Esaminare i segnali e applicare l'autenticazione a più fattori se necessario](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

In base ai nostri studi, esiste oltre il 99,9% di possibilità in meno che un account venga compromesso se si usa l'autenticazione a più fattori (MFA).

Quindi, in che modo l'organizzazione attiva l'autenticazione a più fattori anche gratuitamente, prima di diventare una statistica?

## <a name="free-option"></a>Opzione gratuita

I clienti che usano i vantaggi gratuiti di Azure AD possono usare le [impostazioni predefinite per la sicurezza](../fundamentals/concept-fundamentals-security-defaults.md) per abilitare l'autenticazione a più fattori nel proprio ambiente.

## <a name="microsoft-365-business-e3-or-e5"></a>Microsoft 365 Business, E3 o E5

Per i clienti con Microsoft 365, sono disponibili due opzioni:

* Azure Multi-Factor Authentication è abilitato o disabilitato per tutti gli utenti, per tutti gli eventi di accesso. Non è possibile abilitare l'autenticazione a più fattori solo per un subset di utenti o solo in determinati scenari. La gestione avviene tramite il portale di Office 365. 
* Per migliorare l'esperienza utente, eseguire l'aggiornamento ad Azure AD Premium P1 o P2 e usare l'Accesso condizionale. Per altre informazioni, vedere proteggere le risorse di Microsoft 365 con l'autenticazione a più fattori.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Per i clienti con Azure AD Premium P1 o licenze simili che includono questa funzionalità, ad esempio Enterprise Mobility + Security E3, Microsoft 365 F1 o Microsoft 365 E3: 

usare [Accesso condizionale di Azure AD](../authentication/tutorial-enable-azure-mfa.md) per richiedere agli utenti di eseguire l'autenticazione a più fattori durante determinati scenari o eventi in base alle esigenze aziendali.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Per i clienti con Azure AD Premium P2 o licenze simili che includono questa funzionalità, ad esempio Enterprise Mobility + Security E5 o Microsoft 365 E5: 

offre la posizione di sicurezza più avanzata e l'esperienza utente migliorata. aggiunge l'[accesso condizionale basato sul rischio](../conditional-access/howto-conditional-access-policy-risk.md) alle funzionalità di Azure AD Premium P1 che si adattano ai modelli dell'utente e riduce al minimo le richieste di autenticazione a più fattori.

## <a name="authentication-methods"></a>Metodi di autenticazione

| Metodo | Impostazioni predefinite di sicurezza | Tutti gli altri metodi |
| --- | --- | --- |
| Notifica tramite app per dispositivi mobili | X | X |
| Codice di verifica dall'app per dispositivi mobili o dal token hardware |   | X |
| SMS al telefono |   | X |
| Chiamata al telefono |   | X |

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, guardare il tutoria su come [proteggere gli eventi di accesso con Azure Multi-Factor Authentication](../authentication/tutorial-enable-azure-mfa.md).

Per altre informazioni sulle licenze, vedere [Funzionalità e licenze per Azure Multi-Factor Authentication](../authentication/concept-mfa-licensing.md).
