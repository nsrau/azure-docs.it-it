---
title: Asserzioni client (MSAL.NET) Azure
titleSuffix: Microsoft identity platform
description: Informazioni sul supporto delle asserzioni client firmate per le applicazioni client riservate in Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/18/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8c97387bfd2a362d3bf5a6b8a3252242f061da31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050284"
---
# <a name="confidential-client-assertions"></a>Asserzioni client riservate

Per dimostrare la propria identità, le applicazioni client riservate si scambiano un segreto con Azure AD. Il segreto può essere:
- Un segreto client (password dell'applicazione).
- Certificato, utilizzato per compilare un'asserzione firmata contenente attestazioni standard.

Questo segreto può anche essere direttamente un'asserzione firmata.

MSAL.NET dispone di quattro metodi per fornire credenziali o asserzioni all'app client riservata:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> Sebbene sia possibile `WithClientAssertion()` usare l'API per acquisire token per il client riservato, non è consigliabile usarlo per impostazione predefinita in quanto è più avanzato ed è progettato per gestire scenari molto specifici che non sono comuni. L'utilizzo dell'API `.WithCertificate()` consentirà a MSAL.NET di gestire questa operazione per l'utente. Questa API offre la possibilità di personalizzare la richiesta di `.WithCertificate()` autenticazione, se necessario, ma l'asserzione predefinita creata da sarà sufficiente per la maggior parte degli scenari di autenticazione. Questa API può essere utilizzata anche come soluzione alternativa in alcuni scenari in cui MSAL.NET non riesce a eseguire l'operazione di firma internamente.

### <a name="signed-assertions"></a>Asserzioni firmate

Un'asserzione client firmata assume la forma di un JWT firmato con il payload contenente le attestazioni di autenticazione richieste richieste da Azure AD, codificato Base64.A signed client assertion takes the form of a signed JWT with the payload containing the required authentication claims mandated by Azure AD, Base64 encoded. Per usarla:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Le attestazioni previste da Azure AD sono:The claims expected by Azure AD are:

Tipo di attestazione | valore | Descrizione
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | L'attestazione "aud" (gruppo di destinatari) identifica i destinatari a cui è destinato il token JWT (in questo caso Azure AD) Vedere [RFC 7519, sezione 4.1.3]
exp | Giovedì 27 giu 27 2019 15:04:17 GMT-0200 (ora legale) | L'attestazione "exp" (expiration time) identifica l'ora di scadenza a partire dalla quale o successivamente alla quale il token JWT non deve essere accettato per l'elaborazione. Vedere [RFC 7519, sezione 4.1.4]
iss | "ID client" | L'attestazione "iss" (emittente) identifica il capitale che ha emesso il JWT. L'elaborazione di questa attestazione è specifica dell'applicazione. Il valore "iss" è una stringa con distinzione tra maiuscole e minuscole contenente un valore StringOrURI. [RFC 7519, Sezione 4.1.1]
jti | (un GUID) | L'attestazione "jti" (JWT ID) fornisce un identificatore univoco per il token JWT. Il valore dell'identificatore DEVE essere assegnato in modo da garantire che vi sia una probabilità trascurabile che lo stesso valore venga assegnato accidentalmente a un oggetto dati diverso. se l'applicazione utilizza più emittenti, le collisioni DEVONO essere evitate tra i valori prodotti da emittenti diversi. L'attestazione "jti" può essere utilizzata per impedire che il token JWT venga riprodotto. Il valore "jti" è una stringa con distinzione tra maiuscole e minuscole. [RFC 7519, Sezione 4.1.7]
nbf | Giovedì 27 giu 27 2019 14:54:17 GMT-0200 (ora legale) | L'attestazione "nbf" (not before) identifica l'ora prima della quale il token JWT non deve essere accettato per l'elaborazione. [RFC 7519, Sezione 4.1.5]
sub | "ID client" | L'affermazione "sub" (oggetto) identifica l'oggetto del JWT. Le affermazioni in un JWT sono normalmente dichiarazioni sull'argomento. Il valore dell'oggetto DEVE essere definito come univoco a livello locale nel contesto dell'emittente o essere univoco a livello globale. Vedere [RFC 7519, sezione 4.1.2]

Ecco un esempio di come creare queste affermazioni:

```csharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = $"https://login.microsoftonline.com/{tenantId}/v2.0";

      string ConfidentialClientID = "00000000-0000-0000-0000-000000000000" //client id
      const uint JwtToAadLifetimeInSeconds = 60 * 10; // Ten minutes
      DateTime validFrom = DateTime.UtcNow;
      var nbf = ConvertToTimeT(validFrom);
      var exp = ConvertToTimeT(validFrom + TimeSpan.FromSeconds(JwtToAadLifetimeInSeconds));

      return new Dictionary<string, string>()
           {
                { "aud", aud },
                { "exp", exp.ToString() },
                { "iss", ConfidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "nbf", nbf.ToString() },
                { "sub", ConfidentialClientID }
            };
}
```

Ecco come creare un'asserzione client firmata:Here is how to craft a signed client assertion:

```csharp
string Encode(byte[] arg)
{
    char Base64PadCharacter = '=';
    char Base64Character62 = '+';
    char Base64Character63 = '/';
    char Base64UrlCharacter62 = '-';
    char Base64UrlCharacter63 = '_';

    string s = Convert.ToBase64String(arg);
    s = s.Split(Base64PadCharacter)[0]; // RemoveAccount any trailing padding
    s = s.Replace(Base64Character62, Base64UrlCharacter62); // 62nd char of encoding
    s = s.Replace(Base64Character63, Base64UrlCharacter63); // 63rd char of encoding

    return s;
}

string GetSignedClientAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
     X509Certificate2 certificate = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

    //Create RSACryptoServiceProvider
    var x509Key = new X509AsymmetricSecurityKey(certificate);
    var privateKeyXmlParams = certificate.PrivateKey.ToXmlString(true);
    var rsa = new RSACryptoServiceProvider();
    rsa.FromXmlString(privateKeyXmlParams);

    //alg represents the desired signing algorithm, which is SHA-256 in this case
    //kid represents the certificate thumbprint
    var header = new Dictionary<string, string>()
         {
              { "alg", "RS256"},
              { "kid", Encode(Certificate.GetCertHash()) }
         };

    //Please see the previous code snippet on how to craft claims for the GetClaims() method
    string token = Encode(Encoding.UTF8.GetBytes(JObject.FromObject(header).ToString())) + "." + Encode(Encoding.UTF8.GetBytes(JObject.FromObject(GetClaims())));

    string signature = Encode(rsa.SignData(Encoding.UTF8.GetBytes(token), new SHA256Cng()));
    string signedClientAssertion = string.Concat(token, ".", signature);
    return signedClientAssertion;
}
```

### <a name="alternative-method"></a>Metodo alternativo

È inoltre possibile utilizzare [Microsoft.IdentityModel.JsonWebTokens](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/) per creare l'asserzione automaticamente. Il codice sarà un più elegante come mostrato nell'esempio seguente:

```csharp
        string GetSignedClientAssertion()
        {
            var cert = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

            //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
            string aud = $"https://login.microsoftonline.com/{tenantID}/v2.0";

            // client_id
            string confidentialClientID = "00000000-0000-0000-0000-000000000000";

            // no need to add exp, nbf as JsonWebTokenHandler will add them by default.
            var claims = new Dictionary<string, object>()
            {
                { "aud", aud },
                { "iss", confidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "sub", confidentialClientID }
            };

            var securityTokenDescriptor = new SecurityTokenDescriptor
            {
                Claims = claims,
                SigningCredentials = new X509SigningCredentials(cert)
            };

            var handler = new JsonWebTokenHandler();
            var signedClientAssertion = handler.CreateToken(securityTokenDescriptor);
        }
```

Dopo aver firmato l'asserzione client, è possibile utilizzarla con l'API MSAL come illustrato di seguito.

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)`Per impostazione predefinita, verrà prodotto un'asserzione firmata contenente le attestazioni previste da Azure AD oltre a attestazioni client aggiuntive che si desidera inviare. Ecco un frammento di codice su come farlo.

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

Se una delle attestazioni nel dizionario passato è uguale a una delle attestazioni obbligatorie, verrà preso in considerazione il valore dell'attestazione aggiuntiva. Eseguirà l'override delle attestazioni calcolate da MSAL.NET.

Se si desidera fornire attestazioni personalizzate, incluse le attestazioni `false` obbligatorie previste da Azure AD, passare per il `mergeWithDefaultClaims` parametro.
