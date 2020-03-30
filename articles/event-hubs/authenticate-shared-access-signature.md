---
title: Autenticare l'accesso agli hub eventi di Azure con firme di accesso condivisoAuthenticate access to Azure Event Hubs with shared access signatures
description: Questo articolo illustra come autenticare l'accesso alle risorse degli hub eventi usando le firme di accesso condiviso.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: spelluru
ms.openlocfilehash: d17026dba26b3c1cb846d60967180c29563c425d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74545580"
---
# <a name="authenticate-access-to-event-hubs-resources-using-shared-access-signatures-sas"></a>Autenticare l'accesso alle risorse degli hub eventi usando le firme di accesso condivisoAuthenticate access to Event Hubs resources using shared access signatures (SAS)
La firma di accesso condiviso offre un controllo granulare sul tipo di accesso concesso ai client che dispone della firma di accesso condiviso. Ecco alcuni dei controlli che è possibile impostare in una sAS:Here are some of the controls you can set in a SAS: 

- Intervallo di validità della firma di firma di utente, inclusi l'ora di inizio e l'ora di scadenza.
- Le autorizzazioni concesse dalla firma di accesso condiviso. Ad esempio, una sAS per uno spazio dei nomi Hub eventi potrebbe concedere l'autorizzazione di ascolto, ma non l'autorizzazione di invio.
- Solo i client che presentano le credenziali valide possono inviare dati a un hub eventi.
- Un client non può rappresentare un altro client.
- È possibile impedire a un client rouge di inviare dati a un hub eventi.

Questo articolo illustra l'autenticazione dell'accesso alle risorse di Hub eventi tramite SAS. Per informazioni **sull'autorizzazione** dell'accesso alle risorse di Hub eventi tramite la sAS, vedere [questo articolo](authorize-access-shared-access-signature.md). 

> [!NOTE]
> Microsoft consiglia di usare le credenziali di Azure AD quando possibile come procedura consigliata per la sicurezza, anziché usare le firme di accesso condiviso, che possono essere compromesse più facilmente. Sebbene sia possibile continuare a usare le firme di accesso condiviso per concedere un accesso granulare alle risorse degli hub eventi, Azure AD offre funzionalità simili senza la necessità di gestire i token di firma di accesso condiviso o preoccuparsi di revocare una firma di accesso condiviso compromessa.
> 
> Per altre informazioni sull'integrazione di Azure AD negli hub eventi di Azure, vedere [Autorizzare l'accesso agli hub eventi](authorize-access-azure-active-directory.md)usando Azure AD. 


## <a name="configuring-for-sas-authentication"></a>Configurazione per l'autenticazione della provider di sicurezza
È possibile configurare la regola di autorizzazione di accesso condiviso EventHubs in uno spazio dei nomi Hub eventi o un'entità (istanza dell'hub eventi o argomento Kafka in uno spazio dei nomi Event Hubs for Kafka abilitato). La configurazione di una regola di autorizzazione di accesso condiviso in un gruppo di consumer non è attualmente supportata, ma è possibile usare le regole configurate in uno spazio dei nomi o in un'entità per proteggere l'accesso al gruppo di consumer. 

L'immagine seguente mostra come si applicano le regole di autorizzazione alle entità di esempio. 

![Configurare la regola di autorizzazione](./media/authenticate-shared-access-signature/configure-sas-authorization-rule.png)

In questo esempio, lo spazio dei nomi Event Hubs di esempio (ExampleNamespace) ha due entità: eh1 e topic1.In this example, the sample Event Hubs namespace (ExampleNamespace) has two entities: eh1 and topic1. Le regole di autorizzazione vengono definite sia a livello di entità che a livello di spazio dei nomi.  

Le regole di autorizzazione manageRuleNS, sendRuleNS e listenRuleNS si applicano sia all'istanza dell'hub eventi eh1 che all'argomento t1. Le regole di autorizzazione listenRule-eh e sendRule-eh si applicano solo all'istanza dell'hub eventi eh1 e alla regola di autorizzazione sendRuleT si applicano solo a topic1. 

Quando si usa la regola di autorizzazione sendRuleNS, le applicazioni client possono inviare sia a eh1 che a topic1. Quando viene utilizzata la regola di autorizzazione sendRuleT, applica l'accesso granulare solo a topic1 e pertanto le applicazioni client che utilizzano questa regola per l'accesso ora non possono inviare a eh1, ma solo a topic1.

