# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Chiamare l'API di Microsoft Graph da un'applicazione a singola pagina (SPA) di JavaScript

Questa guida dimostra come un'applicazione a pagina singola JavaScript possa accedere ad account personali, aziendali e dell'istituto di istruzione, ottenere un token di accesso e chiamare l'API Microsoft Graph o altre API che richiedono token di accesso dall'endpoint di Azure Active Directory v2.

### <a name="how-this-guide-works"></a>Come interpretare questa guida

![Funzionamento dell'app di esempio generata da questa guida](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>Altre informazioni

L'applicazione di esempio creata in questa guida consente a una SPA di Javascript di eseguire query nell'API di Microsoft Graph o in un'API Web che accetta token dall'endpoint di Azure Active Directory v2. Per questo scenario, dopo l'accesso di un utente, viene richiesto e aggiunto un token di accesso a richieste HTTP tramite l'intestazione dell'autorizzazione. L'acquisizione e il rinnovo del token vengono gestiti da Microsoft Authentication Library (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Librerie

Questa guida usa la libreria seguente:

|Libreria|DESCRIZIONE|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Authentication Library di Microsoft per l'anteprima di JavaScript|

> [!NOTE]
> *msal.js* specifica come destinazione l'*endpoint di Azure Active Directory v2*, che consente agli account personali, aziendali e dell'istituto di istruzione di eseguire l'accesso e di acquisire i token. L'*endpoint di Azure Active Directory v2* ha [alcune limitazioni](..\articles\active-directory\develop\active-directory-v2-limitations.md). Se si è interessati solo ad account aziendali e dell'istituto di istruzione, usare *adal.js* e l'*endpoint V1*. Per conoscere le differenze tra gli endpoint v1 e v2, vedere il [confronto tra v1 e v2](..\articles\active-directory\develop\active-directory-v2-compare.md).

<!--end-collapse-->
