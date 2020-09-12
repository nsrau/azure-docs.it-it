---
title: Scambi di attestazioni API REST nel criterio personalizzato B2C
titleSuffix: Azure AD B2C
description: Introduzione alla creazione di un percorso utente Azure AD B2C che interagisce con i servizi RESTful.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bc2b72779460c2b7e3999204ace50ca57388b9a2
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89594187"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>Integrare scambi di attestazioni API REST nel criterio personalizzato di Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

L'Identity Experience Framework, che è alla base di Azure Active Directory B2C (Azure AD B2C), è in grado di eseguire un'integrazione con API RESTful all'interno di un percorso utente. Questo articolo illustra come creare un percorso utente che interagisce con un servizio RESTful usando un [profilo tecnico RESTful](restful-technical-profile.md).

Usando Azure AD B2C, è possibile aggiungere la logica di business a un percorso utente chiamando il servizio RESTful. L'Identity Experience Framework può inviare e ricevere dati dal servizio RESTful per lo scambio di attestazioni. Ad esempio, è possibile:

- **Convalidare i dati di input utente**. È possibile ad esempio verificare che l'indirizzo di posta elettronica indicato dall'utente sia presente nel database del cliente e, in caso contrario, presentare un errore.
- **Elaborare le attestazioni**. Se un utente immette il nome in lettere tutte minuscole o tutte maiuscole, l'API REST può formattare il nome con solo la prima lettera maiuscola e restituirlo ad Azure AD B2C.
- **Arricchire i dati utente eseguendo un'integrazione più approfondita con applicazioni line-of-business**. Il servizio RESTful può ricevere l'indirizzo di posta elettronica dell'utente, eseguire una query sul database del cliente e restituire il numero del programma fedeltà dell'utente ad Azure AD B2C. Le attestazioni restituite possono essere archiviate nell'account Azure AD dell'utente, valutato nei passaggi di orchestrazione successivi o incluso nel token di accesso.
- **Eseguire la logica di business personalizzata**. È possibile inviare notifiche push, aggiornare i database aziendali, eseguire un processo di migrazione utente, gestire le autorizzazioni, controllare i database ed eseguire qualsiasi altro flusso di lavoro.