## <a name="generate-a-shared-access-signature-token"></a>Generare un token della firma di accesso condiviso 
Qualsiasi client che abbia accesso al nome di una regola di autorizzazione e a una delle relative chiavi di firma può generare un token di firma di accesso condiviso. Il token viene generato creando una stringa nel formato seguente:

- `se`– Token di scadenza immediata. Numero intero che riflette i secondi dall'epoca 00:00:00 UTC del 1 gennaio 1970 (epoca UNIX) alla scadenza del token
- `skn`– Nome della regola di autorizzazione, ovvero il nome della chiave di accesso base.
- `sr`: URI della risorsa a cui si accede.
- `sig`– Firma.

La stringa della firma è l'hash SHA-256 calcolato sull'URI della risorsa (ambito come descritto nella sezione precedente) e la rappresentazione di stringa dell'istante di scadenza del token, separata da CRLF.

Il calcolo del codice hash è simile allo pseudo codice seguente e restituisce un valore hash a 256 bit o 32 byte. 

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Il token contiene i valori non hash in modo che il destinatario possa ricalcolare il codice hash con gli stessi parametri, verificando che l'autorità di certificazione sia in possesso di una chiave di firma valida.

L'URI di risorsa è l'URI completo della risorsa del bus di servizio a cui si richiede l'accesso. Ad esempio,<namespace>http://<entityPath> `sb://<namespace>.servicebus.windows.net/<entityPath>;` .servicebus.windows.net/ `http://contoso.servicebus.windows.net/eventhubs/eh1`o che è, .

L'URI deve essere codificato in percentuale.

La regola di autorizzazione di accesso condiviso usata per la firma deve essere configurata nell'entità specificata da questo URI o in un elemento padre nella gerarchia. Ad esempio `http://contoso.servicebus.windows.net/eventhubs/eh1` o `http://contoso.servicebus.windows.net` nell'esempio precedente.

Un token di firma di accesso condiviso <resourceURI> è valido per tutte le risorse precedute da quella usata nella stringa della firma.

