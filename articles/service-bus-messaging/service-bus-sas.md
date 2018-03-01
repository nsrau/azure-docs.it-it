---
title: Controllo degli accessi del bus di servizio di Azure con firme di accesso condiviso | Microsoft Docs
description: Panoramica del controllo degli accessi del bus di servizio con firme di accesso condiviso, dettagli dell'autorizzazione con firme di accesso condiviso con il bus di servizio di Azure.
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
ms.date: 02/14/2018
ms.author: sethm;clemensv
ms.openlocfilehash: f6bb77ad6df09e36419b24b24924dac7ecd79065
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Controllo degli accessi del bus di servizio con firme di accesso condiviso

Le *firme di accesso condiviso* sono il meccanismo di sicurezza principale per la messaggistica del bus di servizio. Questo articolo illustra le firme di accesso condiviso, il loro funzionamento e come usarle in modo indipendente dalla piattaforma.

La firma di accesso condiviso consente inoltre l'accesso al bus di servizio in base alle regole di autorizzazione configurate in uno spazio dei nomi o in un'entità di messaggistica (inoltro, coda o argomento). Una regola di autorizzazione ha un nome, è associata a diritti specifici e include una coppia di chiavi di crittografia. Usare il nome e la chiave della regola tramite l'SDK del bus di servizio o nel proprio codice per generare un token di firma di accesso condiviso. Un client può quindi passare il token al bus di servizio per dimostrare l'autorizzazione per l'operazione richiesta.

## <a name="overview-of-sas"></a>Panoramica di SAS

Le firme di accesso condiviso (SAS) sono un meccanismo di autorizzazione basato sulle attestazioni che usa token semplici. Quando si usano le firme di accesso, le chiavi non vengono mai passate durante il transito. Le chiavi vengono usate per firmare crittograficamente informazioni che possono essere verificate in un secondo momento dal servizio. L'uso delle firme di accesso condiviso è paragonabile a quello della combinazione di nome utente e password in cui il client entra immediatamente in possesso di un nome di regola di autorizzazione e una chiave corrispondente. È paragonabile anche a un modello di sicurezza federata, in cui il client riceve un token di accesso firmato per un tempo limitato da un servizio token di sicurezza senza mai entrare in possesso della chiave di firma.

L'autenticazione SAS nel bus di servizio è configurata con il nome [Regole di autorizzazione di accesso condiviso](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) e a essa sono associati diritti di accesso e una coppia di chiavi di crittografia primaria e secondaria. Le chiavi sono valori a 256 bit nella rappresentazione Base64. È possibile configurare le regole a livello di spazio dei nomi nel bus di servizio [inoltri](service-bus-fundamentals-hybrid-solutions.md#relays), [code](service-bus-fundamentals-hybrid-solutions.md#queues) e [argomenti](service-bus-fundamentals-hybrid-solutions.md#topics).

Il token [Firma di accesso condiviso](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) include il nome della regola di autorizzazione scelta, l'URI della risorsa alla quale si avrà accesso, un'istante di scadenza e una firma crittografica HMAC-SHA256 calcolata in base a questi campi mediante la chiave crittografica primaria o secondaria della regola di autorizzazione scelta.

## <a name="shared-access-authorization-policies"></a>Criteri di autorizzazione dell'accesso condiviso

Ogni spazio dei nomi e ogni entità del bus di servizio prevede un criterio di autorizzazione dell'accesso condiviso costituito da regole. I criteri a livello di spazio dei nomi si applicano a tutte le entità in esso incluse, indipendentemente dalle specifiche configurazioni dei criteri.

Per ogni regola del criterio di autorizzazione si stabiliscono tre informazioni: **nome**, **ambito** e **diritti**. Il **nome** è un nome univoco all'interno dell’ambito. L'ambito è abbastanza semplice: è l'URI della risorsa in questione. Per uno spazio dei nomi del bus di servizio, l'ambito è il nome di dominio completo (FQDN), ad esempio `https://<yournamespace>.servicebus.windows.net/`.

I diritti assegnati dalla regola del criterio possono essere una combinazione di:

* "Send": conferisce il diritto di inviare messaggi all'entità
* "Listen": conferisce il diritto di ascolto (inoltro) o di ricezione (coda, sottoscrizioni) e di tutta la gestione correlata ai messaggi
* "Manage": conferisce il diritto di gestire la topologia dello spazio dei nomi, incluse la creazione e l'eliminazione di entità

Il diritto "Manage" include i diritti "Send" e "Receive".

