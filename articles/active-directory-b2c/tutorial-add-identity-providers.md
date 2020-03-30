---
title: 'Esercitazione: Aggiungere provider di identità alle appTutorial: Add identity providers to your apps'
titleSuffix: Azure AD B2C
description: Informazioni su come aggiungere provider di identità alle applicazioni in Azure Active Directory B2C usando il portale di Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 07/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1f49061210ca8e3c106b0569f77a67d1f10757a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183517"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Esercitazione: Aggiungere provider di identità alle applicazioni in Azure Active Directory B2CTutorial: Add identity providers to your applications in Azure Active Directory B2C

È possibile consentire agli utenti di accedere alle applicazioni con provider di identità diversi. Un *provider di identità* crea, mantiene e gestisce le informazioni sulle identità fornendo al contempo servizi di autenticazione alle applicazioni. È possibile aggiungere provider di identità supportati da Azure Active Directory B2C (Azure AD B2C) ai [flussi utente](user-flow-overview.md) tramite il portale di Azure.You can add identity providers that are supported by Azure Active Directory B2C (Azure AD B2C) to your user flows using the Azure portal.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Creare le applicazioni dei provider di identità
> * Aggiungere i provider di identità al tenant
> * Aggiungere i provider di identità al flusso utente

In genere si usa un solo provider di identità nelle applicazioni, ma è possibile aggiungerne altri. Questa esercitazione illustra come aggiungere un provider di identità di Azure AD e un provider di identità di Facebook all'applicazione. L'aggiunta di entrambi i provider di identità all'applicazione è facoltativa. È inoltre possibile aggiungere altri provider di identità, ad esempio [Amazon](identity-provider-amazon.md), [GitHub](identity-provider-github.md), [Google](identity-provider-google.md), [LinkedIn](identity-provider-linkedin.md), [Microsoft](identity-provider-microsoft-account.md)o [Twitter](identity-provider-twitter.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

[Creare un flusso utente](tutorial-create-user-flows.md) per consentire agli utenti di iscriversi e accedere all'applicazione.

## <a name="create-applications"></a>Creare applicazioni

Le applicazioni di provider di identità forniscono l'identificatore e la chiave necessari per consentire la comunicazione con il tenant di Azure AD B2C. In questa sezione dell'esercitazione si creeranno un'applicazione di Azure AD e un'applicazione di Facebook da cui ottenere gli identificatori e le chiavi per aggiungere i provider di identità al tenant. Se si intende aggiungere solo uno dei provider di identità, occorre creare solo l'applicazione per tale provider.

### <a name="create-an-azure-active-directory-application"></a>Creare un'applicazione Azure Active Directory

Per consentire agli utenti di accedere da Azure AD, è necessario registrare un'applicazione dall'interno del tenant di Azure AD. Il tenant di Azure AD non corrisponde al tenant di Azure AD B2C.

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD selezionando il filtro **Directory e sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant di Azure AD.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
1. Selezionare **Nuova registrazione**.
1. Immettere un nome per l'applicazione. Ad esempio: `Azure AD B2C App`.
1. Accettare la selezione di **Account in questa directory dell'organizzazione solo** per questa applicazione.
1. Per l'URI di **reindirizzamento**, accettare il valore di **Web** `your-B2C-tenant-name` e immettere l'URL seguente in lettere minuscole, sostituendo con il nome del tenant B2C di Azure AD.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Ad esempio: `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Tutti gli URL dovrebbero ora usare [b2clogin.com](b2clogin.md).

1. Selezionare **Registra**, quindi registrare l'ID **applicazione (client)** utilizzato in un passaggio successivo.
1. In **Gestisci** nel menu dell'applicazione selezionare **Certificati & segreti**, quindi Nuovo **segreto client**.
1. Immettere una **Descrizione** per il segreto client. Ad esempio: `Azure AD B2C App Secret`.
1. Selezionare il periodo di scadenza. Per questa applicazione, accettare la selezione di **In 1 anno**.
1. Selezionare **Aggiungi**, quindi registrare il valore del nuovo segreto client utilizzato in un passaggio successivo.

### <a name="create-a-facebook-application"></a>Creare un'applicazione Facebook

Per usare un account Facebook come provider di identità in Azure AD B2C, è necessario creare un'applicazione di Facebook. Se non si dispone già di un account [https://www.facebook.com/](https://www.facebook.com/)Facebook, è possibile ottenerlo a .

1. Accedere al [sito Web Facebook for developers](https://developers.facebook.com/) con le credenziali dell'account Facebook.
1. Se non è ancora stato fatto, è necessario registrarsi come sviluppatore Facebook. A tale scopo, seleziona **Inizia** nell'angolo in alto a destra della pagina, accetta le norme di Facebook e completa i passaggi di registrazione.
1. Selezionare **App personali** e quindi **Crea app**.
1. Inserire un **nome visualizzato** e una **email di contatto** valida.
1. Fare clic su **Crea ID app**. Potrebbe essere necessario accettare i criteri della piattaforma Facebook e completare un controllo di sicurezza online.
1. Selezionare **Impostazioni** > **di base**.
1. Scegliere una **Categoria**, ad esempio `Business and Pages`. Questo valore è richiesto da Facebook, ma non viene usato da Azure AD B2C.
1. Nella parte inferiore della pagina, selezionare **Aggiungi piattaforma**, quindi selezionare **Sito Web**.
1. In **URL sito**, immettere `https://your-tenant-name.b2clogin.com/` sostituendo `your-tenant-name` con il nome del tenant.
1. Immettere un URL per l'**URL dell'Informativa sulla privacy**, ad esempio `http://www.contoso.com/`. L'URL dell'informativa sulla privacy è una pagina che si gestisce per fornire informazioni sulla privacy per l'applicazione.
1. Selezionare **Save changes** (Salva modifiche).
1. Nella parte superiore della pagina registrare il valore di **ID app**.
1. Accanto a **Segreto app**selezionare **Mostra** e registrarne il valore. Utilizzare sia l'ID app che il segreto app per configurare Facebook come provider di identità nel tenant. **Segreto app** è un'importante credenziale di sicurezza che è necessario archiviare in modo sicuro.
1. Seleziona il segno più accanto a **PRODOTTI**, quindi in **Facebook Login**seleziona **Configura**.
1. In **Facebook Login (Accesso a** Facebook) nel menu a sinistra, seleziona **Impostazioni**.
1. In **Valid OAuth redirect URIs** (URI di reindirizzamento OAuth valide) immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Sostituire `your-tenant-name` con il nome del tenant. Seleziona **Salva modifiche** nella parte inferiore della pagina.
1. Per rendere l'applicazione Facebook disponibile per Azure AD B2C, fare clic sul selettore **di stato** nella parte superiore destra della pagina e **attivarla** per rendere pubblica l'applicazione e quindi fare clic su **Conferma**. A questo punto lo stato dovrebbe cambiare da **Sviluppo** a **Live**.

