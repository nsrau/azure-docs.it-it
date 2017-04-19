---
title: Autenticazione di Azure Active Directory e Resource Manager | Microsoft Docs
description: Guida per gli sviluppatori all&quot;autenticazione con l&quot;API di Azure Resource Manager e Active Directory per l&quot;integrazione di un&quot;app con altre sottoscrizioni di Azure.
services: azure-resource-manager,active-directory
documentationcenter: na
author: dushyantgill
manager: timlt
editor: tysonn
ms.assetid: 17b2b40d-bf42-4c7d-9a88-9938409c5088
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/27/2016
ms.author: dugill;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 73ee330c276263a21931a7b9a16cc33f86c58a26
ms.openlocfilehash: de1355a8dc4b0099dca3efc2109ccfb9facf7269
ms.lasthandoff: 04/05/2017


---
# <a name="use-resource-manager-authentication-api-to-access-subscriptions"></a>Usare l'API di autenticazione di Resource Manager per accedere alle sottoscrizioni
## <a name="introduction"></a>Introduzione
Questo argomento illustra agli sviluppatori software, che devono creare un'app per gestire le risorse di Azure di un cliente, come eseguire l'autenticazione con le API di Azure Resource Manager e ottenere l'accesso alle risorse in altre sottoscrizioni.

L'app può accedere alle API di Resource Manager in due modi:

1. **Accesso utente + app**: per le app che accedono alle risorse per conto di un utente connesso. Questo approccio funziona per le app, ad esempio le app Web e gli strumenti da riga di comando, che trattano solo la "gestione interattiva" delle risorse di Azure.
2. **Accesso solo app**: per le app che eseguono servizi daemon e processi pianificati. All'identità dell'app viene concesso l'accesso diretto alle risorse. Questo approccio funziona per le app che richiedono l'accesso headless (automatico) a lungo termine ad Azure.

Questo argomento include istruzioni dettagliate per la creazione di un'app che usa entrambi i metodi di autorizzazione. Illustra come eseguire ogni passaggio con l'API REST o C#. L'applicazione ASP.NET MVC completa è disponibile all'indirizzo [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

Tutto il codice di questo argomento viene eseguito come app Web, che è possibile provare all'indirizzo [http://vipswapper.azurewebsites.net/cloudsense](http://vipswapper.azurewebsites.net/cloudsense).

## <a name="what-the-web-app-does"></a>Operazioni eseguite dall'app Web
L'app Web:

1. Esegue l'accesso per un utente di Azure.
2. Chiede all'utente di concedere a Resource Manager l'accesso all'app Web.
3. Ottiene il token di accesso app + utente per l'accesso a Resource Manager.
4. Usa il token (ottenuto nel passaggio 3) per chiamare Resource Manager e assegnare l'entità servizio dell'app a un ruolo nella sottoscrizione, che consente l'accesso a lungo termine dell'app alla sottoscrizione.
5. Ottiene il token di accesso solo app.
6. Usa il token (ottenuto nel passaggio 5) per gestire le risorse nella sottoscrizione tramite Resource Manager.

Ecco il flusso end-to-end dell'applicazione Web.

