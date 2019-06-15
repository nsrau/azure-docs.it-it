---
title: Configurare una trasformazione sink nella funzionalità di Mapping del flusso di dati di Azure Data Factory
description: Informazioni su come configurare una trasformazione sink nel Mapping di flusso di dati.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 4341cbb0e24330d535f5211c088f0068eab33af7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65596273"
---
# <a name="sink-transformation-for-a-data-flow"></a>Trasformazione per un flusso di dati di sink

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Dopo aver trasformato il flusso di dati, è possibile includere i dati in un set di dati di destinazione. Nella trasformazione sink, scegliere una definizione di set di dati per i dati di output di destinazione. Può avere molti sink trasformazioni come flusso di dati richiesto.

All'account per la deviazione schema e le modifiche nei dati in ingresso, elaborare i dati di output in una cartella senza uno schema definito nel set di dati di output. È possibile inoltre in considerazione per la modifica delle colonne delle origini selezionando **consentire deviazione schema** nell'origine. Quindi Esegui mapping automatico di tutti i campi nel sink.

![Le opzioni della scheda Sink, tra cui l'opzione Auto Map](media/data-flow/sink1.png "sink 1")

Per elaborare tutti i campi in ingresso, attivare **mapping automatico**. Per scegliere i campi al sink nella destinazione o per modificare i nomi dei campi nella destinazione, la disattivazione **mapping automatico**. Quindi aprire il **Mapping** pressione di tab per eseguire il mapping di campi di output.

![Le opzioni nella scheda Mapping](media/data-flow/sink2.png "sink 2")

## <a name="output"></a>Output 
Per i tipi di sink di archivio Data Lake o archiviazione Blob di Azure di output i dati trasformati in una cartella. Spark genera file di dati di output partizionato basati sullo schema di partizionamento utilizzato dalla trasformazione sink. 

È possibile impostare lo schema di partizionamento dal **Ottimizza** scheda. Se si desidera che Data Factory per unire l'output in un singolo file, selezionare **singola partizione**.

![Le opzioni nella scheda Ottimizza](media/data-flow/opt001.png "sink opzioni")

## <a name="field-mapping"></a>Mapping campi

Nel **Mapping** scheda della trasformazione del sink, è possibile mappare le colonne in ingresso a sinistra per le destinazioni a destra. Quando si sink flussi di dati per il file, Data Factory verrà sempre in grado di scrivere i nuovi file in una cartella. Quando esegue il mapping a un set di dati di database, è possibile generare una nuova tabella che utilizza questo schema impostando **Salva criterio** al **Sovrascrivi**. O inserire nuove righe in una tabella esistente e quindi eseguire il mapping campi allo schema esistente. 

![Scheda Mapping](media/data-flow/sink2.png "sink")

Nella tabella di mapping, è possibile selezione multipla per collegare più colonne, vengono scollegate le più colonne o eseguire il mapping di più righe per lo stesso nome di colonna.

Eseguire il mapping sempre il set di campi in ingresso a una destinazione non appena vengono e di accettare completamente le definizioni di schema flessibile, selezionare **consentire deviazione schema**.

![Scheda Mapping, che mostra i campi mappati alle colonne nel set di dati](media/data-flow/multi1.png "più opzioni")

Per reimpostare i mapping delle colonne, selezionare **rimappare**.

![La scheda di Sink](media/data-flow/sink1.png "un Sink")

Selezionare **schema di convalida** per eseguire il sink se viene modificato lo schema.

Selezionare **cancellare la cartella** per troncare il contenuto della cartella sink prima di scrivere i file di destinazione in tale cartella di destinazione.

## <a name="file-name-options"></a>Opzioni di nomi di file

Impostare la denominazione dei file: 

   * **Predefinita**: Consenti Spark per denominare i file basati sulle impostazioni predefinite di parte.
   * **Modello**: Immettere un modello per i file di output. Ad esempio, **loans [n]** creerà loans1.csv loans2.csv e così via.
   * **Per ogni partizione**: Immettere un nome di file per ogni partizione.
   * **I dati nella colonna**: Impostare il file di output per il valore di una colonna.
   * **L'output in un singolo file**: Con questa opzione, Azure Data factory combinerà i file di output partizionato in un unico file denominato. Per usare questa opzione, il set di dati deve risolvere un nome di cartella. Inoltre, tenere presente che questa operazione di unione può verificarsi un errore in base alla dimensione del nodo.

> [!NOTE]
> Avvio di operazioni di file solo quando si esegue l'attività di esecuzione del flusso di dati. Non vengano avviati in modalità di flusso di Debug dei dati.

## <a name="database-options"></a>Opzioni di database

Scegliere le impostazioni del database:

* **Aggiornare il metodo**: Il valore predefinito è per consentire gli inserimenti. Deselezionare **Consenti insert** se si desidera arrestare l'inserimento di nuove righe provenienti dall'origine. Per aggiornare, upsert, o eliminare le righe, innanzitutto aggiungere una trasformazione Modifica righe alle righe di tag per le azioni. 
* **Ricrea tabella**: Eliminare o creare la tabella di destinazione prima di completamento del flusso di dati.
* **Istruzione TRUNCATE table**: Rimuovere tutte le righe dalla tabella di destinazione prima al termine del flusso di dati.
* **Dimensioni batch**: Immettere un numero di scritture di bucket in blocchi. Usare questa opzione per carichi di dati di grandi dimensioni. 
* **Abilitare la gestione temporanea**: Usare PolyBase durante il caricamento di Azure Data Warehouse come set di dati sink.

![La scheda Impostazioni, che mostra le opzioni del sink SQL](media/data-flow/alter-row2.png "opzioni SQL")

> [!NOTE]
> Nel flusso di dati, è possibile indirizzare Data Factory per creare una nuova definizione di tabella nel database di destinazione. Per creare la definizione della tabella, impostare un set di dati nella trasformazione sink con un nuovo nome di tabella. Nel set di dati SQL, sotto il nome della tabella, selezionare **modifica** e immettere un nuovo nome di tabella. Quindi, nella trasformazione sink, attivare **consentire deviazione schema**. Impostare **Importa schema** al **None**.

![Impostazioni di set di dati SQL, che mostra dove modificare il nome della tabella](media/data-flow/dataset2.png "Schema SQL")

> [!NOTE]
> Quando si aggiornano o eliminano righe nel sink di database, è necessario impostare la colonna chiave. Questa impostazione consente la trasformazione Modifica righe determinare la riga univoca nella libreria di spostamento dei dati (DML).

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato creato il flusso di dati, aggiungere un [attività di flusso di dati per la pipeline](concepts-data-flow-overview.md).
