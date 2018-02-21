---
title: Autenticazione del bus di servizio di Azure con firme di accesso condiviso | Documentazione Microsoft
description: Panoramica dell'autenticazione del bus di servizio con firme di accesso condiviso, dettagli dell'autenticazione con firme di accesso condiviso con il bus di servizio.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: cdbac0fd18ad440ece35881cbe165c3c7eff8914
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="service-bus-authentication-with-shared-access-signatures"></a>Autenticazione del bus di servizio con firme di accesso condiviso

Le *firme di accesso condiviso* sono il meccanismo di sicurezza principale per la messaggistica del bus di servizio. Questo articolo illustra le firme di accesso condiviso, il loro funzionamento e come usarle in modo indipendente dalla piattaforma.

L'autenticazione con firma di accesso condiviso consente alle applicazioni di eseguire l'autenticazione al bus di servizio usando una chiave di accesso configurata nello spazio dei nomi o nell'entità di messaggistica, ad esempio coda o argomento, a cui sono associati diritti specifici. È quindi possibile usare questa chiave per generare un token di firma di accesso condiviso di cui possono avvalersi i client per eseguire l'autenticazione al bus di servizio.

Il supporto per l'autenticazione della firma di accesso condiviso è inclusa in Azure SDK 2.0 e versioni successive.

## <a name="overview-of-sas"></a>Panoramica di SAS

Le firme di accesso condiviso sono un meccanismo di autenticazione basato su hash sicuri SHA-256 o URI. SAS è un meccanismo estremamente efficace utilizzato da tutti i servizi del bus di servizio. Nell'uso effettivo, SAS presenta due componenti: i *criteri di accesso condiviso* e una *firma di accesso condiviso* che viene spesso chiamata *token*.

Nel bus di servizio, l'autenticazione della firma di accesso condiviso implica la configurazione di una chiave di crittografia con i relativi diritti in una risorsa del bus di servizio. I client richiedono l'accesso alle risorse del bus di servizio presentando un token di firma di accesso condiviso. Questo token è costituito dall'URI della risorsa a cui si accede e da una scadenza firmata con la chiave configurata.

