---
title: Richiesta di token di accesso - Azure AD B2C | Microsoft Docs
description: In questo articolo viene spiegato come configurare un'applicazione client e acquisire un token di accesso.
services: active-directory-b2c
documentationcenter: android
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/09/2017
ms.author: davidmu
ms.openlocfilehash: bd919543072a8d2bf5fb0ebba17e69ba2f467218
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-requesting-access-tokens"></a>Azure AD B2C: richiesta di token di accesso

Un token di accesso, indicato come **access\_token** nelle risposte da Azure AD B2C, è un tipo di token di sicurezza che un client può usare per accedere alle risorse protette da un [server di autorizzazione](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-protocols#the-basics), ad esempio un'API Web. I token di accesso sono rappresentati come token [JWT](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#types-of-tokens) e contengono informazioni sui server della risorsa desiderata e le autorizzazioni concesse al server. Quando si chiama il server delle risorse, il token di accesso deve essere presente nella richiesta HTTP.

Questo articolo descrive come configurare un'applicazione client e un'API Web per ottenere un oggetto **access\_token**.

> [!NOTE]
> **Le catene di API Web (On-Behalf-Of) non sono supportate da Azure Active Directory B2C.**
>
> Molte architetture includono un'API Web che deve chiamare un'altra API Web downstream, entrambe protette da Azure AD B2C. Questo scenario è comune nei client nativi che hanno un back-end dell'API Web, che a sua volta chiama un servizio online Microsoft come l'API Graph di Azure AD.
>
> Questo scenario di API Web concatenate può essere supportato tramite la concessione di credenziali del bearer token JWT di OAuth 2.0, nota anche come flusso On-Behalf-Of. Il flusso On-Behalf-Of, tuttavia, non è attualmente implementato in Azure AD B2C.

## <a name="register-a-web-api-and-publish-permissions"></a>Registrare un'API Web e pubblicare le autorizzazioni

Prima di richiedere un token di accesso, è necessario registrare un'API Web e pubblicare le autorizzazioni (ambiti) che possono essere concesse all'applicazione client.

### <a name="register-a-web-api"></a>Registrare un'API Web

1. Nel menu delle funzionalità di Azure AD B2C nel portale di Azure fare clic su **Applicazioni**.
1. Fare clic su **+Aggiungi** nella parte superiore del menu.
1. Immettere un **nome** per l'applicazione che descriva l'applicazione agli utenti. Ad esempio, è possibile immettere "API Contoso".
1. Attivare/Disattivare l'opzione **Includi app Web/API Web** impostandola su **Sì**.
1. Immettere un valore arbitrario per **URL di risposta**. Ad esempio, immettere `https://localhost:44316/`. Il valore non è importante, perché un'API non dovrà ricevere il token direttamente da Azure AD B2C.
1. Immettere un **URI ID app**. Questo è l'identificatore usato per l'API Web. Ad esempio, immettere "notes" nella casella. Per **URI ID app** il valore sarà quindi `https://{tenantName}.onmicrosoft.com/notes`.
1. Fare clic su **Crea** per registrare l'applicazione.
1. Fare clic sull'applicazione appena creata e copiare l' **ID client applicazione** univoco globale che verrà usato in seguito nel codice.

### <a name="publishing-permissions"></a>Pubblicazione delle autorizzazioni

Gli ambiti, analoghi alle autorizzazioni, sono necessari quando l'app chiama un'API. Alcuni esempi di ambiti sono "read" e "write". Si supponga di voler fare in modo che l'app Web o nativa possa leggere da un'API. L'app chiama Azure AD B2C e richiede un token di accesso che dia accesso all'ambito "read". Perché Azure AD B2C emetta questo token di accesso, l'API specifica deve concedere l'autorizzazione di lettura all'app. A questo scopo, l'API deve prima di tutto pubblicare l'ambito "read".

1. Nel menu **Applicazioni** di Azure AD B2C aprire l'applicazione API Web ("API Contoso").
1. Fare clic su **Ambiti pubblicati**. È possibile definire qui le autorizzazioni (ambiti) che possono essere concesse ad altre applicazioni.
1. Configurare i campi **Valore ambito** nel modo necessario, ad esempio "read". Per impostazione predefinita, verrà definito l'ambito "user_impersonation", che può essere ignorato se lo si desidera. Immettere una descrizione dell'ambito nella colonna **Nome ambito**.
1. Fare clic su **Save**.

> [!IMPORTANT]
> **Nome ambito** è la descrizione di **Valore ambito**. Quando si usa l'ambito, assicurarsi di usare **Valore ambito**.

## <a name="grant-a-native-or-web-app-permissions-to-a-web-api"></a>Concedere a un'app Web o nativa le autorizzazioni per un'API Web

Dopo aver configurato un'API per la pubblicazione degli ambiti, è necessario concedere gli ambiti all'applicazione client tramite il portale di Azure.

1. Passare al menu **Applicazioni** nel menu delle funzionalità di Azure AD B2C.
1. Registrare un'applicazione client ([applicazione web](active-directory-b2c-app-registration.md#register-a-web-app) o [client nativo](active-directory-b2c-app-registration.md#register-a-mobile-or-native-app)) se non è già disponibile. Se si segue questa guida come punto di partenza, sarà necessario registrare un'applicazione client.
1. Fare clic su **Accesso all'API**.
1. Fare clic su **Aggiungi**.
1. Selezionare l'API Web e gli ambiti (autorizzazioni) che si desidera concedere.
1. Fare clic su **OK**.

> [!NOTE]
> Azure AD B2C non richiede il consenso agli utenti dell'applicazione client. Al contrario, tutti i consensi vengono concessi dall'amministratore, in base alle autorizzazioni configurate tra le applicazioni descritte in precedenza. Se è stata revocata un'autorizzazione concessa per un'applicazione, tutti gli utenti che in precedenza potevano acquisire tale autorizzazione non saranno più in grado di farlo.

## <a name="requesting-a-token"></a>Richiesta di un token

Quando si richiede un token di accesso, l'applicazione client deve specificare le autorizzazioni desiderate nel parametro **scope** della richiesta. Ad esempio, per specificare "read" per **Valore ambito** per l'API con **URI ID app** impostato su `https://contoso.onmicrosoft.com/notes`, l'ambito sarà `https://contoso.onmicrosoft.com/notes/read`. Di seguito è riportato un esempio di una richiesta di codice di autorizzazione per l'endpoint `/authorize`.

> [!NOTE]
> Attualmente i domini personalizzati non sono supportati insieme ai token di accesso. È necessario usare il dominio NomeTenant.onmicrosoft.com nell'URL della richiesta.

```
https://login.microsoftonline.com/<tenantName>.onmicrosoft.com/oauth2/v2.0/authorize?p=<yourPolicyId>&client_id=<appID_of_your_client_application>&nonce=anyRandomValue&redirect_uri=<redirect_uri_of_your_client_application>&scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread&response_type=code 
```

Per acquisire più autorizzazioni nella stessa richiesta, è possibile aggiungere più voci nell'unico parametro **scope**, separato da spazi. Ad esempio: 

URL decodificato:

```
scope=https://contoso.onmicrosoft.com/notes/read openid offline_access
```

URL codificato:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread%20openid%20offline_access
```

È possibile richiedere più ambiti (autorizzazioni) per una risorsa rispetto a quelli concessi per l'applicazione client. In questo caso, la chiamata avrà esito positivo se almeno un'autorizzazione viene concessa. L'attestazione "scp" dell'**access\_token** risultante sarà popolata con solo le autorizzazioni concesse.

> [!NOTE] 
> La richiesta di autorizzazioni per due risorse Web diverse nella stessa richiesta non è supportata. Questo tipo di richiesta avrà esito negativo.

### <a name="special-cases"></a>Casi speciali

Lo standard OpenID Connect consente di specificare valori speciali diversi per "scope". Gli ambiti speciali seguenti rappresentano l'autorizzazione per "accedere al profilo dell'utente":

* **openid**: richiede un token ID
* **offline\_access**: richiede un token di aggiornamento (mediante [flussi del codice di autenticazione](active-directory-b2c-reference-oauth-code.md)).

Se il parametro `response_type` in una richiesta `/authorize` include `token`, il parametro `scope` deve includere almeno un ambito delle risorse (diverso da `openid` e `offline_access`) che verrà concesso. In caso contrario, la richiesta `/authorize` verrà terminata con un errore.

## <a name="the-returned-token"></a>Token restituito

In un **access\_token** creato correttamente (da un endpoint `/authorize` o `/token`), saranno presenti le attestazioni seguenti:

| NOME | Attestazione | DESCRIZIONE |
| --- | --- | --- |
|Audience |`aud` |L'**ID applicazione** della singola risorsa per cui il token concede l'accesso. |
|Scope |`scp` |Le autorizzazioni concesse alla risorsa. Le autorizzazioni multiple concesse saranno separate da spazi. |
|Entità autorizzati |`azp` |L'**ID applicazione** dell'applicazione client che ha avviato la richiesta. |

Quando l'API riceve **access\_token**, deve [convalidare il token](active-directory-b2c-reference-tokens.md) per dimostrarne l'autenticità e la presenza dell'attestazione corretta.

Commenti e suggerimenti sono sempre graditi. In caso di difficoltà con questo argomento, inserire un post in Stack Overflow usando il tag ['azure-ad-b2c'](https://stackoverflow.com/questions/tagged/azure-ad-b2c). Le richieste di funzionalità possono essere aggiunte in [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

## <a name="next-steps"></a>Passaggi successivi

* Compilare un'API Web con [.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)
* Compilare un'API Web con [Node.JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)
