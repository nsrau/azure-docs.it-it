 <properties
   pageTitle="Token e i tipi di attestazione supportati | Microsoft Azure"
   description="Una guida alla comprensione e alla valutazione delle attestazioni nei token SAML 2.0 e JSON Web Tokens (JWT) emessi da Azure Active Directory (AAD)"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   services="active-directory"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/17/2015"
   ms.author="mbaldwin"/>

# Token e tipi di attestazioni supportati

Questo argomento è progettato per facilitare la comprensione e la valutazione delle attestazioni nei token SAML 2.0 e JSON Web Token (JWT) emessi da Azure Active Directory (Azure AD).

L'argomento inizia con una descrizione di ogni attestazione token e mostra un esempio di attestazione in un token SAML e un token JWT, come appropriato. Le attestazioni che sono in stato di anteprima vengono elencate separatamente. Termina con i token di esempio per visualizzare le attestazioni nel contesto.

Azure aggiunge attestazioni per i token nel tempo per attivare nuovi scenari. In genere queste attestazioni vengono introdotte nello stato di anteprima e quindi convertite nel supporto completo dopo un periodo di prova. Per preparare le modifiche di attestazione, le applicazioni che accettano i token da Azure AD devono ignorare le attestazioni nei token familiarità in modo che le nuove attestazioni non determinano l'interruzione dell'applicazione. Le applicazioni che usano le attestazioni che sono in stato di anteprima non devono dipendere da queste attestazioni e non devono generare eccezioni se l'attestazione non viene visualizzata nel token. Se per l'applicazione sono necessarie attestazioni non sono disponibili nei token SAML o JWT emesse da Azure AD, usare la sezione Aggiunte della community nella parte inferiore di questa pagina per suggerire e discutere nuovi tipi di attestazione.

## Riferimento di attestazioni token

In questa sezione vengono elencate e descritte le attestazioni nei token restituite da Azure AD. Include la versione SAML e la versione JWT dell'attestazione e una descrizione dell'attestazione e il relativo uso. Le attestazioni sono elencate in ordine alfabetico.

### ID applicazione

L'attestazione ID applicazione identifica l'applicazione che usa il token per l'accesso a una risorsa. L'applicazione può fungere per conto proprio o per conto dell'utente. L'ID dell'applicazione in genere rappresenta un oggetto applicazione, ma può anche rappresentare un oggetto di entità servizio in Azure AD.

Azure AD non supporta un'attestazione dell'ID applicazione in un token SAML.

In un token JWT l'ID dell'applicazione viene visualizzato in un'attestazione appid.

    "appid":"15CB020F-3984-482A-864D-1D92265E8268"

### Destinatari
Il gruppo di destinatari di un token è il destinatario previsto del token. L'applicazione che riceve il token deve verificare che il valore del gruppo di destinatari sia corretto e rifiuta tutti i token destinati a un gruppo di destinatari diverso.

Il valore del gruppo di destinatari è una stringa, in genere, l'indirizzo di base della risorsa cui si accede, ad esempio "https://contoso.com". Nei token di Azure AD, il gruppo di destinatari è l'URI ID app dell'applicazione che ha richiesto il token. Quando l'applicazione, ovvero il gruppo di destinatari, ha più di un URI ID app, l'URI ID app nell'attestazione del gruppo di destinatari del token corrisponde all'URI ID app nella richiesta del token. In un token SAML l'attestazione del gruppo di destinatari è definita nell'elemento Audience dell'elemento AudienceRestriction.

    <AudienceRestriction>
    <Audience>https://contoso.com</Audience>
    </AudienceRestriction>

In un token JWT il gruppo di destinatari viene visualizzato in un'attestazione aud.

    "aud":"https://contoso.com"

### Riferimento alla classe contesto di autenticazione applicazione

L'attestazione Riferimento alla classe contesto di autenticazione applicazione indica la modalità di autenticazione del client. Per un client pubblico, il valore è 0. Se vengono usati l'ID client e il segreto client, il valore è 1.

In un token JWT il valore di riferimento alla classe contesto di autenticazione viene visualizzata in un'attestazione appidacr (valore ACR specifico dell'applicazione).

    "appidacr": "0"

