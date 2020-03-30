---
title: MFA o 2FA e gestione delle identità con privilegi - Azure AD Documenti Microsoft
description: Informazioni su come Azure Active Directory Privileged Identity Management (PIM) convalida l'autenticazione Multi-Factor Authentication (MFA).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6df593909c3ae5962e413eb84e64196fade0326b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022147"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>Autenticazione a più fattori e gestione delle identità con privilegi

È consigliabile richiedere l'autenticazione Multi-Factor Authentication (MFA) per tutti gli amministratori. Ciò consente di ridurre il rischio di attacchi causato da una password compromessa.

È possibile richiedere che gli utenti completino una richiesta di autenticazione a più fattori quando accedono. È anche possibile richiedere che gli utenti completino una richiesta di autenticazione a più fattori quando attivano un ruolo in Azure Active Directory (Azure AD) Privileged Identity Management (PIM). In questo modo, se l'utente non ha completato una richiesta di autenticazione a più fattori al momento dell'accesso, verrà richiesto di farlo da Gestione identità privilegiate.

> [!IMPORTANT]
> Al momento, Azure Multi-Factor Authentication funziona solo con account aziendali o dell'istituto di istruzione, non con account personali Microsoft (in genere un account personale usato per accedere a servizi Microsoft come Skype, Xbox o Outlook.com). Per questo motivo, chiunque utilizzi un account personale non può essere un amministratore idoneo perché non può utilizzare l'autenticazione a più fattori per attivare i propri ruoli. Se devono continuare a gestire i carichi di lavoro con un account Microsoft, elevare questi utenti a livello di amministratori permanenti per ora.

## <a name="how-pim-validates-mfa"></a>Modalità di convalida di MFA da parte di PIM

Sono disponibili due opzioni per la convalida dell'autenticazione a più fattori quando un utente attiva un ruolo.

L'opzione più semplice consiste nell'utilizzare Azure Multi-Factor Authentication per gli utenti che attivano un ruolo con privilegi. A tale scopo, verificare innanzitutto che tali utenti dispongano di una licenza, se necessario, e si siano registrati per Azure Multi-Factor Authentication.To do this, first check that those users are licensed, if necessary, and have registered for Azure Multi-Factor Authentication. Per altre informazioni su come distribuire Azure Multi-Factor Authentication, vedere [Distribuire Azure Multi-Factor Authentication basato su cloud.](../authentication/howto-mfa-getstarted.md) È consigliabile, ma non obbligatorio, configurare Azure AD per applicare l'autenticazione a più fattori per questi utenti al momento dell'accesso. Ciò è dovuto al fatto che i controlli di autenticazione a più fattori verranno eseguiti da Privileged Identity Management stesso.

In alternativa, se gli utenti eseguono l'autenticazione in locale, è possibile fare in modo che il provider di identità sia responsabile dell'autenticazione a più fattori. Ad esempio, se AD Federation Services è stato configurato per richiedere l'autenticazione basata su smart card prima dell'accesso ad Azure AD, vedere le istruzioni per la configurazione di AD FS per l'invio di attestazioni ad Azure AD nell'articolo [Protezione delle risorse cloud con Azure Multi-Factor Authentication e AD FS](../authentication/howto-mfa-adfs.md) . Quando un utente tenta di attivare un ruolo, Gestione identità con privilegi accetterà che l'autenticazione a più fattori sia già stata convalidata per l'utente dopo aver ricevuto le attestazioni appropriate.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le impostazioni dei ruoli di Azure AD in Gestione delle identità con privilegiConfigure Azure AD role settings in Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Configurare le impostazioni del ruolo delle risorse di Azure in Gestione delle identità con privilegiConfigure Azure resource role settings in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
