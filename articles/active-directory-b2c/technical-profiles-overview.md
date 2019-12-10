---
title: Panoramica dei profili tecnici nei criteri personalizzati
titleSuffix: Azure AD B2C
description: Informazioni sul modo in cui i profili tecnici vengono usati in un criterio personalizzato in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: af08a24ff28d59bf743f92aa69ffa823dcdcc544
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951038"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Informazioni sui profili tecnici nei criteri personalizzati di Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Un profilo tecnico fornisce un Framework con un meccanismo incorporato per la comunicazione con tipi diversi di entità mediante un criterio personalizzato in Azure Active Directory B2C (Azure AD B2C). I profili tecnici vengono usati per comunicare con il tenant di Azure AD B2C, creare un utente o leggere un profilo utente. Un profilo tecnico può essere autocertificato per consentire l'interazione con l'utente, ad esempio per raccogliere le credenziali dell'utente per l'accesso e quindi eseguire il rendering della pagina di iscrizione o di reimpostazione della password.

## <a name="type-of-technical-profiles"></a>Tipi di profili tecnici

Un profilo tecnico supporta i tipi di scenario riportati di seguito.

- [Azure Active Directory](active-directory-technical-profile.md): supporto della gestione degli utenti di Azure Active Directory B2C.
- [Autorità emittente di token JWT](jwt-issuer-technical-profile.md): rilascio di un token JWT che viene restituito all'applicazione relying party.
- **Provider PhoneFactor**: autenticazione a più fattori.
- [OAuth1](oauth1-technical-profile.md): federazione con qualsiasi provider di identità con protocollo OAuth 1.0.
- [OAuth2](oauth2-technical-profile.md): federazione con qualsiasi provider di identità con protocollo OAuth 2.0.
- [OpenID Connect](openid-connect-technical-profile.md) -Federazione con qualsiasi provider di identità del protocollo OpenID Connect.
- [Trasformazione delle attestazioni](claims-transformation-technical-profile.md): chiamata di trasformazioni delle attestazioni di output per convalidare le attestazioni, modificarne i valori o impostare valori predefiniti per un set di attestazioni di output.
- [Provider RESTful](restful-technical-profile.md) : è possibile chiamare i servizi API REST, ad esempio convalidare l'input dell'utente, arricchire i dati utente o integrarsi con applicazioni line-of-business.
- [SAML2](saml-technical-profile.md): federazione con qualsiasi provider di identità con protocollo SAML.
- [Autocertificazione](self-asserted-technical-profile.md): interazione con l'utente, ad esempio per raccogliere le credenziali dell'utente per l'accesso, eseguire il rendering della pagina di iscrizione o reimpostare la password.
- [Gestione delle sessioni](active-directory-b2c-reference-sso-custom.md): gestione di diversi tipi di sessioni.
- **Application Insights**

## <a name="technical-profile-flow"></a>Flusso dei profili tecnici

Tutti i tipi di profili tecnici condividono lo stesso concetto. Si inviano attestazioni di input, si esegue la trasformazione delle attestazioni e si comunica con l'entità configurata, ad esempio un provider di identità, un'API REST o servizi directory di Azure AD. Al termine del processo, il profilo tecnico restituisce le attestazioni di output ed eventualmente ne esegue la trasformazione. Il diagramma seguente illustra come vengono elaborati le trasformazioni e i mapping a cui viene fatto riferimento nel profilo tecnico. Indipendentemente dall'entità con cui interagisce il profilo tecnico, dopo l'esecuzione di qualsiasi trasformazione delle attestazioni, le attestazioni di output del profilo tecnico vengono immediatamente archiviate nel contenitore delle attestazioni.

