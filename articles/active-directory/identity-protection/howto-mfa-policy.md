---
title: Come configurare i criteri di registrazione per l'autenticazione a più fattori in Azure Active Directory Identity Protection | Microsoft Docs
description: Informazioni su come configurare i criteri di registrazione per l'autenticazione a più fattori di Azure Active Directory Identity Protection.
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, gestione applicazioni, sicurezza, rischio, livello di rischio, vulnerabilità, criteri di sicurezza
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: ed1104d3e5ddf54657fb1f7b5c84a85ea855d95c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55148584"
---
# <a name="how-to-configure-the-multi-factor-authentication-registration-policy"></a>Procedura: Configurare i criteri di registrazione per l'autenticazione a più fattori

Azure AD Identity Protection permette di gestire il rollout della registrazione per l'autenticazione a più fattori (MFA) mediante la configurazione di criteri. Questo articolo illustra le modalità di configurazione e utilizzo dei criteri da adottare.

## <a name="what-is-the-multi-factor-authentication-registration-policy"></a>Cosa sono i criteri di registrazione per l'autenticazione a più fattori?

Azure Multi-Factor Authentication è un metodo di verifica dell'identità dell'utente che richiede l'uso di più fattori, oltre a un nome utente e una password. Fornisce un secondo livello di sicurezza agli accessi e alle transazioni degli utenti.  

È consigliabile richiedere l'autenticazione a più fattori per l'accesso degli utenti perché:

- Offre un'autenticazione avanzata con una gamma di opzioni di verifica semplici

- Svolge un ruolo chiave nella preparazione dell'organizzazione per le operazioni di protezione e ripristino in caso di compromissione degli account


Per altre informazioni, vedere la pagina relativa ad [Informazioni su Azure Multi-Factor Authentication](../authentication/multi-factor-authentication.md)


## <a name="how-do-i-access-the-mfa-registration-policy"></a>Come si accede ai criteri di registrazione MFA?
   
I criteri di registrazione MFA si trovano nella sezione di **configurazione** della [pagina di Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Criteri di MFA](./media/howto-mfa-policy/1014.png)




## <a name="policy-settings"></a>Impostazioni dei criteri

Quando si configurano i criteri di rischio di accesso, è necessario impostare:

- Gli utenti e i gruppi a cui vengono applicati i criteri:

    ![Utenti e gruppi](./media/howto-mfa-policy/11.png)

- Il tipo di accesso che si intende applicare:  

    ![Utenti e gruppi](./media/howto-mfa-policy/12.png)

- Lo stato dei criteri:

    ![Applicare i criteri](./media/howto-mfa-policy/14.png)


La finestra di dialogo di configurazione dei criteri include un'opzione che consente di stimare l'impatto della configurazione.

![Impatto stimato](./media/howto-mfa-policy/15.png)




## <a name="user-experience"></a>Esperienza utente


Per una panoramica dell'esperienza utente correlata, vedere:

* [Flusso di registrazione per l'autenticazione a più fattori](flows.md#multi-factor-authentication-registration).  
* [Esperienze di accesso con Azure AD Identity Protection](flows.md).  



## <a name="next-steps"></a>Passaggi successivi

Per ottenere una panoramica di Azure AD Identity Protection, vedere l'articolo di [panoramica di Azure AD Identity Protection](overview.md).
