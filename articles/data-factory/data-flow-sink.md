---
title: Trasformazione sink del Flusso di dati di mapping di Azure Data Factory
description: Trasformazione sink del Flusso di dati di mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 3829fb3c045b149552d3f022e31f30f9cfae8182
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57852441"
---
# <a name="mapping-data-flow-sink-transformation"></a>Trasformazione sink del Flusso di dati di mapping

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Opzioni di sink](media/data-flow/windows1.png "sink 1")

Dopo aver completato la trasformazione del flusso di dati, è possibile effettuare il sink dei propri dati trasformati, in un set di dati di destinazione. Nella Trasformazione sink, è possibile scegliere la definizione del set di dati che si vuole usare per i dati di output di destinazione. Si possono eseguire tante Trasformazioni sink quante richieste dal flusso di dati.

Una pratica comune, per tenere conto della modifica dei dati in ingresso e della deviazione schema, è quella di eseguire il sink dei dati di output in una cartella senza uno schema definito nel set di dati di output. È inoltre possibile giustificare le modifiche di tutte le colonne nelle proprie origini, selezionando "Consenti deviazione schema" nell'Origine e successivamente eseguire il mapping automatico di tutti i campi nel Sink.

Si può scegliere di sovrascrivere, accodare o interrompere il flusso di dati durante il sink di un set di dati.

È anche possibile scegliere "Mapping automatico" per elaborare tutti i campi in ingresso. Se si scelgono i campi in cui si vuole effettuare il sink nella destinazione o si vogliono modificare i nomi dei campi nella destinazione, scegliere "Disattivato" per "Mapping automatico" e fare clic sulla scheda Mapping per eseguire il mapping dei campi di output:

![Opzioni di sink](media/data-flow/sink2.png "sink 2")

## <a name="output-to-one-file"></a>Output in un solo file
Per i tipi di sink BLOB del servizio di archiviazione di Azure o Data Lake, si potranno ottenere i dati trasformati in una cartella. Spark genererà file di dati di output partizionati, in base allo schema di partizione usato nella Trasformazione sink. È possibile impostare lo schema di partizione facendo clic sulla scheda "Ottimizza". Se si vuole che Azure Data Factory unisca l'output in un singolo file, fare clic sul pulsante di opzione "Partizione singola".

![Opzioni di sink](media/data-flow/opt001.png "opzioni di sink")

## <a name="field-mapping"></a>Mapping campi

Nella scheda Mapping della Trasformazione sink, è possibile eseguire il mapping delle colonne in ingresso (lato sinistro) nella destinazione (lato destro). Quando si esegue il sink dei flussi di dati nei file, Azure Data Factory scriverà sempre nuovi file in una cartella. Quando si esegue il mapping in un set di dati del database, è possibile scegliere di generare una nuova tabella con questo schema (impostare Salva criteri di Azure per "sovrascrivere") o inserire nuove righe in una tabella esistente ed eseguire il mapping di campi in uno schema esistente.

È possibile usare una selezione multipla nella tabella di mapping per collegare le colonne di più con un solo clic, vengono scollegate le più colonne o eseguire il mapping di più righe per lo stesso nome di colonna.

Quando si vuole eseguire sempre richiedere il set di campi in ingresso ed eseguirne il mapping a una destinazione come-, impostare l'impostazione "Consenti deviazioni dello Schema".

![Mapping dei campi](media/data-flow/multi1.png "più opzioni")

Se si vuole reimpostare il mapping delle colonne, fare clic sul pulsante "Nuovo mapping" per reimpostare il mapping.

![Opzioni di sink](media/data-flow/sink1.png "Sink 1")

![Opzioni di sink](media/data-flow/sink2.png "Sink")

* Le opzioni Consenti deviazione schema e Convalida schema sono ora disponibili nel Sink. Ciò permette di inviare una richiesta ad Azure Data Factory per accettare completamente le definizioni dello schema (Deviazione schema) o fallire il Sink se lo schema cambia (Convalida schema).

* Cancellare la cartella. Azure Data Factory troncherà il contenuto della cartella sink, prima di scrivere i file di destinazione nella cartella di destinazione.

## <a name="file-name-options"></a>Opzioni di nomi di file

   * Predefinito: consentire a Spark di denominare i file basati sulle impostazioni predefinite di PART
   * Criterio: immettere un nome per i file di output
   * Per ogni partizione: immettere un nome di file per ogni partizione
   * Dati nella colonna: impostare il file di output per il valore di una colonna

> [!NOTE]
> Le operazioni sui file verranno eseguite solo quando l'attività di esecuzione del Flusso di dati è in corso, non in modalità Debug del Flusso di dati

## <a name="database-options"></a>Opzioni di database

* Consenti insert, update, delete, esegue l'Upsert. Il valore predefinito è per consentire gli inserimenti. Se si desidera righe insert, upsert o aggiornamento, è necessario aggiungere innanzitutto una trasformazione di riga alter alle righe di tag per le azioni specifiche.
* Istruzione TRUNCATE table (rimuove tutte le righe dalla tabella di destinazione prima di completare il flusso di dati)
* Ricreare la tabella (eseguita creano/eliminano della tabella di destinazione prima di completare il flusso di dati)
* Dimensione batch per i caricamenti di dati di grandi dimensioni. Immettere un numero di scritture di bucket in blocchi
* Abilitare la gestione temporanea: Ciò indicherà a Azure Data factory per usare Polybase durante il caricamento di Azure Data Warehouse come set di dati sink

![Opzioni di SQL Sink](media/data-flow/alter-row2.png "opzioni SQL")

> [!NOTE]
> Durante l'aggiornamento o eliminazione di righe nel sink di database, è necessario impostare la colonna chiave. In questo modo, Alter riga è in grado di determinare la riga univoca nel DML.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato creato il flusso di dati, aggiungere un'[attività di esecuzione del Flusso di dati alla pipeline](concepts-data-flow-overview.md).
