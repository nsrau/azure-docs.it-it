<properties
   pageTitle="Panoramica delle firme di accesso condiviso"
   description="Quali sono le firme di accesso condiviso, come funzionano e come utilizzarle dal nodo, da PHP e da C#."
   services="service-bus,event-hubs"
   documentationCenter="na"
   authors="djrosanova"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="07/24/2015"
   ms.author="darosa"/>

# firme di accesso condiviso

*Firme di accesso condiviso*(SAS) è il meccanismo di sicurezza principale per il bus di servizio, che comprende hub eventi, messaggistica negoziata (code e argomenti) e messaggistica inoltrata. In questo articolo vengono illustrate le firme di accesso condiviso, il loro funzionamento e come utilizzarle in modo indipendente dalla piattaforma.

## Panoramica di SAS

Le firme di accesso condiviso sono un meccanismo di autenticazione basato su hash sicuri SHA-256 o URI. SAS è un meccanismo estremamente efficace utilizzato da tutti i servizi del bus di servizio. Nell’uso effettivo, SAS presenta due componenti: un *criterio di accesso condiviso* e una *firma di accesso condiviso* (spesso chiamata *token*).

Per ulteriori informazioni sulle firme di accesso condiviso con il bus di servizio vedere [Autenticazione della firma di accesso condiviso con il bus di servizio](https://msdn.microsoft.com/library/azure/dn170477.aspx).

## Criterio di accesso condiviso

Un elemento importante da comprendere rispetto a SAS è che tutto inizia con un criterio. Per ogni criterio, si stabiliscono tre informazioni: **nome**, **ambito** e **autorizzazioni**. Il **nome** è un nome univoco all'interno dell’ambito. L'ambito è abbastanza semplice: è l'URI della risorsa in questione. Per uno spazio dei nomi del bus di servizio, l'ambito è il nome di dominio completo (FQDN), ad esempio **`https://<yournamespace>.servicebus.windows.net/`**.

Le autorizzazioni disponibili per un criterio sono in gran parte autoesplicative:

  + Invio
  + Attesa
  + Manage

Dopo aver creato il criterio, al criterio vengono assegnate una *chiave primaria* e una *chiave secondaria*. Si tratta di chiavi di crittografia complesse. Queste chiavi non possono essere perse in quanto sono sempre disponibili nel portale. È possibile utilizzare una delle chiavi generate ed è possibile rigenerarle in qualsiasi momento. Tuttavia, se si rigenera o si modifica la chiave primaria nel criterio, le firme di accesso condiviso create da tale chiave verranno invalidate.

Quando si crea uno spazio dei nomi del bus di servizio, un criterio denominato **RootManageSharedAccessKey** viene creato automaticamente per l'intero spazio dei nomi e questo criterio dispone di tutte le autorizzazioni. Non accedere come **root** e non utilizzare questo criterio a meno che non esista un motivo molto valido. È possibile creare criteri aggiuntivi nella scheda **Configura** per lo spazio dei nomi nel portale di gestione di Azure. È importante notare che a un singolo livello della struttura ad albero nel bus di servizio (spazio dei nomi, coda, hub eventi e così via) può essere associato un massimo di 12 criteri.

## Firma di accesso condiviso (token)

Il criterio non è il token di accesso del bus di servizio. È l'oggetto da cui viene generato il token di accesso, utilizzando la chiave primaria o secondaria. Il token viene generato creando attentamente una stringa nel formato seguente:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

Dove `signature-string` è l'hash SHA-256 dell'ambito del token (**ambito** come descritto nella sezione precedente) con un CRLF aggiunto e un'ora di scadenza (in secondi dopo il periodo: `00:00:00 UTC` il 1° gennaio 1970).

L'hash è simile al seguente pseudocodice e restituisce 32 byte.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

I valori non hash si trovano nella stringa **SharedAccessSignature** per cui il destinatario può calcolare l'hash con gli stessi parametri, per essere certo che restituisca lo stesso risultato. L'URI specifica l'ambito e il nome della chiave identifica i criteri da utilizzare per calcolare l'hash. Questo è importante dal punto di vista della sicurezza. Se la firma non corrisponde a quella calcolata dal destinatario (bus di servizio), l'accesso viene negato. A questo punto si può essere certi che il mittente abbia accesso alla chiave e che disponga dei diritti specificati nei criteri.

## Generazione di una firma da un criterio

Come si esegue effettivamente questa operazione nel codice? Di seguito vengono esaminate alcune di queste operazioni.

### NodeJS

```
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
}
``` 

### Java

```
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

### PHP

```
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

$token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires . 		"&skn=" . $sasKeyName; 
return $token; 
}
```
 
### C&#35;

```
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

## Uso della firma di accesso condiviso (a livello HTTP)
 
Dopo aver creato firme di accesso condiviso per tutte le entità nel bus di servizio, si è pronti a eseguire una richiesta HTTP POST:

