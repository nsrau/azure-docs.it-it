---
title: Proteggere il back-end di un'API Web con Azure Active Directory e Gestione API | Documentazione Microsoft
description: Informazioni su come proteggere il back-end di un'API Web con Azure Active Directory e Gestione API.
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: 32adec024b2ee2649f2631cfa72ee575094aa0c0
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="how-to-protect-a-web-api-backend-with-azure-active-directory-and-api-management"></a>Come proteggere il back-end di un'API Web con Azure Active Directory e Gestione API
Il video che segue illustra come compilare il back-end di un'API Web e proteggerlo usando il protocollo OAuth 2.0 con Azure Active Directory e Gestione API.  Questo articolo fornisce una panoramica e informazioni aggiuntive per le procedure illustrate nel video. Il video della durata di 24 minuti mostra come fare per:

* Compilare il back-end di un'API Web e proteggerlo con AAD - iniziando all’1:30
* Importare l'API in Gestione API - iniziando alle 7:10
* Configurare il portale per sviluppatori per chiamare l'API - iniziando alle 9:09
* Configurare un'applicazione desktop per chiamare l'API - iniziando alle 18:08
* Configurare criteri di convalida JWT per preautorizzare le richieste - iniziando alle 20:47

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Protecting-Web-API-Backend-with-Azure-Active-Directory-and-API-Management/player]
> 
> 

## <a name="create-an-azure-ad-directory"></a>Creare una directory di Azure AD
Per proteggere il back-end dell'API Web con Azure Active Directory, si deve avere prima di tutto un tenant AAD. Questo video usa un tenant denominato **APIMDemo** . Per creare un tenant AAD, accedere al [portale di Azure classico](https://manage.windowsazure.com) e fare clic su **Nuovo**->**Servizi app**->**Active Directory**->**Directory**->**Creazione personalizzata**. 

![Azure Active Directory][api-management-create-aad-menu]

In questo esempio viene creata una directory denominata **APIMDemo** con un dominio predefinito denominato **DemoAPIM.onmicrosoft.com**. Questa directory viene usata in tutto il video.

![Azure Active Directory][api-management-create-aad]

## <a name="create-a-web-api-service-secured-by-azure-active-directory"></a>Creare un servizio API Web protetto da Azure Active Directory
In questo passaggio viene creato il back-end di un'API Web con Visual Studio 2013. Questo passaggio del video inizia da 1:30 minuti. Per creare un progetto back-end dell'API Web in Visual Studio, fare clic su **File**->**Nuovo**->**Progetto** e scegliere **Applicazione Web ASP.NET** dall'elenco di modelli **Web**. In questo video il progetto è denominato **APIMAADDemo**. Fare clic su **OK** per creare il progetto. 

![Visual Studio][api-management-new-web-app]

Fare clic su **API Web** nell'elenco **Selezionare un modello** per creare un progetto API Web. Per configurare l'autenticazione di Azure Active Directory fare clic su **Modifica autenticazione**.

![Nuovo progetto][api-management-new-project]

Fare clic su **Account aziendali** e specificare il **Dominio** del tenant AAD. In questo esempio il dominio è **DemoAPIM.onmicrosoft.com**. Il dominio della directory può essere rilevato dalla scheda **Domini** della stessa directory.

![Domini][api-management-aad-domains]

Configurare le impostazioni nella finestra di dialogo **Modifica autenticazione** e fare clic su **OK**.

![Modifica autenticazione][api-management-change-authentication]

Quando si fa clic su **OK** , Visual Studio prova a registrare l'applicazione con la directory di Azure AD e all'utente potrebbe essere richiesto di accedere da Visual Studio. Accedere con un account amministrativo per la directory.

![Accesso a Visual Studio][api-management-sign-in-vidual-studio]

Per configurare questo progetto come un'API Web di Azure, selezionare la casella **Ospita nel cloud**, quindi fare clic su **OK**.

![Nuovo progetto][api-management-new-project-cloud]

Potrebbe venire richiesto di accedere ad Azure, dopo di che si potrà configurare l'app Web.

![Configurare][api-management-configure-web-app]

In questo esempio viene specificato un nuovo **Piano di servizio app** denominato **APIMAADDemo**.

Fare clic su **OK** per configurare l'app Web e creare il progetto.

## <a name="add-the-code-to-the-web-api-project"></a>Aggiungere il codice al progetto API Web
Il passaggio successivo del video aggiunge il codice al progetto API Web API. Questo passaggio inizia da 4:35 minuti.

