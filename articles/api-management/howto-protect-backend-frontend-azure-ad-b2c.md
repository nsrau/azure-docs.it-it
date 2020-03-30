---
title: Proteggi il back-end SPA con OAuth 2.0 usando Azure Active Directory B2C e Gestione API di Azure.
description: Proteggere un'API con OAuth 2.0 usando Azure Active Directory B2C, Gestione API di Azure e Easy Auth da chiamare da una SPA JavaScript.Protect an API with OAuth 2.0 by using Azure Active Directory B2C, Azure API Management and Easy Auth to be called from a JavaScript SPA.
services: api-management, azure-ad-b2c, app-service
documentationcenter: ''
author: WillEastbury
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: wieastbu
ms.custom: fasttrack-new
ms.openlocfilehash: 55acea360de11c5fcc699d65daf92cf24dfd691d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475477"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Proteggi il back-end SPA con OAuth 2.0, Azure Active Directory B2C e Gestione API di Azure

Questo scenario illustra come configurare l'istanza di Gestione API di Azure per proteggere un'API.
Useremo il protocollo OAuth 2.0 con Azure AD B2C, insieme a Gestione API per proteggere un back-end di Funzioni di Azure tramite EasyAuth.We'll use the OAuth 2.0 protocol with Azure AD B2C, alongside API Management to secure an Azure Functions backend using EasyAuth.

## <a name="aims"></a>Mira
Vedremo come è possibile usare Gestione API in uno scenario semplificato con Funzioni di Azure e Azure AD B2C. Verrà creata un'app JavaScript (JS) che chiama un'API che accede agli utenti con Azure AD B2C. Quindi si utilizzeranno le funzionalità dei criteri di convalida-jwt di Gestione API per proteggere l'API back-end.

Per una difesa approfondita, utilizziamo quindi EasyAuth per convalidare nuovamente il token all'interno dell'API back-end.

## <a name="prerequisites"></a>Prerequisiti
Per eseguire i passaggi in questo articolo è necessario avere quanto segue:
* Un account di archiviazione Azure (StorageV2) general Purpose V2 per ospitare l'app per una singola pagina JS front-end
* Un'istanza di Gestione API di AzureAn Azure API Management instance 
* Un'app funzione di Azure vuota (che esegue il runtime V2 .NET Core, in un piano di consumo di Windows) per ospitare l'API chiamata
* Un tenant B2C di Azure AD, collegato a una sottoscrizioneAn Azure AD B2C tenant, linked to a subscription 

Anche se in pratica si userebbero le risorse nella stessa area nei carichi di lavoro di produzione, per questo articolo how-to l'area di distribuzione non è importante.