![Diagramma di uno scambio di attestazioni del servizio RESTful](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

> [!NOTE]
> Se è presente una risposta lenta o nessuna risposta dal servizio RESTful a Azure AD B2C, il timeout è di 30 secondi e il numero di tentativi è di 2 volte (ovvero sono presenti 3 tentativi in totale). Le impostazioni per il numero di tentativi e il timeout non sono attualmente configurabili.

## <a name="calling-a-restful-service"></a>Chiamata a un servizio RESTful

L'interazione include uno scambio di attestazioni di informazioni tra le attestazioni API REST e Azure AD B2C. È possibile progettare l'integrazione con i servizi RESTful nei modi seguenti:

- **Profilo tecnico di convalida**. La chiamata al servizio RESTful si verifica all'interno di un [profilo tecnico di convalida](validation-technical-profile.md) del [profilo tecnico autocertificato](self-asserted-technical-profile.md) specificato o di un [controllo di visualizzazione della verifica](display-control-verification.md) di un [controllo di visualizzazione](display-controls.md). Il profilo tecnico convalida i dati specificati dall'utente prima che il percorso utente proceda. Il profilo tecnico di convalida consente di eseguire queste operazioni:

  - Inviare attestazioni all'API REST.
  - Convalidare le attestazioni e generare messaggi di errore personalizzati che vengono visualizzati all'utente.
  - Restituire attestazioni dall'API REST ai passaggi successivi dell'orchestrazione.

- **Scambio di attestazioni**. Uno scambio di attestazioni dirette può essere configurato chiamando un profilo tecnico dell'API REST direttamente da un passaggio di orchestrazione di un [percorso utente](userjourneys.md). Questa definizione è limitata alle operazioni seguenti:

  - Inviare attestazioni all'API REST.
  - Convalidare le attestazioni e generare messaggi di errore personalizzati che vengono restituiti all'applicazione.
  - Restituire attestazioni dall'API REST ai passaggi successivi dell'orchestrazione.

È possibile aggiungere una chiamata API REST in qualsiasi passaggio del percorso utente definito da un criterio personalizzato. Ad esempio, è possibile chiamare un'API REST:

- Durante l'accesso, subito prima che Azure AD B2C convalidi le credenziali.
- Subito dopo l'accesso.
- Prima che Azure AD B2C crei un nuovo account nella directory.
- Dopo che Azure AD B2C ha creato un nuovo account nella directory.
- Prima che Azure AD B2C rilasci un token di accesso.

![Raccolta del profilo tecnico di convalida](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>Invio di dati

Nel [profilo tecnico RESTful](restful-technical-profile.md), l'elemento `InputClaims` contiene un elenco di attestazioni da inviare al servizio RESTful. È possibile mappare il nome dell'attestazione al nome definito nel servizio RESTful, impostare un valore predefinito e usare [resolver di attestazioni](claim-resolver-overview.md).

È possibile configurare la modalità di invio delle attestazioni di input al provider di attestazioni RESTful tramite l'attributo SendClaimsIn. I valori possibili sono:

- **Corpo**, inviato nel corpo della richiesta HTTP POST in formato JSON.
- **Modulo**, inviato nel corpo della richiesta HTTP POST nel formato valore di chiave e commerciale "&" separata.
- **Intestazione**, inviato nell'intestazione della richiesta HTTP GET.
- **QueryString**, inviato nella stringa di query della richiesta HTTP GET.

Quando viene configurata l'opzione **Corpo**, il profilo tecnico dell'API REST consente di inviare un payload JSON complesso a un endpoint. Per altre informazioni, vedere [Inviare un payload JSON](restful-technical-profile.md#send-a-json-payload).

## <a name="receiving-data"></a>Ricezione di dati

L'elemento `OutputClaims` del [profilo tecnico RESTful](restful-technical-profile.md) contiene un elenco di attestazioni restituite dall'API REST. Potrebbe essere necessario eseguire il mapping del nome dell'attestazione definito nei criteri per il nome definito nell'API REST. È anche possibile includere attestazioni che non sono restituite dal provider di identità API REST purché siano impostate sull'attributo DefaultValue.

Le attestazioni di output analizzate dal provider di attestazioni RESTful si aspettano sempre di analizzare una risposta del corpo JSON Flat, ad esempio:

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

Le attestazioni di output saranno simili all'attestazione seguente:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

Per analizzare una risposta del corpo JSON annidata, impostare i metadati ResolveJsonPathsInJsonTokens su true. Nell'attestazione di output impostare PartnerClaimType sull'elemento percorso JSON di cui si vuole eseguire l'output.

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


Le attestazioni di output saranno simili all'attestazione seguente:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts[0].person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts[0].person.emails[0].email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts[0].person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

È necessario proteggere l'endpoint dell'API REST in modo che solo i client autenticati possano comunicare con esso. L'API REST deve usare un endpoint HTTPS. Impostare i metadati AuthenticationType su uno dei metodi di autenticazione seguenti:

- Il **certificato client** limita l'accesso usando l'autenticazione del certificato client. Solo i servizi con i certificati appropriati possono accedere all'API. Il certificato client viene archiviato in una chiave dei criteri Azure AD B2C. Informazioni su come [proteggere il servizio RESTful usando certificati client](secure-rest-api.md#https-client-certificate-authentication).
- L'**autenticazione di base** protegge l'API REST con l'autenticazione di base HTTP. Solo gli utenti verificati, tra cui Azure AD B2C, possono accedere all'API. Il nome utente e la password vengono archiviati nelle chiavi dei criteri Azure AD B2C. Informazioni su come [proteggere i servizi RESTful usando l'autenticazione di base HTTP](secure-rest-api.md#http-basic-authentication).
- Il **token di connessione** limita l'accesso usando un token di accesso OAuth2 client. Il token di accesso viene archiviato in una chiave dei criteri Azure AD B2C. Informazioni su come [proteggere il servizio RESTful usando il token di connessione](secure-rest-api.md#oauth2-bearer-authentication).

## <a name="rest-api-platform"></a>Piattaforma API REST
L'API REST può essere basata su qualsiasi piattaforma e scritta in qualsiasi linguaggio di programmazione, purché sia protetta e possa inviare e ricevere attestazioni come specificato nel [profilo tecnico RESTful](restful-technical-profile.md).

## <a name="localize-the-rest-api"></a>Localizzare l'API REST
In un profilo tecnico RESTful, è possibile inviare il linguaggio o le impostazioni locali della sessione corrente e, se necessario, generare un messaggio di errore localizzato. Usando il [resolver delle attestazioni](claim-resolver-overview.md), è possibile inviare un'attestazione contestuale, ad esempio la lingua dell'utente. Nell'esempio seguente viene illustrato un profilo tecnico RESTful che dimostra questo scenario.

```xml
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

## <a name="handling-error-messages"></a>Gestione dei messaggi di errore

L'API REST può restituire un messaggio di errore, ad esempio "Utente nel sistema CRM non trovato". Quando si verifica un errore, l'API REST deve restituire un messaggio di errore HTTP 409 (codice di stato risposta di conflitto). Per altre informazioni, vedere il [profilo tecnico RESTful](restful-technical-profile.md#returning-validation-error-message).

Questa operazione può essere eseguita solo chiamando un profilo tecnico dell'API REST da un profilo tecnico di convalida. Ciò consente all'utente di correggere i dati nella pagina ed eseguire nuovamente la convalida al momento dell'invio della pagina.

È necessaria una risposta HTTP 409 per impedire l'elaborazione di tutti i profili tecnici di convalida successivi in questo passaggio dell'orchestrazione.

Se si fa riferimento a un profilo tecnico dell'API REST direttamente da un percorso utente, l'utente viene reindirizzato di nuovo all'applicazione basata su attestazioni con il messaggio di errore pertinente.

## <a name="publishing-your-rest-api"></a>Pubblicazione dell'API REST

La richiesta al servizio API REST deriva da server Azure AD B2C. Il servizio API REST deve essere pubblicato in un endpoint HTTPS accessibile pubblicamente. Le chiamate all'API REST arriveranno da un indirizzo IP del data center Azure.

Progettare il servizio API REST e i componenti sottostanti (ad esempio, il database e file system) in modo che abbiano una disponibilità elevata.

## <a name="next-steps"></a>Passaggi successivi

Per esempi relativi all'uso di un profilo tecnico RESTful, vedere gli articoli seguenti:

- [Procedura dettagliata: Integrare scambi di attestazioni API REST nel percorso utente di Azure AD B2C come convalida dell'input utente](custom-policy-rest-api-claims-validation.md)
- [Procedura dettagliata: Aggiungere scambi di attestazioni API REST ai criteri personalizzati in Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Proteggere i servizi API REST](secure-rest-api.md)
- [Informazioni di riferimento: Profilo tecnico RESTful](restful-technical-profile.md)
