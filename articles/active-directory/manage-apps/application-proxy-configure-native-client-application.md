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
ms.openlocfilehash: 2cbee6bfcca3ddb356abe9dceab2fca07c152b07
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961800"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Come abilitare le applicazioni client native per l'interazione con le applicazioni proxy

È possibile utilizzare il proxy dell'applicazione Azure Active Directory (Azure AD) per pubblicare le app Web, ma può essere utilizzato anche per pubblicare applicazioni client native configurate con la libreria di Autenticazione di Azure AD (ADAL). Le applicazioni client native si differenziano dalle app Web perché vengono installate in un dispositivo, mentre le app Web sono accessibili tramite un browser.

Per supportare le applicazioni client native, il proxy di applicazione accetta token rilasciati Azure AD inviati nell'intestazione. Il servizio proxy di applicazione esegue l'autenticazione per gli utenti. Questa soluzione non usa i token dell'applicazione per l'autenticazione.

![Relazione tra utenti finali, Azure AD e applicazioni pubblicate](./media/application-proxy-configure-native-client-application/richclientflow.png)

Per pubblicare applicazioni native, utilizzare la libreria Autenticazione di Azure AD, che si occupa dell'autenticazione e supporta molti ambienti client. Il proxy di applicazione si integra con [lo scenario Da applicazione nativa ad API Web](../develop/native-app.md).

Questo articolo illustra i quattro passaggi necessari per pubblicare un'applicazione nativa con il proxy di applicazione e Azure AD Authentication Library.

## <a name="step-1-publish-your-proxy-application"></a>Passaggio 1: pubblicare l'applicazione proxy

Pubblicare l'applicazione proxy come qualsiasi altra applicazione e assegnare agli utenti l'accesso all'applicazione. Per altre informazioni, vedere [Pubblicare applicazioni mediante il proxy di applicazione](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>Passaggio 2: registrare l'applicazione nativa

È ora necessario registrare l'applicazione in Azure AD, come indicato di seguito:

1. Accedere al portale di [Azure Active Directory](https://aad.portal.azure.com/). Viene visualizzato il dashboard per l' **interfaccia** di **Amministrazione Azure Active Directory** .
1. Nella barra laterale selezionare **Azure Active Directory**. Viene visualizzata la pagina Panoramica **Azure Active Directory** .
1. Nella barra laterale Azure AD Panoramica selezionare **registrazioni app**. Viene visualizzato l'elenco di tutte le registrazioni dell'app.
1. Selezionare **Nuova registrazione**. Viene visualizzata la pagina **registra un'applicazione** .

   ![Creare una nuova registrazione dell'app nel portale di Azure](./media/application-proxy-configure-native-client-application/create.png)

1. Nell'intestazione **nome** specificare un nome visualizzato per l'utente per l'applicazione.
1. Nell'intestazione **tipi di conto supportati** selezionare un livello di accesso utilizzando le linee guida seguenti:

   - Per avere come destinazione solo gli account interni all'organizzazione, selezionare **account solo in questa directory aziendale**.
   - Per avere come destinazione solo i clienti aziendali o dell'Istituto di istruzione, selezionare **account in qualsiasi directory dell'organizzazione**.
   - Per impostare come destinazione il set più ampio di identità Microsoft, selezionare **account in qualsiasi directory organizzativa e account Microsoft personali**.

1. Nell'intestazione **Reindirizzamento URI** selezionare **client pubblico (Mobile & desktop)** , quindi digitare l'URI di reindirizzamento per l'applicazione.
1. Selezionare e leggere i **criteri della piattaforma Microsoft**e quindi selezionare **registra**. Verrà creata e visualizzata una pagina di panoramica per la registrazione della nuova applicazione.

Per informazioni più dettagliate sulla creazione di una nuova registrazione dell'applicazione, vedere [integrazione di applicazioni con Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>Passaggio 3: concedere l'accesso all'applicazione proxy

Ora che è stata registrata l'applicazione nativa, è possibile concedere l'accesso ad altre applicazioni nella directory, in questo caso per accedere all'applicazione proxy. Per consentire l'esposizione dell'applicazione nativa all'applicazione proxy:

1. Nella barra laterale della pagina registrazione nuova applicazione selezionare **autorizzazioni API**. Viene visualizzata la pagina **autorizzazioni API** per la registrazione della nuova applicazione.
1. Selezionare **Aggiungi un'autorizzazione**. Verrà visualizzata la pagina **autorizzazioni API richiesta** .
1. Nell'impostazione **selezionare un'API** selezionare **le API utilizzate dall'organizzazione**. Viene visualizzato un elenco contenente le applicazioni nella directory che espongono le API.
1. Digitare nella casella di ricerca o scorrere per trovare l'applicazione proxy pubblicata in [passaggio 1: pubblicare l'applicazione proxy](#step-1-publish-your-proxy-application), quindi selezionare l'applicazione proxy.
1. Nell'intestazione specificare il **tipo di autorizzazioni richiesto dall'applicazione** , selezionare il tipo di autorizzazione. Se l'applicazione nativa deve accedere all'API dell'applicazione proxy come utente connesso, scegliere **autorizzazioni delegate**.
1. Nell'intestazione **selezionare le autorizzazioni** selezionare l'autorizzazione desiderata e selezionare **Aggiungi autorizzazioni**. La pagina **autorizzazioni API** per l'applicazione nativa Mostra ora l'applicazione proxy e l'API di autorizzazione aggiunta.

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

| Informazioni obbligatorie | Come trovarlo nel portale di Azure AD |
| --- | --- |
| ID tenant \<> | **Proprietà** > **Azure Active Directory** > **ID directory** |
| \<URL esterno dell'app proxy > | **Applicazioni aziendali** > *l'applicazione proxy* > **proxy di applicazione** > **URL esterno** |
| \<ID app dell'app nativa > | **Le applicazioni aziendali** > le **proprietà** > *dell'applicazione nativa* > **ID applicazione** |
| \<URI di reindirizzamento dell'app nativa > | **Azure Active Directory** > **registrazioni app** > gli **uri di reindirizzamento** *dell'applicazione > nativa* |
| URL dell'API dell'app proxy \<> | **Azure Active Directory** > **registrazioni app** > *l'applicazione nativa* > **autorizzazioni API** > **nome API/autorizzazioni** |

Dopo aver modificato il ADAL con questi parametri, gli utenti possono eseguire l'autenticazione a applicazioni client native anche quando si trovano all'esterno della rete aziendale.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul flusso di applicazioni native, vedere [app native in Azure Active Directory](../develop/native-app.md).

Informazioni sulla configurazione [di Single Sign-on per le applicazioni in Azure Active Directory](what-is-single-sign-on.md#choosing-a-single-sign-on-method).
