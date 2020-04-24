---
title: Scambi di attestazioni API REST nel criterio personalizzato B2C
titleSuffix: Azure AD B2C
description: Introduzione alla creazione di un percorso utente Azure AD B2C che interagisce con i servizi RESTful.
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
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>Integrare scambi di attestazioni API REST nei criteri personalizzati Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Il Framework dell'esperienza di identità, che è sottoAzure Active Directory B2C (Azure AD B2C), può integrarsi con le API RESTful in un percorso utente. Questo articolo illustra come creare un percorso utente che interagisce con un servizio RESTful usando un [profilo tecnico RESTful](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster).

Con Azure AD B2C è possibile aggiungere la propria logica di business a un percorso utente chiamando il servizio RESTful. Il Framework dell'esperienza di identità può inviare e ricevere dati dal servizio RESTful per lo scambio di attestazioni. Ad esempio, è possibile:

- **Convalidare i dati di input dell'utente**. Ad esempio, è possibile verificare che l'indirizzo di posta elettronica fornito dall'utente esista nel database del cliente e, in caso contrario, presentare un errore.
- **Elaborare le attestazioni**. Se un utente immette il nome in lettere minuscole o maiuscole, l'API REST può formattare il nome solo con la prima lettera maiuscola e restituirla a Azure AD B2C.
- **Arricchire i dati degli utenti grazie all'integrazione con le applicazioni line-of-business aziendali**. Il servizio RESTful può ricevere l'indirizzo di posta elettronica dell'utente, eseguire una query sul database del cliente e restituire il numero del programma fedeltà dell'utente ad Azure AD B2C. Quindi, le attestazioni restituite possono essere archiviate nell'account di Azure AD dell'utente, valutato nei passaggi di orchestrazione successivi o inclusi nel token di accesso.
- **Eseguire la logica di business personalizzata**. È possibile inviare notifiche push, aggiornare i database aziendali, eseguire un processo di migrazione utente, gestire le autorizzazioni, controllare i database ed eseguire altri flussi di lavoro.