### Riferimento alla classe contesto di autenticazione
L'attestazione Riferimento alla classe contesto di autenticazione indica la modalità di autenticazione del soggetto, invece del client nell'attestazione di riferimento alla classe contesto di autenticazione dell'applicazione. Il valore "0" indica che l'autenticazione dell'utente finale non soddisfa i requisiti ISO/IEC 29115.

- In un token JWT l'attestazione di riferimento alla classe contesto di autenticazione viene visualizzata nell'attestazione acr (valore ACR specifico dell'utente).

    "acr": "0"

### Istante di autenticazione

L'attestazione Istante di autenticazione registra la data e ora in cui si è verificata l'autenticazione.

In un token SAML l'istante di autenticazione viene visualizzato nell'attributo AuthnInstant dell'elemento AuthnStatement. Rappresenta un valore datetime in ora UTC (Z).

    <AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">

Azure AD non dispone di un'attestazione equivalente nei token JWT.

### Metodo di autenticazione

L'attestazione Metodo di autenticazione indica come il soggetto del token è stato autenticato. In questo esempio, il provider di identità usava una password per autenticare l'utente. http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password

In un token SAML il valore del metodo di autenticazione viene visualizzato nell'elemento AuthnContextClassRef.

    <AuthnContextClassRef>http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password</AuthnContextClassRef>

In un token JWT viene visualizzato il valore del metodo di autenticazione all'interno dell'attestazione amr.

    “amr”: ["pwd"]

###Nome

L'attestazione Nome o del "nome specificato" fornisce il nome o il nome "specificato" dell'utente, come impostato nell'oggetto utente di Azure AD.

In un token SAML viene visualizzato il nome (o "nome specificato") in un'attestazione nell'elemento SAML Attribute givenname.

    <Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>
    <AttributeValue>Frank<AttributeValue>

In un token JWT il nome viene visualizzato nell'attestazione given\_name.

    "given_name": "Frank"

### Gruppi

L'attestazione Gruppi fornisce gli ID oggetto che rappresentano le appartenenze ai gruppi del soggetto. Questi valori sono univoci (vedere ID oggetto) e possono essere usati in modo sicuro per la gestione dell'accesso, ad esempio l'attivazione dell'autorizzazione per accedere a una risorsa. I gruppi inclusi nella relativa attestazione sono configurati in base all'applicazione, tramite la proprietà "groupMembershipClaims" del manifesto dell'applicazione. Un valore null escluderà tutti i gruppi, un valore "SecurityGroup" includerà solo le appartenenze al gruppo di sicurezza di Active Directory e un valore "All" includerà sia i gruppi di sicurezza che le liste di distribuzione di Office 365. In qualsiasi configurazione, l'attestazione groups rappresenta le appartenenze ai gruppi transitivi del soggetto.

In un token SAML l'attestazione dei gruppi viene visualizzata nell'attributo groups.

    <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
    <AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>

In un token JWT l'attestazione dei gruppi viene visualizzata nell'attestazione groups.

    “groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]

### Provider di identità

L'attestazione Provider di identità registra il provider di identità che ha autenticato l'oggetto del token. Questo valore è identico al valore dell'attestazione Autorità di certificazione, a meno che l'account utente non sia in un tenant diverso rispetto all'autorità di certificazione.

In un token SAML il provider di identità viene visualizzato in un'attestazione nell'elemento SAML Attribute identityprovider.

    <Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>
    <AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>

In un token JWT il provider di identità viene visualizzato in un'attestazione idp.

    "idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”

### IssuedAt

L'attestazione IssuedAt archivia l'ora in cui è stato rilasciato il token. Viene spesso usata per misurare la validità del token. In un token SAML il valore IssuedAt viene visualizzato nell'asserzione IssueInstant.

    <Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">

In un token JWT il valore IssuedAt viene visualizzato nell'asserzione iat. Il valore viene espresso nel numero di secondi trascorsi dal 010-01-1970:0:0Z in Coordinated Universal Time (UTC).

    "iat": 1390234181

