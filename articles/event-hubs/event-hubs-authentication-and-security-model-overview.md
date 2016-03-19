<properties 
   pageTitle="Panoramica sul modello di autenticazione e di sicurezza di Hub di eventi | Microsoft Azure"
   description="Panoramica sul modello di autenticazione e sicurezza di Hub eventi."
   services="event-hubs"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/26/2016"
   ms.author="sethm" />

# Panoramica sull’autenticazione di Hub eventi e sul modello di protezione

Il modello di protezione di Hub eventi soddisfa i requisiti seguenti:

- Solo i dispositivi con credenziali valide possono inviare dati a un hub eventi.
- Un dispositivo non può rappresentare un altro dispositivo.
- A un dispositivo non autorizzato può essere impedito l'invio di dati a un hub eventi.

## Autenticazione del dispositivo

Il modello di protezione di Hub eventi si basa su una combinazione di token [Shared Access Signature (SAS)](../service-bus/service-bus-shared-access-signature-authentication.md) e autori di eventi. Un autore di eventi definisce un endpoint virtuale per un Hub eventi. L'autore si può usare solo per inviare messaggi a un hub eventi. Non è possibile ricevere messaggi da un autore.

In genere, un hub eventi usa un autore per ogni dispositivo. Tutti i messaggi inviati a uno qualsiasi degli autori di un hub eventi vengono accodati all'interno di tale hub eventi. Gli autori consentono la limitazione e il controllo dell’accesso con granularità fine.

A ogni dispositivo viene assegnato un token univoco, che viene caricato nel dispositivo. I token vengono prodotti in modo tale che ogni token univoco conceda l'accesso a un diverso autore univoco. Un dispositivo che possiede un token può inviare a un solo autore e a nessun altro autore. Se più dispositivi condividono lo stesso token, ognuno di tali dispositivi condivide un autore.

Sebbene non sia consigliato, è possibile dotare i dispositivi di token che concedono l'accesso diretto a un hub eventi. Qualsiasi dispositivo che contiene un token di questo tipo può inviare messaggi direttamente a tale hub eventi. Tale dispositivo non sarà soggetto alla limitazione. Non è possibile disattivare l'invio a tale hub eventi per il dispositivo.

Tutti i token sono firmati con una chiave SAS. In genere, tutti i token sono firmati con la stessa chiave. I dispositivi non conoscono la chiave, per cui non possono produrre token.

### Creare la chiave SAS

Quando si crea uno spazio dei nomi, Bus di servizio genera una chiave SAS a 256 bit denominata **RootManageSharedAccessKey**. Tale chiave concede diritti di invio, attesa e gestione allo spazio dei nomi. È possibile creare ulteriori chiavi. È consigliabile produrre una chiave che concede le autorizzazioni di invio all'hub eventi specifico. Nella parte restante di questo argomento, si presuppone che questa chiave sia denominata `EventHubSendKey`.

L'esempio seguente crea una chiave di solo invio durante la creazione dell'hub eventi:

```
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create Event hub with a SAS rule that allows sending to that Event hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### Generare token

È possibile generare token utilizzando la chiave SAS. È necessario ottenere solo un token per dispositivo. È possibile produrre token utilizzando il metodo riportato di seguito. Tutti i token vengono generati utilizzando la chiave **EventHubSendKey**. A ogni token viene assegnato un URI univoco.

```
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

Quando si chiama questo metodo, l'URI deve essere specificato come `//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`. Per tutti i token l'URI è identico, ad eccezione di `PUBLISHER_NAME`, che deve essere diverso per ogni token. In teoria, `PUBLISHER_NAME` rappresenta l'ID del dispositivo che riceve il token.

Questo metodo genera un token con la struttura seguente:

```
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

L'ora di scadenza del token è espressa in secondi dal 1 gennaio 1970. Di seguito è riportato un esempio di token:

```
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

In genere, i token hanno una durata simile o superiore a quella del dispositivo. Se il dispositivo è in grado di ottenere un nuovo token, è possibile utilizzare token con una durata più breve.

### Dispositivi che inviano dati

Dopo avere creato i token, viene eseguito il provisioning di ogni dispositivo con il proprio token univoco.

