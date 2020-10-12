---
title: Tipi di account supportati-piattaforma di identità Microsoft | Azure
description: Documentazione concettuale sui destinatari e sui tipi di account supportati nelle applicazioni
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 50a0859b58e2db0d3f644db01cfcde8c533b6871
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86518131"
---
# <a name="supported-account-types"></a>Tipi di account supportati

Questo articolo illustra i tipi di account (talvolta chiamati *destinatari*) supportati nelle applicazioni della piattaforma Microsoft Identity.

<!-- This section can be in an include for many of the scenarios (SPA, web app signing-in users, protecting a web API, Desktop (depending on the flows), Mobile -->

## <a name="account-types-in-the-public-cloud"></a>Tipi di account nel cloud pubblico

Nel cloud pubblico Microsoft Azure, la maggior parte dei tipi di app può accedere agli utenti con tutti i destinatari:

- Se si sta scrivendo un'applicazione line-of-business (LOB), è possibile accedere agli utenti nella propria organizzazione. Tale applicazione è talvolta denominata *single-tenant*.
- Se si è un ISV, è possibile scrivere un'applicazione per l'accesso degli utenti:

  - In qualsiasi organizzazione. Tale applicazione è detta applicazione Web *multi-tenant* . Si noterà a volte che l'utente esegue l'accesso agli utenti con account aziendali o dell'Istituto di istruzione.
  - Con gli account Microsoft aziendali o dell'Istituto di istruzione o personali.
  - Con solo account Microsoft personali.
    
- Se si sta scrivendo un'applicazione business-to-consumer, è anche possibile accedere agli utenti con le proprie identità social, usando Azure Active Directory B2C (Azure AD B2C).

## <a name="account-type-support-in-authentication-flows"></a>Supporto del tipo di conto nei flussi di autenticazione

Alcuni tipi di account non possono essere usati con determinati flussi di autenticazione. Ad esempio, nelle applicazioni desktop, UWP o daemon:

- Le applicazioni daemon possono essere usate solo con Azure AD organizzazioni. Non ha senso provare a usare le applicazioni daemon per modificare gli account personali di Microsoft. Il consenso dell'amministratore non verrà mai concesso.
- È possibile usare il flusso di autenticazione integrata di Windows solo con account aziendali o dell'Istituto di istruzione (nell'organizzazione o in qualsiasi organizzazione). L'autenticazione integrata di Windows funziona con gli account di dominio e richiede che i computer siano aggiunti a un dominio o Azure AD Uniti in join. Questo flusso non ha senso per gli account Microsoft personali.
- La [concessione delle credenziali password del proprietario della risorsa](./v2-oauth-ropc.md) (nome utente/password) non può essere usata con gli account Microsoft personali. Per gli account Microsoft personali è necessario che l'utente acconsente all'accesso alle risorse personali a ogni sessione di accesso. Questo è il motivo per cui questo comportamento non è compatibile con i flussi non interattivi.

## <a name="account-types-in-national-clouds"></a>Tipi di conto nei cloud nazionali

Le app possono anche consentire l'accesso per gli utenti nei [cloud nazionali](authentication-national-cloud.md). Tuttavia, gli account personali Microsoft non sono supportati in questi cloud. Ecco perché i tipi di account supportati sono ridotti, per questi cloud, per l'organizzazione (tenant singolo) o per qualsiasi organizzazione (applicazioni multi-tenant).

## <a name="next-steps"></a>Passaggi successivi

- Scopri di più sull' [affitto in Azure Active Directory](./single-and-multi-tenant-apps.md).
- Scopri di più sui [cloud nazionali](./authentication-national-cloud.md).
