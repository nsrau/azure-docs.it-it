---
title: Proteggere il back-end SPA con OAuth 2,0 usando Azure Active Directory B2C e gestione API di Azure.
description: Proteggi un'API con OAuth 2,0 usando Azure Active Directory B2C, gestione API di Azure e Easy auth da chiamare da una SPA JavaScript.
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
ms.openlocfilehash: 60177dd00dc6326aae4cfdc0b658c85f2635f8c0
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86253695"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Proteggere il back-end SPA con OAuth 2,0, Azure Active Directory B2C e gestione API di Azure

Questo scenario illustra come configurare l'istanza di gestione API di Azure per proteggere un'API.
Il protocollo OAuth 2,0 verrà usato con Azure AD B2C, insieme a gestione API per proteggere un back-end di funzioni di Azure con EasyAuth.

## <a name="aims"></a>Mira
Vedremo come usare gestione API in uno scenario semplificato con funzioni di Azure e Azure AD B2C. Si creerà un'app JavaScript (JS) che chiama un'API che consente di accedere agli utenti con Azure AD B2C. Quindi si useranno le funzionalità dei criteri Validate-JWT di gestione API per proteggere l'API back-end.

Per la difesa in profondità, viene usato EasyAuth per convalidare nuovamente il token all'interno dell'API back-end.

## <a name="prerequisites"></a>Prerequisiti
Per eseguire i passaggi in questo articolo è necessario avere quanto segue:
* Un account di archiviazione di Azure (archiviazione v2) per utilizzo generico V2 per ospitare l'app a pagina singola di frontend JS
* Un'istanza di gestione API di Azure 
* App per le funzioni di Azure vuota (che esegue il runtime di .NET Core V2, in un piano a consumo di Windows) per ospitare l'API chiamata
* Un tenant di Azure AD B2C, collegato a una sottoscrizione 

Sebbene in pratica si usino risorse nella stessa area dei carichi di lavoro di produzione, per questo articolo sulle procedure l'area di distribuzione non è importante.

