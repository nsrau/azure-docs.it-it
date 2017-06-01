---
title: Metadati della federazione di Azure AD | Documentazione Microsoft
description: Questo articolo descrive il documento di metadati della federazione pubblicato da Azure Active Directory per i servizi che accettano i token di Azure Active Directory.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: c2d5f80b-aa74-452c-955b-d8eb3ed62652
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: priyamo
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: a2f3c2d97df37e067670af48b37cc2e2dd3a36cc
ms.contentlocale: it-it
ms.lasthandoff: 05/25/2017


---
# <a name="federation-metadata"></a>Metadati della federazione
Azure Active Directory (Azure AD) pubblica un documento di metadati della federazione per i servizi configurati per accettare i token di sicurezza rilasciati da Azure AD. Il formato del documento di metadati della federazione è descritto in [Web Services Federation Language (WS-Federation) Version 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) (Linguaggio Web Services Federation (WS-Federation) versione 1.2), che estende la pubblicazione [Metadata for the OASIS Security Assertion Markup Language (SAML) v2.0](http://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf) (Metadati per il linguaggio SAML (Security Assertion Markup Language) OASIS v 2.0).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Endpoint dei metadati specifici del tenant e indipendenti dal tenant
Azure AD pubblica endpoint specifici del tenant e indipendenti dal tenant.

Gli endpoint specifici del tenant sono progettati per un particolare tenant. I metadati di federazione specifici del tenant includono informazioni sul tenant, incluse le informazioni specifiche del tenant relative all'autorità emittente e all’endpoint. Le applicazioni che limitano l'accesso a un singolo tenant utilizzano endpoint specifici del tenant.

Gli endpoint indipendenti dal tenant forniscono informazioni comuni a tutti i tenant di Azure AD. Queste informazioni si applicano ai tenant ospitati in *login.microsoftonline.com* e vengono condivise tra i tenant. Per le applicazioni multi-tenant si consiglia di utilizzare endpoint indipendenti dal tenant, dal momento che non sono associate a un particolare tenant.

## <a name="federation-metadata-endpoints"></a>Endpoint dei metadati della federazione
Azure AD pubblica i metadati della federazione all'indirizzo `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.

Per gli **endpoint specifici del tenant**, `TenantDomainName` può essere uno dei seguenti tipi:

* Un nome di dominio registrato di un tenant di Azure AD, ad esempio: `contoso.onmicrosoft.com`.
* L'ID tenant non modificabile del dominio, ad esempio `72f988bf-86f1-41af-91ab-2d7cd011db45`.

Per gli **endpoint indipendenti dal tenant**, `TenantDomainName` è `common`. Questo documento elenca solo gli elementi dei metadati della federazione che sono comuni a tutti i tenant di Azure AD ospitati in login.microsoftonline.com.

Un endpoint specifico del tenant può essere ad esempio `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`. L'endpoint indipendente dal tenant è [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml). È possibile visualizzare il documento di metadati della federazione digitando questo URL in un browser.

## <a name="contents-of-federation-metadata"></a>Contenuto dei metadati della federazione
Nella sezione seguente vengono fornite le informazioni necessarie per i servizi che utilizzano token emessi da Azure AD.

### <a name="entity-id"></a>ID entità
L'elemento `EntityDescriptor` contiene un attributo `EntityID`. Il valore dell'attributo `EntityID` rappresenta l'autorità di certificazione, vale a dire il servizio token di sicurezza che ha rilasciato il token. È importante convalidare l'autorità di certificazione, quando si riceve un token.

I metadati seguenti indicano un elemento `EntityDescriptor` di esempio specifico del tenant con un elemento `EntityID`.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
È possibile sostituire l'ID tenant nell'endpoint indipendente dal tenant con il proprio ID tenant per creare un valore `EntityID` specifico del tenant. Il valore risultante sarà lo stesso dell’autorità emittente del token. La strategia consente a un'applicazione multi-tenant di convalidare l'autorità di certificazione per un tenant specificato.

I metadati seguenti indicano un esempio di elemento `EntityID` indipendente dal tenant. Si noti che `{tenant}` è un valore letterale e non un segnaposto.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Certificati per la firma di token
Quando un servizio riceve un token emesso da un tenant di Azure AD, è necessario convalidare la firma del token con una chiave per la firma che viene pubblicata nel documento dei metadati di federazione. I metadati di federazione includono la parte pubblica dei certificati utilizzati dai tenant per la firma dei token. I byte non elaborati del certificato vengono visualizzati nell'elemento `KeyDescriptor` . Il certificato per la firma di token è valido per la firma solo quando il valore dell'attributo `use` è `signing`.

Un documento di metadati della federazione pubblicato da Azure AD può avere più chiavi per la firma, ad esempio quando Azure AD sta per aggiornare il certificato di firma. Quando un documento di metadati di federazione include certificati, un servizio che convalida i token deve supportare tutti i certificati nel documento.

I metadati seguenti indicano un esempio di elemento `KeyDescriptor` con una chiave di firma.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

L'elemento `KeyDescriptor` appare in due punti del documento di metadati della federazione: nella sezione specifica di WS-Federation e in quella specifica di SAML. I certificati pubblicati in entrambe le sezioni saranno uguali.

Nella sezione specifica di WS-Federation un lettore di metadati di WS-Federation legge i certificati da un elemento `RoleDescriptor` con il tipo `SecurityTokenServiceType`.

I metadati seguenti indicano un esempio di elemento `RoleDescriptor` .

```
<RoleDescriptor xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706">
```

Nella sezione specifica di SAML, un lettore di metadati di WS-Federation legge i certificati da un elemento `IDPSSODescriptor` .

I metadati seguenti indicano un esempio di elemento `IDPSSODescriptor` .

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Non esistono differenze nel formato di certificati specifici del tenant e indipendenti dal tenant.

### <a name="ws-federation-endpoint-url"></a>URL dell'endpoint WS-Federation
I metadati di federazione includono l'URL utilizzato da Azure AD per il Single Sign-In e il Single Sign-Out nel protocollo WS-Federation. Questo endpoint viene visualizzato nell'elemento `PassiveRequestorEndpoint` .

I metadati seguenti indicano un elemento `PassiveRequestorEndpoint` di esempio per un endpoint specifico del tenant.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
Per l'endpoint indipendente dal tenant, l'URL di WS-Federation viene visualizzato nell'endpoint WS-Federation, come illustrato nell'esempio seguente.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>URL dell'endpoint del protocollo SAML
I metadati di federazione includono l'URL utilizzato da Azure AD per il Single Sign-In e il Single Sign-Out nel protocollo SAML 2.0. Questi endpoint vengono visualizzati nell'elemento `IDPSSODescriptor` .

Gli URL di accesso e di disconnessione vengono visualizzati negli elementi `SingleSignOnService` e `SingleLogoutService`.

I metadati seguenti indicano un `PassiveResistorEndpoint` di esempio per un endpoint specifico del tenant.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Allo stesso modo, gli endpoint per gli endpoint del protocollo SAML 2.0 comune vengono pubblicati nei metadati di federazione indipendenti dal tenant, come illustrato nell'esempio seguente.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```

