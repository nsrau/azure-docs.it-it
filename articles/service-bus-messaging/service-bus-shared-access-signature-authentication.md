---
title: Autenticazione della firma di accesso condiviso con il bus di servizio | Documentazione Microsoft
description: Informazioni dettagliate sull&quot;autenticazione della firma di accesso condiviso con il bus di servizio.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 1690eb8e-28ae-49bb-aeaa-022cda34c5a4
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 57aec98a681e1cb5d75f910427975c6c3a1728c3
ms.openlocfilehash: 70a4c5ee79a5c74d63d461371f70b49a23d5cf25


---
# <a name="shared-access-signature-authentication-with-service-bus"></a>Autenticazione della firma di accesso condiviso con il bus di servizio
[firma di accesso condiviso](service-bus-sas-overview.md) consente alle applicazioni di eseguire l'autenticazione nel bus di servizio usando una chiave di accesso configurata nello spazio dei nomi o nell'entità di messaggistica (coda o argomento) a cui sono associati diritti specifici. È quindi possibile usare questa chiave per generare un token di firma di accesso condiviso di cui possono avvalersi i client per eseguire l'autenticazione al bus di servizio.

Il supporto per l'autenticazione della firma di accesso condiviso è inclusa in Azure SDK 2.0 e versioni successive. Per altre informazioni sull'autenticazione del bus di servizio, vedere [Autenticazione e autorizzazione del bus di servizio](service-bus-authentication-and-authorization.md).

## <a name="concepts"></a>Concetti
Nel bus di servizio, l'autenticazione della firma di accesso condiviso implica la configurazione di una chiave di crittografia con i relativi diritti in una risorsa del bus di servizio. I client richiedono l'accesso alle risorse del bus di servizio presentando un token di firma di accesso condiviso. Questo token è costituito dall'URI della risorsa a cui si accede e da una scadenza firmata con la chiave configurata.

