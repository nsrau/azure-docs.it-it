---
title: Come configurare i criteri di registrazione per l'autenticazione a più fattori in Azure Active Directory Identity Protection | Microsoft Docs
description: Informazioni su come configurare i criteri di registrazione per l'autenticazione a più fattori di Azure Active Directory Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/01/2019
author: MicrosoftGuyJFlo
manager: daveba
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 939f08fd16cf27e641cf6436a00396ad2db8e6c3
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126382"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Procedura: Configurare i criteri di registrazione del Multi-Factor Authentication di Azure

Azure AD Identity Protection consente di gestire l'implementazione della registrazione dell'autenticazione a più fattori tramite la configurazione di un criterio di accesso condizionale per richiedere la registrazione a più fattori, indipendentemente dall'app di autenticazione moderna a cui si accede. Questo articolo illustra il modo in cui è possibile usare i criteri e come configurarlo.



## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Informazioni sui criteri di registrazione di Azure Multi-Factor Authentication

Azure Multi-Factor Authentication fornisce un mezzo per verificare chi sta usando più di un nome utente e una password. Fornisce un secondo livello di sicurezza agli accessi degli utenti. Affinché gli utenti siano in grado di rispondere alle richieste di autenticazione a più fattori, devono prima registrarsi per Multi-Factor Authentication di Azure.

Per gli accessi utente è consigliabile richiedere Azure Multi-Factor Authentication perché:

- Offre un'autenticazione avanzata con una gamma di opzioni di verifica semplici
- Gioca un ruolo fondamentale nella preparazione dell'organizzazione per la protezione e il ripristino da rilevamenti dei rischi in Identity Protection

Per altri dettagli sull'autenticazione a più fattori, vedere informazioni su [Azure multi-factor authentication](../authentication/howto-mfa-getstarted.md)

## <a name="how-do-i-access-the-registration-policy"></a>Ricerca per categorie accedere ai criteri di registrazione?

I criteri di registrazione MFA si trovano nella sezione di **configurazione** della [pagina di Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).

![Criteri di MFA](./media/howto-mfa-policy/1014.png)

## <a name="policy-settings"></a>Impostazioni di criteri

Quando si configurano i criteri di registrazione dell'autenticazione a più fattori, è necessario apportare le modifiche di configurazione seguenti:

- Utenti e gruppi a cui si applicano i criteri. Ricordarsi di escludere gli [account di accesso di emergenza](../users-groups-roles/directory-emergency-access.md)dell'organizzazione.

    ![Utenti e gruppi](./media/howto-mfa-policy/11.png)

- Il controllo che si vuole applicare: **richiedere la registrazione** dell'autenticazione a più fattori di Azure

    ![Accesso](./media/howto-mfa-policy/12.png)

- Imponi criterio deve essere impostato **su on**.

    ![Applicare i criteri](./media/howto-mfa-policy/14.png)

- **Salvare** i criteri

## <a name="user-experience"></a>Esperienza utente

Azure Active Directory Identity Protection chiederà agli utenti di effettuare la registrazione al successivo accesso interattivo.

Per una panoramica dell'esperienza utente correlata, vedere:

- [Flusso di registrazione per l'autenticazione a più fattori](flows.md#multi-factor-authentication-registration).  
- [Esperienze di accesso con Azure AD Identity Protection](flows.md).  

## <a name="next-steps"></a>Passaggi successivi

Per ottenere una panoramica di Azure AD Identity Protection, vedere l'articolo di [panoramica di Azure AD Identity Protection](overview.md).
