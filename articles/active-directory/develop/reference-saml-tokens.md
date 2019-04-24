---
title: Informazioni sui diversi tipi di token e di attestazione supportati da Azure AD | Documentazione Microsoft
description: Una guida alla comprensione e alla valutazione delle attestazioni nei token SAML 2.0 e JSON Web Tokens (JWT) emessi da Azure Active Directory (AAD)
documentationcenter: na
author: CelesteDG
services: active-directory
manager: mtillman
editor: ''
ms.assetid: 166aa18e-1746-4c5e-b382-68338af921e2
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/22/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a77118edd08faf6d40897a916ee85e2b6e20d3bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60298258"
---
# <a name="azure-ad-saml-token-reference"></a>Informazioni di riferimento sui token SAML di Azure AD

Azure Active Directory (Azure AD) rilascia tipi diversi di token di sicurezza durante l'elaborazione di ogni flusso di autenticazione. Questo documento descrive il formato, le caratteristiche di sicurezza e i contenuti di ogni tipo di token.

## <a name="claims-in-saml-tokens"></a>Attestazioni nei token SAML

> [!div class="mx-codeBreakAll"]
> | Name | Attestazione JWT equivalente | DESCRIZIONE | Esempio |
> | --- | --- | --- | ------------|
> |Audience | `aud` |Destinatario previsto per il token. L'applicazione che riceve il token deve verificare che il valore del gruppo di destinatari sia corretto e rifiuta tutti i token destinati a un gruppo di destinatari diverso. | `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>`  |
> | Istante di autenticazione | |Registra la data e l'ora in cui è avvenuta l'autenticazione. | `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` | 
> |Metodo di autenticazione | `amr` |Identifica la modalità di autenticazione dell'oggetto del token. | `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` |
> |Nome | `given_name` |Fornisce il nome o il nome "specificato" dell'utente, come impostato nell'oggetto utente di Azure AD. | `<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">`<br>`<AttributeValue>Frank<AttributeValue>`  |
> |Gruppi | `groups` |Fornisce gli ID oggetto che rappresentano le appartenenze ai gruppi del soggetto. Questi valori sono univoci (vedere ID oggetto) e possono essere usati in modo sicuro per la gestione dell'accesso, ad esempio l'attivazione dell'autorizzazione per accedere a una risorsa. I gruppi inclusi nella relativa attestazione sono configurati in base all'applicazione, tramite la proprietà "groupMembershipClaims" del manifesto dell'applicazione. Un valore null escluderà tutti i gruppi, un valore "SecurityGroup" includerà solo le appartenenze al gruppo di sicurezza di Active Directory e un valore "All" includerà sia i gruppi di sicurezza che le liste di distribuzione di Office 365. <br><br> **Note**: <br> Se il numero di gruppi in cui è presente l'utente supera il limite (150 per SAML, 200 per JWT), verrà aggiunta un'attestazione per l'eccedenza alle origini delle attestazioni che puntano all'endpoint Graph che contiene l'elenco dei gruppi per l'utente. (in . | `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` |
> | Indicatore di eccedenza dei gruppi | `groups:src1` | Per le richieste di token che non hanno un limite di lunghezza (vedere `hasgroups` più indietro), ma sono comunque troppo grandi per il token, verrà incluso un collegamento all'elenco di gruppi completo per l'utente. Per SAML, viene aggiunto come nuova attestazione al posto dell'attestazione `groups`. | `<Attribute Name=" http://schemas.microsoft.com/claims/groups.link">`<br>`<AttributeValue>https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects<AttributeValue>` |
> |Provider di identità | `idp` |Registra il provider di identità che ha autenticato l'oggetto del token. Questo valore è identico al valore dell'attestazione Autorità di certificazione, a meno che l'account utente non sia in un tenant diverso rispetto all'autorità di certificazione. | `<Attribute Name=" http://schemas.microsoft.com/identity/claims/identityprovider">`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` |
> |IssuedAt | `iat` |Archivia l'ora in cui è stato rilasciato il token. Viene spesso usata per misurare la validità del token. | `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` |
> |Issuer | `iss` |Identifica il servizio token di sicurezza (STS) che costruisce e restituisce il token. Nel token restituito da Azure AD l'autorità di certificazione è sts.windows.net. Il GUID del valore di attestazione dell'autorità di certificazione è l'ID tenant della directory di Azure AD. L'ID tenant è un identificatore non modificabile e affidabile della directory. | `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` |
> |Cognome | `family_name` |Fornisce il cognome dell'utente, come definito nell'oggetto utente di Azure AD. | `<Attribute Name=" http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">`<br>`<AttributeValue>Miller<AttributeValue>` |
> |Name | `unique_name` |Fornisce un valore leggibile che identifica l'oggetto del token. Questo valore potrebbe non essere univoco all'interno di un tenant e può essere usato solo per scopi di visualizzazione. | `<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>`|
> |ID oggetto | `oid` |Contiene un identificatore univoco di un oggetto in Azure AD. Questo valore non è modificabile e non può essere riassegnato o riutilizzato. Usare l'ID oggetto per identificare un oggetto nelle query ad Azure AD. | `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` |
> |Ruoli | `roles` |Rappresenta tutti i ruoli applicazione concessi al soggetto sia direttamente che indirettamente tramite l'appartenenza a gruppi e può essere usata per imporre il controllo degli accessi in base al ruolo. I ruoli applicazione sono definiti in base all'applicazione, tramite la proprietà `appRoles` del manifesto dell'applicazione. La proprietà `value` di ogni ruolo applicazione è il valore visualizzato nell'attestazione Ruoli. | `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`|
> |Subject | `sub` |Identifica l'entità su cui il token asserisce informazioni, ad esempio l'utente di un'applicazione. Questo valore non è modificabile e non può essere riassegnato o riutilizzato, è quindi possibile usarlo per eseguire controlli di autorizzazione in modo sicuro. Dato che il soggetto è sempre presente nei token rilasciati da Azure AD, è consigliabile l'uso di questo valore in un sistema di autorizzazione per utilizzo generico. <br> `SubjectConfirmation` non è un'attestazione. Descrive la modalità di verifica del soggetto del token. `Bearer` indica che il soggetto viene confermato dal possesso del token. | `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>`|
> |ID tenant | `tid` |Identificatore non modificabile e non riutilizzabile che identifica il tenant della directory che ha rilasciato il token. È possibile usare questo valore per accedere alle risorse di directory specifiche del tenant in un'applicazione multi-tenant. Ad esempio, è possibile usare questo valore per identificare il tenant in una chiamata all'API Graph. | `<Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>`|
> |Durata del token | `nbf`, `exp` |Definisce l'intervallo di tempo entro il quale un token è valido. Il servizio che convalida il token deve verificare che la data corrente sia compresa nella durata del token. In caso contrario, deve rifiutare il token. La tolleranza del servizio è fino a cinque minuti oltre l'intervallo della durata del token per tener conto di eventuali differenze di orario ("mancata sincronizzazione dell'ora") tra Azure AD e il servizio. | `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br>|

