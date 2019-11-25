---
title: Come delegare la registrazione utente e la sottoscrizione ai prodotti
description: Informazioni su come delegare la registrazione dell'utente e la sottoscrizione del prodotto a terze parti in Gestione API di Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: a69babdf2fffb4cb9d963f1806f3c85755e50294
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454349"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Come delegare la registrazione utente e la sottoscrizione ai prodotti

Delegation allows you to use your existing website for handling developer sign in/sign up and subscription to products, as opposed to using the built-in functionality in the developer portal. It enables your website to own the user data and perform the validation of these steps in a custom way.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegate-signin-up"></a>Delega dell'accesso e dell'iscrizione degli sviluppatori

To delegate developer, sign in and sign up to your existing website, you'll need to create a special delegation endpoint on your site. It needs to act as the entry-point for any such request initiated from the API Management developer portal.

Il flusso di lavoro finale sarà il seguente:

1. Developer clicks on the sign in or sign up link at the API Management developer portal
2. Il browser viene reindirizzato all'endpoint di delega.
3. Delegation endpoint in return redirects to or presents UI asking user to sign in or sign up
4. In caso di esito positivo, l'utente viene reindirizzato alla pagina del portale per sviluppatori di Gestione API da dove è partito.

Per iniziare, configurare innanzitutto Gestione API per indirizzare le richieste tramite l'endpoint di delega. In the Azure portal, search for **Security** in your API Management resource and then click the **Delegation** item. Click the checkbox to enable 'Delegate sign in & sign up'.

![Pagina Delega][api-management-delegation-signin-up]

* Stabilire l'URL dell'endpoint di delega speciale e immetterlo nel campo **URL dell'endpoint delega** . 
* Nel campo Delegation authentication key (Chiave di autenticazione delega) immettere una chiave privata da usare per calcolare una firma fornita per la verifica in modo da garantire che la richiesta provenga effettivamente da Gestione API di Azure. Facendo clic sul pulsante **Genera**, Gestione API genererà una chiave in modo casuale.

È ora necessario creare l'**endpoint di delega**, che dovrà eseguire diverse operazioni:

1. Ricevere una richiesta nel formato seguente:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL of source page}&salt={string}&sig={string}*
   > 
   > 
   
    Query parameters for the sign in / sign up case:
   
   * **operation**: identifica il tipo di richiesta di delega; in questo caso può essere solo di tipo **SignIn**
   * **returnUrl**: the URL of the page where the user clicked on a sign in or sign up link
   * **salt**: stringa salt speciale usata per il calcolo di un hash di sicurezza
   * **sig**: hash di sicurezza calcolato da usare per il confronto con il proprio hash calcolato
2. Verificare che la richiesta provenga da Gestione API di Azure. Questa operazione è facoltativa ma altamente consigliata per motivi di sicurezza.
   
   * Calcolare un hash HMAC-SHA512 di una stringa in base ai parametri di query **returnUrl** e **salt** ([il codice di esempio è fornito di seguito]):
     
     > HMAC(**salt** + '\n' + **returnUrl**)
     > 
     > 
   * Confrontare l'hash sopra calcolato con il valore del parametro di query **sig** . Se i due hash corrispondono, procedere con il passaggio successivo; in caso contrario, negare la richiesta.
3. Verify that you are receiving a request for sign in/sign up: the **operation** query parameter will be set to "**SignIn**".
4. Present the user with UI to sign in or sign up
5. Se l'utente sta effettuando un'iscrizione, è necessario creare un account corrispondente in Gestione API. [Creare un utente] con l'API REST di Gestione API. When doing so, ensure that you set the user ID to the same value as in your user store or to an ID that you can keep track of.
6. Quando l'utente viene autenticato correttamente:
   
   * [Richiedere un token SSO (Single Sign-On)] tramite l'API REST di Gestione API
   * Aggiungere un parametro di query returnUrl all'URL SSO ricevuto dalla chiamata API precedente:
     
     > ad esempio https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * Reindirizzare l'utente all'URL generato in precedenza

