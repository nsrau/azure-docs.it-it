---
title: Registrare un'applicazione SaaS-Azure Marketplace
description: Informazioni su come usare la portale di Azure per registrare un'applicazione SaaS e ricevere un token di sicurezza Azure Active Directory.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: dsindona
ms.openlocfilehash: 630dceedcac36cf6d37d54612d73fabe676d74f6
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85963717"
---
# <a name="register-a-saas-application"></a>Registrare un'applicazione SaaS

Questo articolo illustra come registrare un'applicazione SaaS usando il [portale di Azure](https://portal.azure.com/) Microsoft e come ottenere il token di accesso dell'editore (Azure Active Directory token di accesso). Il server di pubblicazione userà questo token per autenticare l'applicazione SaaS chiamando le API di evasione SaaS.  Le API di evasione usano le credenziali client OAuth 2,0 per concedere a Flow negli endpoint Azure Active Directory (v 1.0) per effettuare una richiesta di token di accesso da servizio a servizio.

Azure Marketplace non impone alcun vincolo al metodo di autenticazione usato dal servizio SaaS per gli utenti finali. Il flusso riportato di seguito è necessario solo per l'autenticazione del servizio SaaS in Azure Marketplace.

Per ulteriori informazioni sull'Azure AD (Active Directory), vedere [che cos'è l'autenticazione](../../active-directory/develop/authentication-scenarios.md)?

## <a name="register-an-azure-ad-secured-app"></a>Registrare un'app protetta da Azure AD

Qualsiasi applicazione che vuole usare le funzionalità di Azure AD deve prima essere registrata in un tenant di Azure AD. Questo processo di registrazione comporta l'assegnazione di Azure AD alcuni dettagli sull'applicazione. Per registrare una nuova applicazione nel portale di Azure, seguire i passaggi seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Se l'account consente di accedere a più di uno, fare clic sull'account nell'angolo in alto a destra e impostare la sessione del portale sul tenant di Azure AD desiderato.
3. Nel riquadro di spostamento a sinistra fare clic sul servizio **Azure Active Directory**, fare clic su **Registrazioni per l'app** e fare clic su **Registrazione nuova applicazione**.

    ![Registrazioni di App SaaS AD](./media/saas-offer-app-registration-v1.png)

4. Nella pagina Crea, immettere le informazioni di registrazione\' dell'applicazione:
    -   **Nome:** Immettere un nome significativo per l'applicazione
    -   **Tipo di applicazione**:  
        
        Selezionare **app Web/API** per [le applicazioni client](../../active-directory/develop/active-directory-dev-glossary.md#client-application)) e [applicazioni per risorse/API](../../active-directory/develop/active-directory-dev-glossary.md#resource-server)) installate in un server protetto. Questa impostazione viene usata per [i client Web](../../active-directory/develop/active-directory-dev-glossary.md#web-client)OAuth riservati) e [i client pubblici basati su agente utente](../../active-directory/develop/active-directory-dev-glossary.md#user-agent-based-client)).
        La stessa applicazione può anche esporre sia un'API client che un'API di risorse.

        Per esempi specifici di applicazioni Web, vedere le configurazioni guidate introduttive disponibili nella sezione [Introduzione](../../active-directory/develop/quickstart-create-new-tenant.md) della [guida per sviluppatori Azure ad](../../active-directory/develop/index.yml).

5. Al termine, fare clic su **registra**.  Azure AD assegna un *ID applicazione* univoco alla nuova applicazione. Si consiglia di registrare un'app che accede solo all'API e come singolo tenant.

6. Per creare il segreto client, passare alla **pagina certificati & segreti** e fare clic su **+ nuovo segreto client**.  Assicurarsi di copiare il valore del segreto per usarlo nel codice.

Il **Azure ad ID app** è associato all'ID editore, quindi assicurarsi che lo stesso *ID app* venga usato in tutte le offerte.

>[!Note]
>Se un server di pubblicazione dispone di due account diversi nel centro per i partner, è necessario usare due diversi ID app Azure AD.  Ogni account partner nel centro per i partner deve usare un ID app univoco Azure AD per tutte le offerte SaaS pubblicate tramite questo account.

## <a name="how-to-get-the-publishers-authorization-token"></a>Come ottenere il token di autorizzazione dell'editore

Dopo aver registrato l'applicazione, è possibile richiedere a livello di codice il token di autorizzazione dell'editore (Azure AD token di accesso, usando Azure AD endpoint V1). Il server di pubblicazione deve usare questo token quando si chiamano le varie API di evasione SaaS. Questo token è valido solo per un'ora. 

Per altre informazioni su questi token, vedere [Token di accesso di Azure Active Directory](../../active-directory/develop/access-tokens.md).  Si noti che nel flusso sotto il token di endpoint V1 viene usato.

### <a name="get-the-token-with-an-http-post"></a>Ottenere il token con un POST HTTP

#### <a name="http-method"></a>Metodo HTTP

Pubblica<br>

##### <a name="request-url"></a>*Request URL (URL richiesta)* 

`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`

##### <a name="uri-parameter"></a>*Parametro URI*

|  Nome parametro    |  Obbligatoria         |  Descrizione |
|  ---------------   |  ---------------  | ------------ |
|  `tenantId`        |  True      |  ID tenant dell'applicazione AAD registrata. |

##### <a name="request-header"></a>*Intestazione della richiesta*

|  Nome intestazione       |  Obbligatoria         |  Descrizione |
|  ---------------   |  ---------------  | ------------ |
|  `content-type`    |  True      |  Tipo di contenuto associato alla richiesta. Il valore predefinito è `application/x-www-form-urlencoded`. |

##### <a name="request-body"></a>*Corpo della richiesta*

|  Nome proprietà     |  Obbligatoria         |  Descrizione |
|  ---------------   |  ---------------  | ------------ |
|  `grant-type`      |  True      |  Tipo di concessione. Usare `"client_credentials"`. |
|  `client_id`       |  True      |  Identificatore del client/app associato all'app di Azure AD. |
|  `client_secret`   |  True      |  Segreto associato all'app Azure AD. |
|  `resource`        |  True      |  Risorsa di destinazione per cui è richiesto il token. Usare `20e940b3-4c77-4b0b-9a53-9e16a1b010a7` perché in questo caso l'API Saas del Marketplace è sempre la risorsa di destinazione. |

##### <a name="response"></a>*Risposta*

|  Nome     |  Type         |  Description |
|  ------   |  ---------------  | ------------ |
|  200 - OK   |  TokenResponse    |  La richiesta ha avuto esito positivo. |

##### <a name="tokenresponse"></a>*TokenResponse*

Risposta di esempio:

```json
{
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

Il `"access_token"` valore del campo nella risposta è l'oggetto `<access_token>` che viene passato come parametro di autorizzazione quando si chiamano tutte le API di evasione SaaS e di misurazione del Marketplace.

## <a name="next-steps"></a>Passaggi successivi

L'app protetta da Azure AD ora può usare l' [API di evasione Saas versione 2](./pc-saas-fulfillment-api-v2.md).
