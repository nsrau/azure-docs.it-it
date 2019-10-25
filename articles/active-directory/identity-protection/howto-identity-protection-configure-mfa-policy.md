---
title: Come configurare i criteri di registrazione di autenticazione a più fattori in Azure Active Directory Identity Protection
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
ms.openlocfilehash: bc98f645c6b24069e090560a049ccb4fcd03dfec
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887572"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Procedura: configurare i criteri di registrazione del Multi-Factor Authentication di Azure

Azure AD Identity Protection consente di gestire l'implementazione della registrazione di Azure Multi-Factor Authentication (multi-factor authentication) configurando criteri di accesso condizionale per richiedere la registrazione a più fattori, a prescindere dall'app di autenticazione moderna a cui si accede.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Informazioni sui criteri di registrazione di Azure Multi-Factor Authentication

Azure Multi-Factor Authentication fornisce un mezzo per verificare chi sta usando più di un nome utente e una password. Fornisce un secondo livello di sicurezza agli accessi degli utenti. Affinché gli utenti siano in grado di rispondere alle richieste di autenticazione a più fattori, devono prima registrarsi per Multi-Factor Authentication di Azure.

Per gli accessi utente è consigliabile richiedere Azure Multi-Factor Authentication perché:

- Offre autenticazione avanzata tramite una gamma di opzioni di verifica.
- Svolge un ruolo fondamentale nella preparazione dell'organizzazione per la correzione automatica dai rilevamenti dei rischi in Identity Protection.

Per altre informazioni su Azure Multi-Factor Authentication, vedere [che cos'è azure multi-factor authentication?](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>Configurazione dei criteri

1. Passare al [portale di Azure](https://portal.azure.com).
1. Passare a **Azure Active Directory** > **sicurezza** > **Identity Protection** > **criteri di registrazione**dell'autenticazione a più fattori.
   1. In **assegnazioni**
      1. **Utenti** : scegliere **tutti gli utenti** o **selezionare singoli utenti e gruppi** in caso di limitazione dell'implementazione.
         1. Facoltativamente, è possibile scegliere di escludere gli utenti dai criteri.
   1. Sotto **controlli**
      1. Verificare che la casella di controllo **Richiedi registrazione** autenticazione a più fattori di Azure sia selezionata e scegliere **Seleziona**.
   1. **Imponi - criteri** **in**
   1. **Salva**

## <a name="user-experience"></a>Esperienza utente

Azure Active Directory Identity Protection chiederà agli utenti di effettuare la registrazione al successivo accesso interattivo e avranno 14 giorni per completare la registrazione. Durante questo periodo di 14 giorni, è possibile ignorare la registrazione, ma al termine del periodo sarà necessario effettuare la registrazione prima di poter completare il processo di accesso.

Per una panoramica dell'esperienza utente correlata, vedere:

- [Esperienze di accesso con Azure AD Identity Protection](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare l'accesso e i criteri di rischio utente](howto-identity-protection-configure-risk-policies.md)

- [Abilitare la reimpostazione della password self-service Azure AD](../authentication/howto-sspr-deployment.md)

- [Abilita Multi-Factor Authentication di Azure](../authentication/howto-mfa-getstarted.md)
