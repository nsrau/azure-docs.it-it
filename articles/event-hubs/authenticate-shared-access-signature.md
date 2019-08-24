---
title: Autenticare l'accesso a hub eventi di Azure con firme di accesso condiviso
description: Questo articolo illustra come autenticare l'accesso alle risorse di hub eventi usando le firme di accesso condiviso.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: cb5c53f3f473c10a3c9a12bb1aac20b109c06422
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992536"
---
# <a name="authenticate-access-to-event-hubs-resources-using-shared-access-signatures-sas"></a>Autenticare l'accesso alle risorse di hub eventi usando firme di accesso condiviso (SAS)
Firma di accesso condiviso (SAS) offre un controllo granulare sul tipo di accesso concesso ai client che dispongono della firma di accesso condiviso. Ecco alcuni dei controlli che è possibile impostare in una firma di accesso condiviso: 

- Intervallo in base al quale la firma di accesso condiviso è valida, incluse l'ora di inizio e l'ora di scadenza.
- Le autorizzazioni concesse dalla firma di accesso condiviso. Ad esempio, una firma di accesso condiviso per uno spazio dei nomi di hub eventi può concedere l'autorizzazione Listen, ma non l'autorizzazione SEND.
- Solo i client che presentano le credenziali valide possono inviare dati a un hub eventi.
- Un client non può rappresentare un altro client.
- Un client Rouge può impedire l'invio di dati a un hub eventi.

Questo articolo illustra come autenticare l'accesso alle risorse di hub eventi tramite SAS. Per informazioni sull' **autorizzazione** dell'accesso alle risorse di hub eventi tramite SAS, vedere [questo articolo](authorize-access-shared-access-signature.md). 

> [!NOTE]
> Microsoft consiglia di utilizzare le credenziali Azure AD quando possibile come procedura di sicurezza consigliata, anziché utilizzare le firme di accesso condiviso, che possono essere compromesse in modo più semplice. Sebbene sia possibile continuare a usare le firme di accesso condiviso (SAS) per concedere l'accesso con granularità fine alle risorse di hub eventi, Azure AD offre funzionalità simili senza dover gestire i token SAS o preoccuparsi di revocare una firma di accesso condiviso compromessa.
> 
> Per altre informazioni sull'integrazione di Azure AD in hub eventi di Azure, vedere [autorizzare l'accesso a hub eventi usando Azure ad](authorize-access-azure-active-directory.md). 


## <a name="configuring-for-sas-authentication"></a>Configurazione per l'autenticazione SAS
È possibile configurare la regola di autorizzazione di accesso condiviso EventHubs in uno spazio dei nomi di hub eventi o un'entità (istanza di hub eventi o argomento Kafka in un hub eventi per lo spazio dei nomi abilitato Kafka). La configurazione di una regola di autorizzazione di accesso condiviso in un gruppo di consumer non è attualmente supportata, ma è possibile usare regole configurate in uno spazio dei nomi o un'entità per proteggere l'accesso al gruppo di consumer 

Nell'immagine seguente viene illustrato il modo in cui vengono applicate le regole di autorizzazione sulle entità di esempio. 

![Configurare la regola di autorizzazione](./media/authenticate-shared-access-signature/configure-sas-authorization-rule.png)

In questo esempio lo spazio dei nomi di hub eventi di esempio (ExampleNamespace) ha due entità: EH1 e Topic1. Le regole di autorizzazione sono definite a livello di entità e anche a livello di spazio dei nomi.  

Le regole di autorizzazione manageRuleNS, sendRuleNS e Listenrulens si sono valide sia per l'istanza di hub eventi EH1 che per l'argomento T1. Le regole di autorizzazione listenRule-eh e sendRule-eh si applicano solo all'istanza di hub eventi EH1 e la regola di autorizzazione sendRuleT si applica solo all'argomento Topic1. 

Quando si usa la regola di autorizzazione sendRuleNS, le applicazioni client possono inviare sia EH1 che Topic1. Quando si usa la regola di autorizzazione sendRuleT, viene applicato solo l'accesso granulare a Topic1 e pertanto le applicazioni client che usano questa regola per Access Now non possono inviare a EH1, ma solo a Topic1.

