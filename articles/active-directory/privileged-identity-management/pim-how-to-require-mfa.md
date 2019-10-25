---
title: Multi-factor authentication (autenticazione a più fattori) e Privileged Identity Management-Azure Active Directory | Microsoft Docs
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
ms.date: 08/31/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12b6cd17fac77361fa20d3b3e048e64228a2ed3d
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809019"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>Autenticazione a più fattori e Privileged Identity Management

È consigliabile richiedere l'autenticazione Multi-Factor Authentication (MFA) per tutti gli amministratori. Ciò consente di ridurre il rischio di attacchi causato da una password compromessa.

È possibile richiedere agli utenti di completare una richiesta di autenticazione a più fattori quando eseguono l'accesso. È anche possibile richiedere che gli utenti completino una richiesta di autenticazione a più fattori quando attivano un ruolo in Azure Active Directory (Azure AD) Privileged Identity Management (PIM). In questo modo, se l'utente non ha completato una richiesta di autenticazione a più fattori di autenticazione quando ha eseguito l'accesso, verrà richiesto di eseguire questa operazione dal Privileged Identity Management.

> [!IMPORTANT]
> Attualmente, Azure Multi-Factor Authentication funziona solo con gli account aziendali o dell'Istituto di istruzione, non con gli account personali Microsoft (in genere un account personale usato per accedere ai servizi Microsoft, ad esempio Skype, Xbox o Outlook.com). Per questo motivo, chiunque usi un account personale non può essere un amministratore idoneo perché non può usare l'autenticazione a più fattori per attivare i ruoli. Se devono continuare a gestire i carichi di lavoro con un account Microsoft, elevare questi utenti a livello di amministratori permanenti per ora.

## <a name="how-pim-validates-mfa"></a>Modalità di convalida di MFA da parte di PIM

Sono disponibili due opzioni per la convalida dell'autenticazione a più fattori quando un utente attiva un ruolo.

L'opzione più semplice consiste nell'utilizzare Multi-Factor Authentication di Azure per gli utenti che attivano un ruolo con privilegi. A tale scopo, verificare prima di tutto che questi utenti siano provvisti di licenza, se necessario, e abbiano effettuato la registrazione per Azure Multi-Factor Authentication. Per altre informazioni su come distribuire Multi-Factor Authentication di Azure, vedere [distribuire azure multi-factor authentication basato sul cloud](../authentication/howto-mfa-getstarted.md). È consigliabile, ma non obbligatorio, configurare Azure AD per applicare l'autenticazione a più fattori per questi utenti al momento dell'accesso. Ciò è dovuto al fatto che i controlli di autenticazione a più fattori verranno effettuati dal Privileged Identity Management stesso.

In alternativa, se gli utenti eseguono l'autenticazione in locale, il provider di identità può essere responsabile dell'autenticazione a più fattori. Ad esempio, se AD Federation Services è stato configurato per richiedere l'autenticazione basata su smart card prima dell'accesso ad Azure AD, vedere le istruzioni per la configurazione di AD FS per l'invio di attestazioni ad Azure AD nell'articolo [Protezione delle risorse cloud con Azure Multi-Factor Authentication e AD FS](../authentication/howto-mfa-adfs.md) . Quando un utente tenta di attivare un ruolo, Privileged Identity Management accetta che l'autenticazione a più fattori sia già stata convalidata per l'utente dopo la ricezione delle attestazioni appropriate.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le impostazioni del ruolo Azure AD in Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Configurare le impostazioni del ruolo delle risorse di Azure in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
