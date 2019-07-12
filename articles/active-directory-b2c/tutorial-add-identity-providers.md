---
title: 'Esercitazione: aggiungere i provider di identità alle applicazioni - Azure Active Directory B2C'
description: Informazioni su come aggiungere provider di identità alle applicazioni in Azure Active Directory B2C usando il portale di Azure.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 33f595dd36ac9448cc1276647f9943326b0b74c1
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655229"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Esercitazione: Aggiungere provider di identità alle applicazioni in Azure Active Directory B2C

È possibile consentire agli utenti di accedere alle applicazioni con provider di identità diversi. Un *provider di identità* crea, mantiene e gestisce le informazioni sulle identità fornendo al contempo servizi di autenticazione alle applicazioni. È possibile aggiungere provider di identità supportati da Azure Active Directory (Azure AD) B2C ai propri [flussi utente](active-directory-b2c-reference-policies.md) usando il portale di Azure.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare le applicazioni dei provider di identità
> * Aggiungere i provider di identità al tenant
> * Aggiungere i provider di identità al flusso utente

In genere si usa un solo provider di identità nelle applicazioni, ma è possibile aggiungerne altri. Questa esercitazione illustra come aggiungere un provider di identità di Azure AD e un provider di identità di Facebook all'applicazione. L'aggiunta di entrambi i provider di identità all'applicazione è facoltativa. È anche possibile aggiungere altri provider di identità, ad esempio [Amazon](active-directory-b2c-setup-amzn-app.md), [GitHub](active-directory-b2c-setup-github-app.md), [Google](active-directory-b2c-setup-goog-app.md), [LinkedIn](active-directory-b2c-setup-li-app.md), [Microsoft](active-directory-b2c-setup-msa-app.md), oppure [Twitter](active-directory-b2c-setup-twitter-app.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

[Creare un flusso utente](tutorial-create-user-flows.md) per consentire agli utenti di iscriversi e accedere all'applicazione.

## <a name="create-applications"></a>Creare applicazioni

Le applicazioni di provider di identità forniscono l'identificatore e la chiave necessari per consentire la comunicazione con il tenant di Azure AD B2C. In questa sezione dell'esercitazione si creeranno un'applicazione di Azure AD e un'applicazione di Facebook da cui ottenere gli identificatori e le chiavi per aggiungere i provider di identità al tenant. Se si intende aggiungere solo uno dei provider di identità, occorre creare solo l'applicazione per tale provider.

### <a name="create-an-azure-active-directory-application"></a>Creare un'applicazione Azure Active Directory

Per consentire agli utenti di accedere da Azure AD, è necessario registrare un'applicazione dall'interno del tenant di Azure AD. Il tenant di Azure AD non corrisponde al tenant di Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory contenente il tenant di Azure AD facendo clic sul **filtro per directory e sottoscrizione** nel menu in alto e scegliendo tale directory.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
1. Selezionare **Nuova registrazione**.
1. Immettere un nome per l'applicazione. Ad esempio `Azure AD B2C App`.
1. Accettare la selezione dei **gli account in questa directory dell'organizzazione solo** per questa applicazione.
1. Per il **URI di reindirizzamento**, accettare il valore **Web** e immettere l'URL seguente in lettere minuscole, sostituendo `your-B2C-tenant-name` con il nome del tenant di Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Ad esempio `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Tutti gli URL dovrebbero ora usare [b2clogin.com](b2clogin.md).

1. Selezionare **registrare**, quindi registrare il **ID applicazione (client)** utilizzabile in un passaggio successivo.
1. Sotto **Manage** nel menu dell'applicazione, selezionare **i certificati e i segreti**, quindi selezionare **nuovo segreto client**.
1. Immettere un **descrizione** per il segreto client. Ad esempio `Azure AD B2C App Secret`.
1. Selezionare il periodo di scadenza. Per questa applicazione, la selezione di accettare **tra 1 anno**.
1. Selezionare **Add**, quindi prendere nota del valore del nuovo segreto client utilizzabile in un passaggio successivo.

### <a name="create-a-facebook-application"></a>Creare un'applicazione Facebook

Per usare un account Facebook come provider di identità in Azure AD B2C, è necessario creare un'applicazione di Facebook. Se non si possiede già un account Facebook, è possibile crearlo sul sito [https://www.facebook.com/](https://www.facebook.com/).

