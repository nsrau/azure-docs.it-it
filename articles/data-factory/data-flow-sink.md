---
title: Trasformazione sink del Flusso di dati di mapping di Azure Data Factory
description: Trasformazione sink del Flusso di dati di mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 381dc2f9f6d3a074af00ba047472719c086f5811
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408409"
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

### <a name="output-settings"></a>Impostazioni di output

L'azione di sovrascrivere troncherà la tabella se esistente, per poi ricrearla e caricare i dati. L'azione di accodare consentirà di inserire nuove righe. Se la tabella dal nome della tabella di set di dati non esiste nell'ADW di destinazione, il Flusso di dati creerà la tabella e successivamente caricherà i dati.

Se si deseleziona "Mapping automatico", è possibile eseguire manualmente il mapping dei campi nella tabella di destinazione.

![Opzioni di ADW sink](media/data-flow/adw2.png "adw sink")

#### <a name="field-mapping"></a>Mapping dei campi

Nella scheda Mapping della Trasformazione sink, è possibile eseguire il mapping delle colonne in ingresso (lato sinistro) nella destinazione (lato destro). Quando si esegue il sink dei flussi di dati nei file, Azure Data Factory scriverà sempre nuovi file in una cartella. Quando si esegue il mapping in un set di dati del database, è possibile scegliere di generare una nuova tabella con questo schema (impostare Salva criteri di Azure per "sovrascrivere") o inserire nuove righe in una tabella esistente ed eseguire il mapping di campi in uno schema esistente.

È possibile usare una selezione multipla nella tabella di mapping per collegare più colonne con un solo clic, scollegare più colonne o eseguire il mapping di più righe per lo stesso nome della colonna.

![Mapping dei campi](media/data-flow/multi1.png "più opzioni")

Se si vuole reimpostare il mapping delle colonne, fare clic sul pulsante "Nuovo mapping" per reimpostare il mapping.

![Connessioni](media/data-flow/maxcon.png "Connessioni")

### <a name="updates-to-sink-transformation-for-adf-v2-ga-version"></a>Aggiornamenti per la Trasformazione sink per la versione V2 GA di Azure Data Factory

![Opzioni di sink](media/data-flow/sink1.png "Sink 1")

![Opzioni di sink](media/data-flow/sink2.png "Sink")

* Le opzioni Consenti deviazione schema e Convalida schema sono ora disponibili nel Sink. Ciò permette di inviare una richiesta ad Azure Data Factory per accettare completamente le definizioni dello schema (Deviazione schema) o fallire il Sink se lo schema cambia (Convalida schema).

* Cancellare la cartella. Azure Data Factory troncherà il contenuto della cartella sink, prima di scrivere i file di destinazione nella cartella di destinazione.

* Opzioni di nomi di file

   * Predefinito: consentire a Spark di denominare i file basati sulle impostazioni predefinite di PART
   * Criterio: immettere un nome per i file di output
   * Per ogni partizione: immettere un nome di file per ogni partizione
   * Dati nella colonna: impostare il file di output per il valore di una colonna

> [!NOTE]
> Le operazioni sui file verranno eseguite solo quando l'attività di esecuzione del Flusso di dati è in corso, non in modalità Debug del Flusso di dati

Con i tipi di sink SQL, è possibile impostare:

* Tronca tabella
* Ricrea tabella (esegue rilascia/crea)
* Dimensione batch per i caricamenti di dati di grandi dimensioni. Immettere un numero di scritture di bucket in blocchi.

![Mapping dei campi](media/data-flow/sql001.png "Opzioni SQL")

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato creato il flusso di dati, aggiungere un'[attività di esecuzione del Flusso di dati alla pipeline](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-overview).
