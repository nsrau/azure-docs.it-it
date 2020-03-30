---
title: Livelli di compatibilità di Analisi di flusso di AzureAzure Stream Analytics compatibility levels
description: Informazioni su come impostare un livello di compatibilità per un processo di Analisi di flusso di Azure e modifiche sostanziali nel livello di compatibilità più recente
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 8f22b1ff97826dc318794aca58973b1276e74209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087851"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Livello di compatibilità per i processi di Analisi di flusso di Azure

Questo articolo descrive l'opzione del livello di compatibilità in Analisi di flusso di Azure.This article describes the compatibility level option in Azure Stream Analytics. Analisi di flusso è un servizio gestito, con aggiornamenti regolari delle funzionalità e miglioramenti delle prestazioni. La maggior parte degli aggiornamenti di runtime del servizio viene automaticamente resa disponibile agli utenti finali. 

Tuttavia, alcune nuove funzionalità del servizio possono introdurre una modifica importante, ad esempio una modifica nel comportamento di un processo esistente o una modifica nel modo in cui i dati vengono utilizzati nei processi in esecuzione. Puoi mantenere in esecuzione i processi di Analisi di flusso esistenti senza modifiche importanti lasciando bassa l'impostazione del livello di compatibilità. Quando si è pronti per i comportamenti di runtime più recenti, è possibile acconsentire esplicitamente aumentando il livello di compatibilità. 

## <a name="choose-a-compatibility-level"></a>Scegliere un livello di compatibilità

Il livello di compatibilità controlla il comportamento di runtime di un processo di Analisi di flusso. 

Azure Stream Analytics supporta attualmente tre livelli di compatibilità:Azure Stream Analytics currently supports three compatibility levels:

* 1.0 - Livello di compatibilità originale, introdotto durante la disponibilità generale di Analisi di flusso di Azure diversi anni fa.
* 1.1 - Comportamento precedente
* 1.2 - Comportamento più recente con miglioramenti più recenti

Quando crei un nuovo processo di Analisi di flusso, è consigliabile crearlo utilizzando il livello di compatibilità più recente. Iniziare la progettazione del lavoro basandosi sui comportamenti più recenti, per evitare ulteriori cambiamenti e complessità in un secondo momento.

## <a name="set-the-compatibility-level"></a>Configurare il livello di compatibilità

