<properties pageTitle="How to delegate user registration and product subscription" metaKeywords="" description="Learn how to delegate user registration and product subscription to a third party in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to delegate user registration and product subscription in Azure API Management" authors="antonba" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="antonba" />

# Come delegare la registrazione utente e la sottoscrizione ai prodotti

La delega consente di usare il sito Web esistente per gestire l'accesso e l'iscrizione degli sviluppatori e la sottoscrizione ai prodotti invece di usare la funzionalità incorporata nel portale per sviluppatori. Ciò consente al sito Web di avere la proprietà dei dati utente e di eseguire la convalida di questi passaggi in modo personalizzato.

## Contenuto dell'argomento

-   [Delega dell'accesso e dell'iscrizione degli sviluppatori][Delega dell'accesso e dell'iscrizione degli sviluppatori]
-   [Delega della sottoscrizione ai prodotti][Delega della sottoscrizione ai prodotti]

## <a name="delegate-signin-up"> </a>Delega dell'accesso e dell'iscrizione degli sviluppatori

Per delegare l'accesso e l'iscrizione degli sviluppatori al sito Web esistente, è necessario creare un endpoint di delega speciale nel sito che funge da punto di ingresso per qualsiasi richiesta avviata dal portale per sviluppatori di Gestione API.

Il flusso di lavoro finale sarà il seguente:

1.  Lo sviluppatore fa clic sul collegamento per l'accesso o l'iscrizione nel portale per sviluppatori di Gestione API.
2.  Il browser viene reindirizzato all'endpoint di delega.
3.  L'endpoint di delega visualizza o reindirizza a sua volta all'interfaccia utente in cui viene richiesto di effettuare l'accesso o l'iscrizione.
4.  In caso di esito positivo, l'utente viene reindirizzato alla pagina del portale per sviluppatori di Gestione API da dove è partito.

Per iniziare, configurare innanzitutto Gestione API per indirizzare le richieste tramite l'endpoint di delega. Nel portale per sviluppatori di Gestione API, sotto l'intestazione **Portale per sviluppatori** nel menu a sinistra, fare clic su **Delega**, quindi su **Delega accesso e iscrizione**.

![Pagina Delega][Pagina Delega]

-   Stabilire l'URL dell'endpoint di delega speciale e immetterlo nel campo **URL dell'endpoint delega**.

-   Nel campo **Chiave di autenticazione delega** immettere una chiave privata da usare per calcolare una firma fornita per verifica in modo da garantire che la richiesta provenga effettivamente da Gestione API di Azure.

È ora necessario creare l'**endpoint di delega**, che dovrà eseguire diverse operazioni:

1.  Ricevere una richiesta nel formato seguente:

    > *<http://www.yourwebsite.com/apimdelegation?operation=SignIn&redirectUrl>={URL della pagina di origine}&salt={stringa}&sid={stringa}*

    Parametri di query per l'accesso o l'iscrizione:

    -   **operation**: identifica il tipo di richiesta di delega; in questo caso può essere solo "SignIn"
    -   **redirectUrl**: URL della pagina in cui l'utente ha fatto clic sul collegamento per l'accesso o l'iscrizione
    -   **salt**: stringa salt speciale usata per il calcolo di un hash di sicurezza
    -   **sig**: hash di sicurezza calcolato da usare per il confronto con il proprio hash calcolato

2.  Verificare che la richiesta provenga da Gestione API di Azure. Questa operazione è facoltativa ma altamente consigliata per motivi di sicurezza.

    -   Calcolare un hash HMAC-SHA512 di una stringa in base ai parametri di query **redirectUrl** e **salt**:

        > HMAC(**redirectUrl** + '\\n' + **salt**)

    -   Confrontare l'hash sopra calcolato con il valore del parametro di query **sig**. Se i due hash corrispondono, procedere con il passaggio successivo; in caso contrario, negare la richiesta.

3.  Verificare la ricezione di una richiesta di accesso o iscrizione: il parametro di query **operation** verrà impostato su "**SignIn**".

4.  Visualizzare l'interfaccia utente per effettuare l'accesso o l'iscrizione.

5.  Se l'utente sta effettuando un'iscrizione, è necessario creare un account corrispondente in Gestione API. [Creare un utente][Creare un utente] con l'API REST di Gestione API. Quando si esegue questa operazione, assicurarsi di impostare l'ID utente sullo stesso valore presente nell'archivio utenti o su un ID di cui è possibile tenere traccia.