### Autorità di certificazione

L'attestazione Autorità di certificazione identifica il servizio token di sicurezza (STS) che costruisce e restituisce il token e il tenant di directory di Azure AD. Nel token restituito da Azure AD l'autorità di certificazione è sts.windows.net. Il GUID del valore di attestazione dell'autorità di certificazione è l'ID tenant della directory di Azure AD. L'ID tenant è un identificatore non modificabile e affidabile della directory.

In un token SAML l'attestazione Autorità di certificazione viene visualizzata in un elemento Issuer.

    <Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>

In un token JWT l'autorità di certificazione viene visualizzata in un'attestazione iss.

    "iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”

### Cognome

L'attestazione Cognome fornisce il cognome dell'utente, come definito nell'oggetto utente di Azure AD. In un token SAML viene visualizzato il cognome in un'attestazione nell'elemento SAML Attribute surname.

    <Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>
    <AttributeValue>Miller<AttributeValue>

In un token JWT il cognome viene visualizzato nell'attestazione family\_name.

    "family_name": "Miller"

### Nome

Fornisce un valore leggibile che identifica il soggetto del token. Questo valore potrebbe non essere univoco all'interno di un tenant e può essere usato solo per scopi di visualizzazione. In un token SAML il nome viene visualizzato nell'attributo Name.

    <Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>
    <AttributeValue>frankm@contoso.com<AttributeValue>

In un'attestazione JWT il nome viene visualizzato nell'attestazione unique\_name.

    "unique_name": "frankm@contoso.com"

### ID oggetto

L'attestazione ID oggetto contiene un identificatore univoco di un oggetto in Azure AD. Questo valore non è modificabile e non può essere riassegnato o riutilizzato, pertanto è possibile usarlo per eseguire controlli di autorizzazione in modo sicuro, ad esempio quando il token viene usato per accedere a una risorsa. Usare l'ID oggetto per identificare un oggetto nelle query ad Azure AD. In un token SAML l'ID oggetto viene visualizzato nell'attributo objectidentifier.

    <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
    <AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>

In un token JWT ID oggetto viene visualizzato in un'attestazione oid.

    "oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"

### Ruoli

L'attestazione Ruoli fornisce brevi stringhe che rappresentano le assegnazioni dei ruoli applicazione del soggetto in Azure AD e possono essere usati di attivare il controllo degli accessi in base al ruolo. I ruoli applicazione sono definiti in base all'applicazione, tramite la proprietà "appRoles" del manifesto dell'applicazione. La proprietà "value" di ogni ruolo applicazione è il valore visualizzato nell'attestazione roles. I ruoli inclusi nell'attestazione roles rappresentano tutti i ruoli applicazione concessi al soggetto sia direttamente che indirettamente tramite l'appartenenza al gruppo. In un token SAML l'attestazione roles viene visualizzata nell'attributo roles.

    <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">
    <AttributeValue>Admin</AttributeValue>

In un token JWT l'attestazione roles viene visualizzata nell'attestazione roles.

    “roles”: ["Admin", … ]

### Scope

L'ambito del token indica le autorizzazioni di rappresentazione concesse all'applicazione client. L'autorizzazione predefinita è user\_impersonation. Il proprietario della risorsa protetta può registrare valori aggiuntivi in Azure AD.

In un token JWT l'ambito del token viene specificato in un'attestazione scp.

    "scp": "user_impersonation"

### Oggetto

L'oggetto del token è l'entità su cui il token asserisce informazioni, ad esempio l'utente di un'applicazione. Questo valore non è modificabile e non può essere riassegnato o riutilizzato, pertanto è possibile usarlo per eseguire controlli di autorizzazione in modo sicuro, ad esempio quando il token viene usato per accedere a una risorsa. Poiché il soggetto è sempre presente nei token emessi da Azure AD, è consigliabile l'uso di questo valore in un sistema di autorizzazione generale.

In un token SAML l'oggetto del token viene specificato nell'elemento NameID dell'elemento Subject. Il NameID è un identificatore univoco non riutilizzato del soggetto, che può essere un utente, un'applicazione o un servizio.