## <a name="generate-a-shared-access-signature-token"></a>Generare un token della firma di accesso condiviso 
Qualsiasi client che abbia accesso al nome di una regola di autorizzazione e a una delle relative chiavi di firma può generare un token di firma di accesso condiviso. Il token viene generato creando una stringa nel formato seguente:

- `se`: Istante di scadenza del token. Integer che riflette i secondi a partire dall'ora UTC 00:00:00 il 1 ° gennaio 1970 (Epoch UNIX) quando il token scade
- `skn`: Nome della regola di autorizzazione, ovvero il nome della chiave di firma di accesso condiviso.
- `sr`: URI della risorsa a cui si accede.
- `sig`Firma.

La stringa di firma è l'hash SHA-256 calcolato sull'URI della risorsa (ambito come descritto nella sezione precedente) e la rappresentazione di stringa dell'istante di scadenza del token, separate da CRLF.

Il calcolo del codice hash è simile allo pseudo codice seguente e restituisce un valore hash a 256 bit o 32 byte. 

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Il token contiene i valori non hash in modo che il destinatario possa ricalcolare il codice hash con gli stessi parametri, verificando che l'autorità di certificazione sia in possesso di una chiave di firma valida.

L'URI di risorsa è l'URI completo della risorsa del bus di servizio a cui si richiede l'accesso. Ad esempio, http://<namespace>. ServiceBus.Windows.NET/<entityPath> o `sb://<namespace>.servicebus.windows.net/<entityPath>;` ovvero, `http://contoso.servicebus.windows.net/eventhubs/eh1`.

L'URI deve essere codificato in percentuale.

La regola di autorizzazione di accesso condiviso usata per la firma deve essere configurata nell'entità specificata da questo URI o in un elemento padre nella gerarchia. Ad esempio `http://contoso.servicebus.windows.net/eventhubs/eh1` o `http://contoso.servicebus.windows.net` nell'esempio precedente.

Un token di firma di accesso condiviso è valido per tutte le <resourceURI> risorse con il prefisso usato nella stringa di firma.

