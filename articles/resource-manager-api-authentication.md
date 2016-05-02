<properties 
   pageTitle="Autorizzazione con l'API di Resource Manager | Microsoft Azure"
   description="Guida per gli sviluppatori all'autorizzazione con l'API di Azure Resource Manager e Active Directory per l'integrazione di un'app con Azure."
   services="azure-resource-manager,active-directory"
   documentationCenter="na"
   authors="dushyantgill"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/18/2016"
   ms.author="dugill;tomfitz" />


# Guida per gli sviluppatori sull'autorizzazione con l'API di Azure Resource Manager

Questo argomento illustra come eseguire l'autenticazione con le API di Azure Resource Manager agli sviluppatori software che vogliono integrare un'app con Azure o gestire le risorse di Azure del cliente.

L'app può accedere alle API di Resource Manager in due modi:

1. **Accesso utente + app**: usare questo metodo per le app che accedono alle risorse per conto di un utente registrato. Funziona per le app, ad esempio le app Web e gli strumenti da riga di comando, che trattano solo la "gestione interattiva" delle risorse di Azure.
1. **Accesso solo app**: usare questo metodo quando all'identità dell'app deve essere concesso l'accesso diretto alle risorse. Funziona per i servizi daemon e i processi programmati che richiedono un "accesso offline" a lungo termine ad Azure.

Questo argomento contiene una descrizione dettagliata di come creare un'app che usa entrambi i metodi di autorizzazione.

Si compilerà un'applicazione Web che:

1. Fa accedere un utente Azure
2. Esegue una query di Resource Manager per conto dell'utente (accesso utente + app) per ottenere un elenco di sottoscrizioni di Azure di proprietà dell'utente
3. Consente all'utente di "connettere" le sottoscrizioni all'app, concedendo in tal modo l'accesso diretto nella sottoscrizione all'applicazione
4. Accede a Resource Manager come applicazione per eseguire operazioni offline (accesso solo app)

Ecco il flusso end-to-end dell'applicazione Web che si scriverà.

