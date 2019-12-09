---
title: Configurare app per dispositivi mobili che chiamano API Web | Azure
titleSuffix: Microsoft identity platform
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
ms.openlocfilehash: 2c5160d88e390f9bfe905dfee0684983a7a49365
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919886"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>App per dispositivi mobili che chiama le API Web-registrazione app

Questo articolo contiene le istruzioni per la registrazione di app per la creazione di un'applicazione per dispositivi mobili.

## <a name="supported-accounts-types"></a>Tipi di account supportati

I tipi di account supportati nelle applicazioni per dispositivi mobili dipendono dall'esperienza che si vuole abilitare e dai flussi da usare.

### <a name="audience-for-interactive-token-acquisition"></a>Destinatari per l'acquisizione di token interattivi

La maggior parte delle applicazioni mobili usa l'autenticazione interattiva. In tal caso, è possibile accedere agli utenti da qualsiasi tipo di [account](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)

### <a name="audience-for-integrated-authentication-usernamepassword-and-b2c"></a>Destinatari per l'autenticazione integrata, il nome utente/password e B2C

- Se si intende usare l'autenticazione integrata di Windows (possibile nelle app UWP) o il nome utente/password, l'applicazione deve eseguire l'accesso degli utenti nel proprio tenant (sviluppatore LOB) o nelle organizzazioni di Azure Active Directory (scenario ISV). Questi flussi di autenticazione non sono supportati per gli account personali Microsoft
- Se si esegue l'accesso agli utenti con identità di social networking che passano un'autorità e un criterio B2C, è possibile usare solo l'autenticazione interattiva e nome utente-password. Username-password è attualmente supportata solo in Novell. iOS, Novell. Android e UWP.

Per un quadro generale, vedere [scenari e flussi di autenticazione supportati](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) e [scenari e piattaforme e linguaggi supportati](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="platform-configuration-and-redirect-uris"></a>URI di reindirizzamento e configurazione della piattaforma  

### <a name="interactive-authentication"></a>Autenticazione interattiva

Quando si compila un'app per dispositivi mobili usando l'autenticazione interattiva, il passaggio di registrazione più critico è l'URI di reindirizzamento. Questa impostazione può essere configurata tramite la [configurazione della piattaforma nel pannello autenticazione](https://aka.ms/MobileAppReg).

Questa esperienza consente all'app di ottenere Single Sign-On (SSO) tramite i Microsoft Authenticator (e Portale aziendale Intune in Android), oltre a supportare i criteri di gestione dei dispositivi.

Si noti che nel portale di registrazione delle app è disponibile un'esperienza di anteprima che consente di calcolare l'URI di risposta negoziato per le applicazioni iOS e Android:

1. Nella registrazione dell'app scegliere **autenticazione** e selezione **prova la nuova esperienza**
   ![immagine](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Selezionare **Aggiungi piattaforma**
   ![immagine](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Quando l'elenco delle piattaforme è supportato, selezionare **iOS**
   ![immagine](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Immettere l'ID bundle come richiesto e quindi premere **Register**
   ![image](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

5. L'URI di reindirizzamento viene calcolato per l'utente.
   ![image](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Se si preferisce configurare manualmente l'URI di reindirizzamento, è possibile farlo tramite il manifesto dell'applicazione. Il formato consigliato è il seguente:

- ***iOS***: `msauth.<BUNDLE_ID>://auth` (ad esempio "msauth. com. impresa. AppName://auth")
- ***Android***: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - L'hash della firma Android può essere generato usando le chiavi di rilascio o di debug tramite il comando di chiave.

### <a name="username-password"></a>Password nome utente

Se l'app usa solo nome utente/password, non è necessario registrare un URI di reindirizzamento per l'applicazione. Questo flusso esegue effettivamente una round trip all'endpoint Microsoft Identity Platform 2.0 e l'applicazione non verrà richiamata su un URI specifico. Tuttavia, è necessario esprimere che l'applicazione è un'applicazione client pubblica. Per ottenere questa configurazione, passare alla sezione **autenticazione** per l'applicazione e nella sottosezione **Impostazioni avanzate** , scegliere **Sì**, per l' **applicazione question treat come client pubblico** (nel paragrafo del **tipo di client predefinito** )

## <a name="api-permissions"></a>Autorizzazioni delle API

Le applicazioni per dispositivi mobili chiamano le API per conto dell'utente che ha eseguito l'accesso. L'app deve richiedere autorizzazioni delegate, denominate anche ambiti. A seconda dell'esperienza desiderata, questa operazione può essere eseguita in modo statico tramite il portale di Azure o dinamicamente in fase di esecuzione. La registrazione statica delle autorizzazioni consente agli amministratori di approvare facilmente l'app ed è consigliata.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurazione del codice](scenario-mobile-app-configuration.md)
