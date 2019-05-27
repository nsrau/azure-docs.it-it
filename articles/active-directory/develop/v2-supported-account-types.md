---
title: Account supportati nelle app (destinatari) - Microsoft Identity Platform
description: Documentazione concettuale sui destinatari e sui tipi di account supportati nelle applicazioni
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 908ba764dfee7c164c3e6f0ff24d2bbf6a0df287
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544835"
---
# <a name="supported-account-types"></a>Tipi di account supportati

Questo articolo spiega quali tipi di account, talvolta denominati destinatari, sono supportati nelle applicazioni

<!-- This section can be in an include for many of the scenarios (SPA, Web App signing-in users, protecting a Web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Tipi di account supportati nelle applicazioni Microsoft Identity Platform

Nel cloud pubblico di Microsoft Azure la maggior parte dei tipi di app può consentire l'accesso degli utenti con qualsiasi destinatario:

- Se si sta scrivendo un'applicazione line-of-business, è possibile consentire l'accesso degli utenti dell'organizzazione. Tale applicazione a volte è chiamata **tenant singolo**.
- I fornitori di software indipendente possono scrivere un'applicazione che consente l'accesso degli utenti:

  - In qualsiasi organizzazione. Tale applicazione a volte è chiamata applicazione Web **multi-tenant**. Si potrebbe sentir dire che gli utenti possono accedere con gli account aziendali o dell'istituto di istruzione.
  - Con l'account aziendale o dell'istituto di istruzione oppure con l'account Microsoft personale.
  - Solo con l'account Microsoft personale.
    > [!NOTE]
    > Microsoft Identity Platform attualmente supporta solo gli account Microsoft personali tramite la registrazione di un'app per gli **account personali Microsoft aziendali o dell'istituto di istruzione**, quindi è necessario limitare l'accesso nel codice dell'applicazione specificando un'autorità di Azure AD quando si compila l'applicazione, ad esempio `https://login.onmicrosoftonline.com/consumers`.

- Se si sta scrivendo un'applicazione Business to Consumer, è anche possibile consentire l'accesso per gli utenti con le identità dei social network, usando Azure AD B2C.

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>Alcuni flussi di autenticazione non supportano tutti i tipi di account

Alcuni tipi di account non possono essere usati con determinati flussi di autenticazione. Ad esempio, nelle applicazioni desktop, UWP o daemon:

- Le applicazioni daemon possono essere usate solo con le organizzazioni di Azure Active Directory. Non ha senso provare a usare applicazioni daemon per modificare gli account personali Microsoft. Il consenso amministratore non verrà mai concesso.  
- È possibile solo usare il flusso di autenticazione integrata di Windows con gli account aziendali o dell'istituto di istruzione (nella propria organizzazione o in qualsiasi organizzazione). In realtà, l'autenticazione integrata di Windows può essere usata solo con gli account di dominio ed è necessario che i computer siano aggiunti a un dominio o ad Azure AD. Questo flusso non ha senso per gli account Microsoft personali.
- La [concessione della password del proprietario della risorsa](./v2-oauth-ropc.md) (nome utente/password) non può essere usata con gli account Microsoft personali. In effetti, gli account Microsoft personali richiedono il consenso utente per l'accesso alle risorse personali a ogni sessione di accesso. Per questo motivo tale comportamento non è compatibile con i flussi non interattivi.
- Il flusso di codice del dispositivo non funziona ancora con gli account Microsoft personali.

## <a name="supported-account-types-in-national-clouds"></a>Tipi di account supportati nei cloud nazionali

 Le app possono anche consentire l'accesso per gli utenti nei [cloud nazionali](authentication-national-cloud.md). Gli account personali Microsoft, tuttavia, non sono supportati in questi cloud (in base alla definizione di tali cloud). Ecco perché i tipi di account supportati sono limitati, per questi cloud, alla propria organizzazione (tenant singolo) o a qualsiasi organizzazione (applicazioni multi-tenant).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [tenancy in Azure Active Directory](./single-and-multi-tenant-apps.md)
- Altre informazioni sui [cloud nazionali](./authentication-national-cloud.md)