L'API Web in questo esempio implementa un servizio calcolatrice di base usando un modello e un controller. Per aggiungere il modello al servizio, fare clic con il pulsante destro del mouse su **Modelli** in **Esplora soluzioni** e scegliere **Aggiungi**, **Classe**. Assegnare alla classe il nome `CalcInput` e fare clic su **Aggiungi**.

Aggiungere l'istruzione `using` seguente all'inizio del file `CalcInput.cs`.

```c#
using Newtonsoft.Json;
```

Sostituire la classe generata con il codice seguente.

```c#
public class CalcInput
{
    [JsonProperty(PropertyName = "a")]
    public int a;

    [JsonProperty(PropertyName = "b")]
    public int b;
}
```

Fare clic con il pulsante destro del mouse su **Controller** in **Esplora soluzioni** e scegliere **Aggiungi**->**Controller**. Scegliere **Web API 2 Controller - Empty** e fare clic su **Aggiungi**. Digitare **CalcController** come nome del controller e fare clic su **Aggiungi**.

![Aggiungi controller][api-management-add-controller]

Aggiungere l'istruzione `using` seguente all'inizio del file `CalcController.cs`.

```c#
using System.IO;
using System.Web;
using APIMAADDemo.Models;
```

Sostituire la classe controller generata con il codice seguente. Questo codice implementa le operazioni `Add`, `Subtract`, `Multiply` e `Divide` dell'API Calculator di base.

```c#
[Authorize]
public class CalcController : ApiController
{
    [Route("api/add")]
    [HttpGet]
    public HttpResponseMessage GetSum([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a + b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/sub")]
    [HttpGet]
    public HttpResponseMessage GetDiff([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a - b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/mul")]
    [HttpGet]
    public HttpResponseMessage GetProduct([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a * b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/div")]
    [HttpGet]
    public HttpResponseMessage GetDiv([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a / b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }
}
```

Premere **F6** per compilare e verificare la soluzione.

## <a name="publish-the-project-to-azure"></a>Pubblicare il progetto in Azure
In questo passaggio il progetto di Visual Studio viene pubblicato in Azure. Questo passaggio del video inizia da 5:45 minuti.

Per pubblicare il progetto in Azure, fare clic con il pulsante destro del mouse sul progetto **APIMAADDemo** in Visual Studio e scegliere **Pubblica**. Mantenere le impostazioni predefinite nella finestra di dialogo **Pubblica sul Web** e fare clic su **Pubblica**.

![Pubblicazione sul Web][api-management-web-publish]

## <a name="grant-permissions-to-the-azure-ad-backend-service-application"></a>Concedere autorizzazioni all'applicazione del servizio back-end di Azure AD
Nella directory di Azure AD viene creata una nuova applicazione per il servizio back-end come parte del processo di configurazione e pubblicazione del progetto API Web. In questo passaggio del video, che inizia da 6:13 minuti, vengono concesse autorizzazioni al back-end dell'API Web.

![Applicazione][api-management-aad-backend-app]

Fare clic sul nome dell'applicazione per configurare le autorizzazioni necessarie. Passare alla scheda **Configura** e scorrere verso il basso fino alla sezione **Autorizzazioni per altre applicazioni**. Fare clic sull'elenco a discesa **Autorizzazioni applicazione** accanto a **Microsoft** **Azure Active Directory**, selezionare la casella **Lettura dati directory** e fare clic su **Salva**.

![Aggiungere autorizzazioni][api-management-aad-add-permissions]

> [!NOTE]
> Se **Microsoft** **Azure Active Directory** non è nell'elenco in Autorizzazioni per altre applicazioni, fare clic su **Aggiungi applicazione** e aggiungere la voce all'elenco.
> 
> 

Prendere nota dell' **URI ID app** che verrà usato in un passaggio successivo durante la configurazione di un'applicazione Azure AD per il portale per sviluppatori di Gestione API.

![URI ID app][api-management-aad-sso-uri]

## <a name="import-the-web-api-into-api-management"></a>Importare l'API Web in Gestione API
Le API vengono configurate dal relativo portale di pubblicazione, accessibile dal Portale di Azure. Per accedervi, fare clic su **Publisher portal** (Portale di pubblicazione) nella barra degli strumenti del servizio Gestione API. Se non è ancora stata creata un'istanza del servizio Gestione API, vedere [Creare un'istanza di Gestione API][Create an API Management service instance] nell'esercitazione [Gestire la prima API][Manage your first API].

