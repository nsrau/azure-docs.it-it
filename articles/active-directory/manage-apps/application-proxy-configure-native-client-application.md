---
title: 'Pubblicare app client native: Azure AD | Microsoft Docs'
description: Illustra come abilitare la comunicazione tra le app client native e il connettore del proxy di applicazione di Azure AD per consentire l'accesso remoto sicuro alle app locali.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d2a2bb9dd543da7455a276075a829ef06032edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159285"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Come abilitare le applicazioni client native per interagire con le applicazioni proxyHow to enable native client applications to interact with proxy applications

È possibile usare il proxy di applicazione di Azure Active Directory (Azure AD) per pubblicare app Web, ma può anche essere usato per pubblicare applicazioni client native configurate con la libreria di autenticazione di Azure AD (ADAL). Le applicazioni client native si differenziano dalle app Web perché vengono installate in un dispositivo, mentre le app Web sono accessibili tramite un browser.

Per supportare le applicazioni client native, il proxy di applicazione accetta i token rilasciati da Azure AD inviati nell'intestazione. Il servizio proxy di applicazione esegue l'autenticazione per gli utenti. Questa soluzione non usa i token dell'applicazione per l'autenticazione.

![Relazione tra gli utenti finali, Azure AD e le applicazioni pubblicate](./media/application-proxy-configure-native-client-application/richclientflow.png)

Per pubblicare applicazioni native, usare la libreria di autenticazione di Azure AD, che si occupa dell'autenticazione e supporta molti ambienti client. Il proxy di applicazione si integra con [lo scenario Da applicazione nativa ad API Web](../azuread-dev/native-app.md).

Questo articolo illustra i quattro passaggi necessari per pubblicare un'applicazione nativa con il proxy di applicazione e Azure AD Authentication Library.

## <a name="step-1-publish-your-proxy-application"></a>Passaggio 1: Pubblicare l'applicazione proxyStep 1: Publish your proxy application

Pubblicare l'applicazione proxy come qualsiasi altra applicazione e assegnare agli utenti l'accesso all'applicazione. Per ulteriori informazioni, consultate [Pubblicare applicazioni con il proxy](application-proxy-add-on-premises-application.md)di applicazione.

## <a name="step-2-register-your-native-application"></a>Passaggio 2: Registrare l'applicazione nativaStep 2: Register your native application

È ora necessario registrare l'applicazione in Azure AD, come indicato di seguito:You now need to register your application in Azure AD, as follows:

1. Accedere al [portale](https://aad.portal.azure.com/)di Azure Active Directory . Viene visualizzato il dashboard per l'interfaccia di amministrazione di Azure Active Directory.The **Dashboard** for the **Azure Active Directory admin center** appears.
1. Nella barra laterale selezionare **Azure Active Directory**. Viene visualizzata la pagina Panoramica di **Azure Active Directory.The Azure Active Directory** overview page appears.
1. Nella barra laterale Panoramica di Azure AD selezionare **Registrazioni app**. Viene visualizzato l'elenco di tutte le registrazioni di app.
1. Selezionare **Nuova registrazione**. Viene visualizzata la pagina **Registra un'applicazione.**

   ![Creare una nuova registrazione dell'app nel portale di AzureCreate a new app registration in the Azure portal](./media/application-proxy-configure-native-client-application/create.png)

1. Nell'intestazione **Nome** specificare un nome visualizzato rivolto all'utente per l'applicazione.
1. Sotto l'intestazione **Tipi di account supportati,** seleziona un livello di accesso utilizzando queste linee guida:

   - Per scegliere come target solo gli account interni all'organizzazione, selezionare **Account solo in questa directory dell'organizzazione**.
   - Per scegliere come target solo i clienti aziendali o didattici, selezionare **Account in qualsiasi directory organizzativa.**
   - Per impostare come destinazione il set più ampio di identità Microsoft, selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali**.

1. Nell'intestazione URI di **reindirizzamento** selezionare **Client pubblico (mobile & desktop)** e quindi digitare l'URI di reindirizzamento per l'applicazione.
1. Selezionare e leggere i **criteri della piattaforma Microsoft**, quindi selezionare **Registra**. Viene creata e visualizzata una pagina di panoramica per la registrazione della nuova applicazione.

Per informazioni più dettagliate sulla creazione di una nuova registrazione dell'applicazione, vedere [Integrazione di applicazioni con Azure Active Directory.](../develop/quickstart-register-app.md)

## <a name="step-3-grant-access-to-your-proxy-application"></a>Passaggio 3: Concedere l'accesso all'applicazione proxyStep 3: Grant access to your proxy application

Dopo aver registrato l'applicazione nativa, è possibile concedere l'accesso ad altre applicazioni nella directory, in questo caso per accedere all'applicazione proxy. Per consentire l'esposizione dell'applicazione nativa all'applicazione proxy:

1. Nella barra laterale della pagina di registrazione della nuova applicazione selezionare **Autorizzazioni API**. Viene visualizzata la pagina **Autorizzazioni API** per la nuova registrazione dell'applicazione.
1. Selezionare **Aggiungi un'autorizzazione**. Viene visualizzata la pagina **Richiedi autorizzazioni API.**
1. In **Impostazione API Selezionare un'API** selezionare **API utilizzate dall'organizzazione.** Viene visualizzato un elenco contenente le applicazioni nella directory che espongono le API.
1. Digitare nella casella di ricerca o scorrere per trovare l'applicazione proxy pubblicata nel [Passaggio 1: Pubblicare l'applicazione proxy](#step-1-publish-your-proxy-application)e quindi selezionare l'applicazione proxy.
1. Nell'intestazione **Tipo di autorizzazioni richiesta dall'applicazione** selezionare il tipo di autorizzazione. Se l'applicazione nativa deve accedere all'API dell'applicazione proxy come utente connesso, scegliere **Autorizzazioni delegate**.
1. Nell'intestazione **Selezione autorizzazioni** selezionare l'autorizzazione desiderata e scegliere **Aggiungi autorizzazioni**. La pagina **delle autorizzazioni API** per l'applicazione nativa ora mostra l'applicazione proxy e l'API di autorizzazione aggiunte.

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Passaggio 4: Modificare Active Directory Authentication Library

Modificare il codice dell'applicazione nativa nel contesto di autenticazione di Active Directory Authentication Library (ADAL) per includere il testo seguente:

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = await authContext.AcquireTokenAsync("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

Le informazioni necessarie nel codice di esempio sono disponibili nel portale di Azure AD, come indicato di seguito:The required info in the sample code can be found in the Azure AD portal, as follows:

| Informazioni richieste | Come trovarlo nel portale di Azure ADHow to find it in the Azure AD portal |
| --- | --- |
| \<> ID tenant | **Azure Active Directory**ID directory > **delle proprietà** > di Azure Active DirectoryAzure Active Directory Properties**Directory Directory ID** |
| \<URL esterno dell'applicazione proxy> | **Applicazioni** > *aziendali, applicazione* > proxy**Proxy di** > applicazione URL**esterno** |
| \<ID app del> dell'app nativa | **Applicazioni aziendali** > id applicazione*dell'applicazione* > nativa**Properties** > **Application ID** |
| \<URI di reindirizzamento dell'app nativa> | **Registrazioni** > **dell'app** > Azure Active Directory negli URI di reindirizzamento*dell'applicazione nativaAzure* > Active Directory App registrations your native application**Redirect URIs** |
| \<> URL dell'API dell'app proxy | **Registrazioni** >  > **dell'app**Azure Active Directory nell'API delle autorizzazioni > *dell'API dell'applicazione* > **API permissions**nativa **/ NOME PERMISSIONS** |

Dopo aver modificato l'ADAL con questi parametri, gli utenti possono eseguire l'autenticazione alle applicazioni client native anche quando si trovano all'esterno della rete aziendale.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul flusso dell'applicazione nativa, vedere App native in Azure Active Directory.For more information about the native application flow, see [Native apps in Azure Active Directory.](../azuread-dev/native-app.md)

Informazioni sulla configurazione [dell'accesso Single Sign-On alle applicazioni in Azure Active Directory.](what-is-single-sign-on.md#choosing-a-single-sign-on-method)
