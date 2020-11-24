---
title: Note sulla versione di Azure SQL Edge
description: Note sulla versione in cui sono illustrate le novità o le modifiche apportate alle immagini Edge di Azure SQL
keywords: Note sulla versione di SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 11/24/2020
ms.openlocfilehash: bb0c176ee8c145e1268827d79b453628e6f1bc28
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95808615"
---
# <a name="azure-sql-edge-release-notes"></a>Note sulla versione di Azure SQL Edge 

Questo articolo descrive le novità e le modifiche apportate a ogni nuova build di Azure SQL Edge.

## <a name="azure-sql-edge---101"></a>Azure SQL Edge-1.0.1

### <a name="sql-engine-build-number---15020001553"></a>Numero di build del motore SQL: 15.0.2000.1553

### <a name="whats-new"></a>Novità

- Consenti espressione Datebucket definita nelle colonne calcolate

### <a name="fixes"></a>Correzioni

- Correzione dei criteri di conservazione per l'eliminazione di una tabella con criteri di conservazione abilitati con timeout infinito
- Supporto della distribuzione di DacFx per le funzionalità dei criteri di streaming e conservazione 
- Correzione della distribuzione di DacFx per abilitare la distribuzione dalla cartella nidificata nell'URL SAS 
- STIMA della correzione per supportare nomi di colonna lunghi nei messaggi di errore

## <a name="azure-sql-edge---100-rtm"></a>Azure SQL Edge-1.0.0 (RTM)

### <a name="sql-engine-build-number---15020001552"></a>Numero di build del motore SQL: 15.0.2000.1552

### <a name="whats-new"></a>Novità
1. Immagini del contenitore basate su Ubuntu 18,04. 
2. Supporto per `IGNORE NULL` la `RESPECT NULL` sintassi e con le `LAST_VALUE()` `FIRST_VALUE()` funzioni e. 
3. Miglioramenti dell'affidabilità per la stima con ONNX.
4. Supporto per la pulizia basata sui criteri di conservazione dei dati.
   - Supporto del buffer circolare per l'attività di pulizia della conservazione per la risoluzione dei problemi.
5. Supporto per nuove funzionalità 
   - Ripristino rapido
   - Ottimizzazione automatica delle query
   - Abilita scenari di esecuzione parallela
6. Miglioramenti al risparmio energetico per la modalità a basso consumo
7. Supporto per la nuova funzionalità di streaming 
   - [Snapshot Windows](/stream-analytics-query/snapshot-window-azure-stream-analytics) : nuovo tipo di finestra che consente di raggruppare gli eventi che arrivano esattamente allo stesso tempo. 
   - Abilitare [topone](/stream-analytics-query/topone-azure-stream-analytics) e [CollectTop](/stream-analytics-query/collecttop-azure-stream-analytics) come funzione analitica, in questo modo sarà possibile restituire i record ordinati in base alla colonna scelta, senza che sia necessario far parte di una finestra. 
   - Miglioramenti apportati a [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics). 

### <a name="fixes"></a>Correzioni
1. Messaggi di errore aggiuntivi e dettagli per la risoluzione dei problemi relativi alle operazioni di streaming TSQL. 
2. Miglioramenti per la conservazione della durata della batteria in modalità inattiva. 
3. Correzioni del motore di flusso TSQL: 
   - Pulizia per un processo di streaming interrotto 
   - Correzioni per la localizzazione e i miglioramenti della gestione Unicode
   - Migliorare la debugging per lo streaming TSQL di Edge, consentire agli utenti di eseguire query sugli errori di processo da get_streaming_job.
4. Pulizia basata su criteri di conservazione dei dati
   - Correzioni per gli scenari di creazione e pulizia dei criteri di conservazione.
5. Correzioni per le attività del timer in background per migliorare il risparmio energetico per la modalità a basso consumo.

### <a name="known-issues"></a>Problemi noti 
1. Non è possibile usare Date_Bucket funzione T-SQL in una colonna calcolata.


## <a name="ctp-23"></a>CTP 2.3
### <a name="sql-engine-build-number---15020001549"></a>Numero di build del motore SQL: 15.0.2000.1549
### <a name="whats-new"></a>Novità
1. Supporto per le origini personalizzate nella funzione Date_Bucket (). 
2. Supporto per i file BacPac come parte della distribuzione SQL.
3. Supporto per la pulizia basata sui criteri di conservazione dei dati.      
   - Supporto DDL per l'abilitazione dei criteri di conservazione 
   - Stored procedure di pulizia e attività di pulizia in background
   - Eventi estesi per il monitoraggio delle attività di pulizia