Quando il dispositivo invia dati a un hub eventi, il dispositivo contrassegna il proprio token con la richiesta di invio. Per evitare che un utente malintenzionato intercetti e rubi il token, la comunicazione tra il dispositivo e l'hub eventi deve verificarsi su un canale crittografato.

### Disattivazione dei dispositivi

In caso di furto di un token da parte di un utente malintenzionato, l'autore dell'attacco può rappresentare il dispositivo il cui token è stato rubato. La disattivazione di un dispositivo lo rende inutilizzabile fino a quando non riceve un nuovo token che utilizza un autore diverso.

## Autenticazione delle applicazioni back-end

Per autenticare le applicazioni back-end che utilizzano i dati generati dai dispositivi, Hub eventi utilizza un modello di protezione simile al modello utilizzato per gli argomenti di Bus di servizio. Un gruppo di consumer di Hub eventi è equivalente a una sottoscrizione a un argomento di Bus di servizio. Un client può creare un gruppo di consumer, se la richiesta per creare il gruppo di consumer è accompagnata da un token che concede privilegi di gestione per l'hub eventi o per lo spazio dei nomi a cui appartiene l'hub eventi. Un client può usare dati di un gruppo di consumer se la richiesta di ricezione è accompagnata da un token che concede i diritti di ricezione in tale gruppo di consumer, l'hub eventi o lo spazio dei nomi a cui appartiene l'hub eventi.

La versione corrente di Bus di servizio non supporta regole SAS per singole sottoscrizioni. Lo stesso vale per i gruppi di consumer di Hub eventi. In futuro verrà aggiunto il supporto SAS per entrambe le funzionalità.

In assenza di autenticazione SAS per gruppi di consumer singoli, è possibile utilizzare chiavi SAS per proteggere tutti i gruppi di consumer con una chiave comune. Questo approccio consente a un'applicazione di utilizzare dati di tutti i gruppi di consumer di un Hub eventi.

### Creazione di identità del servizio, relying party e regole in ACS

ACS supporta diversi modi per creare identità del servizio, relying party e regole, ma il modo più semplice consiste nell'utilizzare il [SBAZTool](http://code.msdn.microsoft.com/Authorization-SBAzTool-6fd76d93). Ad esempio:

1. Creare un'identità del servizio per un **EventHubSender**. Restituisce il nome dell'identità del servizio creato e la relativa chiave:

	```
	sbaztool.exe exe -n <namespace> -k <key>  makeid eventhubsender
	```

2. Concedere a **EventHubSender** l’invio di attestazioni per l'Hub eventi:

	```
	sbaztool.exe -n <namespace> -k <key> grant Send /AuthTestEventHub eventhubsender
	```

3. Creare un'identità del servizio per un destinatario del gruppo di consumer 1:

	```
	sbaztool.exe exe -n <namespace> -k <key> makeid consumergroup1receiver
	```

4. Concedere a `consumergroup1receiver` l’attesa di attestazioni per **ConsumerGroup1**:

	```
	sbaztool.exe -n <namespace> -k <key> grant Listen /AuthTestEventHub/ConsumerGroup1 consumergroup1receiver
	```

5. Creare un'identità del servizio per un destinatario per il **gruppo di consumer 2**:

	```
	sbaztool.exe exe -n <namespace> -k <key>  makeid consumergroup2receiver
	```

6. Concedere a `consumergroup2receiver` l’attesa di attestazioni a **ConsumerGroup2**:

	```
	sbaztool.exe -n <namespace> -k <key> grant Listen /AuthTestEventHub/ConsumerGroup2 consumergroup2receiver
	```

## Passaggi successivi

Per ulteriori informazioni su Hub eventi, visitare i seguenti argomenti:

- [Panoramica di Hub eventi]
- Un'[applicazione di esempio completa che usa Hub eventi].
- Una [soluzione di messaggistica accodata] che usa le code di Bus di servizio.

[Panoramica di Hub eventi]: event-hubs-overview.md
[applicazione di esempio completa che usa Hub eventi]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[soluzione di messaggistica accodata]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=AcomDC_0204_2016-->