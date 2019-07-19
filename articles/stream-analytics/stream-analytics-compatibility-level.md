---
title: Comprendere il livello di compatibilità per i processi di Analisi di flusso di Azure
description: Informazioni su come impostare un livello di compatibilità per un processo di Analisi di flusso di Azure e modifiche sostanziali nel livello di compatibilità più recente
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 5/2/2019
ms.openlocfilehash: 8e3ae84242a1a9b76fbb18a8d8164c97a62a97d9
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68003919"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Livello di compatibilità per i processi di Analisi di flusso di Azure

Questo articolo descrive l'opzione del livello di compatibilità in analisi di flusso di Azure. Analisi di flusso è un servizio gestito, con aggiornamenti regolari delle funzionalità e miglioramenti delle prestazioni. La maggior parte degli aggiornamenti dei runtime del servizio viene resa disponibile automaticamente agli utenti finali. 

Tuttavia, alcune nuove funzionalità del servizio possono introdurre una modifica sostanziale, ad esempio una modifica nel comportamento di un processo esistente, o una modifica nel modo in cui i dati vengono utilizzati nei processi in esecuzione. È possibile mantenere i processi di analisi di flusso esistenti in esecuzione senza modifiche sostanziali lasciando l'impostazione del livello di compatibilità diminuita. Quando si è pronti per i comportamenti di runtime più recenti, è possibile acconsentire esplicitamente aumentando il livello di compatibilità. 

## <a name="choose-a-compatibility-level"></a>Scegliere un livello di compatibilità

Il livello di compatibilità controlla il comportamento di runtime di un processo di Analisi di flusso. 

Analisi di flusso di Azure supporta attualmente tre livelli di compatibilità:

* 1,0-comportamento precedente
* 1,1-comportamento predefinito
* 1,2 (anteprima): comportamento più recente con i miglioramenti più recenti della valutazione

Il livello di compatibilità 1,0 originale è stato introdotto durante la disponibilità generale di analisi di flusso di Azure diversi anni fa.

Quando si crea un nuovo processo di analisi di flusso, è consigliabile crearlo usando il livello di compatibilità più recente. Iniziare la progettazione del processo basandosi sui comportamenti più recenti, per evitare di aggiungere modifiche e complessità in un secondo momento.

## <a name="set-the-compatibility-level"></a>Configurare il livello di compatibilità

