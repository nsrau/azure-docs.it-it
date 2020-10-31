---
title: Argomenti del bus di servizio output di analisi di flusso di Azure
description: Questo articolo descrive gli argomenti del bus di servizio come output per analisi di flusso di Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: d2a9063a202ba542279efd8017d282fe0aa78d42
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129865"
---
# <a name="service-bus-topics-output-from-azure-stream-analytics"></a>Argomenti del bus di servizio output di analisi di flusso di Azure

Le code del bus di servizio forniscono un metodo di comunicazione uno-a-uno dal mittente al destinatario. Gli [argomenti del bus di servizio](/previous-versions/azure/hh367516(v=azure.100)) offrono una forma di comunicazione uno-a-molti.

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un output dell'argomento del bus di servizio.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questo argomento del bus di servizio. |
| Spazio dei nomi del bus di servizio |Contenitore per un set di entità di messaggistica. Quando si crea un nuovo hub eventi, viene anche creato uno spazio dei nomi del bus di servizio. |
| Nome argomento |Gli argomenti sono entità di messaggistica, simili agli Hub eventi e alle code. Sono progettati per raccogliere i flussi di eventi da dispositivi e servizi. Quando un argomento viene creato, gli viene anche assegnato un nome specifico. Dal momento che i messaggi inviati a un argomento non sono disponibili se non viene creata una sottoscrizione, assicurarsi che esistano una o più sottoscrizioni per l'argomento. |
| Nome criteri argomento |Durante la creazione di un argomento del bus di servizio, nella scheda **Configura** dell'argomento è anche possibile creare criteri di accesso condiviso. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. |
| Chiave criteri argomento |Chiave di accesso condiviso usata per autenticare l'accesso allo spazio dei nomi del bus di servizio di Azure. |
| Formato di serializzazione eventi |Formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro. |
| Codifica |Se il formato è CSV o JSON, è necessario specificare un formato di codifica. Al momento UTF-8 è l'unico formato di codifica supportato. |
| Delimitatore |Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati in formato CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Colonne delle proprietà | Facoltativa. Colonne delimitate da virgole che devono essere associate come proprietà utente del messaggio in uscita anziché del payload. Altre informazioni su questa funzionalità sono illustrate nella sezione [Proprietà dei metadati personalizzati per l'output](#custom-metadata-properties-for-output). |
| Colonne delle proprietà di sistema | Facoltativa. Coppie chiave-valore delle proprietà di sistema e nomi di colonna corrispondenti che devono essere allegati al messaggio in uscita anziché al payload. |

Il numero di partizioni è [basato sullo SKU e sulle dimensioni del bus di servizio](../service-bus-messaging/service-bus-partitioning.md). La chiave di partizione è un valore intero univoco per ogni partizione.

## <a name="partitioning"></a>Partizionamento

Il partizionamento viene scelto automaticamente. Il numero di partizioni è [basato sullo SKU e sulle dimensioni del bus di servizio](../service-bus-messaging/service-bus-partitioning.md). La chiave di partizione è un valore intero univoco per ogni partizione. Il numero di writer di output corrisponde al numero di partizioni nell'argomento di output.

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

   :::image type="content" source="media/service-bus-topics-output/property-columns.png" alt-text="Colonne delle proprietà":::

L'immagine seguente è delle proprietà del messaggio di output previste ispezionate in EventHub tramite [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

:::image type="content" source="media/service-bus-topics-output/custom-properties.png" alt-text="Colonne delle proprietà":::

## <a name="system-properties"></a>Proprietà di sistema

È possibile aggiungere colonne di query come [proprietà di sistema](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties&preserve-view=true) ai messaggi in uscita di code o argomenti del bus di servizio. Queste colonne non vengono inserite nel payload, bensì la [proprietà di sistema](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties&preserve-view=true) BrokeredMessage corrispondente viene popolata con i valori della colonna di query.
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