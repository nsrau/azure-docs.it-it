---
title: Criteri di Azure Active Directory Identity Protection
description: Identificazione dei tre criteri abilitati con Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7587ed6d414a69cff67aca9446aebf6260c99fcd
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83736501"
---
# <a name="identity-protection-policies"></a>Criteri di Identity Protection

Azure Active Directory Identity Protection include tre criteri predefiniti che gli amministratori possono scegliere di abilitare. Questi criteri includono una personalizzazione limitata ma sono applicabili alla maggior parte delle organizzazioni. Tutti i criteri consentono di escludere gli utenti, ad esempio gli [account di accesso di emergenza o gli account amministratore critici](../users-groups-roles/directory-emergency-access.md).

![Criteri di Identity Protection](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Criteri di registrazione Azure MFA

Identity Protection consente alle organizzazioni di implementare Azure Multi-Factor Authentication (MFA) usando criteri di accesso condizionale che richiedono la registrazione al momento dell'accesso. L'abilitazione di questi criteri è un ottimo modo per garantire che i nuovi utenti dell'organizzazione abbiano effettuato la registrazione per MFA il primo giorno. L'autenticazione a più fattori è uno dei metodi di correzione automatica per gli eventi di rischio all'interno di Identity Protection. La correzione automatica consente agli utenti di agire autonomamente per ridurre il volume di chiamate del supporto tecnico.

Per altre informazioni su Azure Multi-Factor Authentication, vedere l'articolo [Funzionamento di Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md).

## <a name="sign-in-risk-policy"></a>Criteri di rischio di accesso

Identity Protection analizza i segnali di ogni accesso, sia in tempo reale che offline, e calcola un punteggio di rischio in base alla probabilità che l'accesso non sia stato eseguito dall'utente. Gli amministratori possono prendere una decisione in base al punteggio di rischio per applicare i requisiti dell'organizzazione. Gli amministratori possono scegliere di bloccare l'accesso, consentire l'accesso o consentire l'accesso ma richiedere l'autenticazione a più fattori.

Se viene rilevato un rischio, gli utenti possono eseguire l'autenticazione a più fattori per eseguire autonomamente la correzione e chiudere l'evento di accesso rischioso per evitare inutili problemi per gli amministratori.

> [!NOTE] 
> Prima di attivare i criteri di rischio di accesso, è necessario che gli utenti abbiano già effettuato la registrazione per Azure Multi-Factor Authentication.

### <a name="custom-conditional-access-policy"></a>Criteri di accesso condizionale personalizzati

Gli amministratori possono anche scegliere di creare criteri di accesso condizionale personalizzati che includono il rischio di accesso come condizione di assegnazione. Altre informazioni sul rischio come condizione nei criteri di accesso condizionale sono disponibili nell'articolo [Accesso condizionale: Condizioni](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk)

![Criteri di rischio di accesso condizionale personalizzati](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Criteri di rischio utente

Identity Protection è in grado di calcolare ciò che ritiene sia normale per il comportamento di un utente e di usarlo per basare le decisioni relative al rischio. Il rischio utente è un calcolo della probabilità che un'identità sia stata compromessa. Gli amministratori possono prendere una decisione in base al punteggio di rischio per applicare i requisiti dell'organizzazione. Gli amministratori possono scegliere di bloccare l'accesso, consentire l'accesso o consentire l'accesso e richiedere una modifica della password usando la [reimpostazione della password self-service di Azure AD](../authentication/howto-sspr-deployment.md).

Se viene rilevato un rischio, gli utenti possono eseguire la reimpostazione della password self-service per eseguire autonomamente la correzione e chiudere l'evento di rischio utente per evitare inutili problemi per gli amministratori.

> [!NOTE] 
> Prima di attivare i criteri di rischio utente, è necessario che gli utenti abbiano prima effettuato la registrazione per la reimpostazione della password self-service.

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare la reimpostazione della password self-service di Azure AD](../authentication/howto-sspr-deployment.md)

- [Abilitare Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

- [Abilitare i criteri di registrazione per l'autenticazione a più fattori di Azure](howto-identity-protection-configure-mfa-policy.md)

- [Abilitare i criteri di rischio di accesso e utente](howto-identity-protection-configure-risk-policies.md)
