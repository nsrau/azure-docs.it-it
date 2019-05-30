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
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: 2fad585e1d37694e37c219be210f9521dbbda3a0
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241644"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Come delegare la registrazione utente e la sottoscrizione ai prodotti

La delega consente di utilizzare il sito Web esistente per la gestione di accesso per gli sviluppatori / effettuare l'iscrizione e sottoscrizione ai prodotti, invece di usare la funzionalità incorporata nel portale per sviluppatori. Ciò consente al sito Web di avere la proprietà dei dati utente e di eseguire la convalida di questi passaggi in modo personalizzato.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegate-signin-up"> </a>Delega per gli sviluppatori l'accesso ed effettuare l'iscrizione

Per delegare l'accesso per gli sviluppatori ed effettuare l'iscrizione al sito Web esistente, è necessario creare un endpoint di delega speciale nel sito. Deve agire come punto di ingresso per qualsiasi richiesta avviata dal portale per sviluppatori di gestione API.

Il flusso di lavoro finale sarà il seguente:

1. Collegamento al portale per sviluppatori di gestione API di iscrizione o lo sviluppatore fa clic su Accedi
2. Il browser viene reindirizzato all'endpoint di delega.
3. Endpoint di delega in o reindirizza a presenta un'interfaccia utente richiesta utente per accedere o iscriversi
4. In caso di esito positivo, l'utente viene reindirizzato alla pagina del portale per sviluppatori di Gestione API da dove è partito.

Per iniziare, configurare innanzitutto Gestione API per indirizzare le richieste tramite l'endpoint di delega. Nel portale di pubblicazione Gestione API fare clic su **Sicurezza** e selezionare la scheda **Delega**. Fare clic sulla casella di controllo per abilitare 'Delegare l'accesso e iscrizione'.

![Pagina Delega][api-management-delegation-signin-up]

* Stabilire l'URL dell'endpoint di delega speciale e immetterlo nel campo **URL dell'endpoint delega** . 
* Nel campo Delegation authentication key (Chiave di autenticazione delega) immettere una chiave privata da usare per calcolare una firma fornita per la verifica in modo da garantire che la richiesta provenga effettivamente da Gestione API di Azure. Facendo clic sul pulsante **Genera**, Gestione API genererà una chiave in modo casuale.

È ora necessario creare l'**endpoint di delega**, che dovrà eseguire diverse operazioni:

1. Ricevere una richiesta nel formato seguente:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL of source page}&salt={string}&sig={string}*
   > 
   > 
   
    Parametri di query per l'accesso o l'iscrizione:
   
   * **operation**: identifica il tipo di richiesta di delega; in questo caso può essere solo di tipo **SignIn**
   * **returnUrl**: l'URL della pagina in cui l'utente fa clic su una richiesta di accesso o iscrizione collegamento
   * **salt**: stringa salt speciale usata per il calcolo di un hash di sicurezza
   * **sig**: hash di sicurezza calcolato da usare per il confronto con il proprio hash calcolato
2. Verificare che la richiesta provenga da Gestione API di Azure. Questa operazione è facoltativa ma altamente consigliata per motivi di sicurezza.
   
   * Calcolare un hash HMAC-SHA512 di una stringa in base ai parametri di query **returnUrl** e **salt** ([il codice di esempio è fornito di seguito]):
     
     > HMAC(**salt** + '\n' + **returnUrl**)
     > 
     > 
   * Confrontare l'hash sopra calcolato con il valore del parametro di query **sig** . Se i due hash corrispondono, procedere con il passaggio successivo; in caso contrario, negare la richiesta.
3. Verificare che riceve una richiesta per l'accesso / iscrizione: i **operazione** parametro di query verrà impostato su "**SignIn**".
4. Presentare all'utente con l'interfaccia utente di accedere o iscriversi
5. Se l'utente sta effettuando un'iscrizione, è necessario creare un account corrispondente in Gestione API. [Creare un utente] con l'API REST di Gestione API. In tal caso, assicurarsi di impostare l'ID utente sullo stesso valore come l'archivio utente o a un ID che è possibile tenere traccia di.
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

