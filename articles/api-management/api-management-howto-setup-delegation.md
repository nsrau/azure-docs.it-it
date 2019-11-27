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

La delega consente di usare il sito Web esistente per gestire l'accesso e l'iscrizione degli sviluppatori e la sottoscrizione ai prodotti, anziché usare la funzionalità incorporata nel portale per sviluppatori. Consente al sito Web di essere proprietario dei dati utente ed eseguire la convalida di questi passaggi in modo personalizzato.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegate-signin-up"></a>Delega dell'accesso e dell'iscrizione degli sviluppatori

Per delegare lo sviluppatore, effettuare l'accesso e accedere al sito Web esistente, è necessario creare un endpoint di delega speciale nel sito. Deve fungere da punto di ingresso per qualsiasi richiesta avviata dal portale per sviluppatori di gestione API.

Il flusso di lavoro finale sarà il seguente:

1. Gli sviluppatori fanno clic sul collegamento per l'accesso o l'iscrizione nel portale per sviluppatori di gestione API
2. Il browser viene reindirizzato all'endpoint di delega.
3. L'endpoint di delega in return reindirizza a o presenta l'interfaccia utente chiedendo all'utente di eseguire l'accesso o l'iscrizione
4. In caso di esito positivo, l'utente viene reindirizzato alla pagina del portale per sviluppatori di Gestione API da dove è partito.

Per iniziare, configurare innanzitutto Gestione API per indirizzare le richieste tramite l'endpoint di delega. Nella portale di Azure cercare **sicurezza** nella risorsa gestione API e quindi fare clic sull'elemento **delega** . Fare clic sulla casella di controllo per abilitare ' delega accesso & iscriversi '.

![Pagina Delega][api-management-delegation-signin-up]

* Stabilire l'URL dell'endpoint di delega speciale e immetterlo nel campo **URL dell'endpoint delega** . 
* Nel campo Delegation authentication key (Chiave di autenticazione delega) immettere una chiave privata da usare per calcolare una firma fornita per la verifica in modo da garantire che la richiesta provenga effettivamente da Gestione API di Azure. Facendo clic sul pulsante **Genera**, Gestione API genererà una chiave in modo casuale.

È ora necessario creare l'**endpoint di delega**, che dovrà eseguire diverse operazioni:

1. Ricevere una richiesta nel formato seguente:
   
   > *http:\//www.yourwebsite.com/apimdelegation? Operation = SignIn & returnUrl = {URL della pagina di origine} & Salt = {String} & sig = {String}*
   > 
   > 
   
    Parametri di query per il caso di accesso/iscrizione:
   
   * **operation**: identifica il tipo di richiesta di delega; in questo caso può essere solo di tipo **SignIn**
   * **ReturnUrl**: URL della pagina in cui l'utente ha fatto clic su un collegamento per l'accesso o l'iscrizione
   * **salt**: stringa salt speciale usata per il calcolo di un hash di sicurezza
   * **sig**: hash di sicurezza calcolato da usare per il confronto con il proprio hash calcolato
2. Verificare che la richiesta provenga da Gestione API di Azure. Questa operazione è facoltativa ma altamente consigliata per motivi di sicurezza.
   
   * Calcolare un hash HMAC-SHA512 di una stringa in base ai parametri di query **returnUrl** e **salt** ([il codice di esempio è fornito di seguito]):
     
     > HMAC(**salt** + '\n' + **returnUrl**)
     > 
     > 
   * Confrontare l'hash sopra calcolato con il valore del parametro di query **sig** . Se i due hash corrispondono, procedere con il passaggio successivo; in caso contrario, negare la richiesta.
3. Verificare di ricevere una richiesta di accesso/iscrizione: il parametro di query dell' **operazione** verrà impostato su "**SignIn**".
4. Presentare all'utente l'interfaccia utente per l'accesso o l'iscrizione
5. Se l'utente sta effettuando un'iscrizione, è necessario creare un account corrispondente in Gestione API. [Creare un utente] con l'API REST di Gestione API. Quando si esegue questa operazione, assicurarsi di impostare l'ID utente sullo stesso valore dell'archivio utente o su un ID di cui è possibile tenere traccia.
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
* **userid**: ID utente dell'account da gestire
* **salt**: stringa salt speciale usata per il calcolo di un hash di sicurezza
* **sig**: hash di sicurezza calcolato da usare per il confronto con il proprio hash calcolato

