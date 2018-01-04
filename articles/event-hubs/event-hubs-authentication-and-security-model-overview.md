---
title: Panoramica del modello di sicurezza e autenticazione di Hub eventi di Azure | Documentazione Microsoft
description: Panoramica sul modello di autenticazione e sicurezza di Hub eventi.
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 93841e30-0c5c-4719-9dc1-57a4814342e7
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2017
ms.author: sethm;clemensv
ms.openlocfilehash: bfe7b95236c1a5336c1bb3a93d0eb5ca880adabf
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="event-hubs-authentication-and-security-model-overview"></a>Panoramica sull’autenticazione di Hub eventi e sul modello di protezione

Il modello di sicurezza di Hub eventi di Azure soddisfa i requisiti seguenti:

* Solo i client che presentano le credenziali valide possono inviare dati a un hub eventi.
* Un client non può rappresentare un altro client.
* A un client non autorizzato può essere impedito l'invio di dati a un hub eventi.

## <a name="client-authentication"></a>Autenticazione client

Il modello di sicurezza di Hub eventi si basa su una combinazione di token di [firma di accesso condiviso](../service-bus-messaging/service-bus-sas.md) e *autori di eventi*. Un autore di eventi definisce un endpoint virtuale per un hub eventi. L'autore è utilizzabile solo per inviare messaggi a un hub eventi. Non è possibile ricevere messaggi da un autore.

In genere, un Hub eventi usa un autore per ogni client. Tutti i messaggi inviati a uno qualsiasi degli autori di un Hub eventi vengono accodati all'interno di tale Hub eventi. Gli autori consentono la limitazione e il controllo di accesso con granularità fine.

A ogni client di Hub eventi viene assegnato un token univoco, che viene caricato nel client. I token vengono prodotti in modo tale che ogni token univoco conceda l'accesso a un diverso autore univoco. Un client che possiede un token può inviare a un solo autore e a nessun altro autore. Se più client condividono lo stesso token, ognuno di essi condivide un autore.

Sebbene non sia consigliato, è possibile dotare i dispositivi di token che concedono l'accesso diretto a un Hub eventi. Qualsiasi dispositivo che contiene un token di questo tipo può inviare messaggi direttamente a tale hub eventi. Tale dispositivo non sarà soggetto alla limitazione. Non è possibile disattivare l'invio a tale Hub eventi per il dispositivo.

Tutti i token sono firmati con una chiave SAS. In genere, tutti i token sono firmati con la stessa chiave. I client non conoscono la chiave, per cui altri client non possono produrre token.

### <a name="create-the-sas-key"></a>Creare la chiave SAS

Quando si crea uno spazio dei nomi di Hub eventi, il servizio genera automaticamente una chiave di firma di accesso condiviso a 256 bit denominata **RootManageSharedAccessKey**. Questa regola ha una coppia associata di chiavi primaria e secondaria che concedono i diritti di invio, ascolto e gestione per lo spazio dei nomi. È anche possibile creare chiavi aggiuntive. Si consiglia di produrre una chiave che concede le autorizzazioni di invio allo specifico Hub eventi. Nella parte restante di questo argomento si presuppone che questa chiave sia denominata **EventHubSendKey**.

Nell'esempio seguente viene creata una chiave di solo invio durante la creazione dell'Hub eventi:

```csharp
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create event hub with a SAS rule that enables sending to that event hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### <a name="generate-tokens"></a>Generare token

È possibile generare token utilizzando la chiave SAS. È necessario ottenere solo un token per client. È possibile produrre token utilizzando il metodo riportato di seguito. Tutti i token vengono generati utilizzando la chiave **EventHubSendKey** . A ogni token viene assegnato un URI univoco.

```csharp
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

Quando si chiama questo metodo, l'URI deve essere specificato come `//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`. Per tutti i token l'URI è identico, ad eccezione di `PUBLISHER_NAME`, che deve essere diverso per ogni token. In teoria, `PUBLISHER_NAME` rappresenta l'ID del client che riceve il token.

Questo metodo genera un token con la struttura seguente:

```csharp
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

L'ora di scadenza del token è espressa in secondi dal 1 gennaio 1970. Di seguito è riportato un esempio di token:

```csharp
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

In genere, i token hanno una durata simile o superiore a quella del client. Se il client è in grado di ottenere un nuovo token, è possibile usare token con una durata più breve.

### <a name="sending-data"></a>Invio di dati

Dopo avere creato i token, viene eseguito il provisioning di ogni client con il proprio token univoco.

Quando il client invia dati a un hub eventi, contrassegna la richiesta di invio con il token. Per evitare che un utente malintenzionato intercetti e rubi il token, la comunicazione tra il client e l'Hub eventi deve verificarsi su un canale crittografato.

### <a name="blacklisting-clients"></a>Disattivazione dei client

In caso di furto di un token da parte di un utente malintenzionato, l'autore dell'attacco può rappresentare il client il cui token è stato rubato. La disattivazione di un client rende il rendering di tale client inutilizzabile fino a che non riceve un nuovo token che usa un autore diverso.

## <a name="authentication-of-back-end-applications"></a>Autenticazione delle applicazioni back-end

Per autenticare le applicazioni back-end che usano i dati generati dai client di Hub eventi, Hub eventi usa un modello di sicurezza simile al modello usato per gli argomenti del bus di servizio. Un gruppo di consumer di Hub eventi equivale a una sottoscrizione a un argomento del bus di servizio. Un client può creare un gruppo di consumer se la richiesta di creazione è accompagnata da un token che concede privilegi di gestione per l'hub eventi o per lo spazio dei nomi a cui appartiene l'hub eventi. Un client può usare dati di un gruppo di consumer se la richiesta di ricezione è accompagnata da un token che concede i diritti di ricezione in tale gruppo di consumer, l'Hub eventi o lo spazio dei nomi a cui appartiene l'Hub eventi.

La versione corrente del bus di servizio non supporta regole di firma di accesso condiviso per sottoscrizioni singole. Lo stesso vale per i gruppi di consumer di Hub eventi. In futuro verrà aggiunto il supporto SAS per entrambe le funzionalità.

In assenza di autenticazione SAS per gruppi di consumer singoli, è possibile utilizzare chiavi SAS per proteggere tutti i gruppi di consumer con una chiave comune. Questo approccio consente a un'applicazione di usare dati di tutti i gruppi di consumer di un Hub eventi.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Hub eventi, vedere gli argomenti seguenti:

* [Panoramica di Hub eventi]
* [Panoramica delle firme di accesso condiviso]
* [Applicazioni di esempio che usano Hub eventi]

[Panoramica di Hub eventi]: event-hubs-what-is-event-hubs.md
[Applicazioni di esempio che usano Hub eventi]: https://github.com/Azure/azure-event-hubs/tree/master/samples
[Panoramica delle firme di accesso condiviso]: ../service-bus-messaging/service-bus-sas.md

