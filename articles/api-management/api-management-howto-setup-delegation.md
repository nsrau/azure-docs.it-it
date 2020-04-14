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
ms.openlocfilehash: c28872e6cffa973f01b3f5a87c423d9dd93a2aa5
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259103"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Come delegare la registrazione utente e la sottoscrizione ai prodotti

La delega consente di utilizzare il sito Web esistente per gestire l'accesso/iscrizione degli sviluppatori e la sottoscrizione ai prodotti, anziché utilizzare la funzionalità incorporata nel portale per sviluppatori. Consente al sito Web di essere il proprietario dei dati utente ed eseguire la convalida di questi passaggi in modo personalizzato.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegating-developer-sign-in-and-sign-up"></a><a name="delegate-signin-up"> </a>Delega dell'accesso e dell'iscrizione degli sviluppatori

Per delegare lo sviluppatore, accedere e iscriversi al sito Web esistente, è necessario creare un endpoint di delega speciale nel sito. Deve fungere da punto di ingresso per qualsiasi richiesta di questo tipo avviata dal portale per sviluppatori di Gestione API.

Il flusso di lavoro finale sarà il seguente:

1. Lo sviluppatore fa clic sul collegamento Accedi o Iscriviti nel portale per sviluppatori di Gestione API
2. Il browser viene reindirizzato all'endpoint di delega.
3. Endpoint di delega nei reindirizzamenti di ritorno o presenta l'interfaccia utente che chiede all'utente di accedere o iscriversi
4. In caso di esito positivo, l'utente viene reindirizzato alla pagina del portale per sviluppatori di Gestione API da dove è partito.

Per iniziare, configurare innanzitutto Gestione API per indirizzare le richieste tramite l'endpoint di delega. Nel portale di Azure cercare Sicurezza nella risorsa Gestione API e quindi fare clic sull'elemento **Delega.In** the Azure portal, search **for Security** in your API Management resource and then click the Delegation item. Fare clic sulla casella di controllo per abilitare l'opzione 'Accesso delegato & iscriviti'.

![Pagina Delega][api-management-delegation-signin-up]

* Stabilire l'URL dell'endpoint di delega speciale e immetterlo nel campo **URL dell'endpoint delega** . 
* Nel campo Delegation authentication key (Chiave di autenticazione delega) immettere una chiave privata da usare per calcolare una firma fornita per la verifica in modo da garantire che la richiesta provenga effettivamente da Gestione API di Azure. Facendo clic sul pulsante **Genera**, Gestione API genererà una chiave in modo casuale.

È ora necessario creare l'**endpoint di delega**, che dovrà eseguire diverse operazioni:

1. Ricevere una richiesta nel formato seguente:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn&'URL di ritorno: URL della pagina di origine, &salt, stringa &sig, stringa.*
   > 
   > 
   
    Parametri di query per il caso di accesso/iscrizione:
   
   * **operation**: identifica il tipo di richiesta di delega; in questo caso può essere solo di tipo **SignIn**
   * **returnUrl**: l'URL della pagina in cui l'utente ha fatto clic su un collegamento di accesso o di iscrizione
   * **salt**: stringa salt speciale usata per il calcolo di un hash di sicurezza
   * **sig**: hash di sicurezza calcolato da usare per il confronto con il proprio hash calcolato
2. Verificare che la richiesta provenga da Gestione API di Azure. Questa operazione è facoltativa ma altamente consigliata per motivi di sicurezza.
   
   * Calcolare un hash HMAC-SHA512 di una stringa in base ai parametri di query **returnUrl** e **salt** ([il codice di esempio è fornito di seguito]):
     
     > HMAC(**salt** + '\n' + **returnUrl**)
     > 
     > 
   * Confrontare l'hash sopra calcolato con il valore del parametro di query **sig** . Se i due hash corrispondono, procedere con il passaggio successivo; in caso contrario, negare la richiesta.
3. Verificare che si riceva una richiesta di accesso/iscrizione: il parametro di query **dell'operazione** verrà impostato su "**SignIn**".
4. Presentare all'utente l'interfaccia utente per accedere o registrarsi
5. Se l'utente sta effettuando un'iscrizione, è necessario creare un account corrispondente in Gestione API. [Creare un utente] con l'API REST di Gestione API. In questo caso, assicurarsi di impostare l'ID utente sullo stesso valore dell'archivio utente o su un ID di cui è possibile tenere traccia.
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
* **userId**: l'ID utente dell'account da gestire
* **salt**: stringa salt speciale usata per il calcolo di un hash di sicurezza
* **sig**: hash di sicurezza calcolato da usare per il confronto con il proprio hash calcolato

