---
title: 'Azure Active Directory B2C: richiesta di token di accesso | Microsoft Docs'
description: In questo articolo viene spiegato come configurare un&quot;applicazione client e acquisire un token di accesso.
services: active-directory-b2c
documentationcenter: android
author: parakhj
manager: krassk
editor: 
ms.assetid: 1c75f17f-5ec5-493a-b906-f543b3b1ea66
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 5d15ad7a4e75410390891b5e11f72c6a649ebf53
ms.lasthandoff: 03/23/2017


---


# <a name="azure-ad-b2c-requesting-access-tokens"></a>Azure AD B2C: richiesta di token di accesso


Un token di accesso (indicato come **access\_token**) è un tipo di token di sicurezza che un client può usare per accedere alle risorse protette da un [server di autorizzazione](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-protocols#the-basics), ad esempio un'API web. I token di accesso sono rappresentati come token [JWT](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#types-of-tokens) e contengono informazioni sui server della risorsa desiderata e le autorizzazioni concesse al server. Quando si chiama il server delle risorse, il token di accesso deve essere presente nella richiesta HTTP.

In questo articolo viene descritto come configurare un'applicazione client e fare in modo che esegua una richiesta di acquisizione di un **access\_token** dagli endpoint `authorize` e `token`.

## <a name="prerequisite"></a>Prerequisito

Prima di richiedere un token di accesso, è necessario innanzitutto registrare un'API web e pubblicare le autorizzazioni che possono essere concesse all'applicazione client. Iniziare seguendo la procedura descritta nella sezione [Register a web API](active-directory-b2c-app-registration.md) (Registrare un'API web).

## <a name="granting-permissions-to-a-web-api"></a>Concessione di autorizzazioni a un'API web

Affinché un'applicazione client ottenga le autorizzazioni specifiche per un'API, è necessario concedere a quest'applicazione le autorizzazioni tramite il portale di Azure. Per concedere autorizzazioni a un'applicazione client:

1. Individuare il menu **Applicazioni** nel pannello delle funzioni B2C.
2. Fare clic sull'applicazione client ([Registrare un'applicazione](active-directory-b2c-app-registration.md) se non se ne possiede una).
3. Selezionare **Accesso all'API**.
4. Fare clic su **Aggiungi**.
5. Selezionare l'API Web e gli ambiti (autorizzazioni) che si desidera concedere.
6. Fare clic su **OK**.

> [!NOTE]
> Azure AD B2C non richiede il consenso agli utenti dell'applicazione client. Al contrario, tutti i consensi vengono concessi dall'amministratore, in base alle autorizzazioni configurate tra le applicazioni descritte in precedenza. Se è stata revocata un'autorizzazione concessa per un'applicazione, tutti gli utenti che in precedenza potevano acquisire tale autorizzazione non saranno più in grado di farlo.

## <a name="requesting-a-token"></a>Richiesta di un token

Per ottenere un token di accesso per un'applicazione della risorsa, l'applicazione client deve specificare le autorizzazioni desiderate nel parametro **scope** della richiesta. Ad esempio, per acquisire l'autorizzazione "lettura" per l'applicazione della risorsa con l'URI ID App di `https://contoso.onmicrosoft.com/notes`, l'ambito sarà `https://contoso.onmicrosoft.com/notes/read`. Di seguito è riportato un esempio di una richiesta di codice di autorizzazione per l'endpoint `authorize`.

```
https://login.microsoftonline.com/<yourTenantId>.onmicrosoft.com/oauth2/v2.0/authorize?p=<yourPolicyId>&client_id=<appID_of_your_client_application>&nonce=anyRandomValue&redirect_uri=<redirect_uri_of_your_client_application>&scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread&response_type=code 
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

È possibile richiedere più ambiti o autorizzazioni per una risorsa di quelli concessi per l'applicazione client. In questo caso, la chiamata avrà esito positivo se almeno un'autorizzazione viene concessa. L'attestazione "scp" dell'**access\_token** risultante sarà popolata con solo le autorizzazioni concesse.

> [!NOTE] 
> La richiesta di autorizzazioni per due risorse Web diverse nella stessa richiesta non è supportata. Questo tipo di richiesta avrà esito negativo.

### <a name="special-cases"></a>Casi speciali

Lo standard OpenID Connect consente di specificare valori speciali diversi per "scope". Gli ambiti speciali seguenti rappresentano l'autorizzazione per "accedere al profilo dell'utente":

* **openid**: richiede un token ID
* **offline\_access**: richiede un token di aggiornamento (mediante i flussi del codice di autorizzazione).

Se il parametro "response\_type" in una richiesta `authorize` include "token", il parametro "scope" deve includere almeno un'autorizzazione della risorsa (diversa da "openid" e "offline\_access") che verrà concessa. In caso contrario, la richiesta `authorize` verrà terminata con un errore.

## <a name="the-returned-token"></a>Token restituito

In un **access\_token** creato correttamente (da un endpoint `authorize` o `token`), saranno presenti le attestazioni seguenti:

| Nome | Attestazione | Descrizione |
| --- | --- | --- |
|Audience |`aud` |L'\*ID applicazione\* della singola risorsa per cui il token concede l'accesso. |
|Scope |`scp` |Le autorizzazioni concesse alla risorsa. Le autorizzazioni multiple concesse saranno separate da spazi. |
|Entità autorizzati |`azp` |L'\*ID applicazione\* dell'applicazione client che ha avviato la richiesta. |

Quando l'API riceve **access\_token**, deve [convalidare il token](active-directory-b2c-reference-tokens.md) per dimostrarne l'autenticità e la presenza dell'attestazione corretta.

Commenti e suggerimenti sono sempre graditi. In caso di difficoltà con questo argomento o di suggerimenti per migliorarne il contenuto, è possibile lasciare un commento nella parte inferiore della pagina. Le richieste di funzionalità possono essere aggiunte in [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
