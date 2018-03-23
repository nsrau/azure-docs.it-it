---
title: "Azure AD Connect: Usare un provider di identità SAML 2.0 per l'accesso Single Sign On | Microsoft Docs"
description: Questo argomento descrive l'uso di un IdP conforme a SAML 2.0 per l'accesso Single Sign-On.
services: active-directory
author: billmath
manager: mtillman
ms.custom: it-pro
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 46c65e0efdc91b70c5d0d2afdf83d7205efc8057
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2018
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>Usare un provider di identità (IdP) SAML 2.0 per l'accesso Single Sign-On

Questo argomento contiene informazioni sull'uso di un provider di identità basato sul profilo SP-Lite conforme a SAML 2.0 come servizio token di sicurezza/provider di identità preferito. Si tratta di un'opzione utile quando si hanno già una directory di utenti e un archivio di password locali a cui è possibile accedere con SAML 2.0. La directory di utenti esistente può essere usata per l'accesso a Office 365 e ad altre risorse protette da Azure AD. Il profilo SP-Lite SAML 2.0 si basa sul diffuso standard di identità federate Security Assertion Markup Language (SAML) per fornire un framework di accesso e scambio degli attributi.

>[!NOTE]
>Per un elenco di IdP di terze parti testati per l'uso con Azure AD, vedere l'[elenco di compatibilità di federazione di Azure AD](active-directory-aadconnect-federation-compatibility.md)

Microsoft supporta questa esperienza di accesso come integrazione di un servizio cloud Microsoft, ad esempio Office 365, con l'IdP basato sul profilo SAML 2.0 correttamente configurato. Poiché i provider di identità SAML 2.0 sono prodotti di terze parti, Microsoft non fornisce alcun supporto per la distribuzione, la configurazione, la risoluzione dei problemi e le procedure consigliate. Una volta eseguita correttamente la configurazione, è possibile testare la corretta configurazione dell'integrazione con il provider di identità SAML 2.0 usando Microsoft Connectivity Analyzer Tool, descritto in dettaglio più avanti. Per altre informazioni sul provider di identità basato sul profilo SP-Lite SAML 2.0, rivolgersi all'organizzazione da cui è stato fornito.

>[!IMPORTANT]
>In questo scenario di accesso con i provider di identità SAML 2.0 è disponibile solo un set limitato di client, tra cui:

>- Client basati sul Web, ad esempio Outlook Web Access e SharePoint Online
- Rich client di posta elettronica che usano l'autenticazione di base e un metodo di accesso di Exchange supportato come IMAP, POP, Active Sync, MAPI e così via (è necessario che sia distribuito l'endpoint ECP (Enhanced Client Protocol)), tra cui:
    - Microsoft Outlook 2010/Outlook 2013/Outlook 2016, Apple iPhone (diverse versioni di iOS)
    - Diversi dispositivi Google Android
    - Windows Phone 7, Windows Phone 7.8 e Windows Phone 8.0
    - Client di posta elettronica di Windows 8 e Windows 8.1
    - Client di posta elettronica di Windows 10

Tutti gli altri client non sono disponibili in questo scenario di accesso con il provider di identità SAML 2.0. Il client desktop Lync 2010 non può ad esempio accedere al servizio con il provider di identità SAML 2.0 configurato per l'accesso Single Sign-On.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Requisiti del protocollo SAML 2.0 in Azure AD
Questo argomento contiene informazioni dettagliate sui requisiti relativi al protocollo e alla formattazione dei messaggi che il provider di identità SAML 2.0 deve implementare per configurare la federazione con Azure AD e consentire l'accesso a uno o più servizi cloud Microsoft (ad esempio Office 365). La relying party SAML 2.0 (SP-STS) per un servizio cloud Microsoft in uso in questo scenario è Azure AD.

È consigliabile verificare che i messaggi di output del provider di identità SAML 2.0 siano il più simili possibile alle tracce di esempio fornite. Usare inoltre valori di attributo specifici dei metadati di Azure AD forniti, dove possibile. Una volta ottenuti i messaggi di output desiderati, è possibile testarli con Microsoft Connectivity Analyzer, come descritto di seguito.

