---
title: How it works Azure MFA - Azure Active Directory
description: Azure Multi-Factor Authentication consente di proteggere l'accesso ai dati e alle applicazioni dell'utente, garantendo al tempo stesso una procedura di accesso semplice.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1036d7e8aef29e3185452d5088e660d474726e4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381962"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Funzionamento di Azure Multi-Factor Authentication

La sicurezza della verifica in due passaggi sta nel suo approccio a livelli. La manomissione di più fattori rappresenta una sfida significativa per gli autori di attacchi. Tuttavia, anche se un autore di un attacco riesce a ottenere la password dell'utente, questa risulta inutile se non è in possesso del metodo di autenticazione aggiuntivo. In genere richiede due o più dei metodi di autenticazione seguenti:

* Un'informazione nota (in genere una password)
* Un oggetto che si possiede (un dispositivo attendibile non facile da duplicare, ad esempio un telefono)
* Una caratteristica fisica dell'utente (biometrica)

<center>

![Conceptual authentication methods image](./media/concept-mfa-howitworks/methods.png)</center>

Azure Multi-Factor Authentication consente di proteggere l'accesso ai dati e alle applicazioni, garantendo al tempo stesso un utilizzo semplice agli utenti. Offre ulteriore sicurezza richiedendo una seconda forma di autenticazione nonché un'autenticazione avanzata tramite una gamma di [metodi di autenticazione semplici](concept-authentication-methods.md). Gli utenti possono o meno visualizzare una richiesta di autenticazione MFA in base alle decisioni di configurazione prese da un amministratore.

## <a name="how-to-get-multi-factor-authentication"></a>Come ottenere Multi-Factor Authentication

Multi-Factor Authentication è inclusa in quanto parte delle offerte seguenti:

* **Azure Active Directory Premium** or **Microsoft 365 Business** - Full featured use of Azure Multi-Factor Authentication using Conditional Access policies to require multi-factor authentication.

* **Azure AD Free** or standalone **Office 365** licenses - Use pre-created [Conditional Access baseline protection policies](../conditional-access/concept-baseline-protection.md) to require multi-factor authentication for your users and administrators.

* **Amministratori globali di Azure Active Directory**: per proteggere gli account di amministratore globale è disponibile un subset di funzionalità di Azure multi-Factor Authentication.

> [!NOTE]
> I nuovi clienti non possono più acquistare Azure Multi-Factor Authentication come offerta autonoma valida dal 1 ° settembre 2018. L'autenticazione a più fattori continuerà a essere una funzionalità disponibile nelle licenze di Azure AD Premium.

## <a name="supportability"></a>Supporto

Poiché la maggior parte degli utenti è abituata a usare le password solo per l'autenticazione, è importante che l'organizzazione si metta in comunicazione con tutti gli utenti per informarli in merito al processo. La consapevolezza può ridurre la probabilità che gli utenti contattino l'help desk per problemi di lieve entità riguardanti l'autenticazione a più fattori. Esistono tuttavia alcuni scenari in cui è necessario disabilitare temporaneamente MFA. Per sapere come gestire questi scenari, usare le linee guida seguenti:

* Istruire il personale di supporto per le situazioni in cui l'utente non riesce ad accedere perché non ha accesso ai suoi metodi di autenticazione oppure perchè questi ultimi non funzionano correttamente.
   * Using Conditional Access policies for Azure MFA Service, your support staff can add a user to a group that is excluded from a policy requiring MFA.
* Consider using Conditional Access named locations as a way to minimize two-step verification prompts. With this functionality, administrators can bypass two-step verification for users that are signing in from a secure trusted network location such as a network segment used for new user onboarding.
* Deploy [Azure AD Identity Protection](../active-directory-identityprotection.md) and trigger two-step verification based on risk detections.

## <a name="next-steps"></a>Passaggi successivi

- [Step-by-step Azure Multi-Factor Authentication deployment](howto-mfa-getstarted.md)