![Portale di pubblicazione][api-management-management-console]

Le operazioni possono essere [aggiunte alle API manualmente](api-management-howto-add-operations.md)o possono essere importate. In questo video le operazioni vengono importante in formato Swagger a partire da 6:40 minuti.

Creare un file denominato `calcapi.json` con il contenuto seguente e salvarlo nel computer. Assicurarsi che l'attributo `host` punti al back-end dell'API Web. In questo esempio viene usato `"host": "apimaaddemo.azurewebsites.net"` .

```json
{
  "swagger": "2.0",
  "info": {
    "title": "Calculator",
    "description": "Arithmetics over HTTP!",
    "version": "1.0"
  },
  "host": "apimaaddemo.azurewebsites.net",
  "basePath": "/api",
  "schemes": [
    "http"
  ],
  "paths": {
    "/add?a={a}&b={b}": {
      "get": {
        "description": "Responds with a sum of two numbers.",
        "operationId": "Add two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>51</code>.",
            "required": true,
            "type": "string",
            "default": "51",
            "enum": [
              "51"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>49</code>.",
            "required": true,
            "type": "string",
            "default": "49",
            "enum": [
              "49"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/sub?a={a}&b={b}": {
      "get": {
        "description": "Responds with a difference between two numbers.",
        "operationId": "Subtract two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>100</code>.",
            "required": true,
            "type": "string",
            "default": "100",
            "enum": [
              "100"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>50</code>.",
            "required": true,
            "type": "string",
            "default": "50",
            "enum": [
              "50"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/div?a={a}&b={b}": {
      "get": {
        "description": "Responds with a quotient of two numbers.",
        "operationId": "Divide two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>100</code>.",
            "required": true,
            "type": "string",
            "default": "100",
            "enum": [
              "100"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>20</code>.",
            "required": true,
            "type": "string",
            "default": "20",
            "enum": [
              "20"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/mul?a={a}&b={b}": {
      "get": {
        "description": "Responds with a product of two numbers.",
        "operationId": "Multiply two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>20</code>.",
            "required": true,
            "type": "string",
            "default": "20",
            "enum": [
              "20"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>5</code>.",
            "required": true,
            "type": "string",
            "default": "5",
            "enum": [
              "5"
            ]
          }
        ],
        "responses": { }
      }
    }
  }
}
```

Per importare l'API Calculator, fare clic su **API** nel menu **Gestione API** sulla sinistra e quindi scegliere **Importa API**.

![Pulsante Importa API][api-management-import-api]

Per configurare l'API di calcolatrice, eseguire la procedura seguente:

1. Fare clic su **Da file**, passare al file `calculator.json` salvato e fare clic sul pulsante di opzione **Swagger**.
2. Digitare **calc** nella casella di testo **Suffisso URL API Web**.
3. Fare clic sulla casella **Products (optional)** (Prodotti - facoltativo) e scegliere **Starter**.
4. Fare clic su **Salva** per importare l'API.

![Add new API][api-management-import-new-api]

Una volta importata l'API, la pagina di riepilogo dell'API viene visualizzata nel portale di pubblicazione.

## <a name="call-the-api-unsuccessfully-from-the-developer-portal"></a>Chiamare l'API con esito negativo dal portale per sviluppatori
A questo punto, l'API è stata importata in Gestione API, ma la chiamata dal portale per sviluppatori non può ancora essere completata perché il servizio back-end è protetto con l'autenticazione di Azure AD. Questa operazione è illustrata nel video a partire da 7:40 minuti usando i passaggi seguenti.

Fare clic su **Portale per sviluppatori** sul lato in alto a destra del portale di pubblicazione.

![Portale per sviluppatori][api-management-developer-portal-menu]

Fare clic su **API** e sull'API **Calculator**.

![Portale per sviluppatori][api-management-dev-portal-apis]

Fare clic su **Prova**.

![Prova][api-management-dev-portal-try-it]

Fare clic su **Invia** e prendere nota dello stato della risposta **401 Unauthorized**.

![Invio][api-management-dev-portal-send-401]

La richiesta non è autorizzata perché l'API back-end è protetta da Azure Active Directory. Prima di poter chiamare l'API correttamente, è necessario configurare il portale per sviluppatori impostando l'autorizzazione per l'uso di OAuth 2.0 da parte degli sviluppatori. Questo processo è descritto nelle sezioni seguenti.