È possibile impostare il livello di compatibilità per un processo di analisi di flusso nel portale di Azure o usando la [chiamata all'API REST di creazione del processo](/rest/api/streamanalytics/stream-analytics-job).

Per aggiornare il livello di compatibilità del processo nel portale di Azure:

1. Usare il [portale di Azure](https://portal.azure.com) per individuare il processo di analisi di flusso.
2. **Arrestare** il processo prima di aggiornare il livello di compatibilità. Il livello di compatibilità, infatti, non può essere aggiornato se il processo è in esecuzione.
3. Nell'intestazione **Configura** selezionare livello di **compatibilità**.
4. Scegliere il valore del livello di compatibilità desiderato.
5. Selezionare **Save (Salva** ) nella parte inferiore della pagina.

![Livello di compatibilità di Analisi di flusso nel portale di Azure](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

Quando si aggiorna il livello di compatibilità, il servizio di compilazione Transact-SQL convalida il processo con la sintassi corrispondente al livello di compatibilità selezionato.

## <a name="compatibility-level-12-preview"></a>Livello di compatibilità 1,2 (anteprima)

Nel livello di compatibilità 1,2 sono state introdotte le modifiche principali seguenti:

### <a name="geospatial-functions"></a>Funzioni geospaziali

**Livelli precedenti:** Analisi di flusso di Azure ha usato calcoli di geografia.

**livello 1,2:** Analisi di flusso di Azure consente di calcolare le coordinate geografiche proiettate geometriche. Non sono state apportate modifiche alla firma delle funzioni geospaziali. Tuttavia, la semantica è leggermente diversa, consentendo un calcolo più preciso rispetto a prima.

Analisi di flusso di Azure supporta l'indicizzazione dei dati di riferimento geospaziale È possibile indicizzare i dati di riferimento contenenti elementi geospaziali per un calcolo join più rapido.

Le funzioni geospaziali aggiornate portano l'espressività completa del formato geospaziale WKT (Well Known Text). È possibile specificare altri componenti geospaziali che non sono stati precedentemente supportati con GeoJSON.

Per altre informazioni, vedere [aggiornamenti alle funzionalità geospaziali in analisi di flusso di Azure-cloud e IOT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Esecuzione di query parallele per origini di input con più partizioni

**Livelli precedenti:** Le query di analisi di flusso di Azure richiedono l'uso della clausola PARTITION BY per parallelizzare l'elaborazione di query tra partizioni di origine di input.

**livello 1,2:** Se la logica di query può essere eseguita in parallelo tra le partizioni di origine di input, analisi di flusso di Azure crea istanze di query separate ed esegue calcoli in parallelo.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Integrazione dell'API bulk nativa con output CosmosDB

**Livelli precedenti:** Il comportamento di Upsert è *Insert o merge*.

**livello 1,2:** L'integrazione dell'API bulk nativa con l'output CosmosDB ottimizza la velocità effettiva e gestisce in modo efficiente le richieste di limitazione. Per altre informazioni, vedere [la pagina relativa all'output di analisi di flusso di Azure per Azure Cosmos DB](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12).

Il comportamento di Upsert è *Insert o Replace*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset durante la scrittura nell'output SQL

**Livelli precedenti:** I tipi [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) sono stati modificati in formato UTC.

**livello 1,2:** DateTimeOffset non viene più modificato.

### <a name="long-when-writing-to-sql-output"></a>Long durante la scrittura nell'output SQL

**Livelli precedenti:** I valori sono stati troncati in base al tipo di destinazione.

**livello 1,2:** I valori che non rientrano nel tipo di destinazione vengono gestiti in base ai criteri di errore di output.

### <a name="strict-validation-of-prefix-of-functions"></a>Convalida rigorosa del prefisso delle funzioni

**Livelli precedenti:** Non è stata convalidata alcuna restrizione dei prefissi di funzione.

**livello 1,2:** Analisi di flusso di Azure ha una convalida rigorosa dei prefissi di funzione. L'aggiunta di un prefisso a una funzione incorporata causa un errore. Ad esempio,`myprefix.ABS(…)` non è supportato.

L'aggiunta di un prefisso alle aggregazioni predefinite genera anche un errore. Ad esempio, `myprefix.SUM(…)` non è supportato.

L'utilizzo del prefisso "System" per tutte le funzioni definite dall'utente genera un errore.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Non consentire matrici e oggetti come proprietà chiave nell'adattatore di output Cosmos DB

**Livelli precedenti:** I tipi di matrice e oggetto sono supportati come proprietà chiave.

**livello 1,2:** I tipi di matrice e oggetto non sono più supportati come proprietà chiave.

## <a name="compatibility-level-11"></a>Livello di compatibilità 1,1

Nel livello di compatibilità 1.1 sono state introdotte le modifiche sostanziali seguenti:

### <a name="service-bus-xml-format"></a>Formato XML del bus di servizio

**livello 1,0:** Analisi di flusso di Azure usava DataContractSerializer e nel contenuto del messaggio erano quindi inclusi i tag XML. Ad esempio:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**livello 1,1:** il contenuto del messaggio contiene direttamente il flusso, senza tag aggiuntivi. Ad esempio: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Mantenimento della distinzione tra maiuscole e minuscole per i nomi dei campi

**livello 1,0:** i nomi dei campi venivano modificati in lettere minuscole quando venivano elaborati dal motore di Analisi di flusso di Azure.

**1,1 Level:** la distinzione tra maiuscole e minuscole viene mantenute per i nomi di campo quando vengono elaborati dal motore di analisi di flusso di Azure.

> [!NOTE]
> La distinzione tra maiuscole e minuscole persistente non è ancora disponibile per i processi di Analisi di flusso ospitati in un ambiente di dispositivo perimetrale. Se il processo è ospitato in un dispositivo perimetrale, quindi, tutti i nomi di campo vengono convertiti in caratteri minuscoli.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**livello 1,0:** il comando CREATE TABLE non filtrava gli eventi contrassegnati con NaN (Not a Number, ad esempio: Infinity, -Infinity) in una colonna di tipo FLOAT, poiché non erano inclusi nell'intervallo documentato per questi numeri.

**livello 1,1:** CREATE TABLE consente di specificare uno schema sicuro. Il motore di Analisi di flusso convalida la conformità dei dati a questo schema e, con questo modello, il comando può filtrare anche eventi contrassegnati con valori NaN.

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>Disabilitare il multicast automatico per le stringhe DateTime in JSON

**livello 1,0:** il parser JSON eseguiva automaticamente l'upcast dei valori di stringa con le informazioni di data/ora/fuso orario su un tipo DateTime e li convertiva in formato UTC. Questo comportamento ha causato la perdita delle informazioni sul fuso orario.

**livello 1,1:** non viene più eseguito l'upcast automatico dei valori di stringa con le informazioni di data/ora/fuso orario su un tipo DateTime. Le informazioni sul fuso orario vengono quindi mantenute.

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi degli input di Analisi di flusso di Azure](stream-analytics-troubleshoot-input.md)
* [Integrità risorse di analisi di flusso](stream-analytics-resource-health.md)