### <a name="fixes"></a>Correzioni
1. Messaggi di errore aggiuntivi e dettagli per la risoluzione dei problemi relativi alle operazioni di streaming TSQL. 
2. Miglioramenti per la conservazione della durata della batteria in modalità inattiva. 
3. Correzioni del motore di flusso TSQL: 
   - Correzione del problema relativo alla filigrana bloccata con la finestra di salto in flusso 
   - Correzione della gestione delle eccezioni del Framework per assicurarsi che venga raccolta come errore eseguibile dall'utente


## <a name="ctp-22"></a>CTP 2.2
### <a name="sql-engine-build-number---15020001546"></a>Numero di build del motore SQL: 15.0.2000.1546
### <a name="whats-new"></a>Novità
1. Supporto per i contenitori non radice 
2. Supporto per la raccolta di dati di utilizzo e diagnostica 
3. Aggiornamenti di streaming T-SQL
   - Supporto per i caratteri Unicode per i nomi di oggetti di flusso

### <a name="fixes"></a>Correzioni
1. Aggiornamenti di streaming T-SQL
   - Miglioramenti alla pulizia del processo
   - Miglioramenti della registrazione e della diagnostica
2. Miglioramento delle prestazioni per l'inserimento dei dati

## <a name="ctp-21"></a>CTP 2.1 
### <a name="sql-engine-build-number---15020001545"></a>Numero di build del motore SQL: 15.0.2000.1545
### <a name="fixes"></a>Correzioni
1. Correzione della stima con i modelli ONNX per gestire il problema di CPUID in ARM 
2. Correzione per migliorare la gestione del percorso di errore all'avvio del flusso TSQL 
3. Correggere il valore errato del ritardo della filigrana nelle metriche del processo quando non sono presenti dati. 
4. Risolvere il problema con l'adattatore di output quando l'adapter dispone di uno schema variabile tra i batch.  

## <a name="ctp-20"></a>CTP 2.0 
### <a name="sql-engine-build-number---15020001401"></a>Numero di build del motore SQL: 15.0.2000.1401
### <a name="whats-new"></a>Novità
1.  Nome prodotto aggiornato a' Azure SQL Edge '
1.  Funzione Date_bucket

    i.  Supporto per data, ora, tipo DateTime
3.  PREVEDERE con ONNX
    
    i.  Parametro di RUNTIME necessario per ONNX 
    
4.  Supporto dello streaming TSQL (anteprima limitata) 
 
### <a name="known-issues"></a>Problemi noti

1. <b>Problema:</b> Potenziali errori con l'applicazione di dacpac all'avvio a causa di un problema di temporizzazione.

    <b>Soluzione alternativa:</b> Il riavvio di SQL Server o il contenitore tenterà di applicare il dacpac e dovrebbe risolvere il problema
### <a name="request-support"></a>Richiedi supporto
1. È possibile richiedere supporto nella [pagina del supporto tecnico](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

4. Verificare che siano selezionati i campi seguenti: 
    * Tipo di problema-tecnico 
    * IoT Edge di servizio
    * Tipo di problema: il problema riguarda un modulo IoT Edge
    * Sottotipo di problema-Azure SQL Edge

   ![Ticket di supporto di esempio](media/get-support/support-ticket.png)

## <a name="ctp-15"></a>CTP 1,5
### <a name="sql-engine-build-number---15020001331"></a>Numero di build del motore SQL: 15.0.2000.1331
### <a name="whats-new"></a>Novità
1. Funzione Date_bucket
    
    i. Supporto per il tipo DateTimeOffset
2. PREVEDERE con i modelli ONNX

    i. supporto nvarchar
 
## <a name="ctp-14"></a>CTP 1,4
### <a name="sql-engine-build-number---15020001247"></a>Numero di build del motore SQL: 15.0.2000.1247
### <a name="whats-new"></a>Novità
1.  PREVEDERE con i modelli ONNX
 
    i.  Supporto varchar
    
    ii. Migrazione a ONNX Runtime versione 1,0 
2.  Supporto delle funzionalità: sono abilitate le funzionalità seguenti:

    i.   Supporto CDC

    ii.  Tabella di cronologia con compressione

    iii. Fattore di scala più elevato per Read-ahead di log

    iv.  Distribuzione di modalità batch ES filtro

    v.   Ottimizzazioni Read-Ahead
 
## <a name="ctp-13"></a>CTP 1,3
### <a name="sql-engine-build-number---15020001147"></a>Numero di build del motore SQL: 15.0.2000.1147
### <a name="whats-new"></a>Novità
1. Distribuzione del portale di Azure 

    i.   Supporto per la distribuzione di immagini AMD64 e ARM

    ii.  Supporto per la creazione di processi di streaming

    iii. Distribuzione di dacpac
2. PREVEDERE con i modelli ONNX

    i. Supporto per tipi numerici
3. Supporto delle funzionalità: sono abilitate le funzionalità seguenti:

    i.  Distribuzione aggregazione all'analisi dell'archivio colonne

    ii. Analisi di buon passo
4. Lavoro di riduzione dell'utilizzo di memoria e footprint
