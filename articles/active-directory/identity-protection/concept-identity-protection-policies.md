---
title: Criteri di Azure AD Identity ProtectionAzure AD Identity Protection policies
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72887416"
---
# <a name="identity-protection-policies"></a>Criteri di Identity Protection

Azure Active Directory Identity Protection include tre criteri predefiniti che gli amministratori possono scegliere di abilitare. Questi criteri includono una personalizzazione limitata, ma sono applicabili alla maggior parte delle organizzazioni. Tutti i criteri consentono di escludere gli utenti, ad esempio [l'accesso di emergenza o gli account di amministratore di break-glass](../users-groups-roles/directory-emergency-access.md).

![Criteri di Identity Protection](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Criteri di registrazione di Azure MFAAzure MFA registration policy

La protezione delle identità può aiutare le organizzazioni a implementare Azure Multi-Factor Authentication (MFA) usando un criterio di accesso condizionale che richiede la registrazione all'accesso. L'abilitazione di questo criterio è un ottimo modo per garantire che i nuovi utenti dell'organizzazione si siano registrati per l'autenticazione a più fattori il primo giorno. L'autenticazione a più fattori è uno dei metodi di correzione automatica per gli eventi di rischio all'interno di Identity Protection. La correzione automatica consente agli utenti di intervenire autonomamente per ridurre il volume delle chiamate all'helpdesk.

Ulteriori informazioni su Azure Multi-Factor Authentication sono disponibili nell'articolo [Come funziona: Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md).

## <a name="sign-in-risk-policy"></a>Criteri di rischio di accesso

Identity Protection analizza i segnali provenienti da ogni accesso, sia in tempo reale che offline, e calcola un punteggio di rischio in base alla probabilità che l'accesso non sia stato eseguito dall'utente. Gli amministratori possono prendere una decisione in base a questo segnale di punteggio di rischio per applicare i requisiti organizzativi. Gli amministratori possono scegliere di bloccare l'accesso, consentire l'accesso o consentire l'accesso, ma richiedere l'autenticazione a più fattori.

Se viene rilevato un rischio, gli utenti possono eseguire l'autenticazione a più fattori per correggere autonomamente e chiudere l'evento di accesso rischioso per evitare rumori non necessari per gli amministratori.

> [!NOTE] 
> Gli utenti devono aver precedentemente registrato per Azure Multi-Factor Authentication prima di attivare i criteri di rischio di accesso.

### <a name="custom-conditional-access-policy"></a>Criteri di accesso condizionale personalizzatiCustom Conditional Access policy

Gli amministratori possono anche scegliere di creare criteri di accesso condizionale personalizzati, incluso il rischio di accesso come condizione di assegnazione. Ulteriori informazioni sull'accesso condizionale sono disponibili nell'articolo [Che cos'è l'accesso condizionale?](../conditional-access/overview.md)

![Criteri di rischio di accesso con accesso condizionale personalizzatoCustom Conditional Access sign-in risk policy](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Criteri di rischio utente

Identity Protection può calcolare ciò che ritiene normale per il comportamento di un utente e utilizzarlo per basare le decisioni per il rischio. Il rischio dell'utente è un calcolo della probabilità che un'identità sia stata compromessa. Gli amministratori possono prendere una decisione in base a questo segnale di punteggio di rischio per applicare i requisiti organizzativi. Gli amministratori possono scegliere di bloccare l'accesso, consentire l'accesso o consentire l'accesso, ma richiedere una modifica della password usando la [reimpostazione della password self-service](../authentication/howto-sspr-deployment.md)di Azure AD.

Se viene rilevato un rischio, gli utenti possono eseguire la reimpostazione della password self-service per correggere automaticamente e chiudere l'evento di rischio per l'utente per evitare rumori non necessari per gli amministratori.

> [!NOTE] 
> Gli utenti devono essere stati registrati in precedenza per la reimpostazione della password self-service prima di attivare i criteri di rischio utente.

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare la reimpostazione della password self-service di Azure ADEnable Azure AD self-service password reset](../authentication/howto-sspr-deployment.md)

- [Abilitare Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

- [Abilitare i criteri di registrazione di Azure Multi-Factor AuthenticationEnable Azure Multi-Factor Authentication registration policy](howto-identity-protection-configure-mfa-policy.md)

- [Abilitare i criteri di accesso e di rischio per gli utenti](howto-identity-protection-configure-risk-policies.md)