È possibile configurare le regole di autorizzazione della firma di accesso condiviso in [inoltri](service-bus-fundamentals-hybrid-solutions.md#relays), [code](service-bus-fundamentals-hybrid-solutions.md#queues) e [argomenti](service-bus-fundamentals-hybrid-solutions.md#topics) del bus di servizio.

L'autenticazione della firma di accesso condiviso usa gli elementi seguenti:

* [Regola di autorizzazione per l'accesso condiviso](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule): una chiave di crittografia primaria a 256 bit in formato Base 64, una chiave secondaria facoltativa e un nome di chiave e i diritti associati (una raccolta di diritti di tipo *Listen*, *Send* o *Manage*).
* [firma di accesso condiviso](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) : generato usando l'algoritmo HMAC-SHA256 di una stringa di risorsa, costituito dall'URI della risorsa a cui si accede e da una scadenza, con la chiave di crittografia. La firma e gli altri elementi descritti nelle sezioni seguenti vengono formattati in una stringa per formare un token di firma di accesso condiviso.

## <a name="shared-access-policy"></a>Criteri di accesso condiviso

Un elemento importante da comprendere rispetto alle firme di accesso condiviso è che iniziano con un criterio. Per ogni criterio si stabiliscono tre informazioni: **nome**, **ambito** e **autorizzazioni**. Il **nome** è un nome univoco all'interno dell’ambito. L'ambito è abbastanza semplice: è l'URI della risorsa in questione. Per uno spazio dei nomi del bus di servizio, l'ambito è il nome di dominio completo (FQDN), ad esempio `https://<yournamespace>.servicebus.windows.net/`.

Le autorizzazioni disponibili per un criterio sono in gran parte autoesplicative:

* Invio
* Attesa
* Gestisci

Dopo aver creato il criterio, gli vengono assegnate una *chiave primaria* e una *chiave secondaria*. Si tratta di chiavi di crittografia complesse. Queste chiavi non possono essere perse perché sono sempre disponibili nel [portale di Azure][Azure portal]. È possibile utilizzare una delle chiavi generate ed è possibile rigenerarle in qualsiasi momento. Tuttavia, se si rigenera o si modifica la chiave primaria nel criterio, le firme di accesso condiviso create da tale chiave verranno invalidate.

Quando si crea uno spazio dei nomi del bus di servizio, per l'intero spazio dei nomi viene creato automaticamente un criterio con tutte le autorizzazioni denominato **RootManageSharedAccessKey**. Non accedere come **radice** e non usare questo criterio a meno che non esista un motivo molto valido. È possibile creare criteri aggiuntivi nella scheda **Configura** per lo spazio dei nomi nel portale. È importante notare che a un singolo livello della struttura ad albero nel bus di servizio (spazio dei nomi, coda e così via) può essere associato un massimo di 12 criteri.

## <a name="configuration-for-shared-access-signature-authentication"></a>Configurazione dell'autenticazione della firma di accesso condiviso
È possibile configurare la regola [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) in spazi dei nomi, code, argomenti del bus di servizio. La configurazione di una regola [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) in una sottoscrizione del bus di servizio non è attualmente supportata, ma è possibile usare le regole configurate in uno spazio dei nomi o in un argomento per proteggere l'accesso alle sottoscrizioni. Per un esempio pratico di questa procedura, vedere l'articolo relativo all' [uso dell'autenticazione della firma di accesso condiviso con le sottoscrizioni del bus di servizio](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) .

In uno spazio dei nomi, una coda o un argomento del bus di servizio è possibile configurare fino 12 regole di questo tipo. Le regole configurate in uno spazio dei nomi del bus di servizio si applicano a tutte le entità incluse nello spazio dei nomi.

![SAS](./media/service-bus-sas/service-bus-namespace.png)

In questa figura le regole di autorizzazione *manageRuleNS*, *sendRuleNS* e *listenRuleNS* si applicano sia alla coda Q1 che all'argomento T1, mentre *listenRuleQ* e *sendRuleQ* si applicano solo alla coda Q1 e *sendRuleT* si applica solo all'argomento T1.

I parametri principali di [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) sono i seguenti:

| Parametro | DESCRIZIONE |
| --- | --- |
| *KeyName* |Stringa che descrive la regola di autorizzazione. |
| *PrimaryKey* |Chiave primaria a 256 bit con codifica Base 64 per la firma e la convalida del token di firma di accesso condiviso. |
| *SecondaryKey* |Chiave secondaria a 256 bit con codifica Base 64 per la firma e la convalida del token di firma di accesso condiviso. |
| *AccessRights* |Elenco di diritti di accesso concessi dalla regola di autorizzazione. Questi diritti possono essere qualsiasi raccolta di diritti di ascolto, invio e gestione. |

Quando viene eseguito il provisioning di uno spazio dei nomi del bus di servizio, per impostazione predefinita viene creata una regola [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), con [KeyName](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_KeyName) impostato su **RootManageSharedAccessKey**.

## <a name="generate-a-shared-access-signature-token"></a>Generare una firma di accesso condiviso (token)

Il criterio non è il token di accesso del bus di servizio. È l'oggetto da cui viene generato il token di accesso, utilizzando la chiave primaria o secondaria. Qualsiasi client che abbia accesso alle chiavi di firma specificate nella regola di autorizzazione di accesso condiviso può generare il token di firma di accesso condiviso. Il token viene generato creando attentamente una stringa nel formato seguente:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

Dove `signature-string` è l'hash SHA-256 dell'ambito del token (**ambito** come descritto nella sezione precedente) con un CRLF accodato e una scadenza (in secondi dal periodo: `00:00:00 UTC` l'1 gennaio 1970). 

> [!NOTE]
> Per evitare che i token scadano rapidamente è consigliabile codificare il valore della scadenza come almeno un numero intero senza segno a 32 bit o preferibilmente un numero intero lungo a 64 bit.  
> 
> 

L'hash è simile al seguente pseudocodice e restituisce 32 byte.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

I valori non hash si trovano nella stringa **SharedAccessSignature** per cui il destinatario può calcolare l'hash con gli stessi parametri, per essere certo che restituisca lo stesso risultato. L'URI specifica l'ambito e il nome della chiave identifica i criteri da utilizzare per calcolare l'hash. Questo è importante dal punto di vista della sicurezza. Se la firma non corrisponde a quella calcolata dal destinatario (bus di servizio), l'accesso viene negato. A questo punto si può essere certi che il mittente abbia accesso alla chiave e che disponga dei diritti specificati nei criteri.

Si noti che è necessario usare l'URI di risorsa codificato per questa operazione. L'URI di risorsa è l'URI completo della risorsa del bus di servizio a cui si richiede l'accesso. Ad esempio `http://<namespace>.servicebus.windows.net/<entityPath>` o `sb://<namespace>.servicebus.windows.net/<entityPath>`, ovvero `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`.

La regola di autorizzazione di accesso condiviso usata per la firma deve essere configurata nell'entità specificata da questo URI o in un elemento padre nella gerarchia. Ad esempio `http://contoso.servicebus.windows.net/contosoTopics/T1` o `http://contoso.servicebus.windows.net` nell'esempio precedente.

Un token di firma di accesso condiviso è valido per tutte le risorse nell'elemento `<resourceURI>` usato in `signature-string`.

L'oggetto [KeyName](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_KeyName) nel token di firma di accesso condiviso fa riferimento all'oggetto **keyName** della regola di autorizzazione di accesso condiviso usata per generare il token.

L'elemento *URL-encoded-resourceURI* deve corrispondere all'URI usato nella stringa da firmare durante il calcolo della firma. Il valore deve essere [codificato in percentuale](https://msdn.microsoft.com/library/4fkewx0t.aspx).

È consigliabile rigenerare periodicamente le chiavi usate nella regola [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) . In genere le applicazioni usano il parametro [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) per generare un token di firma di accesso condiviso. Quando si rigenerano le chiavi, sostituire [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) con la chiave primaria precedente e generare una nuova chiave come nuova chiave primaria. In questo modo, è possibile continuare a usare i token per l'autorizzazione rilasciati con la chiave primaria precedente e non ancora scaduti.

Se una chiave viene compromessa ed è necessario revocare le chiavi, è possibile rigenerare entrambi gli oggetti [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) e [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) per una regola [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), sostituendo le chiavi precedenti con quelle nuove. Se viene eseguita questa procedura, tutti i token firmati con le chiavi precedenti non sono più validi.

## <a name="how-to-use-shared-access-signature-authentication-with-service-bus"></a>Come usare l'autenticazione della firma di accesso condiviso con il bus di servizio

Gli scenari illustrati di seguito includono la configurazione delle regole di autorizzazione, la generazione di token di firma di accesso condiviso e l'autorizzazione dei client.

Per un esempio pratico completo di un'applicazione del bus di servizio che illustra la configurazione e usa l'autorizzazione con firma di accesso condiviso, vedere [Autenticazione della firma di accesso condiviso con il bus di servizio](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Un esempio correlato che illustra l'uso delle regole di autorizzazione con firma di accesso condiviso configurate negli spazi dei nomi o negli argomenti per proteggere le sottoscrizioni del bus di servizio è disponibile nella pagina relativa all' [uso dell'autenticazione con firma di accesso condiviso con le sottoscrizioni del bus di servizio](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-a-namespace"></a>Accesso alle regole di autorizzazione per l'accesso condiviso in uno spazio dei nomi

Per le operazioni nella radice dello spazio dei nomi del bus di servizio è necessaria l'autenticazione tramite certificati. È necessario caricare un certificato di gestione per la sottoscrizione di Azure. Per caricare un certificato di gestione, seguire [questi passaggi](../cloud-services/cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) usando il [portale di Azure][Azure portal]. Per altre informazioni sui certificati di gestione di Azure, vedere la [Panoramica sui certificati di Azure](../cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

L'endpoint per l'accesso alle regole di autorizzazione per l'accesso condiviso in uno spazio dei nomi del bus di servizio è il seguente:

```http
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/
```

Per creare un oggetto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) in uno spazio dei nomi del bus di servizio, eseguire un'operazione POST in questo endpoint con le informazioni sulle regole serializzate come JSON o XML. Ad esempio: 

```csharp
// Base address for accessing authorization rules on a namespace
string baseAddress = @"https://management.core.windows.net/<subscriptionId>/services/ServiceBus/namespaces/<namespace>/AuthorizationRules/";

// Configure authorization rule with base64-encoded 256-bit key and Send rights
var sendRule = new SharedAccessAuthorizationRule("contosoSendAll",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send });

// Operations on the Service Bus namespace root require certificate authentication.
WebRequestHandler handler = new WebRequestHandler
{
    ClientCertificateOptions = ClientCertificateOption.Manual
};
// Access the management certificate by subject name
handler.ClientCertificates.Add(GetCertificate(<certificateSN>));

HttpClient httpClient = new HttpClient(handler)
{
    BaseAddress = new Uri(baseAddress)
};
httpClient.DefaultRequestHeaders.Accept.Add(
    new MediaTypeWithQualityHeaderValue("application/json"));
httpClient.DefaultRequestHeaders.Add("x-ms-version", "2015-01-01");

// Execute a POST operation on the baseAddress above to create an auth rule
var postResult = httpClient.PostAsJsonAsync("", sendRule).Result;
```

In modo analogo, usare un'operazione GET sull'endpoint per leggere le regole di autorizzazione configurate nello spazio dei nomi.

Per aggiornare o eliminare una regola di autorizzazione specifica, usare l'endpoint seguente:

```http
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/{KeyName}
```

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Accedere alle regole di autorizzazione per l'accesso condiviso in un'entità

È possibile accedere a un oggetto [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) configurato in una coda o in un argomento del bus di servizio tramite la raccolta [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) negli oggetti [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) o [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription) corrispondenti.

Il codice seguente illustra come aggiungere regole di autorizzazione per una coda.

```csharp
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString(
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## <a name="use-shared-access-signature-authorization"></a>Usare l'autorizzazione con firma di accesso condiviso

Le applicazioni che usano Azure .NET SDK con le librerie .NET del bus di servizio possono usare l'autorizzazione con firma di accesso condiviso nella classe [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) . Il codice seguente illustra l'uso del provider di token per inviare messaggi a una coda del bus di servizio.

```csharp
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb",
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri,
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

Le applicazioni possono anche usare la firma di accesso condiviso per l'autenticazione usando una stringa di connessione della firma di accesso condiviso nei metodi che accettano stringhe di connessione.

Si noti che per usare l'autorizzazione con firma di accesso condiviso con gli inoltri del bus di servizio è possibile usare le chiavi della firma di accesso condiviso configurate nello spazio dei nomi del bus di servizio. Se si crea in modo esplicito un inoltro per lo spazio dei nomi ([NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) con un oggetto [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription)), è possibile impostare le regole della firma di accesso condiviso solo per tale inoltro. Per usare l'autorizzazione con firma di accesso condiviso con le sottoscrizioni del bus di servizio è possibile usare le chiavi della firma di accesso condiviso configurate in uno spazio dei nomi o in un argomento del bus di servizio.

## <a name="use-the-shared-access-signature-at-http-level"></a>Usare la firma di accesso condiviso (a livello HTTP)

Dopo aver creato firme di accesso condiviso per tutte le entità nel bus di servizio, si è pronti a eseguire una richiesta HTTP POST:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 

Questa procedura funziona per ogni elemento. È possibile creare una firma di accesso condiviso per una coda, un argomento o una sottoscrizione. 

Se un token SAS viene assegnato a un mittente o ad un client, questi ultimi non dispongono della chiave direttamente e non possono invertire l'hash per ottenerla. Di conseguenza, è necessario controllare a cosa possono accedere e per quanto tempo. Se si modifica la chiave primaria nel criterio, le firme di accesso condiviso create da tale chiave verranno invalidate.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Usare la firma di accesso condiviso (a livello AMQP)

Nella sezione precedente, è stato illustrato come utilizzare il token SAS con una richiesta HTTP POST per l'invio di dati per il Bus di servizio. Com'è noto, è possibile accedere al bus di servizio usando il protocollo AMQP (Advanced Message Queuing Protocol), ovvero il protocollo preferito da usare per motivi di prestazioni in molti scenari. L'uso del token SAS con AMQP viene descritto nel documento dedicato ad [AMQP Claim-Based Security versione 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) , in fase di bozza dal 2013 ma attualmente supportato da Azure.

Prima di iniziare a inviare i dati al bus di servizio, il server di pubblicazione deve inviare il token di firma di accesso condiviso all'interno di un messaggio AMQP a un nodo AMQP ben definito denominato **"$cbs"**. Può essere visualizzato come una coda "speciale" usata dal servizio per acquisire e convalidare tutti i token di firma di accesso condiviso. Il server di pubblicazione deve specificare il campo **ReplyTo** all'interno del messaggio AMQP. Si tratta del nodo in cui il servizio invia una risposta al server di pubblicazione con il risultato della convalida del token. È un modello di richiesta/risposta semplice tra il server di pubblicazione e il servizio. Questo nodo risposta viene creato al momento in quanto "creazione dinamica di nodo remoto" come descritto nella specifica di AMQP 1.0. Dopo avere verificato che il token di firma di accesso condiviso è valido, il server di pubblicazione può andare avanti e iniziare a inviare dati al servizio.

La procedura seguente illustra come inviare il token SAS con il protocollo AMQP usando la libreria [AMQP.Net Lite](https://github.com/Azure/amqpnetlite) . È utile se non è possibile usare la versione ufficiale di Service Bus SDK (ad esempio su WinRT, .Net Compact Framework, .Net Micro Framework e Mono) che si sviluppa in C\#. Naturalmente, questa libreria è utile per comprendere il funzionamento della sicurezza basata sulle attestazioni a livello AMQP, dopo aver visto il funzionamento a livello HTTP (con una richiesta HTTP POST e il token SAS inviati all'interno dell'intestazione "Authorization"). Se non sono necessarie tali informazioni approfondite su AMQP, è possibile usare l'SDK ufficiale del bus di servizio con le applicazioni .Net Framework che eseguiranno l'operazione in modo automatico.

### <a name="c35"></a>C&#35;

```csharp
/// <summary>
/// Send claim-based security (CBS) token
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
    request.Properties.MessageId = Guid.NewGuid().ToString();
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

Il metodo `PutCbsToken()` riceve la *connessione*, vale a dire l'istanza della classe di connessione AMQP indicata dalla [libreria AMQP .NET Lite](https://github.com/Azure/amqpnetlite), che rappresenta la connessione TCP al servizio e il parametro *sasToken*, ovvero il token di firma di accesso condiviso da inviare. 

> [!NOTE]
> È importante che la connessione venga creata con il **meccanismo di autenticazione SASL impostato su EXTERNAL** (e non il PLAIN predefinito con nome utente e password usato quando non è necessario inviare il token SAS).
> 
> 

Successivamente, il server di pubblicazione crea due collegamenti AMQP per inviare il token SAS e ricevere la risposta (il risultato di convalida del token) dal servizio.

Il messaggio AMQP contiene un insieme di proprietà e altre informazioni rispetto a un semplice messaggio. Il token SAS rappresenta il corpo del messaggio (tramite il relativo costruttore). La proprietà **"ReplyTo"** è impostata sul nome del nodo per la ricezione del risultato di convalida sul collegamento ricevitore (se si desidera modificare il nome, è possibile farlo e verrà creato in modo dinamico dal servizio). Le ultime tre proprietà personalizzate/relative all'applicazione vengono usate dal servizio per indicare quale tipologia di operazione è necessario eseguire. Come descritto nella bozza di specifica CBS, devono essere il **nome dell'operazione** ("put-token"), il **tipo di token** (in questo caso, "servicebus.windows.net:sastoken") e il **"nome" del gruppo di destinatari** a cui viene applicato il token (l'intera entità).

Dopo aver inviato il token SAS sul collegamento mittente, il server di pubblicazione deve leggere la risposta sul collegamento ricevitore. La risposta è un semplice messaggio AMQP con una proprietà dell'applicazione denominata **"status-code"** che può contenere gli stessi valori di un codice di stato HTTP.

## <a name="rights-required-for-service-bus-operations"></a>Diritti necessari per le operazioni del bus di servizio

La tabella seguente illustra i diritti di accesso necessari per l'esecuzione di operazioni relative alle risorse del bus di servizio.

| Operazione | Attestazione necessaria | Ambito attestazione |
| --- | --- | --- |
| **Spazio dei nomi** | | |
| Configurare le regole di autorizzazione relative a uno spazio dei nomi |Gestisci |Qualsiasi indirizzo dello spazio dei nomi |
| **Registro di sistema del servizio** | | |
| Enumerare i criteri privati |Gestisci |Qualsiasi indirizzo dello spazio dei nomi |
| Iniziare l'attesa su uno spazio dei nomi del servizio |Attesa |Qualsiasi indirizzo dello spazio dei nomi |
| Inviare messaggi a un listener in uno spazio dei nomi |Invio |Qualsiasi indirizzo dello spazio dei nomi |
| **Coda** | | |
| Creare una coda |Gestisci |Qualsiasi indirizzo dello spazio dei nomi |
| Eliminare una coda |Gestisci |Qualsiasi indirizzo valido della coda |
| Enumerare le code |Gestisci |/$Resources/Queues |
| Ottenere la descrizione di una coda |Gestisci |Qualsiasi indirizzo valido della coda |
| Configurare le regole di autorizzazione per una coda |Gestisci |Qualsiasi indirizzo valido della coda |
| Effettuare un invio alla coda |Invio |Qualsiasi indirizzo valido della coda |
| Ricevere messaggi da una coda |Attesa |Qualsiasi indirizzo valido della coda |
| Abbandonare o completare messaggi dopo la ricezione del messaggio in modalità PeekLock (blocco di visualizzazione) |Attesa |Qualsiasi indirizzo valido della coda |
| Rinviare un messaggio per il successivo recupero |Attesa |Qualsiasi indirizzo valido della coda |
| Spostare un messaggio nella coda dei messaggi non recapitabili |Attesa |Qualsiasi indirizzo valido della coda |
| Ottenere lo stato associato a una sessione della coda dei messaggi |Attesa |Qualsiasi indirizzo valido della coda |
| Impostare lo stato associato a una sessione della coda dei messaggi |Attesa |Qualsiasi indirizzo valido della coda |
| **Argomento** | | |
| Creare un argomento |Gestisci |Qualsiasi indirizzo dello spazio dei nomi |
| Eliminare un argomento |Gestisci |Qualsiasi indirizzo valido dell'argomento |
| Enumerare gli argomenti |Gestisci |/$Resources/Topics |
| Ottenere la descrizione di un argomento |Gestisci |Qualsiasi indirizzo valido dell'argomento |
| Configurare le regole di autorizzazione per un argomento |Gestisci |Qualsiasi indirizzo valido dell'argomento |
| Effettuare un invio all'argomento |Invio |Qualsiasi indirizzo valido dell'argomento |
| **Sottoscrizione** | | |
| Creare una sottoscrizione |Gestisci |Qualsiasi indirizzo dello spazio dei nomi |
| Eliminare una sottoscrizione |Gestisci |../myTopic/Subscriptions/mySubscription |
| Enumerare le sottoscrizioni |Gestisci |../myTopic/Subscriptions |
| Ottenere la descrizione di una sottoscrizione |Gestisci |../myTopic/Subscriptions/mySubscription |
| Abbandonare o completare messaggi dopo la ricezione del messaggio in modalità PeekLock (blocco di visualizzazione) |Attesa |../myTopic/Subscriptions/mySubscription |
| Rinviare un messaggio per il successivo recupero |Attesa |../myTopic/Subscriptions/mySubscription |
| Spostare un messaggio nella coda dei messaggi non recapitabili |Attesa |../myTopic/Subscriptions/mySubscription |
| Ottenere lo stato associato a una sessione dell'argomento |Attesa |../myTopic/Subscriptions/mySubscription |
| Impostare lo stato associato a una sessione dell'argomento |Attesa |../myTopic/Subscriptions/mySubscription |
| **Regole** | | |
| Creare una regola |Gestisci |../myTopic/Subscriptions/mySubscription |
| Eliminare una regola |Gestisci |../myTopic/Subscriptions/mySubscription |
| Enumerare le regole |Manage o Listen |../myTopic/Subscriptions/mySubscription/Rules 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti.

* [Dati fondamentali del bus di servizio](service-bus-fundamentals-hybrid-solutions.md)
* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Come usare le code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com