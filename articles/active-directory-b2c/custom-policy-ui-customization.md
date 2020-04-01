---
title: Personalizzare l'interfaccia utente dell'app con criteri personalizzatiCustomize the user interface of your app with a custom policy
titleSuffix: Azure AD B2C
description: Informazioni sulla personalizzazione di un'interfaccia utente mediante un criterio personalizzato in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e50d6d0623e87dfa68a7cc9744c3f595ff0179c6
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396377"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Personalizzare l'interfaccia utente dell'applicazione usando un criterio personalizzato in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Completando i passaggi descritti in questo articolo, si crea un criterio personalizzato di iscrizione e accesso con il marchio e l'aspetto. Con Azure Active Directory B2C (Azure AD B2C) si ottiene il controllo quasi completo del contenuto HTML e CSS presentato agli utenti. Quando si usa un criterio personalizzato, si configura la personalizzazione dell'interfaccia utente in XML anziché usare i controlli nel portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Completare la procedura descritta in [Introduzione ai criteri personalizzati](custom-policy-get-started.md). È necessario disporre di un criterio personalizzato di lavoro per l'iscrizione e l'accesso con account locali.

[!INCLUDE [active-directory-b2c-html-how-to](../../includes/active-directory-b2c-html-how-to.md)]

### <a name="4-modify-the-extensions-file"></a>4. Modificare il file delle estensioni

Per configurare la personalizzazione dell'interfaccia utente, copiare **ContentDefinition** e i relativi elementi figlio dal file di base nel file delle estensioni.

1. Aprire il file di base dei criteri, Ad esempio, <em> `SocialAndLocalAccounts/` </em>. Questo file di base è uno dei file di criteri inclusi nello starter pack dei criteri personalizzati, che sarebbe dovuto essere ottenuto nel prerequisito Introduzione ai [criteri personalizzati.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom)
1. Cercare e copiare l'intero contenuto dell'elemento **ContentDefinitions**.
1. Aprire il file di estensione. ad esempio *TrustFrameworkExtensions.xml*. Cercare l'elemento **BuildingBlocks**. Se l'elemento non esiste, aggiungerlo.
1. Incollare l'intero contenuto dell'elemento **ContentDefinitions** copiato come figlio dell'elemento **BuildingBlocks**.
1. Cercare l'elemento **ContentDefinition** che contiene `Id="api.signuporsignin"` nel codice XML copiato.
1. Cambiare il valore di **LoadUri** nell'URL del file HTML caricato nell'archivio. Ad esempio: `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    I criteri personalizzati dovrebbero essere simili al frammento di codice seguente:Your custom policy should look like the following code snippet:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Salvare il file delle estensioni.

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. Caricare e testare i criteri personalizzati aggiornati

#### <a name="51-upload-the-custom-policy"></a>5.1 Caricare il criterio personalizzato

1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Cercare e selezionare **Azure AD B2C**.
1. In **Criteri**selezionare **Framework di esperienza identità**.
1. Selezionare **Carica criterio personalizzato**.
1. Caricare il file delle estensioni modificato in precedenza.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5.2 Testare il criterio personalizzato utilizzando **Esegui ora**

1. Selezionare il criterio caricato e quindi selezionare **Esegui ora**.
1. Dovrebbe essere possibile iscriversi usando un indirizzo di posta elettronica.

[!INCLUDE [active-directory-b2c-html-templates](../../includes/active-directory-b2c-html-templates.md)]

## <a name="configure-dynamic-custom-page-content-uri"></a>Configurare l'URI del contenuto della pagina personalizzata dinamicaConfigure dynamic custom page content URI

Usando i criteri personalizzati di Azure AD B2C, è possibile inviare un parametro nel percorso URL o in una stringa di query. Passando il parametro all'endpoint HTML, è possibile modificare dinamicamente il contenuto della pagina. È ad esempio possibile modificare l'immagine di sfondo della pagina di accesso o di iscrizione ad Azure AD B2C in base a un parametro passato dall'applicazione Web o per dispositivi mobili. Il parametro può essere qualsiasi sistema di [risoluzione delle attestazioni,](claim-resolver-overview.md)ad `campaignId`esempio l'ID applicazione, l'ID lingua o il parametro della stringa di query personalizzata, ad esempio .

### <a name="sending-query-string-parameters"></a>Invio di parametri della stringa di querySending query string parameters

Per inviare i parametri della stringa di query, nei [criteri della relying party](relyingparty.md)aggiungere un `ContentDefinitionParameters` elemento come illustrato di seguito.

```XML
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

Nella definizione del contenuto `LoadUri` modificare `https://<app_name>.azurewebsites.net/home/unified`il valore di in . I criteri `ContentDefinition` personalizzati dovrebbero essere simili al frammento di codice seguente:Your custom policy should look like the following code snippet:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Quando Azure AD B2C carica la pagina, effettua una chiamata all'endpoint del server Web:When Azure AD B2C loads the page, it makes a call to your web server endpoint:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>URI contenuto pagina dinamica

Il contenuto può essere estratto da luoghi diversi in base ai parametri utilizzati. Nell'endpoint abilitato per CORS, impostare una struttura di cartelle per ospitare il contenuto. Ad esempio, è possibile organizzare il contenuto nella struttura seguente. Cartella *radice/cartella per lingua/file html*. Ad esempio, l'URI della pagina personalizzata potrebbe essere simile al seguente:For example, your custom page URI might look like:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C invia il codice ISO `fr` di due lettere per la lingua, per il francese:Azure AD B2C sends the two letter ISO code for the language, for French:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sugli elementi dell'interfaccia utente che possono essere personalizzati, vedere la guida di riferimento per la [personalizzazione dell'interfaccia utente per i flussi utente.](customize-ui-overview.md)