SubjectConfirmation non è un'attestazione. Descrive la modalità di verifica del soggetto del token. "Bearer" indica che il soggetto viene confermato dal possesso del token.

    <Subject>
    <NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>
    <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
    </Subject>

In un token JWT il soggetto viene visualizzato in un'attestazione sub.

    "sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"

### ID tenant
L'ID tenant è un identificatore non modificabile e non riutilizzabile che identifica il tenant della directory che ha emesso il token. È possibile usare questo valore per accedere alle risorse di directory specifiche del tenant in un'applicazione multi-tenant. Ad esempio, è possibile usare questo valore per identificare il tenant in una chiamata all'API Graph.

In un token SAML l'ID tenant viene visualizzato in un'attestazione nell'elemento SAML Attribute tenantid.

    <Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>
    <AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>

In un token JWT l'ID tenant viene visualizzato in un'attestazione tid.

    "tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"

### Durata del token
L'attestazione Durata del token definisce l'intervallo di tempo entro il quale un token è valido. Il servizio che convalida il token deve verificare che la data corrente sia compresa nella durata del token. In caso contrario, deve rifiutare il token. La tolleranza del servizio è fino a cinque minuti oltre l'intervallo della durata del token per tener conto di eventuali differenze di orario ("mancata sincronizzazione dell'ora") tra Azure AD e il servizio.

In un token SAML l'attestazione Durata del token è definita nell'elemento Conditions usando gli attributi NotBefore e NotOnOrAfter.

    <Conditions
    NotBefore="2013-03-18T21:32:51.261Z"
    NotOnOrAfter="2013-03-18T22:32:51.261Z"
    >

In un token JWT la durata del token è definita da attestazioni nbf (non prima) e exp (scadenza). Il valore di queste attestazioni viene espresso nel numero di secondi trascorsi dal 010-01-1970:0:0Z in Coordinated Universal Time (UTC). Per altre informazioni, vedere RFC 3339.

    "nbf":1363289634,
    "exp":1363293234

### Nome dell'entità utente
L'attestazione Nome dell'entità utente archivia il nome utente dell'entità utente.

In un token JWT il nome dell'entità utente viene visualizzato in un'attestazione upn.

    "upn": frankm@contoso.com

### Versione
L'attestazione Versione archivia il numero di versione del token. In un token JWT il nome dell'entità utente viene visualizzato in un'attestazione ver.

    "ver": "1.0"

## Token di esempio

Questa sezione consente di visualizzare esempi di token SAML e JWT restituiti da Azure AD. Questi esempi consentono di vedere le attestazioni nel contesto. Token SAML

Questo è un esempio di un tipico token SAML.

	<?xml version="1.0" encoding="UTF-8"?>
	<t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
	  <t:Lifetime>
		<wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
		<wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
	  </t:Lifetime>
	  <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
		<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
		  <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
		</EndpointReference>
	  </wsp:AppliesTo>
	  <t:RequestedSecurityToken>
		<Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
		  <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
		  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
			<ds:SignedInfo>
			  <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
			  <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
			  <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
				<ds:Transforms>
				  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
				  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
				</ds:Transforms>
				<ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
				<ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
			  </ds:Reference>
			</ds:SignedInfo>
			<ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
			<KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
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
		<SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
		  <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
		</SecurityTokenReference>
	  </t:RequestedAttachedReference>
	  <t:RequestedUnattachedReference>
		<SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
		  <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
		</SecurityTokenReference>
	  </t:RequestedUnattachedReference>
	  <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
	  <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
	  <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
	</t:RequestSecurityTokenResponse>

### Token JWT: rappresentazione dell'utente

Questo è un esempio di un tipico token Web JSON (JWT) usato in un flusso Web di rappresentazione utente. Oltre alle attestazioni, il token include un numero di versione **ver** e **appidacr**, il riferimento alla classe contesto di autenticazione, che indica la modalità di autenticazione client. Per un client pubblico, il valore è 0. Se vengono usati l'ID client o il segreto client, il valore è 1.

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

##Vedere anche

[Protocolli di autenticazione di Azure Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)

<!---HONumber=Oct15_HO3-->