## <a name="register-the-developer-portal-as-an-aad-application"></a>Registrare il portale per sviluppatori come un'applicazione AAD
Il primo passaggio per configurare il portale per sviluppatori con l'autorizzazione per l'uso di OAuth 2.0 consiste nel registrare il portale come un'applicazione AAD. Nel video questa operazione è illustrata a partire da 8:27 minuti.

Passare al tenant di Azure AD dal primo passaggio di questo video, in questo esempio **APIMDemo**, e quindi alla scheda **Applicazioni**.

![Nuova applicazione][api-management-aad-new-application-devportal]

Fare clic sul pulsante **Aggiungi** per creare una nuova applicazione Azure Active Directory e scegliere **Aggiungi un'applicazione che l'organizzazione sta sviluppando**.

![Nuova applicazione][api-management-new-aad-application-menu]

Scegliere **Applicazione Web e/o API Web**, immettere un nome e fare clic sulla freccia avanti. In questo esempio viene usato **APIMDeveloperPortal** .

![Nuova applicazione][api-management-aad-new-application-devportal-1]

Per **URL accesso** immettere l'URL del servizio Gestione API e aggiungere `/signin`. In questo esempio viene usato `https://contoso5.portal.azure-api.net/signin` .

Per **URI ID app** immettere l'URL del servizio Gestione API e aggiungere alcuni caratteri univoci. Si può usare qualsiasi carattere. In questo esempio vengono usati `https://contoso5.portal.azure-api.net/dp`. Dopo aver completato la configurazione delle **Proprietà dell'app**, fare clic sul segno di spunta per creare l'applicazione.

![Nuova applicazione][api-management-aad-new-application-devportal-2]

## <a name="configure-an-api-management-oauth-20-authorization-server"></a>Configurare un server autorizzazione OAuth 2.0 in Gestione API
Il passaggio successivo consiste nel configurare un server autorizzazione OAuth 2.0 in Gestione API. Questo passaggio è illustrato nel video a partire da 9:43 minuti.

Fare clic su **Sicurezza** dal menu Gestione API a sinistra, scegliere **OAuth 2.0** e fare clic su **Add authorization** server.

![Add authorization server][api-management-add-authorization-server]

Immettere un nome ed eventualmente una descrizione nei campi **Nome** e **Descrizione**. Questi campi vengono usati per identificare il server autorizzazione OAuth 2.0 all'interno dell'istanza del servizio Gestione API corrente. In questo esempio viene usato **Authorization server demo** . In un secondo momento quando si specifica un server di OAuth 2.0 da usare per l'autenticazione per un'API, si selezionerà questo nome.

Per **Client registration page URL** immettere un valore segnaposto, ad esempio `http://localhost`.  **Client registration page URL** fa riferimento alla pagina che gli utenti possono usare per creare e configurare i propri account per i provider OAuth 2.0 che supportano la gestione degli account da parte degli utenti. In questo esempio gli utenti non creano e configurano i propri account, quindi si usa un segnaposto.

![Add authorization server][api-management-add-authorization-server-1]

Successivamente, specificare un valore per **Authorization endpoint URL** e **Token endpoint URL**.

![Serve autorizzazione][api-management-add-authorization-server-1a]

Questi valori possono essere recuperati dalla pagina **Endpoint dell'app** dell'applicazione AAD creata per il portale per sviluppatori. Per accedere agli endpoint passare alla scheda **Configura** per l'applicazione di AAD e fare clic su **Visualizza endpoint**.

![Applicazione][api-management-aad-devportal-application]

![Visualizza endpoint][api-management-aad-view-endpoints]

Copiare il valore di **Endpoint di autorizzazione OAuth 2.0** e incollarlo nella casella di testo **Authorization endpoint URL**.

![Add authorization server][api-management-add-authorization-server-2]

Copiare il valore di **Endpoint token OAuth 2.0** e incollarlo nella casella di testo **Token endpoint URL**.

![Add authorization server][api-management-add-authorization-server-2a]

Oltre a incollare il valore nell'endpoint del token, aggiungere un altro parametro del corpo denominato **resource** e come valore usare l'**URI ID App** dell'applicazione AAD per il servizio back-end creato durante la pubblicazione del progetto di Visual Studio.

![URI ID app][api-management-aad-sso-uri]

