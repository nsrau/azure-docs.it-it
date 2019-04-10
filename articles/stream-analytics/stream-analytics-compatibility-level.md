---
title: Comprendere il livello di compatibilità per i processi di Analisi di flusso di Azure
description: Informazioni su come impostare un livello di compatibilità per un processo di Analisi di flusso di Azure e modifiche sostanziali nel livello di compatibilità più recente
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 6fb93152263d253de983b17d25f02f4c68a172fd
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361398"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Livello di compatibilità per i processi di Analisi di flusso di Azure
 
Il livello di compatibilità fa riferimento ai comportamenti specifici di versione di un servizio Analisi di flusso di Azure. Analisi di flusso di Azure è un servizio gestito con regolari aggiornamenti delle funzionalità e miglioramenti delle prestazioni. In genere, gli aggiornamenti vengono resi disponibili agli utenti finali in modo automatico. Alcune nuove funzionalità, tuttavia, possono introdurre modifiche sostanziali, ad esempio al comportamento di un processo esistente, ai processi che usano i dati di questi processi e così via. Un livello di compatibilità consente di rappresentare una modifica sostanziale introdotta in Analisi di flusso. Le modifiche sostanziali vengono sempre introdotte con un nuovo livello di compatibilità. 

Livello di compatibilità garantisce che i processi esistenti vengono eseguiti senza alcun errore. Quando si crea un nuovo processo di Stream Analitica, è consigliabile crearla usando il livello di compatibilità più recente. 
 
## <a name="set-a-compatibility-level"></a>Impostare un livello di compatibilità 

Il livello di compatibilità controlla il comportamento di runtime di un processo di Analisi di flusso. È possibile impostare il livello di compatibilità per un processo di Analisi di flusso usando il portale o la [chiamata all'API REST per la creazione di un processo](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Analisi di flusso di Azure supporta attualmente due livelli di compatibilità: "1.0" e "1.1". Per impostazione predefinita, il livello di compatibilità è impostato su "1.0", introdotto durante la disponibilità generale di Analisi di flusso di Azure. Per aggiornare il valore predefinito, passare al processo di Analisi di flusso esistente, selezionare l'opzione **Livello di compatibilità** nella sezione **Configura** e modificare il valore. 

Non dimenticare di arrestare il processo prima di aggiornare il livello di compatibilità. Il livello di compatibilità, infatti, non può essere aggiornato se il processo è in esecuzione. 

![Livello di compatibilità di Analisi di flusso nel portale di Azure](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

 
Quando si aggiorna il livello di compatibilità, il servizio di compilazione Transact-SQL convalida il processo con la sintassi corrispondente al livello di compatibilità selezionato. 

## <a name="major-changes-in-the-latest-compatibility-level-12"></a>Importanti modifiche nel livello di compatibilità più recente (1.2)

Nel livello di compatibilità 1.2 vengono introdotte le modifiche principali seguenti:

### <a name="geospatial-functions"></a>Funzioni geospaziali 

**versioni precedenti:** Azure Stream Analitica utilizzati calcoli geografici.

**versione corrente:** Azure Stream Analitica consente di calcolare le coordinate geografiche proiettata geometrica. Non sussiste alcuna modifica della firma delle funzioni geospaziali. Tuttavia, presentano una semantica è leggermente diversa, che consente di calcolo più preciso rispetto a prima.

Azure Stream Analitica supporta l'indicizzazione dei dati di geospaziale riferimento. Per un calcolo di join più veloce è possibile indicizzare i dati di riferimento che contiene elementi geospaziali.

Le funzioni geospaziali aggiornato portare l'espressività completa del formato geospaziali Well-Known Text (WKT). È possibile specificare altri componenti geospaziali che in precedenza non erano supportate con GeoJson.

Per altre informazioni, vedere [gli aggiornamenti alle funzionalità geospaziale in Azure Stream Analitica – Cloud e IoT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Esecuzione di query parallele per le origini di input con più partizioni 

**versioni precedenti:** Query Analitica Stream Azure richiesto l'utilizzo della clausola PARTITION BY per parallelizzare l'elaborazione di query tra partizioni di origine di input.

**versione corrente:** Se per la logica di query può essere parallelizzata tra le partizioni di origine di input, Azure Stream Analitica crea istanze di query separata ed esegue i calcoli in parallelo.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Integrazione di API Bulk nativa con l'output di COSMOS DB

**versioni precedenti:** È stato il comportamento di upsert *insert o merge*.

**versione corrente:** Integrazione di API Bulk nativa con l'output di COSMOS DB ottimizza la velocità effettiva e gestisce in modo efficiente la limitazione delle richieste.

È il comportamento di upsert *inserire o sostituire*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset durante la scrittura in output SQL

**versioni precedenti:** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) tipi sono stati modificati in formato UTC.

**versione corrente:** DateTimeOffset non è più viene modificato.

### <a name="strict-validation-of-prefix-of-functions"></a>Convalida di tipo Strict del prefisso delle funzioni

**versioni precedenti:** Si è verificato alcun convalida di tipo strict dei prefissi di funzione.

**versione corrente:** Azure Stream Analitica dispone di una convalida rigorosa di prefissi (funzione). Aggiunta di un prefisso a una funzione predefinita genera un errore. Ad esempio,`myprefix.ABS(…)` non è supportato.

Aggiunta di un prefisso per le aggregazioni incorporate comporta anche in errore. Ad esempio, `myprefix.SUM(…)` non è supportato.

Usando il prefisso "system" per i risultati di funzioni definite dall'utente in errore.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Non consentire di matrice e oggetto come proprietà chiave nell'adattatore di output di Cosmos DB

**versioni precedenti:** Tipi di matrice e oggetto erano supportati come proprietà chiave.

**versione corrente:** Tipi di matrice e oggetto non sono supportati non più come proprietà chiave.


## <a name="next-steps"></a>Passaggi successivi
* [Risolvere i problemi di input di Azure Stream Analitica](stream-analytics-troubleshoot-input.md)
* [Integrità risorse di Analitica di Stream](stream-analytics-resource-health.md)
