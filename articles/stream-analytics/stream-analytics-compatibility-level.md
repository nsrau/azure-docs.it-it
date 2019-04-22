---
title: Comprendere il livello di compatibilità per i processi di Analisi di flusso di Azure
description: Informazioni su come impostare un livello di compatibilità per un processo di Analisi di flusso di Azure e modifiche sostanziali nel livello di compatibilità più recente
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: b5c833798f8533e7c6fbe3595a726ac6ce56e2d2
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682815"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Livello di compatibilità per i processi di Analisi di flusso di Azure

Questo articolo descrive l'opzione a livello di compatibilità in Azure Stream Analitica. Analitica Stream è un servizio gestito con regolari aggiornamenti delle funzionalità e miglioramenti delle prestazioni. La maggior parte degli aggiornamenti di runtime del servizio vengono automaticamente resi disponibile agli utenti finali. 

Tuttavia, alcune nuove funzionalità del servizio può introdurre un cambiamento radicale, ad esempio una modifica nel comportamento di un processo esistente, o una modifica nei dati di modalità verrà utilizzata in processi in esecuzione. È possibile mantenere i processi di Analitica Stream esistenti in esecuzione senza modifiche rilevanti se si lascia l'impostazione ridotti a livello di compatibilità. Quando si è pronti per i comportamenti di runtime più recenti, è possibile acconsentire esplicitamente dall'aumento del livello di compatibilità. 

## <a name="choose-a-compatibility-level"></a>Scegliere un livello di compatibilità

Il livello di compatibilità controlla il comportamento di runtime di un processo di Analisi di flusso. 

Azure Analitica Stream supporta attualmente tre livelli di compatibilità:

* 1.0 - livello predefinito
* 1.1 - comportamento di versione corrente
* 1.2 (anteprima): il comportamento più recente con i miglioramenti più recenti nella valutazione

Il livello di compatibilità originale 1.0 è stato introdotto durante la disponibilità generale di Azure Stream Analitica diversi anni fa.

Quando si crea un nuovo processo di Stream Analitica, è consigliabile crearla usando il livello di compatibilità più recente. Avviare la progettazione processo basandosi al momento i comportamenti più recenti, per evitare modifiche aggiunta e la complessità in un secondo momento.

## <a name="set-the-compatibility-level"></a>Configurare il livello di compatibilità

