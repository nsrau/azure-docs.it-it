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
ms.openlocfilehash: cb36d6a03da07681db468184a489a79f7f0deab7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65825497"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Come abilitare le applicazioni client native interagire con applicazioni proxy

È possibile usare il Proxy di applicazione di Azure Active Directory (Azure AD) per pubblicare le app web, ma può anche essere utilizzato per pubblicare le applicazioni client native configurate con Azure Active Directory Authentication Library (ADAL). Le applicazioni client native si differenziano dalle app Web perché vengono installate in un dispositivo, mentre le app Web sono accessibili tramite un browser.

Per supportare le applicazioni client native, il Proxy di applicazione accetta i token rilasciati AD Azure che vengono inviati nell'intestazione. Il servizio Proxy di applicazione esegue l'autenticazione per gli utenti. Questa soluzione non usa i token dell'applicazione per l'autenticazione.

![Relazione tra utenti finali, Azure Active Directory e applicazioni pubblicate](./media/application-proxy-configure-native-client-application/richclientflow.png)

Per pubblicare le applicazioni native, usare Azure Active Directory Authentication Library, che si occupa di autenticazione e supporta molti ambienti client. Il proxy di applicazione si integra con [lo scenario Da applicazione nativa ad API Web](../develop/native-app.md).

Questo articolo illustra i quattro passaggi necessari per pubblicare un'applicazione nativa con il proxy di applicazione e Azure AD Authentication Library.

## <a name="step-1-publish-your-proxy-application"></a>Passaggio 1: Pubblicare il proxy di applicazione

Pubblicare l'applicazione proxy come qualsiasi altra applicazione e assegnare agli utenti l'accesso all'applicazione. Per altre informazioni, vedere [Pubblicare applicazioni mediante il proxy di applicazione](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>Passaggio 2: Registrare l'applicazione nativa

È ora necessario registrare l'applicazione in Azure AD, come indicato di seguito:

1. Accedi per il [portale di Azure Active Directory](https://aad.portal.azure.com/). Il **Dashboard** per il **interfaccia di amministrazione di Azure Active Directory** viene visualizzata.
2. Nella barra laterale, selezionare **Azure Active Directory**. Il **Azure Active Directory** verrà visualizzata la pagina di panoramica.
3. Nella barra laterale Panoramica di Azure AD, selezionare **registrazioni per l'App**. Verrà visualizzato l'elenco di tutte le registrazioni per l'app.
4. Selezionare **Nuova registrazione**. Il **registrare un'applicazione** verrà visualizzata la pagina.

   ![Creare una nuova registrazione di app](./media/application-proxy-configure-native-client-application/create.png)
5. Nel **nome** intestazione, specificare un nome visualizzato agli utenti finali per l'applicazione.
6. Sotto il **tipi di account supportati** titolo, selezionare un livello di accesso usando le linee guida:
   - Per impostare come destinazione solo gli account interni all'organizzazione, selezionare **gli account in questa directory dell'organizzazione solo**.
   - Per impostare come destinazione solo aziendali o scolastiche ai clienti, selezionare **gli account in qualsiasi directory dell'organizzazione**.
   - Per indirizzare il set più ampio di identità di Microsoft, selezionare **gli account in qualsiasi directory dell'organizzazione e gli account Microsoft personali**.
7. Nel **URI di reindirizzamento** titolo, selezionare **client pubblico (per dispositivi mobili e desktop)** , quindi digitare l'URI di reindirizzamento per l'applicazione.
8. Seleziona e Leggi i **i criteri della piattaforma Microsoft**, quindi selezionare **registrare**. Una pagina di panoramica per la registrazione nuova applicazione verrà creata e visualizzata.

Per altre informazioni sulla creazione di una registrazione nuova applicazione, vedere [integrazione di applicazioni con Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>Passaggio 3: Concedere l'accesso all'applicazione proxy

Dopo che aver registrato l'applicazione nativa, è possibile assegnare l'accesso ad altre applicazioni nella directory, in questo caso per accedere all'applicazione proxy. Per abilitare l'applicazione nativa ad essere esposto per il proxy di applicazione:

1. Nella barra laterale della pagina di registrazione nuova applicazione, selezionare **le autorizzazioni API**. Il **le autorizzazioni API** pagina per la registrazione nuova applicazione viene visualizzato.
2. Selezionare **Aggiungi un'autorizzazione**. Il **le autorizzazioni richieste API** verrà visualizzata la pagina.
3. Sotto il **selezionare un'API** impostazione, selezionare **API Usa la mia organizzazione**. Viene visualizzato un elenco, che contiene le applicazioni nella directory che espongono le API.
4. Digitare nella casella di ricerca o scorrere per trovare il proxy di applicazione che è stato pubblicato in [passaggio 1: Pubblicare il proxy di applicazione](#step-1-publish-your-proxy-application)e quindi selezionare il proxy di applicazione.
5. Nel **quale tipo di autorizzazioni per l'applicazione richiede?** titolo, selezionare il tipo di autorizzazione. Se l'applicazione nativa deve accedere all'API di application proxy come utente connesso, scegliere **autorizzazioni delegate**. Se l'applicazione nativa viene eseguito come servizio in background o daemon senza utente connesso, scegliere **autorizzazioni applicazione**.
6. Nel **selezionare le autorizzazioni** titolo, selezionare l'autorizzazione desiderata, quindi selezionare **aggiungere autorizzazioni**. Il **le autorizzazioni API** pagina dell'applicazione nativa ora mostrano il proxy dell'applicazione e l'autorizzazione dell'API che è stato aggiunto.

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

Le informazioni necessarie nel codice di esempio sono disponibili nel portale di Azure AD, come indicato di seguito:

| Informazioni necessarie | Procedura per trovarlo nel portale di Azure AD |
| --- | --- |
| \<ID tenant > | **Azure Active Directory** > **delle proprietà** > **ID Directory** |
| \<Url esterno dell'App Proxy > | **Le applicazioni aziendali** > *il proxy di applicazione* > **proxy di applicazione** > **Url esterno** |
| \<ID App dell'app nativa > | **Le applicazioni aziendali** > *dell'applicazione nativa* > **proprietà** > **ID applicazione** |
| \<URI di reindirizzamento dell'App nativa > | **Azure Active Directory** > **registrazioni per l'App** > *l'applicazione nativa* > **URI di reindirizzamento** |
| \<Url del proxy App API > | **Azure Active Directory** > **registrazioni per l'App** > *l'applicazione nativa* > **autorizzazioni delle API**  >  **API o sul nome di autorizzazioni** |

Dopo la modifica di ADAL con questi parametri, possono eseguire l'autenticazione gli utenti alle applicazioni client native, anche quando si trovano all'esterno della rete aziendale.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul flusso dell'applicazione nativa, vedere [App Native in Azure Active Directory](../develop/native-app.md).

Informazioni sulla configurazione di [Single sign-on alle applicazioni in Azure Active Directory](what-is-single-sign-on.md#choosing-a-single-sign-on-method).