Specificare quindi le credenziali del client. Queste sono le credenziali per la risorsa a cui si vuole accedere, in questo caso il portale per gli sviluppatori.

![Credenziali del client][api-management-client-credentials]

Per ottenere il valore di **ID client** passare alla scheda **Configura** dell'applicazione AAD per il portale per gli sviluppatori e copiare il valore di **ID client**.

Per ottenere il **Segreto client** fare clic sull'elenco a discesa **Seleziona durata** nella sezione **Chiavi** e specificare un intervallo. In questo esempio viene usato 1 anno.

![ID Client][api-management-aad-client-id]

Fare clic su **Salva** per salvare la configurazione e visualizzare la chiave. 

> [!IMPORTANT]
> Annotare il valore relativo alla chiave. Una volta chiusa la finestra di configurazione di Azure Active Directory, la chiave non potrà più essere visualizzata.
> 
> 

Copiare la chiave negli Appunti, tornare al portale di pubblicazione, incollare la chiave nella casella di testo **Segreto client** e fare clic su **Salva**.

![Add authorization server][api-management-add-authorization-server-3]

Subito dopo le credenziali del client è presente una concessione del codice di autorizzazione. Copiare questo codice di autorizzazione e tornare all'applicazione del portale per sviluppatori Azure AD e incollare la concessione di autorizzazione nel campo **URL di risposta**, quindi fare di nuovo clic su **Salva**.

![URL di risposta][api-management-aad-reply-url]

Il passaggio successivo consiste nel configurare le autorizzazioni per l'applicazione AAD nel portale per sviluppatori. Fare clic su **Autorizzazioni applicazione** e selezionare la casella per **Lettura dati directory**. Fare clic su **Salva** per salvare la modifica e quindi scegliere **Aggiungi applicazione**.

![Aggiungere autorizzazioni][api-management-add-devportal-permissions]

Fare clic sull'icona di ricerca digitare **APIM** nella casella Che inizia con, selezionare **APIMAADDemo** e fare clic sul segno di spunta per salvare.

![Aggiungere autorizzazioni][api-management-aad-add-app-permissions]

Fare clic su **Autorizzazioni delegate** per **APIMAADDemo**, selezionare la casella **Accedi a APIMAADDemo** e fare clic su **Salva**. Questa impostazione consente all'applicazione nel portale per sviluppatori di accedere al servizio back-end.

![Aggiungere autorizzazioni][api-management-aad-add-delegated-permissions]

## <a name="enable-oauth-20-user-authorization-for-the-calculator-api"></a>Abilitare l'autorizzazione utente OAuth 2.0 per l'API Calculator
Dopo aver configurato il server OAuth 2.0, è possibile specificarlo nelle impostazioni di sicurezza per l'API. Questo passaggio è illustrato nel video a partire da 14:30 minuti.

Fare clic su **API** nel menu a sinistra, quindi fare clic su **Calculator** per visualizzarne e configurarne le impostazioni.

![API Calculator][api-management-calc-api]

Passare alla scheda **Sicurezza**, selezionare la casella di controllo **OAuth 2.0**, selezionare il server di autorizzazione specifico dall'elenco a discesa **Authorization server** e fare clic su **Salva**.

![API Calculator][api-management-enable-aad-calculator]

## <a name="successfully-call-the-calculator-api-from-the-developer-portal"></a>Chiamare correttamente l'API Calculator dal portale per sviluppatori
Dopo avere configurato l'autorizzazione OAuth 2.0 nell'API, le relative operazioni possono essere chiamate correttamente dal centro per sviluppatori. Questo passaggio è illustrato nel video a partire da 15:00 minuti.

Tornare all'operazione **Aggiungere due Integer** del servizio calcolatrice nel portale per sviluppatori e fare clic su **Prova**. Si noti il nuovo elemento nella sezione **Autorizzazione** corrispondente al server autorizzazione appena aggiunto.

![API Calculator][api-management-calc-authorization-server]

Selezionare **Authorization code** dall'elenco a discesa dell'autorizzazione e immettere le credenziali dell'account da usare. Se si è già eseguito l'accesso con l'account, è possibile che non venga richiesto.

![API Calculator][api-management-devportal-authorization-code]

Fare clic su **Invia** e prendere nota del valore **200 OK** in **Stato della risposta** e dei risultati dell'operazione nel contenuto della risposta.

![API Calculator][api-management-devportal-response]

