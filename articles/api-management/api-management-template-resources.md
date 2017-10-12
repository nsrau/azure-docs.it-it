---
title: Risorse del modello Gestione API di Azure | Documentazione Microsoft
description: Informazioni sui tipi di risorse disponibili per l'utilizzo nei modelli del portale per sviluppatori in Gestione API di Azure.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 51a1b4c6-a9fd-4524-9e0e-03a9800c3e94
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 212e7ea7bb2ffea63c7ba210195df0da38aa8f0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-api-management-template-resources"></a>Risorse del modello Gestione API di Azure
Gestione API di Azure fornisce i seguenti tipi di risorse per l'uso nei modelli del portale per sviluppatori.  
  
-   [Risorse di tipo stringa](#strings)  
  
-   [Risorse di tipo glifo](#glyphs)  
  
##  <a name="strings"></a> Risorse di tipo stringa  
 Gestione API offre un set completo di risorse di tipo stringa da usare nel portale per sviluppatori. Tali risorse sono localizzate in tutte le lingue supportate da Gestione API. Il set predefinito di modelli usa queste risorse per le intestazioni di pagina, le etichette e per ogni altra stringa costante visualizzata nel portale per sviluppatori. Per usare una risorsa di tipo stringa nei modelli, specificare il prefisso della stringa della risorsa seguito dal nome della stringa, come illustrato nell'esempio seguente.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 L'esempio deriva dal modello Product list e visualizza i **prodotti** nella parte superiore della pagina.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
 Fare riferimento alle tabelle seguenti per le risorse di tipo stringa da usare nei modelli del portale per sviluppatori. Usare il nome della tabella come prefisso per le risorse stringa in tale tabella.  
  
-   [ApisStrings](#ApisStrings)  
  
-   [ApplicationListStrings](#ApplicationListStrings)  
  
-   [AppDetailsStrings](#AppDetailsStrings)  
  
-   [AppStrings](#AppStrings)  
  
-   [CommonResources](#CommonResources)  
  
-   [CommonStrings](#CommonStrings)  
  
-   [Documentazione](#Documentation)  
  
-   [ErrorPageStrings](#ErrorPageStrings)  
  
-   [IssuesStrings](#IssuesStrings)  
  
-   [NotFoundStrings](#NotFoundStrings)  
  
-   [ProductDetailsStrings](#ProductDetailsStrings)  
  
-   [ProductsStrings](#ProductsStrings)  
  
-   [ProviderInfoStrings](#ProviderInfoStrings)  
  
-   [SigninResources](#SigninResources)  
  
-   [SigninStrings](#SigninStrings)  
  
-   [SignupStrings](#SignupStrings)  
  
-   [SubscriptionListStrings](#SubscriptionListStrings)  
  
-   [SubscriptionStrings](#SubscriptionStrings)  
  
-   [UpdateProfileStrings](#UpdateProfileStrings)  
  
-   [UserProfile](#UserProfile)  
  
###  <a name="ApisStrings"></a> ApisStrings  
  
|Nome|Text|  
|----------|----------|  
|PageTitleApis|API|  
  
###  <a name="AppDetailsStrings"></a> AppDetailsStrings  
  
|Nome|Text|  
|----------|----------|  
|WebApplicationsDetailsTitle|Application preview (Anteprima dell'applicazione)|  
|WebApplicationsRequirementsHeader|Requisiti|  
|WebApplicationsScreenshotAlt|Schermata|  
|WebApplicationsScreenshotsHeader|Screenshots (Schermate)|  
  
###  <a name="ApplicationListStrings"></a> ApplicationListStrings  
  
|Nome|Text|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Are you sure that you want to remove application? (Rimuovere l'applicazione?)|  
|WebDevelopersAppNotPublished|Not published (Non pubblicata)|  
|WebDevelopersAppNotSubminted|Not submitted (Non inviata)|  
|WebDevelopersAppTableCategoryHeader|Categoria|  
|WebDevelopersAppTableNameHeader|Nome|  
|WebDevelopersAppTableStateHeader|Stato|  
|WebDevelopersEditLink|Modificare|  
|WebDevelopersRegisterAppLink|Registrare l'applicazione|  
|WebDevelopersRemoveLink|Rimuovere|  
|WebDevelopersSubmitLink|Submit|  
|WebDevelopersYourApplicationsHeader|Applicazioni|  
  
###  <a name="AppStrings"></a> AppStrings  
  
|Nome|Text|  
|----------|----------|  
|WebApplicationsHeader|Applicazioni|  
  
###  <a name="CommonResources"></a> CommonResources  
  
|Nome|Text|  
|----------|----------|  
|NoItemsToDisplay|No results found. (Nessun risultato trovato.)|  
|GeneralExceptionMessage|Something is not right. (Si è verificato un problema.) It could be a temporary glitch or a bug. (Potrebbe trattarsi di un problema temporaneo o di un bug.) Please, try again. (Riprovare.)|  
|GeneralJsonExceptionMessage|Something is not right. (Si è verificato un problema.) It could be a temporary glitch or a bug. (Potrebbe trattarsi di un problema temporaneo o di un bug.) Please, reload the page and try again. (Ricaricare la pagina e riprovare.)|  
|ConfirmationMessageUnsavedChanges|There are some unsaved changes. (Sono presenti modifiche non salvate.) Are you sure you want to cancel and discard the changes? (Annullare e rimuovere le modifiche?)|  
|AzureActiveDirectory|Azure Active Directory|  
|HttpLargeRequestMessage|Http Request Body too large. (Corpo della richiesta HTTP troppo grande.)|  
  
###  <a name="CommonStrings"></a> CommonStrings  
  
|Nome|Text|  
|----------|----------|  
|ButtonLabelCancel|Annulla|  
|ButtonLabelSave|Salva|  
|GeneralExceptionMessage|Something is not right. (Si è verificato un problema.) It could be a temporary glitch or a bug. (Potrebbe trattarsi di un problema temporaneo o di un bug.) Please, try again. (Riprovare.)|  
|NoItemsToDisplay|There are no items to display. (Nessun elemento da visualizzare.)|  
|PagerButtonLabelFirst|First (Primo)|  
|PagerButtonLabelLast|Last (Ultimo)|  
|PagerButtonLabelNext|Avanti|  
|PagerButtonLabelPrevious|Prev (Precedente)|  
|PagerLabelPageNOfM|Page {0} of {1} (Pagina {0} di {1})|  
|PasswordTooShort|The Password is too short (La password è troppo breve)|  
|EmailAsPassword|Do not use your email as your password (La password non può contenere l'indirizzo di posta elettronica)|  
|PasswordSameAsUserName|Your password cannot contain your username (La password non può contenere il nome utente)|  
|PasswordTwoCharacterClasses|Use different character classes (Usare classi di caratteri differenti)|  
|PasswordTooManyRepetitions|Too many repetitions (Troppe ripetizioni)|  
|PasswordSequenceFound|Your password contains sequences (La password contiene sequenze)|  
|PagerLabelPageSize|Page size (Dimensione della pagina)|  
|CurtainLabelLoading|Loading... (Caricamento in corso...)|  
|TablePlaceholderNothingToDisplay|There is no data for the selected period and scope (Non sono presenti dati per il periodo e l'ambito selezionati)|  
|ButtonLabelClose|Chiudi|  
  
###  <a name="Documentation"></a> Documentazione  
  
|Nome|Text|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Invalid header '{0}' (Intestazione '{0}' non valida)|  
|WebDocumentationInvalidRequestErrorMessage|Invalid Request URL (URL della richiesta non valido)|  
|TextboxLabelAccessToken|Token di accesso *|  
|DropdownOptionPrimaryKeyFormat|Primary-{0} ({0}-primario)|  
|DropdownOptionSecondaryKeyFormat|Secondary-{0} (Secondario-{0})|  
|WebDocumentationSubscriptionKeyText|Your subscription key (Chiave della sottoscrizione)|  
|WebDocumentationTemplatesAddHeaders|Add required HTTP headers (Aggiungere intestazioni HTTP obbligatorie)|  
|WebDocumentationTemplatesBasicAuthSample|Basic Authorization Sample (Esempio di autorizzazione di base)|  
|WebDocumentationTemplatesCurlForBasicAuth|for Basic Authorization use: --user {username}:{password} (per l'utilizzo dell'autorizzazione di base:--user {nomeutente}: {password})|  
|WebDocumentationTemplatesCurlValuesForPath|Specify values for path parameters (shown as {...}), your subscription key and values for query parameters (Specificare i valori dei parametri del percorso (mostrati come {…}), la chiave di sottoscrizione e i valori dei parametri di query)|  
|WebDocumentationTemplatesDeveloperKey|Specify your subscription key (Specificare la chiave della sottoscrizione)|  
|WebDocumentationTemplatesJavaApache|Questo esempio usa il client HTTP di Apache reperibile in HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|Specify values for optional parameters, as needed (Specificare i valori per i parametri facoltativi in base alle esigenze)|  
|WebDocumentationTemplatesPhpPackage|This sample uses the HTTP_Request2 package. (Questo esempio usa il pacchetto HTTP_Request2.) (for more information: http://pear.php.net/package/HTTP_Request2) ((per altre informazioni: http://pear.php.net/package/HTTP_Request2))|  
|WebDocumentationTemplatesPythonValuesForPath|Specify values for path parameters (shown as {...}) and request body if needed (Specificare i valori dei parametri del percorso (mostrati come {…}) e il corpo della richiesta se necessario)|  
|WebDocumentationTemplatesRequestBody|Specify request body (Specificare il corpo della richiesta)|  
|WebDocumentationTemplatesRequiredParams|Specify values for the following required parameters (Specificare i valori per i parametri obbligatori seguenti)|  
|WebDocumentationTemplatesValuesForPath|Specify values for path parameters (shown as {...}) (Specificare i valori dei parametri del percorso (mostrati come {…}))|  
|OAuth2AuthorizationEndpointDescription|The authorization endpoint is used to interact with the resource owner and obtain an authorization grant. (L'endpoint di autorizzazione viene usato per interagire con il proprietario delle risorse e ottenere una concessione di autorizzazione.)|  
|OAuth2AuthorizationEndpointName|Authorization endpoint (Endpoint di autorizzazione)|  
|OAuth2TokenEndpointDescription|The token endpoint is used by the client to obtain an access token by presenting its authorization grant or refresh token. (L'endpoint del token viene usato dal client per ottenere un token di accesso tramite la presentazione della concessione di autorizzazione o il token di aggiornamento.)|  
|OAuth2TokenEndpointName|Token endpoint (Endpoint di token)|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|<p\>The client initiates the flow by directing the resource owner's user-agent to the authorization endpoint. (Il client avvia il flusso indirizzando l'agente utente del proprietario delle risorse all'endpoint di autorizzazione.)  The client includes its client identifier, requested scope, local state, and a redirection URI to which the authorization server will send the user-agent back once access is granted (or denied). (Il client include il proprio identificatore client, l'ambito richiesto, lo stato locale e un URI di reindirizzamento a cui il server di autorizzazione invia l'agente utente una volta concesso o negato l'accesso.)     </p\>     <p\>The authorization server authenticates the resource owner (via the user-agent) and establishes whether the resource owner grants or denies the client's access request. (Il server di autorizzazione autentica il proprietario delle risorse tramite l'agente utente e stabilisce se il proprietario concede o nega la richiesta di accesso del client.)     </p\>     <p\>Assuming the resource owner grants access, the authorization server redirects the user-agent back to the client using the redirection URI provided earlier (in the request or during         client registration). (Supponendo che il proprietario delle risorse conceda l'accesso, il server di autorizzazione reindirizza l'agente utente al client mediante l'URI fornito in precedenza, nella richiesta o durante la registrazione del client.)  The redirection URI includes an authorization code and any local state provided by the client earlier. (L'URI di reindirizzamento include un codice di autorizzazione e qualsiasi stato locale fornito in precedenza dal client.)     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|<p\>If the user denies the access request of if the request is invalid, the client will be informed using the following parameters added on to the redirect: </p\> (Se l'utente rifiuta la richiesta di accesso o se la richiesta non è valida, il client verrà informato mediante l'aggiunta dei seguenti parametri al reindirizzamento:)|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Authorization request (Richiesta di autorizzazione)|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|<p\>The client app must send the user to the authorization endpoint in order to initiate the OAuth process. (Per avviare il processo OAuth, l'applicazione client deve inviare l'utente all'endpoint di autorizzazione.)          At the authorization endpoint, the user authenticates and then grants or denies access to the app. (L'utente esegue l'autenticazione nell'endpoint di autorizzazione e quindi concede o nega l'accesso all'app.)     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|<p\> Assuming the resource owner grants access, authorization server     redirects the user-agent back to the client using the   redirection URI provided earlier (in the request or during client registration). (Supponendo che il proprietario delle risorse conceda l'accesso, il server di autorizzazione reindirizza l'agente utente al client usando l'URI fornito in precedenza, nella richiesta o durante la registrazione del client).  The redirection URI includes an authorization code and any local state provided by the client earlier. (L'URI di reindirizzamento include un codice di autorizzazione e qualsiasi stato locale fornito in precedenza dal client.) </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|<p\>The client requests an access token from the authorization server''s token endpoint by including the authorization code received in the previous step. (Il client richiede un token di accesso all'endpoind di token del server di autorizzazione, includendo il codice di autorizzazione ricevuto nel passaggio precedente.)  When making the request, the client authenticates with the authorization server. (Al momento della richiesta, il client esegue l'autenticazione nel server di autorizzazione.)  The client includes the redirection URI used to obtain the authorization code for verification. (Il client include l'URI di reindirizzamento usato per ottenere il codice di autorizzazione per la verifica.) </p\> <p\>The authorization server authenticates the client, validates the     authorization code, and ensures that the redirection URI received matches the URI used to redirect the client in step (C). (Il server di autorizzazione autentica il client, convalida il codice di autorizzazione e assicura che l'URI di reindirizzamento ricevuto corrisponde all'URI usato per reindirizzare il client nel passaggio (C).)  If valid, the authorization server responds back with an access token and, optionally, a refresh token. (Se è valido, il server di autorizzazione risponde con un token di accesso e, facoltativamente, un token di aggiornamento.) </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|<p\>If the request client authentication failed or is invalid, the authorization server responds with an HTTP 400 (Bad Request)     status code (unless specified otherwise) and includes the following parameters with the response. (Se la richiesta di autenticazione del client non ha esito positivo o non è valida, il server di autorizzazione risponde con un codice di stato HTTP 400 (Richiesta non valida) (se non diversamente specificato) e include nella risposta i parametri seguenti.) </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|<p\> The client makes a request to the token endpoint by sending the     following parameters using the "application/x-www-form-urlencoded"  format with a character encoding of UTF-8 in the HTTP  request entity-body. (Il client esegue una richiesta all'endpoint di token inviando i parametri seguenti nel formato "application/x-www-form-urlencoded" con una codifica dei caratteri UTF-8 nel corpo della richiesta HTTP.) </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|<p\>The authorization server issues an access token and optional refresh    token, and constructs the response by adding the following parameters to the entity-body of the HTTP response with a 200 (OK) status code. (Il server di autorizzazione genera un token di accesso e un token di aggiornamento facoltativo e costruisce la risposta aggiungendo i parametri seguenti al corpo della risposta HTTP con un codice di stato 200 (OK).) </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|<p\>The client authenticates with the authorization server and     requests an access token from the token endpoint. (Il client esegue l'autenticazione nel server di autorizzazione e richiede un token di accesso all'endpoint di token.) </p\> <p\>The authorization server authenticates the client, and if valid, issues an access token. (Il server di autorizzazione autentica il client e, se è valido, genera un token di accesso.) </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|<p\>If the request failed client authentication or is invalid, the authorization server responds with an HTTP 400 (Bad Request)     status code (unless specified otherwise) and includes the following parameters with the response. (Se la richiesta di autenticazione del client non ha esito positivo o non è valida, il server di autorizzazione risponde con un codice di stato HTTP 400 (Richiesta non valida) (se non diversamente specificato) e include nella risposta i parametri seguenti.) </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|<p\> The client makes a request to the token endpoint by adding the     following parameters using the "application/x-www-form-urlencoded"  format with a character encoding of UTF-8 in the HTTP request entity-body. (Il client esegue una richiesta all'endpoint di token aggiungendo i parametri seguenti nel formato "application/x-www-form-urlencoded" con una codifica dei caratteri UTF-8 nel corpo della richiesta HTTP.) </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|<p\> If the access token request is valid and authorized, the authorization server issues an access token and optional refresh     token, and constructs the response by adding the following parameters to the entity-body of the HTTP response with a 200 (OK) status code. (Se la richiesta del token di accesso è valida e autorizzata, il server di autorizzazione genera un token di accesso e un token di aggiornamento facoltativo e costruisce la risposta aggiungendo i parametri seguenti al corpo della risposta HTTP con un codice di stato 200 (OK).) </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|<p\> The client initiates the flow by directing the resource owner''s user-agent to the authorization endpoint. (Il client avvia il flusso indirizzando l'agente utente del proprietario delle risorse all'endpoint di autorizzazione.)  The client includes its client identifier, requested scope, local state, and a redirection URI to which the authorization server will send the user-agent back once access is granted (or denied). (Il client include il proprio identificatore client, l'ambito richiesto, lo stato locale e un URI di reindirizzamento a cui il server di autorizzazione invia l'agente utente una volta concesso o negato l'accesso.) </p\> <p\>The authorization server authenticates the resource owner (via the user-agent) and establishes whether the resource owner grants or denies the client's access request. (Il server di autorizzazione autentica il proprietario delle risorse tramite l'agente utente e stabilisce se il proprietario concede o nega la richiesta di accesso del client.) </p\> <p\>Assuming the resource owner grants access, the authorization server redirects the user-agent back to the client using the redirection URI provided earlier. (Supponendo che il proprietario delle risorse conceda l'accesso, il server di autorizzazione reindirizza l'agente utente al client mediante l'URI fornito in precedenza.)  The redirection URI includes the access token in the URI fragment. (L'URI di reindirizzamento include il token di accesso nel frammento URI.) </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|<p\>If the resource owner denies the access request or if the request     fails for reasons other than a missing or invalid redirection URI, the authorization server informs the client by adding the following parameters to the fragment component of the redirection URI using the "application/x-www-form-urlencoded" format. (Se il proprietario delle risorse rifiuta la richiesta di accesso o questa non ha esito positivo per motivi diversi dal mancato o non valido URI di reindirizzamento, il server di autorizzazione lo comunica al client aggiungendo i parametri seguenti al frammento dell'URI di reindirizzamento usando il formato "application/x-www-form-urlencoded".) </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|<p\>The client app must send the user to the authorization endpoint in order to initiate the OAuth process. (Per avviare il processo OAuth, l'applicazione client deve inviare l'utente all'endpoint di autorizzazione.)      At the authorization endpoint, the user authenticates and then grants or denies access to the app. (L'utente esegue l'autenticazione nell'endpoint di autorizzazione e quindi concede o nega l'accesso all'app.) </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|<p\> If the resource owner grants the access request, the authorization     server issues an access token and delivers it to the client by adding     the following parameters to the fragment component of the redirection     URI using the "application/x-www-form-urlencoded" format. (Se il proprietario delle risorse concede la richiesta di accesso, il server di autorizzazione genera un token di accesso e lo invia al client aggiungendo i parametri seguenti al frammento dell'URI di  reindirizzamento usando il formato "application/x-www-form-urlencoded".) </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|Authorization code flow is optimized for clients capable of maintaining the confidentiality of their credentials (e.g., web server applications implemented using  PHP, Java, Python, Ruby, ASP.NET, etc.). (Il flusso del codice di autorizzazione è ottimizzato per i client in grado di mantenere la riservatezza delle proprie credenziali, ad esempio, applicazioni server Web implementate usando PHP, Java, Python, Ruby, ASP.NET, ecc.)|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Authorization Code grant (Concessione del codice di autorizzazione)|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|Client credentials flow is suitable in cases where the client (your application) is requesting access to the protected resources under its control. (Il flusso di credenziali client è adatto quando il client (l'applicazione) richiede l'accesso alle risorse protette che controlla.) The client is considered as a resource owner, so no end-user interaction is required. (Il client è considerato un proprietario di risorse, pertanto non è necessaria alcuna interazione con l'utente finale.)|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Client Credentials grant (Concessione delle credenziali client)|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Implicit flow is optimized for clients incapable of maintaining the confidentiality of their credentials known to operate a particular redirection URI. (Il flusso implicito è ottimizzato per i client non in grado di mantenere la riservatezza delle proprie credenziali note per usare un URI di reindirizzamento particolare.) These clients are typically implemented in a browser using a scripting language such as JavaScript. (Questi client sono generalmente implementati in un browser usando un linguaggio di scripting come JavaScript.)|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Implicit grant (Concessione implicita)|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|Resource owner password credentials flow is suitable in cases where the resource owner has a trust relationship with the client (your application), such as the device operating system or a highly privileged application. (Il flusso delle credenziali di tipo password del proprietario delle risorse è adatto ai casi in cui il proprietario delle risorse ha una relazione di trust con il client (l'applicazione), ad esempio il sistema operativo del dispositivo o un'applicazione con privilegi elevati.) This flow is suitable for clients capable of obtaining the resource owner's credentials (username and password, typically using an interactive form). (Questo flusso è adatto ai client in grado di ottenere le credenziali del proprietario delle risorse, cioè nome utente e password, in genere tramite un modulo interattivo.)|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Resource Owner Password Credentials grant (Concessione delle credenziali di tipo password del proprietario delle risorse)|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|<p\>The resource owner provides the client with its username and     password. (Il proprietario delle risorse fornisce al client nome utente e password.) </p\> <p\>The client requests an access token from the authorization server''s token endpoint by including the credentials received     from the resource owner. (Il client richiede un token di accesso all'endpoind di token del server di autorizzazione, includendo le credenziali ricevute dal proprietario delle risorse.)  When making the request, the client authenticates with the authorization server. (Al momento della richiesta, il client esegue l'autenticazione nel server di autorizzazione.) </p\> <p\>The authorization server authenticates the client and validates the resource owner credentials, and if valid, issues an access     token. (Il server di autorizzazione autentica il client e convalida le credenziali del proprietario delle risorse; se valide, genera un token di accesso.) </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|<p\>If the request failed client authentication or is invalid, the authorization server responds with an HTTP 400 (Bad Request)     status code (unless specified otherwise) and includes the following parameters with the response. (Se la richiesta di autenticazione del client non ha esito positivo o non è valida, il server di autorizzazione risponde con un codice di stato HTTP 400 (Richiesta non valida) (se non diversamente specificato) e include nella risposta i parametri seguenti.) </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|<p\> The client makes a request to the token endpoint by adding the     following parameters using the "application/x-www-form-urlencoded"  format with a character encoding of UTF-8 in the HTTP request entity-body. (Il client esegue una richiesta all'endpoint di token aggiungendo i parametri seguenti nel formato "application/x-www-form-urlencoded" con una codifica dei caratteri UTF-8 nel corpo della richiesta HTTP.) </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|<p\> If the access token request is valid and authorized, the authorization server issues an access token and optional refresh     token, and constructs the response by adding the following parameters to the entity-body of the HTTP response with a 200 (OK) status code. (Se la richiesta del token di accesso è valida e autorizzata, il server di autorizzazione genera un token di accesso e un token di aggiornamento facoltativo e costruisce la risposta aggiungendo i parametri seguenti al corpo della risposta HTTP con un codice di stato 200 (OK).) </p\>|  
|OAuth2Step_AccessTokenRequest_Name|Access token request (Richiesta di token di accesso)|  
|OAuth2Step_AuthorizationRequest_Name|Authorization request (Richiesta di autorizzazione)|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|OBBLIGATORIO. The access token issued by the authorization server. (Il token di accesso generato dal server di autorizzazione.)|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|OBBLIGATORIO. The access token issued by the authorization server. (Il token di accesso generato dal server di autorizzazione.)|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|OBBLIGATORIO. The access token issued by the authorization server. (Il token di accesso generato dal server di autorizzazione.)|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OBBLIGATORIO. The access token issued by the authorization server. (Il token di accesso generato dal server di autorizzazione.)|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|OBBLIGATORIO. Client identifier. (Identificatore cliente.)|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|REQUIRED if the client is not authenticating with the authorization server. (OBBLIGATORIO se il client non esegue l'autenticazione nel server di autorizzazione.)|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|OBBLIGATORIO. The client identifier. (Identificatore cliente.)|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|OBBLIGATORIO. The authorization code generated by the authorization server. (Il codice di autorizzazione generato dal server di autorizzazione.)|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|OBBLIGATORIO. The authorization code received by the authorization server. (Il codice di autorizzazione ricevuto dal server di autorizzazione.)|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|FACOLTATIVO. Human-readable ASCII text providing additional information. (Testo ASCII leggibile dall'utente che fornisce informazioni aggiuntive.)|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|FACOLTATIVO. Human-readable ASCII text providing additional information. (Testo ASCII leggibile dall'utente che fornisce informazioni aggiuntive.)|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|FACOLTATIVO. Human-readable ASCII text providing additional information. (Testo ASCII leggibile dall'utente che fornisce informazioni aggiuntive.)|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|FACOLTATIVO. Human-readable ASCII text providing additional information. (Testo ASCII leggibile dall'utente che fornisce informazioni aggiuntive.)|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|FACOLTATIVO. Human-readable ASCII text providing additional information. (Testo ASCII leggibile dall'utente che fornisce informazioni aggiuntive.)|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|FACOLTATIVO. A URI identifying a human-readable web page with information about the error. (URI che identifica una pagina Web leggibile dall'utente con informazioni sull'errore.)|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|FACOLTATIVO. A URI identifying a human-readable web page with information about the error. (URI che identifica una pagina Web leggibile dall'utente con informazioni sull'errore.)|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|FACOLTATIVO. A URI identifying a human-readable web page with information about the error. (URI che identifica una pagina Web leggibile dall'utente con informazioni sull'errore.)|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|FACOLTATIVO. A URI identifying a human-readable web page with information about the error. (URI che identifica una pagina Web leggibile dall'utente con informazioni sull'errore.)|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|FACOLTATIVO. A URI identifying a human-readable web page with information about the error. (URI che identifica una pagina Web leggibile dall'utente con informazioni sull'errore.)|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|OBBLIGATORIO. A single ASCII error code from the following: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable. (Un singolo codice di errore ASCII tra i seguenti: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.)|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|OBBLIGATORIO. A single ASCII error code from the following: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope. (Un singolo codice di errore ASCII tra i seguenti: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.)|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|OBBLIGATORIO. A single ASCII error code from the following: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope. (Un singolo codice di errore ASCII tra i seguenti: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.)|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|OBBLIGATORIO. A single ASCII error code from the following: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable. (Un singolo codice di errore ASCII tra i seguenti: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.)|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|OBBLIGATORIO. A single ASCII error code from the following: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope. (Un singolo codice di errore ASCII tra i seguenti: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.)|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|CONSIGLIATO. The lifetime in seconds of the access token. (La durata in secondi del token di accesso.)|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|CONSIGLIATO. The lifetime in seconds of the access token. (La durata in secondi del token di accesso.)|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|CONSIGLIATO. The lifetime in seconds of the access token. (La durata in secondi del token di accesso.)|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|CONSIGLIATO. The lifetime in seconds of the access token. (La durata in secondi del token di accesso.)|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|OBBLIGATORIO. Value MUST be set to "authorization_code". (Il valore deve essere impostato su "authorization_code".)|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|OBBLIGATORIO. Value MUST be set to "client_credentials". (Il valore deve essere impostato su "client_credentials".)|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|OBBLIGATORIO. Value MUST be set to "password". (Il valore deve essere impostato su "password".)|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|OBBLIGATORIO. The resource owner password. (La password del proprietario delle risorse.)|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|FACOLTATIVO. The redirection endpoint URI must be an absolute URI. (L'URI dell'endpoint di reindirizzamento deve essere un URI assoluto.)|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|REQUIRED if the "redirect_uri" parameter was included in the authorization request, and their values MUST be identical. (OBBLIGATORIO se il parametro "redirect_uri" è stato incluso nella richiesta di autorizzazione; i relativi valori devono essere identici.)|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|FACOLTATIVO. The redirection endpoint URI must be an absolute URI. (L'URI dell'endpoint di reindirizzamento deve essere un URI assoluto.)|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|FACOLTATIVO. The refresh token, which can be used to obtain new access tokens. (Il token di aggiornamento, che può essere usato per ottenere nuovi token di accesso.)|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|FACOLTATIVO. The refresh token, which can be used to obtain new access tokens. (Il token di aggiornamento, che può essere usato per ottenere nuovi token di accesso.)|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|FACOLTATIVO. The refresh token, which can be used to obtain new access tokens. (Il token di aggiornamento, che può essere usato per ottenere nuovi token di accesso.)|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|OBBLIGATORIO. Value MUST be set to "code". (Il valore deve essere impostato su "code".)|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|OBBLIGATORIO. Value MUST be set to "token". (Il valore deve essere impostato su "token".)|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|FACOLTATIVO. The scope of the access request. (L'ambito della richiesta di accesso.)|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|OPTIONAL if identical to the scope requested by the client; otherwise, REQUIRED. (FACOLTATIVO se è identico all'ambito richiesto dal client; in caso contrario, è OBBLIGATORIO.)|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|FACOLTATIVO. The scope of the access request. (L'ambito della richiesta di accesso.)|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|OPTIONAL if identical to the scope requested by the client; otherwise, REQUIRED. (FACOLTATIVO se è identico all'ambito richiesto dal client; in caso contrario, è OBBLIGATORIO.)|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|FACOLTATIVO. The scope of the access request. (L'ambito della richiesta di accesso.)|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|OPTIONAL if identical to the scope requested by the client; otherwise, REQUIRED. (FACOLTATIVO se è identico all'ambito richiesto dal client; in caso contrario, è OBBLIGATORIO.)|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|FACOLTATIVO. The scope of the access request. (L'ambito della richiesta di accesso.)|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OPTIONAL if identical to the scope requested by the client; otherwise, REQUIRED. (FACOLTATIVO se è identico all'ambito richiesto dal client; in caso contrario, è OBBLIGATORIO.)|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|REQUIRED if the "state" parameter was present in the client authorization request. (OBBLIGATORIO se il parametro "state" era presente nella richiesta di autorizzazione del client.)  The exact value received from the client. (L'esatto valore ricevuto dal client.)|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|CONSIGLIATO. An opaque value used by the client to maintain state between the request and callback. (Un valore opaco usato dal client per mantenere lo stato tra la richiesta e il callback.)  The authorization server includes this value when redirecting the user-agent back to the client. (Il server di autorizzazione include questo valore quando reindirizza l'agente utente al client.)  The parameter SHOULD be used for preventing cross-site request forgery. (È necessario usare il parametro per prevenire richieste intersito false.)|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|REQUIRED if the "state" parameter was present in the client authorization request. (OBBLIGATORIO se il parametro "state" era presente nella richiesta di autorizzazione del client.)  The exact value received from the client. (L'esatto valore ricevuto dal client.)|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|REQUIRED if the "state" parameter was present in the client authorization request. (OBBLIGATORIO se il parametro "state" era presente nella richiesta di autorizzazione del client.)  The exact value received from the client. (L'esatto valore ricevuto dal client.)|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|CONSIGLIATO. An opaque value used by the client to maintain state between the request and callback. (Un valore opaco usato dal client per mantenere lo stato tra la richiesta e il callback.)  The authorization server includes this value when redirecting the user-agent back to the client. (Il server di autorizzazione include questo valore quando reindirizza l'agente utente al client.)  The parameter SHOULD be used for preventing cross-site request forgery. (È necessario usare il parametro per prevenire richieste intersito false.)|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|REQUIRED if the "state" parameter was present in the client authorization request. (OBBLIGATORIO se il parametro "state" era presente nella richiesta di autorizzazione del client.)  The exact value received from the client. (L'esatto valore ricevuto dal client.)|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|OBBLIGATORIO. The type of the token issued. (Il tipo di token generato.)|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|OBBLIGATORIO. The type of the token issued. (Il tipo di token generato.)|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|OBBLIGATORIO. The type of the token issued. (Il tipo di token generato.)|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OBBLIGATORIO. The type of the token issued. (Il tipo di token generato.)|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|OBBLIGATORIO. Il nome utente del proprietario delle risorse.|  
|OAuth2UnsupportedTokenType|Token type '{0}' is not supporetd. (Il tipo di token "{0}" non è supportato.)|  
|OAuth2InvalidState|Invalid response from authorization server (Risposta non valida dal server di autorizzazione)|  
|OAuth2GrantType_AuthorizationCode|Authorization code (Codice di autorizzazione)|  
|OAuth2GrantType_Implicit|Implicit (Implicita)|  
|OAuth2GrantType_ClientCredentials|Credenziali del client|  
|OAuth2GrantType_ResourceOwnerPassword|Resource owner password. (Password del proprietario delle risorse.)|  
|WebDocumentation302Code|302 - Trovato|  
|WebDocumentation400Code|400 - Richiesta non valida|  
|OAuth2SendingMethod_AuthHeader|Authorization header (Intestazione dell'autorizzazione)|  
|OAuth2SendingMethod_QueryParam|Query parameter (Parametro di query)|  
|OAuth2AuthorizationServerGeneralException|An error has occurred while authorizing access via {0} (Si è verificato un errore durante l'autorizzazione all'accesso tramite {0})|  
|OAuth2AuthorizationServerCommunicationException|An HTTP connection to authorization server could not be established or it has been unexpectedly closed. (Non è stato possibile stabilire la connessione HTTP al server di autorizzazione oppure è stata chiusa in modo imprevisto.)|  
|WebDocumentationOAuth2GeneralErrorMessage|Errore imprevisto.|  
|AuthorizationServerCommunicationException|Authorization server communication exception has happened. (Si è verificata un'eccezione di comunicazione con il server di autorizzazione.) Contattare l'amministratore.|  
|TextblockSubscriptionKeyHeaderDescription|Subscription key which provides access to this API. (Chiave di sottoscrizione che fornisce l'accesso a questa API.) Si trova in <a href='/developer'\>Profilo</a\>.|  
|TextblockOAuthHeaderDescription|OAuth 2.0 access token obtained from <i\>{0}</i\>. (Token di accesso OAuth 2.0 ottenuto da <> </> \>{0}\>.) Supported grant types: <i\>{1}</i\>. (Tipi di concessione supportati: <i\>{1}</i\>.)|  
|TextblockContentTypeHeaderDescription|Media type of the body sent to the API. (Tipo di supporto del corpo inviato all'API.)|  
|ErrorMessageApiNotAccessible|The API you are trying to call is not accessible at this time. (Si sta tentando di chiamare un'API non accessibile al momento.) Contattare l'autore dell'API <a href="/issues"\>qui</a\>.|  
|ErrorMessageApiTimedout|The API you are trying to call is taking longer than normal to get response back. (L'API che si sta tentando di chiamare impiega più tempo del previsto per ottenere risposta.) Contattare l'autore dell'API <a href="/issues"\>qui</a\>.|  
|BadRequestParameterExpected|"'{0}' parameter is expected" ("È previsto il parametro '{0}'")|  
|TooltipTextDoubleClickToSelectAll|Double click to select all. (Fare doppio clic per selezionare tutto.)|  
|TooltipTextHideRevealSecret|Mostra/Nascondi|  
|ButtonLinkOpenConsole|Prova|  
|SectionHeadingRequestBody|Corpo della richiesta|  
|SectionHeadingRequestParameters|Parametri della richiesta|  
|SectionHeadingRequestUrl|URL richiesta|  
|SectionHeadingResponse|Response|  
|SectionHeadingRequestHeaders|Intestazioni della richiesta|  
|FormLabelSubtextOptional|Facoltativo|  
|SectionHeadingCodeSamples|Esempi di codice|  
|TextblockOpenidConnectHeaderDescription|OpenID Connect id token obtained from <i\>{0}</i\>. (Token ID di OpenID Connect ottenuto da <i\>{0}</i\>.) Supported grant types: <i\>{1}</i\>. (Tipi di concessione supportati: <i\>{1}</i\>.)|  
  
###  <a name="ErrorPageStrings"></a> ErrorPageStrings  
  
|Nome|Text|  
|----------|----------|  
|LinkLabelBack|Indietro|  
|LinkLabelHomePage|home page|  
|LinkLabelSendUsEmail|Inviare un messaggio di posta elettronica|  
|PageTitleError|Sorry, there was a problem serving the requested page (Si è verificato un problema nella gestione della pagina richiesta)|  
|TextblockPotentialCauseIntermittentIssue|This may be an intermittent data access issue that is already gone. (Potrebbe trattarsi di un problema di accesso intermittente ai dati ora non presente.)|  
|TextblockPotentialCauseOldLink|The link you have clicked on may be old and not point to the correct location anymore. (Il collegamento selezionato potrebbe essere obsoleto e non puntare più al percorso corretto.)|  
|TextblockPotentialCauseTechnicalProblem|There may be a technical problem on our end. (Potenziale problema tecnico.)|  
|TextblockPotentialSolutionRefresh|Provare ad aggiornare la pagina.|  
|TextblockPotentialSolutionStartOver|Start over from our {0}. (Ricominciare dal nostro {0}.)|  
|TextblockPotentialSolutionTryAgain|Go {0} and try the action you performed again. (Passare a {0} e ripetere nuovamente l'azione eseguita.)|  
|TextReportProblem|{0} describing what went wrong and we will look at it as soon as we can. ({0} descrivendo l'errore; verrà esaminato non appena possibile.)|  
|TitlePotentialCause|Potential cause (Causa potenziale)|  
|TitlePotentialSolution|It's possibly just a temporary issue, a few things to try (Potrebbe trattarsi di un problema temporaneo, alcune soluzioni da provare)|  
  
###  <a name="IssuesStrings"></a> IssuesStrings  
  
|Nome|Text|  
|----------|----------|  
|WebIssuesIndexTitle|Problemi|  
|WebIssuesNoActiveSubscriptions|Non si dispone di sottoscrizioni attive. You need to subscribe for a product to report an issue. (È necessario disporre della sottoscrizione al prodotto per segnalare un problema.)|  
|WebIssuesNotSignin|You're not signed in. (Non è stato eseguito l'accesso.) Please {0} to report an issue or post a comment. ({0} per segnalare un problema o inviare un commento.)|  
|WebIssuesReportIssueButton|Report Issue (Segnala un problema)|  
|WebIssuesSignIn|sign in|  
|WebIssuesStatusReportedBy|Status: {0} &#124; Reported by {1} (Stato: {0} &#124; Segnalato da {1})|  
  
###  <a name="NotFoundStrings"></a> NotFoundStrings  
  
|Nome|Text|  
|----------|----------|  
|LinkLabelHomePage|home page|  
|LinkLabelSendUsEmail|Inviare un messaggio di posta elettronica|  
|PageTitleNotFound|Sorry, we can't find the page you are looking for (Impossibile trovare la pagina cercata)|  
|TextblockPotentialCauseMisspelledUrl|You may have misspelled the URL if you typed it in. (L'URL digitato potrebbe essere errato.)|  
|TextblockPotentialCauseOldLink|The link you have clicked on may be old and not point to the correct location anymore. (Il collegamento selezionato potrebbe essere obsoleto e non puntare più al percorso corretto.)|  
|TextblockPotentialSolutionRetype|Try retyping the URL. (Digitare nuovamente l'URL.)|  
|TextblockPotentialSolutionStartOver|Start over from our {0}. (Ricominciare dal nostro {0}.)|  
|TextReportProblem|{0} describing what went wrong and we will look at it as soon as we can. ({0} descrivendo l'errore; verrà esaminato non appena possibile.)|  
|TitlePotentialCause|Potential cause (Causa potenziale)|  
|TitlePotentialSolution|Potential solution (Potenziale soluzione)|  
  
###  <a name="ProductDetailsStrings"></a> ProductDetailsStrings  
  
|Nome|Text|  
|----------|----------|  
|WebProductsAgreement|By subscribing to {0} Product, I agree to the `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`. (Con la sottoscrizione al prodotto {0}, accetto le `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`.)|  
|WebProductsLegalTermsLink|Condizioni per l'utilizzo|  
|WebProductsSubscribeButton|Sottoscrivi|  
|WebProductsUsageLimitsHeader|Limiti di consumo|  
|WebProductsYouAreNotSubscribed|You are subscribed to this product. (È presente una sottoscrizione al prodotto.)|  
|WebProductsYouRequestedSubscription|You requested subscription to this product. (È stata richiesta una sottoscrizione al prodotto.)|  
|ErrorYouNeedtoAgreeWithLegalTerms|You must agree to the Terms of Use before you can proceed. (È necessario accettare le Condizioni per l'utilizzo prima di continuare.)|  
|ButtonLabelAddSubscription|Aggiungi sottoscrizione|  
|LinkLabelChangeSubscriptionName|change (modifica)|  
|ButtonLabelConfirm|Confirm|  
|TextblockMultipleSubscriptionsCount|You have {0} subscriptions to this product: (Sono presenti {0} sottoscrizioni al prodotto:)|  
|TextblockSingleSubscriptionsCount|You have {0} subscription to this product: (Sono presenti {0} sottoscrizioni al prodotto:)|  
|TextblockSingleApisCount|This product contains {0} API: (Questo prodotto contiene {0} API:)|  
|TextblockMultipleApisCount|This product contains {0} APIs: (Questo prodotto contiene {0} API:)|  
|TextblockHeaderSubscribe|Subscribe to product (Sottoscrizione al prodotto)|  
|TextblockSubscriptionDescription|A new subscription will be created as follows: (Verrà creata la nuova sottoscrizione indicata di seguito:)|  
|TextblockSubscriptionLimitReached|Subscriptions limit reached. (È stato raggiunto il limite di sottoscrizioni.)|  
  
###  <a name="ProductsStrings"></a> ProductsStrings  
  
|Nome|Text|  
|----------|----------|  
|PageTitleProducts|Prodotti|  
  
###  <a name="ProviderInfoStrings"></a> ProviderInfoStrings  
  
|Nome|Text|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|Sign in is disabled by the administrators at the moment. (L'accesso è al momento disabilitato dagli amministratori.)|  
|TextboxExternalIdentitiesSigninInvitation|Alternatively, sign in with (In alternativa, accedere con)|  
|TextboxExternalIdentitiesSigninInvitationPrimary|Sign in with: (Accedere con:)|  
  
###  <a name="SigninResources"></a> SigninResources  
  
|Nome|Text|  
|----------|----------|  
|PrincipalNotFound|Principal is not found or signature is invalid (Entità non trovata o firma non valida)|  
|ErrorSsoAuthenticationFailed|SSO authentication failed (Autenticazione SSO non riuscita)|  
|ErrorSsoAuthenticationFailedDetailed|Invalid token provided or signature cannot be verified. (È stato fornito un token non valido oppure è impossibile verificare la firma.)|  
|ErrorSsoTokenInvalid|SSO token is invalid (Token SSO non valido.)|  
|ValidationErrorSpecificEmailAlreadyExists|Email '{0}' already registered (L'indirizzo di posta elettronica '{0}' è già registrato)|  
|ValidationErrorSpecificEmailInvalid|Email '{0}' is invalid (L'indirizzo di posta elettronica '{0}' non è valido)|  
|ValidationErrorPasswordInvalid|Password non valida. Correggere gli errori e riprovare.|  
|PropertyTooShort|{0} is too short ({0} è troppo breve)|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Indirizzo di posta elettronica non valido.|  
|ValidationMessageNewPasswordConfirmationRequired|Conferma nuova password|  
|ValidationErrorPasswordConfirmationRequired|Confirm password is empty (La password di conferma è vuota)|  
|WebAuthenticationEmailChangeNotice|Change confirmation email is on the way to {0}. (Il messaggio di conferma di modifica della posta elettronica è stato inviato a {0}.) Please follow instructions within it to confirm your new email address. (Seguire le istruzioni in esso contenute per confermare il nuovo indirizzo.) If the email does not arrive to your inbox in the next few minutes, please check your junk email folder. (Se non si riceve il messaggio entro pochi minuti, controllare la cartella della posta indesiderata.)|  
|WebAuthenticationEmailChangeNoticeHeader|Your email change request was successfully processed (La richiesta di modifica dell'indirizzo di posta elettronica è stata elaborata)|  
|WebAuthenticationEmailChangeNoticeTitle|Email change requested (Modifica dell'indirizzo di posta elettronica richiesta)|  
|WebAuthenticationEmailHasBeenRevertedNotice|You email already exist. (Indirizzo di posta elettronica già esistente.) Request has been reverted (La richiesta è stata ripristinata)|  
|ValidationErrorEmailAlreadyExists|Email already exist. (Indirizzo di posta elettronica già esistente)|  
|ValidationErrorEmailInvalid|Invalid e-mail address (Indirizzo di posta elettronica non valido)|  
|TextboxLabelEmail|Email|  
|ValidationErrorEmailRequired|L'indirizzo di posta elettronica è obbligatorio.|  
|WebAuthenticationErrorNoticeHeader|Errore|  
|WebAuthenticationFieldLengthErrorMessage|{0} must be a maximum length of {1} ({0} deve avere una lunghezza massima di {1})|  
|TextboxLabelEmailFirstName|Nome|  
|ValidationErrorFirstNameRequired|First name is required. (Un nome è obbligatorio.)|  
|ValidationErrorFirstNameInvalid|Invalid first name (Nome non valido)|  
|NoticeInvalidInvitationToken|Please note that confirmation links are valid for only 48 hours. (I collegamenti di conferma sono validi solo per 48 ore.) If you are still within this timeframe, please make sure your link is correct. (Se si è ancora entro questo intervallo di tempo, verificare che il collegamento sia corretto.) If your link has expired, then please repeat the action you're trying to confirm. (Se il collegamento è scaduto, ripetere l'azione che si intende confermare.)|  
|NoticeHeaderInvalidInvitationToken|Invalid invitation token (Token di invito non valido)|  
|NoticeTitleInvalidInvitationToken|Confirmation error (Errore di conferma)|  
|WebAuthenticationLastNameInvalidErrorMessage|Cognome non valido|  
|TextboxLabelEmailLastName|Cognome|  
|ValidationErrorLastNameRequired|Il cognome è obbligatorio.|  
|WebAuthenticationLinkExpiredNotice|Confirmation link sent to you has expired. (Il collegamento di conferma inviato è scaduto.) `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|Your password reset link is invalid or expired. (Il collegamento per il ripristino della password non è valido o è scaduto).|  
|WebAuthenticationLinkExpiredNoticeTitle|Link sent (Collegamento inviato)|  
|WebAuthenticationNewPasswordLabel|Nuova password|  
|ValidationMessageNewPasswordRequired|New password is required. (La nuova password è obbligatoria.)|  
|TextboxLabelNotificationsSenderEmail|Notifications sender email (Indirizzo di posta elettronica del mittente delle notifiche)|  
|TextboxLabelOrganizationName|Nome organizzazione|  
|WebAuthenticationOrganizationRequiredErrorMessage|Organization name is empty (Il nome dell'organizzazione è vuoto)|  
|WebAuthenticationPasswordChangedNotice|Your password was successfully updated (Aggiornamento della password completato)|  
|WebAuthenticationPasswordChangedNoticeTitle|Password aggiornata|  
|WebAuthenticationPasswordCompareErrorMessage|Passwords don't match (Le password non corrispondono)|  
|WebAuthenticationPasswordConfirmLabel|Conferma password|  
|ValidationErrorPasswordInvalidDetailed|Password is too weak. (La password è troppo debole.)|  
|WebAuthenticationPasswordLabel|Password|  
|ValidationErrorPasswordRequired|Password is required. (La password è obbligatoria.)|  
|WebAuthenticationPasswordResetSendNotice|Change password confirmation email is on the way to {0}. (Il messaggio di conferma di modifica password è stato inviato a {0}.) Please follow the instructions within the email to continue your password change process. (Seguire le istruzioni contenute nel messaggio per continuare la procedura di modifica della password.)|  
|WebAuthenticationPasswordResetSendNoticeHeader|Your passowrd change request was successfully processed (La richiesta di modifica della password è stata elaborata)|  
|WebAuthenticationPasswordResetSendNoticeTitle|Password reset requested (Reimpostazione password richiesta)|  
|WebAuthenticationRequestNotFoundNotice|Request not found (Richiesta non trovata)|  
|WebAuthenticationSenderEmailRequiredErrorMessage|Notifications sender email is empty (L'indirizzo di posta elettronica del mittente delle notifiche è vuoto)|  
|WebAuthenticationSigninPasswordLabel|Please confirm the change by entering a password (Per confermare la modifica, immettere una password)|  
|WebAuthenticationSignupConfirmNotice|Registration confirmation email is on its way to {0}.<br /\> Please follow instructions within the email to activate your account.<br /\> If the email does not arrive in your inbox within the next few minutes, please check your junk email folder. (Il messaggio di conferma della registrazione è stato inviato a {0}.<br /\> Seguire le istruzioni contenute nel messaggio per attivare l'account.<br /\> Se non si riceve il messaggio entro pochi minuti, controllare la cartella della posta indesiderata.)|  
|WebAuthenticationSignupConfirmNoticeHeader|Your account was successfully created (L'account è stato creato)|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|Registration confirmation email was sent again (Il messaggio di posta elettronica di conferma della registrazione è stato inviato di nuovo)|  
|WebAuthenticationSignupConfirmNoticeTitle|Account creato|  
|WebAuthenticationTokenRequiredErrorMessage|Token is empty (Il token è vuoto)|  
|WebAuthenticationUserAlreadyRegisteredNotice|It seems a user with this email is already registered in the system. (Un utente con questo indirizzo email è già registrato nel sistema.) If you forgot your password, please try to restore it or contact our support team. (Se la password è stata dimenticata, provare a ripristinarla o contattare il supporto tecnico.)|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|User already registered (Utente già registrato)|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Already registered (Già registrato)|  
|ButtonLabelChangePassword|Cambia password|  
|ButtonLabelChangeAccountInfo|Change account information(Modifica informazioni sull'account)|  
|ButtonLabelCloseAccount|Close account (Chiudi account)|  
|WebAuthenticationInvalidCaptchaErrorMessage|Text entered doesn't match text on the picture. (Il testo immesso non corrisponde al testo dell'immagine.) Riprova più tardi.|  
|ValidationErrorCredentialsInvalid|Email or password is invalid. (Indirizzo email o password non validi.) Correggere gli errori e riprovare.|  
|WebAuthenticationRequestIsNotValid|Richiesta non valida.|  
|WebAuthenticationUserIsNotConfirm|Please confirm your registration before attempting to sign in. (Confermare la registrazione prima di tentare l'accesso.)|  
|WebAuthenticationInvalidEmailFormated|Email is invalid: {0} (L'indirizzo di posta elettronica non è valido: {0})|  
|WebAuthenticationUserNotFound|Utente non trovato|  
|WebAuthenticationTenantNotRegistered|Your account belongs to a Azure Active Directory tenant which is not authorized to access this portal. (L'account appartiene a un tenant Azure Active Directory non autorizzato ad accedere a questo portale.)|  
|WebAuthenticationAuthenticationFailed|Authentication has failed. (Autenticazione non riuscita.)|  
|WebAuthenticationGooglePlusNotEnabled|Authentication has failed. (Autenticazione non riuscita.) If you authorized the application then please contact the admin to make sure that Google authentication is configured correctly. (Se l'applicazione è stata autorizzata, contattare l'amministratore per assicurarsi che l'autenticazione di Google sia configurata correttamente.)|  
|ValidationErrorAllowedTenantIsRequired|Allowed Tenant is required (Il campo Tenant consentito è obbligatorio)|  
|ValidationErrorTenantIsNotValid|The Azure Active Directory tenant '{0}' is not valid. (Il tenant Azure Active Directory '{0}' non è valido.)|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Log in using your {0} account (Accedere con l'account {0})|  
|WebAuthenticationUserLimitNotice|This service has reached the maximum number of allowed users. (È stato raggiunto il numero massimo di utenti consentiti dal servizio.) Please `<a href="mailto:{0}"\>contact the administrator</a\>` to upgrade their service and re-enable user registration. (`<a href="mailto:{0}"\>contact the administrator</a\>` per aggiornare il servizio e riabilitare la registrazione dell'utente.)|  
|WebAuthenticationUserLimitNoticeHeader|User registration disabled (Registrazione utente disabilitata)|  
|WebAuthenticationUserLimitNoticeTitle|User registration disabled (Registrazione utente disabilitata)|  
|WebAuthenticationUserRegistrationDisabledNotice|Registration of users has been disabled by the administrator. (La registrazione degli utenti è stata disabilitata dall'amministratore.) Please login with external identity provider. (Accedere con un provider di identità esterno.)|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|User registration disabled (Registrazione utente disabilitata)|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|User registration disabled (Registrazione utente disabilitata)|  
|WebAuthenticationSignupPendingConfirmationNotice|Before we can complete the creation of your account we need to verify your e-mail address. (Prima di completare la creazione dell'account è necessario verificare l'indirizzo di posta elettronica.) We've sent an e-mail to {0}. (Un messaggio di posta elettronica è stato inviato a {0}.) Please follow the instructions inside the e-mail to activate your account. (Seguire le istruzioni contenute nel messaggio per attivare l'account.) If the e-mail doesn't arrive within the next few minutes, please check your junk email folder. (Se non si riceve il messaggio entro pochi minuti, controllare la cartella della posta indesiderata.)|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|We found an unconfirmed account for the e-mail address {0}. (È presente un account non confermato per l'indirizzo di posta elettronica {0}.) To complete the creation of your account we need to verify your e-mail address. (Per completare la creazione dell'account è necessario verificare l'indirizzo di posta elettronica.) We've sent an e-mail to {0}. (Un messaggio di posta elettronica è stato inviato a {0}.) Please follow the instructions inside the e-mail to activate your account. (Seguire le istruzioni contenute nel messaggio per attivare l'account.) If the e-mail doesn't arrive within the next few minutes, please check your junk email folder (Se non si riceve il messaggio entro pochi minuti, controllare la cartella della posta indesiderata)|  
|WebAuthenticationSignupConfirmationAlmostDone|Almost Done (La procedura è quasi completata)|  
|WebAuthenticationSignupConfirmationEmailSent|We've sent an e-mail to {0}. (Un messaggio di posta elettronica è stato inviato a {0}.) Please follow the instructions inside the e-mail to activate your account. (Seguire le istruzioni contenute nel messaggio per attivare l'account.) If the e-mail doesn't arrive within the next few minutes, please check your junk email folder. (Se non si riceve il messaggio entro pochi minuti, controllare la cartella della posta indesiderata.)|  
|WebAuthenticationEmailSentNotificationMessage|Email sent successfully to {0} (Messaggio di posta elettronica inviato a {0})|  
|WebAuthenticationNoAadTenantConfigured|No Azure Active Directory tenant configured for the service. (Nessun tenant Azure Active Directory configurato per il servizio.)|  
|CheckboxLabelUserRegistrationTermsConsentRequired|I agree to the `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`. (Accetto le `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`.)|  
|TextblockUserRegistrationTermsProvided|Please review `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>` (Rivedere le `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`)|  
|DialogHeadingTermsOfUse|Condizioni per l'utilizzo|  
|ValidationMessageConsentNotAccepted|You must agree to the Terms of Use before you can proceed. (È necessario accettare le Condizioni per l'utilizzo prima di continuare.)|  
  
###  <a name="SigninStrings"></a> SigninStrings  
  
|Nome|Text|  
|----------|----------|  
|WebAuthenticationForgotPassword|Password dimenticata?|  
|WebAuthenticationIfAdministrator|If you are an Administrator you must sign in `<a href="{0}"\>here</a\>`. (Gli amministratori devono accedere da `<a href="{0}"\>here</a\>`.)|  
|WebAuthenticationNotAMember|Not a member yet? (Non si è ancora membri?) `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Remember me on this computer (Memorizza utente su questo computer)|  
|WebAuthenticationSigininWithPassword|Sign in with your username and password (Immettere il proprio nome utente e la password)|  
|WebAuthenticationSigninTitle|pagina di accesso|  
|WebAuthenticationSignUpNow|Effettua l'iscrizione ora|  
  
###  <a name="SignupStrings"></a> SignupStrings  
  
|Nome|Text|  
|----------|----------|  
|PageTitleSignup|Iscrizione|  
|WebAuthenticationAlreadyAMember|Already a member? (Si è già membri?)|  
|WebAuthenticationCreateNewAccount|Create a new API Management account (Creare un nuovo account di Gestione API)|  
|WebAuthenticationSigninNow|Sign in now (Accedere ora)|  
|ButtonLabelSignup|Iscrizione|  
  
###  <a name="SubscriptionListStrings"></a> SubscriptionListStrings  
  
|Nome|Text|  
|----------|----------|  
|SubscriptionCancelConfirmation|Are you sure that you want to cancel this subscription? (Annullare la sottoscrizione?)|  
|SubscriptionRenewConfirmation|Are you sure that you want to renew this subscription? (Rinnovare la sottoscrizione?)|  
|WebDevelopersManageSubscriptions|Gestisci sottoscrizioni|  
|WebDevelopersPrimaryKey|Chiave primaria|  
|WebDevelopersRegenerateLink|Regenerate (Rigenera)|  
|WebDevelopersSecondaryKey|Secondary key (Chiave secondaria)|  
|ButtonLabelShowKey|Mostra|  
|ButtonLabelRenewSubscription|Renew|  
|WebDevelopersSubscriptionReqested|Requested on {0} (Richiesta il {0})|  
|WebDevelopersSubscriptionRequestedState|Requested (Richiesta)|  
|WebDevelopersSubscriptionTableNameHeader|Nome|  
|WebDevelopersSubscriptionTableStateHeader|Stato|  
|WebDevelopersUsageStatisticsLink|Analytics reports (Report di analisi)|  
|WebDevelopersYourSubscriptions|Your subscriptions (Sottoscrizioni)|  
|SubscriptionPropertyLabelRequestedDate|Requested on (Richiesta il)|  
|SubscriptionPropertyLabelStartedDate|Started on (Avviata il)|  
|PageTitleRenameSubscription|Rename subscription (Rinomina sottoscrizione)|  
|SubscriptionPropertyLabelName|Nome della sottoscrizione|  
  
###  <a name="SubscriptionStrings"></a> SubscriptionStrings  
  
|Nome|Text|  
|----------|----------|  
|SectionHeadingCloseAccount|Looking to close your account? (Chiudere l'account?)|  
|PageTitleDeveloperProfile|Profilo|  
|ButtonLabelHideKey|Nascondi|  
|ButtonLabelRegenerateKey|Regenerate (Rigenera)|  
|InformationMessageKeyWasRegenerated|Are you sure that you want to regenerate this key? (Rigenerare la chiave?)|  
|ButtonLabelShowKey|Mostra|  
  
###  <a name="UpdateProfileStrings"></a> UpdateProfileStrings  
  
|Nome|Text|  
|----------|----------|  
|ButtonLabelUpdateProfile|Update profile (Aggiorna profilo)|  
|PageTitleUpdateProfile|Update account information (Aggiorna informazioni sull'account)|  
  
###  <a name="UserProfile"></a> UserProfile  
  
|Nome|Text|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Change account information(Modifica informazioni sull'account)|  
|ButtonLabelChangePassword|Cambia password|  
|ButtonLabelCloseAccount|Close account (Chiudi account)|  
|TextboxLabelEmail|Email|  
|TextboxLabelEmailFirstName|Nome|  
|TextboxLabelEmailLastName|Cognome|  
|TextboxLabelNotificationsSenderEmail|Notifications sender email (Indirizzo di posta elettronica del mittente delle notifiche)|  
|TextboxLabelOrganizationName|Nome organizzazione|  
|SubscriptionStateActive|Attivo|  
|SubscriptionStateCancelled|Operazione annullata|  
|SubscriptionStateExpired|Scaduto|  
|SubscriptionStateRejected|Rifiutato|  
|SubscriptionStateRequested|Requested (Richiesta)|  
|SubscriptionStateSuspended|Suspended|  
|DefaultSubscriptionNameTemplate|{0}  (impostazione predefinita)|  
|SubscriptionNameTemplate|Accesso sviluppatore n.: {0}|  
|TextboxLabelSubscriptionName|Nome della sottoscrizione|  
|ValidationMessageSubscriptionNameRequired|Il nome della sottoscrizione non può essere vuoto.|  
|ApiManagementUserLimitReached|This service has reached the maximum number of allowed users. (È stato raggiunto il numero massimo di utenti consentiti dal servizio.) Please upgrade to a higher pricing tier. (Eseguire l'aggiornamento a un piano tariffario superiore.)|  
  
##  <a name="glyphs"></a> Risorse di tipo glifo  
 Nei modelli del portale per sviluppatori di Gestione API è possibile usare i glifi presenti in [Glyphicons da Bootstrap](http://getbootstrap.com/components/#glyphicons). Si tratta di un set di oltre 250 glifi in formato carattere dal set Halflings di [Glyphicon](http://glyphicons.com/). Per usare uno dei glifi del set, applicare la sintassi seguente.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 Per l'elenco completo dei glifi, vedere [Glyphicons da Bootstrap](http://getbootstrap.com/components/#glyphicons).

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sull'uso dei modelli, vedere [Come personalizzare il portale per sviluppatori di Gestione API usando i modelli](api-management-developer-portal-templates.md).
