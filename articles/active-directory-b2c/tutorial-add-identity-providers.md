---
title: Esercitazione - Aggiungere provider di identità alle applicazioni - Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come aggiungere provider di identità alle applicazioni in Azure Active Directory B2C usando il portale di Azure.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/01/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: c1bd78442f6a7218dde3710236766117816c42fe
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64710144"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Esercitazione: Aggiungere provider di identità alle applicazioni in Azure Active Directory B2C

È possibile consentire agli utenti di accedere alle applicazioni con provider di identità diversi. Un *provider di identità* crea, mantiene e gestisce le informazioni sulle identità fornendo al contempo servizi di autenticazione alle applicazioni. È possibile aggiungere provider di identità supportati da Azure Active Directory (Azure AD) B2C ai propri [flussi utente](active-directory-b2c-reference-policies.md) usando il portale di Azure.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare le applicazioni dei provider di identità
> * Aggiungere i provider di identità al tenant
> * Aggiungere i provider di identità al flusso utente

In genere si usa un solo provider di identità nelle applicazioni, ma è possibile aggiungerne altri. Questa esercitazione illustra come aggiungere un provider di identità di Azure AD e un provider di identità di Facebook all'applicazione. L'aggiunta di entrambi i provider di identità all'applicazione è facoltativa. Si possono aggiungere anche altri provider di identità, come [Amazon](active-directory-b2c-setup-amzn-app.md), [GitHub](active-directory-b2c-setup-github-app.md), [Google](active-directory-b2c-setup-goog-app.md), [LinkedIn](active-directory-b2c-setup-li-app.md), [Microsoft](active-directory-b2c-setup-msa-app.md) o [Twitter](active-directory-b2c-setup-twitter-app.md). 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

[Creare un flusso utente](tutorial-create-user-flows.md) per consentire agli utenti di iscriversi e accedere all'applicazione. 

## <a name="create-applications"></a>Creare applicazioni

Le applicazioni di provider di identità forniscono l'identificatore e la chiave necessari per consentire la comunicazione con il tenant di Azure AD B2C. In questa sezione dell'esercitazione si creeranno un'applicazione di Azure AD e un'applicazione di Facebook da cui ottenere gli identificatori e le chiavi per aggiungere i provider di identità al tenant. Se si intende aggiungere solo uno dei provider di identità, occorre creare solo l'applicazione per tale provider.

### <a name="create-an-azure-active-directory-application"></a>Creare un'applicazione Azure Active Directory

Per consentire agli utenti di accedere da Azure AD, è necessario registrare un'applicazione dall'interno del tenant di Azure AD. Il tenant di Azure AD non corrisponde al tenant di Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi di usare la directory contenente il tenant di Azure AD facendo clic sul **filtro per directory e sottoscrizione** nel menu in alto e scegliendo tale directory.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
4. Selezionare **Registrazione nuova applicazione**.
5. Immettere un nome per l'applicazione. Ad esempio: `Azure AD B2C App`.
6. In **Tipo di applicazione** selezionare `Web app / API`.
7. In **URL di accesso** immettere l'URL seguente, interamente in lettere minuscole, sostituendo `your-B2C-tenant-name` con il nome del tenant di Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```
    
    Ad esempio: `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
    
    Tutti gli URL dovrebbero ora usare [b2clogin.com](b2clogin.md).

8. Fare clic su **Create**(Crea). Copiare l'**ID applicazione**, che dovrà essere usato in seguito.
9. Selezionare l'applicazione e quindi **Impostazioni**.
10. Selezionare **Chiavi**, immettere la descrizione della chiave, selezionare una durata e quindi fare clic su **Salva**. Copiare il valore della chiave per poterlo usare successivamente nel corso dell'esercitazione.

### <a name="create-a-facebook-application"></a>Creare un'applicazione Facebook

Per usare un account Facebook come provider di identità in Azure AD B2C, è necessario creare un'applicazione di Facebook. Se non si possiede già un account Facebook, è possibile crearlo sul sito [https://www.facebook.com/](https://www.facebook.com/).

1. Accedere al [sito Web Facebook for developers](https://developers.facebook.com/) con le credenziali dell'account Facebook.
2. Se non è ancora stato fatto, è necessario registrarsi come sviluppatore Facebook. A tale scopo, selezionare **Registrati** nell'angolo in alto a destra della pagina, accettare le condizioni di Facebook e completare la procedura di registrazione.
3. Selezionare **My Apps** (App personali) e quindi **Add a new App** (Aggiungi una nuova app). 
4. Inserire un **nome visualizzato** e una **email di contatto** valida.
5. Fare clic su **Crea ID app**. Potrebbe essere necessario accettare i criteri della piattaforma Facebook e completare un controllo di sicurezza online.
6. Selezionare **Impostazioni** > **Base**.
7. Scegliere una **Categoria**, ad esempio `Business and Pages`. Questo valore è richiesto da Facebook, ma non usato per Azure AD B2C.
8. Nella parte inferiore della pagina, selezionare **Aggiungi piattaforma**, quindi selezionare **Sito Web**.
9. In **URL sito**, immettere `https://your-tenant-name.b2clogin.com/` sostituendo `your-tenant-name` con il nome del tenant. Immettere un URL per l'**URL dell'Informativa sulla privacy**, ad esempio `http://www.contoso.com`. L'URL del criterio è una pagina da mantenere per fornire informazioni sulla privacy per l'applicazione.
10. Selezionare **Save changes** (Salva modifiche).
11. Nella parte superiore della pagina copiare l'**ID app**. 
12. Fare clic su **Show** (Mostra) e copiare il valore **App Secret** (Segreto app). Sono necessari entrambi per configurare Facebook come provider di identità nel tenant. **App Segreta** è una credenziale di sicurezza importante.
13. Selezionare **Prodotti**, quindi selezionare **Configura** sotto **Facebook Login** (Accesso a Facebook).
14. Selezionare **Impostazioni** sotto **Facebook Login** (Accesso a Facebook).
15. In **Valid OAuth redirect URIs** (URI di reindirizzamento OAuth valide) immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Sostituire `your-tenant-name` con il nome del tenant. Fare clic su **Salva le modifiche** nella parte inferiore della pagina.
16. Per rendere disponibile l'applicazione Facebook per Azure AD B2C, fare clic sul selettore di **stato** nella parte superiore destra della pagina e **attivarlo**. Fare clic su **Conferma**. A questo punto lo stato dovrebbe cambiare da **Sviluppo** a **Live**.