È possibile impostare il livello di compatibilità per un processo di Analisi di flusso nel portale di Azure o usando la [chiamata all'API REST del processo](/rest/api/streamanalytics/stream-analytics-job)di creazione.

Per aggiornare il livello di compatibilità del processo nel portale di Azure:

1. Usare il portale di [Azure](https://portal.azure.com) per individuare il processo di Analisi di flusso.
2. **Arrestare** il processo prima di aggiornare il livello di compatibilità. Il livello di compatibilità, infatti, non può essere aggiornato se il processo è in esecuzione.
3. Sotto **l'intestazione Configura** selezionare Livello **di compatibilità**.
4. Scegliere il valore del livello di compatibilità desiderato.
5. Seleziona **Salva** nella parte inferiore della pagina.

![Livello di compatibilità di Analisi di flusso nel portale di Azure](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

Quando si aggiorna il livello di compatibilità, il compilatore T convalida il processo con la sintassi corrispondente al livello di compatibilità selezionato.

## <a name="compatibility-level-12"></a>Livello di compatibilità 1.2

Nel livello di compatibilità 1.2 sono state introdotte le seguenti modifiche principali:

###  <a name="amqp-messaging-protocol"></a>Protocollo di messaggistica AMQP

**1.2 livello**: Azure Stream Analytics usa il protocollo di messaggistica [Advanced Message Queueing Protocol (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md) per scrivere nelle code e negli argomenti del bus di servizio. AMQP consente di creare applicazioni ibride multipiattaforma usando un protocollo aperto standard.

### <a name="geospatial-functions"></a>Funzioni geospaziali

**Livelli precedenti:** Analisi di flusso di Azure ha usato i calcoli geografici.

**1.2 livello:** Analisi di flusso di Azure consente di calcolare le coordinate geografiche proiettate geometriche. Non c'è nessun cambiamento nella firma delle funzioni geospaziali. Tuttavia, la loro semantica è leggermente diversa, consentendo un calcolo più preciso rispetto a prima.

Analisi di flusso di Azure supporta l'indicizzazione dei dati di riferimento geospaziale. I dati di riferimento contenenti elementi geospaziali possono essere indicizzati per un calcolo di join più veloce.

Le funzioni geospaziali aggiornate portano la piena espressività del formato geospaziale WKT (Well Known Text). È possibile specificare altri componenti geospaziali che non erano supportati in precedenza con GeoJson.You can specify other geospatial components that weren't previously supported with GeoJson.

Per altre informazioni, vedere Aggiornamenti alle funzionalità geospaziali in Analisi di flusso di [Azure - Cloud e IoT Edge.](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/)

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Esecuzione di query parallele per origini di input con più partizioniParallel query execution for input sources with multiple partitions

**Livelli precedenti:** Le query di Analisi di flusso di Azure richiedevano l'uso della clausola PARTITION BY per parallelizzare l'elaborazione delle query tra le partizioni di origine di input.

**1.2 livello:** Se la logica di query può essere parallelizzata tra partizioni di origine di input, Analisi flusso di Azure crea istanze di query separate ed esegue calcoli in parallelo.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Integrazione dell'API bulk nativa con l'output CosmosDB

**Livelli precedenti:** Il comportamento upsert era *insert o merge*.

**1.2 livello:** L'integrazione delle API bulk native con l'output CosmosDB massimizza la velocità effettiva e gestisce in modo efficiente la limitazione delle richieste. Per altre informazioni, vedere [la pagina Dell'output](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12)di Analisi di flusso di Azure nel database Cosmos di Azure.For more information, see the Azure Stream Analytics output to Azure Cosmos DB page .

Il comportamento di upsert è *inserimento o sostituzione*di .

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset durante la scrittura nell'output SQLDateTimeOffset when writing to SQL output

**Livelli precedenti:** i tipi [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) sono stati modificati in UTC.

**1.2 livello:** DateTimeOffset non è più regolato.

### <a name="long-when-writing-to-sql-output"></a>Lungo durante la scrittura nell'output SQLLong when writing to SQL output

**Livelli precedenti:** I valori sono stati troncati in base al tipo di destinazione.

**1.2 livello:** I valori che non rientrano nel tipo di destinazione vengono gestiti in base ai criteri di errore di output.

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>Serializzazione di record e matrici durante la scrittura nell'output SQLRecord and array serialization when writing to SQL output

**Livelli precedenti:** I record sono stati scritti come "Record" e le matrici sono state scritte come "Array".

**1.2 livello:** I record e le matrici vengono serializzati in formato JSON.

### <a name="strict-validation-of-prefix-of-functions"></a>Convalida rigorosa del prefisso delle funzioni

**Livelli precedenti:** Non vi era alcuna convalida rigorosa dei prefissi di funzione.

**1.2 livello:** Analisi di flusso di Azure ha una convalida rigorosa dei prefissi di funzione. L'aggiunta di un prefisso a una funzione incorporata causa un errore. Ad esempio,`myprefix.ABS(…)` non è supportato.

L'aggiunta di un prefisso alle aggregazioni predefinite genera anche un errore. Ad esempio, `myprefix.SUM(…)` non è supportato.

L'utilizzo del prefisso "system" per tutte le funzioni definite dall'utente genera un errore.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Non consentire Array e Object come proprietà chiave nell'adattatore di output Cosmos DB

**Livelli precedenti:** I tipi Array e Object sono supportati come proprietà chiave.

**1.2 livello:** I tipi Array e Object non sono più supportati come proprietà chiave.

## <a name="compatibility-level-11"></a>Livello di compatibilità 1.1

Nel livello di compatibilità 1.1 sono state introdotte le modifiche sostanziali seguenti:

### <a name="service-bus-xml-format"></a>Formato XML del bus di servizio

**1.0 livello:** Azure Stream Analytics ha usato DataContractSerializer, pertanto il contenuto del messaggio includeva tag XML. Ad esempio:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**1.1 livello:** Il contenuto del messaggio contiene direttamente il flusso senza tag aggiuntivi. Ad esempio: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Distinzione maiuscole/minuscole persistente nei nomi dei campi

**1.0 livello:** I nomi dei campi sono stati modificati in lettere minuscole quando vengono elaborati dal motore di Analisi di flusso di Azure.Field names were changed to lower case when processed by the Azure Stream Analytics engine.

**1.1 livello:** la distinzione tra maiuscole e minuscole viene mantenuta per i nomi dei campi quando vengono elaborati dal motore di Analisi di Flusso di Azure.1 level: case-sensitivity is persisted for field names when they are processed by the Azure Stream Analytics engine.

> [!NOTE]
> La distinzione tra maiuscole e minuscole persistente non è ancora disponibile per i processi di Analisi di flusso ospitati in un ambiente di dispositivo perimetrale. Se il processo è ospitato in un dispositivo perimetrale, quindi, tutti i nomi di campo vengono convertiti in caratteri minuscoli.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**1.0 livello:** Il comando CREATE TABLE non filtra gli eventi con NaN (Not-a-Number). ad esempio: Infinity, -Infinity) in una colonna di tipo FLOAT, poiché non erano inclusi nell'intervallo documentato per questi numeri.

**1.1 livello:** CREATE TABLE consente di specificare uno schema sicuro. Il motore di Analisi di flusso convalida la conformità dei dati a questo schema e, con questo modello, il comando può filtrare anche eventi contrassegnati con valori NaN.

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>Disabilitare l'upcast automatico per le stringhe datetime in JSONDisable automatic upcast for datetime strings in JSON

**1.0 livello:** Il parser JSON eleverrebbe automaticamente i valori stringa con informazioni di data/ora/fuso orario nel tipo DateTime e quindi li convertirà in UTC. Questo comportamento ha comportato la perdita delle informazioni sul fuso orario.

**1.1 livello:** Non esiste più l'upcast automatico dei valori stringa con informazioni di data/ora/fuso orario al tipo DateTime. Le informazioni sul fuso orario vengono quindi mantenute.

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi degli input di Analisi di flusso di Azure](stream-analytics-troubleshoot-input.md)
* [Integrità delle risorse di Analisi di flusso](stream-analytics-resource-health.md)
