---
title: Criteri di Azure AD Identity Protection
description: Identificazione dei tre criteri abilitati con Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a44e32efa3e38cf9c4df7dc00e3189c129db418
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887416"
---
# <a name="identity-protection-policies"></a>Criteri di Identity Protection

Azure Active Directory Identity Protection include tre criteri predefiniti che gli amministratori possono scegliere di abilitare. Questi criteri includono una personalizzazione limitata ma sono applicabili alla maggior parte delle organizzazioni. Tutti i criteri consentono di escludere gli utenti, ad esempio l' [accesso di emergenza o gli account amministratore break-Glass](../users-groups-roles/directory-emergency-access.md).

![Criteri di Identity Protection](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Criteri di registrazione autenticazione a più fattori di Azure

Identity Protection può aiutare le organizzazioni a implementare Azure Multi-Factor Authentication (multi-factor authentication) usando criteri di accesso condizionale che richiedono la registrazione all'accesso. L'abilitazione di questo criterio è un ottimo modo per garantire che i nuovi utenti dell'organizzazione abbiano effettuato la registrazione per l'autenticazione a più fattori nel primo giorno. L'autenticazione a più fattori è uno dei metodi di correzione automatica per gli eventi di rischio all'interno di Identity Protection. La correzione automatica consente agli utenti di agire autonomamente per ridurre il volume di chiamate del supporto tecnico.

Altre informazioni su Azure Multi-Factor Authentication sono disponibili nell'articolo [come funziona: azure multi-factor authentication](../authentication/concept-mfa-howitworks.md).

## <a name="sign-in-risk-policy"></a>Criteri di rischio di accesso

Identity Protection analizza i segnali di ogni accesso, sia in tempo reale che offline, e calcola un punteggio di rischio in base alla probabilità che l'accesso non sia stato eseguito dall'utente. Gli amministratori possono prendere una decisione in base a questo segnale di Punteggio di rischio per applicare i requisiti dell'organizzazione. Gli amministratori possono scegliere di bloccare l'accesso, consentire l'accesso o consentire l'accesso, ma richiedere l'autenticazione a più fattori.

Se viene rilevato un rischio, gli utenti possono eseguire l'autenticazione a più fattori per eseguire autonomamente la correzione e la chiusura dell'evento di accesso rischioso per evitare inutili rumori per gli amministratori.

> [!NOTE] 
> Prima di attivare i criteri di rischio di accesso, gli utenti devono essere registrati in precedenza per Multi-Factor Authentication di Azure.

### <a name="custom-conditional-access-policy"></a>Criteri di accesso condizionale personalizzati

Gli amministratori possono anche scegliere di creare criteri di accesso condizionale personalizzati, incluso il rischio di accesso come condizione di assegnazione. Altre informazioni sull'accesso condizionale sono disponibili nell'articolo relativo all' [accesso condizionale.](../conditional-access/overview.md)

![Criteri di rischio per l'accesso condizionale personalizzato](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Criteri di rischio utente

Identity Protection è in grado di calcolare ciò che ritiene sia normale per il comportamento di un utente e di usarlo per basare le decisioni relative al rischio. Il rischio utente è un calcolo della probabilità che un'identità sia stata compromessa. Gli amministratori possono prendere una decisione in base a questo segnale di Punteggio di rischio per applicare i requisiti dell'organizzazione. Gli amministratori possono scegliere di bloccare l'accesso, consentire l'accesso o consentire l'accesso, ma è necessario modificare la password usando [Azure ad la reimpostazione della password self-service](../authentication/howto-sspr-deployment.md).

Se viene rilevato un rischio, gli utenti possono eseguire la reimpostazione della password self-service per la correzione automatica e la chiusura dell'evento di rischio utente per evitare inutili rumori per gli amministratori.

> [!NOTE] 
> Prima di attivare i criteri di rischio utente, gli utenti devono essere registrati in precedenza per la reimpostazione della password self-service.

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare la reimpostazione della password self-service Azure AD](../authentication/howto-sspr-deployment.md)

- [Abilita Multi-Factor Authentication di Azure](../authentication/howto-mfa-getstarted.md)

- [Abilitare i criteri di registrazione di Azure Multi-Factor Authentication](howto-identity-protection-configure-mfa-policy.md)

- [Abilitare l'accesso e i criteri di rischio utente](howto-identity-protection-configure-risk-policies.md)
