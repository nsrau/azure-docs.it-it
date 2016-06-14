<properties
	pageTitle="Protocollo SAML per Single Sign-On di Azure | Microsoft Azure"
	description="Questo articolo illustra il protocollo SAML per Single Sign-On in Azure Active Directory"
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="priyamo"/>

# Protocollo SAML per Single Sign-On

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

Questo articolo illustra le richieste di autenticazione SAML 2.0 e le risposte supportate da Azure Active Directory (Azure AD) per Single Sign-On.

Il diagramma del protocollo di seguito descrive la sequenza di accesso Single Sign-On. Il servizio cloud (provider di servizi) usa un'associazione di reindirizzamento HTTP per passare un elemento `AuthnRequest` (richiesta di autenticazione) ad Azure AD (provider di identità). Azure AD usa quindi una un'associazione HTTP Post per inviare un elemento `Response` al servizio cloud.

![Flusso di lavoro di Single Sign-On](media/active-directory-single-sign-on-protocol-reference/active-directory-saml-single-sign-on-workflow.png)

## AuthnRequest

Per richiedere un'autenticazione utente, i servizi cloud inviano un elemento `AuthnRequest` ad Azure AD. Un esempio di SAML 2.0 `AuthnRequest` può essere simile al seguente:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```


| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | --------------- |
| ID | Obbligatoria | Azure AD usa questo attributo per popolare l'attributo `InResponseTo` della risposta restituita. L'ID non deve iniziare con un numero, quindi una strategia comune consiste nell'anteporre una stringa come "id" alla rappresentazione di stringa di un GUID. Ad esempio, `id6c1c178c166d486687be4aaf5e482730` è un ID valido. |
| Versione | Obbligatoria | Deve essere **2.0**.|
| IssueInstant | Obbligatoria | Stringa DateTime con un valore UTC e [formato round trip ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD prevede un valore DateTime di questo tipo, ma non valuta o usa il valore. |
| AssertionConsumerServiceUrl | Facoltativa | Se specificato deve corrispondere al valore `RedirectUri` del servizio cloud in Azure AD. |
| ForceAuthn | Facoltativa | Se specificato deve essere false. Qualsiasi altro valore causa un errore.|
| IsPassive | Facoltativa | Se specificato deve essere false. Qualsiasi altro valore causa un errore. |  

Tutti gli altri attributi `AuthnRequest`, ad esempio Consent, Destination, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex e ProviderName, vengono **ignorati**.

Azure AD ignora anche l'elemento `Conditions` in `AuthnRequest`.

### Autorità di certificazione

L'elemento `Issuer` in una `AuthnRequest` deve corrispondere esattamente a uno dei **ServicePrincipalNames** nel servizio cloud in Azure AD. Viene in genere impostato sull'**URI ID app** specificato durante la registrazione dell'applicazione.

Un estratto di codice SAML di esempio contenente l'elemento `Issuer` è simile al seguente:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### NameIDPolicy

Questo elemento richiede un formato di ID nome specifico nella risposta ed è facoltativo negli elementi `AuthnRequest` inviati ad Azure AD.

Un elemento `NameIdPolicy` di esempio è simile al seguente:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Se viene specificato `NameIDPolicy` è possibile includere l'attributo facoltativo `Format`. L'attributo `Format` può avere solo uno dei seguenti valori; qualsiasi altro valore causa un errore.

-  `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory rilascia l'attestazione NameID come identificatore pairwise.
- `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory rilascia l'attestazione NameID in formato di indirizzo di posta elettronica.
- `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Questo valore consente ad Azure Active Directory di selezionare il formato dell'attestazione. Azure Active Directory rilascia NameID come identificatore pairwise.

Non includere l'attributo `SPNameQualifer`. Azure AD ignora l'attributo `AllowCreate`.

### RequestAuthnContext

L'elemento `RequestedAuthnContext` specifica i metodi di autenticazione. È facoltativo negli elementi `AuthnRequest` inviati ad Azure AD. Azure AD supporta un solo valore `AuthnContextClassRef`: `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### Scoping

L'elemento `Scoping`, che include un elenco di provider di identità, è facoltativo negli elementi `AuthnRequest` inviati ad Azure AD.

Se viene specificato, non includere l'attributo `ProxyCount` e l'elemento `IDPListOption` o `RequesterID` perché non sono supportati.

### Firma

Non includere un elemento `Signature` negli elementi `AuthnRequest`perché Azure AD non supporta le richieste di autenticazione firmate.

### Oggetto

Azure AD ignora l'elemento `Subject` degli elementi `AuthnRequest`.

## Risposta

Quando viene completato un accesso richiesto, Azure AD invia una risposta al servizio cloud. Un esempio di risposta a un tentativo di accesso riuscito è simile al seguente:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### Risposta

L'elemento `Response` include il risultato della richiesta di autorizzazione. Azure AD imposta i valori `ID`, `Version` e `IssueInstant` nell'elemento `Response`. Imposta anche gli attributi seguenti:

- `Destination`: quando l'accesso viene completato correttamente, questo attributo viene impostato sul `RedirectUri` del provider di servizi (servizio cloud).
- `InResponseTo`: viene impostato sull'attributo `ID` dell'elemento `AuthnRequest` che ha avviato la risposta.

### Autorità di certificazione

Azure AD imposta l'elemento `Issuer` su `https://login.microsoftonline.com/<TenantIDGUID>/`, dove <TenantIDGUID> è l'ID del tenant di Azure AD.

Una risposta di esempio con elemento Issuer può avere un aspetto simile al seguente:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### Firma

Azure AD firma l'elemento `Response` quando l'accesso viene completato. L'elemento `Signature` contiene una firma digitale che l'applicazione può usare per autenticare l'origine e verificare l'integrità della risposta.

Azure AD usa la chiave di firma specificata nell'elemento `IDPSSODescriptor` del relativo documento metadati. Per altre informazioni vedere [Documento di metadati della federazione](active-directory-federation-metadata.md).

Azure AD firma anche l'elemento `Assertion`, ma i due elementi di firma sono indipendenti.

Un esempio di elemento `Signature` nella risposta può essere simile al seguente:

```
<ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
```

### Stato

L'elemento `Status` indica l'esito positivo o negativo dell'accesso. Include l'elemento `StatusCode` che contiene un codice o un set di codici nidificati che rappresentano lo stato della richiesta. Include anche l'elemento `StatusMessage` che contiene i messaggi di errore personalizzati generati durante il processo di accesso.

<!-- TODO: Add a authentication protocol error reference -->

Di seguito è indicata una risposta SAML a un tentativo di accesso non riuscito.

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### Assertion

Oltre a `ID`, `IssueInstant` e `Version`, Azure AD imposta gli elementi seguenti nell'elemento `Assertion` della risposta.

#### Autorità di certificazione

Viene impostato su `https://sts.windows.net/<TenantIDGUID>/`, dove <TenantIDGUID> è l'ID del tenant di Azure AD.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### Firma

Azure AD firma l'asserzione in risposta a un accesso riuscito. L'elemento `Signature` contiene una firma digitale che il servizio cloud può usare per autenticare l'origine e verificare l'integrità dell'asserzione.

Per generare questa firma digitale, Azure AD usa la chiave di firma specificata nell'elemento `IDPSSODescriptor` del relativo documento metadati.

```
<ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### Oggetto

Specifica l'entità oggetto delle istruzioni nell'asserzione. Contiene un elemento `NameID` che rappresenta l'utente autenticato. Il valore `NameID` è un identificatore di destinazione che viene indirizzato solo al provider di servizi destinatario del token. È persistente: può essere revocato, ma mai riassegnato. È anche opaco perché non rivela alcun dettaglio relativo all'utente e non può essere usato come identificatore per le query di attributi.

L'attributo `Method` dell'elemento `SubjectConfirmation` è sempre impostato su `urn:oasis:names:tc:SAML:2.0:cm:bearer`.

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### Condizioni

Questo elemento specifica le condizioni che definiscono l'uso accettabile delle asserzioni SAML.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

Gli attributi `NotBefore` e `NotOnOrAfter` specificano l'intervallo durante il quale l'asserzione è valida.

- Il valore dell'attributo `NotBefore` è uguale o leggermente successivo (meno di un secondo) al valore dell'attributo `IssueInstant` dell'elemento `Assertion`. Azure AD non tiene conto dell'eventuale differenza di orario con il servizio cloud (provider di servizi) e non aggiunge alcun buffer a questo orario.
- Il valore dell'attributo `NotOnOrAfter` è successivo di 70 minuti al valore dell'attributo `NotBefore`.

#### Destinatari

Contiene un URI che identifica un gruppo di destinatari. Azure AD imposta il valore di questo elemento sul valore dell'elemento `Issuer` della `AuthnRequest` che ha avviato l'accesso. Per valutare valore di `Audience`, usare il valore di `App ID URI` specificato durante la registrazione dell'applicazione.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Come il valore `Issuer`, il valore `Audience` deve corrispondere esattamente a uno dei nomi dell'entità servizio che rappresenta il servizio cloud in Azure AD. Se tuttavia il valore dell'elemento `Issuer` non è un valore URI, il valore `Audience` nella risposta è il valore `Issuer` preceduto da `spn:`.

#### AttributeStatement

Contiene attestazioni relative all'oggetto o all'utente. L'estratto seguente contiene un esempio di elemento `AttributeStatement`. I puntini di sospensione indicano che l'elemento può includere più attributi e valori di attributo.

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```		

- **Attestazione Name** : il valore dell'attributo `Name`, `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`, è il nome entità dell'utente autenticato, ad esempio `testuser@managedtenant.com`.
- **Attestazione ObjectIdentifier** : il valore dell'attributo `ObjectIdentifier`, `http://schemas.microsoft.com/identity/claims/objectidentifier`, è l'`ObjectId` dell'oggetto directory che rappresenta l'utente autenticato in Azure AD. `ObjectId` è un identificatore non modificabile, globalmente univoco e riutilizzabile in modo sicuro dell'utente autenticato.

#### AuthnStatement

Questo elemento asserisce che l'oggetto dell'asserzione è stato autenticato in un determinato modo e in un determinato momento.

- L'attributo `AuthnInstant` specifica il momento in cui l'utente ha eseguito l'autenticazione con Azure AD.
- L'elemento `AuthnContext` specifica il contesto di autenticazione usato per autenticare l'utente.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```

<!---HONumber=AcomDC_0601_2016-->