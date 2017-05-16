---
title: Informazioni sulla messaggistica hub IoT di Azure | Microsoft Docs
description: Guida per gli sviluppatori - Messaggistica da dispositivo a cloud e da cloud a dispositivo con l&quot;hub IoT. Include informazioni sui formati dei messaggi e sui protocolli di comunicazione supportati.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3fc5f1a3-3711-4611-9897-d4db079b4250
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: ac3f4d2220c1902f00049ce237468ddee992209d
ms.contentlocale: it-it
ms.lasthandoff: 04/03/2017


---
# <a name="send-and-receive-messages-with-iot-hub"></a>Inviare e ricevere messaggi con l'hub IoT
## <a name="overview"></a>Panoramica
L'hub IoT fornisce le primitive di messaggistica riportate di seguito per comunicare con un dispositivo:

* [Dispositivo a cloud][lnk-d2c]: da un dispositivo a un'app back-end.
* [Cloud a dispositivo][lnk-c2d]: da un'app di back-end (*servizio* o *cloud*).

Le proprietà di base della funzionalità di messaggistica dell'hub IoT sono l'affidabilità e la durabilità dei messaggi. Queste proprietà consentono la resilienza in caso di connettività intermittente sul lato dispositivo e picchi di carico durante l'elaborazione degli eventi sul lato cloud. L'hub IoT implementa *almeno una volta* le garanzie di recapito per la messaggistica da dispositivo a cloud e da cloud a dispositivo.

L'hub IoT supporta più [protocolli per il dispositivo][lnk-protocols], ad esempio MQTT, AMQP e HTTP. Per supportare l'interoperabilità senza problemi tra i protocolli, l'hub IoT definisce un [formato di messaggio comune][lnk-message-format] supportato da tutti i protocolli per il dispositivo.

L'hub IoT espone un [endpoint compatibile con Hub eventi][lnk-compatible-endpoint] predefinito per consentire alle app back-end di leggere i messaggi da dispositivo a cloud ricevuti dall'hub. È anche possibile creare endpoint personalizzati nell'hub IoT collegando all'hub gli altri servizi nella sottoscrizione.

### <a name="when-to-use"></a>Quando usare le autorizzazioni
Usare i messaggi da dispositivo a cloud per inviare la telemetria relativa alle serie temporali e gli avvisi dall'app del dispositivo e i messaggi da cloud a dispositivo per notifiche unidirezionali all'app del dispositivo.

Vedere [Device-to-cloud communication guidance][lnk-d2c-guidance] (Indicazioni sulla comunicazione da dispositivo a cloud) in caso di dubbi tra l'uso delle proprietà indicate, dei messaggi da dispositivo a cloud o del caricamento di file.
Vedere [Cloud-to-device communication guidance][lnk-c2d-guidance] (Indicazioni sulla comunicazione da cloud a dispositivo) in caso di dubbi tra l'uso delle proprietà specifiche, dei metodi diretti o dei messaggi da cloud a dispositivo.

Per un confronto tra i servizi dell'hub IoT e di Hub eventi, vedere [Confronto tra l'hub IoT e Hub eventi][lnk-compare].

## <a name="device-to-cloud-messages"></a>Messaggi da dispositivo a cloud
I messaggi da dispositivo a cloud vengono inviati tramite un endpoint per il dispositivo (**/devices/{deviceId}/messages/events**). A quel punto le regole di routing reindirizzano i messaggi a uno degli endpoint di servizio nell'hub IoT. Le regole di routing usano le proprietà dei messaggi da dispositivo a cloud che passano nell'hub per determinare dove reindirizzarli. Per impostazione predefinita, i messaggi vengono reindirizzati all'endpoint per servizio predefinito (messages/events) compatibile con [Hub eventi][lnk-event-hubs]. È quindi possibile usare l'[integrazione standard di Hub eventi e gli SDK][lnk-compatible-endpoint] per ricevere i messaggi da dispositivo a cloud.

L'hub IoT implementa la messaggistica da dispositivo a cloud usando un modello di messaggistica di flusso. I messaggi da dispositivo a cloud dell'hub IoT somigliano più a *eventi* di [Hub eventi][lnk-event-hubs] che non a *messaggi* del [bus di servizio][lnk-servicebus], poiché è presente un volume elevato di eventi che passa nel servizio ed è leggibile da più lettori.

Questa implementazione presenta le implicazioni seguenti:

* Analogamente agli eventi di Hub eventi, i messaggi da dispositivo a cloud sono durevoli e vengono mantenuti nell'endpoint **messages/events** predefinito in un hub IoT per un massimo di sette giorni.
* Analogamente agli eventi di Hub eventi, i messaggi da dispositivo a cloud possono avere dimensioni massime pari a 256 KB e possono essere raggruppati in batch per ottimizzare gli invii. I batch possono avere dimensioni massime pari a 256 KB.

