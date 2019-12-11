---
title: Registrare un'app desktop che chiama API Web-piattaforma di identità Microsoft | Azure
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94f7f2dfdbf404a092773857a0f7727618cd429a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965535"
---
# <a name="desktop-app-that-calls-web-apis---app-registration"></a>App desktop che chiama le API Web-registrazione app

Questo articolo illustra le specifiche di registrazione delle app per un'applicazione desktop.

## <a name="supported-accounts-types"></a>Tipi di account supportati

I tipi di account supportati nell'applicazione desktop dipendono dall'esperienza che si desidera attivare. A causa di questa relazione, i tipi di conto supportati dipendono dai flussi che si desidera utilizzare.

### <a name="audience-for-interactive-token-acquisition"></a>Destinatari per l'acquisizione di token interattivi

Se l'applicazione desktop usa l'autenticazione interattiva, è possibile accedere agli utenti da qualsiasi [tipo di account](quickstart-register-app.md#register-a-new-application-using-the-azure-portal).

### <a name="audience-for-desktop-app-silent-flows"></a>Destinatari per flussi invisibile all'app desktop

- Per usare l'autenticazione integrata di Windows o il nome utente/password, l'applicazione deve eseguire l'accesso degli utenti nel proprio tenant (sviluppatore LOB) o nelle organizzazioni di Azure Active Directory (scenario ISV). Questi flussi di autenticazione non sono supportati per gli account personali Microsoft.
- Se si vuole usare il flusso del codice del dispositivo, non è ancora possibile accedere agli utenti con gli account personali Microsoft.
- Se si esegue l'accesso agli utenti con identità di social networking che passano un'autorità e un criterio B2C, è possibile usare solo l'autenticazione interattiva e username-password.

## <a name="redirect-uris"></a>URI di reindirizzamento

Gli URI di Reindirizzamento da usare nell'applicazione desktop dipenderanno dal flusso che si vuole usare.

- Se si usa l' **autenticazione interattiva** o il **flusso del codice del dispositivo**, è opportuno usare `https://login.microsoftonline.com/common/oauth2/nativeclient`. Per ottenere questa configurazione, fare clic sull'URL corrispondente nella sezione **autenticazione** per l'applicazione.
  
  > [!IMPORTANT]
  > Attualmente MSAL.NET usa un altro URI di reindirizzamento per impostazione predefinita nelle applicazioni desktop in esecuzione in Windows (`urn:ietf:wg:oauth:2.0:oob`). In futuro è opportuno modificare questa impostazione predefinita e pertanto si consiglia di usare `https://login.microsoftonline.com/common/oauth2/nativeclient`

- Se si sta creando un'app Objective-C o SWIFT nativa per macOS, è opportuno registrare il redirectUri in base all'identificatore del bundle dell'applicazione nel formato seguente: **msauth. < il. app. bundle. id >://auth** (sostituire < il. app. bundle. ID > con l'identificatore del bundle dell'applicazione)
- Se l'app usa solo l'autenticazione integrata di Windows o il nome utente/password, non è necessario registrare un URI di reindirizzamento per l'applicazione. Questi flussi eseguono un round trip all'endpoint Microsoft Identity Platform 2.0 e l'applicazione non verrà richiamata su un URI specifico.
- Per distinguere il flusso del codice del dispositivo, l'autenticazione integrata di Windows e il nome utente/password da un flusso di applicazioni client riservate che non dispone di URI di reindirizzamento (il flusso di credenziali client utilizzato nelle applicazioni daemon), è necessario esprimere che l'applicazione è un'applicazione client pubblica. Per ottenere questa configurazione, passare alla sezione **autenticazione** per l'applicazione. Quindi, nella sottosezione **Impostazioni avanzate** , nel paragrafo del **tipo di client predefinito** , scegliere **Sì** per l'applicazione question **treat come client pubblico**.

  ![Consenti client pubblico](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>Autorizzazioni delle API

Le applicazioni desktop chiamano le API per l'utente che ha eseguito l'accesso. Devono richiedere autorizzazioni delegate. Tuttavia, non possono richiedere le autorizzazioni dell'applicazione, che vengono gestite solo nelle [applicazioni daemon](scenario-daemon-overview.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [App desktop-configurazione app](scenario-desktop-app-configuration.md)