## <a name="add-the-identity-providers"></a>Aggiungere i provider di identità

Dopo aver creato l'applicazione per il provider di identità che si vuole aggiungere, occorre aggiungere il provider di identità al tenant.

### <a name="add-the-azure-active-directory-identity-provider"></a>Aggiungere il provider di identità di Azure Active Directory

1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C facendo clic sul **filtro per directory e sottoscrizione** nel menu in alto e scegliendo tale directory.
2. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
3. Selezionare **Provider di identità** e quindi selezionare **Aggiungi**.
4. Immettere un **Nome**. Ad esempio, immettere *Contoso Azure AD*.
5. Fare clic su **Tipo di provider di identità**, selezionare **OpenID Connect (anteprima)** e quindi fare clic su **OK**.
6. Fare clic su **Configura questo provider di identità**
7. In **URL dei metadati** immettere l'URL seguente, sostituendo `your-AD-tenant-domain` con il nome di dominio del tenant di Azure AD.

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Ad esempio: `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

8. In **ID client** e **Segreto client** immettere rispettivamente l'ID applicazione e il valore della chiave registrati in precedenza.
9. Facoltativamente immettere un valore per **Domain_hint**. Ad esempio: `ContosoAD`. 
10. Fare clic su **OK**.
11. Selezionare **Esegui mapping delle attestazioni di questo provider di identità** e impostare le attestazioni seguenti:
    
    - Per **ID utente** immettere `oid`.
    - Per **nome visualizzato** immettere `name`.
    - Per **Nome** immettere `given_name`.
    - Per **Cognome** immettere `family_name`.
    - In **Posta elettronica** immettere `unique_name`.

12. Fare clic su **OK** e quindi su **Crea** per salvare la configurazione.

### <a name="add-the-facebook-identity-provider"></a>Aggiungere il provider di identità Facebook

1. Selezionare **Provider di identità** e quindi selezionare **Aggiungi**.
2. Immettere un **Nome**. Ad esempio, immettere *Facebook*.
3. Fare clic su **Identity provider type** (Tipo di provider di identità), selezionare **Facebook** e scegliere **OK**.
4. Selezionare **Configura questo provider di identità** e immettere l'ID app registrato in precedenza come **ID client**. Immettere il segreto dell'app registrato in precedenza come **Segreto client**.
5. Fare clic su **OK** e quindi su **Create** (Crea) per salvare la configurazione di Facebook.

## <a name="update-the-user-flow"></a>Aggiornare il flusso utente

Nell'esercitazione completata come parte dei prerequisiti è stato creato un flusso utente per l'iscrizione e l'accesso denominato *B2C_1_signupsignin1*. In questa sezione si aggiungeranno i provider di identità al flusso utente *B2C_1_signupsignin1*.

1. Selezionare **Flussi utente (criteri)** e quindi il flusso utente *B2C_1_signupsignin1*.
2. Selezionare **Provider di identità** e quindi selezionare i provider di identità **Facebook** e **Contoso Azure AD** aggiunti.
3. Selezionare **Salva**.

### <a name="test-the-user-flow"></a>Testare il flusso utente

1. Nella pagina Panoramica del flusso utente creato selezionare **Esegui il flusso utente**.
2. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
3. Fare clic su **Esegui il flusso utente** e quindi accedere con un provider di identità aggiunto in precedenza.
4. Ripetere i passaggi da 1 a 3 per gli altri provider di identità aggiunti.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Creare le applicazioni dei provider di identità
> * Aggiungere i provider di identità al tenant
> * Aggiungere i provider di identità al flusso utente

> [!div class="nextstepaction"]
> [Personalizzare l'interfaccia utente delle applicazioni in Azure Active Directory B2C](tutorial-customize-ui.md)