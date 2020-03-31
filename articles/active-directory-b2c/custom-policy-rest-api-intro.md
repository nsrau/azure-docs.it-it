---
title: Scambi di attestazioni dell'API REST nei criteri personalizzati B2CREST API claims exchanges in B2C custom policy
titleSuffix: Azure AD B2C
description: Introduzione alla creazione di un percorso utente B2C di Azure AD che interagisce con i servizi RESTful.An introduction to creating an Azure AD B2C user journey that interacts with RESTful services.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6a6cc8e5931f3e29c242f51a6e062441953228ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337415"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>Integrare gli scambi di attestazioni dell'API REST nei criteri personalizzati di Azure AD

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Identity Experience Framework, che è alla base di Azure Active Directory B2C (Azure AD B2C), può integrarsi con le API RESTful all'interno di un percorso utente. In questo articolo viene illustrato come creare un percorso utente che interagisce con un servizio RESTful utilizzando un [profilo tecnico RESTful](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster).

Usando Azure AD B2C, è possibile aggiungere la propria logica di business a un percorso utente chiamando il proprio servizio RESTful.Using Azure AD B2C, you can add your own business logic to a user journey by calling your own RESTful service. Identity Experience Framework può inviare e ricevere dati dal servizio RESTful per lo scambio di attestazioni. Ad esempio, è possibile:

- **Convalidare i dati di input dell'utente**. Ad esempio, è possibile verificare che l'indirizzo di posta elettronica fornito dall'utente esista nel database del cliente e, in caso contrario, presentare un errore.
- **Elaborare le attestazioni**. Se un utente immette il proprio nome in lettere minuscole o tutte maiuscole, l'API REST può formattare il nome solo con la prima lettera maiuscola e restituirlo ad Azure AD B2C.
- **Arricchisci i dati degli utenti integrandosi ulteriormente con le applicazioni line-of-business aziendali.** Il servizio RESTful può ricevere l'indirizzo di posta elettronica dell'utente, eseguire una query sul database del cliente e restituire il numero del programma fedeltà dell'utente ad Azure AD B2C. Le attestazioni restituite possono quindi essere archiviate nell'account Azure AD dell'utente, valutate nei passaggi di orchestrazione successivi o incluse nel token di accesso.
- Eseguire la **logica di business personalizzata**. È possibile inviare notifiche push, aggiornare i database aziendali, eseguire un processo di migrazione degli utenti, gestire le autorizzazioni, controllare i database ed eseguire qualsiasi altro flusso di lavoro.

