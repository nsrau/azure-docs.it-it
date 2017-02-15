---
title: Come delegare la registrazione utente e la sottoscrizione ai prodotti
description: Informazioni su come delegare la registrazione dell&quot;utente e la sottoscrizione del prodotto a terze parti in Gestione API di Azure.
services: api-management
documentationcenter: 
author: antonba
manager: erikre
editor: 
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: antonba
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3522d157e19b5202efc61ce38bce216252fbe2e7


---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Come delegare la registrazione utente e la sottoscrizione ai prodotti
La delega consente di usare il sito Web esistente per gestire l'accesso e l'iscrizione degli sviluppatori e la sottoscrizione ai prodotti invece di usare la funzionalità incorporata nel portale per sviluppatori. Ciò consente al sito Web di avere la proprietà dei dati utente e di eseguire la convalida di questi passaggi in modo personalizzato.

## <a name="delegate-signin-up"> </a>Delega dell'accesso e dell'iscrizione degli sviluppatori
Per delegare l'accesso e l'iscrizione degli sviluppatori al sito Web esistente, è necessario creare un endpoint di delega speciale nel sito che funge da punto di ingresso per qualsiasi richiesta avviata dal portale per sviluppatori di Gestione API.

Il flusso di lavoro finale sarà il seguente:

1. Lo sviluppatore fa clic sul collegamento per l'accesso o l'iscrizione nel portale per sviluppatori di Gestione API.
2. Il browser viene reindirizzato all'endpoint di delega.
3. L'endpoint di delega visualizza o reindirizza a sua volta all'interfaccia utente in cui viene richiesto di effettuare l'accesso o l'iscrizione.
4. In caso di esito positivo, l'utente viene reindirizzato alla pagina del portale per sviluppatori di Gestione API da dove è partito.

Per iniziare, configurare innanzitutto Gestione API per indirizzare le richieste tramite l'endpoint di delega. Nel portale di pubblicazione Gestione API fare clic su **Sicurezza** e selezionare la scheda **Delega**. Fare clic sulla casella di controllo per abilitare "Delega accesso e iscrizione".

![Pagina Delega][api-management-delegation-signin-up]

* Stabilire l'URL dell'endpoint di delega speciale e immetterlo nel campo **URL dell'endpoint delega** . 
* Nel campo **Chiave di autenticazione delega** immettere una chiave privata da usare per calcolare una firma fornita per verifica in modo da garantire che la richiesta provenga effettivamente da Gestione API di Azure. È quindi possibile fare clic sul pulsante **genera** in modo che Gestione API generi una chiave in modo casuale.

È ora necessario creare l'**endpoint di delega**, che dovrà eseguire diverse operazioni:

1. Ricevere una richiesta nel formato seguente:
   
   > *http://www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL of source page}&salt={string}&sig={string}*
   > 
   > 
   
    Parametri di query per l'accesso o l'iscrizione:
   
   * **operation**: identifica il tipo di richiesta di delega; in questo caso può essere solo di tipo **SignIn**
   * **returnUrl**: l'URL della pagina in cui l'utente ha selezionato il collegamento per l'accesso o l'iscrizione
   * **salt**: stringa salt speciale usata per il calcolo di un hash di sicurezza
   * **sig**: hash di sicurezza calcolato da usare per il confronto con il proprio hash calcolato
2. Verificare che la richiesta provenga da Gestione API di Azure. Questa operazione è facoltativa ma altamente consigliata per motivi di sicurezza.
   
   * Calcolare un hash HMAC-SHA512 di una stringa in base ai parametri di query **returnUrl** e **salt** ([il codice di esempio è fornito di seguito]):
     
     > HMAC(**salt** + '\n' + **returnUrl**)
     > 
     > 
   * Confrontare l'hash sopra calcolato con il valore del parametro di query **sig** . Se i due hash corrispondono, procedere con il passaggio successivo; in caso contrario, negare la richiesta.
3. Verificare la ricezione di una richiesta di accesso o iscrizione: il parametro di query **operation** verrà impostato su "**SignIn**".
4. Visualizzare l'interfaccia utente per effettuare l'accesso o l'iscrizione.
5. Se l'utente sta effettuando un'iscrizione, è necessario creare un account corrispondente in Gestione API. [Creare un utente] con l'API REST di Gestione API. Quando si esegue questa operazione, assicurarsi di impostare l'ID utente sullo stesso valore presente nell'archivio utenti o su un ID di cui è possibile tenere traccia.
6. Quando l'utente viene autenticato correttamente:
   
   * [Richiedere un token SSO (Single Sign-On)] tramite l'API REST di Gestione API
   * Aggiungere un parametro di query returnUrl all'URL SSO ricevuto dalla chiamata API precedente:
     
     > ad esempio, https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * Reindirizzare l'utente all'URL generato in precedenza