## <a name="configure-a-desktop-application-to-call-the-api"></a>Configurare un'applicazione desktop per chiamare l'API
La procedura successiva del video inizia da 16:30 minuti e configura una semplice applicazione desktop per chiamare l'API. Il primo passaggio consiste nel registrare l'applicazione desktop in Azure AD e nel concedere l'accesso alla directory e al servizio back-end. Da 18:25 minuti è presente una dimostrazione dell'applicazione desktop che chiama un'operazione nell'API Calculator.

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configurare criteri di convalida JWT per preautorizzare le richieste
L'ultima procedura dei video inizia dal minuto 20:48 e illustra come usare i criteri di [convalida JWT](https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT) per preautorizzare le richieste con la convalida dei token di accesso di ogni richiesta in ingresso. Se la richiesta non viene convalidata dai criteri di convalida JWT, viene bloccata da Gestione API e non viene passata al back-end.

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/DemoAPIM.onmicrosoft.com/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>https://DemoAPIM.NOTonmicrosoft.com/APIMAADDemo</value>
        </claim>
    </required-claims>
</validate-jwt>
```

Per un'altra dimostrazione relativa alla configurazione e all'uso di questi criteri, vedere l' [episodio 177 di Cloud Cover su altre funzionalità di Gestione API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e passare direttamente al minuto 13:50. Passare a 15:00 minuti per vedere i criteri configurati nell'editor dei criteri e quindi a 18:50 minuti per una dimostrazione della chiamata di un'operazione dal portale per sviluppatori, con e senza il token di autorizzazione richiesto.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sui [video](https://azure.microsoft.com/documentation/videos/index/?services=api-management) relativi a Gestione API.
* Per altri metodi di protezione del servizio back-end, vedere [Come proteggere i servizi back-end usando l'autenticazione reciproca dei certificati](api-management-howto-mutual-certificates.md).

[api-management-management-console]: ./media/api-management-howto-protect-backend-with-aad/api-management-management-console.png

[api-management-import-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-new-api.png
[api-management-create-aad-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad-menu.png
[api-management-create-aad]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad.png
[api-management-new-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-web-app.png
[api-management-new-project]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project.png
[api-management-new-project-cloud]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project-cloud.png
[api-management-change-authentication]: ./media/api-management-howto-protect-backend-with-aad/api-management-change-authentication.png
[api-management-sign-in-vidual-studio]: ./media/api-management-howto-protect-backend-with-aad/api-management-sign-in-vidual-studio.png
[api-management-configure-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-configure-web-app.png
[api-management-aad-domains]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-domains.png
[api-management-add-controller]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-controller.png
[api-management-web-publish]: ./media/api-management-howto-protect-backend-with-aad/api-management-web-publish.png
[api-management-aad-backend-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-backend-app.png
[api-management-aad-add-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-permissions.png
[api-management-developer-portal-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-developer-portal-menu.png
[api-management-dev-portal-apis]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-apis.png
[api-management-dev-portal-try-it]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-try-it.png
[api-management-dev-portal-send-401]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-send-401.png
[api-management-aad-new-application-devportal]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal.png
[api-management-aad-new-application-devportal-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-1.png
[api-management-aad-new-application-devportal-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-2.png
[api-management-aad-devportal-application]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-devportal-application.png
[api-management-add-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server.png
[api-management-aad-sso-uri]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-sso-uri.png
[api-management-aad-view-endpoints]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-view-endpoints.png
[api-management-aad-client-id]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-client-id.png
[api-management-add-authorization-server-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1.png
[api-management-add-authorization-server-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2.png
[api-management-add-authorization-server-2a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2a.png
[api-management-add-authorization-server-3]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-3.png
[api-management-aad-reply-url]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-reply-url.png
[api-management-add-devportal-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-devportal-permissions.png
[api-management-aad-add-app-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-app-permissions.png
[api-management-aad-add-delegated-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-delegated-permissions.png
[api-management-calc-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-api.png
[api-management-enable-aad-calculator]: ./media/api-management-howto-protect-backend-with-aad/api-management-enable-aad-calculator.png
[api-management-devportal-authorization-code]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-authorization-code.png
[api-management-devportal-response]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-response.png
[api-management-calc-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-authorization-server.png
[api-management-add-authorization-server-1a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1a.png
[api-management-client-credentials]: ./media/api-management-howto-protect-backend-with-aad/api-management-client-credentials.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-aad-application-menu.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Manage your first API]: import-and-publish.md