1. Accedere al [sito Web Facebook for developers](https://developers.facebook.com/) con le credenziali dell'account Facebook.
1. Se non è ancora stato fatto, è necessario registrarsi come sviluppatore Facebook. A questo scopo, selezionare **iniziare a usare** nell'angolo superiore destro della pagina, accettare i criteri di Facebook e completare la procedura di registrazione.
1. Selezionare **alle App personali** e quindi **creare App**.
1. Inserire un **nome visualizzato** e una **email di contatto** valida.
1. Fare clic su **Crea ID app**. Potrebbe essere necessario accettare i criteri della piattaforma Facebook e completare un controllo di sicurezza online.
1. Selezionare **Impostazioni** > **Base**.
1. Scegliere una **Categoria**, ad esempio `Business and Pages`. Questo valore è richiesto da Facebook, ma non viene usato da Azure AD B2C.
1. Nella parte inferiore della pagina, selezionare **Aggiungi piattaforma**, quindi selezionare **Sito Web**.
1. In **URL sito**, immettere `https://your-tenant-name.b2clogin.com/` sostituendo `your-tenant-name` con il nome del tenant.
1. Immettere un URL per l'**URL dell'Informativa sulla privacy**, ad esempio `http://www.contoso.com/`. Privacy policy URL è una pagina da mantenere per fornire informazioni sulla privacy per l'applicazione.
1. Selezionare **Save changes** (Salva modifiche).
1. Nella parte superiore della pagina, prendere nota del valore di **App ID**.
1. Accanto a **segreto App**, selezionare **mostrano** e registrare il relativo valore. Si usa l'ID App e segreto dell'App per configurare Facebook come provider di identità nel tenant. **Segreto dell'app** è un'importante credenziale di sicurezza che è necessario archiviare in modo sicuro.
1. Selezionare il segno più accanto a **prodotti**, quindi in **account di accesso di Facebook**, selezionare **configurare**.
1. Sotto **account di accesso di Facebook** nel menu a sinistra, selezionare **impostazioni**.
1. In **Valid OAuth redirect URIs** (URI di reindirizzamento OAuth valide) immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Sostituire `your-tenant-name` con il nome del tenant. Selezionare **Save Changes** nella parte inferiore della pagina.
1. Per rendere disponibile l'applicazione di Facebook per Azure AD B2C, fare clic sul **lo stato** selettore nella parte superiore destra della pagina e trasformarli **sul** rendere pubblico l'applicazione e quindi fare clic su **Confirm** . A questo punto lo stato dovrebbe cambiare da **Sviluppo** a **Live**.

## <a name="add-the-identity-providers"></a>Aggiungere i provider di identità

Dopo aver creato l'applicazione per il provider di identità che si vuole aggiungere, occorre aggiungere il provider di identità al tenant.

### <a name="add-the-azure-active-directory-identity-provider"></a>Aggiungere il provider di identità di Azure Active Directory

1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C facendo clic sul **filtro per directory e sottoscrizione** nel menu in alto e scegliendo tale directory.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Provider di identità** e quindi selezionare **Aggiungi**.
1. Immettere un **Nome**. Ad esempio, immettere *Contoso Azure AD*.
1. Fare clic su **Tipo di provider di identità**, selezionare **OpenID Connect (anteprima)** e quindi fare clic su **OK**.
1. Fare clic su **Configura questo provider di identità**
1. Per la **url dei metadati**, immettere l'URL seguente, sostituendo `your-AD-tenant-domain` con il nome di dominio del tenant di Azure AD.

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Ad esempio `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

1. Per la **ID Client**, immettere il *ID applicazione (client)* registrato in precedenza.
1. Per la **segreto Client**, immettere il *privata del client* valore registrato in precedenza.
1. Facoltativamente immettere un valore per **Domain_hint**. Ad esempio `ContosoAD`. [Gli hint di dominio](../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) sono direttive incluse nella richiesta di autenticazione da un'applicazione. Possono essere usate per accelerare l'indirizzamento dell'utente alla rispettiva pagina di accesso dell'IdP federato. Oppure possono essere usati da un'applicazione multi-tenant per accelerare l'utente direttamente verso la pagina di accesso di Azure AD personalizzata per il suo tenant.
1. Selezionare **OK**.
1. Selezionare **Esegui mapping delle attestazioni di questo provider di identità** e impostare le attestazioni seguenti:

    - Per **ID utente** immettere `oid`.
    - Per **nome visualizzato** immettere `name`.
    - Per **Nome** immettere `given_name`.
    - Per **Cognome** immettere `family_name`.
    - In **Posta elettronica** immettere `unique_name`.

1. Selezionare **OK**, quindi selezionare **crea** per salvare la configurazione.

### <a name="add-the-facebook-identity-provider"></a>Aggiungere il provider di identità Facebook

1. Selezionare **Provider di identità** e quindi selezionare **Aggiungi**.
1. Immettere un **Nome**. Ad esempio, immettere *Facebook*.
1. Selezionare **tipo di provider di identità**, selezionare **Facebook**, quindi selezionare **OK**.
1. Selezionare **configura questo provider di identità** e immettere il *App ID* annotato in precedenza come il **ID Client**.
1. Immettere il *segreto App* registrato come il **privata del Client**.
1. Selezionare **OK** e quindi selezionare **crea** per salvare la configurazione di Facebook.

## <a name="update-the-user-flow"></a>Aggiornare il flusso utente

Nell'esercitazione completata come parte dei prerequisiti è stato creato un flusso utente per l'iscrizione e l'accesso denominato *B2C_1_signupsignin1*. In questa sezione si aggiungeranno i provider di identità al flusso utente *B2C_1_signupsignin1*.

1. Selezionare **Flussi utente (criteri)** e quindi il flusso utente *B2C_1_signupsignin1*.
2. Selezionare **Provider di identità** e quindi selezionare i provider di identità **Facebook** e **Contoso Azure AD** aggiunti.
3. Selezionare **Salva**.

## <a name="test-the-user-flow"></a>Testare il flusso utente

1. Nella pagina Panoramica del flusso utente creato selezionare **Esegui il flusso utente**.
1. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Selezionare **eseguire il flusso utente**e quindi accedere con un provider di identità aggiunto in precedenza.
1. Ripetere i passaggi da 1 a 3 per gli altri provider di identità aggiunti.

Se l'accesso nell'operazione ha esito positivo, si verrà reindirizzati al `https://jwt.ms` che consente di visualizzare la decodifica Token, simile a:

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

Quindi, imparare a personalizzare l'interfaccia utente delle pagine visualizzate agli utenti come parte della propria esperienza di identità nelle applicazioni:

> [!div class="nextstepaction"]
> [Personalizzare l'interfaccia utente delle applicazioni in Azure Active Directory B2C](tutorial-customize-ui.md)