![Flusso di autenticazione di Resource Manager](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

L'utente fornisce l'ID sottoscrizione per la sottoscrizione che vuole usare:

![Fornire l'ID sottoscrizione](./media/resource-manager-api-authentication/sample-ux-1.png)

Selezionare l'account da usare per l'accesso.

![Selezionare un account](./media/resource-manager-api-authentication/sample-ux-2.png)

Fornire le credenziali.

![Fornire le credenziali](./media/resource-manager-api-authentication/sample-ux-3.png)

Concedere all'app l'accesso alle sottoscrizioni di Azure:

![Concedere l'accesso](./media/resource-manager-api-authentication/sample-ux-4.png)

Gestire le sottoscrizioni connesse:

![Connettere la sottoscrizione](./media/resource-manager-api-authentication/sample-ux-7.png)

## <a name="register-application"></a>Registrare l'applicazione
Prima di iniziare a scrivere codice, registrare l'app Web in Azure Active Directory (AD). La registrazione dell'app crea un'identità centrale per l'app in Azure AD, che contiene le informazioni di base sull'applicazione, ad esempio l'ID client OAuth, gli URL di risposta e le credenziali che l'applicazione usa per l'autenticazione e l'accesso alle API di Azure Resource Manager. Con la registrazione dell'app vengono registrate anche le diverse autorizzazioni delegate richieste dall'applicazione quando accede alle API Microsoft per conto dell'utente.

Poiché l'applicazione accede ad altre sottoscrizioni, è necessario configurarla come un'applicazione multi-tenant. Per superare la convalida, fornire un dominio associato ad Active Directory. Per visualizzare i domini associati ad Active Directory, accedere al [portale classico](https://manage.windowsazure.com). Selezionare l'istanza di Active Directory e quindi **Domini**.

L'esempio seguente mostra come registrare l'app usando Azure PowerShell. Per il funzionamento di questo comando, è necessaria la versione più recente (agosto 2016) di Azure PowerShell.

    $app = New-AzureRmADApplication -DisplayName "{app name}" -HomePage "https://{your domain}/{app name}" -IdentifierUris "https://{your domain}/{app name}" -Password "{your password}" -AvailableToOtherTenants $true

Per accedere come applicazione Active Directory, sono necessari l'ID applicazione e la password. Per visualizzare l'ID applicazione restituito dal comando precedente, usare:

    $app.ApplicationId

L'esempio seguente mostra come registrare l'app usando l'interfaccia della riga di comando di Azure.

    azure ad app create --name {app name} --home-page https://{your domain}/{app name} --identifier-uris https://{your domain}/{app name} --password {your password} --available true

I risultati includono l'AppId necessario per autenticarsi come applicazione.

### <a name="optional-configuration---certificate-credential"></a>Configurazione facoltativa: credenziali del certificato
Azure AD supporta anche le credenziali certificato per le applicazioni: creare un certificato autofirmato, mantenere la chiave privata e aggiungere la chiave pubblica alla registrazione dell'applicazione Azure AD. Per l'autenticazione, l'applicazione invia un piccolo payload ad Azure AD firmato usando la chiave privata e Azure AD convalida la firma usando la chiave pubblica registrata.

Per informazioni sulla creazione di un'app Active Directory con un certificato, vedere [Use Azure PowerShell to create a service principal to access resources](resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority) (Usare Azure PowerShell per creare un'entità servizio per accedere alle risorse) o [Use Azure CLI to create a service principal to access resources](resource-group-authenticate-service-principal-cli.md#create-service-principal-with-certificate) (Usare l'interfaccia della riga di comando di Azure per creare un'entità servizio per accedere alle risorse).

## <a name="get-tenant-id-from-subscription-id"></a>Ottenere l'ID tenant dall'ID sottoscrizione
Per richiedere un token da usare per chiamare Resource Manager, l'applicazione deve conoscere l'ID tenant del tenant di Azure AD che ospita la sottoscrizione di Azure. Molto probabilmente gli utenti conoscono i relativi ID sottoscrizione, ma potrebbero non conoscere gli ID tenant per Active Directory. Per ottenere l'ID tenant dell'utente, chiedere all'utente l'ID sottoscrizione. Fornire questo ID sottoscrizione quando si invia una richiesta relativa alla sottoscrizione:

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

La richiesta non riesce perché l'utente non è ancora connesso, ma è possibile recuperare l'ID tenant dalla risposta. Nel caso di eccezione, recuperare l'ID tenant dal valore dell'intestazione della risposta per **WWW-Authenticate**. Questa implementazione è visibile nel metodo [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) .

## <a name="get-user--app-access-token"></a>Ottenere il token di accesso utente + app
L'applicazione reindirizza l'utente ad Azure AD con una richiesta di autorizzazione OAuth 2.0 per autenticare le credenziali dell'utente e ricevere un codice di autorizzazione. L'applicazione usa il codice di autorizzazione per ottenere un token di accesso per Resource Manager. Il metodo [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) consente di creare la richiesta di autorizzazione.

Questo argomento descrive le richieste dell'API REST per autenticare l'utente. È anche possibile usare librerie helper per eseguire l'autenticazione nel codice. Per altre informazioni su queste librerie, vedere [Azure Active Directory Authentication Library](../active-directory/active-directory-authentication-libraries.md) (Libreria di autenticazione di Azure Active Directory). Per informazioni aggiuntive su come integrare la gestione delle identità in un'applicazione, vedere [Guida per gli sviluppatori di Azure Active Directory](../active-directory/active-directory-developers-guide.md).

### <a name="auth-request-oauth-20"></a>Richiesta di autorizzazione (OAuth 2.0)
Rilasciare una richiesta di autorizzazione Open ID Connect/OAuth2.0 all'endpoint di autorizzazione di Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

I parametri della stringa di query disponibili per questa richiesta sono descritti nell'articolo su come [richiedere un codice di autorizzazione](../active-directory/develop/active-directory-protocols-oauth-code.md#request-an-authorization-code) .

L'esempio seguente illustra come richiedere l'autorizzazione OAuth 2.0:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Azure AD autentica l'utente e, se necessario, chiede all'utente di concedere l'autorizzazione all'app. Restituisce il codice di autorizzazione all'URL di risposta dell'applicazione. A seconda della response_mode richiesta, Azure AD invia i dati nella stringa di query o come dati di post.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Richiesta di autorizzazione (Open ID Connect)
Se non si vuole solo accedere ad Azure Resource Manager per conto dell'utente, ma anche consentire all'utente di accedere all'applicazione usando l'account Azure AD, inviare una richiesta di autorizzazione OpenID Connect. Con OpenID Connect l'applicazione riceve anche un id_token da Azure AD, che può essere usato dall'app per consentire l'accesso dell'utente.

I parametri della stringa di query disponibili per questa richiesta sono descritti nell'articolo su come [inviare la richiesta di accesso](../active-directory/develop/active-directory-protocols-openid-connect-code.md#send-the-sign-in-request).

Una richiesta Open ID Connect di esempio è:

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Azure AD autentica l'utente e, se necessario, chiede all'utente di concedere l'autorizzazione all'app. Restituisce il codice di autorizzazione all'URL di risposta dell'applicazione. A seconda della response_mode richiesta, Azure AD invia i dati nella stringa di query o come dati di post.

Una risposta Open ID Connect di esempio è:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Richiesta di token (flusso di concessione del codice OAuth 2.0)
Ora che l'applicazione ha ricevuto il codice di autorizzazione da Azure AD, è possibile ottenere il token di accesso per Azure Resource Manager.  Inserire una richiesta di token di concessione del codice OAuth2.0 nell'endpoint del token di Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

I parametri della stringa di query disponibili per questa richiesta sono descritti nell'articolo su come [usare il codice di autorizzazione](../active-directory/develop/active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

L'esempio seguente illustra una richiesta per un token di concessione del codice con la credenziale password:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Quando si usano le credenziali certificato, creare un token Web JSON (JWT) e una firma (RSA SHA256) usando la chiave privata della credenziale certificato dell'applicazione. I tipi di attestazione per il token sono illustrati nell'argomento sulle [attestazioni nei token JWT](../active-directory/develop/active-directory-protocols-oauth-code.md#jwt-token-claims). Come riferimento, vedere [Active Directory Auth Library (.NET) code](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/dev/src/ADAL.PCL.Desktop/CryptographyHelper.cs) (Codice di Active Directory Authentication Library - .NET) per firmare i token JWT per l'asserzione client.

Per dettagli sull'autenticazione client, vedere la [specifica di OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) .

L'esempio seguente illustra una richiesta per un token di concessione del codice con la credenziale certificato:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Risposta di esempio per il token di concessione del codice:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Gestire la risposta del token di concessione del codice
Una risposta del token riuscita contiene il token di accesso (utente + app) per Azure Resource Manager. L'applicazione usa questo token di accesso per accedere a Resource Manager per conto dell'utente. La durata dei token di accesso rilasciati da Azure AD è di un'ora. È improbabile che l'applicazione Web debba rinnovare il token di accesso (utente + app). Se è necessario rinnovare il token di accesso, usare il token di aggiornamento ricevuto dall'applicazione nella risposta del token. Inserire una richiesta di token OAuth2.0 nell'endpoint del token di Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

I parametri da usare con la richiesta di aggiornamento sono descritti nell'articolo su come [aggiornare i token di accesso](../active-directory/develop/active-directory-protocols-oauth-code.md#refreshing-the-access-tokens).

L'esempio seguente mostra come usare il token di aggiornamento:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Anche se i token di aggiornamento possono essere usati per ottenere nuovi token di accesso per Azure Resource Manager, non sono adatti per l'accesso offline dell'applicazione. La durata dei token di aggiornamento è limitata e i token di aggiornamento sono associati all'utente. Se l'utente lascia l'organizzazione, l'applicazione che usa il token di aggiornamento perde l'accesso. Questo approccio non è adatto per le applicazioni usate dai team per gestire le risorse di Azure.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Verificare se l'utente può assegnare l'accesso alla sottoscrizione
L'applicazione ora ha un token per accedere ad Azure Resource Manager per conto dell'utente. Nel passaggio successivo si connette l'app alla sottoscrizione. Dopo la connessione, l'app può gestire le sottoscrizioni anche quando l'utente non è presente (accesso offline a lungo termine).

Per ogni sottoscrizione da connettere, chiamare l'API di [Resource Manager per elencare le autorizzazioni](https://docs.microsoft.com/rest/api/authorization/permissions) per determinare se all'utente sono concessi i diritti di gestione degli accessi per la sottoscrizione.

Il metodo [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) dell'app di esempio ASP.NET MVC implementa questa chiamata.

L'esempio seguente illustra come richiedere le autorizzazioni di un utente per una sottoscrizione. 83cfe939-2402-4581-b761-4f59b0a041e4 è l'ID della sottoscrizione.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Ecco un esempio della risposta per ottenere le autorizzazioni di un utente per la sottoscrizione:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

L'API per le autorizzazioni restituisce più autorizzazioni. Ogni autorizzazione è costituita dalle azioni consentite (actions) e dalle azioni non consentite (notactions). Se un'azione è presente nell'elenco di azioni consentite di un'autorizzazione e non è presente nell'elenco notactions di tale autorizzazione, all'utente è consentito eseguire tale azione. **microsoft.authorization/roleassignments/write** è l'azione che concede i diritti di gestione degli accessi. L'applicazione deve analizzare il risultato delle autorizzazioni per cercare una corrispondenza regex nella stringa di questa azione negli elenchi actions e notactions di ogni autorizzazione.

## <a name="get-app-only-access-token"></a>Ottenere il token di accesso solo app
A questo punto, si sa se l'utente può assegnare l'accesso alla sottoscrizione di Azure. Ecco i passaggi successivi:

1. Assegnare il ruolo Controllo degli accessi in base al ruolo appropriato all'identità dell'applicazione nella sottoscrizione.
2. Convalidare l'assegnazione dell'accesso eseguendo una query per l'autorizzazione dell'applicazione nella sottoscrizione o accedendo a Resource Manager con il token solo app.
3. Registrare la connessione nella struttura dei dati delle "sottoscrizioni connesse" dell'applicazione, rendendo persistente l'ID della sottoscrizione.

Ora verrà esaminato più dettagliatamente il primo passaggio. Per assegnare il ruolo Controllo degli accessi in base al ruolo appropriato all'identità dell'applicazione, è necessario determinare:

* L'ID oggetto dell'identità dell'applicazione nella directory Azure Active Directory dell'utente
* L'identificatore del ruolo Controllo degli accessi in base al ruolo che l'applicazione richiede nella sottoscrizione

Quando l'applicazione autentica un utente da un'istanza di Azure AD, crea un oggetto entità servizio per l'applicazione in tale istanza di Azure AD. Azure consente di assegnare alle entità servizio i ruoli Controllo degli accessi in base al ruolo, per concedere l'accesso diretto alle applicazioni corrispondenti nelle risorse di Azure. Questa azione è esattamente ciò che si vuole eseguire. Eseguire una query sull'API Graph di Azure AD per determinare l'identificatore dell'entità servizio dell'applicazione nell'istanza di Azure AD dell'utente connesso.

Si ha un solo token di accesso per Azure Resource Manager ed è necessario un nuovo token di accesso per chiamare l'API Graph di Azure AD. Ogni applicazione in Azure AD ha le autorizzazioni per eseguire query sul proprio oggetto entità servizio, quindi un token di accesso solo app sarà sufficiente.

<a id="app-azure-ad-graph" />

### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Ottenere il token di accesso solo app per l'API Graph di Azure AD
Per autenticare l'app e ottenere un token per l'API Graph di Azure AD, rilasciare una richiesta del token di flusso OAuth2.0 per la concessione delle credenziali client per l'endpoint del token di Azure AD (**http://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token**).

Il metodo [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) dell'applicazione di esempio ASP.NET MVC ottiene un token di accesso solo app per l'API Graph usando Active Directory Authentication Library per .NET.

I parametri della stringa di query disponibili per questa richiesta sono descritti nell'articolo su come [richiedere un token di accesso](../active-directory/develop/active-directory-protocols-oauth-service-to-service.md#request-an-access-token).

Richiesta di esempio del token di concessione delle credenziali client:

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Risposta di esempio del token di concessione delle credenziali client:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>Ottenere l'ObjectId dell'entità servizio dell'applicazione nella directory Azure AD dell'utente
Ora usare il token di accesso solo app per eseguire una query sull'API dell' [entità servizio Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) per determinare l'ID oggetto dell'entità servizio dell'applicazione nella directory.

Il metodo [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) dell'applicazione di esempio ASP.NET MVC implementa questa chiamata.

L'esempio seguente illustra come richiedere l'entità servizio di un'applicazione. a0448380-c346-4f9f-b897-c18733de9394 è l'ID client dell'applicazione.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

L'esempio seguente illustra una risposta alla richiesta per l'entità servizio di un'applicazione

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Ottenere l'identificatore del ruolo Controllo degli accessi in base al ruolo di Azure
Per assegnare il ruolo Controllo degli accessi in base al ruolo appropriato all'entità servizio, è necessario determinare l'identificatore di questo ruolo di Azure.

Il ruolo Controllo degli accessi in base al ruolo appropriato per l'applicazione:

* Se l'applicazione monitora solo la sottoscrizione, senza apportare modifiche, richiede solo le autorizzazioni di lettura per la sottoscrizione. Assegnare il ruolo **Lettore** .
* Se l'applicazione gestisce la sottoscrizione di Azure, creando/modificando/eliminando le entità, richiede una delle autorizzazioni di collaborazione.
  * Per gestire un particolare tipo di risorsa, assegnare i ruoli di collaboratore specifici della risorsa (Collaboratore Macchina virtuale, Collaboratore Rete virtuale, Collaboratore Account di archiviazione e così via).
  * Per gestire un tipo di risorsa qualsiasi, assegnare il ruolo **Collaboratore** .

L'assegnazione del ruolo per l'applicazione è visibile agli utenti, quindi selezionare il privilegio meno necessario.

Chiamare l' [API per la definizione del ruolo di Resource Manager](https://docs.microsoft.com/rest/api/authorization/roledefinitions) per elencare tutti i ruoli di Controllo degli accessi in base al ruolo di Azure e quindi scorrere il risultato per trovare la definizione di ruolo desiderata in base al nome.

Il metodo [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) dell'app di esempio ASP.NET MVC implementa questa chiamata.

L'esempio di richiesta seguente illustra come ottenere l'identificatore del ruolo Controllo degli accessi in base al ruolo di Azure. 09cbd307-aa71-4aca-b346-5f253e6e3ebb è l'ID della sottoscrizione.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

La risposta è nel formato seguente:

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Si noti che non è necessario chiamare l'API regolarmente. Una volta determinato il GUID conosciuto della definizione del ruolo, è possibile costruire l'ID definizione del ruolo come segue:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Ecco i GUID conosciuti dei ruoli predefiniti usati comunemente:

| Ruolo | Guid |
| --- | --- |
| Lettore |acdd72a7-3385-48ef-bd42-f606fba81ae7 |
| Collaboratore |b24988ac-6180-42a0-ab88-20f7382dd24c |
| Collaboratore macchine virtuali |d73bb868-a0df-4d4d-bd69-98a00b01fccb |
| Collaboratore reti virtuali |b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
| Collaboratore account di archiviazione |86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
| Collaboratore siti Web |de139f84-1756-47ae-9be6-808fbbe84772 |
| Collaboratore piani Web |2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
| Collaboratore SQL Server |6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
| Collaboratore database SQL |9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |

### <a name="assign-rbac-role-to-application"></a>Assegnare il ruolo Controllo degli accessi in base al ruolo all'applicazione
A questo punto è disponibile tutto quanto è necessario per assegnare il ruolo Controllo degli accessi in base al ruolo appropriato all'entità servizio usando l'API di [Resource Manager per creare un'assegnazione di ruolo](https://docs.microsoft.com/rest/api/authorization/roleassignments) .

Il metodo [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) dell'app di esempio ASP.NET MVC implementa questa chiamata.

Richiesta di esempio per assegnare il ruolo Controllo degli accessi in base al ruolo all'applicazione:

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

Nella richiesta vengono usati i valori seguenti:

| Guid | Descrizione |
| --- | --- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb |ID della sottoscrizione |
| c3097b31-7309-4c59-b4e3-770f8406bad2 |ID oggetto dell'entità servizio dell'applicazione |
| acdd72a7-3385-48ef-bd42-f606fba81ae7 |ID del ruolo Lettore |
| 4f87261d-2816-465d-8311-70a27558df4c |Nuovo GUID creato per la nuova assegnazione di ruolo |

La risposta è nel formato seguente:

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Ottenere il token di accesso solo app per Azure Resource Manager
Per verificare che l'app abbia l'accesso desiderato alla sottoscrizione, eseguire un'attività di test nella sottoscrizione usando un token solo app.

Per ottenere un token di accesso solo app, seguire le istruzioni nella sezione [Ottenere il token di accesso solo app per l'API Graph di Azure AD](#app-azure-ad-graph)con un valore diverso per il parametro della risorsa:

    https://management.core.windows.net/

Il metodo [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) dell'applicazione di esempio ASP.NET MVC ottiene un token di accesso solo app per Azure Resource Manager usando Active Directory Authentication Library per .NET.

#### <a name="get-applications-permissions-on-subscription"></a>Ottenere le autorizzazioni dell'applicazione per la sottoscrizione
Per verificare che l'applicazione abbia l'accesso desiderato in una sottoscrizione di Azure, è anche possibile chiamare l'API di [Resource Manager per elencare le autorizzazioni](https://docs.microsoft.com/rest/api/authorization/permissions) . Questo approccio è simile a quello usato per determinare se all'utente sono concessi i diritti di gestione degli accessi per la sottoscrizione. Questa volta occorre però chiamare l'API per le autorizzazioni con il token di accesso solo app ottenuto nel passaggio precedente.

Il metodo [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) dell'app di esempio ASP.NET MVC implementa questa chiamata.

## <a name="manage-connected-subscriptions"></a>Gestire le sottoscrizioni connesse
Quando il ruolo Controllo degli accessi in base al ruolo appropriato viene assegnato all'entità servizio dell'applicazione nella sottoscrizione, l'applicazione può continuare a monitorarla/gestirla usando i token di accesso solo app per Azure Resource Manager.

Se il proprietario di una sottoscrizione rimuove l'assegnazione del ruolo dell'applicazione usando il portale classico o gli strumenti da riga di comando, l'applicazione non potrà più accedere a tale sottoscrizione. In tal caso, è consigliabile informare l'utente che la connessione con la sottoscrizione è stata interrotta dall'esterno dell'applicazione e offrirgli la possibilità di "ripristinare" la connessione. Il "ripristino" si limiterà a creare di nuovo l'assegnazione del ruolo eliminata offline.

Come si è consentito all'utente di connettere le sottoscrizioni all'applicazione, è necessario consentirgli anche di disconnetterle. Dal punto di vista della gestione degli accessi, disconnettere significa rimuovere l'assegnazione del ruolo che l'entità servizio dell'applicazione ha nella sottoscrizione. Facoltativamente, è possibile rimuovere anche qualsiasi stato dell'applicazione per la sottoscrizione.
Solo gli utenti con l'autorizzazione per la gestione degli accessi possono disconnettere la sottoscrizione.

Il metodo [RevokeRoleFromServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) dell'app di esempio ASP.NET MVC implementa questa chiamata.

Gli utenti ora possono connettere e gestire facilmente le sottoscrizioni di Azure con l'applicazione.