```
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 
	
Questa procedura funziona per ogni elemento. È possibile creare SAS per una coda, un argomento, una sottoscrizione, un hub eventi o un inoltro. Se si utilizza l'identità di pubblicazione per gli hub eventi, è sufficiente aggiungere `/publishers/< publisherid>`.

Se un token SAS viene assegnato a un mittente o ad un client, questi ultimi non dispongono della chiave direttamente e non possono invertire l'hash per ottenerla. Di conseguenza, è necessario controllare a cosa possono accedere e per quanto tempo. Se si modifica la chiave primaria nel criterio, le firme di accesso condiviso create da tale chiave verranno invalidate.

## Uso della firma di accesso condiviso (a livello AMQP)

Nella sezione precedente, è stato illustrato come utilizzare il token SAS con una richiesta HTTP POST per l'invio di dati per il Bus di servizio. Come sapete, è possibile accedere a Bus di servizio utilizzando il protocollo AMQP (Advanced Message Queue Protocol) che è il protocollo principale e preferito da utilizzare per motivi di prestazioni in molti scenari. Nel documento seguente è descritto l'utilizzo di token SAS con [AMQP Claim-Based Security versione 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc)in cui è in bozza di lavoro poiché 2013, ma è anche supportato da Azure oggi.

Prima di iniziare a inviare dati al Bus di servizio, il server di pubblicazione deve inviare il token di firma di accesso condiviso all'interno di un messaggio AMQP a un nodo AMQP ben definito denominato**"$cbs"**(può essere visualizzato come una coda "speciale" utilizzata dal servizio per acquisire e convalidare tutti i token di firma di accesso condiviso). Il server di pubblicazione deve specificare il campo**"ReplyTo"** all'interno del messaggio AMQP; si tratta del nodo di cui il servizio invierà una risposta al server di pubblicazione con il risultato della convalida del token (un modello di richiesta/risposta semplice tra server di pubblicazione e il servizio). Questo nodo risposta viene creato al momento in quanto "creazione dinamica di nodo remoto" come descritto nella specifica di AMQP 1.0. Dopo avere verificato che il token di firma di accesso condiviso è valido, il server di pubblicazione può andare avanti e iniziare a inviare dati al servizio.

I passaggi seguenti illustreranno come inviare il token SAS con protocollo AMQP utilizzando la libreria[AMQP.Net Lite](http://amqpnetlite.codeplex.com) utile se non è possibile utilizzare il SDK del Bus di servizio ufficiale (ad esempio su WinRT, .Net Compact Framework, .Net Micro Framework e Mono) che si sviluppa in C & #35;. Certamente, questa libreria è utile per comprendere come funziona la protezione basata sui reclami a livello AMQP come si è visto funzionare a livello HTTP (con una richiesta HTTP POST e il token SAS inviati all'interno dell'intestazione "Autorizzazione"). Tuttavia, nessun problema. Se non sono necessarie tali informazioni approfondite sui AMQP, è possibile utilizzare il bus di servizio ufficiale SDK con le applicazioni .Net Framework che eseguiranno il tutto automaticamente o la libreria[Azure SB Lite](http://azuresblite.codeplex.com)per tutte le altre piattaforme (vedere sopra).

### C&#35;

```
/// <summary>
/// Send Claim Based Security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = "1";
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

Il metodo precedente *PutCbsToken()* riceve la *connessione* (connessione AMQP istanza della classe fornita dalla libreria .Net Lite AMQP) che rappresenta la connessione TCP al servizio e il parametro *sasToken* che è la firma di accesso condiviso. Nota: è importante che la connessione venga creata con il**meccanismo di autenticazione SASL impostato su ESTERNO**(e non il normale PIANO predefinito con nome utente e password utilizzati quando non è necessario inviare il token SAS).

Successivamente il server di pubblicazione crea due collegamenti AMQP per inviare il token SAS e ricevere la risposta (risultato di convalida del token) dal servizio.

Il messaggio AMQP è complesso con una serie di proprietà e altre informazioni oltre al semplice messaggio. Il token SAS viene inserito come corpo del messaggio (tramite il relativo costruttore). La proprietà **"ReplyTo"**è impostata per il nome del nodo per la ricezione del risultato della convalida sul collegamento ricevitore (è possibile modificare il nome come si desidera e verrà creato in modo dinamico dal servizio). Le ultime tre proprietà applicazione/personalizzato vengono utilizzate dal servizio per comprendere il tipo di operazione che è necessario eseguire. Come descritto nella specifica di bozza CBS devono essere il**nome dell'operazione**(quindi "put-token"), il**tipo di token**inserito (in modo "servicebus.windows.net:sastoken") e infine il**"nome" del gruppo di destinatari**a cui viene applicato il token (l'intera entità).

Dopo aver inviato il token SAS sul collegamento mittente, il server di pubblicazione deve leggere la risposta sul collegamento ricevitore. La risposta è un semplice messaggio AMQP con proprietà dell'applicazione denominate**"codice di stato"**che può contenere gli stessi valori di un codice di stato HTTP.

## Passaggi successivi

Vedere [Informazioni di riferimento sulle API REST del bus di servizio](https://msdn.microsoft.com/library/azure/hh780717.aspx) per ulteriori informazioni sulle operazioni che è possibile eseguire con questi token SAS.

Per ulteriori informazioni sulla firma di accesso condiviso, vedere il nodo di [autenticazione del bus di servizio](https://msdn.microsoft.com/library/azure/dn155925.aspx) su MSDN. Ulteriori esempi sulle associazioni di sicurezza in C# e Java Script sono disponibili nel [blog di Damir](http://developers.de/blogs/damir_dobric/archive/2013/10/17/how-to-create-shared-access-signature-for-service-bus.aspx)

<!---HONumber=August15_HO7-->