---
title: Configurare i criteri di registrazione dell'autenticazione a più fattori-Azure Active Directory Identity Protection
description: Informazioni su come configurare i criteri di registrazione per l'autenticazione a più fattori di Azure Active Directory Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 072db1d47abd95844075aeedfeddc4f8cf6bf936
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835867"
---
# <a name="how-to-configure-the-azure-ad-multi-factor-authentication-registration-policy"></a>Procedura: configurare i criteri di registrazione Multi-Factor Authentication Azure AD

Azure AD Identity Protection consente di gestire l'implementazione della registrazione del Multi-Factor Authentication di Azure AD (multi-factor authentication) configurando criteri di accesso condizionale per richiedere la registrazione a più fattori, a prescindere dall'app di autenticazione moderna a cui si accede.

## <a name="what-is-the-azure-ad-multi-factor-authentication-registration-policy"></a>Quali sono i criteri di registrazione di Azure AD Multi-Factor Authentication?

Azure AD Multi-Factor Authentication fornisce un mezzo per verificare chi utilizza più di un nome utente e una password. Fornisce un secondo livello di sicurezza agli accessi degli utenti. Affinché gli utenti siano in grado di rispondere alle richieste di autenticazione a più fattori, devono prima registrarsi per Azure AD Multi-Factor Authentication.

Si consiglia di richiedere Azure AD Multi-Factor Authentication per gli accessi utente perché:

- Offre autenticazione avanzata tramite una gamma di opzioni di verifica.
- Svolge un ruolo fondamentale nella preparazione dell'organizzazione per la correzione automatica dai rilevamenti dei rischi in Identity Protection.

Per ulteriori informazioni su Azure AD Multi-Factor Authentication, vedere [che cos'è Azure AD multi-factor authentication?](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>Configurazione dei criteri

1. Passare al [portale di Azure](https://portal.azure.com).
1. Individuare i criteri di registrazione dell'autenticazione a più fattori **Azure Active Directory**  >  **Security**  >  **Identity Protection**  >  **MFA registration policy**.
   1. In **assegnazioni**
      1. **Utenti** : scegliere **tutti gli utenti** o **selezionare singoli utenti e gruppi** in caso di limitazione dell'implementazione.
         1. Facoltativamente, è possibile scegliere di escludere gli utenti dai criteri.
   1. Sotto **controlli**
      1. Verificare che la casella di controllo **richiedi Azure ad registrazione** autenticazione a più fattori sia selezionata e scegliere **Seleziona**.
   1. **Imponi criteri**  -  **Il**
   1. **Salva**

## <a name="user-experience"></a>Esperienza utente

Azure Active Directory Identity Protection chiederà agli utenti di effettuare la registrazione al successivo accesso interattivo e avranno 14 giorni per completare la registrazione. Durante questo periodo di 14 giorni, è possibile ignorare la registrazione, ma al termine del periodo sarà necessario effettuare la registrazione prima di poter completare il processo di accesso.

Per una panoramica dell'esperienza utente correlata, vedere:

- [Esperienze di accesso con Azure AD Identity Protection](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare i criteri di rischio per accessi e utenti](howto-identity-protection-configure-risk-policies.md)

- [Abilitare la reimpostazione della password self-service di Azure AD](../authentication/howto-sspr-deployment.md)

- [Abilita Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
