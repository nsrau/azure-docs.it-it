---
title: Deduplicare le righe e individuare i valori null usando frammenti del flusso di dati
description: Informazioni su come deduplicare facilmente le righe e individuare i valori null usando frammenti di codice nei flussi di dati
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: makromer
ms.openlocfilehash: 1c630cdd66fa4f8e609524feb9c3f0bcad9711a0
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92458167"
---
# <a name="dedupe-rows-and-find-nulls-by-using-data-flow-snippets"></a>Deduplicare le righe e individuare i valori null usando frammenti del flusso di dati

[!INCLUDE [appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilizzando i frammenti di codice nel mapping di flussi di dati, è possibile eseguire facilmente attività comuni quali la deduplicazione dei dati e il filtro null. Questo articolo illustra come aggiungere facilmente tali funzioni alle pipeline usando i frammenti di codice di script del flusso di dati.
<br>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>Creare una pipeline

1. Selezionare **nuova pipeline**.

1. Aggiungere un'attività flusso di dati.

1. Selezionare la scheda **impostazioni di origine** , aggiungere una trasformazione origine, quindi connetterla a uno dei set di dati.

    ![Screenshot del riquadro "impostazioni di origine" per l'aggiunta di un tipo di origine.](media/data-flow/snippet-adf-2.png)

    I frammenti di codice deduplicati e di controllo null utilizzano modelli generici che sfruttano i vantaggi della deriva dello schema del flusso di dati. I frammenti di codice funzionano con qualsiasi schema del set di dati oppure con i set di dati che non dispongono di uno schema predefinito.

1. Nella sezione "DISTINCT row using all Columns" dello [script del flusso di dati (DFS)](https://docs.microsoft.com/azure/data-factory/data-flow-script#distinct-row-using-all-columns)copiare il frammento di codice per DistinctRows.

1. Nell'interfaccia utente della finestra di progettazione del flusso di dati, selezionare il pulsante **script** in alto a destra per aprire l'editor di script dietro il grafico del flusso di dati.

    ![Screenshot di un frammento di codice sorgente.](media/data-flow/snippet-adf-3.png)

1. Nello script, dopo la definizione per `source1` , premere invio e quindi incollare il frammento di codice.

1. Eseguire una delle operazioni seguenti:

   * Connettere il frammento di codice incollato alla trasformazione di origine creata in precedenza nel grafico digitando **source1** davanti al codice incollato.

   * In alternativa, è possibile connettere la nuova trasformazione nella finestra di progettazione selezionando il flusso in ingresso dal nuovo nodo trasformazione del grafico.

     ![Screenshot del riquadro "impostazioni Suddivisione condizionale".](media/data-flow/snippet-adf-4.png)

   A questo punto, il flusso di dati rimuoverà le righe duplicate dall'origine utilizzando la trasformazione aggregazione, che raggruppa per tutte le righe utilizzando un hash generale in tutti i valori di colonna.
    
1. Aggiungere un frammento di codice per suddividere i dati in un flusso che contiene righe con valori null e un altro flusso senza valori null. A tale scopo, procedere come indicato di seguito:

   a. Tornare alla libreria dei [frammenti](https://docs.microsoft.com/azure/data-factory/data-flow-script#check-for-nulls-in-all-columns), quindi copiare il codice per i controlli null.

   b. Nella finestra di progettazione del flusso di dati selezionare di nuovo **script** , quindi incollare questo nuovo codice di trasformazione in basso. Questa azione connette lo script alla trasformazione precedente inserendo il nome della trasformazione prima del frammento incollato.

   Il grafico del flusso di dati dovrebbe ora essere simile al seguente:

    ![Screenshot del grafico del flusso di dati.](media/data-flow/snippet-adf-1.png)

  A questo punto è stato creato un flusso di dati funzionante con controlli deduping e null generici, prendendo i frammenti di codice esistenti dalla libreria di script del flusso di dati e aggiungendoli alla progettazione esistente.

## <a name="next-steps"></a>Passaggi successivi

* Compilare il resto della logica del flusso di dati tramite il mapping delle [trasformazioni](concepts-data-flow-overview.md)dei flussi di dati.