Esistono tuttavia alcune differenze importanti tra i messaggi da dispositivo a cloud dell'hub IoT e Hub eventi:

* Come illustrato nella sezione [Controllare l'accesso all'hub IoT][lnk-devguide-security], l'hub IoT consente il controllo di accesso e l'autenticazione per singoli dispositivi.
* L'hub IoT consente di creare fino a 10 endpoint personalizzati. I messaggi vengono recapitati agli endpoint in base alle route configurate nell'hub IoT.
* L'hub IoT consente milioni di dispositivi connessi simultaneamente (vedere [Quote e limitazioni][lnk-quotas]), mentre gli hub eventi sono limitati a 5000 connessioni AMQP per ogni spazio dei nomi.
* L'hub IoT non consente il partizionamento arbitrario tramite **PartitionKey**. I messaggi da dispositivo a cloud vengono partizionati in base al valore **deviceId**di origine.
* Il ridimensionamento dell'hub IoT è leggermente diverso rispetto al ridimensionamento dell'hub eventi. Per altre informazioni, vedere [Ridimensionamento dell'hub IoT][lnk-guidance-scale].

Per informazioni dettagliate sull'uso della messaggistica da dispositivo a cloud, vedere [Azure IoT SDKs][lnk-sdks] (SDK di IoT di Azure).

Per informazioni dettagliate su come configurare il routing dei messaggi, vedere [Regole di routing](#routing-rules).

> [!NOTE]
> Quando si usa HTTP per inviare messaggi da dispositivo a cloud, i valori e i nomi di proprietà possono contenere solo caratteri ASCII alfanumerici più ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.
> 
> 

### <a name="non-telemetry-traffic"></a>Traffico non di telemetria
Spesso, oltre ai punti dati di telemetria, i dispositivi inviano messaggi e richieste che devono essere eseguiti e gestiti separatamente dal livello della logica di business dell'applicazione. Ad esempio, gli avvisi critici che devono attivare un'azione specifica nel back-end. È possibile scrivere facilmente una regola di routing per l'invio di questi tipi di messaggi a un endpoint dedicato alla loro elaborazione.

Per altre informazioni sul modo migliore di elaborare questo tipo di messaggio, vedere l'[Esercitazione: Elaborare messaggi da dispositivo a cloud dell'hub IoT usando .Net][lnk-d2c-tutorial].

### <a name="routing-rules"></a>Regole di routing

L'hub IoT consente di eseguire il routing dei messaggi ai propri endpoint in base alle proprietà dei messaggi stessi. Le regole di routing offrono la flessibilità necessaria per inviare messaggi alle destinazioni desiderate, senza dover attivare altri servizi per elaborare i messaggi o scrivere codice aggiuntivo. Ogni regola di routing configurata ha le proprietà seguenti:

* **Nome**. Il nome univoco che identifica la regola.
* **Source**. L'origine del flusso dati su cui intervenire. Ad esempio, i dati di telemetria del dispositivo.
* **Condition**. L'espressione di query per la regola di routing che viene eseguita rispetto alle proprietà del messaggio e usata per determinare se si tratti di una corrispondenza per l'endpoint. Per altre informazioni sulla creazione di una condizione di route, vedere il [Riferimento: linguaggio query per dispositivi gemelli e processi][lnk-devguide-query-language].
* **Endpoint**. Il nome dell'endpoint dove l'hub IoT invia i messaggi corrispondenti alla condizione. Gli endpoint devono trovarsi nella stessa area dell'hub IoT, in caso contrario potrebbero essere addebitati dei costi per le scritture tra aree.

Un singolo messaggio può corrispondere alla condizione su più regole di routing. In questo caso, l'hub IoT invia il messaggio all'endpoint associato a ciascuna regola corrispondente. L'hub IoT deduplica automaticamente la consegna dei messaggi, quindi se uno di questi corrisponde a più regole che hanno tutte la stessa destinazione, verrà scritto in quella destinazione una sola volta.

Per altre informazioni sulla creazione di endpoint personalizzati nell'hub IoT, vedere [Endpoint dell'hub IoT][lnk-devguide-endpoints].

### <a name="built-in-endpoint-messagesevents"></a>Endpoint predefinito: messages/events

Un hub IoT espone le proprietà seguenti per consentire il controllo dell'endpoint di messaggistica predefinito **messages/events**, compatibile con Hub eventi.

* **Conteggio partizioni**. Impostare questa proprietà in fase di creazione per definire il numero di [partizioni][lnk-event-hub-partitions] per l'inserimento di eventi da dispositivo a cloud.
* **Periodo di memorizzazione**. Questa proprietà specifica per quanti giorni i messaggi vengono conservati dall'hub IoT. Il valore predefinito è un giorno, ma può essere aumentato a sette giorni.