## <a name="delegate-product-subscription"> </a>Delega della sottoscrizione ai prodotti
La delega della sottoscrizione del prodotto funziona in modo analogo alla delega dell'accesso utente. Il flusso di lavoro finale sarà il seguente:

1. Developer seleziona un prodotto nel portale per sviluppatori di gestione API e fa clic sul pulsante Subscribe (sottoscrizione).
2. Il browser viene reindirizzato all'endpoint di delega.
3. L'endpoint di delega esegue i passaggi necessari per la sottoscrizione al prodotto. È necessario progettare i passaggi. Possono includere il reindirizzamento a un'altra pagina per richiedere informazioni di fatturazione, porre domande aggiuntive o semplicemente archiviare le informazioni e non richiedere alcuna azione da parte dell'utente.

Per abilitare la funzionalità, nella pagina **Delega** fare clic su **Delega sottoscrizione ai prodotti**.

Assicurarsi quindi che l'endpoint di delega esegue le azioni seguenti:

1. Ricevere una richiesta nel formato seguente:
   
   > *http:\//www.yourwebsite.com/apimdelegation? Operation = {Operation} & productId = {prodotto per sottoscrivere} & userId = {utente che effettua la richiesta} & Salt = {String} & sig = {String}*
   >
   
    Parametri di query per la sottoscrizione ai prodotti:
   
   * **operation**: identifica il tipo di richiesta di delega. Per le richieste di sottoscrizione ai prodotti le opzioni valide sono:
     * "Subscribe": richiesta di sottoscrizione a un prodotto specifico con l'ID fornito (vedere sotto)
     * "Unsubscribe": richiesta di annullamento della sottoscrizione a un prodotto
     * "Renew": richiesta di rinnovo di una sottoscrizione, ad esempio perché è scaduta
   * **productId**: ID del prodotto a cui effettuare la sottoscrizione
   * **SubscriptionId**: su *Annulla sottoscrizione* e *rinnovo* -ID della sottoscrizione del prodotto
   * **userid**: ID dell'utente per cui viene effettuata la richiesta
   * **salt**: stringa salt speciale usata per il calcolo di un hash di sicurezza
   * **sig**: hash di sicurezza calcolato da usare per il confronto con il proprio hash calcolato

2. Verificare che la richiesta provenga da Gestione API di Azure. Questa operazione è facoltativa ma altamente consigliata per motivi di sicurezza.
   
   * Calcolare un HMAC-SHA512 di una stringa in base ai parametri di query **ProductID**, **userid**e **Salt** :
     
     > HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
     > 
     > 
   * Confrontare l'hash sopra calcolato con il valore del parametro di query **sig** . Se i due hash corrispondono, procedere con il passaggio successivo; in caso contrario, negare la richiesta.
3. Elaborare la sottoscrizione del prodotto in base al tipo di operazione richiesta in **Operation** , ad esempio fatturazione, altre domande e così via.
4. Quando si sottoscrive correttamente l'utente al prodotto, sottoscrivere l'utente al prodotto gestione API [chiamata dell'API REST per le sottoscrizioni].

## <a name="delegate-example-code"></a> Codice di esempio

Questi esempi di codice illustrano come:

* Prendere la *chiave di convalida della delega*, impostata nella schermata delega del portale di pubblicazione
* Creare un HMAC, che viene quindi usato per convalidare la firma, dimostrando la validità del returnUrl passato.

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
> Per rendere effettive le modifiche della delega è necessario [ripubblicare il portale per sviluppatori](api-management-howto-developer-portal-customize.md#publish) .

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla delega, vedere il video seguente:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[Richiedere un token SSO (Single Sign-On)]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/User/GenerateSsoUrl
[Creare un utente]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user/createorupdate
[chiamata dell'API REST per le sottoscrizioni]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/subscription/createorupdate
[Next steps]: #next-steps
[il codice di esempio è fornito di seguito]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