## <a name="delegate-product-subscription"></a>Delega della sottoscrizione ai prodotti
Delega sottoscrizione ai prodotti funziona in modo analogo alla delega dell'accesso dell'utente o dell'iscrizione. Il flusso di lavoro finale sarà il seguente:

1. Sviluppatore seleziona un prodotto del portale per sviluppatori di gestione API e fa clic sul pulsante "Subscribe".
2. Browser viene reindirizzato all'endpoint di delega.
3. Endpoint di delega esegue i passaggi di sottoscrizione del prodotto richiesto. Spetta all'utente per i passaggi di progettazione. Possono includere il reindirizzamento a un'altra pagina per richiedere le informazioni di fatturazione, porre altre domande, o semplicemente se archiviare le informazioni e che non richiedono intervento dell'utente.

Per abilitare la funzionalità, nella pagina **Delega** fare clic su **Delega sottoscrizione ai prodotti**.

Verificare quindi che l'endpoint di delega esegue le azioni seguenti:

1. Ricevere una richiesta nel formato seguente:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation= {operazione} & productId = {prodotto per la sottoscrizione a} & userId = {utente che effettua richiesta} & salt = {string} & sig = {string}*
   >
   
    Parametri di query per la sottoscrizione ai prodotti:
   
   * **operation**: identifica il tipo di richiesta di delega. Per le richieste di sottoscrizione ai prodotti le opzioni valide sono:
     * "Subscribe": richiesta di sottoscrizione a un prodotto specifico con l'ID fornito (vedere sotto)
     * "Unsubscribe": richiesta di annullamento della sottoscrizione a un prodotto
     * "Renew": richiesta di rinnovo di una sottoscrizione, ad esempio perché è scaduta
   * **productId**: ID del prodotto a cui effettuare la sottoscrizione
   * **subscriptionId**: in *Unsubscribe* e *Renew* -l'ID della sottoscrizione del prodotto
   * **userId**: l'ID dell'utente viene effettuata la richiesta per
   * **salt**: stringa salt speciale usata per il calcolo di un hash di sicurezza
   * **sig**: hash di sicurezza calcolato da usare per il confronto con il proprio hash calcolato

2. Verificare che la richiesta provenga da Gestione API di Azure. Questa operazione è facoltativa ma altamente consigliata per motivi di sicurezza.
   
   * Calcolare un hash HMAC-SHA512 di una stringa in base il **productId**, **userId**, e **salt** parametri di query:
     
     > HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
     > 
     > 
   * Confrontare l'hash sopra calcolato con il valore del parametro di query **sig** . Se i due hash corrispondono, procedere con il passaggio successivo; in caso contrario, negare la richiesta.
3. Sottoscrizione al prodotto in base al tipo di operazione richiesta in elaborazione **operazione** , ad esempio fatturazione, altre domande e così via.
4. Dopo la corretta sottoscrizione utente per il prodotto sul lato dell'utente, effettuare la sottoscrizione al prodotto in Gestione API da [chiamare l'API REST per le sottoscrizioni].

## <a name="delegate-example-code"></a> Codice di esempio

Questi mostra esempi di codice come per:

* Sfruttare il *chiave di convalida delega*, impostata nella schermata delega del portale di pubblicazione
* Creare un hash HMAC che viene quindi usato per convalidare la firma, dimostrando la validità del returnUrl passato.

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

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla delega, vedere il video seguente:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[Richiedere un token SSO (Single Sign-On)]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/User/GenerateSsoUrl
[Creare un utente]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user/createorupdate
[chiamare l'API REST per le sottoscrizioni]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/subscription/createorupdate
[Next steps]: #next-steps
[il codice di esempio è fornito di seguito]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
