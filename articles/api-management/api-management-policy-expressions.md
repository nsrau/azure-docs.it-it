---
title: Espressioni di criteri di Gestione API di Azure | Microsoft Docs
description: Informazioni sulle espressioni di criteri in Gestione API di Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: 6614e70d130abe46067c657bda3ccdd7000caddc
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845281"
---
# <a name="api-management-policy-expressions"></a>Espressioni di criteri di Gestione API
Questo articolo illustra la sintassi delle espressioni C# di criteri in 7. Ogni espressione ha accesso alla variabile [context](api-management-policy-expressions.md#ContextVariables) fornita implicitamente e a un [subset](api-management-policy-expressions.md#CLRTypes) autorizzato di tipi di .NET Framework.

Per altre informazioni:

- Vedere la procedura per fornire informazioni di contesto al servizio back-end. Per fornire queste informazioni, usare i criteri per l'[impostazione del parametro di stringa di query](api-management-transformation-policies.md#SetQueryStringParameter) e l'[impostazione dell'intestazione HTTP](api-management-transformation-policies.md#SetHTTPheader).
- Vedere la procedura per usare i criteri di [convalida JWT](api-management-access-restriction-policies.md#ValidateJWT) per preautorizzare l'accesso alle operazioni in base alle attestazioni dei token.
- Vedere la procedura per usare una traccia di [API Inspector](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) (Controllo API) per visualizzare il modo in cui i criteri vengono valutati e i risultati delle valutazioni.
- Vedere la procedura per usare le espressioni con i criteri di [recupero dalla cache](api-management-caching-policies.md#GetFromCache) e di [archiviazione nella cache](api-management-caching-policies.md#StoreToCache) per configurare la memorizzazione delle risposte nella cache di Gestione API. Impostare la durata corrispondente alla memorizzazione delle risposte nella cache del servizio back-end, come specificato dalla direttiva `Cache-Control` del servizio.
- Vedere la procedura per eseguire operazioni di filtro dei contenuti. Rimuovere elementi dati dalla risposta ricevuta dal servizio back-end usando i criteri di [controllo del flusso](api-management-advanced-policies.md#choose) e [impostazione del corpo](api-management-transformation-policies.md#SetBody).
- Per scaricare gli esempi dei criteri, vedere il repository GitHub [api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies).


## <a name="Syntax"></a> Sintassi
Le espressioni a istruzione singola sono racchiuse tra `@(expression)`, dove `expression` è un'istruzione di espressione C# ben formata.

Le espressioni a più istruzioni sono racchiuse tra `@{expression}`. Tutti i percorsi di codice all'interno di espressioni a più istruzioni devono terminare con un'istruzione `return`.

## <a name="PolicyExpressionsExamples"></a> Esempi

```
@(true)

@((1+1).ToString())

@("Hi There".Length)

@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)

@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)

@{
  string[] value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
      if(value != null && value.Length > 0)
      {
          return Encoding.UTF8.GetString(Convert.FromBase64String(value[0]));
      }
  }
  return null;

}
```

## <a name="PolicyExpressionsUsage"></a>Utilizzo
Le espressioni possono essere usate come valori di attributo o valori di testo in tutti i [criteri](api-management-policies.md) di Gestione API, salvo diversamente specificato nella documentazione di riferimento.

> [!IMPORTANT]
> Quando si usano le espressioni di criteri, è prevista solo una verifica limitata di tali espressioni al momento della definizione dei criteri. Le espressioni vengono eseguite dal gateway in fase di esecuzione e le eccezioni generate determinano un errore di runtime.

## <a name="CLRTypes"></a> Tipi di .NET Framework consentiti nelle espressioni di criteri
Nella tabella seguente sono elencati i tipi di .NET Framework e i relativi membri consentiti nelle espressioni di criteri.

|Tipo|Membri supportati|
|--------------|-----------------------|
|Newtonsoft.Json.Formatting|Tutto|
|Newtonsoft.Json.JsonConvert|Per SerializeObject è, DeserializeObject|
|Newtonsoft.Json.Linq.Extensions|Tutto|
|Newtonsoft.Json.Linq.JArray|Tutto|
|Newtonsoft.Json.Linq.JConstructor|Tutto|
|Newtonsoft.Json.Linq.JContainer|Tutto|
|Newtonsoft.Json.Linq.JObject|Tutto|
|Newtonsoft.Json.Linq.JProperty|Tutto|
|Newtonsoft.Json.Linq.JRaw|Tutto|
|Newtonsoft.Json.Linq.JToken|Tutto|
|Newtonsoft.Json.Linq.JTokenType|Tutto|
|Newtonsoft.Json.Linq.JValue|Tutto|
|System.Array|Tutto|
|System.BitConverter|Tutto|
|System.Boolean|Tutto|
|System.Byte|Tutto|
|System.Char|Tutto|
|System. Collections. Generic. Dictionary < TKey, TValue >|Tutto|
|System.Collections.Generic.HashSet\<T>|Tutto|
|System.Collections.Generic.ICollection\<T>|Tutto|
|System. Collections. Generic. IDictionary < TKey, TValue >|Tutto|
|System.Collections.Generic.IEnumerable\<T>|Tutto|
|System.Collections.Generic.IEnumerator\<T>|Tutto|
|System. Collections. Generic. IList\<T >|Tutto|
|System.Collections.Generic.IReadOnlyCollection\<T>|Tutto|
|System.Collections.Generic.IReadOnlyDictionary<TKey, TValue>|Tutto|
|System.Collections.Generic.ISet\<T>|Tutto|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|Tutto|
|System.Collections.Generic.List\<T>|Tutto|
|System.Collections.Generic.Queue\<T>|Tutto|
|System.Collections.Generic.Stack\<T>|Tutto|
|System.Convert|Tutto|
|System.DateTime|(Costruttore), Add, AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, date, Day, DayOfWeek, DayOfYear, DaysInMonth, hour, IsDaylightSavingTime, IsLeapYear, MaxValue, millisecond, minute, MinValue, month, Now , Parse, Second, Subtract, TimeOfDay, Today, ToString, UtcNow, Year|
|System.DateTimeKind|UTC|
|System.DateTimeOffset|Tutto|
|System.Decimal|Tutto|
|System.Double|Tutto|
|System.Exception|Tutto|
|System.Guid|Tutto|
|System.Int16|Tutto|
|System.Int32|Tutto|
|System.Int64|Tutto|
|System.IO.StringReader|Tutto|
|System.IO.StringWriter|Tutto|
|System.Linq.Enumerable|Tutto|
|System.Math|Tutto|
|System.MidpointRounding|Tutto|
|System.Net.WebUtility|Tutto|
|System.Nullable|Tutto|
|System.Random|Tutto|
|System.SByte|Tutto|
|System.Security.Cryptography.AsymmetricAlgorithm|Tutto|
|System.Security.Cryptography.CipherMode|Tutto|
|System.Security.Cryptography.HashAlgorithm|Tutto|
|System.Security.Cryptography.HashAlgorithmName|Tutto|
|System.Security.Cryptography.HMAC|Tutto|
|System.Security.Cryptography.HMACMD5|Tutto|
|System.Security.Cryptography.HMACSHA1|Tutto|
|System.Security.Cryptography.HMACSHA256|Tutto|
|System.Security.Cryptography.HMACSHA384|Tutto|
|System.Security.Cryptography.HMACSHA512|Tutto|
|System.Security.Cryptography.KeyedHashAlgorithm|Tutto|
|System.Security.Cryptography.MD5|Tutto|
|System.Security.Cryptography.Oid|Tutto|
|System.Security.Cryptography.PaddingMode|Tutto|
|System.Security.Cryptography.RNGCryptoServiceProvider|Tutto|
|System.Security.Cryptography.RSA|Tutto|
|System.Security.Cryptography.RSAEncryptionPadding|Tutto|
|System.Security.Cryptography.RSASignaturePadding|Tutto|
|System.Security.Cryptography.SHA1|Tutto|
|System.Security.Cryptography.SHA1Managed|Tutto|
|System.Security.Cryptography.SHA256|Tutto|
|System.Security.Cryptography.SHA256Managed|Tutto|
|System.Security.Cryptography.SHA384|Tutto|
|System.Security.Cryptography.SHA384Managed|Tutto|
|System.Security.Cryptography.SHA512|Tutto|
|System.Security.Cryptography.SHA512Managed|Tutto|
|System.Security.Cryptography.SymmetricAlgorithm|Tutto|
|System.Security.Cryptography.X509Certificates.PublicKey|Tutto|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|Tutto|
|System.Security.Cryptography.X509Certificates.X500DistinguishedName|Nome|
|System.Security.Cryptography.X509Certificates.X509Certificate|Tutto|
|System.Security.Cryptography.X509Certificates.X509Certificate2|Tutto|
|System.Security.Cryptography.X509Certificates.X509ContentType|Tutto|
|System.Security.Cryptography.X509Certificates.X509NameType|Tutto|
|System.Single|Tutto|
|System.String|Tutto|
|System.StringComparer|Tutto|
|System.StringComparison|Tutto|
|System.StringSplitOptions|Tutto|
|System.Text.Encoding|Tutto|
|System.Text.RegularExpressions.Capture|Indice, lunghezza, valore|
|System.Text.RegularExpressions.CaptureCollection|Conteggio, elemento|
|System.Text.RegularExpressions.Group|Acquisizioni, esito positivo|
|System.Text.RegularExpressions.GroupCollection|Conteggio, elemento|
|System.Text.RegularExpressions.Match|Empty, Groups, Result|
|System.Text.RegularExpressions.Regex|(Costruttore), IsMatch, match, Matches, Replace, unescape, Split|
|System.Text.RegularExpressions.RegexOptions|Tutto|
|System.Text.StringBuilder|Tutto|
|System.TimeSpan|Tutto|
|System.TimeZone|Tutto|
|System.TimeZoneInfo.AdjustmentRule|Tutto|
|System.TimeZoneInfo.TransitionTime|Tutto|
|System.TimeZoneInfo|Tutto|
|System.Tuple|Tutto|
|System.UInt16|Tutto|
|System.UInt32|Tutto|
|System.UInt64|Tutto|
|System.Uri|Tutto|
|System.UriPartial|Tutto|
|System.Xml.Linq.Extensions|Tutto|
|System.Xml.Linq.XAttribute|Tutto|
|System.Xml.Linq.XCData|Tutto|
|System.Xml.Linq.XComment|Tutto|
|System.Xml.Linq.XContainer|Tutto|
|System.Xml.Linq.XDeclaration|Tutto|
|System.Xml.Linq.XDocument|All, eccetto: Load|
|System.Xml.Linq.XDocumentType|Tutto|
|System.Xml.Linq.XElement|Tutto|
|System.Xml.Linq.XName|Tutto|
|System.Xml.Linq.XNamespace|Tutto|
|System.Xml.Linq.XNode|Tutto|
|System.Xml.Linq.XNodeDocumentOrderComparer|Tutto|
|System.Xml.Linq.XNodeEqualityComparer|Tutto|
|System.Xml.Linq.XObject|Tutto|
|System.Xml.Linq.XProcessingInstruction|Tutto|
|System.Xml.Linq.XText|Tutto|
|System.Xml.XmlNodeType|Tutto|

## <a name="ContextVariables"></a> Variabile di contesto
Una variabile denominata `context` è disponibile in modo implicito in ogni [espressione](api-management-policy-expressions.md#Syntax) di criteri. I suoi membri forniscono informazioni riguardanti `\request`. Tutti i membri di `context` sono di sola lettura.

|Variabile di contesto|Metodi, proprietà e valori di parametro consentiti|
|----------------------|-------------------------------------------------------|
|contesto|[API](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Distribuzione](#ref-context-deployment)<br /><br /> Elapsed: TimeSpan - intervallo di tempo tra il valore di Timestamp e l'ora corrente<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [operazione](#ref-context-operation)<br /><br /> [Prodotto](#ref-context-product)<br /><br /> [Richiesta](#ref-context-request)<br /><br /> RequestId: Guid - identificatore univoco della richiesta<br /><br /> [Risposta](#ref-context-response)<br /><br /> [Sottoscrizione](#ref-context-subscription)<br /><br /> Timestamp: DateTime - momento di ricezione della richiesta<br /><br /> Tracing: bool - indica se la funzionalità di traccia è attiva o disattiva <br /><br /> [Utente](#ref-context-user)<br /><br /> [Variabili](#ref-context-variables): IReadOnlyDictionary < String, Object ><br /><br /> void Trace(message: string)|
|<a id="ref-context-api"></a>context.Api|Id: string<br /><br /> IsCurrentRevision: bool<br /><br />  Name: string<br /><br /> Path: string<br /><br /> Revision: string<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Version: string |
|<a id="ref-context-deployment"></a>contesto. Distribuzione|Region: string<br /><br /> ServiceName: string<br /><br /> Certificati: IReadOnlyDictionary<string, X509Certificate2>|
|<a id="ref-context-lasterror"></a>context.LastError|Source: string<br /><br /> Reason: string<br /><br /> Message: string<br /><br /> Scope: string<br /><br /> Section: string<br /><br /> Path: string<br /><br /> PolicyId: string<br /><br /> Per ulteriori informazioni su context.LastError, vedere [Gestione degli errori](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>contesto. Operazione|Id: string<br /><br /> Method: string<br /><br /> Name: string<br /><br /> UrlTemplate: string|
|<a id="ref-context-product"></a>context.Product|API: IEnumerable <[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: bool<br /><br /> Gruppi: IEnumerable <[IGroup](#ref-igroup)\><br /><br /> Id: string<br /><br /> Name: string<br /><br /> State: enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|<a id="ref-context-request"></a>context.Request|Body: [IMessageBody](#ref-imessagebody) o `null` se la richiesta non contiene un corpo.<br /><br /> Certificate: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [Intestazioni](#ref-context-request-headers): IReadOnlyDictionary < String, String [] ><br /><br /> IpAddress: string<br /><br /> MatchedParameters: IReadOnlyDictionary<string, string><br /><br /> Method: string<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> URL: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>contesto di stringa. Request. Headers. GetValueOrDefault (HeaderName: String, defaultValue: String)|headerName: string<br /><br /> defaultValue: string<br /><br /> Restituisce valori di intestazione di richiesta separati da virgole oppure `defaultValue` se non viene trovata l'intestazione.|
|<a id="ref-context-response"></a>context.Response|Corpo: [IMessageBody](#ref-imessagebody)<br /><br /> [Intestazioni](#ref-context-response-headers): IReadOnlyDictionary < String, String [] ><br /><br /> StatusCode: int<br /><br /> StatusReason: string|
|<a id="ref-context-response-headers"></a>contesto di stringa. Response. Headers. GetValueOrDefault (HeaderName: String, defaultValue: String)|headerName: string<br /><br /> defaultValue: string<br /><br /> Restituisce valori di intestazione di risposta separati da virgole oppure `defaultValue` se non viene trovata l'intestazione.|
|<a id="ref-context-subscription"></a>context.Subscription|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> Id: string<br /><br /> Key: string<br /><br /> Name: string<br /><br /> PrimaryKey: string<br /><br /> SecondaryKey: string<br /><br /> StartDate: DateTime?|
|<a id="ref-context-user"></a>context.User|Email: string<br /><br /> FirstName: string<br /><br /> Gruppi: IEnumerable <[IGroup](#ref-igroup)\><br /><br /> Id: string<br /><br /> Identità: IEnumerable <[IUserIdentity](#ref-iuseridentity)\><br /><br /> LastName: string<br /><br /> Note: string<br /><br /> RegistrationDate: DateTime|
|<a id="ref-iapi"></a>IApi|Id: string<br /><br /> Name: string<br /><br /> Path: string<br /><br /> Protocols: IEnumerable<string\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|Id: string<br /><br /> Name: string|
|<a id="ref-imessagebody"></a>IMessageBody|As < T\>(preserveContent: bool = false): where T: String, byte [], JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> I metodi `context.Request.Body.As<T>` e `context.Response.Body.As<T>` vengono usati per leggere il corpo dei messaggi di richiesta e di risposta in un tipo `T` specificato. Per impostazione predefinita, il metodo usa il flusso del corpo del messaggio originale e lo rende non disponibile dopo aver restituito un valore. Per evitarlo, far sì che il metodo venga eseguito su una copia del flusso del corpo impostando il parametro `preserveContent` su `true`. Per un esempio, vedere [qui](api-management-transformation-policies.md#SetBody).|
|<a id="ref-iurl"></a>IUrl|Host: string<br /><br /> Path: string<br /><br /> Port: int<br /><br /> [Query](#ref-iurl-query): IReadOnlyDictionary < String, String [] ><br /><br /> QueryString: string<br /><br /> Scheme: string|
|<a id="ref-iuseridentity"></a>IUserIdentity|Id: string<br /><br /> Provider: string|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Header: string<br /><br /> Query: string|
|<a id="ref-iurl-query"></a>String IUrl. query. GetValueOrDefault (queryParameterName: String, defaultValue: String)|queryParameterName: string<br /><br /> defaultValue: string<br /><br /> Restituisce valori di parametro separati da virgole oppure `defaultValue` se il parametro non viene trovato.|
|<a id="ref-context-variables"></a>Contesto T. Variables. GetValueOrDefault < T\>(variableName: String, defaultValue: T)|variableName: string<br /><br /> defaultValue: T<br /><br /> Restituisce il valore della variabile nel tipo `T` oppure `defaultValue` se la variabile non viene trovata.<br /><br /> Questo metodo genera un'eccezione se il tipo specificato non corrisponde al tipo effettivo della variabile restituita.|
|BasicAuthCredentials AsBasic(input: this string)|input: string<br /><br /> Se il parametro di input contiene un valore di intestazione di richiesta di autorizzazione Autenticazione HTTP di base valido, il metodo restituisce un oggetto di tipo `BasicAuthCredentials`; in caso contrario restituisce un valore null.|
|bool TryParseBasic(input: this string, result: out BasicAuthCredentials)|input: string<br /><br /> result: out BasicAuthCredentials<br /><br /> Se il parametro di input contiene un valore di autorizzazione Autenticazione HTTP di base valido nell'intestazione della richiesta, il metodo restituisce `true` e il parametro risultante contiene un valore di tipo `BasicAuthCredentials`; in caso contrario, il metodo restituisce `false`.|
|BasicAuthCredentials|Password: string<br /><br /> UserId: string|
|Jwt AsJwt(input: this string)|input: string<br /><br /> Se il parametro di input contiene un valore di token JWT valido, il metodo restituisce un oggetto di tipo `Jwt`; in caso contrario restituisce `null`.|
|bool TryParseJwt(input: this string, result: out Jwt)|input: string<br /><br /> result: out Jwt<br /><br /> Se il parametro di input contiene un valore di token JWT valido, il metodo restituisce `true` e il parametro del risultato contiene un valore di tipo `Jwt`; in caso contrario il metodo restituisce `false`.|
|Jwt|Algorithm: string<br /><br /> Audience: IEnumerable<string\><br /><br /> Claims: IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime: DateTime?<br /><br /> Id: string<br /><br /> Issuer: string<br /><br /> Issuedat è incluso: DateTime?<br /><br /> NotBefore: DateTime?<br /><br /> Subject: string<br /><br /> Type: string|
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: string<br /><br /> defaultValue: string<br /><br /> Restituisce valori di attestazione separati da virgole oppure `defaultValue` se non viene trovata l'intestazione.|
|byte[] Encrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - testo non crittografato da crittografare<br /><br />alg - nome di un algoritmo di crittografia simmetrica<br /><br />key - chiave di crittografia<br /><br />iv - vettore di inizializzazione<br /><br />Restituisce testo normale crittografato.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input - testo non crittografato da crittografare<br /><br />alg - algoritmo di crittografia<br /><br />Restituisce testo normale crittografato.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - testo non crittografato da crittografare<br /><br />alg - algoritmo di crittografia<br /><br />key - chiave di crittografia<br /><br />iv - vettore di inizializzazione<br /><br />Restituisce testo normale crittografato.|
|byte[] Decrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - messaggio cifrato da decrittografare<br /><br />alg - nome di un algoritmo di crittografia simmetrica<br /><br />key - chiave di crittografia<br /><br />iv - vettore di inizializzazione<br /><br />Restituisce testo normale non crittografato.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - messaggio cifrato da decrittografare<br /><br />alg - algoritmo di crittografia<br /><br />Restituisce testo normale non crittografato.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - messaggio cifrato da decrittografare<br /><br />alg - algoritmo di crittografia<br /><br />key - chiave di crittografia<br /><br />iv - vettore di inizializzazione<br /><br />Restituisce testo normale non crittografato.|
|bool VerifyNoRevocation (input: This System. Security. Cryptography. X509Certificates. X509Certificate2)|Esegue una convalida della catena X. 509 senza controllare lo stato di revoca del certificato.<br /><br />input-certificate-oggetto<br /><br />Restituisce `true` se la convalida ha esito positivo; `false` se la convalida ha esito negativo.|


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di questi criteri, vedere:

+ [Criteri di Gestione API](api-management-howto-policies.md)
+ [Trasformare le API](transform-api.md)
+ [Informazioni di riferimento sui criteri](api-management-policy-reference.md) per un elenco completo delle istruzioni dei criteri e delle relative impostazioni
+ [Esempi di criteri](policy-samples.md)