## <a name="sample-saml-token"></a>Token SAML di esempio

Questo è un esempio di un tipico token SAML.

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
          <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
      </wsp:AppliesTo>
      <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
          <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
          <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
              <ds:CanonicalizationMethod Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
              <ds:SignatureMethod Algorithm="https://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
              <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                <ds:Transforms>
                  <ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                  <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="https://www.w3.org/2001/04/xmlenc#sha256" />
                <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
              </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
            <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
              <X509Data>
                <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
              </X509Data>
            </KeyInfo>
          </ds:Signature>
          <Subject>
            <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
            <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
          </Subject>
          <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
            <AudienceRestriction>
              <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
            </AudienceRestriction>
          </Conditions>
          <AttributeStatement>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
              <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
              <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
              <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
              <AttributeValue>Admin</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
              <AttributeValue>Sample</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
              <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
              <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
              <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
              <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
              <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
              <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
              <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
              <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
              <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
              <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
              <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
              <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
              <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
              <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
            </Attribute>
          </AttributeStatement>
          <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
            <AuthnContext>
              <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
            </AuthnContext>
          </AuthnStatement>
        </Assertion>
      </t:RequestedSecurityToken>
      <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="https://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedAttachedReference>
      <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="https://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedUnattachedReference>
      <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
      <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

## <a name="related-content"></a>Contenuti correlati
* Per altre informazioni sulla gestione dei criteri di durata dei token tramite l'API Graph di Azure AD, vedere gli articoli relativi alle [operazioni sui criteri](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) e all'[entità dei criteri](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity) di Azure AD Graph.
* Per altre informazioni ed esempi sulla gestione dei criteri tramite i cmdlet PowerShell, vedere [Configurable token lifetimes in Azure AD](active-directory-configurable-token-lifetimes.md) (Durata dei token configurabile in Azure AD). 
* Aggiungere [attestazioni personalizzate e facoltative](active-directory-optional-claims.md) ai token per l'applicazione.
* Usare [Single Sign-On (SSO) con SAML](single-sign-on-saml-protocol.md).
* Usare il [protocollo SAML per Single Sign-Out di Azure](single-sign-out-saml-protocol.md)