Oltre all'operazione **SignIn** , è anche possibile eseguire la gestione degli account seguendo i passaggi precedenti e usando una delle operazioni seguenti.

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**

È necessario passare i parametri di query seguenti per le operazioni di gestione di account.

* **operation**: identifica il tipo di richiesta di delega (ChangePassword, ChangeProfile o CloseAccount)
* **userId**: ID utente dell'account da gestire
* **salt**: stringa salt speciale usata per il calcolo di un hash di sicurezza
* **sig**: hash di sicurezza calcolato da usare per il confronto con il proprio hash calcolato

## <a name="delegate-product-subscription"> </a>Delega della sottoscrizione ai prodotti
La delega della sottoscrizione ai prodotti funziona in modo analogo alla delega dell'accesso o dell'iscrizione. Il flusso di lavoro finale sarà il seguente:

1. Lo sviluppatore seleziona un prodotto nel portale per sviluppatori di Gestione API e fa clic sul pulsante Sottoscrivi.
2. Il browser viene reindirizzato all'endpoint di delega.
3. L'endpoint di delega esegue i passaggi necessari per la sottoscrizione ai prodotti. È possibile scegliere i passaggi da eseguire, ad esempio se reindirizzare a un'altra pagina per richiedere le informazioni di fatturazione, se porre altre domande o semplicemente se archiviare le informazioni senza richiedere alcun intervento da parte dell'utente.

Per abilitare la funzionalità, nella pagina **Delega** fare clic su **Delega sottoscrizione ai prodotti**.

Assicurarsi quindi che l'endpoint di delega esegua le operazioni seguenti:

1. Ricevere una richiesta nel formato seguente:
   
   > *http://www.yourwebsite.com/apimdelegation?operation={operazione}&productId={prodotto per il quale effettuare la sottoscrizione}&userId={utente che esegue la richiesta}&salt={stringa}&sid={stringa}*
   > 
   > 
   
    Parametri di query per la sottoscrizione ai prodotti:
   
   * **operation**: identifica il tipo di richiesta di delega. Per le richieste di sottoscrizione ai prodotti le opzioni valide sono:
     * "Subscribe": richiesta di sottoscrizione a un prodotto specifico con l'ID fornito (vedere sotto)
     * "Unsubscribe": richiesta di annullamento della sottoscrizione a un prodotto
     * "Renew": richiesta di rinnovo di una sottoscrizione, ad esempio perché è scaduta
   * **productId**: ID del prodotto a cui effettuare la sottoscrizione
   * **userId**: ID dell'utente per il quale viene eseguita la richiesta
   * **salt**: stringa salt speciale usata per il calcolo di un hash di sicurezza
   * **sig**: hash di sicurezza calcolato da usare per il confronto con il proprio hash calcolato
2. Verificare che la richiesta provenga da Gestione API di Azure. Questa operazione è facoltativa ma altamente consigliata per motivi di sicurezza.
   
   * Calcolare un hash HMAC-SHA512 di una stringa in base ai parametri di query **productId**, **userId** e **salt**:
     
     > HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
     > 
     > 
   * Confrontare l'hash sopra calcolato con il valore del parametro di query **sig** . Se i due hash corrispondono, procedere con il passaggio successivo; in caso contrario, negare la richiesta.
3. Eseguire una qualsiasi elaborazione della sottoscrizione al prodotto in base al tipo di operazione richiesta in **operation** , ad esempio fatturazione, altre domande e così via.
4. Dopo la corretta sottoscrizione al prodotto in locale, effettuare la sottoscrizione al prodotto in Gestione API [chiamando l'API REST per la sottoscrizione al prodotto].

## <a name="delegate-example-code"> </a> Codice di esempio
I codici di esempio seguenti illustrano come usare la *chiave di convalida della delega*, impostata nella schermata Delega del portale di pubblicazione, per creare un HMAC che viene quindi usato per convalidare la firma, dimostrando la validità del returnUrl passato. Lo stesso codice funziona per productId e userId con leggeri modifiche.

**Codice C# per generare l'hash di returnUrl**

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


**Codice NodeJS per generare l'hash di returnUrl**

    var crypto = require('crypto');

    var key = 'delegation validation key'; 
    var returnUrl = 'returnUrl query parameter';
    var salt = 'salt query parameter';

    var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
    var digest = hmac.update(salt + '\n' + returnUrl).digest();
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter

    var signature = digest.toString('base64');

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla delega, vedere il video seguente.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delega dell'accesso e dell'iscrizione degli sviluppatori]: #delegate-signin-up
[Delega della sottoscrizione ai prodotti]: #delegate-product-subscription
[Richiedere un token SSO (Single Sign-On)]: http://go.microsoft.com/fwlink/?LinkId=507409
[create a user]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
[chiamando l'API REST per la sottoscrizione al prodotto]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
[Passaggi successivi]: #next-steps
[il codice di esempio è fornito di seguito]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 



<!--HONumber=Nov16_HO3-->