Un criterio di entità o dello spazio dei nomi può contenere fino a 12 regole di autorizzazione di accesso condiviso, rendendo disponibile spazio per tre set di regole, ognuno dei quali copre i diritti di base e la combinazione di Send e Listen. Questo limite sottolinea che l'archivio dei criteri di firma di accesso condiviso non deve essere un utente o un archivio di account del servizio. Se l'applicazione deve concedere l'accesso al bus di servizio in base alle identità utente o del servizio, deve implementare un servizio token di sicurezza che rilascia token di firma di accesso condiviso dopo un controllo di autenticazione e accesso.

A una regola di autorizzazione vengono assegnate una *chiave primaria* e una *chiave secondaria*. Si tratta di chiavi di crittografia complesse. Queste chiavi non possono essere perse perché sono sempre disponibili nel [portale di Azure][Azure portal]. È possibile utilizzare una delle chiavi generate ed è possibile rigenerarle in qualsiasi momento. Se si rigenera o si modifica una chiave nel criterio, tutti i token emessi in precedenza in base a tale chiave diventano immediatamente non validi. Le connessioni in corso create in base a tali token continueranno invece a funzionare fino alla scadenza del token.

Quando si crea uno spazio dei nomi del bus di servizio, viene creato automaticamente un criterio denominato **RootManageSharedAccessKey**. Questo criterio dispone delle autorizzazioni Manage per l'intero spazio dei nomi. È consigliabile considerare questa regola come un account **radice** amministratore e non usarla nell'applicazione. È possibile creare regole aggiuntive dei criteri nella scheda **Configura** per lo spazio dei nomi nel portale mediante Powershell o l'interfaccia della riga di comando di Azure.

## <a name="configuration-for-shared-access-signature-authentication"></a>Configurazione dell'autenticazione della firma di accesso condiviso

È possibile configurare la regola [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) in spazi dei nomi, code, argomenti del bus di servizio. La configurazione di una regola [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) in una sottoscrizione del bus di servizio non è attualmente supportata, ma è possibile usare le regole configurate in uno spazio dei nomi o in un argomento per proteggere l'accesso alle sottoscrizioni. Per un esempio pratico di questa procedura, vedere l'articolo relativo all' [uso dell'autenticazione della firma di accesso condiviso con le sottoscrizioni del bus di servizio](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) .

![SAS](./media/service-bus-sas/service-bus-namespace.png)

In questa figura le regole di autorizzazione *manageRuleNS*, *sendRuleNS* e *listenRuleNS* si applicano sia alla coda Q1 che all'argomento T1, mentre *listenRuleQ* e *sendRuleQ* si applicano solo alla coda Q1 e *sendRuleT* si applica solo all'argomento T1.

## <a name="generate-a-shared-access-signature-token"></a>Generare un token della firma di accesso condiviso

Qualsiasi client che abbia accesso al nome di una regola di autorizzazione e a una delle relative chiavi di firma può generare un token di firma di accesso condiviso. Il token viene generato creando una stringa nel formato seguente:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* **`se`**: istante di scadenza del token. Valore intero che riflette i secondi trascorsi dalle `00:00:00 UTC` del 1 ° gennaio 1970 (epoca UNIX) quando il token scade.
* **`skn`**: nome della regola di autorizzazione.
* **`sr`**: URI della risorsa a cui si ha accesso.
* **`sig`**: firma.

`signature-string` è il codice hash SHA-256 calcolato in base all'URI della risorsa (l'**ambito** descritto nella sezione precedente) e la rappresentazione di stringa dell'istante di scadenza del token, separati da CRLF.

Il calcolo del codice hash è simile allo pseudo codice seguente e restituisce un valore hash a 256 bit o 32 byte.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Il token contiene i valori non hash in modo che il destinatario possa ricalcolare il codice hash con gli stessi parametri, verificando che l'autorità di certificazione sia in possesso di una chiave di firma valida. 

