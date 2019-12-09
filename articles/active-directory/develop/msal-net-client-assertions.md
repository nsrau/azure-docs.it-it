---
title: Asserzioni client (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sul supporto delle asserzioni client firmate per le applicazioni client riservate in Microsoft Authentication Library per .NET (MSAL.NET).
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4731a7265265c48bed02e836de91d61971b9be14
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921912"
---
# <a name="confidential-client-assertions"></a>Asserzioni client riservate

Per dimostrare la propria identità, le applicazioni client riservate scambiano un segreto con Azure AD. Il segreto può essere:
- Segreto client (password dell'applicazione).
- Un certificato, utilizzato per compilare un'asserzione firmata contenente attestazioni standard.

Questo segreto può anche essere un'asserzione firmata direttamente.

MSAL.NET dispone di quattro metodi per fornire le credenziali o le asserzioni all'app client riservata:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> Sebbene sia possibile usare l'API `WithClientAssertion()` per acquisire i token per il client riservato, non è consigliabile usarla per impostazione predefinita perché è più avanzata ed è progettata per gestire scenari molto specifici che non sono comuni. L'uso dell'API `.WithCertificate()` consentirà a MSAL.NET di gestire questa operazione. Questa API offre la possibilità di personalizzare la richiesta di autenticazione, se necessario, ma l'asserzione predefinita creata da `.WithCertificate()` sarà sufficiente per la maggior parte degli scenari di autenticazione. Questa API può essere usata anche come soluzione alternativa in alcuni scenari in cui MSAL.NET non riesce a eseguire l'operazione di firma internamente.

### <a name="signed-assertions"></a>Asserzioni firmate

Un'asserzione client firmata assume il formato di un JWT firmato con il payload che contiene le attestazioni di autenticazione richieste richieste da Azure AD, codificato con Base64. Per usarla:

```CSharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Le attestazioni previste da Azure AD sono:

Tipo di attestazione | Value | Description
---------- | ---------- | ----------
aud | https://login.microsoftonline.com/{tenantId}/v2.0 | L'attestazione "AUD" (audience) identifica i destinatari a cui è destinato il JWT (qui Azure AD) vedere [RFC 7519, sezione 4.1.3]
exp | Gio giu 27 2019 15:04:17 GMT + 0200 (Romance Daylight Time) | L'attestazione "exp" (expiration time) identifica l'ora di scadenza a partire dalla quale o successivamente alla quale il token JWT non deve essere accettato per l'elaborazione. Vedere [RFC 7519, sezione 4.1.4]
iss | ClientID | L'attestazione "ISS" (emittente) identifica l'entità che ha emesso il JWT. L'elaborazione di questa attestazione è specifica dell'applicazione. Il valore "ISS" è una stringa con distinzione tra maiuscole e minuscole contenente un valore StringOrURI. [RFC 7519, sezione 4.1.1]
jti | (Guid) | L'attestazione "ITC" (ID JWT) fornisce un identificatore univoco per il JWT. Il valore dell'identificatore deve essere assegnato in modo da garantire che esista una probabilità trascurabile che lo stesso valore verrà assegnato accidentalmente a un oggetto dati diverso. Se l'applicazione usa più autorità emittenti, è necessario impedire conflitti tra i valori prodotti da autorità di certificazione diverse. L'attestazione "ITC" può essere usata per impedire la riproduzione del JWT. Il valore "ITC" è una stringa con distinzione tra maiuscole e minuscole. [RFC 7519, sezione 4.1.7]
nbf | Gio giu 27 2019 14:54:17 GMT + 0200 (Romance Daylight Time) | L'attestazione "nbf" (not before) identifica l'ora prima della quale il token JWT non deve essere accettato per l'elaborazione. [RFC 7519, sezione 4.1.5]
sub | ClientID | L'attestazione "Sub" (Subject) identifica l'oggetto del JWT. Le attestazioni in un JWT sono in genere istruzioni relative all'oggetto. È necessario che il valore dell'oggetto sia definito localmente come univoco nel contesto dell'emittente o sia univoco a livello globale. Vedere [RFC 7519, sezione 4.1.2]

Di seguito è riportato un esempio di creazione di queste attestazioni:

```CSharp
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

Di seguito viene illustrato come creare un'asserzione client firmata:

```CSharp
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

È anche possibile usare [Microsoft. IdentityModel. JsonWebTokens](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/) per creare l'asserzione. Il codice sarà più elegante, come illustrato nell'esempio seguente:

```CSharp
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

Una volta eseguita l'asserzione client firmata, è possibile usarla con le API MSAL, come illustrato di seguito.

```CSharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)` per impostazione predefinita produrrà un'asserzione firmata contenente le attestazioni previste da Azure AD e altre attestazioni client che si desidera inviare. Ecco un frammento di codice per eseguire questa operazione.

```CSharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

Se una delle attestazioni nel dizionario passato è uguale a una delle attestazioni obbligatorie, verrà considerato il valore dell'attestazione aggiuntiva. Eseguirà l'override delle attestazioni calcolate da MSAL.NET.

Se si desidera fornire attestazioni personalizzate, incluse le attestazioni obbligatorie previste da Azure AD, passare `false` per il parametro `mergeWithDefaultClaims`.
