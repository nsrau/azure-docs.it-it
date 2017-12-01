---
title: Espressioni di criteri di Gestione API di Azure | Microsoft Docs
description: Informazioni sulle espressioni di criteri in Gestione API di Azure.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 96455dcdcf2eb90c836675c73c83c0320524fdac
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2017
---
# <a name="api-management-policy-expressions"></a>Espressioni di criteri di Gestione API
La sintassi delle espressioni di criteri è C# 6.0. Ogni espressione ha accesso alla variabile [context](api-management-policy-expressions.md#ContextVariables) fornita implicitamente e a un [subset](api-management-policy-expressions.md#CLRTypes) autorizzato di tipi di .NET Framework.  
  
> [!NOTE]
>  Per ulteriori informazioni sulle espressioni di criteri, vedere il video [Espressioni di criteri](https://azure.microsoft.com/documentation/videos/policy-expressions-in-azure-api-management/).  
>   
>  Per una dimostrazione relativa alla configurazione dei criteri usando le espressioni, vedere l'[episodio 177 di Cloud Cover su altre funzionalità di Gestione API con Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/). Il video include le seguenti dimostrazioni di espressioni di criteri.  
>   
>  -   10:30: informazioni su come applicare criteri al livello dell'API per fornire informazioni di contesto al servizio back-end usando i criteri [Imposta parametro di stringa della query](api-management-transformation-policies.md#SetQueryStringParameter) e [Imposta intestazione HTTP](api-management-transformation-policies.md#SetHTTPheader). A 12:10 minuti viene illustrata una dimostrazione su come chiamare un'operazione nel portale per sviluppatori, dove è possibile vedere all'opera i criteri stessi.  
> -   13:50: informazioni su come usare il criterio [Convalida JWT](api-management-access-restriction-policies.md#ValidateJWT) per preautorizzare l'accesso alle operazioni in base alle attestazioni dei token. Passare a 15:00 minuti per vedere i criteri configurati nell'editor dei criteri e quindi a 18:50 minuti per una dimostrazione della chiamata di un'operazione dal portale per sviluppatori, con e senza il token di autorizzazione richiesto.  
> -   21:00: informazioni su come usare una traccia di [Controllo API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) per visualizzare il modo in cui i criteri vengono valutati e le valutazioni stesse.   
> -   25:25: informazioni su come usare le espressioni di criteri con i criteri [Recupera dalla cache](api-management-caching-policies.md#GetFromCache) e [Archivia nella cache](api-management-caching-policies.md#StoreToCache) per configurare la durata della memorizzazione nella cache di Gestione API corrispondente alla memorizzazione delle risposte nella cache del serivzio back-end, come specificato dalla direttiva `Cache-Control` del servizio in questione.  
> -   34:30: informazioni su come eseguire operazioni di filtro sui contenuti rimuovendo elementi dati dalla risposta ricevuta dal servizio back-end usando i criteri [Flusso di controllo](api-management-advanced-policies.md#choose) e [Imposta corpo](api-management-transformation-policies.md#SetBody). Andare al minuto 31:50 per visualizzare una panoramica di [The Dark Sky Forecast API](https://developer.forecast.io/), l'API usata in questa dimostrazione.  
> -   Le istruzioni dei criteri usate in questo video sono disponibili nel repository di github [api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies).  
  
  
##  <a name="Syntax"></a> Sintassi  
 Le espressioni a istruzione singola sono racchiuse tra `@(expression)`, dove `expression` è un'istruzione di espressione C# ben formata.  
  
 Le espressioni a più istruzioni sono racchiuse tra `@{expression}`. Tutti i percorsi di codice all'interno di espressioni a più istruzioni devono terminare con un'istruzione `return`.  
  
##  <a name="PolicyExpressionsExamples"></a> Esempi  
  
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
  
##  <a name="PolicyExpressionsUsage"></a> Uso  
 Le espressioni possono essere usate come valori di attributo o valori di testo in uno qualsiasi dei [criteri](api-management-policies.md) di Gestione API, salvo diversamente specificato dai criteri.  
  
> [!IMPORTANT]
>  Si noti che quando si usano espressioni di criteri, è prevista solo una verifica limitata di tali espressioni quando i criteri vengono definiti. Poiché le espressioni vengono eseguite dal gateway al momento del runtime nella pipeline in entrata o in uscita, eventuali eccezioni di runtime generate dalle espressioni di criteri porteranno a un errore di runtime nella chiamata dell'API.  
  
##  <a name="CLRTypes"></a> Tipi di .NET Framework consentiti nelle espressioni di criteri  
 Nella tabella seguente sono elencati i tipi di .NET Framework e i relativi membri consentiti nelle espressioni di criteri.  
  
|Tipo CLR|Metodi supportati|  
|--------------|-----------------------|  
|Newtonsoft.Json.Linq.Extensions|Tutti i metodi sono supportati|  
|Newtonsoft.Json.Linq.JArray|Tutti i metodi sono supportati|  
|Newtonsoft.Json.Linq.JConstructor|Tutti i metodi sono supportati|  
|Newtonsoft.Json.Linq.JContainer|Tutti i metodi sono supportati|  
|Newtonsoft.Json.Linq.JObject|Tutti i metodi sono supportati|  
|Newtonsoft.Json.Linq.JProperty|Tutti i metodi sono supportati|  
|Newtonsoft.Json.Linq.JRaw|Tutti i metodi sono supportati|  
|Newtonsoft.Json.Linq.JToken|Tutti i metodi sono supportati|  
|Newtonsoft.Json.Linq.JTokenType|Tutti i metodi sono supportati|  
|Newtonsoft.Json.Linq.JValue|Tutti i metodi sono supportati|  
|System.Collections.Generic.IReadOnlyCollection<T\>|Tutti|  
|System.Collections.Generic.IReadOnlyDictionary<TKey,  TValue>|Tutti|  
|System.Collections.Generic.ISet<TKey, TValue>|Tutti|  
|System.Collections.Generic.KeyValuePair<TKey,  TValue>|Chiave, valore|  
|System.Collections.Generic.List<TKey, TValue>|Tutti|  
|System.Collections.Generic.Queue<TKey, TValue>|Tutti|  
|System.Collections.Generic.Stack<TKey, TValue>|Tutti|  
|System.Convert|Tutti|  
|System.DateTime|Tutti|  
|System.DateTimeKind|UTC|  
|System.DateTimeOffset|Tutti|  
|System.Decimal|Tutti|  
|System.Double|Tutti|  
|System.Guid|Tutti|  
|System.IEnumerable<T\>|Tutti|  
|System.IEnumerator<T\>|Tutti|  
|System.Int16|Tutti|  
|System.Int32|Tutti|  
|System.Int64|Tutti|  
|System.Linq.Enumerable<T\>|Tutti i metodi sono supportati|  
|System.Math|Tutti|  
|System.MidpointRounding|Tutti|  
|System.Nullable<T\>|Tutti|  
|System.Random|Tutti|  
|System.SByte|Tutti|  
|System.Security.Cryptography. HMACSHA384|Tutti|  
|System.Security.Cryptography. HMACSHA512|Tutti|  
|System.Security.Cryptography.HashAlgorithm|Tutti|  
|System.Security.Cryptography.HMAC|Tutti|  
|System.Security.Cryptography.HMACMD5|Tutti|  
|System.Security.Cryptography.HMACSHA1|Tutti|  
|System.Security.Cryptography.HMACSHA256|Tutti|  
|System.Security.Cryptography.KeyedHashAlgorithm|Tutti|  
|System.Security.Cryptography.MD5|Tutti|  
|System.Security.Cryptography.RNGCryptoServiceProvider|Tutti|  
|System.Security.Cryptography.SHA1|Tutti|  
|System.Security.Cryptography.SHA1Managed|Tutti|  
|System.Security.Cryptography.SHA256|Tutti|  
|System.Security.Cryptography.SHA256Managed|Tutti|  
|System.Security.Cryptography.SHA384|Tutti|  
|System.Security.Cryptography.SHA384Managed|Tutti|  
|System.Security.Cryptography.SHA512|Tutti|  
|System.Security.Cryptography.SHA512Managed|Tutti|  
|System.Single|Tutti|  
|System.String|Tutti|  
|System.StringSplitOptions|Tutti|  
|System.Text.Encoding|Tutti|  
|System.Text.RegularExpressions.Capture|Indice, lunghezza, valore|  
|System.Text.RegularExpressions.CaptureCollection|Conteggio, elemento|  
|System.Text.RegularExpressions.Group|Acquisizioni, esito positivo|  
|System.Text.RegularExpressions.GroupCollection|Conteggio, elemento|  
|System.Text.RegularExpressions.Match|Empty, Groups, Result|  
|System.Text.RegularExpressions.Regex|.ctor, IsMatch, Match, Matches, Replace|  
|System.Text.RegularExpressions.RegexOptions|Compiled, IgnoreCase, IgnorePatternWhitespace, Multiline, None, RightToLeft, Singleline|  
|System.TimeSpan|Tutti|  
|System.Tuple|Tutti|  
|System.UInt16|Tutti|  
|System.UInt32|Tutti|  
|System.UInt64|Tutti|  
|System.Uri|Tutti|  
|System.Xml.Linq.Extensions|Tutti i metodi sono supportati|  
|System.Xml.Linq.XAttribute|Tutti i metodi sono supportati|  
|System.Xml.Linq.XCData|Tutti i metodi sono supportati|  
|System.Xml.Linq.XComment|Tutti i metodi sono supportati|  
|System.Xml.Linq.XContainer|Tutti i metodi sono supportati|  
|System.Xml.Linq.XDeclaration|Tutti i metodi sono supportati|  
|System.Xml.Linq.XDocument|Tutti i metodi sono supportati|  
|System.Xml.Linq.XDocumentType|Tutti i metodi sono supportati|  
|System.Xml.Linq.XElement|Tutti i metodi sono supportati|  
|System.Xml.Linq.XName|Tutti i metodi sono supportati|  
|System.Xml.Linq.XNamespace|Tutti i metodi sono supportati|  
|System.Xml.Linq.XNode|Tutti i metodi sono supportati|  
|System.Xml.Linq.XNodeDocumentOrderComparer|Tutti i metodi sono supportati|  
|System.Xml.Linq.XNodeEqualityComparer|Tutti i metodi sono supportati|  
|System.Xml.Linq.XObject|Tutti i metodi sono supportati|  
|System.Xml.Linq.XProcessingInstruction|Tutti i metodi sono supportati|  
|System.Xml.Linq.XText|Tutti i metodi sono supportati|  
|System.Xml.XmlNodeType|Tutti|  
  
##  <a name="ContextVariables"></a> Variabile di contesto  
 Una variabile denominata `context` è disponibile in modo implicito in ogni [espressione](api-management-policy-expressions.md#Syntax) di criteri. I suoi membri forniscono informazioni riguardanti `\request`. Tutti i membri di `context` sono di sola lettura.  
  
|Variabile di contesto|Metodi, proprietà e valori di parametro consentiti|  
|----------------------|-------------------------------------------------------|  
|context|Api: IApi<br /><br /> Distribuzione<br /><br /> LastError<br /><br /> Operazione<br /><br /> Prodotto<br /><br /> Richiesta<br /><br /> RequestId: Guid<br /><br /> Response<br /><br /> Sottoscrizione<br /><br /> Tracing: bool<br /><br /> Utente<br /><br /> Variables:IReadOnlyDictionary<string, object><br /><br /> void Trace(message: string)|  
|context.Api|Id: string<br /><br /> Name: string<br /><br /> Path: string<br /><br /> ServiceUrl: IUrl|  
|context.Deployment|Region: string<br /><br /> ServiceName: string<br /><br /> Certificati: IReadOnlyDictionary<string, X509Certificate2>|  
|context.LastError|Source: string<br /><br /> Reason: string<br /><br /> Message: string<br /><br /> Scope: string<br /><br /> Section: string<br /><br /> Path: string<br /><br /> PolicyId: string<br /><br /> Per ulteriori informazioni su context.LastError, vedere [Gestione degli errori](api-management-error-handling-policies.md).|  
|context.Operation|Id: string<br /><br /> Method: string<br /><br /> Name: string<br /><br /> UrlTemplate: string|  
|context.Product|Apis: IEnumerable<IApi\><br /><br /> ApprovalRequired: bool<br /><br /> Groups: IEnumerable<IGroup\><br /><br /> Id: string<br /><br /> Name: string<br /><br /> State: enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|  
|context.Request|Body: IMessageBody<br /><br /> Certificate: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> Headers: IReadOnlyDictionary<string, string[]><br /><br /> IpAddress: string<br /><br /> MatchedParameters: IReadOnlyDictionary<string, string><br /><br /> Method: string<br /><br /> OriginalUrl:IUrl<br /><br /> Url: IUrl|  
|string context.Request.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Restituisce valori di intestazione di richiesta separati da virgole oppure `defaultValue` se non viene trovata l'intestazione.|  
|context.Response|Body: IMessageBody<br /><br /> Headers: IReadOnlyDictionary<string, string[]><br /><br /> StatusCode: int<br /><br /> StatusReason: string|  
|string context.Response.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Restituisce valori di intestazione di risposta separati da virgole oppure `defaultValue` se non viene trovata l'intestazione.|  
|context.Subscription|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> Id: string<br /><br /> Key: string<br /><br /> Name: string<br /><br /> PrimaryKey: string<br /><br /> SecondaryKey: string<br /><br /> StartDate: DateTime?|  
|context.User|Email: string<br /><br /> FirstName: string<br /><br /> Groups: IEnumerable<IGroup\><br /><br /> Id: string<br /><br /> Identities: IEnumerable<IUserIdentity\><br /><br /> LastName: string<br /><br /> Note: string<br /><br /> RegistrationDate: DateTime|  
|IApi|Id: string<br /><br /> Name: string<br /><br /> Path: string<br /><br /> Protocols: IEnumerable<string\><br /><br /> ServiceUrl: IUrl<br /><br /> SubscriptionKeyParameterNames: ISubscriptionKeyParameterNames|  
|IGroup|Id: string<br /><br /> Name: string|  
|IMessageBody|As<T\>(preserveContent: bool = false): Where T: string, JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> I metodi `context.Request.Body.As<T>` e `context.Response.Body.As<T>` vengono usati per leggere il corpo dei messaggi di richiesta e di risposta in un tipo `T` specificato. Per impostazione predefinita, il metodo usa il flusso del corpo del messaggio originale e lo rende non disponibile dopo aver restituito un valore. Per evitarlo, far sì che il metodo venga eseguito su una copia del flusso del corpo impostando il parametro `preserveContent` su `true`. Per un esempio, vedere [qui](api-management-transformation-policies.md#SetBody).|  
|IUrl|Host: string<br /><br /> Path: string<br /><br /> Port: int<br /><br /> Query: IReadOnlyDictionary<string, string[]><br /><br /> QueryString: string<br /><br /> Scheme: string|  
|IUserIdentity|Id: string<br /><br /> Provider: string|  
|ISubscriptionKeyParameterNames|Header: string<br /><br /> Query: string|  
|string IUrl.Query.GetValueOrDefault(queryParameterName: string, defaultValue: string)|queryParameterName: string<br /><br /> defaultValue: string<br /><br /> Restituisce valori di parametro separati da virgole oppure `defaultValue` se il parametro non viene trovato.|  
|T context.Variables.GetValueOrDefault<T\>(variableName: string, defaultValue: T)|variableName: string<br /><br /> defaultValue: T<br /><br /> Restituisce il valore della variabile nel tipo `T` oppure `defaultValue` se la variabile non viene trovata.<br /><br /> Questo metodo genera un'eccezione se il tipo specificato non corrisponde al tipo effettivo della variabile restituita.|  
|BasicAuthCredentials AsBasic(input: this string)|input: string<br /><br /> Se il parametro di input contiene un valore di intestazione di richiesta di autorizzazione Autenticazione HTTP di base valido, il metodo restituisce un oggetto di tipo `BasicAuthCredentials`; in caso contrario restituisce un valore null.|  
|bool TryParseBasic(input: this string, result: out BasicAuthCredentials)|input: string<br /><br /> result: out BasicAuthCredentials<br /><br /> Se il parametro di input contiene un valore di intestazione di richiesta di autorizzazione Autenticazione HTTP di base valido, il metodo restituisce un oggetto di tipo `true` e il parametro risultante contiene un valore di tipo `BasicAuthCredentials`; in caso contrario restituisce `false`.|  
|BasicAuthCredentials|Password: string<br /><br /> UserId: string|  
|Jwt AsJwt(input: this string)|input: string<br /><br /> Se il parametro di input contiene un valore di token JWT valido, il metodo restituisce un oggetto di tipo `Jwt`; in caso contrario restituisce `null`.|  
|bool TryParseJwt(input: this string, result: out Jwt)|input: string<br /><br /> result: out Jwt<br /><br /> Se il parametro di input contiene un valore di token JWT valido, il metodo restituisce `true` e il parametro del risultato contiene un valore di tipo `Jwt`; in caso contrario il metodo restituisce `false`.|  
|Jwt|Algorithm: string<br /><br /> Audience: IEnumerable<string\><br /><br /> Claims: IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime: DateTime?<br /><br /> Id: string<br /><br /> Issuer: string<br /><br /> NotBefore: DateTime?<br /><br /> Subject: string<br /><br /> Type: string|  
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: string<br /><br /> defaultValue: string<br /><br /> Restituisce valori di attestazione separati da virgole oppure `defaultValue` se non viene trovata l'intestazione.|
|byte[] Encrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - testo non crittografato da crittografare<br /><br />alg - nome di un algoritmo di crittografia simmetrica<br /><br />key - chiave di crittografia<br /><br />iv - vettore di inizializzazione<br /><br />Restituisce testo normale crittografato.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input - testo non crittografato da crittografare<br /><br />alg - algoritmo di crittografia<br /><br />Restituisce testo normale crittografato.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - testo non crittografato da crittografare<br /><br />alg - algoritmo di crittografia<br /><br />key - chiave di crittografia<br /><br />iv - vettore di inizializzazione<br /><br />Restituisce testo normale crittografato.|
|byte[] Decrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - messaggio cifrato da decrittografare<br /><br />alg - nome di un algoritmo di crittografia simmetrica<br /><br />key - chiave di crittografia<br /><br />iv - vettore di inizializzazione<br /><br />Restituisce testo normale non crittografato.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - messaggio cifrato da decrittografare<br /><br />alg - algoritmo di crittografia<br /><br />Restituisce testo normale non crittografato.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - input - messaggio cifrato da decrittografare<br /><br />alg - algoritmo di crittografia<br /><br />key - chiave di crittografia<br /><br />iv - vettore di inizializzazione<br /><br />Restituisce testo normale non crittografato.|

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'uso dei criteri, vedere [Criteri di Gestione API](api-management-howto-policies.md).  
