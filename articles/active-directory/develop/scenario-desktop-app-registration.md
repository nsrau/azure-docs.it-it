---
title: Registrare le app desktop che chiamano API Web-piattaforma di identità Microsoft | Azure
description: Informazioni su come creare un'app desktop che chiama le API Web (registrazione dell'app)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 787380dbf6f739d4b88f18a836da2146b06024c3
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443144"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>App desktop che chiama le API Web: registrazione dell'app

Questo articolo illustra le specifiche di registrazione delle app per un'applicazione desktop.

## <a name="supported-account-types"></a>Tipi di account supportati

I tipi di account supportati in un'applicazione desktop dipendono dall'esperienza che si desidera attivare. A causa di questa relazione, i tipi di conto supportati dipendono dai flussi che si desidera utilizzare.

### <a name="audience-for-interactive-token-acquisition"></a>Destinatari per l'acquisizione di token interattivi

Se l'applicazione desktop usa l'autenticazione interattiva, è possibile accedere agli utenti da qualsiasi [tipo di account](quickstart-register-app.md).

### <a name="audience-for-desktop-app-silent-flows"></a>Destinatari per flussi invisibile all'app desktop

- Per usare l'autenticazione integrata di Windows o un nome utente e una password, l'applicazione deve eseguire l'accesso degli utenti nel proprio tenant, ad esempio, se si è uno sviluppatore line-of-business (LOB). In alternativa, in Azure Active Directory organizzazioni, l'applicazione deve eseguire l'accesso degli utenti nel proprio tenant se si tratta di uno scenario ISV. Questi flussi di autenticazione non sono supportati per gli account personali Microsoft.
- Se si esegue l'accesso agli utenti con identità di social networking che passano un'autorità e un criterio business-to-Commerce (B2C), è possibile usare solo l'autenticazione interattiva e nome utente-password.

## <a name="redirect-uris"></a>URI di reindirizzamento

Gli URI di Reindirizzamento da usare in un'applicazione desktop dipendono dal flusso che si vuole usare.

- Se si usa l'autenticazione interattiva o il flusso del codice del dispositivo, usare `https://login.microsoftonline.com/common/oauth2/nativeclient` . Per ottenere questa configurazione, selezionare l'URL corrispondente nella sezione **autenticazione** per l'applicazione.

  > [!IMPORTANT]
  > Attualmente, MSAL.NET usa un altro URI di reindirizzamento per impostazione predefinita nelle applicazioni desktop eseguite in Windows ( `urn:ietf:wg:oauth:2.0:oob` ). In futuro, sarà necessario modificare questa impostazione predefinita, quindi è consigliabile usare `https://login.microsoftonline.com/common/oauth2/nativeclient` .

- Se si compila un'app Objective-C o SWIFT nativa per macOS, registrare l'URI di reindirizzamento in base all'identificatore del bundle dell'applicazione nel formato seguente: `msauth.<your.app.bundle.id>://auth` . Sostituire `<your.app.bundle.id>` con l'identificatore del bundle dell'applicazione.
- Se l'app usa solo l'autenticazione integrata di Windows o un nome utente e una password, non è necessario registrare un URI di reindirizzamento per l'applicazione. Questi flussi eseguono una round trip all'endpoint della piattaforma Microsoft Identity Platform v 2.0. L'applicazione non verrà richiamata su un URI specifico.
- Per distinguere [il flusso del codice del dispositivo](scenario-desktop-acquire-token.md#device-code-flow), [l'autenticazione integrata di Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication)e un [nome utente e una password](scenario-desktop-acquire-token.md#username-and-password) da un'applicazione client riservata usando un flusso di credenziali client usato nelle [applicazioni daemon](scenario-daemon-overview.md), nessuna delle quali richiede un URI di reindirizzamento, è necessario configurarlo come applicazione client pubblica. Per ottenere questa configurazione:

    1. Nella [portale di Azure](https://portal.azure.com)selezionare l'app in **registrazioni app** e quindi selezionare **autenticazione**.
    1. In **Impostazioni avanzate**  >  il **tipo di client predefinito**  >  **considera applicazione come client pubblico** selezionare **Sì**.

        :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Abilitare l'impostazione client pubblica nel riquadro autenticazione in portale di Azure":::

## <a name="api-permissions"></a>Autorizzazioni delle API

Le applicazioni desktop chiamano le API per l'utente che ha eseguito l'accesso. Devono richiedere autorizzazioni delegate. Non possono richiedere le autorizzazioni dell'applicazione, che vengono gestite solo nelle [applicazioni daemon](scenario-daemon-overview.md).

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo in questo scenario, configurazione del [codice dell'app](scenario-desktop-app-configuration.md).
