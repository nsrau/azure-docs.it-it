---
title: Registrare un'applicazione SaaS | Azure Marketplace
description: Viene illustrato come registrare un'applicazione SaaS tramite il portale di Azure.
services: Azure, Marketplace, Cloud Partner Portal, Azure portal
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: pabutler
ms.openlocfilehash: e31efb9a52ff004e6e35ddfc251732c014eedae9
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257478"
---
# <a name="register-a-saas-application"></a>Registrare un'applicazione SaaS

Questo articolo illustra come registrare un'applicazione SaaS con Microsoft [portale di Azure](https://portal.azure.com/).  Dopo la registrazione di un esito positivo, si riceverà un token di sicurezza di Azure Active Directory (Azure AD) che è possibile usare per accedere alle API SaaS evasione degli ordini.  Per altre informazioni su Azure AD, vedere [cos'è l'autenticazione?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)

> [!IMPORTANT] 
> SaaS offrono funzionalità è stata eseguita la migrazione per la [Microsoft Partner Center](https://partner.microsoft.com/dashboard/directory).  Tutti i nuovi server di pubblicazione deve utilizzare Centro per i Partner per la creazione di nuove offerte SaaS e la gestione delle offerte esistenti.  I server di pubblicazione corrente con offerte SaaS batchwise la migrazione dal portale per Cloud Partner per il centro per i Partner.  Il portale Cloud Partner visualizzerà i messaggi di stato per indicare quando specifiche offerte esistenti sono state migrate.
> Per altre informazioni, vedere [creare una nuova offerta SaaS](../../partner-center-portal/create-new-saas-offer.md).


## <a name="service-to-service-authentication-flow"></a>Flusso di autenticazione da servizio a

Il diagramma seguente mostra il flusso di sottoscrizione di un nuovo cliente e quando vengono usate queste API:

![Flusso di API dell'offerta SaaS](./media/saas-offer-publish-api-flow-v1.png)

Azure non impone i vincoli per l'autenticazione che il servizio SaaS espone ai propri utenti finali. Tuttavia, l'autenticazione con le API di evasione SaaS viene eseguita con un token di sicurezza di Azure AD, in genere ottenuto tramite la registrazione dell'app SaaS tramite il portale di Azure. 


## <a name="register-an-azure-ad-secured-app"></a>Registrare un'app protetta AD Azure

Qualsiasi applicazione che vuole usare le funzionalità di Azure AD deve prima essere registrata in un tenant di Azure AD. Questo processo di registrazione comporta l'assegnazione di dettagli di Azure AD sull'applicazione, ad esempio l'URL in cui si trova, l'URL per inviare risposte dopo che un utente viene autenticato, l'URI che identifica l'app e così via.  Per registrare una nuova applicazione nel portale di Azure, seguire i passaggi seguenti:

1.  Accedere al [portale di Azure](https://portal.azure.com/).
2.  Se l'account consente di accedere a più tenant, fare clic sull'account in alto a destra e impostare la sessione del portale sul tenant di Azure AD desiderato.
3.  Nel riquadro di spostamento a sinistra fare clic sul servizio **Azure Active Directory**, fare clic su **Registrazioni per l'app** e fare clic su **Registrazione nuova applicazione**.

    ![Registrazioni di App SaaS AD](./media/saas-offer-app-registration-v1.png)

4.  Nella pagina Crea, immettere le informazioni di registrazione\' dell'applicazione:
    -   **Nome**: immettere un nome significativo per l'applicazione.
    -   **Tipo di applicazione**: 
        - Selezionare **Nativa** per le [applicazioni client](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) che sono installate localmente in un dispositivo. Questa impostazione viene usata per i [client nativi](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) OAuth pubblici.
        - Selezionare **App Web/API** per le [applicazioni client](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) e le [applicazioni della risorsa/API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) installate su un server protetto. Questa impostazione viene utilizzata per i [client Web](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) OAuth riservati e i [client basati su agente utente](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client) pubblici.
        La stessa applicazione può anche esporre sia un'API client che un'API di risorse.
    -   **URL di accesso**: Per le applicazioni API o le app Web, specificare l'URL di base dell'app. Ad esempio, **http://localhost:31544** potrebbe essere l'URL per un'app Web in esecuzione sul computer locale. Gli utenti possono quindi usare questo URL per accedere a un'applicazione client Web.
    -   **URI di reindirizzamento**: per le applicazione native, specificare l'URI usato da Azure AD per restituire le risposte dei token. Immettere un valore specifico per l'applicazione, ad esempio **http://MyFirstAADApp** .

        ![Registrazioni di App SaaS AD](./media/saas-offer-app-registration-v1-2.png)

        Per esempi specifici per le applicazioni web o native, vedere la Guida introduttiva interattiva configurazioni disponibili nel *iniziare a usare* sezione il [Guida per sviluppatori Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

5.  Al termine, fare clic su **Crea**. Azure AD assegna un valore univoco *ID applicazione* per l'applicazione e si\'re impiegato per l'applicazione\'pagina di registrazione principale s. A seconda che si tratti di un'applicazione Web o nativa, sono fornite opzioni diverse per l'aggiunta di altre funzionalità all'applicazione.

>[!Note]
>Per impostazione predefinita, l'applicazione appena registrata viene configurata per consentire solo agli utenti dello stesso tenant di accedere all'applicazione.


## <a name="using-the-azure-ad-security-token"></a>Usando il token di sicurezza di Azure AD

Dopo aver registrato l'applicazione, è possibile richiedere a livello di codice un token di sicurezza di Azure AD.  Per usare questo token e chiedere di risolvere il problema è previsto il server di pubblicazione.  Quando si usano le varie API di evasione degli ordini, il parametro di query di token è nell'URL quando l'utente viene reindirizzato al sito Web di SaaS di Azure.  Questo token è valido solo per un'ora.  Inoltre, è necessario decodificare il valore del token dal browser prima di poterla usare.

Per altre informazioni su questi token, vedere [token di accesso di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Ottenere un token basato sull'app Azure AD

Metodo HTTP

`GET`

*Request URL (URL richiesta)*

**https://login.microsoftonline.com/ *{tenantId}* /oauth2/token**

*Parametro URI*

|  **Nome parametro**  | **Obbligatorio**  | **Descrizione**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| TenantId             | True           | ID tenant dell'applicazione AAD registrata   |
|  |  |  |


*Intestazione della richiesta*

|  **Nome dell'intestazione**  | **Obbligatorio** |  **Descrizione**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | True          | Tipo di contenuto associato alla richiesta. Il valore predefinito è `application/x-www-form-urlencoded`.  |
|  |  |  |


*Corpo della richiesta*

| **Nome proprietà**   | **Obbligatorio** |  **Descrizione**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True          | Tipo di concessione. Il valore predefinito è `client_credentials`.                    |
|  Client_id          | True          |  Identificatore del client/app associato all'app di Azure AD.                  |
|  client_secret      | True          |  Password associata all'app di Azure AD.                               |
|  Resource           | True          |  Risorsa di destinazione per cui è richiesto il token. Il valore predefinito è `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Risposta*

|  **Nome**  | **Tipo**       |  **Descrizione**    |
| ---------- | -------------  | ------------------- |
| 200 - OK    | TokenResponse  | La richiesta ha avuto esito positivo   |
|  |  |  |

*TokenResponse*

Token di risposta di esempio:

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "62d94f6c-d599-489b-a797-3e10e42fbe22",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```


## <a name="next-steps"></a>Passaggi successivi

App protette con AD di Azure ora è possibile usare la [SaaS evasione API versione 2](./cpp-saas-fulfillment-api-v2.md).