![Autorizzazione ARM - Registrazione dell'app 1](./media/resource-manager-api-authentication/ARM-Auth-Swim-Lane.png)

Tutto il codice di questo argomento è in esecuzione come app Web che è possibile provare all'indirizzo [http://vipswapper.azurewebsites.net/cloudsense](http://vipswapper.azurewebsites.net/cloudsense).

L'utente deve selezionare il tipo di account da usare per l'accesso:

![Selezionare l'accesso](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-1.png)

Fornire le credenziali.

![Fornire le credenziali](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-2.png)

Concedere all'app l'accesso alle sottoscrizioni di Azure:
 
 ![Concedere l'accesso](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-3.png)
 
Connettere le sottoscrizioni all'app per il monitoraggio:

![Connettere la sottoscrizione](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-4.png)

Disconnettere o ripristinare la connessione all'app:

![Disconnettere la sottoscrizione](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-5.png)

## Registrare l'applicazione con Azure Active Directory

Iniziare registrando l'app Web con Azure Active Directory (AD). La registrazione dell'app crea un'identità centrale per l'app in Azure AD, che contiene le informazioni di base sull'applicazione, ad esempio l'ID client OAuth, gli URL di risposta e le credenziali che l'applicazione userà per l'autenticazione e l'accesso alle API di Azure Resource Manager. La registrazione dell'app registra anche le diverse autorizzazioni delegate necessarie all'applicazione quando accede alle API Microsoft per conto dell'utente.

L'argomento [Creare un'applicazione e un'entità servizio di Active Directory tramite il portale](resource-group-create-service-principal-portal.md) illustra tutti i passaggi necessari per configurare l'applicazione. Vedere tale argomento quando si crea un'applicazione con le proprietà seguenti:

- Applicazione Web denominata **CloudSense**
- URL di accesso e URI ID app nel formato **http://{domain_name_of_your_directory}/{name_of_the_app}**.
- Chiave di autenticazione per l'accesso all'applicazione
- Autorizzazione delegata **Access Azure Service Management** per **Azure Service Management API**. Lasciare l'impostazione predefinita **Enable single sign-on and read user's profile** per **Azure Active Directory**.
- Applicazione multi-tenant

### Configurazione facoltativa - Credenziali certificato

Azure AD supporta anche le credenziali certificato per le applicazioni: creare un certificato autofirmato, mantenere la chiave privata e aggiungere la chiave pubblica alla registrazione dell'applicazione Azure AD. Per l'autenticazione, l'applicazione invia un piccolo payload ad Azure AD firmato usando la chiave privata e Azure AD convalida la firma usando la chiave pubblica registrata.

Per informazioni sulla configurazione del certificato, vedere [Compilare app di servizio e daemon in Office 365](https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365). La sezione intitolata "Configurazione di un certificato pubblico X.509 per l'applicazione" contiene le istruzioni dettagliate per la configurazione del certificato. In alternativa, vedere [Autenticazione di un'entità servizio con Azure Resource Manager](resource-group-authenticate-service-principal.md) per esempi di configurazione di un certificato con Azure PowerShell o l'interfaccia della riga di comando.

## Autenticare l'utente e ottenere il token di accesso

Ora si ha tutto quanto è necessario per iniziare a scrivere il codice dell'applicazione. Questo argomento fornisce gli esempi di API REST per ogni passaggio del flusso end-to-end e i collegamenti al codice C# pertinente per ogni passaggio. L'esempio di applicazione ASP.NET MVC completo è disponibile all'indirizzo [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

Si inizia dal punto in cui l'utente decide di connettere la sottoscrizione di Azure all'applicazione.

È necessario chiedere all'utente due cose:

1. **Nome di dominio directory**: nome di dominio della directory Azure Active Directory associata alla sottoscrizione di Azure dell'utente. La richiesta di autorizzazione OAuth 2.0 deve essere inviata a questa directory Azure AD. Per trovare il nome di dominio della directory Azure AD, l'utente deve andare al portale di Azure e selezionare l'account nell'angolo in altro a destra. È possibile fornire all'utente istruzioni visive, ad esempio: 

     ![](./media/resource-manager-api-authentication/show-directory.png)
   
1. **Account Microsoft o account aziendale**: determinare se l'utente gestisce la sottoscrizione di Azure con un account Microsoft (noto anche come Live ID) o con un account aziendale (noto anche come account organizzazione). Se usa un account Microsoft, l'applicazione reindirizzerà l'utente alla pagina di accesso di Azure Active Directory con un parametro della stringa di query (&domain\_hint=live.com) che indicherà ad Azure AD di far passare l'utente direttamente alla pagina di accesso dell'account Microsoft. Il codice di autorizzazione e i token ricevuti per entrambi i tipi di account verranno elaborati nello stesso modo.

L'applicazione reindirizza quindi l'utente ad Azure AD con una richiesta di autorizzazione OAuth 2.0: autenticare le credenziali dell'utente e ricevere un codice di autorizzazione. L'applicazione userà il codice di autorizzazione per ottenere un token di accesso per Resource Manager.

### Richiesta di autorizzazione (OAuth 2.0)

Rilasciare una richiesta di autorizzazione Open ID Connect/OAuth2.0 all'endpoint di autorizzazione di Azure AD:

    http://login.microsoftonline.com/{directory_domain_name}/OAuth2/Authorize

I parametri della stringa di query disponibili per questa richiesta sono descritti nell'argomento [Flusso di concessione del codice di autorizzazione](https://msdn.microsoft.com/library/azure/dn645542.aspx).

L'esempio seguente illustra come richiedere l'autorizzazione OAuth 2.0:

    https://login.windows.net/dushyantgill.com/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Azure AD autentica l'utente e, se necessario, chiede all'utente di concedere l'autorizzazione all'app. Restituisce il codice di autorizzazione all'URL di risposta dell'applicazione. A seconda della response\_mode richiesta, Azure AD invia i dati nella stringa di query o come dati di post.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### Richiesta di autorizzazione (Open ID Connect)

Se non si vuole solo accedere ad Azure Resource Manager per conto dell'utente, ma anche consentire all'utente di accedere all'applicazione usando l'account Azure AD, rilasciare una richiesta di autorizzazione Open ID Connect. Con Open ID Connect, l'applicazione riceverà anche un id\_token da Azure AD, che l'app può usare per far accedere l'utente.

I parametri della stringa di query della richiesta di autorizzazione OAuth 2.0 sono:

| Parametro stringa query | Valore
|----|----
| client\_id | ID client dell'applicazione
| response\_mode | **form\_post** o **query**
| response\_type | **code+id\_token**
| redirect\_uri | URL di risposta codificato dell'applicazione. Ad esempio: http://www.vipswapper.com/cloudsense/Account/SignIn |
| resource | Identificatore codificato URL di Azure Service Management API: https://management.core.windows.net/ |
| scope | openid+profile
| nonce | Parte dei dati per collegare la richiesta di autorizzazione all'id\_token restituito per assicurarsi che la risposta di autorizzazione sia sollecitata e non venga riprodotta.
| domain\_hint | live.com <br />**Nota**: usare il parametro domain\_hint solo se l'utente gestisce la sottoscrizione di Azure usando un account Microsoft.
| state | Specificare facoltativamente i dati di qualsiasi stato che Azure AD deve restituire con la risposta.

Una richiesta Open ID Connect di esempio è:

     https://login.windows.net/dushyantgill.com/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Azure AD autentica l'utente e, se necessario, chiede all'utente di concedere l'autorizzazione all'app. Restituisce il codice di autorizzazione all'URL di risposta dell'applicazione. A seconda della response\_mode richiesta, Azure AD invia i dati nella stringa di query o come dati di post.

Una risposta Open ID Connect di esempio è:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### Convalidare id\_token

Prima di far accedere l'utente, l'applicazione deve convalidare l'id\_token. La convalida del token è un argomento complesso ed è consigliabile usare una libreria di gestori token Web JSON standard per la piattaforma di sviluppo. Vedere il [codice sorgente del gestore JWT Azure AD per .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/System.IdentityModel.Tokens.Jwt/JwtSecurityTokenHandler.cs). Ciò premesso, poiché la sicurezza dell'applicazione è responsabilità dell'utente, verificare che la libreria usata per gestire l'id\_token convalidi correttamente gli aspetti seguenti del token:

- **Tempistica del token**: controllare le attestazioni nbf ed exp per assicurarsi che il token non sia troppo recente o troppo obsoleto. È consigliabile mantenere un margine di flessibilità (5 minuti) per adattarsi agli sfasamenti orari.
- **Emittente**: controllare l'attestazione iss per assicurarsi che l'emittente del token sia Azure Active Directory: https://sts.windows.net/{tenant_id_of_the_directory}
- **Destinatari**: controllare l'attestazione aud per assicurarsi che il token sia specifico dell'applicazione. Il valore deve essere l'ID client dell'applicazione.
- **Nonce**: controllare l'attestazione nonce per confrontare i dati nonce inviati nella richiesta di autorizzazione, per assicurarsi che la risposta sia stata sollecitata dall'applicazione e che il token non venga riprodotto.
- **Firma**: l'app deve verificare che il token sia stato firmato da Azure Active Directory. Le chiavi di firma di Azure AD cambiano spesso, quindi l'app deve eseguire ogni giorno il polling per le chiavi aggiornate o modificare le chiavi aggiornate se la convalida della firma non riesce. Per altre informazioni, vedere [Informazioni importanti sul rollover della chiave di firma in Azure AD](https://msdn.microsoft.com/library/azure/dn641920.aspx).

Una volta convalidato l'**id\_token**, usare il valore attestazione oid come identificatore non modificabile e non riutilizzabile dell'utente. Usare l'attestazione **unique\_name** o l'attestazione upn/email come nome visualizzato leggibile dell'utente. È anche possibile usare le attestazioni given\_name/family\_name facoltative per scopi di visualizzazione.

### Richiesta di token (flusso di concessione del codice OAuth 2.0)

Ora che l'applicazione ha ricevuto il codice di autorizzazione da Azure AD, è possibile ottenere il token di accesso per Azure Resource Manager. Inserire una richiesta di token di concessione del codice OAuth2.0 nell'endpoint del token di Azure AD:

    http://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token

I parametri della stringa di query disponibili per questa richiesta sono descritti nell'argomento [Flusso di concessione del codice di autorizzazione](https://msdn.microsoft.com/library/azure/dn645542.aspx).

L'esempio seguente illustra una richiesta per un token di concessione del codice con la credenziale password:

    POST https://login.windows.net/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Quando si usano le credenziali certificato, creare un token Web JSON (JWT) e una firma (RSA SHA256) usando la chiave privata della credenziale certificato dell'applicazione. I tipi di attestazione per il token sono illustrati in [Flusso di concessione del codice di autorizzazione](https://msdn.microsoft.com/library/azure/dn645542.aspx). Come riferimento, vedere il [codice di Active Directory Authentication Library (.NET)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/master/src/ADAL.NET/CryptographyHelper.cs) per firmare i token JWT per l'asserzione client.

Per i dettagli sull'autenticazione client, vedere la [specifica su Open ID Connect](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication). Ecco un [token JWT per l'asserzione client di esempio](https://www.authnauthz.com/OAuth/ParseJWTToken?token=eyJhbGciOiJSUzI1NiIsIng1dCI6IlFwcXdKZnJNZ003ekJ4M1hkM2NSSFdkYVFsTSJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ud2luZG93cy5uZXRcL2FhbHRlc3RzLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQyODk2Mjk5MSwiaXNzIjoiOTA4M2NjYjgtOGE0Ni00M2U3LTg0MzktMWQ2OTZkZjk4NGFlIiwianRpIjoiMmYyMjczMzQtZGQ3YS00NzZkLWFlOTYtYzg4NDQ4YTkxZGM0IiwibmJmIjoxNDI4OTYyMzkxLCJzdWIiOiI5MDgzY2NiOC04YTQ2LTQzZTctODQzOS0xZDY5NmRmOTg0YWUifQ.UXQE9H-FlwxYQmRVG0-p7pAX9TFgiRXcYr7GhbcC7ndIPHKpZ5tfHWPEgBl3ZVRvF2l8uA7HEV86T7t2w7OHhHwLBoW7XTgj-17hnV1CY21MwjrebPjaPIVITiilekKiBASfW2pmss3MjeOYcnBV2MuUnIgt4A_iUbF_-opRivgI4TFT4n17_3VPlChcU8zJqAMpt3TcAxC3EXXfh10Mw0qFfdZKqQOQxKHjnL8y7Of9xeB9BBD_b22JNRv0m7s0cYRx2Cz0cUUHw-ipHhWaW7YwhVRMfK6BMkaDUgaie4zFkcgHb7rm1z0rM1CvzIqP-Mwu3oEqYpY9cYo8nEjMyA).

L'esempio seguente illustra una richiesta per un token di concessione del codice con la credenziale certificato:

	POST https://login.windows.net/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1
	
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 1012
	
	grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Risposta di esempio per il token di concessione del codice:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### Gestire la risposta del token di concessione del codice

Una risposta del token riuscita conterrà il token di accesso (utente + app) per Azure Resource Manager. L'applicazione userà questo token di accesso per accedere a Resource Manager per conto dell'utente. La durata dei token di accesso rilasciati da Azure AD è di un'ora. È improbabile che l'applicazione Web debba rinnovare il token di accesso (utente + app), ma, se fosse necessario, è possibile usare il token di aggiornamento che l'applicazione riceve nella risposta del token. Inserire una richiesta di token OAuth2.0 nell'endpoint del token di Azure AD:

    http://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token

I parametri da usare con la richiesta di aggiornamento sono descritti in [Flusso di concessione del codice di autorizzazione](https://msdn.microsoft.com/library/azure/dn645542.aspx).

L'esempio seguente mostra come usare il token di aggiornamento:

    POST https://login.windows.net/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Si noti che, anche se i token di aggiornamento possono essere usati per ottenere nuovi token di accesso per Azure Resource Manager, non sono adatti per l'accesso offline dall'applicazione. La durata dei token di aggiornamento è limitata e i token di aggiornamento sono associati all'utente. Se l'utente lascia l'organizzazione, l'applicazione che usa il token di aggiornamento perderà l'accesso. Questo approccio non è adatto per le applicazioni usate dai team per gestire le risorse di Azure.

## Elencare le sottoscrizioni disponibili

L'applicazione ora ha un token per accedere ad Azure Resource Manager per conto dell'utente.

Il passaggio successivo dell'esperienza consiste nel consentire all'utente di connettere la sottoscrizione di Azure all'app in modo che l'app possa gestire tali sottoscrizioni anche quando l'utente non è presente (accesso offline a lungo termine). Mostrare all'utente l'elenco di sottoscrizioni di Azure in cui l'utente può gestire l'accesso e consentire all'utente di assegnare un ruolo Controllo degli accessi in base al ruolo direttamente all'identità dell'applicazione.

![Autorizzazione ARM - Esperienza utente con l'app di esempio 4](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-4-full.png)

### Elencare le sottoscrizioni in cui l'utente ha un accesso

Prima di tutto verrà chiamata l'API [di Resource Manager per elencare tutte le sottoscrizioni](https://msdn.microsoft.com/library/azure/dn790531.aspx) in cui l'utente ha qualche tipo di accesso. Quindi verranno identificate quelle per cui l'utente può gestire l'accesso.

Il metodo [GetUserSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L79) dell'app di esempio ASP.NET MVC implementa questa chiamata.

Una richiesta di esempio per elencare le sottoscrizioni è:

    GET https://management.azure.com/subscriptions?api-version=2014-04-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Una risposta di esempio per elencare le sottoscrizioni è:

    HTTP/1.1 200 OK

    {"value":[{"id":"/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2","subscriptionId":"34370e90-ac4a-4bf9-821f-85eeedeae1a2","displayName":"Sandbox","state":"Enabled","subscriptionPolicies":{"locationPlacementId":"Public_2014-09-01","quotaId":"PayAsYouGo_2014-09-01"}},{"id":"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e","subscriptionId":"c276fc76-9cd4-44c9-99a7-4fd71546436e","displayName":"Production","state":"Enabled","subscriptionPolicies":{"locationPlacementId":"Public_2014-09-01","quotaId":"PayAsYouGo_2014-09-01"}}]}

### Ottenere le autorizzazioni dell'utente per la sottoscrizione

L'azione di connessione/disconnessione deve essere visualizzata solo per le sottoscrizioni per cui l'utente può gestire l'accesso. Per ogni sottoscrizione, si chiamerà l'API [di Resource Manager per elencare le autorizzazioni](https://msdn.microsoft.com/library/azure/dn906889.aspx) per determinare se l'utente abbia o meno i diritti di gestione degli accessi per la sottoscrizione.

Il metodo [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L132) dell'app di esempio ASP.NET MVC implementa questa chiamata.

L'esempio seguente illustra come richiedere le autorizzazioni di un utente per una sottoscrizione. 83cfe939-2402-4581-b761-4f59b0a041e4 è l'ID della sottoscrizione.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2014-07-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Un esempio della risposta per ottenere le autorizzazioni dell'utente per la sottoscrizione è:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

L'API delle autorizzazioni restituisce più autorizzazioni. Ogni autorizzazione è costituita dalle azioni consentite (actions) e dalle azioni non consentite (notactions). Se un'azione è presente nell'elenco di azioni consentite di un'autorizzazione e non è presente nell'elenco notactions di tale autorizzazione, all'utente è consentito eseguire tale azione. **microsoft.authorization/roleassignments/write** è l'azione che concede i diritti di gestione degli accessi. L'applicazione deve analizzare il risultato delle autorizzazioni per cercare una corrispondenza regex nella stringa di questa azione negli elenchi actions e notactions di ogni autorizzazione.

### Facoltativo: elencare le directory in cui è presente l'account utente

L'account di un utente può essere presente in più directory Azure Active Directory. È possibile che all'inizio l'utente non abbia specificato il nome directory corretto. In tal caso la sottoscrizione desiderata non verrà visualizzata nell'elenco.

L'API [di Resource Manager per elencare i tenant](https://msdn.microsoft.com/library/azure/dn790536.aspx) elenca gli identificatori di tutte le directory in cui è presente l'account dell'utente. È possibile chiamare l'API per determinare se l'account dell'utente si trova in più di una directory e facoltativamente visualizzare all'utente un messaggio simile a "La sottoscrizione cercata non è stata trovata? Potrebbe trovarsi nell'altra directory Azure Active Directory di cui si è membri. Fare clic qui per cambiare directory".

Il metodo [GetUserOrganizations](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) dell'app di esempio ASP.NET MVC implementa questa chiamata.

Una richiesta di esempio per elencare le directory è:

    GET https://management.azure.com/tenants?api-version=2014-04-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1Qi****8DJf1UO4a-ZZ_TJmWFlwO1mM7Cw6JWtfY2lGc5A

Una risposta di esempio per elencare le directory è:

    HTTP/1.1 200 OK

    {"value":[{"id":"/tenants/7fe877e6-a150-4992-bbfe-f517e304dfa0","tenantId":"7fe877e6-a150-4992-bbfe-f517e304dfa0"},{"id":"/tenants/62e173e9-301e-423e-bcd4-29121ec1aa24","tenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24"}]}

## Connettere la sottoscrizione all'applicazione

Ora è disponibile un elenco di sottoscrizioni di Azure che l'utente può connettere all'applicazione. Il passaggio successivo consiste nel fornire all'utente un comando per creare la connessione. Quando l'utente seleziona **connect**, l'app:

1. Assegna il ruolo Controllo degli accessi in base al ruolo appropriato all'identità dell'applicazione nella sottoscrizione.
2. Convalida l'assegnazione dell'accesso eseguendo una query dell'autorizzazione dell'applicazione nella sottoscrizione o accedendo a Resource Manager con il token solo app.
1. Registra la connessione nella struttura dei dati delle "sottoscrizioni connesse" dell'applicazione, rendendo persistente l'ID della sottoscrizione.

Ora verrà esaminato più dettagliatamente il primo passaggio: per assegnare il ruolo Controllo degli accessi in base al ruolo appropriato all'identità dell'applicazione, è necessario determinare:

- L'ID oggetto dell'identità dell'applicazione nella directory Azure Active Directory dell'utente
- L'identificatore del ruolo Controllo degli accessi in base al ruolo che l'applicazione richiede nella sottoscrizione

Verrà ora esaminata la prima parte: la prima volta che l'applicazione autentica un utente da una directory Azure AD, un oggetto entità servizio per l'applicazione viene creato in tale directory Azure AD. Azure consente di assegnare alle entità servizio i ruoli Controllo degli accessi in base al ruolo, per concedere l'accesso diretto alle applicazioni corrispondenti nelle risorse di Azure. Questo è proprio ciò che si vuole fare, quindi si inizierà eseguendo una query dell'API Graph di Azure AD per determinare l'identificatore dell'entità servizio dell'applicazione nella directory Azure AD dell'utente registrato.

Si ha un solo token di accesso per Azure Resource Manager ed è necessario un nuovo token di accesso per chiamare l'API Graph di Azure AD. Ogni applicazione in Azure AD ha le autorizzazioni per eseguire una query del proprio oggetto entità servizio, quindi non è necessario un token di accesso utente + app, ma sarà sufficiente un token di accesso solo app.

<a id="app-azure-ad-graph">
### Ottenere il token di accesso solo app per l'API Graph di Azure AD

Per autenticare l'app e ottenere un token per l'API Graph di Azure AD, rilasciare una richiesta del token di flusso OAuth2.0 per la concessione delle credenziali client per l'endpoint del token di Azure AD (**http://login.microsoftonline.com/{directory\_domain\_name}/OAuth2/Token**).

Le righe 73-77 del metodo [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#L73) dell'applicazione di esempio ASP.NET MVC ottengono un token di accesso solo app per l'API Graph usando Active Directory Authentication Library per .NET.

Dati della richiesta del token di concessione delle credenziali client:

| Elemento | Valore
|----|----
| grant\_type | **client\_credentials**
| client\_id | ID client dell'applicazione
| resource | Identificatore codificato URL della risorsa per cui viene richiesto il token di accesso. In questo caso, l'identificatore dell'API Graph di Azure AD è **https://graph.windows.net/** | client\_secret or client\_assertion\_type + client\_assertion | Se l'applicazione usa la credenziale password, usare client\_secret. Se l'applicazione usa la credenziale certificato, usare client\_assertion.

Richiesta di esempio del token di concessione delle credenziali client:

    POST https://login.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Risposta di esempio del token di concessione delle credenziali client:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### Ottenere l'ObjectId dell'entità servizio dell'applicazione nella directory Azure AD dell'utente

Ora usare il token di accesso solo app per eseguire una query dell'API delle [entità servizio Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#ServicePrincipalEntity) per determinare l'ID oggetto dell'entità servizio dell'applicazione nella directory.

Il metodo [GetObjectIdOfServicePrincipalInOrganiation](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#L66) dell'applicazione di esempio ASP.NET MVC implementa questa chiamata.

L'esempio seguente illustra come richiedere l'entità servizio di un'applicazione. a0448380-c346-4f9f-b897-c18733de9394 è l'ID client dell'applicazione.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

L'esempio seguente illustra una risposta alla richiesta per l'entità servizio di un'applicazione

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### Ottenere l'identificatore del ruolo Controllo degli accessi in base al ruolo di Azure

È necessario assegnare il ruolo Controllo degli accessi in base al ruolo appropriato all'entità servizio dell'applicazione nella sottoscrizione selezionata. Per questo è necessario determinare l'identificatore del ruolo Controllo degli accessi in base al ruolo di Azure.

Il ruolo Controllo degli accessi in base al ruolo appropriato per l'applicazione:

- Se l'applicazione monitora solo la sottoscrizione, senza apportare modifiche, richiede solo le autorizzazioni di lettura per la sottoscrizione. Assegnare il ruolo **Lettore**.
- Se l'applicazione gestisce la sottoscrizione di Azure, creando/modificando/eliminando le entità, richiederà una delle autorizzazioni di collaborazione.
  - Per gestire un particolare tipo di risorsa, assegnare i ruoli di collaboratore specifici della risorsa (Collaboratore Macchina virtuale, Collaboratore Rete virtuale, Collaboratore Account di archiviazione e così via).
  - Per gestire un tipo di risorsa qualsiasi, assegnare il ruolo **Collaboratore**.

L'assegnazione del ruolo per l'applicazione sarà visibile agli utenti, quindi selezionare il privilegio meno necessario.

Chiamare l'[API per la definizione del ruolo di Resource Manager](https://msdn.microsoft.com/library/azure/dn906879.aspx) per elencare tutti i ruoli Controllo degli accessi in base al ruolo di Azure e quindi eseguire l'iterazione sul risultato per trovare la definizione di ruolo desiderata in base al nome.

Il metodo [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L354) dell'app di esempio ASP.NET MVC implementa questa chiamata.

L'esempio di richiesta seguente illustra come ottenere l'identificatore del ruolo Controllo degli accessi in base al ruolo di Azure. 09cbd307-aa71-4aca-b346-5f253e6e3ebb è l'ID della sottoscrizione.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2014-07-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

La risposta è nel formato seguente:

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Si noti che non è necessario chiamare l'API regolarmente. Una volta determinato il GUID conosciuto della definizione del ruolo, è possibile costruire l'ID definizione del ruolo come segue:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Ecco i GUID conosciuti dei ruoli predefiniti usati comunemente:

| Ruolo | Guid |
| ----- | ------ |
| Lettore | acdd72a7-3385-48ef-bd42-f606fba81ae7
| Collaboratore | b24988ac-6180-42a0-ab88-20f7382dd24c
| Collaboratore macchine virtuali | d73bb868-a0df-4d4d-bd69-98a00b01fccb
| Collaboratore reti virtuali | b34d265f-36f7-4a0d-a4d4-e158ca92e90f
| Collaboratore account di archiviazione | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25
| Collaboratore siti Web | de139f84-1756-47ae-9be6-808fbbe84772
| Collaboratore piani Web | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b
| Collaboratore SQL Server | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437
| Collaboratore database SQL | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec

### Assegnare il ruolo Controllo degli accessi in base al ruolo all'applicazione

Si ha tutto quanto è necessario per assegnare il ruolo Controllo degli accessi in base al ruolo appropriato all'entità servizio dell'applicazione nella sottoscrizione selezionata usando l'API [di Resource Manager per la creazione dell'assegnazione del ruolo](https://msdn.microsoft.com/library/azure/dn906887.aspx).

Il metodo [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L269) dell'app di esempio ASP.NET MVC implementa questa chiamata.

Richiesta di esempio per assegnare il ruolo Controllo degli accessi in base al ruolo all'applicazione:

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2014-10-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

Nella richiesta vengono usati i valori seguenti:

| Guid | Descrizione |
| ------ | --------- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb | ID della sottoscrizione
| c3097b31-7309-4c59-b4e3-770f8406bad2 | ID oggetto dell'entità servizio dell'applicazione
| acdd72a7-3385-48ef-bd42-f606fba81ae7 | GUID conosciuto del ruolo Controllo degli accessi in base al ruolo Lettore
| 4f87261d-2816-465d-8311-70a27558df4c | Nuovo GUID creato per la nuova assegnazione di ruolo

La risposta è nel formato seguente:

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### Ottenere il token di accesso solo app per Azure Resource Manager

Il passaggio successivo consiste nel convalidare l'accesso dell'app alla sottoscrizione. Per questo è consigliabile eseguire un'attività di test nella sottoscrizione usando un token solo app per Azure Resource Manager. L'attività di test deve verificare che l'applicazione abbia effettivamente l'accesso desiderato nella sottoscrizione, per eseguire il monitoraggio e la gestione offline.

Per ottenere un token di accesso solo app per Azure Resource Manager, seguire le istruzioni della sezione [Ottenere il token di accesso solo app per l'API Graph di Azure AD](#app-azure-ad-graph), con un valore diverso per il parametro della risorsa:

    https://management.core.windows.net/

Le righe 210-214 del metodo [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L203) dell'applicazione di esempio ASP.NET MVC ottengono un token di accesso solo app per Azure Resource Manager usando Active Directory Authentication Library per .NET.

#### Ottenere le autorizzazioni dell'applicazione per la sottoscrizione

Per controllare che l'applicazione abbia l'accesso desiderato in una sottoscrizione di Azure, è anche possibile chiamare l'API [di Resource Manager per le autorizzazioni](https://msdn.microsoft.com/library/azure/dn906889.aspx) in modo simile a come si è determinato se l'utente abbia o meno diritti di gestione degli accessi per la sottoscrizione. Questa volta però chiamare l'API per le autorizzazioni con il token di accesso solo app ricevuto nel passaggio precedente.

Il metodo [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L203) dell'app di esempio ASP.NET MVC implementa questa chiamata.

## Gestire le sottoscrizioni connesse

Quando il ruolo Controllo degli accessi in base al ruolo appropriato viene assegnato all'entità servizio dell'applicazione nella sottoscrizione, l'applicazione può continuare a monitorarla/gestirla usando i token di accesso solo app per Azure Resource Manager.

Se il proprietario di una sottoscrizione rimuove l'assegnazione del ruolo dell'applicazione usando il portale di gestione di Azure o gli strumenti della riga di comando, l'applicazione non potrà più accedere a tale sottoscrizione. In tal caso, è consigliabile notificare all'utente che la connessione con la sottoscrizione è stata interrotta dall'esterno dell'applicazione e dargli la possibilità di "ripristinare" la connessione. Il "ripristino" si limiterà a creare di nuovo l'assegnazione del ruolo eliminata offline.


## Disconnettere le sottoscrizioni

Come si è consentito all'utente di connettere le sottoscrizioni all'applicazione, è necessario consentirgli anche di disconnettere le sottoscrizioni. Dal punto di vista della gestione degli accessi, disconnettere significa rimuovere l'assegnazione del ruolo che l'entità servizio dell'applicazione ha nella sottoscrizione. È facoltativamente possibile rimuovere anche qualsiasi stato nell'applicazione per la sottoscrizione. Solo gli utenti con l'autorizzazione per la gestione degli accessi potranno disconnettere la sottoscrizione.

Il metodo [RevokeRoleFromServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L303) dell'app di esempio ASP.NET MVC implementa questa chiamata.

Gli utenti ora possono facilmente connettere e gestire le sottoscrizioni di Azure con l'applicazione.

<!---HONumber=AcomDC_0420_2016-->