## <a name="delegating-product-subscription"></a><a name="delegate-product-subscription"> </a>Delega della sottoscrizione ai prodotti
La delega dell'abbonamento al prodotto funziona in modo analogo alla delega dell'accesso/accesso dell'utente. Il flusso di lavoro finale sarà il seguente:

1. Lo sviluppatore seleziona un prodotto nel portale per sviluppatori di Gestione API e fa clic sul pulsante Sottoscrivi.
2. Il browser viene reindirizzato all'endpoint di delega.
3. L'endpoint di delega esegue i passaggi di sottoscrizione del prodotto necessari. Sta a te progettare i passaggi. Possono includere il reindirizzamento a un'altra pagina per richiedere informazioni di fatturazione, porre domande aggiuntive o semplicemente memorizzare le informazioni e non richiedere alcuna azione da parte dell'utente.

Per abilitare la funzionalità, nella pagina **Delega** fare clic su **Delega sottoscrizione ai prodotti**.

Assicurarsi quindi che l'endpoint di delega esegua le azioni seguenti:Next, ensure the delegation endpoint does the following actions:

1. Ricevere una richiesta nel formato seguente:
   
   > *http\/: /www.tuositoweb.com/apimdelegation?operation'operation'&productId'product to subscribe to'&userId'user making request'&salt'string'&sig'string'.*
   >
   
    Parametri di query per la sottoscrizione ai prodotti:
   
   * **operation**: identifica il tipo di richiesta di delega. Per le richieste di sottoscrizione ai prodotti le opzioni valide sono:
     * "Subscribe": richiesta di sottoscrizione a un prodotto specifico con l'ID fornito (vedere sotto)
     * "Unsubscribe": richiesta di annullamento della sottoscrizione a un prodotto
     * "Renew": richiesta di rinnovo di una sottoscrizione, ad esempio perché è scaduta
   * **productId**: ID del prodotto a cui effettuare la sottoscrizione
   * **subscriptionId**: su *Unsubscribe* and *Renew* - l'ID dell'abbonamento al prodotto
   * **userId**: l'ID dell'utente per la richiesta per
   * **salt**: stringa salt speciale usata per il calcolo di un hash di sicurezza
   * **sig**: hash di sicurezza calcolato da usare per il confronto con il proprio hash calcolato

2. Verificare che la richiesta provenga da Gestione API di Azure. Questa operazione è facoltativa ma altamente consigliata per motivi di sicurezza.
   
   * Calcolare un HMAC-SHA512 di una stringa in base ai parametri di query **productId**, **userId**e **salt:**
     
     > HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
     > 
     > 
   * Confrontare l'hash sopra calcolato con il valore del parametro di query **sig** . Se i due hash corrispondono, procedere con il passaggio successivo; in caso contrario, negare la richiesta.
3. Elaborare l'abbonamento al prodotto in base al tipo di operazione richiesta in **funzione,** ad esempio fatturazione, ulteriori domande e così via.
4. Al suo abbonamento al prodotto, sottoscrivere l'utente al prodotto Gestione API [chiamando l'API REST per le sottoscrizioni.]

## <a name="example-code"></a><a name="delegate-example-code"> </a> Codice di esempio

Questi esempi di codice illustrano come:These code samples show how to:

* Accettare la chiave di convalida della *delega*, impostata nella schermata Delega del portale dell'editore
* Creare un HMAC, che viene quindi utilizzato per convalidare la firma, dimostrando la validità del returnUrl passato.

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
> È necessario [ripubblicare il portale per sviluppatori](api-management-howto-developer-portal-customize.md#publish) per rendere effettive le modifiche della delega.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla delega, vedere il video seguente:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[Richiedere un token SSO (Single Sign-On)]: https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/User/GenerateSsoUrl
[Creare un utente]: https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/user/createorupdate
[chiamata dell'API REST per le sottoscrizioni]: https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/subscription/createorupdate
[Next steps]: #next-steps
[il codice di esempio è fornito di seguito]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
