---
title: Registrare le app desktop che chiamano API Web - Piattaforma di identità Microsoft Azure
description: Informazioni su come creare un'app desktop che chiama le API Web (registrazione dell'app)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c55fc9eb94a88dba1ab9fc915fe84bc2dd7d4d40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702182"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>App desktop che chiama le API Web: registrazione dell'app

Questo articolo illustra le specifiche di registrazione dell'app per un'applicazione desktop.

## <a name="supported-account-types"></a>Tipi di account supportati

I tipi di account supportati in un'applicazione desktop dipendono dall'esperienza che si desidera illuminare. A causa di questa relazione, i tipi di account supportati dipendono dai flussi che si desidera utilizzare.

### <a name="audience-for-interactive-token-acquisition"></a>Pubblico per l'acquisizione di token interattivi

Se l'applicazione desktop utilizza l'autenticazione interattiva, è possibile accedere agli utenti da qualsiasi [tipo di account.](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)

### <a name="audience-for-desktop-app-silent-flows"></a>Pubblico per flussi silenziosi delle app desktop

- Per usare l'autenticazione integrata di Windows o un nome utente e una password, l'applicazione deve accedere agli utenti nel proprio tenant, ad esempio se si è uno sviluppatore line-of-business (LOB). In alternativa, nelle organizzazioni di Azure Active Directory, l'applicazione deve accedere agli utenti nel proprio tenant se si tratta di uno scenario ISV. Questi flussi di autenticazione non sono supportati per gli account personali Microsoft.These authentication flows aren't supported for Microsoft personal accounts.
- Se si vuole usare il flusso di codice del dispositivo, non è ancora possibile accedere agli utenti con i propri account personali Microsoft.
- Se si accede agli utenti con identità social che passano un'autorità e criteri business-to-commerce (B2C), è possibile utilizzare solo l'autenticazione interattiva e nome utente-password.

## <a name="redirect-uris"></a>URI di reindirizzamento

Gli URI di reindirizzamento da utilizzare in un'applicazione desktop dipendono dal flusso che si vuole usare.

- Se si utilizza l'autenticazione `https://login.microsoftonline.com/common/oauth2/nativeclient`interattiva o il flusso di codice del dispositivo, utilizzare . Per ottenere questa configurazione, selezionare l'URL corrispondente nella sezione **Autenticazione** per l'applicazione.
  
  > [!IMPORTANT]
  > Oggi, MSAL.NET utilizza un altro URI di reindirizzamento`urn:ietf:wg:oauth:2.0:oob`per impostazione predefinita nelle applicazioni desktop eseguite in Windows ( ). In futuro, si vorrà modificare questa impostazione predefinita, `https://login.microsoftonline.com/common/oauth2/nativeclient`pertanto è consigliabile utilizzare .

- Se crei un'app Objective-C o Swift nativa per macOS, registra l'URI di reindirizzamento in base all'identificatore del bundle dell'applicazione nel formato seguente: msauth.<your.app.bundle.id>://auth. Sostituisci <your.app.bundle.id> con l'identificatore del bundle dell'applicazione.
- Se l'app usa solo l'autenticazione integrata di Windows o un nome utente e una password, non è necessario registrare un URI di reindirizzamento per l'applicazione. Questi flussi fanno un round trip all'endpoint Microsoft identity platform 2.0. L'applicazione non verrà richiamata su alcun URI specifico.
- Per distinguere il flusso del codice del dispositivo, l'autenticazione integrata di Windows e un nome utente e una password da un flusso di applicazione client riservato che non dispone di URI di reindirizzamento (il flusso delle credenziali client utilizzato nelle applicazioni daemon), è necessario esprimere tale l'applicazione è un'applicazione client pubblica. Per ottenere questa configurazione, passare alla sezione **Autenticazione** per l'applicazione. Nella sottosezione **Impostazioni avanzate** selezionare **Sì** per Considera applicazione come client pubblico nel paragrafo Tipo di **client predefinito.** **Treat application as a public client**

  ![Consenti client pubblico](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>Autorizzazioni delle API

Le applicazioni desktop chiamano le API per l'utente connesso. Devono richiedere autorizzazioni delegate. Non possono richiedere le autorizzazioni dell'applicazione, che vengono gestite solo nelle [applicazioni daemon](scenario-daemon-overview.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [App desktop: configurazione dell'app](scenario-desktop-app-configuration.md)
