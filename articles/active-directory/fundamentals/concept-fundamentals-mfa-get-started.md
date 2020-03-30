---
title: Azure Multi-Factor Authentication for your organization - Azure Active Directory
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
ms.openlocfilehash: febdb708c637ac322c0ca884eae627da9bd5904c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530394"
---
# <a name="overview-of-azure-multi-factor-authentication-for-your-organization"></a>Panoramica di Azure Multi-Factor Authentication per l'organizzazione

Esistono diversi modi per abilitare Azure Multi-Factor Authentication per gli utenti di Azure Active Directory (AD) in base alle licenze di proprietà dell'organizzazione. 

![Esaminare i segnali e applicare l'autenticazione a più fattori, se necessarioInvestigate signals and enforce MFA if needed](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Sulla base dei nostri studi, il tuo account ha più del 99,9% di probabilità in meno di essere compromesso se utilizzi l'autenticazione a più fattori (MFA).

Quindi, come attiva l'autenticazione a più fattori anche gratuitamente, prima di diventare una statistica?

## <a name="free-option"></a>Opzione gratuita

I clienti che utilizzano i vantaggi gratuiti di Azure AD possono usare [le impostazioni predefinite](../fundamentals/concept-fundamentals-security-defaults.md) di sicurezza per abilitare l'autenticazione a più fattori nel proprio ambiente.

## <a name="office-365-business-premium-e3-or-e5"></a>Office 365 Business Premium, E3 o E5

Per i clienti con Office 365, sono disponibili due opzioni:

* Azure Multi-Factor Authentication è abilitato o disabilitato per tutti gli utenti per tutti gli eventi di accesso. Non è possibile abilitare l'autenticazione a più fattori solo per un sottoinsieme di utenti o solo in determinati scenari. La gestione è tramite il portale di Office 365. 
* Per un'esperienza utente migliorata, eseguire l'aggiornamento ad Azure AD Premium P1 o P2 e usare l'accesso condizionale. Per altre informazioni, vedere proteggere le risorse di Office 365 con l'autenticazione a più fattori.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Per i clienti con Azure AD Premium P1 o licenze simili che includono questa funzionalità, ad esempio Enterprise Mobility - Security E3, Microsoft 365 F1 o Microsoft 365 E3: 

Usare [l'accesso condizionale](../conditional-access/overview.md) di Azure AD per richiedere agli utenti l'autenticazione a più fattori durante determinati scenari o eventi in base ai requisiti aziendali.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Per i clienti con Azure AD Premium P2 o licenze simili che includono questa funzionalità, ad esempio Enterprise Mobility - Security E5 o Microsoft 365 E5: 

Fornisce la posizione di sicurezza più forte e una migliore esperienza utente. Aggiunge [l'accesso condizionale basato sul rischio](../conditional-access/howto-conditional-access-policy-risk.md) alle funzionalità di Azure AD Premium P1 che si adatta ai modelli degli utenti e riduce al minimo le richieste di autenticazione a più fattori.

## <a name="authentication-methods"></a>Metodi di autenticazione

|   | Impostazioni predefinite di sicurezza | Tutti gli altri metodi |
| --- | --- | --- |
| Notifica tramite app per dispositivi mobili | X | X |
| Codice di verifica dall'app per dispositivi mobili o dal token hardware |   | X |
| SMS al telefono |   | X |
| Chiamata al telefono |   | X |

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, vedere l'esercitazione per [proteggere gli eventi di accesso degli utenti con Azure Multi-Factor Authentication](../authentication/tutorial-enable-azure-mfa.md).

Per altre informazioni sulle licenze, vedere [Funzionalità e licenze per Azure Multi-Factor Authentication.](../authentication/concept-mfa-licensing.md)
