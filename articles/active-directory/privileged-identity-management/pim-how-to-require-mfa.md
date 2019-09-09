---
title: Multi-factor authentication (AMF) e PIM-Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: 686c39c9fb1f1ff7c0ecf068a6612f530620d5dc
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804307"
---
# <a name="multi-factor-authentication-mfa-and-pim"></a>Autenticazione Multi-Factor Authentication (MFA) e PIM

È consigliabile richiedere l'autenticazione Multi-Factor Authentication (MFA) per tutti gli amministratori. Ciò consente di ridurre il rischio di attacchi causato da una password compromessa.

È possibile richiedere agli utenti di compilare una richiesta per l'autenticazione MFA durante l'accesso. È anche possibile richiedere agli utenti di completare una richiesta di autenticazione a più fattori quando attivano un ruolo in Azure Active Directory (Azure AD) Privileged Identity Management (PIM). In questo modo, se l'utente non ha compilato una richiesta per l'autenticazione MFA durante l'accesso, PIM richiederà all'utente di eseguire tale operazione.

> [!IMPORTANT]
> Azure Multi-Factor Authentication (MFA) funziona attualmente solo con account aziendali o dell'istituto di istruzione, non con account Microsoft (account personale usato in genere per l'accesso a servizi Microsoft, come Skype, Xbox e Outlook.com). Gli utenti che usano un account Microsoft non possono essere amministratori idonei poiché non possono usare l'autenticazione MFA per attivare i propri ruoli. Se devono continuare a gestire i carichi di lavoro con un account Microsoft, elevare questi utenti a livello di amministratori permanenti per ora.

## <a name="how-pim-validates-mfa"></a>Modalità di convalida di MFA da parte di PIM

Sono disponibili due opzioni per la convalida di MFA quando un utente attiva un ruolo.

L'opzione più semplice consiste nell'usare Azure MFA per gli utenti che attivano un ruolo con privilegi. A tale scopo, verificare prima di tutto che gli utenti abbiano una licenza, se necessario, e che abbiano effettuato la registrazione per Azure MFA. Per altre informazioni su come implementare Azure MFA, vedere [Implementare Azure Multi-Factor Authentication basato su cloud](../authentication/howto-mfa-getstarted.md). È consigliabile, ma non obbligatorio, configurare Azure AD in modo da rendere obbligatoria l'autenticazione MFA per questi utenti durante l'accesso. Le verifiche di MFA vengono infatti eseguite direttamente da PIM.

In alternativa, se gli utenti eseguono l'autenticazione in locale, è possibile assegnare al provider di identità la responsabilità dell'autenticazione MFA. Ad esempio, se AD Federation Services è stato configurato per richiedere l'autenticazione basata su smart card prima dell'accesso ad Azure AD, vedere le istruzioni per la configurazione di AD FS per l'invio di attestazioni ad Azure AD nell'articolo [Protezione delle risorse cloud con Azure Multi-Factor Authentication e AD FS](../authentication/howto-mfa-adfs.md) . Quando un utente prova ad attivare un ruolo, PIM accetta il fatto che l'autenticazione MFA è già stata convalidata per l'utente alla ricezione delle attestazioni appropriate.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le impostazioni del ruolo Azure AD in PIM](pim-how-to-change-default-settings.md)
- [Configurare le impostazioni dei ruoli delle risorse di Azure in PIM](pim-resource-roles-configure-role-settings.md)
