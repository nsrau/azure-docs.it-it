---
title: Protezione end-to-end del contenuto con Azure AD
description: Questo articolo illustra come proteggere i contenuti con Servizi multimediali di Azure e Azure Active Directory
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/1/2020
ms.author: inhenkel
ms.openlocfilehash: 81c83cd8dcea5f8746b67a7bd52ea52a09c8a711
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87001401"
---
# <a name="tutorial-end-to-end-content-protection-using-azure-ad"></a>Esercitazione: Protezione end-to-end del contenuto con Azure AD

Con questa esercitazione e l'app lettore di esempio fornita è possibile configurare un sottosistema di protezione end-to-end del contenuto multimediale in Servizi multimediali di Azure (AMS) e in Azure Active Directory (AAD) per trasmettere contenuti multimediali con tutti i sistemi di crittografia DRM/AES-128, i protocolli di streaming, i codec e i formati di contenitore supportati da AMS. L'esempio è sufficientemente generico da consentire l'accesso sicuro a qualsiasi API REST protetta da OAuth 2 tramite il flusso di codice di autorizzazione con PKCE (Proof Key for Code Exchange). Il servizio di distribuzione delle licenze di Servizi multimediali di Azure è solo una di queste API. Funziona anche per l'API Microsoft Graph o per qualsiasi API REST personalizzata protetta con il flusso di codice di autorizzazione OAuth 2. Questa è la documentazione relativa al [codice di esempio](https://github.com/Azure-Samples/media-services-content-protection-azure-ad).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>
> * Considerare i requisiti di autenticazione
> * Comprendere il funzionamento dell'app
> * Registrare un'app di risorse back-end
> * Registrare un'app client
> * Configurare i criteri di streaming e i criteri di chiave simmetrica dell'account di Servizi multimediali
> * Configurare l'app lettore

Se non si ha una sottoscrizione di Servizi multimediali di Azure, creare un [account di valutazione gratuito](https://azure.microsoft.com/free/) di Azure e quindi creare un account di Servizi multimediali.

### <a name="duration"></a>Durata
Una volta predisposta la tecnologia essenziale, per completare questa esercitazione sono necessarie circa due ore.

## <a name="prerequisites"></a>Prerequisiti

Vengono usati i concetti e le versioni della tecnologia più recente seguenti. È consigliabile acquisire familiarità con questi concetti e tecnologie prima di iniziare l'esercitazione.

### <a name="prerequisite-knowledge"></a>Conoscenze richieste

È consigliabile, anche se non obbligatorio, avere familiarità con i concetti seguenti prima di iniziare l'esercitazione:

* Digital Rights Management (DRM)
* [Servizi multimediali di Azure (AMS) v3](./media-services-overview.md)
* [Criteri di chiave simmetrica](content-key-policy-concept.md) di AMS con l'API AMS v3, il portale di Azure o lo [strumento Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer)
* Endpoint di Azure AD v2 in [Microsoft Identity Platform](../../active-directory/develop/index.yml)
* Autenticazione cloud moderna come [OAuth 2.0 e OpenID Connect](../../active-directory/develop/active-directory-v2-protocols.md)
  * [Flusso di codice di autorizzazione in OAuth 2.0](../../active-directory/develop/v2-oauth2-auth-code-flow.md) e perché PKCE è necessario
  * [Confronto tra autorizzazione delegata e autorizzazione dell'applicazione](../../active-directory/develop/developer-glossary.md#permissions)
* [Token JWT](../../active-directory/develop/access-tokens.md), relative attestazioni e rollover della chiave di firma (inclusi nell'esempio)

### <a name="prerequisite-code-and-installations"></a>Codice e installazioni necessari

* Il codice di esempio. Scaricare il [codice di esempio](https://github.com/Azure-Samples/media-services-content-protection-azure-ad).
* Un'installazione di Visual Studio Code. Scaricare Visual Studio Code qui [https://code.visualstudio.com/download](https://code.visualstudio.com/download).
* Un'installazione di Node.js. Scaricare Node.js qui [https://nodejs.org](https://nodejs.org). NPM viene fornito con l'installazione.
* Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
* Un account di Servizi multimediali di Azure (AMS).
* @azure/msal-browser v2.0, uno dei membri della famiglia di SDK [Microsoft Authentication Library (MSAL)](../../active-directory/develop/msal-overview.md) per diverse piattaforme client.
* La versione più recente di [Azure Media Player](https://github.com/Azure-Samples/azure-media-player-samples) (incluso nell'esempio).
* Le credenziali FPS di Apple se si vuole includere il sistema DRM FairPlay e il certificato dell'applicazione ospitato con CORS accessibile tramite JavaScript lato client.

> [!IMPORTANT]
> Questa esercitazione usa .NET per creare le restrizioni dei criteri di chiave simmetrica.  Se non si sviluppa in .NET ma si vuole provare Node.js per connettersi a Servizi multimediali di Azure, vedere [Connettersi all'API di Servizi multimediali v3 - Node.js](configure-connect-nodejs-howto.md). È disponibile anche un modulo Node.js per gestire automaticamente il rollover della chiave. Vedere [passport-ad module](https://github.com/AzureAD/passport-azure-ad) di Node.js.

## <a name="consider-the-authentication-and-authorization-requirements"></a>Considerare i requisiti di autenticazione e autorizzazione

La progettazione del sottosistema presenta alcune sfide. Entrano in gioco diverse parti mobili, vincoli per le app client e il rollover della chiave di Azure AD che ha luogo ogni sei settimane.

L'applicazione a pagina singola usata in questa esercitazione tiene in considerazione le complessità dei requisiti di autenticazione e le restrizioni che seguono. Usa:

* Gli endpoint di Azure AD v2 in quanto la piattaforma di sviluppo di Azure AD (endpoint v1) sta passando a Microsoft Identity Platform (endpoint v2).
* Il flusso di codice di autorizzazione perché il flusso di concessione implicito di OAuth 2 è stato deprecato.
* Un'app soggetta ai vincoli seguenti:
    * Un client pubblico non può nascondere il segreto client.  Il flusso di codice di autorizzazione usato da solo richiede di nascondere il segreto client, quindi viene usato con PKCE.
    * Un'app basata su browser soggetta a una sandbox di sicurezza del browser (vincolo CORS/preliminare).
    * Un'app basata su browser che usa JavaScript moderno soggetto ai vincoli di sicurezza JavaScript (accessibilità intestazioni personalizzate, ID correlazione).

## <a name="understand-the-subsystem-design"></a>Informazioni sulla progettazione del sottosistema

La progettazione del sottosistema è illustrata nel diagramma seguente.  È costituita da tre livelli:

* Il livello di back office (in nero) per la configurazione dei criteri di chiave simmetrica e la pubblicazione di contenuto per lo streaming
* Gli endpoint pubblici (in blu), ossia endpoint dell'app lettore/rivolti al cliente che forniscono autenticazione, autorizzazione, licenza DRM e contenuto crittografato
* L'app lettore (in azzurro) che integra tutti i componenti e
    * gestisce l'autenticazione utente con Azure AD.
    * gestisce l'acquisizione del token di accesso da Azure AD.
    * riceve il manifesto e il contenuto crittografato da AMS/CDN.
    * acquisisce la licenza DRM da Servizi multimediali di Azure.
    * gestisce la decrittografia, la decodifica e la visualizzazione del contenuto.

![Schermata per l'analisi dei token JWT](media/aad-ams-content-protection/subsystem.svg)

Per altre informazioni sul sottosistema, vedere [Progettazione di un sistema di protezione del contenuto con DRM multiplo e controllo di accesso](./design-multi-drm-system-with-access-control.md).

## <a name="understand-the-single-page-app"></a>Informazioni sull'applicazione a pagina singola

L'app lettore è un'applicazione a pagina singola sviluppata in Visual Studio Code con:

* Node.js con ES 6 JavaScript
* @azure/msal-browser 2.0 beta
* Azure Media Player SDK
* Flusso OAuth 2 su endpoint di Azure AD v2 (Microsoft Identity Platform)

L'app lettore a pagina singola completa le azioni seguenti:

* Autenticazione utente per gli utenti nativi del tenant e gli utenti guest di altri tenant AAD o account MSA. Gli utenti possono scegliere di accedere tramite un popup del browser o un reindirizzamento (per i browser che non consentono i popup come Safari).
* Acquisizione di `access_token` tramite il flusso di codice di autorizzazione con PKCE.
* Rinnovo di `access_token` (i token emessi da AAD sono validi per 1 ora), per cui viene acquisito anche `refresh_token`.
* Analisi dei token JWT (sia `access_token` che `id_token`) a scopo di test/ispezione.
* Acquisizione delle licenze DRM per tutti e tre i sistemi DRM o la chiave simmetrica AES-128.
* Streaming del contenuto in varie combinazioni di DRM, protocollo di streaming e formato contenitore. Per ogni combinazione viene generata la stringa di formato corretta.
* Decrittografia, decodifica e visualizzazione.
* Chiamate API Microsoft Graph per la risoluzione dei problemi. <!--See more details in the subsection Shortest path: testing my protected asset in my subscription with your hosted player app and underlying tenant. -->

Ecco la schermata per l'accesso e per l'acquisizione, il rinnovo e la visualizzazione dei token:

 ![Schermata per l'accesso e per l'acquisizione, il rinnovo e la visualizzazione dei token](media/aad-ams-content-protection/token-acquisition.png)

Ecco la schermata per l'analisi dei token JWT (access_token o id_token):

![Schermata per l'analisi dei token JWT](media/aad-ams-content-protection/parsing-jwt-tokens.png)

Ecco la schermata per il testing del contenuto protetto con diverse combinazioni di DRM/AES, protocolli di streaming e formato contenitore:

![Schermata per l'analisi dei token JWT](media/aad-ams-content-protection/testing-protected-content.png)
-->

<!-- You can see a hosted version of the sample at [https://aka.ms/ott](https://aka.ms/ott)-->

## <a name="choose-an-azure-ad-tenant"></a>Scegliere un tenant di Azure AD

> [!NOTE]
> Da questo punto in poi si presuppone che sia stato eseguito l'accesso al portale di Azure e che si abbia almeno un tenant di Azure AD.

Scegliere un tenant di Azure AD da usare per l'esempio end-to-end. Sono disponibili due opzioni:

* Un tenant di Azure AD esistente. Ogni sottoscrizione di Azure deve avere un tenant di Azure AD, ma un tenant di Azure AD può essere usato da più sottoscrizioni di Azure.
* Un nuovo tenant di Azure AD *non* usato da alcuna sottoscrizione di Azure. Se si sceglie l'opzione del nuovo tenant, l'account del servizio multimediale e l'app lettore di esempio devono trovarsi in una sottoscrizione di Azure che usa un tenant di Azure AD separato. Ciò consente una certa flessibilità. Ad esempio, è possibile usare il proprio tenant di Azure AD ma anche l'account del servizio multimediale del cliente nella sottoscrizione di Azure del cliente.

## <a name="register-the-backend-resource-app"></a>Registrare l'app di risorse back-end

1. Passare al tenant di Azure AD scelto o creato.
1. Selezionare **Azure Active Directory** dal menu.
1. Selezionare **Registrazioni app** dal menu.
1. Fare clic su **+ Nuova registrazione**.
1. Assegnare all'app il nome *LicenseDeliveryResource2* (dove 2 indica gli endpoint di AAD v2).
1. Selezionare **Account solo in questa directory dell'organizzazione (Solo ([*nome tenant*] - Tenant singolo)** . Se si vuole consentire l'accesso a più tenant, selezionare una delle altre opzioni multi-tenant.
1. L'opzione **URI di reindirizzamento** è facoltativa e può essere modificata in un secondo momento.
1. Fare clic su **Register**. Si aprirà la visualizzazione Registrazioni app.
1. Selezionare **Manifesto** dal menu. Si aprirà la visualizzazione Manifesto.
1. Cambiare il valore di `accessTokenAcceptedVersion` in *2* (senza virgolette).
1. Cambiare il valore di `groupMembershipClaims` in *"SecurityGroup"* (con le virgolette).
1. Fare clic su **Salva**.
1. Selezionare **Esporre un'API** dal menu. Si aprirà la visualizzazione Aggiungi un ambito. Azure fornisce un URI dell'ID applicazione, ma se si vuole è possibile modificarlo nel campo URI ID applicazione.
1. Fare clic su **Salva e continua**. La visualizzazione cambierà. Per ogni impostazione nella colonna Impostazione della tabella seguente immettere il valore nella colonna Valore, quindi fare clic su **Aggiungi ambito**.

| Impostazione | Valore | Descrizione |
| ------- | ----- | ----------- |
| Nome ambito | *DRM.License.Delivery* | Nome con cui verrà visualizzato l'ambito quando viene richiesto l'accesso a questa API e nei token di accesso quando l'ambito è stato concesso a un'applicazione client. Deve essere univoco nell'intera applicazione. Una procedura consigliata consiste nell'usare "risorsa.operazione.vincolo" come modello per generare il nome. |
| Utenti che possono fornire il consenso | *Amministratori e utenti* | Determina se gli utenti possono fornire il consenso per questo ambito nelle directory in cui è abilitato il consenso utente. |
| Nome visualizzato per il consenso amministratore | *Distribuzione di licenze DRM* | Nome assegnato all'ambito nella schermata di consenso quando gli amministratori forniscono il consenso per l'ambito. |
| Descrizione del consenso amministratore** | *Ambito della risorsa back-end per la distribuzione di licenze DRM* | Descrizione dettagliata dell'ambito visualizzata quando gli amministratori del tenant espandono un ambito nella schermata di consenso. |
| Nome visualizzato per il consenso utente | *DRM.License.Delivery* | Nome assegnato all'ambito nella schermata di consenso quando gli utenti forniscono il consenso per l'ambito. |
| Descrizione del consenso utente | *Ambito della risorsa back-end per la distribuzione di licenze DRM* | Descrizione dettagliata dell'ambito visualizzata quando gli utenti espandono un ambito nella schermata di consenso. |
| State | *Enabled* | Determina se l'ambito è disponibile per la richiesta da parte dei client. Impostare questa opzione su "Disabilitato" per gli ambiti che non devono essere visibili ai client. È possibile eliminare solo gli ambiti disabilitati ed è consigliabile attendere almeno una settimana dopo la disabilitazione di un ambito prima di eliminarlo, in modo da assicurarsi che non sia ancora usato dai client. |

## <a name="register-the-client-app"></a>Registrare l'app client

1. Passare al tenant di Azure AD scelto o creato.
1. Selezionare **Azure Active Directory** dal menu.
1. Selezionare **Registrazioni app** dal menu.
1. Fare clic su **+ Nuova registrazione**.
1. Assegnare un nome all'applicazione client, ad esempio *Protezione del contenuto AMS AAD*.
1. Selezionare **Account solo in questa directory dell'organizzazione (Solo ([*nome tenant*] - Tenant singolo)** . Se si vuole consentire l'accesso a più tenant, selezionare una delle altre opzioni multi-tenant.
1. L'opzione **URI di reindirizzamento** è facoltativa e può essere modificata in un secondo momento.
1. Fare clic su **Registra**.
1. Selezionare **Autorizzazioni API** dal menu.
1. Fare clic su **+ Aggiungi un'autorizzazione**. Si aprirà la visualizzazione Richiedi le autorizzazioni dell'API.
1. Fare clic sulla scheda **Le mie API** e selezionare l'app *LicenseDeliveryResource2* creata nell'ultima sezione.
1. Fare clic sulla freccia per DRM e selezionare l'autorizzazione *DRM.License.Delivery*.
1. Fare clic su **Aggiungi autorizzazioni**. La visualizzazione Aggiungi autorizzazioni verrà chiusa.
1. Selezionare **Manifesto** dal menu. Si aprirà la visualizzazione Manifesto.
1. Trovare e aggiungere le coppie di valori seguenti all'attributo `replyUrlsWithType`:

   ```json
   "replyUrlsWithType": [
        {
            "url": "https://npmwebapp.azurewebsites.net/",
            "type": "SPA"
        },
        {
            "url": "http://localhost:3000/",
            "type": "SPA"
        }
    ],
   ```

    > [!NOTE]
    > A questo punto non si dispone ancora dell'URL per l'app lettore.  Se si esegue l'app dal server Web localhost, è possibile usare semplicemente la coppia di valori di localhost. Una volta distribuita l'app lettore, è possibile aggiungere la voce qui con l'URL distribuito.  Se si dimentica di farlo, verrà visualizzato un messaggio di errore nella schermata di accesso ad Azure AD.

1. Fare clic su **Salva**.
1. Infine, per verificare che la configurazione sia corretta, selezionare **Autenticazione**.  Si aprirà la visualizzazione Autenticazione, che mostra l'applicazione client come applicazione a pagina singola, l'URI di reindirizzamento e il tipo di concessione come Flusso di codice di autorizzazione con PKCE.

### <a name="set-up-the-media-services-account-content-key-policy-and-streaming-policies"></a>Configurare i criteri di streaming e i criteri di chiave simmetrica dell'account di Servizi multimediali

**Questa sezione presuppone che si sviluppi in .NET e si abbia familiarità con l'uso dell'API AMS v3.**

> [!NOTE]
> Al momento della stesura di questo articolo, non è possibile usare il portale di Azure per la configurazione dei criteri della chiave dell'account di servizi multimediali perché non supporta l'uso di una chiave per la firma di token asimmetrica con OpenID-Config.  La configurazione deve supportare il rollover della chiave di Azure AD perché il token emesso da Azure AD è firmato da una chiave asimmetrica e il rollover della chiave viene eseguito ogni sei settimane. Pertanto, questa esercitazione usa .NET e l'API AMS v3.

Viene applicata la configurazione dei [criteri di chiave simmetrica](content-key-policy-concept.md) e dei [criteri di streaming](streaming-policy-concept.md) per DRM e AES-128.  Cambiare il valore di `ContentKeyPolicyRestriction` nei criteri di chiave simmetrica.

Di seguito è riportato il codice .NET per la creazione della restrizione dei criteri di chiave simmetrica.

```dotnetcli
ContentKeyPolicyRestriction objContentKeyPolicyRestriction;

//use Azure Active Directory OpenId discovery document, supporting key rollover
objContentKeyPolicyRestriction = new ContentKeyPolicyTokenRestriction()
    {
        OpenIdConnectDiscoveryDocument = ConfigAccessor.AppSettings["ida_AADOpenIdDiscoveryDocument"]
    };

string audience = ConfigAccessor.AppSettings["ida_audience"];
string issuer   = ConfigAccessor.AppSettings["ida_issuer"];

ContentKeyPolicyTokenRestriction objContentKeyPolicyTokenRestriction = (ContentKeyPolicyTokenRestriction)objContentKeyPolicyRestriction;
objContentKeyPolicyTokenRestriction.Audience             = audience;
objContentKeyPolicyTokenRestriction.Issuer               = issuer;
objContentKeyPolicyTokenRestriction.RestrictionTokenType = ContentKeyPolicyRestrictionTokenType.Jwt;

objContentKeyPolicyRestriction = objContentKeyPolicyTokenRestriction;

return objContentKeyPolicyRestriction;
```

Cambiare i valori di `ida_AADOpenIdDiscoveryDocument`, `ida_audience` e `ida_issuer` nel codice riportato sopra. Per trovare i valori di questi elementi nel portale di Azure:

1. Selezionare il tenant di AAD usato in precedenza, fare clic su **Registrazioni app** nel menu, quindi fare clic sul collegamento **Endpoint**.
1. Selezionare e copiare il valore del campo **Documento di metadati OpenIdConnect** e incollarlo nel codice come valore di `ida_AADOpenIdDiscoveryDocument`.
1. Il valore di `ida_audience` è l'ID applicazione (client) dell'app registrata *LicenseDeliveryResource2*.
1. Il valore di `ida_issuer` è l'URL `https://login.microsoftonline.com/[tenant_id]/v2.0`. Sostituire [*tenant_id*] con l'ID del tenant.

## <a name="set-up-the-sample-player-app"></a>Configurare l'app lettore di esempio

Se non è già stato fatto, clonare o scaricare l'app dal repository di origine: [https://github.com/Azure-Samples/media-services-content-protection-azure-ad](https://github.com/Azure-Samples/media-services-content-protection-azure-ad).

Sono disponibili due opzioni per configurare l'app lettore:

* Personalizzazione minima (sostituendo solo alcuni valori stringa costanti come client_id, tenant_id e l'URL di streaming), ma è necessario usare Visual Studio Code e Node. js.
* Se si preferisce usare un altro IDE e una piattaforma Web come ASP.NET Core, è possibile inserire i file di pagina Web, i file JavaScript e il file CSS nel progetto, in quanto l'app lettore non usa codice lato server.

### <a name="option-1"></a>Opzione 1

1. Avviare Visual Studio Code.
1. Per aprire il progetto, fare clic su File-> Apri cartella-> individuare e selezionare la cartella padre del file *package.json*.
1. Aprire il file JavaScript *public/javascript/constants.js*.
1. Sostituire `OAUTH2_CONST.CLIENT_ID` con il `client_id` dell'applicazione client registrata nel tenant di AAD.  È possibile trovare il `client_id` nella sezione Panoramica dell'app registrata nel portale di Azure. Nota: è l'ID del client, non dell'oggetto.
1. Sostituire `OAUTH2_CONST.TENANT_ID` con il `tenant_id` del tenant di Azure AD. È possibile trovare il `tenant_id` facendo clic sul menu Azure Active Directory. Il tenant_id verrà visualizzato nella sezione Panoramica.
1. Sostituire `OAUTH2_CONST.SCOPE` con l'ambito aggiunto nell'app client registrata. È possibile trovare l'ambito passando all'app client registrata dal menu **Registrazioni app** e quindi selezionandola:
    1. Selezionare l'app client, fare clic sul menu **Autorizzazioni API**, quindi selezionare l'ambito *DRM.License.Delivery* nell'autorizzazione API *LicenseDeliveryResource2*. Il formato dell'autorizzazione dovrebbe essere simile a *api://df4ed433-dbf0-4da6-b328-e1fe05786db5/DRM.License.Delivery*. **Importante**: mantenere lo spazio davanti a `offline_access` in `OAUTH2_CONST.SCOPE`.
1. Sostituire le due stringhe costanti per `AMS_CONST` come indicato di seguito. Una è l'URL di streaming protetto della risorsa di test e l'altra è l'URL del certificato dell'applicazione FPS se si vuole includere il test case FairPlay. Altrimenti è possibile lasciare invariato `AMS_CONST.APP_CERT_URL`. Fare quindi clic su **Salva**.

```javascript
//defaults in ams.js
class AMS_CONST {
    static get DEFAULT_URL() {
        return "https://eventgridmediaservice-usw22.streaming.media.azure.net/9591e337-ae90-420e-be30-1da36c06665b/MicrosoftElite01.ism/manifest(format=mpd-time-csf,encryption=cenc)";
    }
    //FairPlay application cert URL
    static get APP_CERT_URL() {
     return `${window.location.href}cert/FPSAC.cer`;
    }
}
```

Per eseguire il test in locale:

1. In Visual Studio Code selezionare **Visualizza** dal menu principale, quindi **Terminale**.
1. Se npm non è già stato installato, al prompt dei comandi immettere `npm install`.
1. Al prompt dei comandi immettere `npm start`. Se npm non viene avviato, provare a cambiare la directory in `npmweb` immettendo `cd npmweb` al prompt dei comandi.
1. Usare un browser per passare a `http://localhost:3000`.

A seconda del browser usato, selezionare la combinazione corretta di DRM/AES, protocollo di streaming e formato contenitore da testare dopo l'accesso (acquisizione di `access_token`). Se si esegue il test in Safari su macOS, selezionare l'opzione di reindirizzamento API perché Safari non consente i popup. La maggior parte degli altri browser supporta sia i popup che le opzioni di reindirizzamento.

### <a name="option-2"></a>Opzione 2

Se si prevede di usare un altro IDE o un'altra piattaforma Web e/o un server Web come IIS in esecuzione nel computer di sviluppo, copiare i file seguenti in una nuova directory nel server Web locale. Questi file sono disponibili nei percorsi seguenti nel codice scaricato.

* *views/index.ejs* (cambiare il suffisso in .html)
* *views/jwt.ejs* (cambiare il suffisso in .html)
* *views/info.ejs* (cambiare il suffisso in html)
* *public/* * (file JavaScript, CSS, immagini come illustrato di seguito)

1. Copiare i file trovati nella cartella *views* nella radice della nuova directory.
1. Copiare le *cartelle* trovate nella cartella *public* nella radice della nuova directory.
1. Cambiare le estensioni dei file `.ejs` in `.html`. Non vengono usate variabili lato server quindi è possibile cambiarle senza problemi.
1. Aprire *index.html* in Visual Studio Code (o in un altro editor di codice) e modificare i percorsi `<script>` e `<link>` in modo che riflettano la posizione dei file.  Se sono stati seguiti i passaggi precedenti, è sufficiente eliminare `\` nel percorso.  Ad esempio `<script type="text/javascript" src="/javascript/constants.js"></script>` diventa `<script type="text/javascript" src="javascript/constants.js"></script>`.
1. Personalizzare le costanti nel file *javascript/constants.js* come in Opzione 1.

## <a name="common-customer-scenarios"></a>Scenari di clienti comuni

Ora che l'esercitazione è stata completata e si dispone di un sottosistema funzionante, è possibile provare a modificarlo per gli scenari dei clienti seguenti:

### <a name="role-based-access-control-rbac-for-license-delivery-via-azure-ad-group-membership"></a>Controllo degli accessi in base al ruolo per la distribuzione delle licenze tramite l'appartenenza ai gruppi di Azure AD

Fino a questo punto, il sistema consente a tutti gli utenti che possono accedere di ottenere una licenza valida e riprodurre i contenuti protetti.

Tutti i clienti in genere esprimono l'esigenza che un sottoinsieme di utenti autenticati possa accedere ai contenuti mentre altri utenti no, come nel caso di un cliente che offre sottoscrizioni Basic e Premium per i suoi contenuti video. Chi paga per una sottoscrizione Basic non dovrebbe essere in grado di accedere a contenuti che richiedono una sottoscrizione Premium. Per soddisfare questo requisito è necessario eseguire questi passaggi aggiuntivi:

#### <a name="set-up-the-azure-ad-tenant"></a>Configurare il tenant di Azure AD

1. Configurare due account nel tenant, assegnando loro i nomi *premium_user* e *basic_user*.
1. Creare un gruppo utenti e assegnargli il nome *PremiumGroup*.
1. Aggiungere *premium_user* a *PremiumGroup* come membro, ma non aggiungere *basic_user* al gruppo.
1. Prendere nota dei valori di **ID oggetto** e di *PremiumGroup*.

#### <a name="set-up-the-media-services-account"></a>Configurare l'account Servizi multimediali di Azure

Modificare `ContentKeyPolicyRestriction` (come illustrato nella sezione precedente relativa alla configurazione dell'account di Servizi multimediali) aggiungendo un'attestazione denominata *groups*, dove `ida_EntitledGroupObjectId` ha come valore l'ID oggetto di *PremiumGroup*:

```dotnetcli

var tokenClaims = new ContentKeyPolicyTokenClaim[] { new ContentKeyPolicyTokenClaim("groups", ConfigAccessor.AppSettings["ida_EntitledGroupObjectId"])
//, other claims, if any.
};

if (tokenClaims != null && tokenClaims.Length > 0)
{
     objContentKeyPolicyTokenRestriction.RequiredClaims = new List<ContentKeyPolicyTokenClaim>(tokenClaims);
}
```

L'attestazione *groups* è membro di un [set di attestazioni con restrizioni](../../active-directory/develop/active-directory-claims-mapping.md#claim-sets) in Azure AD.

#### <a name="test"></a>Test

1. Accedere con l'account *premium_user*. Si dovrebbe essere in grado di riprodurre il contenuto protetto.
1. Accedere con l'account *basic_user*. Si dovrebbe ricevere un messaggio di errore che indica che il video è crittografato ma non è presente alcuna chiave per decrittografarlo. Se si visualizzano gli eventi, gli errori e i download con l'elenco a discesa nella parte inferiore della sovrimpressione di diagnostica dell'app, il messaggio di errore dovrebbe indicare un errore di acquisizione della licenza a causa del valore mancante per l'attestazione groups nel token JWT emesso dall'endpoint del token di Azure AD.

### <a name="supporting-multiple-media-service-accounts-across-multiple-subscriptions"></a>Supporto di più account di servizi multimediali (in più sottoscrizioni)

Un cliente può avere più account di servizi multimediali in una o più sottoscrizioni di Azure. Ad esempio, potrebbe avere un account di servizi multimediali come account primario di produzione, un altro come account secondario/di backup e un altro a scopo di sviluppo/test.

Tutto ciò che occorre fare è assicurarsi di usare gli stessi parametri usati nella sezione relativa alla configurazione dell'account di Servizi multimediali per creare `ContentKeyPolicyRestriction` in tutti gli account di servizi multimediali.

### <a name="supporting-a-customer-its-vendors-andor-subsidiaries-across-multiple-aad-tenants"></a>Supporto di un cliente e dei relativi fornitori e/o filiali in più tenant di AAD

Come utenti della soluzione, le filiali e i fornitori/partner di un cliente possono trovarsi in tenant di AAD diversi, ad esempio `mycustomer.com`, `mysubsidiary.com` e `myparther.com`. Anche se questa soluzione si basa su un singolo tenant AAD specifico, ad esempio `mycustomer.com`, è possibile configurarla in modo da essere usata anche dagli utenti di altri tenant.

Usando `mycustomer.com` per questa soluzione, aggiungere un utente di `mypartner.com` come utente guest al tenant `mycustomer.com`. Assicurarsi che l'utente `mypartner.com` attivi l'account guest. L'account guest può essere di un altro tenant di AAD o può essere un account `outlook.com`.

Si noti che gli utenti guest di `mypartner.com`, dopo essere stati attivati in `mycustomer.com`, vengono ancora autenticati tramite il tenant AAD originale, `mypartner.com`, ma `access_token` viene emesso da `mycustomer.com`.

### <a name="supporting-a-customer-tenantsubscription-with-a-setup-in-your-subscriptiontenant"></a>Supporto del tenant o della sottoscrizione di un cliente con una configurazione nella propria sottoscrizione o nel proprio tenant

È possibile usare la propria configurazione per testare il contenuto protetto nella sottoscrizione o nell'account di servizi multimediali del cliente. Occorre configurare un tenant di Azure AD e un account di servizi multimediali nella stessa sottoscrizione. L'account di servizi multimediali del cliente deve essere nella sua sottoscrizione di Azure con il suo tenant di Azure AD.

1. Aggiungere l'account del cliente al proprio tenant come account guest.
1. In collaborazione con il cliente preparare il contenuto protetto nell'account di servizi multimediali del cliente fornendo i tre parametri elencati nella sezione relativa alla configurazione dell'account di Servizi multimediali.

Il cliente può quindi passare alla configurazione, accedere con l'account guest e testare il proprio contenuto protetto. È anche possibile accedere con il proprio account e testare il contenuto del cliente.

La soluzione di esempio può essere configurata in un tenant Microsoft con una sottoscrizione Microsoft o in un tenant personalizzato con una sottoscrizione Microsoft. L'istanza di Servizi multimediali di Azure può essere di un'altra sottoscrizione con il proprio tenant.

## <a name="clean-up-resources"></a>Pulire le risorse

> [!WARNING]
> Se non si intende continuare a usare questa applicazione, eliminare le risorse create seguendo questa esercitazione. In caso contrario, verranno addebitati i costi per l'uso di queste risorse.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Avvio rapido: Crittografare il contenuto](encrypt-content-quickstart.md)