È possibile configurare le regole di autorizzazione della firma di accesso condiviso in Hub eventi e in [inoltri](service-bus-fundamentals-hybrid-solutions.md#relays), [code](service-bus-fundamentals-hybrid-solutions.md#queues) e [argomenti](service-bus-fundamentals-hybrid-solutions.md#topics) del bus di servizio.

L'autenticazione della firma di accesso condiviso usa gli elementi seguenti:

* [Regola di autorizzazione per l'accesso condiviso](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx): una chiave di crittografia primaria a 256 bit in formato Base 64, una chiave secondaria facoltativa e un nome di chiave e i diritti associati (una raccolta di diritti di tipo *Listen*, *Send* o *Manage*).
* [firma di accesso condiviso](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.sharedaccesssignature.aspx) : generato usando l'algoritmo HMAC-SHA256 di una stringa di risorsa, costituito dall'URI della risorsa a cui si accede e da una scadenza, con la chiave di crittografia. La firma e gli altri elementi descritti nelle sezioni seguenti vengono formattati in una stringa per formare un token [SharedAccessSignature](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.sharedaccesssignature.aspx) .

## <a name="configuration-for-shared-access-signature-authentication"></a>Configurazione dell'autenticazione della firma di accesso condiviso
È possibile configurare la regola [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) in spazi dei nomi, code, argomenti del bus di servizio. La configurazione di una regola [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) in una sottoscrizione del bus di servizio non è attualmente supportata, ma è possibile usare le regole configurate in uno spazio dei nomi o in un argomento per proteggere l'accesso alle sottoscrizioni. Per un esempio pratico di questa procedura, vedere l'articolo relativo all' [uso dell'autenticazione della firma di accesso condiviso con le sottoscrizioni del bus di servizio](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) .

In uno spazio dei nomi, una coda o un argomento del bus di servizio è possibile configurare fino 12 regole di questo tipo. Le regole configurate in uno spazio dei nomi del bus di servizio si applicano a tutte le entità incluse nello spazio dei nomi.

![SAS](./media/service-bus-shared-access-signature-authentication/IC676272.gif)

In questa figura le regole di autorizzazione *manageRuleNS*, *sendRuleNS* e *listenRuleNS* si applicano sia alla coda Q1 che all'argomento T1, mentre *listenRuleQ* e *sendRuleQ* si applicano solo alla coda Q1 e *sendRuleT* si applica solo all'argomento T1.

I parametri principali di [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) sono i seguenti:

| Parametro | Descrizione |
| --- | --- |
| *KeyName* |Stringa che descrive la regola di autorizzazione. |
| *PrimaryKey* |Chiave primaria a 256 bit con codifica Base 64 per la firma e la convalida del token di firma di accesso condiviso. |
| *SecondaryKey* |Chiave secondaria a 256 bit con codifica Base 64 per la firma e la convalida del token di firma di accesso condiviso. |
| *AccessRights* |Elenco di diritti di accesso concessi dalla regola di autorizzazione. Questi diritti possono essere qualsiasi raccolta di diritti di ascolto, invio e gestione. |

Quando viene eseguito il provisioning di uno spazio dei nomi del bus di servizio, per impostazione predefinita viene creata una regola [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx), con [KeyName](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.keyname.aspx) impostato su **RootManageSharedAccessKey**.

## <a name="regenerate-and-revoke-keys-for-shared-access-authorization-rules"></a>Rigenerazione e revoca delle chiavi per le regole di autorizzazione di accesso condiviso
È consigliabile rigenerare periodicamente le chiavi usate nella regola [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) . In genere le applicazioni usano il parametro [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) per generare un token di firma di accesso condiviso. Quando si rigenerano le chiavi, sostituire [SecondaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.secondarykey.aspx) con la chiave primaria precedente e generare una nuova chiave come nuova chiave primaria. In questo modo, è possibile continuare a usare i token per l'autorizzazione rilasciati con la chiave primaria precedente e non ancora scaduti.

Se una chiave viene compromessa ed è necessario revocare le chiavi, è possibile rigenerare entrambi gli oggetti [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) e [SecondaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.secondarykey.aspx) per una regola [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx), sostituendo le chiavi precedenti con quelle nuove. Se viene eseguita questa procedura, tutti i token firmati con le chiavi precedenti non sono più validi.

## <a name="generating-a-shared-access-signature-token"></a>Generazione di un token di firma di accesso condiviso
Qualsiasi client che abbia accesso alle chiavi di firma specificate nella regola di autorizzazione di accesso condiviso può generare il token di firma di accesso condiviso. Il token è formattato come illustrato di seguito:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

La **firma** per il token di firma di accesso condiviso viene calcolata usando l'algoritmo HMAC-SHA256 di una stringa da firmare con la proprietà [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) di una regola di autorizzazione. La stringa da firmare è costituita da un URI di risorsa e una scadenza ed è formattata come illustrato di seguito:

```
StringToSign = <resourceURI> + "\n" + expiry;
```

Si noti che è necessario usare l'URI di risorsa codificato per questa operazione. L'URI di risorsa è l'URI completo della risorsa del bus di servizio a cui si richiede l'accesso. Ad esempio `http://<namespace>.servicebus.windows.net/<entityPath>` o `sb://<namespace>.servicebus.windows.net/<entityPath>`, ovvero `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`.

La scadenza è rappresentata dal numero di secondi trascorsi dalle 00:00:00 UTC dell'1 gennaio 1970.

La regola di autorizzazione di accesso condiviso usata per la firma deve essere configurata nell'entità specificata da questo URI o in un elemento padre nella gerarchia. Ad esempio `http://contoso.servicebus.windows.net/contosoTopics/T1` o `http://contoso.servicebus.windows.net` nell'esempio precedente.

Un token di firma di accesso condiviso è valido per tutte le risorse nell'elemento `<resourceURI>` usato stringa da firmare.

L'oggetto [KeyName](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.keyname.aspx) nel token di firma di accesso condiviso fa riferimento all'oggetto **keyName** della regola di autorizzazione di accesso condiviso usata per generare il token.

L'elemento *URL-encoded-resourceURI* deve corrispondere all'URI usato nella stringa da firmare durante il calcolo della firma. Il valore deve essere [codificato in percentuale](https://msdn.microsoft.com/library/4fkewx0t.aspx).

## <a name="how-to-use-shared-access-signature-authentication-with-service-bus"></a>Come usare l'autenticazione della firma di accesso condiviso con il bus di servizio
Gli scenari illustrati di seguito includono la configurazione delle regole di autorizzazione, la generazione di token di firma di accesso condiviso e l'autorizzazione dei client.

Per un esempio pratico completo di un'applicazione del bus di servizio che illustra la configurazione e usa l'autorizzazione con firma di accesso condiviso, vedere [Autenticazione della firma di accesso condiviso con il bus di servizio](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Un esempio correlato che illustra l'uso delle regole di autorizzazione con firma di accesso condiviso configurate negli spazi dei nomi o negli argomenti per proteggere le sottoscrizioni del bus di servizio è disponibile nella pagina relativa all' [uso dell'autenticazione con firma di accesso condiviso con le sottoscrizioni del bus di servizio](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-a-namespace"></a>Accesso alle regole di autorizzazione per l'accesso condiviso in uno spazio dei nomi
Per le operazioni nella radice dello spazio dei nomi del bus di servizio è necessaria l'autenticazione tramite certificati. È necessario caricare un certificato di gestione per la sottoscrizione di Azure. Per caricare un certificato di gestione, fare clic su **Impostazioni** nel riquadro a sinistra del [portale di Azure classico][portale di Azure classico]. Per altre informazioni sui certificati di gestione di Azure, vedere la [Panoramica sui certificati di Azure](../cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

L'endpoint per l'accesso alle regole di autorizzazione per l'accesso condiviso in uno spazio dei nomi del bus di servizio è il seguente:

```
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/
```

Per creare un oggetto [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) in uno spazio dei nomi del bus di servizio, eseguire un'operazione POST in questo endpoint con le informazioni sulle regole serializzate come JSON o XML. Ad esempio:

```
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

```
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/{KeyName}
```

## <a name="accessing-shared-access-authorization-rules-on-an-entity"></a>Accesso alle regole di autorizzazione per l'accesso condiviso in un'entità
È possibile accedere a un oggetto [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) configurato in una coda o in un argomento del bus di servizio tramite la raccolta [AuthorizationRules](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.authorizationrules.aspx) negli oggetti [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx), [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) o [NotificationHubDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.notifications.notificationhubdescription.aspx) corrispondenti.

Il codice seguente illustra come aggiungere regole di autorizzazione per una coda.

```
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

## <a name="using-shared-access-signature-authorization"></a>Uso dell'autorizzazione con firma di accesso condiviso
Le applicazioni che usano Azure .NET SDK con le librerie .NET del bus di servizio possono usare l'autorizzazione con firma di accesso condiviso nella classe [SharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.aspx) . Il codice seguente illustra l'uso del provider di token per inviare messaggi a una coda del bus di servizio.

```
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

Si noti che per usare l'autorizzazione con firma di accesso condiviso con gli inoltri del bus di servizio è possibile usare le chiavi della firma di accesso condiviso configurate nello spazio dei nomi del bus di servizio. Se si crea in modo esplicito un inoltro per lo spazio dei nomi ([NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) con un oggetto [RelayDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.relaydescription.aspx)), è possibile impostare le regole della firma di accesso condiviso solo per tale inoltro. Per usare l'autorizzazione con firma di accesso condiviso con le sottoscrizioni del bus di servizio è possibile usare le chiavi della firma di accesso condiviso configurate in uno spazio dei nomi o in un argomento del bus di servizio.

## <a name="rights-required-for-service-bus-operations"></a>Diritti necessari per le operazioni del bus di servizio
La tabella seguente illustra i diritti di accesso necessari per l'esecuzione di operazioni relative alle risorse del bus di servizio.

| Operazione | Attestazione necessaria | Ambito attestazione |
| --- | --- | --- |
| **Spazio dei nomi** | | |
| Configurare le regole di autorizzazione relative a uno spazio dei nomi |Manage |Qualsiasi indirizzo dello spazio dei nomi |
| **Registro di sistema del servizio** | | |
| Enumerare i criteri privati |Manage |Qualsiasi indirizzo dello spazio dei nomi |
| Inoltro WCF | | |
| Iniziare l'attesa su uno spazio dei nomi del servizio |Attesa |Qualsiasi indirizzo dello spazio dei nomi |
| Inviare messaggi a un listener in uno spazio dei nomi |Invio |Qualsiasi indirizzo dello spazio dei nomi |
| **Coda** | | |
| Creare una coda |Manage |Qualsiasi indirizzo dello spazio dei nomi |
| Eliminare una coda |Manage |Qualsiasi indirizzo valido della coda |
| Enumerare le code |Manage |/$Resources/Queues |
| Ottenere la descrizione di una coda |Manage o Send |Qualsiasi indirizzo valido della coda |
| Configurare le regole di autorizzazione per una coda |Manage |Qualsiasi indirizzo valido della coda |
| Effettuare un invio alla coda |Invio |Qualsiasi indirizzo valido della coda |
| Ricevere messaggi da una coda |Attesa |Qualsiasi indirizzo valido della coda |
| Abbandonare o completare messaggi dopo la ricezione del messaggio in modalità PeekLock (blocco di visualizzazione) |Attesa |Qualsiasi indirizzo valido della coda |
| Rinviare un messaggio per il successivo recupero |Attesa |Qualsiasi indirizzo valido della coda |
| Spostare un messaggio nella coda dei messaggi non recapitabili |Attesa |Qualsiasi indirizzo valido della coda |
| Ottenere lo stato associato a una sessione della coda dei messaggi |Attesa |Qualsiasi indirizzo valido della coda |
| Impostare lo stato associato a una sessione della coda dei messaggi |Attesa |Qualsiasi indirizzo valido della coda |
| **Argomento** | | |
| Creare un argomento |Manage |Qualsiasi indirizzo dello spazio dei nomi |
| Eliminare un argomento |Manage |Qualsiasi indirizzo valido dell'argomento |
| Enumerare gli argomenti |Manage |/$Resources/Topics |
| Ottenere la descrizione di un argomento |Manage o Send |Qualsiasi indirizzo valido dell'argomento |
| Configurare le regole di autorizzazione per un argomento |Manage |Qualsiasi indirizzo valido dell'argomento |
| Effettuare un invio all'argomento |Invio |Qualsiasi indirizzo valido dell'argomento |
| **Sottoscrizione** | | |
| Creare una sottoscrizione |Manage |Qualsiasi indirizzo dello spazio dei nomi |
| Eliminare una sottoscrizione |Manage |../myTopic/Subscriptions/mySubscription |
| Enumerare le sottoscrizioni |Manage |../myTopic/Subscriptions |
| Ottenere la descrizione di una sottoscrizione |Manage o Listen |../myTopic/Subscriptions/mySubscription |
| Abbandonare o completare messaggi dopo la ricezione del messaggio in modalità PeekLock (blocco di visualizzazione) |Attesa |../myTopic/Subscriptions/mySubscription |
| Rinviare un messaggio per il successivo recupero |Attesa |../myTopic/Subscriptions/mySubscription |
| Spostare un messaggio nella coda dei messaggi non recapitabili |Attesa |../myTopic/Subscriptions/mySubscription |
| Ottenere lo stato associato a una sessione dell'argomento |Attesa |../myTopic/Subscriptions/mySubscription |
| Impostare lo stato associato a una sessione dell'argomento |Attesa |../myTopic/Subscriptions/mySubscription |
| **Regola** | | |
| Creare una regola |Manage |../myTopic/Subscriptions/mySubscription |
| Eliminare una regola |Manage |../myTopic/Subscriptions/mySubscription |
| Enumerare le regole |Manage o Listen |../myTopic/Subscriptions/mySubscription/Rules |
| **Hub di notifica** | | |
| Creare un hub di notifica |Manage |Qualsiasi indirizzo dello spazio dei nomi |
| Creare o aggiornare la registrazione per un dispositivo attivo |Listen o Manage |../notificationHub/tags/{tag}/registrations |
| Aggiornare le informazioni PNS |Listen o Manage |../notificationHub/tags/{tag}/registrations/updatepnshandle |
| Effettuare un invio a un hub di notifica |Invio |../notificationHub/messages |

## <a name="next-steps"></a>Passaggi successivi
Per una panoramica generale della firma di accesso condiviso nel bus di servizio, vedere [Firme di accesso condiviso](service-bus-sas-overview.md).

Per altre informazioni sull’autenticazione del bus di servizi, vedere [Autenticazione e autorizzazione del bus di servizio](service-bus-authentication-and-authorization.md) .

[portale di Azure classico]: http://manage.windowsazure.com



<!--HONumber=Nov16_HO3-->