> [!NOTE]
> Per generare un token di accesso per hub eventi, usare i criteri di accesso condiviso. Per altre informazioni, vedere [criteri di autorizzazione dell'accesso condiviso](authorize-access-shared-access-signature.md#shared-access-authorization-policies).

### <a name="generating-a-signaturetoken-from-a-policy"></a>Generazione di una firma (token) da un criterio 
La sezione seguente illustra la generazione di un token SAS usando criteri di firma di accesso condiviso.

#### <a name="nodejs"></a>NodeJS

```javascript
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
```

#### <a name="java"></a>Java

```java
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```
#### <a name="php"></a>PHP

```php
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
    $targetUri = strtolower(rawurlencode(strtolower($uri))); 
    $expires = time();  
    $expiresInMins = 60; 
    $week = 60*60*24*7;
    $expires = $expires + $week; 
    $toSign = $targetUri . "\n" . $expires; 
    $signature = rawurlencode(base64_encode(hash_hmac('sha256',             
     $toSign, $sasKeyValue, TRUE))); 
    
    $token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
    return $token; 
}
```

#### <a name="c"></a>C#

```csharp
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="authenticating-event-hubs-publishers-with-sas"></a>Autenticazione dei publisher di hub eventi con SAS 
Un autore di eventi definisce un endpoint virtuale per un hub eventi. Il server di pubblicazione può essere usato solo per inviare messaggi a un hub eventi e non ricevere messaggi.

In genere, un Hub eventi usa un autore per ogni client. Tutti i messaggi inviati a uno qualsiasi degli autori di un Hub eventi vengono accodati all'interno di tale Hub eventi. Gli autori abilitano il controllo di accesso con granularità fine.

A ogni client di Hub eventi viene assegnato un token univoco, che viene caricato nel client. I token vengono prodotti in modo tale che ogni token univoco conceda l'accesso a un server di pubblicazione univoco diverso. Un client che possiede un token può inviare a un solo server di pubblicazione e a nessun altro server di pubblicazione. Se più client condividono lo stesso token, ognuno di essi condivide un autore.

Tutti i token vengono assegnati con chiavi di firma di accesso condiviso. In genere, tutti i token sono firmati con la stessa chiave. I client non sono a conoscenza della chiave che impedisce ad altri client di produrre token. I client operano sugli stessi token finché non scadono.

Ad esempio, per definire le regole di autorizzazione limitate alla sola trasmissione/pubblicazione a hub eventi, è necessario definire una regola di autorizzazione SEND. Questa operazione può essere eseguita a livello di spazio dei nomi o fornire un ambito più granulare a un'entità specifica (istanza di hub eventi o un argomento). Un client o un'applicazione con un ambito di questo tipo di accesso granulare viene chiamato server di pubblicazione di hub eventi. A questo scopo, attenersi alla procedura seguente:

1. Creare una chiave di firma di accesso condiviso nell'entità che si desidera pubblicare per assegnare l'ambito di **trasmissione** . Per altre informazioni, vedere [criteri di autorizzazione dell'accesso condiviso](authorize-access-shared-access-signature.md#shared-access-authorization-policies).
2. Generare un token di firma di accesso condiviso con un'ora di scadenza per un server di pubblicazione specifico usando la chiave generata in Step1.
3. Fornire il token al client di pubblicazione, che può inviare solo all'entità a cui il token concede l'accesso.
4. Una volta scaduto il token, il client perde l'accesso per l'invio o la pubblicazione all'entità. 


> [!NOTE]
> Sebbene non sia consigliato, è possibile dotare i dispositivi di token che concedono l'accesso a un hub eventi. Tutti i dispositivi che contengono questo token possono inviare messaggi direttamente a tale hub eventi. Non è possibile disattivare l'invio a tale Hub eventi per il dispositivo.
> 
> Il comportamento precedente può essere osservato quando lo stesso token viene distribuito a più dispositivi, che consente l'accesso a livello di spazio dei nomi. In tal caso, un dispositivo o un server di pubblicazione Rouge non può essere isolato e revocato. È sempre consigliabile assegnare ambiti specifici e granulari.

> [!IMPORTANT]
> Dopo avere creato i token, viene eseguito il provisioning di ogni client con il proprio token univoco.
>
> Quando il client invia dati a un hub eventi, ne contrassegna la richiesta con il token. Per evitare che un utente malintenzionato intercetti e rubi il token, la comunicazione tra il client e l'Hub eventi deve verificarsi su un canale crittografato.
> 
> In caso di furto di un token da parte di un utente malintenzionato, l'autore dell'attacco può rappresentare il client il cui token è stato rubato. Inserendo un server di pubblicazione in blacklist, il client viene reso inutilizzabile fino a quando non riceve un nuovo token che usa un server di pubblicazione diverso.


## <a name="authenticating-event-hubs-consumers-with-sas"></a>Autenticazione dei consumer di hub eventi con SAS 
Per autenticare le applicazioni back-end che usano i dati generati dai producer di hub eventi, l'autenticazione del token di hub eventi richiede che i client dispongano dei diritti di **gestione** o dei privilegi di **ascolto** assegnati ai relativi hub eventi. istanza o argomento dell'hub eventi o dello spazio dei nomi. I dati vengono usati dagli hub eventi usando i gruppi di consumer. Sebbene i criteri SAS forniscano un ambito granulare, questo ambito viene definito solo a livello di entità e non a livello di consumer. Significa che i privilegi definiti a livello di spazio dei nomi o di istanza di hub eventi o a livello di argomento verranno applicati ai gruppi di consumer di tale entità.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Autorizzare usando SAS](authenticate-shared-access-signature.md)
- [Autorizzare l'uso del controllo degli accessi in base al ruolo (RBAC)](authenticate-shared-access-signature.md)
- [Altre informazioni su Hub eventi](event-hubs-about.md)

Vedere gli articoli correlati seguenti:

- [Autenticare le richieste a hub eventi di Azure da un'applicazione usando Azure Active Directory](authenticate-application.md)
- [Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse di hub eventi](authenticate-managed-identity.md)
- [Autorizzare l'accesso alle risorse di hub eventi usando Azure Active Directory](authorize-access-azure-active-directory.md)
- [Autorizzare l'accesso alle risorse di hub eventi usando le firme di accesso condiviso](authorize-access-shared-access-signature.md)