## <a name="add-the-identity-providers"></a>Aggiungere i provider di identità

Dopo aver creato l'applicazione per il provider di identità che si vuole aggiungere, occorre aggiungere il provider di identità al tenant.

### <a name="add-the-azure-active-directory-identity-provider"></a>Aggiungere il provider di identità di Azure Active Directory

1. Assicurarsi di usare la directory che contiene il tenant B2C di Azure AD. Nel menu superiore, selezionare il filtro **Directory e sottoscrizione** e scegliere la directory che contiene il tenant B2C di Azure AD.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Provider di identità**, quindi **nuovo provider OpenID Connect**.
1. Immettere un **Nome**. Ad esempio, immettere *Contoso Azure AD*.
1. Per **URL metadati**immettere `your-AD-tenant-domain` l'URL seguente sostituendo con il nome di dominio del tenant di Azure AD:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Ad esempio: `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

1. Per **ID client**immettere l'ID applicazione registrato in precedenza.
1. Per **Segreto client**immettere il segreto client registrato in precedenza.
1. Lasciare i valori predefiniti per **Ambito**, **Tipo di risposta**e **Modalità risposta**.
1. (Facoltativo) Immettere un valore per **Domain_hint**. Ad esempio, *ContosoAD*. [Gli hint](../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) di dominio sono direttive incluse nella richiesta di autenticazione da un'applicazione. Possono essere usate per accelerare l'indirizzamento dell'utente alla rispettiva pagina di accesso dell'IdP federato. Oppure possono essere usati da un'applicazione multi-tenant per accelerare l'utente direttamente verso la pagina di accesso di Azure AD personalizzata per il suo tenant.
1. In **Mapping attestazioni provider**di identità immettere i valori di mapping delle attestazioni seguenti:

    * **ID utente**: *oid*
    * **Nome visualizzato**: *nome*
    * **Nome dato**: *given_name*
    * **Cognome**: *family_name*
    * **E-mail**: *unique_name*

1. Selezionare **Salva**.

### <a name="add-the-facebook-identity-provider"></a>Aggiungere il provider di identità Facebook

1. Seleziona **Provider di identità**, quindi **Facebook**.
1. Immettere un **Nome**. Ad esempio, *Facebook*.
1. Per ID **client,** immettere l'ID app dell'applicazione Facebook creata in precedenza.
1. Per il **segreto client**, immettere il segreto app registrato.
1. Selezionare **Salva**.

## <a name="update-the-user-flow"></a>Aggiornare il flusso utente

Nell'esercitazione completata come parte dei prerequisiti è stato creato un flusso utente per l'iscrizione e l'accesso denominato *B2C_1_signupsignin1*. In questa sezione si aggiungeranno i provider di identità al flusso utente *B2C_1_signupsignin1*.

1. Selezionare **Flussi utente (criteri)** e quindi il flusso utente *B2C_1_signupsignin1*.
2. Selezionare **Provider di identità** e quindi selezionare i provider di identità **Facebook** e **Contoso Azure AD** aggiunti.
3. Selezionare **Salva**.

## <a name="test-the-user-flow"></a>Testare il flusso utente

1. Nella pagina Panoramica del flusso utente creato selezionare **Esegui il flusso utente**.
1. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Selezionare **Esegui flusso utente**e quindi accedere con un provider di identità aggiunto in precedenza.
1. Ripetere i passaggi da 1 a 3 per gli altri provider di identità aggiunti.

Se l'operazione di accesso ha esito `https://jwt.ms` positivo, si viene reindirizzati a che viene visualizzato il token decodificato, simile al seguente:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "<key-ID>"
}.{
  "exp": 1562346892,
  "nbf": 1562343292,
  "ver": "1.0",
  "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
  "sub": "20000000-0000-0000-0000-000000000000",
  "aud": "30000000-0000-0000-0000-000000000000",
  "nonce": "defaultNonce",
  "iat": 1562343292,
  "auth_time": 1562343292,
  "name": "User Name",
  "idp": "facebook.com",
  "postalCode": "12345",
  "tfp": "B2C_1_signupsignin1"
}.[Signature]
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Creare le applicazioni dei provider di identità
> * Aggiungere i provider di identità al tenant
> * Aggiungere i provider di identità al flusso utente

Successivamente, scopri come personalizzare l'interfaccia utente delle pagine mostrate agli utenti come parte della loro esperienza di identità nelle tue applicazioni:

> [!div class="nextstepaction"]
> [Personalizzare l'interfaccia utente delle applicazioni in Azure Active Directory B2C](tutorial-customize-ui.md)