> [!NOTE]
> Generare un token di accesso per gli hub eventi utilizzando i criteri di accesso condiviso. Per ulteriori informazioni, vedere Criteri di [autorizzazione di accesso condiviso](authorize-access-shared-access-signature.md#shared-access-authorization-policies).

### <a name="generating-a-signaturetoken-from-a-policy"></a>Generazione di una firma (token) da un criterioGenerating a signature(token) from a policy 
La sezione seguente mostra la generazione di un token di firma di accesso condiviso usando i criteri di firma di accesso condiviso,

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

## <a name="authenticating-event-hubs-publishers-with-sas"></a>Autenticazione degli autori di Hub eventi con SAS 
Un autore di eventi definisce un endpoint virtuale per un hub eventi. L'autore può essere utilizzato solo per inviare messaggi a un hub eventi e non ricevere messaggi.

In genere, un Hub eventi usa un autore per ogni client. Tutti i messaggi inviati a uno qualsiasi degli autori di un Hub eventi vengono accodati all'interno di tale Hub eventi. I server di pubblicazione consentono un controllo degli accessi con granularità fine.

A ogni client di Hub eventi viene assegnato un token univoco, che viene caricato nel client. I token vengono prodotti in modo che ogni token univoco conceda l'accesso a diversi server di pubblicazione univoci. Un client che contiene un token può inviare solo a un autore e nessun altro editore. Se più client condividono lo stesso token, ognuno di essi condivide il server di pubblicazione.

A tutti i token vengono assegnati chiavi di firma di accesso condiviso. In genere, tutti i token sono firmati con la stessa chiave. I client non sono a conoscenza della chiave, il che impedisce ai client di produrre token. I client operano sugli stessi token fino alla scadenza.

Ad esempio, per definire le regole di autorizzazione con ambito fino a l'invio/pubblicazione solo negli hub eventi, è necessario definire una regola di autorizzazione di invio. Questa operazione può essere eseguita a livello di spazio dei nomi o assegnare un ambito più granulare a una determinata entità (istanza di hub eventi o un argomento). Viene chiamato un client o un'applicazione con ambito con tale accesso granulare, editore Hub eventi. A questo scopo, attenersi alla procedura seguente:

1. Creare una chiave di accesso base nell'entità che si vuole pubblicare per assegnare l'ambito di **invio** su di essa. Per ulteriori informazioni, vedere Criteri di [autorizzazione di accesso condiviso](authorize-access-shared-access-signature.md#shared-access-authorization-policies).
2. Generare un token di firma di accesso condiviso con un tempo di scadenza per un autore specifico usando la chiave generata nel passaggio 1.Generate a SAS token with an expiry time for a specific publisher by using the key generated in step1.

    ```csharp
    var sasToken = SharedAccessSignatureTokenProvider.GetPublisherSharedAccessSignature(
                new Uri("Service-Bus-URI"),
                "eventub-name",
                "publisher-name",
                "sas-key-name",
                "sas-key",
                TimeSpan.FromMinutes(30));
    ```
3. Fornire il token al client dell'editore, che può essere inviato solo all'entità e all'autore a cui il token concede l'accesso.

    Una volta scaduto il token, il client perde l'accesso per inviare/pubblicare nell'entità. 


> [!NOTE]
> Anche se non è consigliabile, è possibile dotare i dispositivi di token che concedono l'accesso a un hub eventi o a uno spazio dei nomi. Qualsiasi dispositivo che contiene questo token può inviare messaggi direttamente all'hub eventi. Non è possibile disattivare l'invio a tale Hub eventi per il dispositivo.
> 
> È sempre consigliabile assegnare ambiti specifici e granulari.

> [!IMPORTANT]
> Dopo avere creato i token, viene eseguito il provisioning di ogni client con il proprio token univoco.
>
> Quando il client invia dati in un hub eventi, contrassegna la richiesta con il token. Per evitare che un utente malintenzionato intercetti e rubi il token, la comunicazione tra il client e l'Hub eventi deve verificarsi su un canale crittografato.
> 
> In caso di furto di un token da parte di un utente malintenzionato, l'autore dell'attacco può rappresentare il client il cui token è stato rubato. Se si elenca un server di pubblicazione nella blacklisting, il client viene inutilizzabile finché non viene ricevuto un nuovo token che usa un editore diverso.


## <a name="authenticating-event-hubs-consumers-with-sas"></a>Autenticazione degli hub eventi per i consumer con sASAuthenticating Event Hubs consumers with SAS 
Per autenticare le applicazioni back-end che utilizzano i dati generati dai produttori di Hub eventi, l'autenticazione token degli hub eventi richiede ai client di disporre dei diritti di **gestione** o dei privilegi di **ascolto** assegnati al relativo spazio dei nomi Hub eventi o all'istanza o all'argomento dell'hub eventi. I dati vengono utilizzati dagli hub eventi che utilizzano i gruppi di consumer. Mentre i criteri di creazione di sicurezza offrono un ambito granulare, questo ambito viene definito solo a livello di entità e non a livello di consumer. Ciò significa che i privilegi definiti a livello di spazio dei nomi o dell'istanza dell'hub eventi o a livello di argomento verranno applicati ai gruppi di consumer di tale entità.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Autorizzare tramite sASAuthorize using SAS](authenticate-shared-access-signature.md)
- [Autorizzare usando il controllo degli accessi in base di ruoli](authenticate-shared-access-signature.md)
- [Ulteriori informazioni sugli hub eventi](event-hubs-about.md)

Vedere i seguenti articoli correlati:

- [Autenticare le richieste agli hub eventi di Azure da un'applicazione tramite Azure Active DirectoryAuthenticate requests to Azure Event Hubs from an application using Azure Active Directory](authenticate-application.md)
- [Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse degli hub eventiAuthenticate a managed identity with Azure Active Directory to access Event Hubs Resources](authenticate-managed-identity.md)
- [Autorizzare l'accesso alle risorse di Hub eventi usando Azure Active DirectoryAuthorize access to Event Hubs resources using Azure Active Directory](authorize-access-azure-active-directory.md)
- [Autorizzare l'accesso alle risorse degli hub eventi usando le firme di accesso condivisoAuthorize access to Event Hubs resources using Shared Access Signatures](authorize-access-shared-access-signature.md)