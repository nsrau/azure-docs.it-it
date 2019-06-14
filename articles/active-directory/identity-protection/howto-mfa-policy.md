---
title: Come configurare i criteri di registrazione per l'autenticazione a più fattori in Azure Active Directory Identity Protection | Microsoft Docs
description: Informazioni su come configurare i criteri di registrazione per l'autenticazione a più fattori di Azure Active Directory Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.date: 05/01/2019
author: MicrosoftGuyJFlo
manager: daveba
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 434d07163713a139b42a5dbe1664f81dafc2a1ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108947"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Procedura: Configurare i criteri di registrazione di Azure multi-Factor Authentication

Azure AD Identity Protection consente di gestire il rollout della registrazione per l'autenticazione a più fattori (MFA) tramite la configurazione di criteri di accesso condizionale per richiedere la registrazione MFA indipendentemente da quali app di cui si accede a. Questo articolo illustra ciò che il criterio può essere utilizzato per e come configurarlo.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Che cos'è il criterio registrazione di Azure multi-Factor Authentication?

Azure multi-Factor Authentication fornisce un mezzo per verificare che usano più semplicemente un nome utente e password. Fornisce un secondo livello di sicurezza agli accessi utente. Affinché gli utenti siano in grado di rispondere alle richieste di autenticazione a più fattori, è prima necessario registrarsi per Azure multi-Factor Authentication.

È consigliabile richiedere Azure multi-Factor Authentication per l'accesso degli utenti perché è:

- Offre un'autenticazione avanzata con una gamma di opzioni di verifica semplici
- Svolge un ruolo fondamentale nella preparazione dell'organizzazione per proteggere e ripristinare da eventi di rischio di Identity Protection

Per ulteriori dettagli sull'autenticazione a più fattori, vedere [che cos'è Azure multi-Factor Authentication?](../authentication/howto-mfa-getstarted.md)

## <a name="how-do-i-access-the-registration-policy"></a>Come posso accedere i criteri di registrazione?

I criteri di registrazione MFA si trovano nella sezione di **configurazione** della [pagina di Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).

![Criteri di MFA](./media/howto-mfa-policy/1014.png)

## <a name="policy-settings"></a>Impostazioni dei criteri

Quando si configurano i criteri di registrazione MFA, è necessario apportare le modifiche di configurazione seguenti:

- Gli utenti e vengono applicati i criteri a gruppi. È necessario escludere l'organizzazione [gli account di accesso di emergenza](../users-groups-roles/directory-emergency-access.md).

    ![Utenti e gruppi](./media/howto-mfa-policy/11.png)

- Il controllo che si desidera applicare - **registrazione richiede MFA di Azure**

    ![Accesso](./media/howto-mfa-policy/12.png)

- Imporre criteri devono essere impostato su **su**.

    ![Applicare i criteri](./media/howto-mfa-policy/14.png)

- **Salvare** i criteri

## <a name="user-experience"></a>Esperienza utente

Per una panoramica dell'esperienza utente correlata, vedere:

- [Flusso di registrazione per l'autenticazione a più fattori](flows.md#multi-factor-authentication-registration).  
- [Esperienze di accesso con Azure AD Identity Protection](flows.md).  

## <a name="next-steps"></a>Passaggi successivi

Per ottenere una panoramica di Azure AD Identity Protection, vedere l'articolo di [panoramica di Azure AD Identity Protection](overview.md).