## <a name="overview"></a>Panoramica
Ecco un'illustrazione dei componenti in uso e del flusso tra di essi una volta completato questo processo.
![Componenti in uso e flusso](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Componenti in uso e flusso")

Ecco una rapida panoramica dei passaggi:

1. Creare le applicazioni B2C di Azure AD (front-end, gestione API) e API con ambiti e concedere l'accesso ALLE APICreate the Azure AD B2C Calling (Frontend, API Management) and API Applications with scopes and grant API Access
1. Creare i criteri di iscrizione o accesso per consentire agli utenti di accedere con Azure AD B2CCreate the sign up or sign in policies to allow users to sign in with Azure AD B2C 
1. Configurare Gestione API con i nuovi ID client B2C di Azure AD e le nuove chiavi per abilitare l'autorizzazione utente OAuth2 nella console per sviluppatoriConfigure API Management with the new Azure AD B2C Client IDs and keys to Enable OAuth2 user authorization in the Developer Console
1. Compilare l'API delle funzioniBuild the Function API
1. Configurare l'API Function per abilitare EasyAuth con le nuove chiavi e gli ID client B2C di Azure AD e bloccare l'indirizzo VIP di APIMConfigure the Function API to enable EasyAuth with the new Azure AD 2C Client ID's and Keys and lock down to APIM VIP 
1. Compilare la definizione dell'API in Gestione APIBuild the API Definition in API Management
1. Configurare Oauth2 per la configurazione dell'API Management API
1. Impostare il criterio **CORS** e aggiungere il criterio **validate-jwt** per convalidare il token OAuth per ogni richiesta in ingresso
1. Compilare l'applicazione chiamante per utilizzare l'APIBuild the calling application to consume the API
1. Carica il campione JS SPA
1. Configurare l'app client JS di esempio con le nuove chiavi e gli ID client B2C di Azure AD 
1. Testare l'applicazione client

## <a name="configure-azure-ad-b2c"></a>Configurare Azure AD B2C 
Aprire il pannello B2C di Azure AD nel portale ed eseguire i passaggi seguenti.
1. Selezionare la scheda **Applicazioni** 
1. Fare clic sul pulsante 'Aggiungi' e creare tre applicazioni per i seguenti scopi
   * Client front-end.
   * API delle funzioni back-end.
   * [Facoltativo] Il portale per sviluppatori di Gestione API (a meno che non si stia eseguendo Gestione API di Azure nel livello di consumo, ulteriori informazioni in questo scenario più avanti).
1. Impostare WebApp / API Web per tutte e 3 le applicazioni e impostare 'Consenti flusso implicito' su yes solo per il client front-end.
1. Impostare ora l'URI dell'ID app e scegliere un elemento univoco e pertinente per il servizio da creare.
1. Usa i segnaposto per gli URL https://localhostdi risposta per ora, ad esempio , aggiorneremo questi URL in un secondo momento.
1. Fai clic su "Crea", quindi ripeti i passaggi da 2 a 5 per ognuna delle tre app precedenti, registrando l'URI AppID, il nome e l'ID applicazione per un utilizzo successivo per tutte e tre le app.
1. Aprire l'applicazione del portale per sviluppatori di Gestione API dall'elenco delle applicazioni e selezionare la scheda *Chiavi* (in Generale), quindi fare clic su 'Genera chiave' per generare una chiave di autenticazione
1. Facendo clic su Salva, registrare la chiave in un luogo sicuro per un uso successivo - si noti che questo posto è l'unica possibilità si arriva a visualizzare e copiare questa chiave.
1. Selezionare ora la scheda *Ambiti pubblicati* (in Accesso API)
1. Creare e assegnare un nome a un ambito per l'API Function e registrare l'ambito e popolato valore dell'ambito completo, quindi fare clic su 'Salva'.
   > [!NOTE]
   > Gli ambiti B2C di Azure AD sono autorizzazioni effettivamente all'interno dell'API a cui altre applicazioni possono richiedere l'accesso tramite il pannello di accesso all'API dalle applicazioni, in effetti sono state appena create le autorizzazioni dell'applicazione per l'API chiamata.
1. Aprire le altre due applicazioni e quindi cercare nella scheda *Accesso API.*
1. Concedere loro l'accesso all'ambito dell'API back-end e quello predefinito già presente ("Accedere al profilo dell'utente").
1. Generare una chiave ciascuno selezionando la scheda *Chiavi* in 'Generale' per generare una chiave di autenticazione e registrare quelle chiavi in un luogo sicuro per più tardi.

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>Creare un flusso utente "Iscriviti o Accedi"
1. Tornare alla radice (o 'Panoramica') del pannello B2C di Azure AD 
1. Quindi selezionare "Flussi utente (Criteri)" e fare clic su "Nuovo flusso utente"
1. Scegliere il tipo di flusso utente "Iscriviti e accedi"
1. Assegnare un nome al criterio e registrarlo per un secondo momento.
1. Quindi in 'Provider di identità', quindi selezionare 'Iscrizione ID utente' (questo potrebbe dire 'Registrazione e-mail') e fare clic su OK. 
1. In "Attributi utente e attestazioni", fai clic su "Mostra altro... quindi scegliere le opzioni di attestazione che si desidera che gli utenti di immettere e vengono restituiti nel token. Seleziona almeno 'Nome visualizzato' e 'Indirizzo e-mail' per raccogliere e restituire, quindi fai clic su 'OK', quindi fai clic su 'Crea'.
1. Selezionare il criterio creato nell'elenco, quindi fare clic sul pulsante 'Esegui flusso utente'.
1. Questa azione aprirà il pannello Esegui flusso utente, selezionerà l'applicazione front-end, quindi registrerà l'indirizzo del dominio di b2clogin.com visualizzato nell'elenco a discesa "Seleziona dominio".
1. Fare clic sul collegamento nella parte superiore per aprire il "noto endpoint di configurazione openid" e registrare i valori di authorization_endpoint e token_endpoint nonché del valore del collegamento stesso come il noto endpoint di configurazione openid.

   > [!NOTE]
   > I criteri B2C consentono di esporre gli endpoint di accesso B2C di Azure AD per poter acquisire componenti di dati diversi e accedere agli utenti in modi diversi. In questo caso è stato configurato un endpoint di iscrizione o accesso, che ha esposto un endpoint di configurazione noto, in particolare i criteri creati sono stati identificati nell'URL dal parametro p.
   > 
   > Una volta fatto questo - ora si dispone di una piattaforma di identità Business to Consumer funzionale che segnalerà gli utenti a più applicazioni. 
   > Se si desidera è possibile fare clic sul pulsante 'Esegui flusso utente' qui (per passare attraverso il processo di iscrizione o di accesso) e avere un'idea di ciò che farà in pratica, ma il passaggio di reindirizzamento alla fine avrà esito negativo come l'applicazione non è ancora stata distribuita.

## <a name="build-the-function-api"></a>Compilare l'API della funzioneBuild the function API
1. Tornare al tenant standard di Azure AD nel portale di Azure in modo da poter configurare nuovamente gli elementi nella sottoscrizione 
1. Passare al pannello App per le funzioni del portale di Azure, aprire l'app per le funzioni vuota, quindi creare una nuova funzione In-Portal 'Webhook - API' tramite la guida introduttiva.
1. Incollare il codice di esempio riportato di seguito in Run.csx sul codice esistente visualizzato.

   ```csharp
   
   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   
   public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
   {
      log.LogInformation("C# HTTP trigger function processed a request.");
      
      return (ActionResult)new OkObjectResult($"Hello World, time and date are {DateTime.Now.ToString()}");
   }
   
   ```

   > [!NOTE]
   > Il codice della funzione di script c'è appena incollato semplicemente registra una riga nei log delle funzioni e restituisce il testo "Hello World" con alcuni dati dinamici (la data e l'ora).