![Diagramma di uno scambio di attestazioni di servizio RESTful](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

## <a name="calling-a-restful-service"></a>Chiamata di un servizio RESTfulCalling a RESTful service

L'interazione include uno scambio di attestazioni di informazioni tra le attestazioni dell'API REST e Azure AD B2C. È possibile progettare l'integrazione con i servizi RESTful nei modi seguenti:

- **Profilo tecnico di convalida**. La chiamata al servizio RESTful avviene all'interno di un [profilo tecnico](validation-technical-profile.md) di convalida del profilo [tecnico auto-asserito](self-asserted-technical-profile.md)specificato o di un controllo di visualizzazione di [verifica](display-control-verification.md) di un controllo [di visualizzazione.](display-controls.md) Il profilo tecnico convalida i dati specificati dall'utente prima che il percorso utente proceda. Il profilo tecnico di convalida consente di eseguire queste operazioni:

  - Inviare attestazioni all'API REST.
  - Convalidare le attestazioni e generare messaggi di errore personalizzati che vengono visualizzati all'utente.
  - Inviare attestazioni dall'API REST ai passaggi di orchestrazione successivi.

- **Scambio di reclami**. Uno scambio diretto di attestazioni può essere configurato chiamando un profilo tecnico dell'API REST direttamente da un passaggio di orchestrazione di un [percorso utente.](userjourneys.md) Questa definizione è limitata alle operazioni seguenti:

  - Inviare attestazioni all'API REST.
  - Convalidare le attestazioni e generare messaggi di errore personalizzati restituiti all'applicazione.
  - Inviare attestazioni dall'API REST ai passaggi di orchestrazione successivi.

È possibile aggiungere una chiamata API REST in qualsiasi passaggio del percorso utente definito da un criterio personalizzato. Ad esempio, è possibile chiamare un'API REST:

- Durante l'accesso, appena prima che Azure AD B2C convalidi le credenziali.
- Immediatamente dopo l'accesso.
- Prima che Azure AD B2C crei un nuovo account nella directory.
- Dopo che Azure AD B2C crea un nuovo account nella directory.
- Prima che Azure AD B2C emetta un token di accesso.

![Raccolta dei profili tecnici di convalida](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>Invio di dati

Nel [profilo tecnico RESTful](restful-technical-profile.md), l'elemento `InputClaims` contiene un elenco di attestazioni da inviare al servizio RESTful. È possibile eseguire il mapping del nome dell'attestazione al nome definito nel servizio RESTful, impostare un valore predefinito e utilizzare [resolver di attestazioni.](claim-resolver-overview.md)

È possibile configurare la modalità di invio delle attestazioni di input al provider di attestazioni RESTful tramite l'attributo SendClaimsIn.You can configure how the input claims are sent to the RESTful claims provider by using the SendClaimsIn attribute. I valori possibili sono:

- **Corpo**, inviato nel corpo della richiesta HTTP POST in formato JSON.
- **Form**, inviato nel corpo della richiesta HTTP POST in un formato di valore di chiave separati '&' e commerciale.
- **Intestazione**, inviata nell'intestazione della richiesta HTTP GET.
- **QueryString**, inviato nella stringa di query della richiesta HTTP GET.

Quando l'opzione **Corpo** è configurata, il profilo tecnico dell'API REST consente di inviare un payload JSON complesso a un endpoint. Per altre informazioni, vedere [Inviare un payload JSON.](restful-technical-profile.md#send-a-json-payload)

## <a name="receiving-data"></a>Ricezione di dati

L'elemento `OutputClaims` del [profilo tecnico RESTful](restful-technical-profile.md) contiene un elenco di attestazioni restituite dall'API REST. Potrebbe essere necessario eseguire il mapping del nome dell'attestazione definito nei criteri per il nome definito nell'API REST. È anche possibile includere attestazioni non restituite dal provider di identità dell'API REST, purché si imposti l'attributo DefaultValue.You can also include claims that aren't returned by the REST API identity provider, as long as you set the DefaultValue attribute.

Le attestazioni di output analizzate dal provider di attestazioni RESTful prevedono sempre di analizzare una risposta flat JSON Body, ad esempio:The output claims parsed by the RESTful claims provider always expect to parse a flat JSON Body response, such as:

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

Le attestazioni di output dovrebbero essere simili alle seguenti:The output claims should look like the following:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

Per analizzare una risposta JSON Body annidata, impostare i metadati ResolveJsonPathsInJsonTokens su true. Nell'attestazione di output impostare PartnerClaimType sull'elemento di percorso JSON che si vuole restituire.

```json
"contacts": [
  {
    "id": "MAINCONTACT_1",
    "person": {
      "name": "Emily Smith",
      "loyaltyNumber":  1234,
      "emails": [
        {
          "id": "EMAIL_1",
          "type": "WORK",
          "email": "email@domain.com"
        }
      ]
    }
  }
],
```


Le attestazioni di output dovrebbero essere simili alla seguente:The output claims should look like following:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts.0.person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts.0.person.emails.0.email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts.0.person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>Considerazioni sulla sicurezza

È necessario proteggere l'endpoint dell'API REST in modo che solo i client autenticati possano comunicare con esso. L'API REST deve usare un endpoint HTTPS. Impostare i metadati AuthenticationType su uno dei seguenti metodi di autenticazione:

- **Il certificato client** limita l'accesso utilizzando l'autenticazione del certificato client. Solo i servizi con i certificati appropriati possono accedere all'API. Archiviare il certificato client in una chiave dei criteri B2C di Azure AD. Ulteriori informazioni su come [proteggere il servizio RESTful utilizzando](secure-rest-api.md#https-client-certificate-authentication)i certificati client .
- **Basic** protegge l'API REST con l'autenticazione di base HTTP. Solo gli utenti verificati, tra cui Azure AD B2C, possono accedere all'API. Il nome utente e la password vengono archiviati nelle chiavi dei criteri B2C di Azure AD. Informazioni su come [proteggere i servizi RESTful utilizzando l'autenticazione di base HTTP.](secure-rest-api.md#http-basic-authentication)
- **Bearer** limita l'accesso utilizzando un token di accesso OAuth2 client. Il token di accesso viene archiviato in una chiave dei criteri B2C di Azure AD. Ulteriori informazioni su come [proteggere il servizio RESTful utilizzando il token Bearer](secure-rest-api.md#oauth2-bearer-authentication).

## <a name="rest-api-platform"></a>Piattaforma API REST
L'API REST può essere basata su qualsiasi piattaforma e scritta in qualsiasi linguaggio di programmazione, purché sia sicura e possa inviare e ricevere attestazioni come specificato nel [profilo tecnico RESTful](restful-technical-profile.md).

## <a name="localize-the-rest-api"></a>Localizzare l'API RESTLocalize the REST API
In un profilo tecnico RESTful, è possibile inviare la lingua/impostazioni locali della sessione corrente e, se necessario, generare un messaggio di errore localizzato. Utilizzando il [resolver di attestazioni](claim-resolver-overview.md), è possibile inviare un'attestazione contestuale, ad esempio la lingua dell'utente. Nell'esempio seguente viene illustrato un profilo tecnico RESTful che illustra questo scenario.

```XML
<TechnicalProfile Id="REST-ValidateUserData">
  <DisplayName>Validate user input data</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="handling-error-messages"></a>Gestione dei messaggi di erroreHandling error messages

Potrebbe essere necessario che l'API REST debba restituire un messaggio di errore, ad esempio "L'utente non è stato trovato nel sistema CRM". Se si verifica un errore, l'API REST deve restituire un messaggio di errore HTTP 409 (codice di stato della risposta di conflitto). Per ulteriori informazioni, vedere il [profilo tecnico RESTful](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster&anchor=returning-error-message).

Ciò può essere ottenuto solo chiamando un profilo tecnico dell'API REST da un profilo tecnico di convalida. Ciò consente all'utente di correggere i dati nella pagina ed eseguire nuovamente la convalida all'invio della pagina.

È necessaria una risposta HTTP 409 per impedire l'elaborazione di eventuali profili tecnici di convalida successivi all'interno di questo passaggio di orchestrazione.

Se si fa riferimento a un profilo tecnico dell'API REST direttamente da un percorso utente, l'utente viene reindirizzato all'applicazione relying party con il messaggio di errore pertinente.

## <a name="publishing-your-rest-api"></a>Pubblicazione dell'API RESTPublishing your REST API

La richiesta al servizio API REST proviene dai server B2C di Azure AD. Il servizio API REST deve essere pubblicato in un endpoint HTTPS accessibile pubblicamente. Le chiamate all'API REST arriveranno da un indirizzo IP del data center di Azure.The REST API calls will arrive from an Azure data center IP address.

Progettare il servizio API REST e i relativi componenti sottostanti (ad esempio il database e il file system) in modo che siano a disponibilità elevata.

## <a name="next-steps"></a>Passaggi successivi

Vedere i seguenti articoli per esempi di utilizzo di un profilo tecnico RESTful:

- [Procedura dettagliata: Integrare gli scambi di attestazioni dell'API REST nel percorso utente B2C di Azure AD come convalida dell'input dell'utenteWalkthrough: Integrate REST API claims exchanges in your Azure AD B2C user journey as validation of user input](custom-policy-rest-api-claims-validation.md)
- [Procedura dettagliata: Aggiungere scambi di attestazioni DELL'API REST ai criteri personalizzati in Azure Active Directory B2CWalkthrough: Add REST API claims exchanges to custom policies in Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Proteggere i servizi dell'API RESTSecure your REST API services](secure-rest-api.md)
- [Riferimento: RESTful profilo tecnico](restful-technical-profile.md)