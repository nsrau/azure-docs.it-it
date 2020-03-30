---
title: Registrare le app per dispositivi mobili che chiamano le API Web Azure
titleSuffix: Microsoft identity platform
description: Scopri come creare un'app per dispositivi mobili che chiama le API Web (configurazione del codice dell'app)
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
ms.openlocfilehash: 75cfd304869bfb63131dfd2afed9f925c86d32fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132445"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>Registrare app per dispositivi mobili che chiamano API Web

Questo articolo contiene istruzioni per la registrazione di un'applicazione per dispositivi mobili che si sta creando.

## <a name="supported-account-types"></a>Tipi di account supportati

I tipi di account supportati dalle applicazioni per dispositivi mobili dipendono dall'esperienza che si desidera abilitare e dai flussi che si desidera utilizzare.

### <a name="audience-for-interactive-token-acquisition"></a>Pubblico per l'acquisizione di token interattivi

La maggior parte delle applicazioni mobili utilizza l'autenticazione interattiva. Se l'app usa questa forma di autenticazione, puoi accedere agli utenti da qualsiasi [tipo di account.](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>Destinatari per l'autenticazione integrata di Windows, nome utente-password e B2C

Se si dispone di un'app UWP (Universal Windows Platform), è possibile usare l'autenticazione integrata di Windows per accedere agli utenti. Per usare l'autenticazione integrata di Windows o l'autenticazione con password utente, l'applicazione deve accedere agli utenti nel proprio tenant per sviluppatori line-of-business (LOB). In uno scenario di fornitore di software indipendente (ISV), l'applicazione può accedere agli utenti nelle organizzazioni di Azure Active Directory.In an independent software vendor (ISV) scenario, your application can sign in users in Azure Active Directory organizations. Questi flussi di autenticazione non sono supportati per gli account personali Microsoft.These authentication flows aren't supported for Microsoft personal accounts.

È inoltre possibile accedere agli utenti utilizzando le identità di social networking che superano un'autorità e criteri B2C. Per utilizzare questo metodo, è possibile utilizzare solo l'autenticazione interattiva e l'autenticazione nomeutente-password. L'autenticazione nome utente-password è attualmente supportata solo su Xamarin.iOS, Xamarin.Android e UWP.

Per ulteriori informazioni, vedere [Scenari e flussi](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) di autenticazione supportati e Scenari e [piattaforme e lingue supportate.](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="platform-configuration-and-redirect-uris"></a>Configurazione della piattaforma e URI di reindirizzamento  

### <a name="interactive-authentication"></a>Autenticazione interattiva

Quando si compila un'app per dispositivi mobili che usa l'autenticazione interattiva, il passaggio di registrazione più critico è l'URI di reindirizzamento. È possibile impostare l'autenticazione interattiva tramite la configurazione della [piattaforma nel pannello **Autenticazione.** ](https://aka.ms/MobileAppReg)

Questa esperienza consentirà all'app di ottenere l'accesso Single Sign-On (SSO) tramite Microsoft Authenticator (e Intune Company Portal in Android). Supporterà anche i criteri di gestione dei dispositivi.

Il portale di registrazione delle app offre un'esperienza di anteprima che consente di calcolare l'URI di risposta negoziata per le applicazioni iOS e Android:The app registration portal provides a preview experience to help you compute the brokered reply URI for iOS and Android applications:

1. Nel portale di registrazione app selezionare **Autenticazione** > **Provare la nuova esperienza**.

   ![Il pannello Autenticazione, in cui è possibile scegliere una nuova esperienza](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Selezionare **Aggiungi una piattaforma**.

   ![Aggiungere una piattaformaAdd a platform](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Quando l'elenco delle piattaforme è supportato, selezionare **iOS**.

   ![Scegliere un'applicazione per dispositivi mobili](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Immettere l'ID del bundle, quindi selezionare **Registra**.

   ![Inserisci il tuo ID bundle](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Quando si completano i passaggi, l'URI di reindirizzamento viene calcolato automaticamente, come nell'immagine seguente.

![URI di reindirizzamento risultante](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Se si preferisce configurare manualmente l'URI di reindirizzamento, è possibile farlo tramite il manifesto dell'applicazione. Ecco il formato consigliato per il manifesto:Here's the recommended format for the manifest:

- **iOS**:`msauth.<BUNDLE_ID>://auth` 
  - Ad esempio, immettere `msauth.com.yourcompany.appName://auth`
- **Androide**:`msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - È possibile generare l'hash della firma Android utilizzando il tasto di rilascio o la chiave di debug tramite il comando KeyTool.You can generate the Android signature hash by using the release key or debug key through the KeyTool command.

### <a name="username-password-authentication"></a>Autenticazione nome utente-password

Se l'app usa solo l'autenticazione nome utente-password, non è necessario registrare un URI di reindirizzamento per l'applicazione. Questo flusso esegue un round trip all'endpoint della piattaforma di identità Microsoft versione 2.0.This flow does a round trip to the Microsoft identity platform version 2.0 endpoint. L'applicazione non verrà richiamata su alcun URI specifico. 

Tuttavia, è necessario identificare l'applicazione come applicazione client pubblica. A tale scopo, iniziare nella sezione **Autenticazione** dell'applicazione. Nella sottosezione **Impostazioni avanzate,** nel paragrafo **Tipo di client predefinito,** per la domanda Considera **l'applicazione come client pubblico,** selezionare **Sì**.

## <a name="api-permissions"></a>Autorizzazioni delle API

Le applicazioni mobili chiamano API per conto dell'utente connesso. L'app deve richiedere autorizzazioni delegate. Queste autorizzazioni sono anche denominate ambiti. A seconda dell'esperienza desiderata, è possibile richiedere le autorizzazioni delegate in modo statico tramite il portale di Azure.Depending on the experience that you want, you can request delegated permissions statically through the Azure portal. In alternativa, è possibile richiederli in modo dinamico in fase di esecuzione. 

Registrando in modo statico le autorizzazioni, consenti agli amministratori di approvare facilmente la tua app. Si consiglia la registrazione statica.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurazione del codice](scenario-mobile-app-configuration.md)