L'hub IoT consente inoltre di gestire i gruppi di consumer nell'endpoint di ricezione predefinito da dispositivo a cloud.

Per impostazione predefinita, tutti i messaggi che non corrispondono in modo esplicito a una regola di routing vengono scritti nell'endpoint predefinito. Se si disattiva questa route di fallback, i messaggi che non corrispondono in modo esplicito a una regola di routing verranno eliminati.

È possibile modificare il tempo di conservazione a livello di codice con le [API del provider di risorse dell'hub IoT di Azure][lnk-resource-provider-apis] oppure usando il [portale di Azure][lnk-management-portal].

### <a name="anti-spoofing-properties"></a>Proprietà anti-spoofing
Per evitare lo spoofing di dispositivi nei messaggi da dispositivo a cloud, l'hub IoT contrassegna tutti i messaggi con le proprietà seguenti:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

Le prime due contengono le proprietà **deviceId** e **generationId** del dispositivo di origine, come indicato in [Proprietà delle identità dei dispositivi][lnk-device-properties].

La proprietà **ConnectionAuthMethod** contiene un oggetto serializzato JSON con le proprietà seguenti:

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="cloud-to-device-messages"></a>Messaggi da cloud a dispositivo
È possibile inviare messaggi da cloud a dispositivo tramite un endpoint per il servizio (**/messages/servicebound**). Il dispositivo li ricevere tramite un endpoint per il dispositivo (**/devices/{deviceId}/messages/devicebound**).

Ogni messaggio da cloud a dispositivo è destinato a un singolo dispositivo, tramite la proprietà **to** impostata su **/devices/{deviceId}/messages/devicebound**.

> [!IMPORTANT]
> Ogni coda di dispositivo può contenere un massimo di 50 messaggi da cloud a dispositivo. Se si prova a inviare più messaggi allo stesso dispositivo, si verificherà un errore.
> 
> [!NOTE]
> Quando si inviano messaggi da cloud a dispositivo, i valori e i nomi di proprietà possono contenere solo caratteri ASCII alfanumerici e ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.
> 
> 

### <a name="message-lifecycle"></a>Ciclo di vita del messaggio
Per garantire il recapito di almeno un messaggio, l'hub IoT salva in modo permanente i messaggi da cloud a dispositivo nelle code dei singoli dispositivi. I dispositivi devono confermare esplicitamente il *completamento* per consentire all'hub IoT di rimuoverli dalla coda. Ciò garantisce la resilienza rispetto a errori di connettività e dei dispositivi.

Il diagramma seguente illustra il grafico sullo stato del ciclo di vita per un messaggio da cloud a dispositivo.

![Ciclo di vita dei messaggi da cloud a dispositivo][img-lifecycle]

Quando il servizio invia un messaggio, questo viene considerato *Accodato*. Quando un dispositivo accetta di *ricevere* un messaggio, l'hub IoT *blocca* il messaggio, impostandone lo stato su **Invisibile**, per consentire ad altri thread nello stesso dispositivo di iniziare a ricevere altri messaggi. Quando un thread del dispositivo termina l'elaborazione di un messaggio, invia una notifica all'hub IoT *completando* il messaggio.

Un dispositivo può anche:

* *Rifiutare* il messaggio. In questo caso, l'hub IoT ne imposta lo stato su **Non recapitabile**. Nota: i dispositivi che si connettono a MQTT non possono rifiutare i messaggi da cloud a dispositivo.
* *Abbandonare* il messaggio. In questo caso, l'hub IoT inserisce di nuovo il messaggio nella coda con lo stato impostato su **Accodato**.

Un thread potrebbe non riuscire a elaborare un messaggio senza inviare una notifica all'hub IoT. In questo caso i messaggi passano automaticamente dallo stato **Invisibile** allo stato **Accodato** dopo un *timeout di visibilità o di blocco*. Il valore predefinito di questo timeout è un minuto.

Un messaggio può passare dallo stato **Accodato** allo stato **Invisibile** e viceversa per il numero massimo di volte specificato nella proprietà **maxDeliveryCount** dell'hub IoT. Dopo il numero di transizioni specificato, l'hub IoT imposta lo stato del messaggio su **Non recapitabile**. Analogamente, l'hub IoT imposta lo stato di un messaggio su **Non recapitabile** dopo la relativa scadenza. Vedere la sezione [Scadenza del messaggio (durata)][lnk-ttl].

Per un'esercitazione sui messaggi da cloud a dispositivo, vedere l'[Esercitazione: Inviare messaggi da cloud a dispositivo con l'hub IoT e .NET][lnk-c2d-tutorial]. Per argomenti di riferimento sul modo in cui le diverse API e i vari SDK espongono la funzionalità da cloud a dispositivo, vedere [Azure IoT SDKs][lnk-sdks] (SDK di IoT di Azure).

> [!NOTE]
> I messaggi da cloud a dispositivo vengono in genere completati quando la perdita del messaggio non influisce sulla logica dell'applicazione. Ad esempio, il contenuto del messaggio è stato salvato in modo permanente nell'archivio locale o un'operazione è stata eseguita correttamente. Il messaggio potrebbe anche includere informazioni temporanee la cui perdita non influirebbe sulla funzionalità dell'applicazione. In alcuni casi, per le attività con esecuzione prolungata, è possibile completare il messaggio da cloud a dispositivo dopo aver salvato in modo permanente la descrizione dell'attività nell'archivio locale. È quindi possibile inviare al back-end della soluzione una notifica con uno o più messaggi da dispositivo a cloud in diverse fasi di avanzamento dell'attività.
> 
> 

### <a name="message-expiration-time-to-live"></a>Scadenza del messaggio (durata)
Ogni messaggio da cloud a dispositivo ha una scadenza. Questa durata viene impostata dal servizio, nella proprietà **ExpiryTimeUtc**, oppure dall'hub IoT, usando il valore di *durata (TTL)* predefinito specificato come proprietà dell'hub IoT. Vedere [Opzioni di configurazione da cloud a dispositivo][lnk-c2d-configuration].

> [!NOTE]
> Un modo comune per usare la scadenza del messaggio a proprio vantaggio ed evitare l'invio di messaggi a dispositivi disconnessi consiste nell'impostare valori di durata brevi. Questo approccio permette di ottenere lo stesso risultato che si ottiene mantenendo connesso il dispositivo, ma è più efficiente. Se si richiede l'acknowledgement dei messaggi, l'hub IoT invia una notifica per indicare quali dispositivi possono ricevere messaggi e quali sono offline o in stato di errore.
> 
> 

### <a name="message-feedback"></a>Commenti sui messaggi
Durante l'invio di messaggi da cloud a dispositivo, il servizio può richiedere il recapito di commenti specifici per ogni messaggio in merito allo stato finale del messaggio.

* Se si imposta la proprietà **Ack** su **positive**, l'hub IoT genera un messaggio con commenti solo se il messaggio da cloud a dispositivo ha raggiunto lo stato **Completato**.
* Se si imposta la proprietà **Ack** su **negative**, l'hub IoT genera un messaggio con commenti solo se il messaggio da cloud a dispositivo raggiunge lo stato **Non recapitabile**.
* Se si imposta la proprietà **Ack** su **full**, l'hub IoT genera un messaggio con commenti in entrambi i casi.

> [!NOTE]
> Se la proprietà **Ack** è impostata su **full** e non si riceve un messaggio con commenti, significa che il messaggio è scaduto. Il servizio non può sapere cosa è successo al messaggio originale. In pratica, un servizio deve garantire che sia possibile elaborare i commenti prima della scadenza. Il periodo di scadenza massimo è di due giorni ed è quindi disponibile un tempo sufficiente per ripristinare il servizio in caso di errore.
> 
> 

Come illustrato nella sezione [Endpoint][lnk-endpoints], l'hub IoT recapita commenti sotto forma di messaggi tramite un endpoint per servizio (**/messages/servicebound/feedback**). La semantica di ricezione per i commenti è uguale a quella dei messaggi da cloud a dispositivo e ha lo stesso [ciclo di vita dei messaggi][lnk-lifecycle]. Quando è possibile, i commenti sui messaggio vengono riuniti in batch in un unico messaggio con il formato seguente:

| Proprietà | Descrizione |
| --- | --- |
| EnqueuedTime |Timestamp che indica quando è stato creato il messaggio. |
| UserId |`{iot hub name}` |
| ContentType |`application/vnd.microsoft.iothub.feedback.json` |

Il corpo è una matrice serializzata con JSON dei record, ognuno con le proprietà seguenti:

| Proprietà | Descrizione |
| --- | --- |
| EnqueuedTimeUtc |Timestamp che indica quando è stato creato il risultato del messaggio. Ad esempio, il dispositivo ha completato l'operazione o il messaggio è scaduto. |
| OriginalMessageId |**MessageId** del messaggio da cloud a dispositivo correlato a queste informazioni sui commenti. |
| StatusCode |Stringa obbligatoria. Usato nei messaggi con commenti generati dall'hub IoT. <br/> 'Operazione riuscita' <br/> 'Scaduto' <br/> 'Numero di distribuzioni superato' <br/> 'Rifiutato' <br/> 'Eliminato definitivamente' |
| Descrizione |Valori stringa per **StatusCode**. |
| deviceId |**DeviceId** del messaggio da cloud a dispositivo correlato a queste informazioni sui commenti. |
| DeviceGenerationId |**DeviceGenerationId** del dispositivo di destinazione del messaggio da cloud a dispositivo correlato a queste informazioni sui commenti. |

> [!IMPORTANT]
> Il servizio deve specificare un valore **MessageId** per il messaggio da cloud a dispositivo per poter correlare i commenti al messaggio originale.
> 
> 

L'esempio seguente illustra il corpo di un messaggio con commenti.

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

### <a name="cloud-to-device-configuration-options"></a>Opzioni di configurazione da cloud a dispositivo
Ogni hub IoT espone le opzioni di configurazione seguenti per la messaggistica da cloud a dispositivo.

| Proprietà | Descrizione | Intervallo e valore predefinito |
| --- | --- | --- |
| defaultTtlAsIso8601 |Durata (TTL) predefinita per messaggi da cloud a dispositivo. |Intervallo ISO_8601 fino a 2D (almeno 1 minuto). Predefinito: 1 ora. |
| maxDeliveryCount |Numero massimo di recapiti per code da cloud a dispositivo per i singoli dispositivi. |Da 1 a 100. Predefinito: 10. |
| feedback.ttlAsIso8601 |Conservazione per messaggi con commenti diretti al servizio. |Intervallo ISO_8601 fino a 2D (almeno 1 minuto). Predefinito: 1 ora. |
| feedback.maxDeliveryCount |Numero massimo di recapiti per la coda di commenti. |Da 1 a 100. Predefinito: 100. |

Per altre informazioni, vedere [Create IoT hubs][lnk-portal] (Creare hub IoT).

## <a name="read-device-to-cloud-messages"></a>Leggere i messaggi da dispositivo a cloud
L'hub IoT espone l'endpoint **messaggi/eventi** predefinito per permettere ai servizi back-end di leggere i messaggi da dispositivo a cloud ricevuti dall'hub. L'endpoint è compatibile con Hub eventi e consente quindi di usare uno dei meccanismi supportati da tale servizio per la lettura dei messaggi.

È inoltre possibile creare endpoint personalizzati nell'hub IoT. L'hub IoT supporta attualmente l'Hub eventi, le code e gli argomenti del bus di servizio come endpoint personalizzati. Per altre informazioni sulla lettura da tali servizi, vedere: lettura dall'[Hub eventi][lnk-getstarted-eh], lettura dalle [code del bus di servizio][lnk-getstarted-queue] e lettura dagli [argomenti del bus di servizio][lnk-getstarted-topic].

### <a name="reading-from-the-built-in-endpoint"></a>Lettura dall'endpoint predefinito

Quando si usa [Azure Service Bus SDK per .NET][lnk-servicebus-sdk] o [Hub eventi - Host processore di eventi][lnk-eventprocessorhost], è possibile usare qualsiasi stringa di connessione dell'hub IoT con le autorizzazioni corrette. Usare quindi **messaggi/eventi** come nome dell'hub eventi.

Quando si usano gli SDK o integrazioni del prodotto non compatibili con l'hub IoT, è necessario recuperare un endpoint e un nome compatibili con Hub eventi dalle impostazioni dell'hub IoT nel [portale di Azure][lnk-management-portal]:

1. Nel pannello dell'hub IoT fare clic su **Endpoint**.
2. Nella sezione **Endpoint predefiniti** fare clic su **Eventi**. Il pannello contiene i valori seguenti: **Endpoint compatibile con l'hub eventi**, **Nome compatibile con l'hub eventi**, **Partizioni**, **Tempo di conservazione** e **Gruppi di consumer**.
   
    ![Impostazioni da dispositivo a cloud][img-eventhubcompatible]

> [!NOTE]
> L'SDK dell'hub IoT richiede il nome dell'endpoint dell'hub IoT, ossia **messages/events** come mostrato nel pannello **Endpoint**.
>
>

> [!NOTE]
> Se l'SDK usato richiede un valore **Nome host** o **Spazio dei nomi**, rimuovere lo schema da **Endpoint compatibile con l'hub eventi**. Ad esempio, se l'endpoint compatibile con l'hub eventi è **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, il **Nome host** sarà **iothub-ns-myiothub-1234.servicebus.windows.net** e lo **Spazio dei nomi** sarà **iothub-ns-myiothub-1234**.
> 
>

È quindi possibile usare qualsiasi criterio di accesso condiviso con autorizzazioni **ServiceConnect** per connettersi all'hub eventi specificato.

Se si deve compilare una stringa di connessione dell'hub eventi con le informazioni precedenti, è possibile usare il modello seguente:

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

Di seguito è riportato un elenco di SDK e integrazioni che è possibile usare con gli endpoint compatibili con l'hub eventi esposti dall'hub IoT:

* [Client degli hub eventi Java.](https://github.com/hdinsight/eventhubs-client)
* [Spout di Apache Storm](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). È possibile visualizzare il [codice sorgente dello spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) su GitHub.
* [Integrazione con Apache Spark.](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md)

## <a name="reference-topics"></a>Argomenti di riferimento:
Negli argomenti di riferimento seguenti vengono offerte altre informazioni sullo scambio di messaggi con l'hub IoT.

## <a name="message-format"></a>Formato dei messaggi
I messaggi dell'hub IoT sono costituiti da questi elementi:

* Un set di *proprietà del sistema*. Proprietà impostate o interpretate dall'hub IoT. Il set è predeterminato.
* Un set di *proprietà dell'applicazione*. Dizionario di proprietà stringa che l'applicazione può definire e a cui può accedere senza dover deserializzare il corpo del messaggio. Queste proprietà non vengono mai modificate dall'hub IoT.
* Corpo binario opaco.

Per altre informazioni sulla codifica del messaggio nei diversi protocolli, vedere [Azure IoT SDKs][lnk-sdks] (SDK di IoT di Azure).

La tabella seguente riporta il set delle proprietà di sistema nei messaggi dell'hub IoT.

| Proprietà | Descrizione |
| --- | --- |
| MessageId |Identificatore configurabile dall'utente per il messaggio, usato per modelli di richiesta-risposta. Formato: stringa con distinzione tra maiuscole e minuscole (con lunghezza massima di 128 caratteri) di caratteri alfanumerici ASCII a 7 bit + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Numero di sequenza |Numero, univoco per ogni dispositivo-coda, assegnato dall'hub IoT a ogni messaggio da cloud a dispositivo. |
| To |Destinazione specificata nei messaggi [da cloud a dispositivo][lnk-c2d]. |
| ExpiryTimeUtc |Data e ora della scadenza del messaggio. |
| EnqueuedTime |Date e ora del messaggio [da cloud a dispositivo][lnk-c2d] ricevute dall'hub IoT. |
| CorrelationId |Proprietà stringa in un messaggio di risposta che contiene in genere il valore MessageId della richiesta nei modelli richiesta-risposta. |
| UserId |ID usato per specificare l'origine dei messaggi. Quando i messaggi vengono generati dall'hub IoT, viene impostata su `{iot hub name}`. |
| Ack |Generatore di messaggi con commenti. Questa proprietà viene usata nei messaggi da cloud a dispositivo per richiedere all'hub IoT di generare messaggi con commenti come risultato dell'utilizzo del messaggio da parte del dispositivo. I valori possibili sono i seguenti: **none** (predefinito): non viene generato alcun messaggio con commenti, **positive**: si riceve un messaggio con commenti se il messaggio è stato completato, **negative**: si riceve un messaggio con commenti se il messaggio è scaduto o se è stato raggiunto il numero massimo di recapiti senza il completamento da parte del dispositivo, **full**: sia per esito positivo che negativo. Per altre informazioni, vedere [Commenti sui messaggi][lnk-feedback]. |
| ConnectionDeviceId |ID impostato dall'hub IoT sui messaggi da dispositivo a cloud. Contiene il valore **deviceId** del dispositivo che ha inviato il messaggio. |
| ConnectionDeviceGenerationId |ID impostato dall'hub IoT sui messaggi da dispositivo a cloud. Contiene il valore **generationId** (come indicato in [Proprietà delle identità dei dispositivi][lnk-device-properties]) del dispositivo che ha inviato il messaggio. |
| ConnectionAuthMethod |Metodo di autenticazione impostato dall'hub IoT sui messaggi da dispositivo a cloud. Questa proprietà contiene informazioni sul metodo di autenticazione usato per autenticare il dispositivo che invia il messaggio. Per altre informazioni, vedere la sezione sull'[anti-spoofing da dispositivo a cloud][lnk-antispoofing]. |

## <a name="message-size"></a>Dimensioni dei messaggi

L'hub IoT misura le dimensioni dei messaggi in modo indipendente dal protocollo, prendendo in considerazione solo il payload effettivo. Le dimensioni in byte vengono calcolate come somma degli elementi seguenti:

* Dimensioni del corpo in byte.
* Dimensioni in byte di tutti i valori delle proprietà di sistema dei messaggi.
* Dimensioni in byte di tutti i nomi e i valori delle proprietà dell'utente.

Si noti che i nomi e i valori delle proprietà sono limitati ai caratteri ASCII, in modo che la lunghezza delle stringhe sia uguale alle dimensioni in byte.

## <a name="communication-protocols"></a>Protocolli di comunicazione
L'hub IoT consente ai dispositivi di usare protocolli [MQTT][lnk-mqtt], MQTT su WebSockets, [AMQP][lnk-amqp], AMQP su WebSockets e HTTP per le comunicazioni lato dispositivo. Nella tabella seguente vengono fornite le indicazioni generali per la scelta del protocollo:

| Protocol | Quando scegliere questo protocollo |
| --- | --- |
| MQTT <br> MQTT su WebSocket |Viene usato su tutti i dispositivi che non richiedono la connessione di più dispositivi, ognuno con le sue credenziali per dispositivo, sulla stessa connessione TLS. |
| AMQP <br> AMQP su WebSocket |Usare in gateway cloud e sul campo per sfruttare il vantaggio della connessione multiplexing tra dispositivi. |
| HTTP |Usare per i dispositivi che non supportano altri protocolli. |

Nella scelta del protocollo per le comunicazioni sul lato dispositivo occorre prendere in considerazione quanto segue:

* **Modello da cloud a dispositivo**. HTTP non offre un modo efficiente per implementare il push del server. Di conseguenza, quando si usa HTTP i dispositivi eseguono il polling dell'hub IoT per i messaggi da cloud a dispositivo. Questo approccio è inefficiente sia per il dispositivo che per l'hub IoT. In base alle attuali linee guida di HTTP, ogni dispositivo dovrebbe eseguire il polling almeno ogni 25 minuti. D'altra parte, MQTT e AMQP supportano il push del server quando si ricevono messaggi da cloud a dispositivo, consentendo il push immediato dei messaggi dall'hub IoT al dispositivo. Se la latenza di recapito rappresenta un problema, è consigliabile usare il protocollo MQTT o AMQP. Per i dispositivi che si connettono raramente, è possibile usare anche il protocollo HTTP.
* **Gateway sul campo**. Quando si usano i protocolli MQTT e HTTP, non è possibile connettere più dispositivi, ognuno con le sue credenziali per dispositivo, con la stessa connessione TLS. Di conseguenza, questi protocolli non sono ottimali per [scenari di gateway sul campo][lnk-azure-gateway-guidance] perché richiedono una connessione TLS tra il gateway sul campo e l'hub IoT per ogni dispositivo connesso al gateway sul campo.
* **Dispositivi con risorse ridotte**. Le raccolte di MQTT e HTTP hanno un'impronta inferiore rispetto alle raccolte di AMQP. Di conseguenza, se il dispositivo ha risorse limitate, ad esempio meno di 1 MB di RAM, questi protocolli potrebbero costituire l'unica implementazione disponibile.
* **Attraversamento rete**. Lo standard AMQP è in ascolto sulla porta 5671, mentre MQTT è in ascolto sulla porta 8883. Questo può provocare problemi nelle reti chiuse ai protocolli non HTTP. MQTT su WebSocket, AMQP su WebSocket e HTTP sono entrambi disponibili per l'uso in questo scenario.
* **Dimensioni del payload**. MQTT e AMQP sono protocolli binari, quindi hanno payload più compatti rispetto a HTTP.

> [!NOTE]
> Quando si usa HTTP, ogni dispositivo deve eseguire il polling dei messaggi da cloud a dispositivo ogni 25 minuti o più. In fase di sviluppo è comunque accettabile eseguire il polling con una frequenza maggiore di 25 minuti.
> 
> 

## <a name="port-numbers"></a>Numeri di porta
I dispositivi possono comunicare con l'hub IoT in Azure tramite una serie di protocolli. In genere, la scelta del protocollo è determinata dai requisiti specifici della soluzione. Nella tabella seguente sono elencate le porte in uscita che devono essere aperte affinché un dispositivo possa utilizzare un protocollo specifico:

| Protocol | Porta |
| --- | --- |
| MQTT |8883 |
| MQTT su WebSocket |443 |
| AMQP |5671 |
| AMQP su WebSockets |443 |
| HTTP |443 |
| LWM2M (gestione dei dispositivi) |5684 |

Dopo aver creato un hub IoT in un'area di Azure, l'hub IoT manterrà lo stesso indirizzo IP per la durata di tale hub IoT. Per mantenere tuttavia la qualità del servizio, se Microsoft sposta l'hub IoT su un'unità di scala diversa, verrà assegnato ad esso un nuovo indirizzo IP.

## <a name="notes-on-mqtt-support"></a>Note sul supporto di MQTT
L'hub IoT implementa il protocollo MQTT v3.1.1 con le limitazioni e il comportamento specifico seguenti:

* **QoS 2 non è supportato**. Quando un'app del dispositivo pubblica un messaggio con **QoS 2**, l'hub IoT chiude la connessione di rete. Quando un'app del dispositivo esegue una sottoscrizione a un argomento con **QoS 2**, l'hub IoT concede il livello QoS 1 massimo nel pacchetto **SUBACK**.
* **I messaggi di mantenimento non vengono resi persistenti**. Se un'app del dispositivo pubblica un messaggio con il flag RETAIN impostato su 1, l'hub IoT aggiunge al messaggio la proprietà dell'applicazione **x-opt-retain** a messaggio. In tal caso, l'hub IoT non rende persistente il messaggio di mantenimento, ma lo passa invece all'app back-end.

Per altre informazioni, vedere [Supporto di MQTT nell'hub IoT][lnk-devguide-mqtt].

È infine consigliabile rivedere la sezione [Gateway del protocollo IoT Azure][lnk-azure-protocol-gateway], che descrive come sia possibile distribuire un gateway del protocollo personalizzato a prestazioni elevate che si interfaccia direttamente con l'hub IoT. Il gateway del protocollo IoT Azure consente di personalizzare il protocollo del dispositivo per supportare le distribuzioni di MQTT cosiddette "brownfield" o altri protocolli personalizzati. Questo approccio richiede tuttavia l'esecuzione e la gestione di un gateway di protocollo personalizzato.

## <a name="additional-reference-material"></a>Materiale di riferimento
Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per gli sviluppatori dell'hub IoT:

* [Endpoint dell'hub IoT][lnk-endpoints] illustra i diversi endpoint esposti da ogni hub IoT per operazioni della fase di esecuzione e di gestione.
* [Quote e limitazioni][lnk-quotas] descrive le quote applicabili al servizio Hub IoT e il comportamento di limitazione previsto quando si usa il servizio.
* [Azure IoT SDK per dispositivi e servizi][lnk-sdks] elenca gli SDK nei diversi linguaggi che è possibile usare quando si sviluppano app per dispositivi e servizi che interagiscono con l'hub IoT.
* [Linguaggio di query per dispositivi gemelli e processi][lnk-query] illustra il linguaggio di query dell'hub IoT che è possibile usare per recuperare informazioni dall'hub IoT sui dispositivi gemelli e sui processi.
* [Supporto di MQTT nell'hub IoT][lnk-devguide-mqtt] offre altre informazioni sul supporto dell'hub IoT per il protocollo MQTT.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver appreso come inviare e ricevere messaggi con l'hub IoT, è possibile vedere altri argomenti di interesse reperibili nella Guida per sviluppatori dell'hub IoT:

* [Caricare file da un dispositivo][lnk-devguide-upload]
* [Gestire le identità dei dispositivi nell'hub IoT][lnk-devguide-identities]
* [Controllare l'accesso all'hub IoT][lnk-devguide-security]
* [Use device twins to synchronize state and configurations][lnk-devguide-device-twins] (Usare dispositivi gemelli per sincronizzare lo stato e le configurazioni)
* [Richiamare un metodo diretto in un dispositivo][lnk-devguide-directmethods]
* [Pianificare processi in più dispositivi][lnk-devguide-jobs]

Per provare alcuni dei concetti descritti in questo articolo, possono essere utili le esercitazioni di hub IoT seguenti:

* [Introduzione all'hub IoT di Azure][lnk-getstarted-tutorial]
* [Inviare messaggi da cloud a dispositivo con l'hub IoT][lnk-c2d-tutorial]
* [Elaborare messaggi da dispositivo a cloud dell'hub IoT][lnk-d2c-tutorial]

[img-lifecycle]: ./media/iot-hub-devguide-messaging/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide-messaging/eventhubcompatible.png

[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-amqp]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf
[lnk-mqtt]: http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-management-portal]: https://portal.azure.com
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-features.md#partitions
[lnk-portal]: iot-hub-create-through-portal.md
[lnk-getstarted-eh]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-getstarted-queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[lnk-getstarted-topic]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md

[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-compatible-endpoint]: iot-hub-devguide-messaging.md#read-device-to-cloud-messages
[lnk-protocols]: iot-hub-devguide-messaging.md#communication-protocols
[lnk-message-format]: iot-hub-devguide-messaging.md#message-format
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-ttl]: iot-hub-devguide-messaging.md#message-expiration-time-to-live
[lnk-c2d-configuration]: iot-hub-devguide-messaging.md#cloud-to-device-configuration-options
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle
[lnk-feedback]: iot-hub-devguide-messaging.md#message-feedback
[lnk-antispoofing]: iot-hub-devguide-messaging.md#anti-spoofing-properties
[lnk-compare]: iot-hub-compare-event-hubs.md

[lnk-devguide-upload]: iot-hub-devguide-file-upload.md
[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx
[lnk-devguide-query-language]: iot-hub-devguide-query-language.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-event-hub-partitions]: ../event-hubs/event-hubs-features.md#partitions

