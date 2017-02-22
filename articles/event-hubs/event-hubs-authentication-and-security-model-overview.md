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
ms.date: 11/30/2016
ms.author: sethm;clemensv
translationtype: Human Translation
ms.sourcegitcommit: 05ca343cfdfc602759eb3ea30a7186a0bb47bd74
ms.openlocfilehash: 3f20a19c212c082aa766cc2bd67e938aaabf715e


---
# <a name="event-hubs-authentication-and-security-model-overview"></a>Panoramica sull’autenticazione di Hub eventi e sul modello di protezione
Il modello di sicurezza di Hub eventi di Azure soddisfa i requisiti seguenti:

* Solo i dispositivi che presentano le credenziali valide possono inviare dati a un Hub eventi.
* Un dispositivo non può rappresentare un altro dispositivo.
* A un dispositivo non autorizzato può essere impedito l’invio di dati a un Hub eventi.

## <a name="device-authentication"></a>Autenticazione del dispositivo
Il modello di sicurezza di Hub eventi si basa su una combinazione di token di [firma di accesso condiviso](../service-bus-messaging/service-bus-shared-access-signature-authentication.md) e *autori di eventi*. Un autore di eventi definisce un endpoint virtuale per un Hub eventi. L’autore è utilizzabile solo per inviare messaggi a un Hub eventi. Non è possibile ricevere messaggi da un autore.

In genere, un Hub eventi utilizza un autore per ogni dispositivo. Tutti i messaggi inviati a uno qualsiasi degli autori di un Hub eventi vengono accodati all'interno di tale Hub eventi. Gli autori consentono la limitazione e il controllo di accesso con granularità fine.

A ogni dispositivo viene assegnato un token univoco, che viene caricato nel dispositivo. I token vengono prodotti in modo tale che ogni token univoco conceda l'accesso a un diverso autore univoco. Un dispositivo che possiede un token può inviare a un solo autore e a nessun altro autore. Se più dispositivi condividono lo stesso token, ognuno di tali dispositivi condivide un autore.

Sebbene non sia consigliato, è possibile dotare i dispositivi di token che concedono l'accesso diretto a un Hub eventi. Qualsiasi dispositivo che contiene un token di questo tipo può inviare messaggi direttamente a tale hub eventi. Tale dispositivo non sarà soggetto alla limitazione. Inoltre, non è possibile disattivare l’invio a tale Hub eventi per il dispositivo.

Tutti i token sono firmati con una chiave SAS. In genere, tutti i token sono firmati con la stessa chiave. I dispositivi non conoscono la chiave, per cui non possono produrre token.

### <a name="create-the-sas-key"></a>Creare la chiave SAS
Quando si crea uno spazio dei nomi di Hub eventi di Azure, il servizio genera una chiave di firma di accesso condiviso a 256 bit denominata **RootManageSharedAccessKey**. Tale chiave concede diritti di invio, attesa e gestione allo spazio dei nomi. È possibile creare ulteriori chiavi. Si consiglia di produrre una chiave che concede le autorizzazioni di invio allo  specifico Hub eventi. Nella parte restante di questo argomento si presuppone che questa chiave sia denominata **EventHubSendKey**.

Nell'esempio seguente viene creata una chiave di solo invio durante la creazione dell'Hub eventi:

```csharp
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create Event Hub with a SAS rule that enables sending to that Event Hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### <a name="generate-tokens"></a>Generare token
È possibile generare token utilizzando la chiave SAS. È necessario ottenere solo un token per dispositivo. È possibile produrre token utilizzando il metodo riportato di seguito. Tutti i token vengono generati utilizzando la chiave **EventHubSendKey** . A ogni token viene assegnato un URI univoco.

```csharp
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

Quando si chiama questo metodo, l'URI deve essere specificato come `//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`. Per tutti i token l'URI è identico, ad eccezione di `PUBLISHER_NAME`, che deve essere diverso per ogni token. In teoria, `PUBLISHER_NAME` rappresenta l'ID del dispositivo che riceve il token.

Questo metodo genera un token con la struttura seguente:

```csharp
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

L'ora di scadenza del token è espressa in secondi dal 1 gennaio 1970. Di seguito è riportato un esempio di token:

```csharp
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

In genere, i token hanno una durata simile o superiore a quella del dispositivo. Se il dispositivo è in grado di ottenere un nuovo token, è possibile utilizzare token con una durata più breve.

### <a name="devices-sending-data"></a>Dispositivi che inviano dati
Dopo avere creato i token, viene eseguito il provisioning di ogni dispositivo con il proprio token univoco.

Quando il dispositivo invia dati a un Hub eventi, il dispositivo contrassegna il proprio token con la richiesta di invio. Per evitare che un utente malintenzionato intercetti e rubi il token, la comunicazione tra il dispositivo e l'Hub eventi deve verificarsi su un canale crittografato.

### <a name="blacklisting-devices"></a>Disattivazione dei dispositivi
In caso di furto di un token da parte di un utente malintenzionato, l'autore dell'attacco può rappresentare il dispositivo il cui token è stato rubato. La disattivazione di un dispositivo lo rende inutilizzabile fino a quando non riceve un nuovo token che utilizza un autore diverso.

## <a name="authentication-of-back-end-applications"></a>Autenticazione delle applicazioni back-end
Per autenticare le applicazioni back-end che utilizzano i dati generati dai dispositivi, Hub eventi usa un modello di sicurezza simile al modello usato per gli argomenti del bus di servizio. Un gruppo di consumer di Hub eventi equivale a una sottoscrizione a un argomento del bus di servizio. Un client può creare un gruppo di consumer, se la richiesta per creare il gruppo di consumer è accompagnata da un token che concede privilegi di gestione per l'Hub eventi o per lo spazio dei nomi a cui appartiene l'Hub di eventi. Un client può utilizzare dati di un gruppo di consumer se la richiesta di ricezione è accompagnata da un token che concede i diritti di ricezione in tale gruppo di consumer, l'Hub eventi o lo spazio dei nomi a cui appartiene l'Hub eventi.

La versione corrente del bus di servizio non supporta regole di firma di accesso condiviso per sottoscrizioni singole. Lo stesso vale per i gruppi di consumer di Hub eventi. In futuro verrà aggiunto il supporto SAS per entrambe le funzionalità.

In assenza di autenticazione SAS per gruppi di consumer singoli, è possibile utilizzare chiavi SAS per proteggere tutti i gruppi di consumer con una chiave comune. Questo approccio consente a un'applicazione di utilizzare dati di tutti i gruppi di consumer di un Hub eventi.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Hub eventi, vedere gli argomenti seguenti:

* [Panoramica di Hub eventi]
* [SAS overview] (Panoramica della firma di accesso condiviso)
* Un'[applicazione di esempio completa che usa Hub eventi]

[Panoramica di Hub eventi]: event-hubs-overview.md
[applicazione di esempio completa che usa Hub eventi]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[SAS overview]: ../service-bus-messaging/service-bus-sas-overview.md (Panoramica della firma di accesso condiviso)




<!--HONumber=Dec16_HO1-->


