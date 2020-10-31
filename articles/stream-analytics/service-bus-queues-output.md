---
title: Output delle code del bus di servizio da analisi di flusso di Azure
description: Questo articolo descrive le code del bus di servizio come output per analisi di flusso di Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: bba7da0343f234d8cc057ac4ecfa2a4f2e5b7242
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129882"
---
# <a name="service-bus-queues-output-from-azure-stream-analytics"></a>Output delle code del bus di servizio da analisi di flusso di Azure

Le [code del bus di servizio](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) consentono un recapito dei messaggi di tipo FIFO a uno o più consumer concorrenti. In genere, i messaggi vengono ricevuti ed elaborati dai destinatari nell'ordine temporale in cui sono stati aggiunti alla coda. Ogni messaggio viene ricevuto ed elaborato da un solo consumer di messaggi.

Nel [livello di compatibilità 1.2](stream-analytics-compatibility-level.md), Analisi di flusso di Azure usa il protocollo di messaggistica [Advanced Message Queueing Protocol (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md) per la scrittura in code e argomenti del bus di servizio. AMQP consente di creare applicazioni ibride multipiattaforma usando un protocollo aperto standard.

## <a name="output-configuration"></a>Configurazione di output

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un output della coda.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questa coda del bus di servizio. |
| Spazio dei nomi del bus di servizio |Contenitore per un set di entità di messaggistica. |
| Nome della coda |Nome della coda del bus di servizio. |
| Nome criteri coda |Durante la creazione di una coda, nella scheda **Configura** della coda è anche possibile creare criteri di accesso condiviso. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. |
| Chiave criteri coda |Chiave di accesso condiviso usata per autenticare l'accesso allo spazio dei nomi del bus di servizio di Azure. |
| Formato di serializzazione eventi |Formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro. |
| Codifica |Al momento UTF-8 è l'unico formato di codifica supportato per i formati CSV e JSON. |
| Delimitatore |Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati in formato CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Format |Applicabile solo per il tipo JSON. **Separato da righe** specifica che l'output viene formattato separando ciascun oggetto JSON con una nuova riga. Se si seleziona **Separato da righe** , JSON viene letto un oggetto alla volta. L'intero contenuto non è di per sé un oggetto JSON valido. **Matrice** specifica che l'output viene formattato come matrice di oggetti JSON. |
| Colonne delle proprietà | Facoltativa. Colonne delimitate da virgole che devono essere associate come proprietà utente del messaggio in uscita anziché del payload. Altre informazioni su questa funzionalità sono illustrate nella sezione [Proprietà dei metadati personalizzati per l'output](#custom-metadata-properties-for-output). |
| Colonne delle proprietà di sistema | Facoltativa. Coppie chiave-valore delle proprietà di sistema e nomi di colonna corrispondenti che devono essere allegati al messaggio in uscita anziché al payload.  |

Il numero di partizioni è [basato sullo SKU e sulle dimensioni del bus di servizio](../service-bus-messaging/service-bus-partitioning.md). La chiave di partizione è un valore intero univoco per ogni partizione.

## <a name="partitioning"></a>Partizionamento

Il partizionamento viene scelto automaticamente. Il numero di partizioni è [basato sullo SKU e sulle dimensioni del bus di servizio](../service-bus-messaging/service-bus-partitioning.md). La chiave di partizione è un valore intero univoco per ogni partizione. Il numero di writer di output è uguale al numero di partizioni nella coda di output.

## <a name="output-batch-size"></a>Dimensione del batch di output

La dimensione massima dei messaggi è 256 KB per messaggio per il livello standard e 1 MB per il livello Premium. Per altre informazioni, vedere [limiti del bus di servizio](../service-bus-messaging/service-bus-quotas.md). Per ottimizzare, utilizzare un singolo evento per messaggio.

## <a name="custom-metadata-properties-for-output"></a>Proprietà dei metadati personalizzati per l'output

È possibile aggiungere colonne di query come proprietà utente ai messaggi in uscita. Queste colonne non vengono inserite nel payload. Le proprietà sono presenti sotto forma di dizionario nel messaggio di output. *Key* è il nome della colonna e *value* è il valore della colonna nel dizionario delle proprietà. Sono supportati tutti i tipi di dati di Analisi di flusso, ad eccezione di record e matrice.

Nell'esempio seguente i campi `DeviceId` e `DeviceStatus` vengono aggiunti ai metadati.

1. Utilizzare la query seguente:

   ```sql
   select *, DeviceId, DeviceStatus from iotHubInput
   ```

1. Configurare `DeviceId,DeviceStatus` come colonne di proprietà nell'output.

   :::image type="content" source="media/service-bus-queues-output/property-columns.png" alt-text="Colonne delle proprietà":::

L'immagine seguente è delle proprietà del messaggio di output previste ispezionate in EventHub tramite [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

:::image type="content" source="media/service-bus-queues-output/custom-properties.png" alt-text="Colonne delle proprietà":::

## <a name="system-properties"></a>Proprietà di sistema

È possibile aggiungere colonne di query come [proprietà di sistema](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties&preserve-view=true) ai messaggi in uscita di code o argomenti del bus di servizio.

Queste colonne non vengono inserite nel payload, bensì la [proprietà di sistema](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties&preserve-view=true) BrokeredMessage corrispondente viene popolata con i valori della colonna di query.
Sono supportate le proprietà di sistema seguenti: `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`.

I valori stringa di queste colonne vengono analizzati come tipo di valore della proprietà di sistema corrispondente e gli eventuali errori di analisi vengono considerati come errori di dati.
Questo campo viene fornito come formato oggetto JSON. Per informazioni dettagliate su questo formato, seguire questa procedura:

* Racchiuso tra parentesi graffe {}.
* Scritto in coppie chiave/valore.
* Chiavi e valori devono essere stringhe.
* Key è il nome della proprietà di sistema e value è il nome della colonna della query.
* Chiavi e valori sono separati da due punti.
* Ogni coppia chiave/valore è separata da una virgola.

Di seguito, viene illustrato come usare questa proprietà:

* Query: `select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Colonne delle proprietà di sistema: `{ "MessageId": "column1", "PartitionKey": "column2"}`

Viene impostato `MessageId` nei messaggi della coda del bus di servizio con i valori di `column1` e PartitionKey viene impostato con i valori di `column2`.

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Creare un processo di Analisi di flusso tramite il portale di Azure](stream-analytics-quick-create-portal.md)
* [Avvio rapido: Creare un processo di Analisi di flusso di Azure con l'interfaccia della riga di comando di Azure](quick-create-azure-cli.md)
* [Avvio rapido: Creare un processo di Analisi di flusso di Azure con un modello di Resource Manager](quick-create-azure-resource-manager.md)
* [Guida introduttiva: creare un processo di analisi di flusso usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Guida introduttiva: Creare un processo di Analisi di flusso di Azure con Visual Studio Code](stream-analytics-quick-create-vs.md)
* [Guida introduttiva: creare un processo di analisi di flusso di Azure in Visual Studio Code](quick-create-visual-studio-code.md)