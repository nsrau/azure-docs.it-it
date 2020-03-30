---
title: Esperienze utente con Azure AD Identity ProtectionUser experiences with Azure AD Identity Protection
description: Esperienza utente di Azure AD Identity Protection
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
ms.openlocfilehash: cc10fb4f9894a355c9eed024ae9f87747214999b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72887000"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>Esperienze utente con Azure AD Identity ProtectionUser experiences with Azure AD Identity Protection

Con Azure Active Directory Identity Protection è possibile:

* Richiedere agli utenti di registrarsi per Azure Multi-Factor Authentication (MFA)Require users to register for Azure Multi-Factor Authentication (MFA)
* Automatizzare la correzione di addicazioni rischiose e utenti compromessiAutomate remediation of risky sign-ins and compromised users

Tutti i criteri di protezione delle identità hanno un impatto sull'esperienza di accesso per gli utenti. Consentire agli utenti di registrarsi e usare strumenti come Azure MFA e la reimpostazione della password self-service può ridurre l'impatto. Questi strumenti, insieme alle scelte appropriate dei criteri, offrono agli utenti un'opzione di correzione automatica quando ne hanno bisogno.

## <a name="multi-factor-authentication-registration"></a>Registrazione per l'autenticazione a più fattori

Abilitando i criteri di protezione delle identità che richiedono la registrazione dell'autenticazione a più fattori e l'assegnazione di tutti gli utenti, si assicurerà che abbiano la possibilità di usare Azure MFA per correggere autonomamente in futuro. La configurazione di questo criterio offre agli utenti un periodo di 14 giorni in cui possono scegliere di registrarsi e alla fine sono costretti a registrarsi. L'esperienza per gli utenti è descritta di seguito. Ulteriori informazioni sono disponibili nella documentazione dell'utente finale nell'articolo Panoramica per la verifica a [due fattori e l'account aziendale o dell'istituto](../user-help/user-help-two-step-verification-overview.md)di istruzione .

### <a name="registration-interrupt"></a>Interruzione di registrazione

1. All'accesso a qualsiasi applicazione integrata di Azure AD, l'utente riceve una notifica sulla necessità di configurare l'account per l'autenticazione a più fattori. Questo criterio viene attivato anche in Windows 10 Out of Box Experience per i nuovi utenti con un nuovo dispositivo.
   
    ![Richiesta di altre informazioni](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. Completare i passaggi guida per la registrazione per Azure Multi-Factor Authentication e completare l'accesso.

## <a name="risky-sign-in-remediation"></a>Correzione dell'accesso rischioso

Quando un amministratore ha configurato un criterio per i rischi di accesso, gli utenti interessati ricevono una notifica quando tentano di accedere e attivare il livello di rischio dei criteri. 

### <a name="risky-sign-in-self-remediation"></a>Automonitoraggio dell'accesso rischioso

1. L’utente è informato del fatto che qualcosa di insolito è stato rilevato in merito al suo accesso, ad esempio l’accesso da una nuova posizione, dispositivo o app.
   
    ![Un prompt insolito](./media/concept-identity-protection-user-experience/120.png)

1. L'utente deve dimostrare la propria identità completando Azure MFA con uno dei metodi registrati in precedenza. 

### <a name="risky-sign-in-administrator-unblock"></a>Sblocco dell'amministratore dell'accesso rischioso

Gli amministratori possono scegliere di bloccare gli utenti al momento dell'accesso a seconda del livello di rischio. Per sbloccarlo, gli utenti finali devono contattare il personale IT oppure provare ad accedere da una posizione o un dispositivo familiare. In questo caso, l'autenticazione a più fattori mediante l'autenticazione a più fattori non è un'opzione.

![Bloccato dai criteri di rischio di accesso](./media/concept-identity-protection-user-experience/200.png)

Il personale IT può seguire le istruzioni riportate nella sezione [Sblocco degli utenti](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk) per consentire agli utenti di accedere di nuovo.

## <a name="risky-user-remediation"></a>Correzione rischiosa degli utenti

Quando è stato configurato un criterio di rischio utente, gli utenti che soddisfano la probabilità di compromissione del livello di rischio utente devono passare attraverso il flusso di ripristino di compromissione degli utenti prima di poter accedere. 

### <a name="risky-user-self-remediation"></a>Auto-correzione degli utenti rischiosi

1. L'utente viene informato che la sicurezza dell'account è a rischio a causa di attività sospette o di credenziali perse.
   
    ![Correzione](./media/concept-identity-protection-user-experience/101.png)

1. L'utente deve dimostrare la propria identità completando Azure MFA con uno dei metodi registrati in precedenza. 
1. Infine, l'utente è costretto a cambiare la password utilizzando la reimpostazione della password self-service poiché qualcun altro potrebbe aver avuto accesso al proprio account.

## <a name="risky-sign-in-administrator-unblock"></a>Sblocco dell'amministratore dell'accesso rischioso

Gli amministratori possono scegliere di bloccare gli utenti al momento dell'accesso a seconda del livello di rischio. Per sbloccarlo, gli utenti finali devono contattare il personale IT. In questo caso, l'autenticazione a più fattori e la reimpostazione della password self-service non sono un'opzione.

![Bloccato dai criteri di rischio per gli utenti](./media/concept-identity-protection-user-experience/104.png)

Il personale IT può seguire le istruzioni riportate nella sezione [Sblocco degli utenti](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk) per consentire agli utenti di accedere di nuovo.

## <a name="see-also"></a>Vedere anche

- [Correggere i rischi e sbloccare gli utenti](howto-identity-protection-remediate-unblock.md)

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) 