3. Seleziona "Integra" dal pannello sinistro, quindi seleziona "Editor avanzato" nell'angolo in alto a destra del riquadro.
4. Incollare il codice di esempio seguente sul json esistente.

   ```json
   {
      "bindings": [
       {
        "authLevel": "function",
        "name": "req",
        "type": "httpTrigger",
        "direction": "in",
        "methods": [
           "get"
        ],
        "route": "hello"
       },
       {
         "name": "$return",
         "type": "http",
         "direction": "out"
       }
     ]
   }
   ```

5. Tornare alla scheda HttpTrigger1, fare clic su 'Ottieni URL funzione', quindi copiare l'URL visualizzato.

   > [!NOTE]
   > I binding appena creati indicano semplicemente a Funzioni di rispondere alle richieste http GET anonime all'URL appena copiato. (Orahttps://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey) abbiamo un'API https senza server scalabile, che è in grado di restituire un payload molto semplice.
   > È ora possibile testare la chiamata di questa API da un Web browser usando l'URL precedente, è anche possibile rimuovere la parte dell'URL ?code-secret e dimostrare che Funzioni di Azure restituirà un errore 401.

## <a name="configure-and-secure-the-function-api"></a>Configurare e proteggere l'API della funzione
1. È necessario configurare due aree aggiuntive nell'app per le funzioni (Autenticazione e Restrizioni di rete).
1. In primo luogo Configuriamo l'autenticazione / autorizzazione, quindi fare &lt;clic&gt; sul nome dell'app per le funzioni (accanto all'icona delle funzioni di z) per visualizzare la pagina di panoramica.
1. Quindi Selezionare la scheda 'Funzionalità piattaforma' e selezionare 'Autenticazione / Autorizzazione'.
1. Attivare la funzionalità di autenticazione del servizio app.
1. In 'Provider di autenticazione' scegliere 'Azure Active Directory' e scegliere 'Avanzate' dall'opzione Modalità di gestione.
1. Incollare l'ID applicazione dell'API della funzione back-end (da Azure AD B2C nella casella 'ID client')Paste the Backend Function API's application ID (from Azure AD B2C into the 'Client ID' box)
1. Incollare l'endpoint di configurazione id aperto noto dai criteri di iscrizione o accesso nella casella URL autorità emittente (la configurazione è stata registrata in precedenza).
1. Selezionare OK.
1. Impostare l'elenco a discesa Azione da eseguire quando la richiesta non è autenticata su "Accedi con Azure Active Directory", quindi fare clic su Salva.Set the Action to take when request is not authenticated dropdown to "Log in with Azure Active Directory", then click Save.

   > [!NOTE]
   > Ora l'API Function viene distribuita e deve generare risposte 401 se non viene fornita la chiave corretta e deve restituire dati quando viene presentata una richiesta valida.
   > È stata aggiunta ulteriore sicurezza di difesa in profondità in EasyAuth configurando l'opzione 'Accesso con Azure AD' per gestire le richieste non autenticate. Tenere presente che questo cambierà il comportamento delle richieste non autorizzate tra l'app per le funzioni back-end e la SPA front-end poiché EasyAuth emetterà un reindirizzamento 302 ad AAD anziché una risposta 401 Not Authorized, la funzionalità verrà corretta utilizzando Gestione API in un secondo momento.
   > Non abbiamo ancora alcuna sicurezza IP applicata, se si dispone di una chiave valida e token OAuth2, chiunque può chiamare questo da qualsiasi luogo - idealmente vogliamo forzare tutte le richieste a venire tramite Gestione API.
   > Se si utilizza il livello di consumo di Gestione API, non sarà possibile eseguire questo blocco tramite VIP poiché non esiste un IP statico dedicato per tale livello, sarà necessario basarsi sul metodo di blocco delle chiamate API tramite la chiave di funzione del segreto condiviso , quindi i passaggi 11-13 non saranno possibili.

1. Chiudere il pannello 'Autenticazione/ Autorizzazione'Close the 'Authentication / Authorization' blade 
1. Selezionare 'Rete' e quindi "Restrizioni di accesso"
1. Bloccare quindi gli indirizzi IP dell'app per le funzioni consentiti nell'indirizzo VIP dell'istanza di Gestione API. Questo indirizzo VIP viene visualizzato nella sezione Gestione API - Panoramica del portale.
1. Se si desidera continuare a interagire con il portale delle funzioni e eseguire i passaggi facoltativi riportati di seguito, è necessario aggiungere qui il proprio indirizzo IP pubblico o l'intervallo CIDR.
1. Una volta che è presente una voce allow nell'elenco, Azure aggiunge una regola di negazione implicita per bloccare tutti gli altri indirizzi. 

È necessario aggiungere blocchi di indirizzi formattati CIDR al pannello Restrizioni IP. When you need to add a single address such as the API Management VIP, you need to add it in the format xx.xx.xx.xx.

   > [!NOTE]
   > Ora l'API Function non deve essere richiamabile da un luogo diverso da tramite la gestione API o il tuo indirizzo.
   
## <a name="import-the-function-app-definition"></a>Importare la definizione dell'app per le funzioniImport the function app definition
1. Aprire il *pannello Gestione API*, quindi aprire *l'istanza*.
1. Seleziona il pannello API dalla sezione Gestione API dell'istanza.
1. Dal riquadro 'Aggiungi una nuova API', scegli "App per le funzioni", quindi seleziona "Completo" nella parte superiore del popup.
1. Fare clic su Sfoglia, scegliere l'app per le funzioni in cui si ospita l'API e fare clic su Seleziona.
1. Assegnare all'API un nome e una descrizione per l'uso interno di Gestione API e aggiungerla al prodotto "illimitato".
1. Assicurarsi di registrare l'URL di base per un utilizzo successivo e quindi fare clic su Crea.

## <a name="configure-oauth2-for-api-management"></a>Configurare Oauth2 per Gestione API

1. Selezionare quindi il pannello Oauth 2.0 dalla scheda Sicurezza e fare clic su 'Aggiungi'
1. Assegnare i valori per *Nome visualizzato* e *Descrizione* per l'endpoint Oauth aggiunto (questi valori verranno visualizzati nel passaggio successivo come endpoint Oauth2).
1. È possibile immettere qualsiasi valore nell'URL della pagina di registrazione client, poiché questo valore non verrà utilizzato.
1. Controllare il tipo di concessione *di autenticazione implicita* e lasciare selezionato il tipo di concessione del codice di autorizzazione.
1. Passare ai campi *dell'endpoint Autorizzazione* e *Token* e immettere i valori acquisiti dal documento xml di configurazione noto in precedenza.
1. Scorrere verso il basso e popolare un *parametro del corpo aggiuntivo* denominato 'resource' con l'ID client dell'API Function back-end dalla registrazione dell'app B2C di Azure AD
1. Selezionare 'Credenziali client', impostare l'ID client sull'ID app dell'app console per sviluppatori: ignorare questo passaggio se si usa il modello di gestione API di consumo.
1. Impostare il segreto client sulla chiave registrata in precedenza - ignorare questo passaggio se si utilizza il modello di gestione API di consumo.
1. Infine, ora registrare il redirect_uri della concessione del codice di autenticazione da Gestione API per un uso successivo.

## <a name="set-up-oauth2-for-your-api"></a>Configurare Oauth2 per l'API
1. L'API verrà visualizzata sul lato sinistro del portale nella sezione "Tutte le API", aprire l'API facendo clic su di essa.
1. Selezionare la scheda 'Impostazioni'.
1. Aggiornare le impostazioni selezionando "Oauth 2.0" dal pulsante di opzione di autorizzazione utente.
1. Selezionare il server Oauth definito in precedenza.
1. Selezionare la casella di controllo 'Ignora ambito' e immettere l'ambito registrato per la chiamata ALL'API back-end in precedenza.

   > [!NOTE]
   > A questo punto è stata esibula un'istanza di Gestione API che sa come ottenere i token di accesso da Azure AD B2C per autorizzare le richieste e comprendere la configurazione B2C di Oauth2 Azure Active Directory.Now we have an API Management instance that knows how to get access tokens from Azure AD B2C to authorize requests and understands our Oauth2 Azure Active Directory B2C configuration.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>Impostare i criteri **CORS** e **validate-jwt**

> Le sezioni seguenti devono essere seguite indipendentemente dal livello APIM in uso. 

1. Tornare alla scheda Progettazione e scegliere "Tutte le API", quindi fare clic sul pulsante della visualizzazione del codice per visualizzare l'editor dei criteri.
1. Modificare la sezione in ingresso e incollare il codice XML seguente in modo che venga letto come il seguente.

   ```xml
   <inbound>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
         <openid-config url="https://tenant.b2clogin.com/tenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_MyDefaultPolicy" />
         <required-claims>
            <claim name="aud">
               <value>your-backend-api-application-client-id</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <cors>
         <allowed-origins>
            <origin>*</origin>
         </allowed-origins>
         <allowed-methods>
           <method>GET</method>
         </allowed-methods>
         <allowed-headers>
            <header>*</header>
         </allowed-headers>
         <expose-headers>
           <header>*</header>
         </expose-headers>
       </cors>
   </inbound>
   ```
1. Modificare l'URL openid-config in modo che corrisponda all'endpoint B2C di Azure AD noto per i criteri di iscrizione o accesso.
1. Modificare il valore dell'attestazione in modo che corrisponda all'ID applicazione valido, noto anche come ID client per l'applicazione API back-end e salva.

   > [!NOTE]
   > Ora la gestione delle API è in grado di rispondere alle richieste tra origini alle app JS SPA ed eseguirà la limitazione delle richieste, la limitazione della frequenza e la pre-convalida del token di autenticazione JWT passato PRIMA di inoltrare la richiesta all'API di funzione.

   > [!NOTE]
   > La sezione seguente è facoltativa e non si applica al livello **Consumo,** che non supporta il portale per sviluppatori.
   > Se non si intende utilizzare il portale per sviluppatori o non è possibile utilizzarlo poiché si utilizza il livello Consumo, ignorare questo passaggio e passare direttamente a ["Build the JavaScript SPA per utilizzare l'API".](#build-the-javascript-spa-to-consume-the-api)

## <a name="optional-configure-the-developer-portal"></a>[Facoltativo] Configurare il portale per sviluppatori

1. Aprire il pannello B2C di Azure AD e passare alla registrazione dell'applicazione per il portale per sviluppatoriOpen the Azure AD B2C blade and navigate to the application registration for the Developer Portal
1. Impostare la voce 'Rispondi URL' a quello annotato quando è stato configurato il redirect_uri della concessione del codice di autenticazione in Gestione API in precedenza.

   Ora che l'autorizzazione utente OAuth 2.0 è abilitata su `Echo API`, la Developer Console ottiene un token di accesso per l'utente, prima di chiamare l'API.

1. Passare a qualsiasi `Echo API` operazione nel portale per sviluppatori e selezionare **Provaper** accedere alla Console per sviluppatori.
1. Si noti un nuovo elemento nella sezione **Autorizzazione,** corrispondente al server di autorizzazione appena aggiunto.
1. Selezionare **Codice di autorizzazione** dall'elenco a discesa delle autorizzazioni e viene richiesto di accedere al tenant di Azure AD. Se hai già effettuato l'accesso con l'account, è possibile che non venga richiesto.
1. Dopo l'accesso riuscito, viene aggiunta un'intestazione alla richiesta, con un token di accesso da Azure AD B2C codificato in Base64.After successful sign-in, an `Authorization: Bearer` header is added to the request, with an access token from Azure AD B2C encoded in Base64. 
1. Selezionare **Invia** ed è possibile chiamare correttamente l'API.

   > [!NOTE]
   > Ora la gestione delle API è in grado di acquisire token per il portale per sviluppatori per testare l'API ed è in grado di comprendere la sua definizione ed eseguire il rendering della pagina di test appropriata nel portale di sviluppo.

1. Dal pannello Panoramica del portale di Gestione API, fare clic su 'Portale sviluppatori' per accedere come amministratore dell'API.
1. In questo caso, l'utente e altri consumer selezionati dell'API possono testarli e chiamarli da una console.
1. Seleziona "Prodotti", quindi scegli "Illimitato", quindi scegli l'API che abbiamo creato in precedenza e fai clic su "PROVA IT"
1. Scopri la chiave di sottoscrizione dell'API e copiala in un luogo sicuro insieme all'URL della richiesta che ti servirà in un secondo momento.
1. Selezionare anche Implicito, dall'elenco a discesa autenticazione oauth e potrebbe essere necessario eseguire l'autenticazione qui con un popup.
1. Fare clic su 'Invia' e se tutto va bene, l'app per le funzioni deve rispondere con un messaggio hello tramite la gestione API con un messaggio 200 OK e un messaggio JSON.

   > [!NOTE]
   > Congratulazioni, ora hai Azure AD B2C, Gestione API e Funzioni di Azure che lavorano insieme per pubblicare, proteggere e usare un'API. Avrete notato che l'API è in realtà protetta due volte utilizzando questo metodo, una volta con la gestione API Ocp-Subscription-Key Header e una volta con l'autorizzazione: Bearer JWT.
   > Si sarebbe corretto, come questo esempio è un'applicazione JavaScript a pagina singola, usiamo la chiave di gestione API solo per le chiamate di frequenza limitante e fatturazione.
   > L'autorizzazione e l'autenticazione effettive vengono gestite da Azure AD B2C e vengono incapsulate nel token JWT, che viene convalidato due volte, una volta da Gestione API e quindi da Funzioni di Azure.The actual Authorization and Authentication is handled by Azure AD B2C, and is encapsulated in the JWT, which gets validated twice, one by API Management, and then by Azure Functions.

## <a name="build-the-javascript-spa-to-consume-the-api"></a>Compilare la SPA JavaScript per usare l'APIBuild the JavaScript SPA to consume the API
1. Aprire il pannello degli account di archiviazione nel portale di AzureOpen the storage accounts blade in the Azure portal 
1. Seleziona l'account che hai creato e seleziona il pannello "Sito web statico" nella sezione Impostazioni (se non vedi l'opzione "Sito web statico", seleziona di aver creato un account V2).
1. Impostare la funzionalità di hosting Web statico su 'enabled' e il nome del documento di indice su 'index.html', quindi fare clic su 'save'.
1. Annotare il contenuto dell'endpoint primario, poiché questa posizione è la posizione in cui verrà ospitato il sito front-end. 

   > [!NOTE]
   > È possibile usare Archiviazione BLOB di Azure , riscrittura CDN o Servizio app di Azure, ma la funzionalità di hosting di siti Web statici dell'archiviazione BLOB offre un contenitore predefinito per la gestione di contenuto Web statico / html / js / css da Archiviazione di Azure e dedurrà una pagina predefinita per noi per il lavoro zero.

## <a name="upload-the-js-spa-sample"></a>Caricare l'esempio JS SPA
1. Sempre nel pannello dell'account di archiviazione selezionare il pannello 'Blob' nella sezione Servizio BLOB e fare clic sul contenitore $web visualizzato nel riquadro di destra.
1. Salvare il codice riportato di seguito in un file localmente nel computer come index.html, quindi caricare il file index.html nel contenitore $web.

   ```html
   <!doctype html>
   <html lang="en">
   <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>Sample JS SPA</title>
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
   </head>
   <body>
        <div class="container-fluid">
            <div class="row">
                <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-light bg-light navbar-dark bg-dark">
                        <a class="navbar-brand" href="#">Sample Code</a>
                        <ul class="navbar-nav ml-md-auto">
                            <li class="nav-item dropdown">
                                <a class="btn btn-large btn-success" onClick="login()">Sign In</a>
                            </li>
                        </ul>
                    </nav>
                </div>
            </div>
            <div class="row">
                <div class="col-md-12">
                    <div class="jumbotron">
                        <h2>
                    <div id="message">Hello, world!</div>
                    </h2>
                        <p>
                            <a class="btn btn-primary btn-large" onClick="GetAPIData()">Call API</a>
                        </p>
                    </div>
                </div>
            </div>
        </div>
        <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
        <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
        <script lang="javascript">
            var applicationConfig = {
                clientID: "clientidgoeshere",
                authority: "https://tenant.b2clogin.com/tfp/tenant/policy",
                b2cScopes: ["https://tenant/app/scope"],
                webApi: 'http://functionurl',
                subKey: 'apimkeygoeshere'
            };
            var msalConfig = {
                auth: {
                        clientId: applicationConfig.clientID, 
                        authority:  applicationConfig.authority,
                        validateAuthority: false
                },
                cache: {
                        cacheLocation: "localStorage",
                        storeAuthStateInCookie: true
                }
            };
            var clientApplication = new Msal.UserAgentApplication(msalConfig);
            function login() {
                var loginRequest = {
                    scopes: applicationConfig.b2cScopes
                };
                clientApplication.loginPopup(loginRequest).then(function (loginResponse) {
                    var tokenRequest = {
                        scopes: applicationConfig.b2cScopes
                    };
                    clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                        document.getElementById("signinbtn").innerHTML = "Logged in as: " + clientApplication.account.name;
                        document.getElementById("callapibtn").hidden = false
                        }).catch(function (error) {
                            clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                                }).catch (function (error) {
                                    console.log("Error acquiring the popup:\n" + error);
                                });
                        })
                    }).catch (function (error) {
                        console.log("Error during login:\n" + error);
                });
            }
            function GetAPIData() {
                var tokenRequest = {
                    scopes: applicationConfig.b2cScopes
                }
                clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                    callApiWithAccessToken(tokenResponse.accessToken);
                    }).catch(function (error) {
                        clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                            callApiWithAccessToken(tokenResponse.accessToken);
                            
                        }).catch(function (error) {
                            console.log("Error acquiring the access token to call the Web api:\n" + error);
                        });
                    })
            }
            function callApiWithAccessToken(token)
            {
                console.log("calling "  + applicationConfig.webApi +  " with " + token);
                    // Make the api call here
                $.ajax({
                    type: "get",
                    headers: {'Authorization': 'Bearer ' + token, 'Ocp-Apim-Subscription-Key': applicationConfig.subKey},                   url: applicationConfig.webApi
                }
                ).done(function (body) {
                    document.getElementById("message").innerHTML = "The API Said " + body;
                });
            }
        </script>
    </body>
   </html>
   
   ```

1. Passare all'endpoint primario del sito Web statico archiviato in precedenza nell'ultima sezione.

   > [!NOTE]
   > Congratulazioni, hai appena distribuito un'app JavaScript a pagina singola in Archiviazione di Azure Poiché l'app JS non è stata configurata con le chiavi per l'API o l'app JS è stata configurata con i dettagli di Azure AD B2C ancora: la pagina non funzionerà ancora se viene aperta.

## <a name="configure-the-js-spa-for-azure-ad-b2c"></a>Configurare JS SPA per Azure AD B2C
1. Ora sappiamo dove tutto è: possiamo configurare la SPA con l'indirizzo API di gestione API appropriato e il corretto Azure AD B2C application / id client
1. Tornare al pannello di archiviazione del portale di Azure e fare clic su index.html, quindi scegliere 'Modifica BLOB'Go back to the Azure portal storage blade and click on index.html, then choose 'Edit Blob' 
1. Aggiornare i dettagli dell'autenticazione in modo che corrispondano all'applicazione front-end registrata in precedenza in B2C, notando che i valori 'b2cScopes' sono per il back-end dell'API.
1. La chiave webApi e l'URL API sono disponibili nel riquadro di test di Gestione API per l'operazione API.
1. Creare una chiave di sottoscrizione di Gestione API passando al pannello Gestione API nel pannello Gestione API, selezionando 'Sottoscrizioni' e facendo clic su 'Aggiungi sottoscrizione' e quindi salvando il record. Facendo clic sui puntini di sospensione (...) accanto alla riga creata sarà possibile visualizzare le chiavi in modo da poter copiare la chiave primaria.
1. Dovrebbe essere simile al codice seguente:-  

   ```javascript
   var applicationConfig =
      clientID: "{aadb2c-clientid-goeshere}",
      authority: "https://{tenant}.b2clogin.com/{tenant}/{policy}",
      b2cScopes: ["https://{tenant}/{app}/{scope}"], 
      webApi: 'http://{apim-url-for-your-function}',
      subKey: '{apim-subscription-key-goes-here}'
   };
   ```

1. Fare clic su Salva.

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Impostare gli URI di reindirizzamento per l'app front-end B2C di Azure ADSet the redirect URIs for the Azure AD B2C frontend app
1. Aprire il pannello B2C di Azure AD e passare alla registrazione dell'applicazione per l'applicazione front-end JavaScript
1. Impostare l'URL di reindirizzamento a quello annotato in precedenza quando è stato precedentemente configurato l'endpoint primario del sito Web statico precedente

   > [!NOTE] 
   > Questa configurazione comporterà la ricezione di un client dell'applicazione front-end con le attestazioni appropriate da Azure AD B2C.
   > La SPA sarà in grado di aggiungerlo come token di connessione nell'intestazione https nella chiamata all'API back-end.
   > Gestione API prevaliderà il token, le chiamate limite di frequenza all'endpoint da parte della chiave del sottoscrittore, prima di passare la richiesta all'API delle funzioni di Azure ricevente.
   > La SPA eseguirà il rendering della risposta nel browser.

   > *Congratulazioni, hai configurato Azure AD B2C, Gestione API di Azure, Funzioni di Azure, Autorizzazione del servizio app di Azure per lavorare in perfetta armonia!*

   > [!NOTE]
   > Ora abbiamo una semplice app con una semplice API protetta, testiamola.

## <a name="test-the-client-application"></a>Testare l'applicazione client
1. Aprire l'URL dell'app di esempio di cui è stato eseguito il annotare dall'account di archiviazione creato in precedenzaOpen the sample app URL that you noted down from the storage account you created earlier
1. Fare clic su "Accedi" nell'angolo in alto a destra, questo clic aprirà il profilo di iscrizione o di accesso di Azure AD B2C.
1. Post Sign nella sezione "Accedi come" dello schermo verrà popolato dal JWT.
1. Ora fare clic su "Chiama Api Web", e la pagina dovrebbe aggiornare con i valori inviati dall'API protetta.

## <a name="and-were-done"></a>E abbiamo finito
I passaggi precedenti possono essere adattati e modificati per consentire molti usi diversi di Azure AD B2C con Gestione API.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni su [Azure Active Directory e OAuth 2.0](../active-directory/develop/authentication-scenarios.md).
* Altre informazioni sui [video](https://azure.microsoft.com/documentation/videos/index/?services=api-management) relativi a Gestione API.
* Per altri metodi di protezione del servizio back-end, vedere [Autenticazione reciproca dei certificati](api-management-howto-mutual-certificates.md).
* [Creare un'istanza](get-started-create-service-instance.md)del servizio Gestione API.
* [Gestire la prima API](import-and-publish.md).