È possibile scaricare i metadati di Azure AD da questo URL: [https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml](http://https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml).
Per i clienti in Cina che usano l'istanza di Office 365 specifica per la Cina, è necessario usare l'endpoint di federazione seguente: [https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml).

## <a name="saml-protocol-requirements"></a>Requisiti del protocollo SAML
Questa sezione illustra in modo dettagliato come vengono combinate le coppie di messaggi di richiesta e di risposta per consentire la corretta formattazione dei messaggi.

È possibile configurare Azure AD per il funzionamento con i provider di identità che usano il profilo SP-Lite SAML 2.0 con alcuni requisiti specifici, come indicato di seguito. Usando i messaggi di richiesta e di risposta SAML di esempio insieme ai test automatizzati e manuali, è possibile ottenere l'interoperabilità con Azure AD.

## <a name="signature-block-requirements"></a>Requisiti del blocco di firma
Nel messaggio di risposta SAML il nodo Signature contiene informazioni sulla firma digitale del messaggio stesso. Il blocco di firma ha i requisiti seguenti:

1. Il nodo di asserzione deve essere firmato
2.  È necessario usare l'algoritmo RSA-sha1 come DigestMethod. Altri algoritmi di firma digitale non sono accettati.
   `<ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  È anche possibile firmare il documento XML. 
4.  Il valore dell'algoritmo di trasformazione deve corrispondere ai valori nell'esempio seguente: `<ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  L'algoritmo SignatureMethod deve corrispondere all'esempio seguente: `<ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Binding supportati
I binding sono i parametri di comunicazione correlati al trasporto richiesti. Ai binding si applicano i requisiti seguenti

1. Il trasporto richiesto è HTTPS.
2.  Azure AD richiede HTTP POST per l'invio dei token durante l'accesso
3.  Azure AD usa HTTP POST per la richiesta di autenticazione al provider di identità e REDIRECT per il messaggio di disconnessione al provider di identità.

## <a name="required-attributes"></a>Attributi richiesti
Questa tabella mostra i requisiti per gli attributi specifici nel messaggio SAML 2.0.
 
|Attributo|DESCRIZIONE|
| ----- | ----- |
|NameID|Il valore di questa asserzione deve corrispondere al valore di ImmutableID dell'utente di Azure AD. Può essere composto da un massimo di 64 caratteri alfanumerici. Qualsiasi carattere non compatibile con HTML deve essere codificato. Ad esempio, il carattere "+" viene visualizzato come ".2B".|
|IDPEmail|Il nome dell'entità utente (UPN) è indicato nella risposta SAML come elemento denominato IDPEmail. Si tratta dell'oggetto UserPrincipalName (UPN) dell'utente in Azure AD/Office 365. Il nome dell'entità utente è nel formato indirizzo di posta elettronica. Valore UPN in Office 365 (Azure Active Directory).|
|Issuer|Deve essere un URI del provider di identità. Non riutilizzare l'autorità emittente dei messaggi di esempio. Se si hanno più domini di primo livello nei tenant di Azure AD, l'autorità emittente deve corrispondere all'impostazione URI specificata configurata per ogni dominio.|

>[!IMPORTANT]
>Azure AD attualmente supporta l'URI di formato di NameID seguente per SAML 2.0: urn:oasis:names:tc:SAML:2.0:nameid-format:persistent.

## <a name="sample-saml-request-and-response-messages"></a>Messaggi di richiesta e di risposta SAML di esempio
Per lo scambio di messaggi di accesso viene visualizzata una coppia di messaggi di richiesta e di risposta.
Di seguito è illustrato un messaggio di richiesta di esempio inviato da Azure AD a un provider di identità SAML 2.0 di esempio. Il provider di identità SAML 2.0 di esempio è Active Directory Federation Services (AD FS) configurato per l'uso del protocollo SAML-P. È stato anche completato un test di interoperabilità con altri provider di identità SAML 2.0.

    `<samlp:AuthnRequest xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" IssueInstant="2014-01-30T16:18:35Z" Version="2.0" AssertionConsumerServiceIndex="0" >
    <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
    <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>`

Di seguito è illustrato un messaggio di risposta di esempio inviato dal provider di identità di esempio conforme a SAML 2.0 ad Azure AD/Office 365.

    `<samlp:Response ID="_592c022f-e85e-4d23-b55b-9141c95cd2a5" Version="2.0" IssueInstant="2014-01-31T15:36:31.357Z" Destination="https://login.microsoftonline.com/login.srf" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
    </samlp:Status>
    <Assertion ID="_7e3c1bcd-f180-4f78-83e1-7680920793aa" IssueInstant="2014-01-31T15:36:31.279Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
        <ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1" />
        <ds:Reference URI="#_7e3c1bcd-f180-4f78-83e1-7680920793aa">
          <ds:Transforms>
            <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
            <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
          </ds:Transforms>
          <ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1" />
          <ds:DigestValue>CBn/5YqbheaJP425c0pHva9PhNY=</ds:DigestValue>
        </ds:Reference>
      </ds:SignedInfo>
      <ds:SignatureValue>TciWMyHW2ZODrh/2xrvp5ggmcHBFEd9vrp6DYXp+hZWJzmXMmzwmwS8KNRJKy8H7XqBsdELA1Msqi8I3TmWdnoIRfM/ZAyUppo8suMu6Zw+boE32hoQRnX9EWN/f0vH6zA/YKTzrjca6JQ8gAV1ErwvRWDpyMcwdYCiWALv9ScbkAcebOE1s1JctZ5RBXggdZWrYi72X+I4i6WgyZcIGai/rZ4v2otoWAEHS0y1yh1qT7NDPpl/McDaTGkNU6C+8VfjD78DrUXEcAfKvPgKlKrOMZnD1lCGsViimGY+LSuIdY45MLmyaa5UT4KWph6dA==</ds:SignatureValue>
      <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
        <ds:X509Data>
          <ds:X509Certificate>MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyhBREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDTE1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9ybWVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/+3ZWxd9T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEMb2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcCAwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvyJOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBySx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==</ds:X509Certificate>
        </ds:X509Data>
      </KeyInfo>
    </ds:Signature>
    <Subject>
      <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">ABCDEG1234567890</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" NotOnOrAfter="2014-01-31T15:41:31.357Z" Recipient="https://login.microsoftonline.com/login.srf" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2014-01-31T15:36:31.263Z" NotOnOrAfter="2014-01-31T16:36:31.263Z">
      <AudienceRestriction>
        <Audience>urn:federation:MicrosoftOnline</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="IDPEmail">
        <AttributeValue>administrator@contoso.com</AttributeValue>
      </Attribute>
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2014-01-31T15:36:30.200Z" SessionIndex="_7e3c1bcd-f180-4f78-83e1-7680920793aa">
      <AuthnContext>
        <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
    </Assertion>
    </samlp:Response>`

## <a name="configure-your-saml-20-compliant-identity-provider"></a>Configurare il provider di identità conforme a SAML 2.0
Questo argomento illustra le linee guida su come configurare il provider di identità SAML 2.0 per la federazione con Azure AD, per consentire l'accesso Single Sign-On a uno o più servizi cloud Microsoft (ad esempio Office 365) con il protocollo SAML 2.0. La relying party SAML 2.0 per un servizio cloud Microsoft in uso in questo scenario è Azure AD.

## <a name="add-azure-ad-metadata"></a>Aggiungere i metadati di Azure AD
Il provider di identità SAML 2.0 deve essere conforme alle informazioni sulla relying party di Azure AD. Azure AD pubblica i metadati all'indirizzo https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml.

È consigliabile importare sempre i metadati di Azure AD più recenti quando si configura il provider di identità SAML 2.0. Si noti che Azure AD non legge i metadati dal provider di identità.

## <a name="add-azure-ad-as-a-relying-party"></a>Aggiungere Azure AD come relying party
È necessario abilitare la comunicazione tra il provider di identità SAML 2.0 e Azure AD. Questa configurazione dipende dal provider di identità specifico. Vedere la documentazione pertinente. È in genere necessario impostare l'ID della relying party sullo stesso valore di entityID dei metadati di Azure AD.

>[!NOTE]
>Verificare che l'orologio del server del provider di identità SAML 2.0 sia sincronizzato con un'origine di ora precisa. L'impostazione non corretta dell'ora può comportare un errore degli accessi federati.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>Installare Windows PowerShell per l'accesso con il provider di identità SAML 2.0
Dopo aver configurato il provider di identità SAML 2.0 per l'uso per l'accesso di Azure AD, il passaggio successivo consiste nello scaricare e installare il Modulo di Azure Active Directory per Windows PowerShell. Una volta eseguita l'installazione, usare questi cmdlet per configurare i domini di Azure AD come domini federati.

Il Modulo di Azure Active Directory per Windows PowerShell è una soluzione per la gestione dei dati dell'organizzazione in Azure AD che è possibile scaricare. Questo modulo installa un set di cmdlet in Windows PowerShell. Eseguire questi cmdlet per impostare l'accesso Single Sign-On ad Azure AD e a tutti i servizi cloud sottoscritti. Per istruzioni su come scaricare e installare i cmdlet, vedere [http://technet.microsoft.com/library/jj151815.aspx](http://technet.microsoft.com/library/jj151815.aspx)

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>Impostare una relazione di trust tra il provider di identità SAML e Azure AD
Prima di configurare la federazione in un dominio di Azure AD, è necessario che sia configurato un dominio personalizzato. Non è possibile configurare la federazione per il dominio predefinito fornito da Microsoft. Il dominio predefinito di Microsoft termina con "onmicrosoft.com".
Si eseguirà una serie di cmdlet nell'interfaccia della riga di comando di Windows PowerShell per aggiungere o convertire i domini per l'accesso Single Sign-On.

Ogni dominio di Azure Active Directory per il quale si vuole configurare la federazione usando il provider di identità SAML 2.0 deve essere aggiunto come dominio Single Sign-On o convertito da dominio standard a dominio Single Sign-On. Aggiungendo o convertendo un dominio si imposta una relazione di trust tra il provider di identità SAML 2.0 e Azure AD.

La procedura seguente illustra i passaggi per la conversione di un dominio standard esistente in un dominio federato con SP-Lite SAML 2.0. Si noti che dopo l'esecuzione di questo passaggio, potrebbe verificarsi un'interruzione del dominio che potrebbe influire sugli utenti per un periodo di massimo 2 ore.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Configurazione di un dominio nella directory di Azure AD per la federazione


1. Connettersi alla directory di Azure AD come amministratore tenant: Connect-MsolService.
2.  Configurare il dominio di Office 365 desiderato per l'uso della federazione con SAML 2.0: `$dom = "contoso.com" $BrandName - "Sample SAML 2.0 IDP" $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" $MyURI = "urn:uri:MySamlp2IDP" $MySigningCert = @" MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" "@ $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" $Protocol = "SAMLP" Set-MsolDomainAuthentication -DomainName $dom -FederationBrandName $dom -Authentication Federated -PassiveLogOnUri $MyURI -ActiveLogOnUri $ecpUrl -SigningCertificate $MySigningCert -IssuerUri $uri -LogOffUri $url -PreferredAuthenticationProtocol $Protocol` 

3.  È possibile ottenere la stringa con codifica Base64 del certificato di firma del file di metadati dell'IdP. Di seguito è illustrato un esempio di questo percorso, che tuttavia potrebbe essere leggermente diverso, in base all'implementazione.

    `<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"> <KeyDescriptor use="signing"> <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#"> <X509Data> <X509Certificate>MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate> </X509Data> </KeyInfo> </KeyDescriptor>` 

Per altre informazioni su "Set-MsolDomainAuthentication", vedere: [http://technet.microsoft.com/library/dn194112.aspx](http://technet.microsoft.com/library/dn194112.aspx).

>[!NOTE]
>È necessario eseguire "$ecpUrl = "https://WS2012R2-0.contoso.com/PAOS"" solo se si configura un'estensione ECP per il provider di identità. I client di Exchange Online, ad eccezione di Outlook Web Application (OWA), usano un endpoint attivo basato su POST. Se il servizio token di sicurezza SAML 2.0 implementa un endpoint attivo simile all'implementazione ECP di Shibboleth di un endpoint attivo, potrebbe essere possibile per questi rich client interagire con il servizio Exchange Online.

Dopo aver configurato la federazione, è possibile tornare alla modalità non federata (o gestita), ma questa modifica richiede fino a due ore, oltre che l'assegnazione di nuove password casuali per l'accesso basato su cloud per ogni utente. La reimpostazione della modalità gestita potrebbe essere necessaria in alcuni scenari per correggere un errore nelle impostazioni. Per altre informazioni sulla conversione dei domini, vedere: [http://msdn.microsoft.com/library/windowsazure/dn194122.aspx](http://msdn.microsoft.com/library/windowsazure/dn194122.aspx).

## <a name="provision-user-principals-to-azure-ad--office-365"></a>Effettuare il provisioning di entità utente in Azure AD/Office 365
Per poter autenticare gli utenti in Office 365, è necessario effettuare il provisioning in Azure AD di entità utente corrispondenti all'asserzione nell'attestazione SAML 2.0. Se queste entità utente non sono note in anticipo ad Azure AD, non possono essere usate per l'accesso federato. Per il provisioning delle entità utente è possibile usare Azure AD Connect o Windows PowerShell.

Azure AD Connect può essere usato per il provisioning di entità nei domini nella directory di Azure AD dall'istanza locale di Active Directory. Per informazioni più dettagliate, vedere [Integrare le directory locali con Azure Active Directory](active-directory-aadconnect.md).

È anche possibile usare Windows PowerShell per automatizzare l'aggiunta di nuovi utenti in Azure AD e per sincronizzare le modifiche dalla directory locale. Per usare i cmdlet di Windows PowerShell, è necessario scaricare i [moduli di Azure Active Directory](https://docs.microsoft.com/powershell/azure/install-adv2?view=azureadps-2.0).

Questa procedura illustra come aggiungere un singolo utente ad Azure AD.


1. Connettersi alla directory di Azure AD come amministratore tenant: Connect-MsolService.
2.  Creare una nuova entità utente: ` New-MsolUser
        -UserPrincipalName elwoodf1@contoso.com
        -ImmutableId ABCDEFG1234567890
        -DisplayName "Elwood Folk"
        -FirstName Elwood 
        -LastName Folk 
        -AlternateEmailAddresses "Elwood.Folk@contoso.com" 
        -LicenseAssignment "samlp2test:ENTERPRISEPACK" 
        -UsageLocation "US" ` 

Per altre informazioni su "New-MsolUser", vedere [http://technet.microsoft.com/library/dn194096.aspx](http://technet.microsoft.com/library/dn194096.aspx)

>[!NOTE]
>Il valore di "UserPrinciplName" deve corrispondere al valore che verrà inviato per "IDPEmail" nell'attestazione SAML 2.0 e il valore di "ImmutableID" deve corrispondere al valore inviato nell'asserzione "NameID".

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>Verificare l'accesso Single Sign-On con l'IdP SAML 2.0
Prima di verificare e gestire l'accesso Single Sign-On (anche noto come federazione delle identità), l'amministratore deve esaminare le informazioni ed eseguire i passaggi negli articoli seguenti per configurare l'accesso Single Sign-On con il provider di identità basato su SP-Lite SAML 2.0:


1.  I requisiti del protocollo SAML 2.0 in Azure AD sono stati esaminati
2.  Il provider di identità SAML 2.0 è stato configurato
3.  Installare Windows PowerShell per l'accesso Single Sign-On con il provider di identità SAML 2.0
4.  Impostare una relazione di trust tra il provider di identità SAML 2.0 e Azure AD
5.  È stato effettuato il provisioning di un'entità utente di test in Azure Active Directory (Office 365) mediante Windows PowerShell o Azure AD Connect.
6.  Configurare la sincronizzazione della directory usando [Azure AD Connect](active-directory-aadconnect.md).

Dopo avere configurato l'accesso Single Sign-On con il provider di identità basato su SP-Lite SAML 2.0, è necessario verificare che funzioni correttamente.

>[!NOTE]
>Se è stato convertito un dominio, invece che aggiungerne uno, potrebbero essere necessarie fino a 24 ore per la configurazione di Single Sign-On.
Prima di verificare l'accesso Single Sign-On, completare la configurazione della sincronizzazione di Active Directory, sincronizzare le directory e attivare gli utenti sincronizzati.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Usare lo strumento per verificare la corretta configurazione di Single Sign-On
Per verificare che l'accesso Single Sign-On sia stato configurato correttamente, è possibile eseguire la procedura seguente per controllare di poter accedere al servizio cloud con le credenziali aziendali.

Microsoft offre uno strumento che è possibile usare per testare il provider di identità basato su SAML 2.0. Prima di eseguire lo strumento di test, è necessario aver configurato un tenant di Azure AD per la federazione con il provider di identità.

>[!NOTE]
>Connectivity Analyzer richiede Internet Explorer 10 o versione successiva.



1. Scaricare Connectivity Analyzer da [https://testconnectivity.microsoft.com/?tabid=Client](https://testconnectivity.microsoft.com/?tabid=Client).
2.  Fare clic sul pulsante per l'installazione per avviare il download e l'installazione dello strumento.
3.  Selezionare "I can't set up federation with Office 365, Azure, or other services that use Azure Active Directory" (Impossibile configurare la federazione con Office 365, Azure o altri servizi che usano Azure Active Directory).
4.  Una volta scaricato ed eseguito lo strumento, verrà visualizzata la finestra di diagnostica della connettività. Lo strumento fornisce informazioni dettagliate per testare la connessione della federazione.
5.  Connectivity Analyzer apre l'IdP SAML 2.0 per consentire di eseguire l'accesso. Immettere le credenziali per l'entità utente che si sta testando: ![SAML](media/active-directory-aadconnect-federation-saml-idp/saml1.png)
6.  Nella finestra di accesso per il test della federazione immettere un nome account e una password per il tenant di Azure AD configurato per la federazione con il provider di identità SAML 2.0. Lo strumento tenterà di accedere usando queste credenziali e fornirà come output informazioni dettagliate sui risultati dei test eseguiti durante il tentativo di accesso.
![SAML](media/active-directory-aadconnect-federation-saml-idp/saml2.png)
7. Questa finestra mostra il risultato di un test non riuscito. Facendo clic sul collegamento per la visualizzazione dei risultati dettagliati verranno visualizzate le informazioni relative ai risultati di ogni test eseguito. È anche possibile salvare i risultati su disco per condividerli.
 
>[!NOTE]
>Connectivity Analyzer testa anche la federazione attiva usando i protocolli basati su WS* ed ECP/PAOS. Se non si usano questi protocolli, è possibile ignorare l'errore relativo al test del flusso di accesso attivo con l'endpoint di federazione attiva del provider di identità.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Verificare manualmente la corretta configurazione di Single Sign-On
La verifica manuale prevede passaggi aggiuntivi che è possibile eseguire per assicurarsi che il provider di identità SAML 2.0 funzioni correttamente in molti scenari.
Per verificare che l'accesso Single Sign-On sia stato configurato correttamente, completare i passaggi seguenti:


1. In un computer aggiunto al dominio, accedere al servizio cloud usando lo stesso nome di accesso usato per le credenziali aziendali.
2.  Fare clic all'interno della casella della password. Se l'accesso Single Sign-On è configurato, la casella della password sarà ombreggiata e verrà visualizzato un messaggio che richiede di accedere a <your company>.
3.  Fare clic sul collegamento per l'accesso a <your company>. Se è possibile eseguire l'accesso, la configurazione di Single Sign-On è corretta.

## <a name="next-steps"></a>Passaggi successivi


- [Gestione e personalizzazione di Active Directory Federation Services con Azure AD Connect](active-directory-aadconnect-federation-management.md)
- [Elenco di compatibilità di federazione di Azure AD](active-directory-aadconnect-federation-compatibility.md)
- [Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