![Diagramma che illustra il flusso del profilo tecnico](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **Trasformazione delle attestazioni di input**: le attestazioni di input di ogni [trasformazione delle attestazioni](claimstransformations.md) di input vengono prelevate dal contenitore delle attestazioni. Al termine dell'esecuzione, le attestazioni di output vengono inserite in tale contenitore. Le attestazioni di output di una trasformazione delle attestazioni di input possono essere le attestazioni di input di una successiva trasformazione delle attestazioni di input.
2. **Attestazioni di input**: le attestazioni vengono prelevate dal contenitore delle attestazioni e usate per il profilo tecnico. Un [profilo tecnico autocertificato](self-asserted-technical-profile.md), ad esempio, usa le attestazioni di input per prepopolare le attestazioni di output fornite dall'utente. Un profilo tecnico API REST usa le attestazioni di input per inviare i parametri di input all'endpoint API REST. Azure Active Directory usa l'attestazione di input come identificatore univoco per la lettura, l'aggiornamento o l'eliminazione di un account.
3. **Esecuzione del profilo tecnico**: il profilo tecnico scambia le attestazioni con l'entità configurata. ad esempio:
    - Reindirizza l'utente al provider di identità per completare l'accesso. Dopo aver completato l'accesso, l'utente torna indietro e l'esecuzione del profilo tecnico continua.
    - Chiama un'API REST inviando i parametri come InputClaims e ricevendo le informazioni restituite come OutputClaims.
    - Crea o aggiorna l'account utente.
    - Invia e verifica il messaggio di testo di MFA.
4. **Profili tecnici di convalida**: per un [profilo tecnico autocertificato](self-asserted-technical-profile.md), è possibile chiamare un [profilo tecnico di convalida](validation-technical-profile.md) dell'input, che convalida i dati inclusi nel profilo dall'utente e restituisce un messaggio di errore oppure OK, con o senza attestazioni di output. Prima che Azure AD B2C crei un nuovo account, ad esempio, controlla se l'utente esiste già nei servizi directory. È possibile chiamare un profilo tecnico API REST per aggiungere logica di business personalizzata.<p>L'ambito delle attestazioni di output di un profilo tecnico di convalida è limitato al profilo tecnico da cui è richiamato e agli altri profili tecnici di convalida nello stesso profilo tecnico. Se si vogliono usare le attestazioni di output nel passaggio di orchestrazione successivo, è necessario aggiungerle al profilo tecnico che richiama il profilo tecnico di convalida.
5. **Attestazioni di output**: le attestazioni vengono restituite al contenitore delle attestazioni. È possibile usare queste attestazioni nel passaggio di orchestrazione successivo oppure nelle trasformazioni delle attestazioni di output.
6. **Trasformazioni delle attestazioni di output**: le attestazioni di input di ogni [trasformazione delle attestazioni](claimstransformations.md) di output vengono prelevate dal contenitore delle attestazioni. Le attestazioni di output del profilo tecnico provenienti dai passaggi precedenti possono essere le attestazioni di input di una trasformazione delle attestazioni di output. Al termine dell'esecuzione, le attestazioni di output vengono inserite nel contenitore delle attestazioni. Le attestazioni di output di una trasformazione delle attestazioni di output possono essere le attestazioni di input di una successiva trasformazione delle attestazioni di output.
7. **Gestione delle sessioni Single Sign-On (SSO)** : la [gestione delle sessioni SSO](active-directory-b2c-reference-sso-custom.md) controlla l'interazione con un utente dopo la relativa autenticazione. L'amministratore può ad esempio controllare se verrà visualizzata la selezione dei provider di identità o se sarà necessario immettere nuovamente i dettagli dell'account locale.

Un profilo tecnico può ereditare da un altro profilo tecnico per modificare le impostazioni o aggiungere nuove funzionalità.  L'elemento **IncludeTechnicalProfile** è un riferimento al profilo tecnico di base da cui un profilo tecnico è derivato.

Il profilo tecnico **AAD-UserReadUsingAlternativeSecurityId-NoError**, ad esempio, include **AAD-UserReadUsingAlternativeSecurityId**. Questo profilo tecnico imposta l'elemento dei metadati **RaiseErrorIfClaimsPrincipalDoesNotExist** su `true` e genera un errore se un account di social networking non esiste nella directory. **AAD-UserReadUsingAlternativeSecurityId-NoError** esegue l'override di questo comportamento e disabilita il messaggio di errore se l'utente non esiste.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** include il profilo tecnico `AAD-Common`.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Sia **AAD-UserReadUsingAlternativeSecurityId-NoError** che **AAD-UserReadUsingAlternativeSecurityId** non specificano l'elemento obbligatorio **Protocol** perché è specificato nel profilo tecnico **AAD-Common**.

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

Un profilo tecnico può includere o ereditare un altro profilo tecnico, che potrebbe includerne un altro ancora. Non sono previsti limiti per il numero di livelli. A seconda dei requisiti aziendali, il percorso utente potrebbe chiamare **AAD-UserReadUsingAlternativeSecurityId**, che genera un errore se l'account di social networking di un utente non esiste, oppure **AAD-UserReadUsingAlternativeSecurityId-NoError**, che non genera un errore.