![Diagramma di uno scambio di attestazioni del servizio RESTful](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

## <a name="calling-a-restful-service"></a>Chiamata a un servizio RESTful

L'interazione include uno scambio di attestazioni di informazioni tra le attestazioni API REST e Azure AD B2C. È possibile progettare l'integrazione con i servizi RESTful nei modi seguenti:

- **Profilo tecnico di convalida**. La chiamata al servizio RESTful si verifica all'interno di un [profilo tecnico di convalida](validation-technical-profile.md) del [profilo tecnico autocertificato](self-asserted-technical-profile.md)specificato o di un [controllo](display-control-verification.md) di visualizzazione della verifica di un [controllo di visualizzazione](display-controls.md). Il profilo tecnico convalida i dati specificati dall'utente prima che il percorso utente proceda. Il profilo tecnico di convalida consente di eseguire queste operazioni:

  - Inviare attestazioni all'API REST.
  - Convalidare le attestazioni e generare messaggi di errore personalizzati che vengono visualizzati all'utente.
  - Restituire attestazioni dall'API REST ai passaggi successivi dell'orchestrazione.

- **Scambio di attestazioni**. Uno scambio di attestazioni dirette può essere configurato chiamando un profilo tecnico dell'API REST direttamente da un passaggio di orchestrazione di un [percorso utente](userjourneys.md). Questa definizione è limitata alle operazioni seguenti:

  - Inviare attestazioni all'API REST.
  - Convalidare le attestazioni e generare messaggi di errore personalizzati che vengono restituiti all'applicazione.
  - Restituire attestazioni dall'API REST ai passaggi successivi dell'orchestrazione.

È possibile aggiungere una chiamata API REST in qualsiasi passaggio del percorso utente definito da un criterio personalizzato. Ad esempio, è possibile chiamare un'API REST:

- Durante l'accesso, appena prima Azure AD B2C convalida le credenziali.
- Subito dopo l'accesso.
- Prima che Azure AD B2C crei un nuovo account nella directory.
- Dopo che Azure AD B2C creato un nuovo account nella directory.
- Prima di Azure AD B2C rilascia un token di accesso.

![Raccolta profilo tecnico di convalida](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>Invio di dati

Nel [profilo tecnico RESTful](restful-technical-profile.md), l' `InputClaims` elemento contiene un elenco di attestazioni da inviare al servizio RESTful. È possibile mappare il nome dell'attestazione al nome definito nel servizio RESTful, impostare un valore predefinito e usare i [resolver di attestazioni](claim-resolver-overview.md).

È possibile configurare la modalità di invio delle attestazioni di input al provider di attestazioni RESTful tramite l'attributo SendClaimsIn. I valori possibili sono:

- **Corpo**, inviato nel corpo della richiesta HTTP post in formato JSON.
- **Form**, inviato nel corpo della richiesta HTTP post in un formato di valore di chiave separato e commerciale "&".
- **Intestazione**, inviata nell'intestazione della richiesta HTTP Get.
- **QueryString**, inviato nella stringa di query della richiesta HTTP Get.

Quando viene configurata l'opzione **Body** , il profilo tecnico dell'API REST consente di inviare un payload JSON complesso a un endpoint. Per altre informazioni, vedere [inviare un payload JSON](restful-technical-profile.md#send-a-json-payload).

## <a name="receiving-data"></a>Ricezione di dati

L' `OutputClaims` elemento del [profilo tecnico RESTful](restful-technical-profile.md) contiene un elenco di attestazioni restituite dall'API REST. Potrebbe essere necessario eseguire il mapping del nome dell'attestazione definito nei criteri per il nome definito nell'API REST. È anche possibile includere le attestazioni che non vengono restituite dal provider di identità dell'API REST, purché sia stato impostato l'attributo DefaultValue.

Le attestazioni di output analizzate dal provider di attestazioni RESTful si aspettano sempre di analizzare una risposta del corpo JSON Flat, ad esempio:

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

Le attestazioni di output dovrebbero avere un aspetto simile al seguente:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

Per analizzare una risposta del corpo JSON annidata, impostare i metadati ResolveJsonPathsInJsonTokens su true. Nell'attestazione di output impostare PartnerClaimType sull'elemento Path JSON di cui si vuole eseguire l'output.

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


Le attestazioni di output dovrebbero avere un aspetto simile al seguente:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts.0.person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts.0.person.emails.0.email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts.0.person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>Considerazioni sulla sicurezza

È necessario proteggere l'endpoint dell'API REST in modo che solo i client autenticati possano comunicare con esso. L'API REST deve usare un endpoint HTTPS. Impostare i metadati AuthenticationType su uno dei metodi di autenticazione seguenti:

- Il **certificato client** limita l'accesso usando l'autenticazione del certificato client. Solo i servizi con i certificati appropriati possono accedere all'API. Il certificato client viene archiviato in una chiave di criteri Azure AD B2C. Altre informazioni su come [proteggere il servizio RESTful usando i certificati client](secure-rest-api.md#https-client-certificate-authentication).
- **Basic** protegge l'API REST con l'autenticazione di base http. Solo gli utenti verificati, tra cui Azure AD B2C, possono accedere all'API. Il nome utente e la password vengono archiviati in Azure AD B2C chiavi dei criteri. Informazioni su come [proteggere i servizi RESTful usando l'autenticazione di base http](secure-rest-api.md#http-basic-authentication).
- Il **titolare** limita l'accesso usando un token di accesso OAuth2 client. Il token di accesso viene archiviato in un Azure AD B2C chiave dei criteri. Altre informazioni su come [proteggere il servizio RESTful usando il token di porta](secure-rest-api.md#oauth2-bearer-authentication).

## <a name="rest-api-platform"></a>Piattaforma API REST
L'API REST può essere basata su qualsiasi piattaforma e scritta in qualsiasi linguaggio di programmazione, purché sia protetta e possa inviare e ricevere attestazioni come specificato in [profilo tecnico RESTful](restful-technical-profile.md).

## <a name="localize-the-rest-api"></a>Localizzare l'API REST
In un profilo tecnico RESTful, è possibile inviare la lingua o le impostazioni locali della sessione corrente e, se necessario, generare un messaggio di errore localizzato. Utilizzando il sistema di [risoluzione delle attestazioni](claim-resolver-overview.md), è possibile inviare un'attestazione contestuale, ad esempio la lingua dell'utente. Nell'esempio seguente viene illustrato un profilo tecnico RESTful che illustra questo scenario.

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

## <a name="handling-error-messages"></a>Gestione dei messaggi di errore

L'API REST potrebbe dover restituire un messaggio di errore, ad esempio "l'utente non è stato trovato nel sistema CRM". Se si verifica un errore, l'API REST deve restituire un messaggio di errore HTTP 409 (codice di stato della risposta in conflitto). Per ulteriori informazioni, vedere il [profilo tecnico RESTful](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster&anchor=returning-error-message).

Questa operazione può essere eseguita solo chiamando un profilo tecnico dell'API REST da un profilo tecnico di convalida. Ciò consente all'utente di correggere i dati nella pagina ed eseguire nuovamente la convalida al momento dell'invio della pagina.

È necessaria una risposta HTTP 409 per impedire l'elaborazione di tutti i profili tecnici di convalida successivi in questo passaggio dell'orchestrazione.

Se si fa riferimento a un profilo tecnico dell'API REST direttamente da un percorso utente, l'utente viene reindirizzato di nuovo all'applicazione relying party con il messaggio di errore pertinente.

## <a name="publishing-your-rest-api"></a>Pubblicazione dell'API REST

La richiesta al servizio API REST deriva da server Azure AD B2C. Il servizio API REST deve essere pubblicato in un endpoint HTTPS accessibile pubblicamente. Le chiamate all'API REST arriveranno da un indirizzo IP di Azure data center.

Progettare il servizio API REST e i componenti sottostanti (ad esempio, il database e file system) per essere a disponibilità elevata.

## <a name="next-steps"></a>Passaggi successivi

Per esempi relativi all'uso di un profilo tecnico RESTful, vedere gli articoli seguenti:

- [Procedura dettagliata: integrare scambi di attestazioni API REST nel percorso utente Azure AD B2C come convalida dell'input utente](custom-policy-rest-api-claims-validation.md)
- [Procedura dettagliata: aggiungere scambi di attestazioni API REST a criteri personalizzati in Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Proteggere i servizi API REST](secure-rest-api.md)
- [Informazioni di riferimento: profilo tecnico RESTful](restful-technical-profile.md)