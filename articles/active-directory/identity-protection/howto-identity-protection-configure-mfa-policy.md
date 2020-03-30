---
title: Configurare i criteri di registrazione dell'autenticazione a più fattori - Azure Active Directory Identity ProtectionConfigure the MFA registration policy - Azure Active Directory Identity Protection
description: Informazioni su come configurare i criteri di registrazione per l'autenticazione a più fattori di Azure Active Directory Identity Protection.
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
ms.openlocfilehash: 2fa6a4cf184b426355f62117ea51642127eee529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382137"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Procedura: Configurare i criteri di registrazione di Azure Multi-Factor AuthenticationHow To: Configure the Azure Multi-Factor Authentication registration policy

Azure AD Identity Protection consente di gestire l'implementazione della registrazione di Azure Multi-Factor Authentication (MFA) configurando criteri di accesso condizionale per richiedere la registrazione dell'autenticazione a più fattori indipendentemente dall'app di autenticazione moderna a cui si accede.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Che cos'è il criterio di registrazione di Azure Multi-Factor Authentication?

Azure Multi-Factor Authentication consente di verificare chi si usa più di un nome utente e una password. Fornisce un secondo livello di sicurezza agli accessi degli utenti. In order for users to be able to respond to MFA prompts, they must first register for Azure Multi-Factor Authentication.

È consigliabile richiedere Azure Multi-Factor Authentication per gli accessi degli utenti perché:We recommend that you require Azure Multi-Factor Authentication for user signing ins because it:

- Fornisce un'autenticazione avanzata attraverso una serie di opzioni di verifica.
- Svolge un ruolo chiave nella preparazione dell'organizzazione per la correzione automatica dei rilevamenti dei rischi in Protezione identità.

Per altre informazioni su Azure Multi-Factor Authentication, vedere [Che cos'è Azure Multi-Factor Authentication?](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>Configurazione dei criteri

1. Passare al [portale di Azure](https://portal.azure.com).
1. Passare ai criteri di**registrazione dell'autenticazione**a più problemi di **Azure Active Directory** > **Security** > **Identity Protection** > .
   1. In **Assegnazioni**
      1. **Utenti:** scegliere **Tutti gli utenti** o Seleziona individui e **gruppi** se si limita l'implementazione.
         1. Facoltativamente, è possibile scegliere di escludere gli utenti dal criterio.
   1. In **Controlli**
      1. Verificare che la casella di controllo **Richiedi registrazione di Azure MFA** sia selezionata e scegliere **Seleziona**.
   1. **Applica criteri** - **su**
   1. **Salva**

## <a name="user-experience"></a>Esperienza utente

Azure Active Directory Identity Protection richiederà agli utenti di registrarsi al successivo accesso interattivo e avranno 14 giorni per completare la registrazione. Durante questo periodo di 14 giorni, possono ignorare la registrazione, ma alla fine del periodo sarà richiesto di registrarsi prima di poter completare il processo di accesso.

Per una panoramica dell'esperienza utente correlata, vedere:

- [Esperienze di accesso con Azure AD Identity Protection](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare i criteri di accesso e di rischio per gli utenti](howto-identity-protection-configure-risk-policies.md)

- [Abilitare la reimpostazione della password self-service di Azure ADEnable Azure AD self-service password reset](../authentication/howto-sspr-deployment.md)

- [Abilitare Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