## <a name="overview"></a>Panoramica
Di seguito è riportata un'illustrazione dei componenti in uso e del flusso tra di essi al termine del processo.
![Componenti in uso e flusso](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Componenti in uso e flusso")

Ecco una rapida panoramica dei passaggi:

1. Creare il Azure AD B2C chiamare (front-end, gestione API) e le applicazioni API con gli ambiti e concedere l'accesso all'API
1. Creare i criteri di iscrizione o di accesso per consentire agli utenti di accedere con Azure AD B2C 
1. Configurare Gestione API con le nuove chiavi e gli ID client di Azure AD B2C per abilitare l'autorizzazione utente OAuth2 nella console per sviluppatori
1. Compilare l'API della funzione
1. Configurare l'API della funzione per abilitare EasyAuth con le nuove chiavi e l'ID client di Azure AD B2C e bloccare il gestione API VIP 
1. Compilare la definizione dell'API in gestione API
1. Configurare OAuth2 per la configurazione API di gestione API
1. Configurare il criterio **CORS** e aggiungere il criterio **Validate-JWT** per convalidare il token OAuth per ogni richiesta in ingresso
1. Compilare l'applicazione chiamante per utilizzare l'API
1. Caricare l'esempio di JS SPA
1. Configurare l'app client JS di esempio con le nuove chiavi e l'ID client di Azure AD B2C 
1. Testare l'applicazione client

## <a name="configure-azure-ad-b2c"></a>Configurare Azure AD B2C 
Aprire il pannello Azure AD B2C nel portale e seguire questa procedura.
1. Selezionare la scheda **applicazioni** 
1. Fare clic sul pulsante "Aggiungi" e creare tre applicazioni per gli scopi seguenti.
   * Client front-end.
   * API della funzione back-end.
   * Opzionale Il portale per sviluppatori di gestione API (a meno che non si esegua gestione API di Azure nel livello consumo, più avanti in questo scenario).
1. Impostare WebApp/API Web per tutte e 3 le applicazioni e impostare ' Consenti flusso implicito ' su Sì solo per il client front-end.
1. Impostare ora l'URI ID app, scegliere un elemento univoco e pertinente per il servizio in fase di creazione.
1. Usare i segnaposto per gli URL di risposta per ora https://localhost , ad esempio, questi URL verranno aggiornati in un secondo momento.
1. Fare clic su' Crea ', quindi ripetere i passaggi 2-5 per ognuna delle tre app precedenti, registrando l'URI AppID, il nome e l'ID applicazione per un uso successivo per tutte e tre le app.
1. Aprire l'applicazione portale per sviluppatori di gestione API dall'elenco di applicazioni e selezionare la scheda *chiavi* (in generale), quindi fare clic su "Genera chiave" per generare una chiave di autenticazione.
1. Quando si fa clic su Salva, registrare la chiave in un luogo sicuro per un uso successivo. si noti che questa è l'unica possibilità per visualizzare e copiare la chiave.
1. Selezionare ora la scheda *ambiti pubblicati* (in accesso all'API)
1. Creare e denominare un ambito per l'API della funzione e registrare l'ambito e il valore di ambito completo popolato, quindi fare clic su "Salva".
   > [!NOTE]
   > Gli ambiti di Azure AD B2C sono le autorizzazioni effettivamente presenti all'interno dell'API a cui altre applicazioni possono richiedere l'accesso tramite il pannello di accesso all'API dalle applicazioni, in modo da creare solo le autorizzazioni dell'applicazione per l'API chiamata.
1. Aprire le altre due applicazioni, quindi cercare nella scheda *accesso all'API* .
1. Concedere loro l'accesso all'ambito dell'API back-end e quello predefinito già presente ("accesso al profilo utente").
1. Generare una chiave ognuna selezionando la scheda *chiavi* in "generale" per generare una chiave di autenticazione e registrare le chiavi in un punto sicuro per un momento successivo.

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>Creare un flusso utente "iscrizione o accesso"
1. Tornare alla radice (o ' panoramica ') del pannello Azure AD B2C 
1. Selezionare quindi "Flussi utente (criteri)" e fare clic su "nuovo flusso utente".
1. Scegliere il tipo di flusso utente "iscrizione e accesso"
1. Assegnare un nome al criterio e registrarlo per un momento successivo.
1. Quindi, in ' Identity providers ' (provider di identità), selezionare ' user ID Sign up ' (questo può indicare "email Sign up") e fare clic su OK. 
1. In ' attributi utente e attestazioni ' fare clic su' Mostra più.. .' scegliere quindi le opzioni di attestazione che si desidera che gli utenti immettano e restituiscono nel token. Controllare almeno ' nome visualizzato ' è indirizzo di posta elettronica ' per raccogliere e restituire, quindi fare clic su' OK ' e quindi su' Crea '.
1. Selezionare il criterio creato nell'elenco, quindi fare clic sul pulsante "Esegui flusso utente".
1. Questa azione consente di aprire il pannello Esegui flusso utente, selezionare l'applicazione front-end, quindi registrare l'indirizzo del dominio b2clogin.com visualizzato nell'elenco a discesa per "Seleziona dominio".
1. Fare clic sul collegamento nella parte superiore per aprire l'endpoint di configurazione OpenID noto e registrare i valori authorization_endpoint e token_endpoint, nonché il valore del collegamento stesso come endpoint di configurazione OpenID noto.

   > [!NOTE]
   > I criteri B2C consentono di esporre gli endpoint di accesso Azure AD B2C per poter acquisire diversi componenti dati e gli utenti di accesso in modi diversi. In questo caso è stato configurato un endpoint di iscrizione o accesso, che ha esposto un endpoint di configurazione noto, in particolare i criteri creati sono stati identificati nell'URL dal parametro p =.
   > 
   > Al termine di questa operazione, ora si dispone di una piattaforma funzionale per le identità dei consumatori che consentirà agli utenti di accedere a più applicazioni. 
   > Se si desidera, è possibile fare clic sul pulsante "Esegui flusso utente" qui (per esaminare il processo di iscrizione o accesso) e ottenere un'idea di cosa verrà fatto in pratica, ma il passaggio di reindirizzamento alla fine non riuscirà perché l'app non è stata ancora distribuita.

## <a name="build-the-function-api"></a>Compilare l'API della funzione
1. Tornare al tenant di Azure AD standard nel portale di Azure per poter configurare di nuovo gli elementi nella sottoscrizione 
1. Passare al pannello app per le funzioni del portale di Azure, aprire l'app per le funzioni vuota, quindi creare una nuova funzione ' webhook + API ' nel portale tramite la Guida introduttiva.
1. Incollare il codice di esempio riportato di seguito in Run. CSX sul codice esistente visualizzato.

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
   > Il codice della funzione script c# appena incollato viene semplicemente registrato una riga nei log delle funzioni e viene restituito il testo "Hello World" con alcuni dati dinamici (data e ora).

3. Selezionare "integra" dal pannello a sinistra, quindi selezionare "Editor avanzato" nell'angolo in alto a destra del riquadro.
4. Incollare il codice di esempio seguente sul file JSON esistente.

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

5. Tornare alla scheda HttpTrigger1, fare clic su' Ottieni URL funzione ', quindi copiare l'URL visualizzato.

   > [!NOTE]
   > Le associazioni appena create direbbero semplicemente alle funzioni di rispondere alle richieste HTTP GET anonime all'URL appena copiato. ( `https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey` ) Ora abbiamo un'API HTTPS scalabile senza server, in grado di restituire un payload molto semplice.
   > È ora possibile testare la chiamata di questa API da un Web browser usando l'URL sopra riportato, è anche possibile rimuovere la parte? code = Secret dell'URL e dimostrare che funzioni di Azure restituirà un errore 401.

## <a name="configure-and-secure-the-function-api"></a>Configurare e proteggere l'API della funzione
1. È necessario configurare due aree aggiuntive nell'app per le funzioni (restrizioni di autenticazione e di rete).
1. Per prima cosa, è necessario configurare l'autenticazione/autorizzazione, quindi fare clic sul nome dell'app per le funzioni (accanto all' &lt; &gt; icona delle funzioni Z) per visualizzare la pagina panoramica.
1. Selezionare quindi la scheda "funzionalità della piattaforma" e selezionare "autenticazione/autorizzazione".
1. Attivare la funzionalità di autenticazione del servizio app.
1. In "provider di autenticazione" scegliere "Azure Active Directory" e scegliere "avanzate" dall'opzione modalità di gestione.
1. Incollare l'ID applicazione dell'API della funzione back-end (da Azure AD B2C nella casella ' ID client ')
1. Incollare l'endpoint di configurazione Open-ID noto dal criterio di iscrizione o di accesso nella casella URL autorità di certificazione. questa configurazione è stata registrata in precedenza.
1. Selezionare OK.
1. Impostare l'azione da eseguire quando la richiesta non è autenticata a discesa "Accedi con Azure Active Directory", quindi fare clic su Salva.

   > [!NOTE]
   > A questo punto, l'API della funzione viene distribuita e deve generare risposte 401 se non viene fornita la chiave corretta e deve restituire i dati quando viene presentata una richiesta valida.
   > È stata aggiunta una sicurezza di difesa in profondità aggiuntiva in EasyAuth configurando l'opzione ' login with Azure AD ' per gestire le richieste non autenticate. Tenere presente che questa operazione modificherà il comportamento della richiesta non autorizzata tra la app per le funzioni back-end e la SPA frontend, perché EasyAuth emetterà un reindirizzamento 302 ad AAD anziché una risposta 401 non autorizzata. questa operazione verrà corretta con gestione API in un secondo momento.
   > Non è ancora stata applicata alcuna sicurezza IP, se si dispone di una chiave valida e di un token OAuth2, chiunque può chiamarlo da qualsiasi posizione, idealmente si vuole forzare l'esecuzione di tutte le richieste tramite gestione API.
   > Se si usa il livello di utilizzo di gestione API, non sarà possibile eseguire questo blocco tramite VIP perché non è disponibile un indirizzo IP statico dedicato per tale livello, sarà necessario basarsi sul metodo di blocco delle chiamate API tramite la chiave della funzione segreta condivisa, quindi i passaggi 11-13 non saranno possibili.

1. Chiudere il pannello "autenticazione/autorizzazione" 
1. Selezionare "rete", quindi selezionare "restrizioni di accesso".
1. Bloccare quindi gli indirizzi IP delle app per le funzioni consentiti all'indirizzo VIP dell'istanza di gestione API. Questo indirizzo VIP viene visualizzato nella sezione gestione API-Panoramica del portale.
1. Se si vuole continuare a interagire con il portale delle funzioni e per eseguire i passaggi facoltativi riportati di seguito, è necessario aggiungere anche il proprio indirizzo IP pubblico o l'intervallo CIDR.
1. Quando nell'elenco è presente una voce Consenti, Azure aggiunge una regola di negazione implicita per bloccare tutti gli altri indirizzi. 

È necessario aggiungere i blocchi in formato CIDR degli indirizzi al pannello restrizioni IP. Quando è necessario aggiungere un singolo indirizzo, ad esempio l'indirizzo VIP di gestione API, è necessario aggiungerlo nel formato XX. XX. XX. XX.

   > [!NOTE]
   > A questo punto, l'API della funzione non deve essere chiamata da qualsiasi luogo oltre che tramite gestione API o l'indirizzo.
   
## <a name="import-the-function-app-definition"></a>Importare la definizione dell'app per le funzioni
1. Aprire il pannello *gestione API*, quindi aprire l' *istanza*.
1. Selezionare il pannello API nella sezione gestione API dell'istanza.
1. Dal riquadro ' Aggiungi nuova API ' scegliere ' app per le funzioni ', quindi selezionare ' completa ' nella parte superiore del popup.
1. Fare clic su Sfoglia, scegliere l'app per le funzioni in cui è ospitata l'API e fare clic su Seleziona.
1. Assegnare all'API un nome e una descrizione per l'uso interno di gestione API e aggiungerlo al prodotto "illimitato".
1. Assicurarsi di registrare l'URL di base per un uso successivo, quindi fare clic su Crea.

## <a name="configure-oauth2-for-api-management"></a>Configurare OAuth2 per gestione API

1. Selezionare quindi il pannello OAuth 2,0 nella scheda sicurezza e fare clic su' Aggiungi '
1. Assegnare i valori per il *nome visualizzato* e la *Descrizione* per l'endpoint OAuth aggiunto. questi valori vengono visualizzati nel passaggio successivo come endpoint OAuth2.
1. È possibile immettere qualsiasi valore nell'URL della pagina di registrazione del client, poiché questo valore non verrà utilizzato.
1. Controllare il tipo di concessione di *autenticazione implicita* e lasciare selezionato il tipo di concessione del codice di autorizzazione.
1. Passare ai campi dell'endpoint di *autorizzazione* e di *token* e immettere in precedenza i valori acquisiti dal documento XML di configurazione noto.
1. Scorrere verso il basso e popolare un *parametro del corpo aggiuntivo* denominato "Resource" con l'ID client dell'API della funzione back-end dalla registrazione dell'app Azure ad B2C
1. Selezionare ' credenziali client ', impostare l'ID client sull'ID app dell'app console per sviluppatori. ignorare questo passaggio se si usa il modello di gestione API a consumo.
1. Impostare il segreto client sulla chiave registrata in precedenza. ignorare questo passaggio se si usa il modello di gestione API a consumo.
1. Infine, registrare ora il redirect_uri della concessione del codice di autenticazione da gestione API per un uso successivo.

## <a name="set-up-oauth2-for-your-api"></a>Configurare OAuth2 per l'API
1. L'API verrà visualizzata sul lato sinistro del portale sotto la sezione ' tutte le API '. per aprire l'API, fare clic su di essa.
1. Selezionare la scheda "Settings" (impostazioni).
1. Per aggiornare le impostazioni, selezionare "OAuth 2,0" dal pulsante di opzione autorizzazione utente.
1. Selezionare il server OAuth definito in precedenza.
1. Selezionare la casella di controllo "ambito di override" e immettere l'ambito registrato per la chiamata all'API back-end precedente in.

   > [!NOTE]
   > A questo punto è disponibile un'istanza di gestione API che sa come ottenere i token di accesso da Azure AD B2C per autorizzare le richieste e comprendere la configurazione della Azure Active Directory B2C OAuth2.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>Configurare i criteri **CORS** e **Validate-JWT**

> Le sezioni seguenti devono essere seguite indipendentemente dal livello di gestione API utilizzato. 

1. Tornare alla scheda Progettazione e scegliere "tutte le API", quindi fare clic sul pulsante visualizzazione codice per visualizzare l'editor dei criteri.
1. Modificare la sezione in ingresso e incollare il codice XML seguente in modo che legga come segue.

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
1. Modificare l'URL OpenID-config in modo che corrisponda all'endpoint Azure AD B2C noto per i criteri di iscrizione o di accesso.
1. Modificare il valore dell'attestazione in modo che corrisponda all'ID applicazione valido, noto anche come ID client per l'applicazione API back-end e Salva.

   > [!NOTE]
   > Gestione API è ora in grado di rispondere alle richieste tra le origini per le app di JS SPA e consente di eseguire la limitazione, la limitazione della frequenza e la pre-convalida del token di autenticazione JWT passato prima di inoltrare la richiesta all'API della funzione.

   > [!NOTE]
   > La sezione seguente è facoltativa e non si applica al livello di **consumo** , che non supporta il portale per sviluppatori.
   > Se non si intende usare il portale per sviluppatori o non è possibile usarlo perché si usa il livello di consumo, ignorare questo passaggio e passare direttamente a ["compilare la Spa JavaScript per utilizzare l'API"](#build-the-javascript-spa-to-consume-the-api).

## <a name="optional-configure-the-developer-portal"></a>Opzionale Configurare il portale per sviluppatori

1. Aprire il pannello Azure AD B2C e passare alla registrazione dell'applicazione per il portale per sviluppatori
1. Impostare la voce ' URL di risposta ' su quella annotata quando è stata configurata la redirect_uri della concessione del codice di autenticazione in gestione API in precedenza.

   Ora che l'autorizzazione utente OAuth 2,0 è abilitata in `Echo API` , la console per sviluppatori ottiene un token di accesso per l'utente, prima di chiamare l'API.

1. Passare a qualsiasi operazione nel `Echo API` portale per sviluppatori e selezionare **prova** per visualizzare la console per sviluppatori.
1. Si noti un nuovo elemento nella sezione **authorization** , corrispondente al server di autorizzazione appena aggiunto.
1. Selezionare **codice di autorizzazione** nell'elenco a discesa autorizzazione. verrà richiesto di accedere al tenant di Azure ad. Se è già stato eseguito l'accesso con l'account, è possibile che non venga richiesto.
1. Dopo aver eseguito l'accesso, `Authorization: Bearer` viene aggiunta un'intestazione alla richiesta con un token di accesso da Azure ad B2C codificato in Base64. 
1. Selezionare **Send (Invia** ). è possibile chiamare l'API correttamente.

   > [!NOTE]
   > Gestione API è ora in grado di acquisire i token per il portale per sviluppatori per testare l'API ed è in grado di comprendere la definizione ed eseguire il rendering della pagina di test appropriata nel portale dev.

1. Nel pannello panoramica del portale di gestione API fare clic su "portale per sviluppatori" per accedere come amministratore dell'API.
1. Qui, l'utente e altri consumer selezionati dell'API possono testarli e chiamarli da una console.
1. Selezionare "prodotti", quindi scegliere "illimitato", quindi scegliere l'API creata in precedenza e fare clic su "prova".
1. Scoprire la chiave di sottoscrizione API e copiarla in una posizione sicura insieme all'URL della richiesta che sarà necessaria in un secondo momento.
1. Selezionare anche implicito, dall'elenco a discesa autenticazione OAuth e potrebbe essere necessario eseguire l'autenticazione con un popup.
1. Fare clic su' Send ' (Invia). Se si tratta di un messaggio, il app per le funzioni deve rispondere con un messaggio Hello tramite gestione API con un messaggio 200 OK e un codice JSON.

   > [!NOTE]
   > Sono ora disponibili Azure AD B2C, gestione API e funzioni di Azure che interagiscono per pubblicare, proteggere e utilizzare un'API. Si potrebbe notare che l'API è effettivamente protetta due volte usando questo metodo, una volta con l'intestazione OCP-Subscription-Key di gestione API e una volta con l'autorizzazione: Bearer JWT.
   > Poiché questo esempio è un'applicazione a pagina singola JavaScript, si userà la chiave di gestione API solo per le chiamate di limitazione e di fatturazione.
   > L'autorizzazione e l'autenticazione effettive vengono gestite da Azure AD B2C e sono incapsulate in JWT, che viene convalidato due volte, una volta per gestione API e quindi da funzioni di Azure.

## <a name="build-the-javascript-spa-to-consume-the-api"></a>Compilare la SPA JavaScript per utilizzare l'API
1. Aprire il pannello account di archiviazione nell'portale di Azure 
1. Selezionare l'account creato e selezionare il pannello "sito Web statico" dalla sezione Impostazioni. se non viene visualizzata l'opzione "sito Web statico", verificare che sia stato creato un account V2.
1. Impostare la funzionalità di hosting Web statico su "Enabled" e impostare il nome del documento di indice su "index.html', quindi fare clic su" Salva ".
1. Annotare il contenuto dell'endpoint primario, perché questo è il percorso in cui verrà ospitato il sito front-end. 

   > [!NOTE]
   > È possibile usare l'archiviazione BLOB di Azure e la riscrittura della rete CDN o il servizio app Azure, ma la funzionalità di hosting di siti web statici dell'archiviazione BLOB fornisce un contenitore predefinito per gestire contenuto Web statico/HTML/JS/CSS da archiviazione di Azure e dedurrà una pagina predefinita per zero lavoro.

## <a name="upload-the-js-spa-sample"></a>Caricare l'esempio di JS SPA
1. Sempre nel pannello dell'account di archiviazione selezionare il pannello "blob" dalla sezione servizio BLOB e fare clic sul contenitore $web visualizzato nel riquadro di destra.
1. Salvare il codice riportato di seguito in un file in locale nel computer come index.html e quindi caricare il file index.html nel contenitore $web.

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
   > È stata appena distribuita un'app JavaScript a pagina singola in archiviazione di Azure, poiché l'app JS non è stata configurata con le chiavi per l'API o è stata configurata l'app JS con i dettagli della Azure AD B2C. la pagina non funzionerà ancora se la si apre.

## <a name="configure-the-js-spa-for-azure-ad-b2c"></a>Configurare JS SPA per Azure AD B2C
1. A questo punto, è possibile configurare la SPA con l'indirizzo API di gestione API appropriato e i Azure AD B2C ID applicazione/client corretti
1. Tornare al pannello archiviazione portale di Azure e fare clic su index.html, quindi scegliere ' modifica BLOB ' 
1. Aggiornare i dettagli di autenticazione in modo che corrispondano all'applicazione front-end registrata in B2C in precedenza, notando che i valori "b2cScopes" sono per il back-end dell'API.
1. La chiave webApi e l'URL dell'API sono disponibili nel riquadro di test di gestione API per l'operazione API.
1. Per creare una chiave di sottoscrizione gestione API, tornare al pannello Gestione API, selezionare "sottoscrizioni" e fare clic su "Aggiungi sottoscrizione" e salvare il record. Facendo clic sui puntini di sospensione (...) accanto alla riga creata, sarà possibile visualizzare le chiavi in modo da poter copiare la chiave primaria.
1. Il codice dovrebbe essere simile al seguente:-  

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

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Impostare gli URI di reindirizzamento per l'app front-end Azure AD B2C
1. Aprire il pannello Azure AD B2C e passare alla registrazione dell'applicazione per l'applicazione front-end JavaScript
1. Impostare l'URL di reindirizzamento su quello annotato quando si è configurato in precedenza l'endpoint primario del sito Web statico sopra

   > [!NOTE] 
   > Questa configurazione comporterà un client dell'applicazione front-end che riceve un token di accesso con le attestazioni appropriate da Azure AD B2C.
   > La SPA sarà in grado di aggiungerla come bearer token nell'intestazione HTTPS nella chiamata all'API back-end.
   > Gestione API consente di pre-convalidare il token, limitare le chiamate all'endpoint tramite la chiave del Sottoscrittore, prima di passare la richiesta all'API della funzione di Azure di destinazione.
   > Il SPA eseguirà il rendering della risposta nel browser.

   > *Complimenti, hai configurato Azure AD B2C, gestione API di Azure, funzioni di Azure, app Azure autorizzazione del servizio per lavorare in perfetta armonia!*

   > [!NOTE]
   > Ora è disponibile una semplice app con una semplice API protetta, che verrà testata.

## <a name="test-the-client-application"></a>Testare l'applicazione client
1. Aprire l'URL dell'app di esempio annotato dall'account di archiviazione creato in precedenza
1. Fare clic su "Accedi" nell'angolo superiore destro. questo clic mostrerà il Azure AD B2C iscrizione o l'accesso al profilo.
1. Il post Sign nella sezione "accesso come" della schermata verrà popolato da JWT.
1. A questo punto fare clic su "chiama API Web" e la pagina deve essere aggiornata con i valori restituiti dall'API protetta.

## <a name="and-were-done"></a>E abbiamo finito
I passaggi precedenti possono essere adattati e modificati per consentire molti usi diversi di Azure AD B2C con gestione API.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni su [Azure Active Directory e OAuth 2.0](../active-directory/develop/authentication-vs-authorization.md).
* Altre informazioni sui [video](https://azure.microsoft.com/documentation/videos/index/?services=api-management) relativi a Gestione API.
* Per altri metodi di protezione del servizio back-end, vedere [Autenticazione reciproca dei certificati](api-management-howto-mutual-certificates.md).
* [Creare un'istanza del servizio gestione API](get-started-create-service-instance.md).
* [Gestire la prima API](import-and-publish.md).