6.  Quando l'utente viene autenticato correttamente:

    -   [Richiedere un token SSO (Single Sign-On)][Richiedere un token SSO (Single Sign-On)] tramite l'API REST di Gestione API

    -   Aggiungere un parametro di query returnUrl all'URL SSO ricevuto dalla chiamata API precedente:

        > ad esempio, <https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url>

    -   Reindirizzare l'utente all'URL generato in precedenza

## <a name="delegate-product-subscription"> </a>Delega della sottoscrizione ai prodotti

La delega della sottoscrizione ai prodotti funziona in modo analogo alla delega dell'accesso o dell'iscrizione. Il flusso di lavoro finale sarà il seguente:

1.  Lo sviluppatore seleziona un prodotto nel portale per sviluppatori di Gestione API e fa clic sul pulsante Sottoscrivi.
2.  Il browser viene reindirizzato all'endpoint di delega.
3.  L'endpoint di delega esegue i passaggi necessari per la sottoscrizione ai prodotti. È possibile scegliere i passaggi da eseguire, ad esempio se reindirizzare a un'altra pagina per richiedere le informazioni di fatturazione, se porre altre domande o semplicemente se archiviare le informazioni senza richiedere alcun intervento da parte dell'utente.
4.  In caso di esito positivo, l'utente viene reindirizzato alla pagina del portale per sviluppatori di Gestione API da dove è partito.

Per abilitare la funzionalità, nella pagina **Delega** fare clic su **Delega sottoscrizione ai prodotti**.

Assicurarsi quindi che l'endpoint di delega esegua le operazioni seguenti:

1.  Ricevere una richiesta nel formato seguente:

    > *<http://www.yourwebsite.com/apimdelegation?operation={operazione}&productId={prodotto per il quale effettuare la sottoscrizione}&userId={utente che esegue la richiesta}&salt={stringa}&sid={stringa}*

    Parametri di query per la sottoscrizione ai prodotti:

    -   **operation**: identifica il tipo di richiesta di delega. Per le richieste di sottoscrizione ai prodotti le opzioni valide sono:

        -   "Sottoscrivi": richiesta di sottoscrizione a un prodotto specifico con l'ID fornito (vedere sotto)
        -   "Annulla sottoscrizione": richiesta di annullamento della sottoscrizione a un prodotto
        -   "Rinnova": richiesta di rinnovo di una sottoscrizione, ad esempio perché è scaduta
    -   **productId**: ID del prodotto a cui effettuare la sottoscrizione
    -   **userId**: ID dell'utente per il quale viene effettuata la richiesta
    -   **salt**: stringa salt speciale usata per il calcolo di un hash di sicurezza
    -   **sig**: hash di sicurezza calcolato da usare per il confronto con il proprio hash calcolato

2.  Verificare che la richiesta provenga da Gestione API di Azure. Questa operazione è facoltativa ma altamente consigliata per motivi di sicurezza.

    -   Calcolare un hash HMAC-SHA512 di una stringa in base ai parametri di query **productId**, **userId** e **salt**:

        > HMAC(**productId** + '\\n' + **userId** + '\\n' + **salt**)

    -   Confrontare l'hash sopra calcolato con il valore del parametro di query **sig**. Se i due hash corrispondono, procedere con il passaggio successivo; in caso contrario, negare la richiesta.

3.  Eseguire una qualsiasi elaborazione della sottoscrizione al prodotto in base al tipo di operazione richiesta in **operation**, ad esempio fatturazione, altre domande e così via.

4.  Dopo la corretta sottoscrizione al prodotto in locale, effettuare la sottoscrizione al prodotto in Gestione API [chiamando l'API REST per la sottoscrizione al prodotto][chiamando l'API REST per la sottoscrizione al prodotto].

5.  Reindirizzare l'utente all'URL **redirectUrl** fornito alla ricezione della richiesta.

  [Delega dell'accesso e dell'iscrizione degli sviluppatori]: #delegate-signin-up
  [Delega della sottoscrizione ai prodotti]: #delegate-product-subscription
  [Pagina Delega]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png
  [Creare un utente]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
  [chiamando l'API REST per la sottoscrizione al prodotto]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