L'URI di risorsa è l'URI completo della risorsa del bus di servizio a cui si richiede l'accesso. Ad esempio `http://<namespace>.servicebus.windows.net/<entityPath>` o `sb://<namespace>.servicebus.windows.net/<entityPath>`, ovvero `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`. L'URI deve essere [codificato in percentuale](https://msdn.microsoft.com/library/4fkewx0t.aspx).

La regola di autorizzazione di accesso condiviso usata per la firma deve essere configurata nell'entità specificata da questo URI o in un elemento padre nella gerarchia. Ad esempio `http://contoso.servicebus.windows.net/contosoTopics/T1` o `http://contoso.servicebus.windows.net` nell'esempio precedente.

Un token di firma di accesso condiviso è valido per tutte le risorse precedute dal prefisso `<resourceURI>` usato in `signature-string`.

## <a name="regenerating-keys"></a>Rigenerazione delle chiavi

È consigliabile rigenerare periodicamente le chiavi usate nella regola [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) . La presenza degli slot di chiave primaria e secondaria cha lo scopo di consentire di ruotare le chiavi gradualmente. Se l'applicazione usa in genere la chiave primaria, è possibile copiare la chiave primaria nello slot della chiave secondaria e solo allora rigenerare la chiave primaria. Il nuovo valore di chiave primaria può essere quindi configurato nelle applicazioni client, che possono accedere in modo continuativo usando la chiave primaria precedente nello slot secondario. Dopo che tutti i client sono stati aggiornati, è possibile rigenerare la chiave secondaria per ritirare infine la chiave primaria precedente.

Se è noto o si sospetta che una chiave è compromessa ed è necessario revocare le chiavi, è possibile rigenerare entrambi gli oggetti [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) e [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) per una regola [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), sostituendo le chiavi precedenti con quelle nuove. Se viene eseguita questa procedura, tutti i token firmati con le chiavi precedenti non sono più validi.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Autenticazione della firma di accesso condiviso con il bus di servizio

Gli scenari illustrati di seguito includono la configurazione delle regole di autorizzazione, la generazione di token di firma di accesso condiviso e l'autorizzazione dei client.

Per un esempio pratico completo di un'applicazione del bus di servizio che illustra la configurazione e usa l'autorizzazione con firma di accesso condiviso, vedere [Autenticazione della firma di accesso condiviso con il bus di servizio](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Un esempio correlato che illustra l'uso delle regole di autorizzazione con firma di accesso condiviso configurate negli spazi dei nomi o negli argomenti per proteggere le sottoscrizioni del bus di servizio è disponibile nella pagina relativa all' [uso dell'autenticazione con firma di accesso condiviso con le sottoscrizioni del bus di servizio](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Accedere alle regole di autorizzazione per l'accesso condiviso in un'entità

Con le librerie .NET Framework del bus di servizio è possibile accedere a un oggetto [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) configurato in una coda o in un argomento del bus di servizio tramite la raccolta [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) negli oggetti [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) o [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription) corrispondenti.

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

Le applicazioni che usano Azure .NET SDK con le librerie .NET del bus di servizio possono usare l'autorizzazione con firma di accesso condiviso nella classe [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) . Il codice seguente illustra l'uso del provider di token per inviare messaggi a una coda del bus di servizio. In alternativa all'uso qui illustrato, è possibile anche passare un token emesso in precedenza al metodo factory del provider di token.

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

È anche possibile utilizzare direttamente il provider di token per il rilascio di token da passare ad altri client. 

Le stringhe di connessione possono includere un nome di regola (*SharedAccessKeyName*) e una chiave di regola (*SharedAccessKey*) o un token rilasciato in precedenza (*SharedAccessSignature*). Quando sono presenti nella stringa di connessione passata a un costruttore o a un metodo factory che accetta una stringa di connessione, il provider di token di firma di accesso condiviso viene automaticamente creato e popolato.

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

Se un token SAS viene assegnato a un mittente o ad un client, questi ultimi non dispongono della chiave direttamente e non possono invertire l'hash per ottenerla. Di conseguenza, è necessario controllare a cosa possono accedere e per quanto tempo. È importante tenere presente che, se si modifica la chiave primaria nel criterio, le firme di accesso condiviso create da tale chiave verranno invalidate.

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
> È importante che la connessione venga creata con il **meccanismo di autenticazione SASL impostato su ANONYMOUS** (e non sul valore predefinito PLAIN con nome utente e password usato quando non è necessario inviare il token SAS).
> 
> 

Successivamente, il server di pubblicazione crea due collegamenti AMQP per inviare il token SAS e ricevere la risposta (il risultato di convalida del token) dal servizio.

Il messaggio AMQP contiene un insieme di proprietà e altre informazioni rispetto a un semplice messaggio. Il token SAS rappresenta il corpo del messaggio (tramite il relativo costruttore). La proprietà **"ReplyTo"** è impostata sul nome del nodo per la ricezione del risultato di convalida sul collegamento ricevitore (se si desidera modificare il nome, è possibile farlo e verrà creato in modo dinamico dal servizio). Le ultime tre proprietà personalizzate/relative all'applicazione vengono usate dal servizio per indicare quale tipologia di operazione è necessario eseguire. Come descritto nella bozza di specifica CBS, devono essere il **nome dell'operazione** ("put-token"), il **tipo di token** (in questo caso, `servicebus.windows.net:sastoken`) e il  **"nome" del gruppo di destinatari** a cui viene applicato il token (l'intera entità).

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