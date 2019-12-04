---
title: Scenari di Query Store-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive alcuni scenari per la Query Store nel database di Azure per PostgreSQL-server singolo.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 31e3f82b6ea1b1fc15c0832dc03edce2a59f1e1b
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74768351"
---
# <a name="usage-scenarios-for-query-store"></a>Scenari di utilizzo per Query Store

**Si applica a:** Database di Azure per PostgreSQL: versioni a server singolo 9,6, 10, 11

È possibile usare Query Store in svariati scenari, in cui è fondamentale garantire prestazioni dei carichi di lavoro prevedibili e tenerne traccia. Si considerino gli esempi seguenti: 
- Identificazione e ottimizzazione delle query con costo più elevato 
- Test A/B 
- Prestazioni stabili durante gli aggiornamenti 
- Identificazione e miglioramento dei carichi di lavoro ad hoc 

## <a name="identify-and-tune-expensive-queries"></a>Identificare e ottimizzare le query con costo elevato 

### <a name="identify-longest-running-queries"></a>Identificare le query in esecuzione da più tempo 
Usare la vista [Informazioni dettagliate prestazioni query](concepts-query-performance-insight.md) nel portale di Azure per identificare rapidamente le query in esecuzione da più tempo. Queste query in genere tendono a consumare una quantità considerevole di risorse. Ottimizzando le query in esecuzione da più tempo, è possibile migliorare le prestazioni liberando risorse che possono essere usate da altre query in esecuzione nel sistema. 

### <a name="target-queries-with-performance-deltas"></a>Identificare le query con valori differenziali nelle prestazioni 
Query Store seziona i dati delle prestazioni in intervalli di tempo che consentono di tenere traccia delle prestazioni di una query nel corso del tempo. In questo modo è possibile identificare esattamente quali query contribuiscono ad aumentare il tempo totale impiegato. Di conseguenza è possibile risolvere in modo mirato i problemi del carico di lavoro.

### <a name="tuning-expensive-queries"></a>Ottimizzazione delle query con costo elevato 
Quando si identifica una query con prestazioni non ottimali, l'azione eseguita dipende dalla natura del problema: 
- Vedere [Raccomandazioni per le prestazioni](concepts-performance-recommendations.md) per determinare se sono presenti indici suggeriti. In caso affermativo, creare l'indice e quindi usare Query Store per valutare le prestazioni delle query dopo aver creato l'indice. 
- Assicurarsi che le statistiche siano aggiornate per le tabelle sottostanti usate dalla query.
- Considerare la possibilità di riscrivere le query con costo elevato. Sfruttare, ad esempio, i vantaggi della parametrizzazione delle query e ridurre l'uso di SQL dinamico. Implementare la logica ottimale durante la lettura dei dati, ad esempio applicando i filtri dei dati sul lato database, non sul lato applicazione. 


## <a name="ab-testing"></a>Test A/B 
Usare Query Store per confrontare le prestazioni del carico di lavoro prima e dopo la modifica di un'applicazione che si intende introdurre. Esempi di scenari per l'uso di Query Store per valutare l'impatto della modifica dell'ambiente o dell'applicazione sulle prestazioni del carico di lavoro: 
- Implementazione di una nuova versione di un'applicazione. 
- Aggiunta di altre risorse al server. 
- Creazione di indici mancanti nelle tabelle a cui fanno riferimento query con costo elevato. 
 
In tutti questi scenari applicare il flusso di lavoro seguente: 
1. Eseguire il carico di lavoro con Query Store prima della modifica pianificata per generare una baseline delle prestazioni. 
2. Applicare le modifiche dell'applicazione in un momento controllato. 
3. Continuare a eseguire il carico di lavoro per il tempo necessario per generare un'immagine delle prestazioni del sistema dopo la modifica. 
4. Confrontare i risultati ottenuti prima e dopo la modifica. 
5. Decidere se mantenere la modifica o eseguire il rollback. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Identificare e migliorare i carichi di lavoro ad hoc 
Alcuni carichi di lavoro non includono query dominanti che è possibile ottimizzare per migliorare le prestazioni complessive dell'applicazione. Questi carichi di lavoro sono in genere caratterizzati da un numero relativamente elevato di query univoche, ognuna delle quali consuma una parte delle risorse di sistema. Ogni query univoca viene eseguita raramente, quindi il consumo individuale del runtime non è critico. D'altra parte, dato che l'applicazione genera continuamente nuove query, una parte significativa delle risorse di sistema viene impiegata per la compilazione delle query, ma ciò non è l'ideale. In genere, questa situazione si verifica se l'applicazione genera query (invece di usare le stored procedure o le query con parametri) o se si basa su framework di mapping relazionale a oggetti che generano query per impostazione predefinita. 
 
Se si ha il controllo del codice dell'applicazione, considerare la possibilità di riscrivere il livello di accesso ai dati per usare le stored procedure o le query con parametri. Tuttavia, questa situazione può essere anche migliorata senza modifiche all'applicazione, forzando la parametrizzazione delle query per l'intero database (tutte le query) o per i singoli modelli di query con lo stesso hash di query. 

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [procedure consigliate per usare Query Store](concepts-query-store-best-practices.md)