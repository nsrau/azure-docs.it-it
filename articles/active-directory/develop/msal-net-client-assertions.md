---
title: Asserzioni client in Microsoft Authentication Library per .NET | Azure
description: Informazioni sul supporto delle asserzioni client firmate per le applicazioni client riservate in Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a938f85b2047ea5cceada98df6adba2c560c1a1
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278668"
---
# <a name="confidential-client-assertions"></a>Asserzioni client riservate
Per dimostrare la propria identità, le applicazioni client riservate scambiano un segreto con Azure AD. Il segreto può essere:
- Segreto client (password dell'applicazione).
- Un certificato, utilizzato per compilare un'asserzione firmata contenente attestazioni standard.

Questo segreto può anche essere un'asserzione firmata direttamente.

MSAL.NET dispone di quattro metodi per fornire le credenziali o le asserzioni all'app client riservata:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithSignedAssertion()`
- `.WithClientClaims()`

### <a name="signed-assertions"></a>Asserzioni firmate

Un'asserzione client firmata assume il formato di un JWT firmato con il payload che contiene le attestazioni di autenticazione richieste richieste da Azure AD, codificato con Base64. Per usarla:

```CSharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Le attestazioni previste da Azure AD sono:

Tipo di attestazione | Valore | Descrizione
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
      string aud = "https://login.microsoftonline.com/72f988bf-86f1-41af-hd4m-2d7cd011db47/v2.0";

      string ConfidentialClientID = "61dab2ba-145d-4b1b-8569-bf4b9aed5dhb" //client id
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

string GetAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
    X509Certificate2 certificate = ReadCertificate(config.CertificateName);

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
    string SignedAssertion = string.Concat(token, ".", signature);
    return SignedAssertion;
}
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)`per impostazione predefinita, produrrà un'asserzione firmata contenente le attestazioni previste da Azure AD più le attestazioni client aggiuntive che si desidera inviare. Ecco un frammento di codice per eseguire questa operazione.

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

Se si desidera fornire attestazioni personalizzate, incluse le attestazioni obbligatorie previste da Azure ad, passare `false` per il `mergeWithDefaultClaims` parametro.