È possibile impostare il livello di compatibilità per un processo di Stream Analitica nel portale di Azure o usando il [creare processi, chiamare l'API REST](/rest/api/streamanalytics/stream-analytics-job).

Per aggiornare il livello di compatibilità del processo nel portale di Azure:

1. Usare la [portale di Azure](https://portal.azure.com) per individuare al processo di Stream Analitica.
2. **Arrestare** il processo prima di aggiornare il livello di compatibilità. Il livello di compatibilità, infatti, non può essere aggiornato se il processo è in esecuzione.
3. Sotto il **Configure** titolo, selezionare **livello di compatibilità**.
4. Scegliere il valore a livello di compatibilità desiderato.
5. Selezionare **salvare** nella parte inferiore della pagina.

![Livello di compatibilità di Analisi di flusso nel portale di Azure](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

Quando si aggiorna il livello di compatibilità, il servizio di compilazione Transact-SQL convalida il processo con la sintassi corrispondente al livello di compatibilità selezionato.

## <a name="compatibility-level-12"></a>Livello di compatibilità 1.2

Nel livello di compatibilità 1.2 vengono introdotte le modifiche principali seguenti:

### <a name="geospatial-functions"></a>Funzioni geospaziali

**Livelli precedenti:** Azure Stream Analitica utilizzati calcoli geografici.

**1.2 livello:** Azure Stream Analitica consente di calcolare le coordinate geografiche proiettata geometrica. Non sussiste alcuna modifica della firma delle funzioni geospaziali. Tuttavia, presentano una semantica è leggermente diversa, che consente di calcolo più preciso rispetto a prima.

Azure Stream Analitica supporta l'indicizzazione dei dati di geospaziale riferimento. Per un calcolo di join più veloce è possibile indicizzare i dati di riferimento che contiene elementi geospaziali.

Le funzioni geospaziali aggiornato portare l'espressività completa del formato geospaziali Well-Known Text (WKT). È possibile specificare altri componenti geospaziali che in precedenza non erano supportate con GeoJson.

Per altre informazioni, vedere [gli aggiornamenti alle funzionalità geospaziale in Azure Stream Analitica – Cloud e IoT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Esecuzione di query parallele per le origini di input con più partizioni

**Livelli precedenti:** Query Analitica Stream Azure richiesto l'utilizzo della clausola PARTITION BY per parallelizzare l'elaborazione di query tra partizioni di origine di input.

**1.2 livello:** Se per la logica di query può essere parallelizzata tra le partizioni di origine di input, Azure Stream Analitica crea istanze di query separata ed esegue i calcoli in parallelo.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Integrazione di API Bulk nativa con l'output di COSMOS DB

**Livelli precedenti:** È stato il comportamento di upsert *insert o merge*.

**1.2 livello:** Integrazione di API Bulk nativa con l'output di COSMOS DB ottimizza la velocità effettiva e gestisce in modo efficiente la limitazione delle richieste.

È il comportamento di upsert *inserire o sostituire*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset durante la scrittura in output SQL

**Livelli precedenti:** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) tipi sono stati modificati in formato UTC.

**1.2 livello:** DateTimeOffset non è più viene modificato.

### <a name="strict-validation-of-prefix-of-functions"></a>Convalida di tipo Strict del prefisso delle funzioni

**Livelli precedenti:** Si è verificato alcun convalida di tipo strict dei prefissi di funzione.

**1.2 livello:** Azure Stream Analitica dispone di una convalida rigorosa di prefissi (funzione). Aggiunta di un prefisso a una funzione predefinita genera un errore. Ad esempio,`myprefix.ABS(…)` non è supportato.

Aggiunta di un prefisso per le aggregazioni incorporate comporta anche in errore. Ad esempio, `myprefix.SUM(…)` non è supportato.

Usando il prefisso "system" per i risultati di funzioni definite dall'utente in errore.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Non consentire di matrice e oggetto come proprietà chiave nell'adattatore di output di Cosmos DB

**Livelli precedenti:** Tipi di matrice e oggetto erano supportati come proprietà chiave.

**1.2 livello:** Tipi di matrice e oggetto non sono supportati non più come proprietà chiave.

## <a name="compatibility-level-11"></a>Livello di compatibilità 1.1

Nel livello di compatibilità 1.1 sono state introdotte le modifiche sostanziali seguenti:

### <a name="service-bus-xml-format"></a>Formato XML del Bus di servizio

**livello di 1.0:** Analisi di flusso di Azure usava DataContractSerializer e nel contenuto del messaggio erano quindi inclusi i tag XML. Ad esempio: 

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**livello 1.1:** il contenuto del messaggio contiene direttamente il flusso, senza tag aggiuntivi. Ad esempio: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Distinzione maiuscole/minuscole persistente nei nomi dei campi

**livello di 1.0:** i nomi dei campi venivano modificati in lettere minuscole quando venivano elaborati dal motore di Analisi di flusso di Azure.

**livello 1.1:** distinzione maiuscole/minuscole viene mantenuta nei nomi dei campi quando vengono elaborate dal motore di Analitica Stream di Azure.

> [!NOTE]
> La distinzione tra maiuscole e minuscole persistente non è ancora disponibile per i processi di Analisi di flusso ospitati in un ambiente di dispositivo perimetrale. Se il processo è ospitato in un dispositivo perimetrale, quindi, tutti i nomi di campo vengono convertiti in caratteri minuscoli.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**livello di 1.0:** il comando CREATE TABLE non filtrava gli eventi contrassegnati con NaN (Not a Number, ad esempio: Infinity, -Infinity) in una colonna di tipo FLOAT, poiché non erano inclusi nell'intervallo documentato per questi numeri.

**livello 1.1:** CREATE TABLE consente di specificare uno schema sicuro. Il motore di Analisi di flusso convalida la conformità dei dati a questo schema e, con questo modello, il comando può filtrare anche eventi contrassegnati con valori NaN.

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>Disattivazione dell'upcast automatico per le stringhe di data/ora in formato JSON

**livello di 1.0:** il parser JSON eseguiva automaticamente l'upcast dei valori di stringa con le informazioni di data/ora/fuso orario su un tipo DateTime e li convertiva in formato UTC. Questo comportamento comportava la perdita di informazioni sul fuso orario.

**livello 1.1:** non viene più eseguito l'upcast automatico dei valori di stringa con le informazioni di data/ora/fuso orario su un tipo DateTime. Le informazioni sul fuso orario vengono quindi mantenute.

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi degli input di Analisi di flusso di Azure](stream-analytics-troubleshoot-input.md)
* [Integrità risorse di Analitica di Stream](stream-analytics-resource-health.md)
