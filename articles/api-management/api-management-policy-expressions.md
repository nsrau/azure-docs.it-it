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
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: 22be5509a93d0713b8113ba17debfda3cf576006
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508967"
---
# <a name="api-management-policy-expressions"></a>Espressioni di criteri di Gestione API
Questo articolo illustra la sintassi delle espressioni di criteri, che è C# 7. Ogni espressione ha accesso alla variabile [context](api-management-policy-expressions.md#ContextVariables) fornita implicitamente e a un [subset](api-management-policy-expressions.md#CLRTypes) autorizzato di tipi di .NET Framework.

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
  string value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
    return Encoding.UTF8.GetString(Convert.FromBase64String(value));
  }
  else
  {
    return null;
  }
}
```

## <a name="PolicyExpressionsUsage"></a>Utilizzo
Le espressioni possono essere usate come valori di attributo o valori di testo in tutti i [criteri](api-management-policies.md) di Gestione API, salvo diversamente specificato nella documentazione di riferimento.

> [!IMPORTANT]
> Quando si usano le espressioni di criteri, è prevista solo una verifica limitata di tali espressioni al momento della definizione dei criteri. Le espressioni vengono eseguite dal gateway in fase di esecuzione e le eccezioni generate determinano un errore di runtime.

## <a name="CLRTypes"></a> Tipi di .NET Framework consentiti nelle espressioni di criteri
Nella tabella seguente sono elencati i tipi di .NET Framework e i relativi membri consentiti nelle espressioni di criteri.

|Type|Membri supportati|
|--------------|-----------------------|
|Newtonsoft.Json.Formatting|Tutti|
|Newtonsoft.Json.JsonConvert|SerializeObject, DeserializeObject|
|Newtonsoft.Json.Linq.Extensions|Tutti|
|Newtonsoft.Json.Linq.JArray|Tutti|
|Newtonsoft.Json.Linq.JConstructor|Tutti|
|Newtonsoft.Json.Linq.JContainer|Tutti|
|Newtonsoft.Json.Linq.JObject|Tutti|
|Newtonsoft.Json.Linq.JProperty|Tutti|
|Newtonsoft.Json.Linq.JRaw|Tutti|
|Newtonsoft.Json.Linq.JToken|Tutti|
|Newtonsoft.Json.Linq.JTokenType|Tutti|
|Newtonsoft.Json.Linq.JValue|Tutti|
|System.Array|Tutti|
|System.BitConverter|Tutti|
|System.Boolean|Tutti|
|System.Byte|Tutti|
|System.Char|Tutti|
|System.Collections.Generic.Dictionary<TKey, TValue>|Tutti|
|System.Collections.Generic.HashSet\<T>|Tutti|
|System.Collections.Generic.ICollection\<T>|Tutti|
|System.Collections.Generic.IDictionary<TKey, TValue>|Tutti|
|System.Collections.Generic.IEnumerable\<T>|Tutti|
|System.Collections.Generic.IEnumerator\<T>|Tutti|
|System.Collections.Generic.IList\<T>|Tutti|
|System.Collections.Generic.IReadOnlyCollection\<T>|Tutti|
|System.Collections.Generic.IReadOnlyDictionary<TKey, TValue>|Tutti|
|System.Collections.Generic.ISet\<T>|Tutti|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|Tutti|
|System.Collections.Generic.List\<T>|Tutti|
|System.Collections.Generic.Queue\<T>|Tutti|
|System.Collections.Generic.Stack\<T>|Tutti|
|System.Convert|Tutti|
|System.DateTime|(Costruttore), aggiungere, AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, data, giorno, DayOfWeek, DayOfYear, DaysInMonth, ora, IsDaylightSavingTime, IsLeapYear, MaxValue, relativo ai millisecondi, minuto, MinValue, mese, a questo punto , Analisi, in secondo luogo, sottrarre, segni di graduazione, TimeOfDay, oggi, ToString, UtcNow, anno|
|System.DateTimeKind|UTC|
|System.DateTimeOffset|Tutti|
|System.Decimal|Tutti|
|System.Double|Tutti|
|System.Exception|Tutti|
|System.Guid|Tutti|
|System.Int16|Tutti|
|System.Int32|Tutti|
|System.Int64|Tutti|
|System.IO.StringReader|Tutti|
|System.IO.StringWriter|Tutti|
|System.Linq.Enumerable|Tutti|
|System.Math|Tutti|
|System.MidpointRounding|Tutti|
|System.Net.WebUtility|Tutti|
|System.Nullable|Tutti|
|System.Random|Tutti|
|System.SByte|Tutti|
|System.Security.Cryptography.AsymmetricAlgorithm|Tutti|
|System.Security.Cryptography.CipherMode|Tutti|
|System.Security.Cryptography.HashAlgorithm|Tutti|
|System.Security.Cryptography.HashAlgorithmName|Tutti|
|System.Security.Cryptography.HMAC|Tutti|
|System.Security.Cryptography.HMACMD5|Tutti|
|System.Security.Cryptography.HMACSHA1|Tutti|
|System.Security.Cryptography.HMACSHA256|Tutti|
|System.Security.Cryptography.HMACSHA384|Tutti|
|System.Security.Cryptography.HMACSHA512|Tutti|
|System.Security.Cryptography.KeyedHashAlgorithm|Tutti|
|System.Security.Cryptography.MD5|Tutti|
|System.Security.Cryptography.Oid|Tutti|
|System.Security.Cryptography.PaddingMode|Tutti|
|System.Security.Cryptography.RNGCryptoServiceProvider|Tutti|
|System.Security.Cryptography.RSA|Tutti|
|System.Security.Cryptography.RSAEncryptionPadding|Tutti|
|System.Security.Cryptography.RSASignaturePadding|Tutti|
|System.Security.Cryptography.SHA1|Tutti|
|System.Security.Cryptography.SHA1Managed|Tutti|
|System.Security.Cryptography.SHA256|Tutti|
|System.Security.Cryptography.SHA256Managed|Tutti|
|System.Security.Cryptography.SHA384|Tutti|
|System.Security.Cryptography.SHA384Managed|Tutti|
|System.Security.Cryptography.SHA512|Tutti|
|System.Security.Cryptography.SHA512Managed|Tutti|
|System.Security.Cryptography.SymmetricAlgorithm|Tutti|
|System.Security.Cryptography.X509Certificates.PublicKey|Tutti|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|Tutti|
|System.Security.Cryptography.X509Certificates.X500DistinguishedName|NOME|
|System.Security.Cryptography.X509Certificates.X509Certificate|Tutti|
|System.Security.Cryptography.X509Certificates.X509Certificate2|Tutti|
|System.Security.Cryptography.X509Certificates.X509ContentType|Tutti|
|System.Security.Cryptography.X509Certificates.X509NameType|Tutti|
|System.Single|Tutti|
|System.String|Tutti|
|System.StringComparer|Tutti|
|System.StringComparison|Tutti|
|System.StringSplitOptions|Tutti|
|System.Text.Encoding|Tutti|
|System.Text.RegularExpressions.Capture|Indice, lunghezza, valore|
|System.Text.RegularExpressions.CaptureCollection|Conteggio, elemento|
|System.Text.RegularExpressions.Group|Acquisizioni, esito positivo|
|System.Text.RegularExpressions.GroupCollection|Conteggio, elemento|
|System.Text.RegularExpressions.Match|Empty, Groups, Result|
|System.Text.RegularExpressions.Regex|(Costruttore), IsMatch, corrispondenza, corrispondenze, sostituire, Unescape, divisa|
|System.Text.RegularExpressions.RegexOptions|Tutti|
|System.Text.StringBuilder|Tutti|
|System.TimeSpan|Tutti|
|System.TimeZone|Tutti|
|System.TimeZoneInfo.AdjustmentRule|Tutti|
|System.TimeZoneInfo.TransitionTime|Tutti|
|System.TimeZoneInfo|Tutti|
|System.Tuple|Tutti|
|System.UInt16|Tutti|
|System.UInt32|Tutti|
|System.UInt64|Tutti|
|System.Uri|Tutti|
|System.UriPartial|Tutti|
|System.Xml.Linq.Extensions|Tutti|
|System.Xml.Linq.XAttribute|Tutti|
|System.Xml.Linq.XCData|Tutti|
|System.Xml.Linq.XComment|Tutti|
|System.Xml.Linq.XContainer|Tutti|
|System.Xml.Linq.XDeclaration|Tutti|
|System.Xml.Linq.XDocument|Tutti, ad eccezione di: Caricamento|
|System.Xml.Linq.XDocumentType|Tutti|
|System.Xml.Linq.XElement|Tutti|
|System.Xml.Linq.XName|Tutti|
|System.Xml.Linq.XNamespace|Tutti|
|System.Xml.Linq.XNode|Tutti|
|System.Xml.Linq.XNodeDocumentOrderComparer|Tutti|
|System.Xml.Linq.XNodeEqualityComparer|Tutti|
|System.Xml.Linq.XObject|Tutti|
|System.Xml.Linq.XProcessingInstruction|Tutti|
|System.Xml.Linq.XText|Tutti|
|System.Xml.XmlNodeType|Tutti|

## <a name="ContextVariables"></a> Variabile di contesto
Una variabile denominata `context` è disponibile in modo implicito in ogni [espressione](api-management-policy-expressions.md#Syntax) di criteri. I suoi membri forniscono informazioni riguardanti `\request`. Tutti i membri di `context` sono di sola lettura.

|Variabile di contesto|Metodi, proprietà e valori di parametro consentiti|
|----------------------|-------------------------------------------------------|
|context|[Api](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Distribuzione](#ref-context-deployment)<br /><br /> Elapsed: TimeSpan - intervallo di tempo tra il valore di Timestamp e l'ora corrente<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [operazione](#ref-context-operation)<br /><br /> [Prodotto](#ref-context-product)<br /><br /> [Richiesta](#ref-context-request)<br /><br /> RequestId: Guid - identificatore univoco della richiesta<br /><br /> [Risposta](#ref-context-response)<br /><br /> [Sottoscrizione](#ref-context-subscription)<br /><br /> Timestamp: DateTime - data/ora di ricezione della richiesta<br /><br /> Tracing: bool - indica se la funzionalità di traccia è attiva o disattiva <br /><br /> [Utente](#ref-context-user)<br /><br /> [Variabili](#ref-context-variables): IReadOnlyDictionary<string, object><br /><br /> void Trace(message: string)|
|<a id="ref-context-api"></a>context.Api|Id: string<br /><br /> IsCurrentRevision: bool<br /><br />  Name: string<br /><br /> Path: string<br /><br /> Revision: string<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Version: string |
|<a id="ref-context-deployment"></a>context.Deployment|Region: string<br /><br /> ServiceName: string<br /><br /> Certificates: IReadOnlyDictionary<string, X509Certificate2>|
|<a id="ref-context-lasterror"></a>context.LastError|Source: string<br /><br /> Reason: string<br /><br /> Message: string<br /><br /> Scope: string<br /><br /> Section: string<br /><br /> Path: string<br /><br /> PolicyId: string<br /><br /> Per ulteriori informazioni su context.LastError, vedere [Gestione degli errori](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>context.Operation|Id: string<br /><br /> Method: string<br /><br /> Name: string<br /><br /> UrlTemplate: string|
|<a id="ref-context-product"></a>context.Product|Apis: Oggetto IEnumerable <[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: bool<br /><br /> Groups: Oggetto IEnumerable <[IGroup](#ref-igroup)\><br /><br /> Id: string<br /><br /> Name: string<br /><br /> State: enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|<a id="ref-context-request"></a>context.Request|Corpo: [IMessageBody](#ref-imessagebody) o `null` se richiesta non ha un corpo.<br /><br /> Certificate: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [Intestazioni](#ref-context-request-headers): IReadOnlyDictionary<string, string[]><br /><br /> IpAddress: string<br /><br /> MatchedParameters: IReadOnlyDictionary<string, string><br /><br /> Method: string<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> Url: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>string context.Request.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Restituisce valori di intestazione di richiesta separati da virgole oppure `defaultValue` se non viene trovata l'intestazione.|
|<a id="ref-context-response"></a>context.Response|Corpo: [IMessageBody](#ref-imessagebody)<br /><br /> [Intestazioni](#ref-context-response-headers): IReadOnlyDictionary<string, string[]><br /><br /> StatusCode: int<br /><br /> StatusReason: string|
|<a id="ref-context-response-headers"></a>string context.Response.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Restituisce valori di intestazione di risposta separati da virgole oppure `defaultValue` se non viene trovata l'intestazione.|
|<a id="ref-context-subscription"></a>context.Subscription|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> Id: string<br /><br /> Key: string<br /><br /> Name: string<br /><br /> PrimaryKey: string<br /><br /> SecondaryKey: string<br /><br /> StartDate: DateTime?|
|<a id="ref-context-user"></a>context.User|Email: string<br /><br /> FirstName: string<br /><br /> Groups: Oggetto IEnumerable <[IGroup](#ref-igroup)\><br /><br /> Id: string<br /><br /> Identities: Oggetto IEnumerable <[IUserIdentity](#ref-iuseridentity)\><br /><br /> LastName: string<br /><br /> Note: string<br /><br /> RegistrationDate: DateTime|
|<a id="ref-iapi"></a>IApi|Id: string<br /><br /> Name: string<br /><br /> Path: string<br /><br /> Protocols: IEnumerable<string\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|Id: string<br /><br /> Name: string|
|<a id="ref-imessagebody"></a>IMessageBody|As<T\>(preserveContent: bool = false): Where T: string, JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> I metodi `context.Request.Body.As<T>` e `context.Response.Body.As<T>` vengono usati per leggere il corpo dei messaggi di richiesta e di risposta in un tipo `T` specificato. Per impostazione predefinita, il metodo usa il flusso del corpo del messaggio originale e lo rende non disponibile dopo aver restituito un valore. Per evitarlo, far sì che il metodo venga eseguito su una copia del flusso del corpo impostando il parametro `preserveContent` su `true`. Per un esempio, vedere [qui](api-management-transformation-policies.md#SetBody).|
|<a id="ref-iurl"></a>IUrl|Host: string<br /><br /> Path: string<br /><br /> Port: int<br /><br /> [Query](#ref-iurl-query): IReadOnlyDictionary<string, string[]><br /><br /> QueryString: string<br /><br /> Scheme: string|
|<a id="ref-iuseridentity"></a>IUserIdentity|Id: string<br /><br /> Provider: string|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Header: string<br /><br /> Query: string|
|<a id="ref-iurl-query"></a>string IUrl.Query.GetValueOrDefault(queryParameterName: string, defaultValue: string)|queryParameterName: string<br /><br /> defaultValue: string<br /><br /> Restituisce valori di parametro separati da virgole oppure `defaultValue` se il parametro non viene trovato.|
|<a id="ref-context-variables"></a>T context.Variables.GetValueOrDefault<T\>(variableName: string, defaultValue: T)|variableName: string<br /><br /> defaultValue: T<br /><br /> Restituisce il valore della variabile nel tipo `T` oppure `defaultValue` se la variabile non viene trovata.<br /><br /> Questo metodo genera un'eccezione se il tipo specificato non corrisponde al tipo effettivo della variabile restituita.|
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
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input - messaggio cifrato da decrittografare<br /><br />alg - algoritmo di crittografia<br /><br />Restituisce testo normale non crittografato.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - messaggio cifrato da decrittografare<br /><br />alg - algoritmo di crittografia<br /><br />key - chiave di crittografia<br /><br />iv - vettore di inizializzazione<br /><br />Restituisce testo normale non crittografato.|
|bool VerifyNoRevocation(input: this System.Security.Cryptography.X509Certificates.X509Certificate2)|Esegue una convalida della catena X.509 senza controllare lo stato di revoca di certificato.<br /><br />input - oggetto certificato<br /><br />Restituisce `true` se la convalida ha esito positivo; `false` se la convalida non riesce.|


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di questi criteri, vedere:

+ [Criteri di Gestione API](api-management-howto-policies.md)
+ [Trasformare le API](transform-api.md)
+ [Informazioni di riferimento sui criteri](api-management-policy-reference.md) per un elenco completo delle istruzioni dei criteri e delle relative impostazioni
+ [Esempi di criteri](policy-samples.md)
