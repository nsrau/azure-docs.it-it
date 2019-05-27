---
title: App per dispositivi mobili che chiama l'API - configurazione del codice dell'app web | Piattaforma delle identità Microsoft
description: Informazioni su come compilare un'app per dispositivi mobili di chiamate di web API (configurazione del codice dell'app)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b6ebab0eeca6895e1c7a0f6008972030d81da42
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962400"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>App per dispositivi mobili che chiama l'API - registrazione dell'app web

Questo articolo contiene le istruzioni di registrazione di app per la creazione di un'applicazione per dispositivi mobili.

## <a name="supported-account-types"></a>Tipi di account supportati

I tipi di account nelle applicazioni per dispositivi mobili supportati variano in base l'esperienza che si vuole abilitare e gli utenti che è destinata l'app.

## <a name="platform-configuration-and-redirect-uris"></a>Configurazione della piattaforma e gli URI di reindirizzamento  

Quando si compila un'app per dispositivi mobili, il passaggio di registrazione più importante è l'URI di reindirizzamento. Ciò può essere impostata tramite il [configurazione della piattaforma nel pannello autenticazione](https://aka.ms/MobileAppReg).

Questa esperienza consentirà all'app di ottenere accesso single sign-on (SSO) tramite il Microsoft Authenticator e portale aziendale di Intune in Android, oltre al supporto i criteri di gestione di dispositivi.

Se si preferisce configurare manualmente l'URI di reindirizzamento, è possibile farlo tramite il manifesto dell'applicazione. Il formato consigliato è la seguente:

- ***iOS***: `msauth.<BUNDLE_ID>://auth`
- ***Android***: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - L'hash della firma di Android può essere generato usando le chiavi release o debug tramite il comando dello strumento KeyTool.

## <a name="api-permissions"></a>Autorizzazioni API

Applicazioni per dispositivi mobili chiamano le API per conto dell'utente connesso. L'app deve richiedere autorizzazioni delegate, nota anche come ambiti. A seconda dell'esperienza desiderata, questa operazione può essere eseguita in modo statico tramite il portale di Azure o in modo dinamico in fase di esecuzione. In modo statico la registrazione di autorizzazioni consente agli amministratori di approvare facilmente l'app ed è consigliata.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Acquisire un token](scenario-mobile-acquire-token.md)