Oltre all'operazione **SignIn**, è anche possibile eseguire la gestione degli account seguendo i passaggi precedenti e usando una delle operazioni seguenti:

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**

È necessario passare i parametri di query seguenti per le operazioni di gestione di account.

* **operation**: identifica il tipo di richiesta di delega (ChangePassword, ChangeProfile o CloseAccount)
* **userId**: the user ID of the account to manage
* **salt**: stringa salt speciale usata per il calcolo di un hash di sicurezza
* **sig**: hash di sicurezza calcolato da usare per il confronto con il proprio hash calcolato

## <a name="delegate-product-subscription"></a>Delega della sottoscrizione ai prodotti
Delegating product subscription works similarly to delegating user sign in/-up. Il flusso di lavoro finale sarà il seguente:

1. Developer selects a product in the API Management developer portal and clicks on the Subscribe button.
2. Browser is redirected to the delegation endpoint.
3. Delegation endpoint performs required product subscription steps. It's up to you to design the steps. They may include redirecting to another page to request billing information, asking additional questions, or simply storing the information and not requiring any user action.

Per abilitare la funzionalità, nella pagina **Delega** fare clic su **Delega sottoscrizione ai prodotti**.

Next, ensure the delegation endpoint does the following actions:

1. Ricevere una richiesta nel formato seguente:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation={operation}&productId={product to subscribe to}&userId={user making request}&salt={string}&sig={string}*
   >
   
    Parametri di query per la sottoscrizione ai prodotti:
   
   * **operation**: identifica il tipo di richiesta di delega. Per le richieste di sottoscrizione ai prodotti le opzioni valide sono:
     * "Subscribe": richiesta di sottoscrizione a un prodotto specifico con l'ID fornito (vedere sotto)
     * "Unsubscribe": richiesta di annullamento della sottoscrizione a un prodotto
     * "Renew": richiesta di rinnovo di una sottoscrizione, ad esempio perché è scaduta
   * **productId**: ID del prodotto a cui effettuare la sottoscrizione
   * **subscriptionId**: on *Unsubscribe* and *Renew* - the ID of the product subscription
   * **userId**: the ID of the user the request is made for
   * **salt**: stringa salt speciale usata per il calcolo di un hash di sicurezza
   * **sig**: hash di sicurezza calcolato da usare per il confronto con il proprio hash calcolato

2. Verificare che la richiesta provenga da Gestione API di Azure. Questa operazione è facoltativa ma altamente consigliata per motivi di sicurezza.
   
   * Compute an HMAC-SHA512 of a string based on the **productId**, **userId**, and **salt** query parameters:
     
     > HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
     > 
     > 
   * Confrontare l'hash sopra calcolato con il valore del parametro di query **sig** . Se i due hash corrispondono, procedere con il passaggio successivo; in caso contrario, negare la richiesta.
3. Process product subscription based on the type of operation requested in **operation** - for example, billing, further questions, etc.
4. On successfully subscribing the user to the product on your side, subscribe the user to the API Management product by [calling the REST API for subscriptions].

## <a name="delegate-example-code"></a> Codice di esempio

These code samples show how to:

* Take the *delegation validation key*, which is set in the Delegation screen of the publisher portal
* Create an HMAC, which is then used to validate the signature, proving the validity of the passed returnUrl.

Lo stesso codice funziona per productId e userId con leggeri modifiche.

**Codice C# per generare l'hash di returnUrl**

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**Codice NodeJS per generare l'hash di returnUrl**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

> [!IMPORTANT]
> You need to [republish the developer portal](api-management-howto-developer-portal-customize.md#publish) for the delegation changes to take effect.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla delega, vedere il video seguente:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[Richiedere un token SSO (Single Sign-On)]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/User/GenerateSsoUrl
[Creare un utente]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user/createorupdate
[calling the REST API for subscriptions]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/subscription/createorupdate
[Next steps]: #next-steps
[il codice di esempio è fornito di seguito]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
