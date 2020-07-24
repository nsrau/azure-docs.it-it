---
title: Eseguire la migrazione di un'app a pagina singola JavaScript da una concessione implicita al flusso del codice di autorizzazione | Azure
titleSuffix: Microsoft identity platform
description: Come aggiornare un'applicazione a pagina singola JavaScript con MSAL.js 1.x e il flusso di concessione implicita a MSAL.js 2.x e il flusso del codice di autorizzazione con il supporto PKCE e CORS.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev
ms.openlocfilehash: b7316756aab7875dce50a3783cb95ca42676b970
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87027088"
---
# <a name="migrate-a-javascript-single-page-app-from-implicit-grant-to-auth-code-flow"></a>Eseguire la migrazione di un'app a pagina singola JavaScript da una concessione implicita al flusso del codice di autorizzazione

Microsoft Authentication Library per JavaScript (MSAL.js) v2.0 fornisce il supporto per il flusso del codice di autorizzazione con PKCE e CORS alle applicazioni a pagina singola in Microsoft Identity Platform. Seguire la procedura descritta nelle sezioni seguenti per eseguire la migrazione dell'applicazione MSAL.js 1.x con la concessione implicita a MSAL.js 2.0+ (da qui in avanti *2.x*) e il flusso del codice di autenticazione.

MSAL.js 2.x rappresenta un miglioramento rispetto a MSAL.js 1.x in quanto supporta il flusso del codice di autorizzazione nel browser invece del flusso di concessione implicita. MSAL.js 2.x **NON** supporta il flusso implicito.

## <a name="migration-steps"></a>Passaggi della migrazione

Per aggiornare l'applicazione a MSAL.js 2.x e al flusso del codice di autenticazione, sono previsti tre passaggi principali:

1. Modificare gli URI di reindirizzamento di [registrazione app](#switch-redirect-uris-to-spa-platform) dalla piattaforma **Web** alla piattaforma di tipo **applicazione a pagina singola**.
1. Aggiornare il [codice](#switch-redirect-uris-to-spa-platform) da MSAL.js 1.x a **2.x**.
1. Disabilitare la [concessione implicita](#disable-implicit-grant-settings) nella registrazione app quando tutte le applicazioni che condividono la registrazione sono state aggiornate a MSAL.js 2.x e al flusso del codice di autenticazione.

Le sezioni seguenti descrivono ogni passaggio in modo più dettagliato.

## <a name="switch-redirect-uris-to-spa-platform"></a>Modificare gli URI di reindirizzamento alla piattaforma di tipo applicazione a pagina singola

Se si vuole continuare a usare la registrazione app esistente per le applicazioni, usare il portale di Azure per aggiornare gli URI di reindirizzamento della registrazione alla piattaforma di tipo applicazione a pagina singola. In questo modo, si abilita il flusso del codice di autorizzazione con il supporto PKCE e CORS per le app che usano la registrazione (è comunque necessario aggiornare il codice dell'applicazione a MSAL.js v2.x).

Seguire questa procedura per le registrazioni app che sono attualmente configurate con gli URI di reindirizzamento della piattaforma **Web**:

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare il tenant **Azure Active Directory**.
1. In **Registrazioni app** selezionare l'applicazione e quindi **Autenticazione**.
1. Nel riquadro della piattaforma **Web** sotto gli **URI di reindirizzamento** selezionare il banner di avviso che indica che è necessario eseguire la migrazione degli URI.

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-01-implicit-warning-banner.png" alt-text="Banner di avviso del flusso implicito nel riquadro dell'app Web nel portale di Azure":::
1. Selezionare *solo* gli URI di reindirizzamento le cui applicazioni useranno MSAL.js 2.x e quindi selezionare **Configura**.

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-02-select-redirect-uri.png" alt-text="Riquadro di selezione degli URI di reindirizzamento nel riquadro dell'applicazione a pagina singola nel portale di Azure":::

Questi URI di reindirizzamento verranno ora visualizzati nel riquadro della piattaforma di tipo **applicazione a pagina singola**, che mostra che il supporto CORS con il flusso del codice di autorizzazione e PKCE sono abilitati per questi URI.

:::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-03-spa-redirect-uri-tile.png" alt-text="Riquadro dell'applicazione a pagina singola nella registrazione app del portale di Azure":::

È anche possibile [creare una nuova registrazione app](scenario-spa-app-registration.md) invece di aggiornare gli URI di reindirizzamento nella registrazione esistente.

## <a name="update-your-code-to-msaljs-2x"></a>Aggiornare il codice a MSAL.js 2.x

In MSAL 1.x è stata creata un'istanza dell'applicazione inizializzando un oggetto [UserAgentApplication][msal-js-useragentapplication] come indicato di seguito:

```javascript
// MSAL 1.x
import * as msal from "msal";

const msalInstance = new msal.UserAgentApplication(config);
```

In MSAL 2.x, inizializzare invece un oggetto [PublicClientApplication][msal-js-publicclientapplication]:

```javascript
// MSAL 2.x
import * as msal from "@azure/msal-browser";

const msalInstance = new msal.PublicClientApplication(config);
```

Per una procedura dettagliata dell'aggiunta di MSAL 2.x all'applicazione, vedere l'articolo[Esercitazione: Eseguire l'accesso degli utenti e chiamare l'API Microsoft Graph da un'applicazione a pagina singola JavaScript usando il flusso del codice di autenticazione](tutorial-v2-javascript-auth-code.md).

Per altre modifiche che potrebbe essere necessario apportare al codice, vedere la [guida alla migrazione](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/v1-migration.md) su GitHub.

## <a name="disable-implicit-grant-settings"></a>Disabilitare le impostazioni per la concessione implicita

Dopo aver aggiornato tutte le applicazioni di produzione che usano questa registrazione app e il relativo ID client a MSAL 2.x e il flusso del codice di autorizzazione, deselezionare le impostazioni per la concessione implicita nella registrazione app.

Quando si deselezionano le impostazioni per la concessione implicita nella registrazione app, il flusso implicito viene disabilitato per tutte le applicazioni che usano la registrazione e il relativo ID client.

**Non** disabilitare il flusso di concessione implicita prima di aver aggiornato tutte le applicazioni a MSAL.js 2.x e [PublicClientApplication][msal-js-publicclientapplication].

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul flusso del codice di autorizzazione, incluse le differenze tra i flussi di codice implicito e di autenticazione, vedere [Flusso del codice di autorizzazione di Microsoft Identity Platform e OAuth 2.0](v2-oauth2-auth-code-flow.md).

Se si vuole approfondire lo sviluppo di applicazioni a pagina singola JavaScript con Microsoft Identity Platform, la serie di articoli in più parti [Scenario: applicazione a pagina singola](scenario-spa-overview.md) può essere utile per iniziare.

<!-- LINKS - external -->
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/modules/_useragentapplication_.html
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/classes/_src_app_publicclientapplication_.publicclientapplication.html
