---
title: App per dispositivi mobili che chiama le API Web-configurazione del codice dell'app | Piattaforma di identità Microsoft
description: Informazioni su come creare un'app per dispositivi mobili che chiama le API Web (configurazione del codice dell'app)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
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
ms.openlocfilehash: f43ae9da51f68c9765a36d27c993d1c9935d61fa
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326115"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>App per dispositivi mobili che chiama le API Web-registrazione app

Questo articolo contiene le istruzioni per la registrazione di app per la creazione di un'applicazione per dispositivi mobili.

## <a name="supported-account-types"></a>Tipi di account supportati

I tipi di account supportati nelle applicazioni per dispositivi mobili dipendono dall'esperienza che si vuole abilitare e dagli utenti a cui è destinata l'app.

## <a name="platform-configuration-and-redirect-uris"></a>URI di reindirizzamento e configurazione della piattaforma  

Quando si compila un'app per dispositivi mobili, il passaggio di registrazione più critico è l'URI di reindirizzamento. Questa impostazione può essere configurata tramite la [configurazione della piattaforma nel pannello autenticazione](https://aka.ms/MobileAppReg).

Questa esperienza consente all'app di ottenere l'accesso Single Sign-on (SSO) tramite il Microsoft Authenticator (e Portale aziendale Intune in Android), oltre a supportare i criteri di gestione dei dispositivi.

Se si preferisce configurare manualmente l'URI di reindirizzamento, è possibile farlo tramite il manifesto dell'applicazione. Il formato consigliato è il seguente:

- ***iOS***:`msauth.<BUNDLE_ID>://auth`
- ***Android***:`msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - L'hash della firma Android può essere generato usando le chiavi di rilascio o di debug tramite il comando di chiave.

## <a name="api-permissions"></a>Autorizzazioni delle API

Le applicazioni per dispositivi mobili chiamano le API per conto dell'utente che ha eseguito l'accesso. L'app deve richiedere autorizzazioni delegate, denominate anche ambiti. A seconda dell'esperienza desiderata, questa operazione può essere eseguita in modo statico tramite il portale di Azure o dinamicamente in fase di esecuzione. La registrazione statica delle autorizzazioni consente agli amministratori di approvare facilmente l'app ed è consigliata.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Acquisizione di un token](scenario-mobile-acquire-token.md)
