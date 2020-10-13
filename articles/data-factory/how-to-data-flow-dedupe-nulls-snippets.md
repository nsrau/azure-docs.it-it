---
title: Deduplicare le righe e trovare valori null usando frammenti di flusso di dati
description: Informazioni su come deduplicare facilmente le righe e individuare i valori null usando frammenti di codice nei flussi di dati
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: makromer
ms.openlocfilehash: 841484a647d2737d621c75ebe63f65f2de829a26
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91666501"
---
# <a name="dedupe-rows-and-find-nulls-using-data-flow-snippets"></a>Deduplicare le righe e trovare valori null usando frammenti di flusso di dati

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilizzando frammenti di codice nel mapping di flussi di dati, è possibile eseguire in modo semplice attività comuni come Deduplicazione dati e filtro null. Questa guida dettagliata illustra come aggiungere le funzioni alle pipeline in modo molto semplice usando frammenti di script del flusso di dati.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>Creare una pipeline

1. Selezionare **+ nuova pipeline** per creare una nuova pipeline.

2. Aggiungere un'attività flusso di dati.

3. Aggiungere una trasformazione di origine e connetterla a uno dei set di dati

    ![Frammento di codice sorgente 2](media/data-flow/snippet-adf-2.png)

4. I frammenti di codice per la deduplicazione e il controllo NULL utilizzano modelli generici che sfruttano la deriva dello schema del flusso di dati in modo che funzionino con qualsiasi schema del set di dati o con set di dati che non dispongono di uno schema predefinito.

5. [Passare alla pagina della documentazione dello script del flusso di dati e copiare il frammento di codice per le righe distinte.](https://docs.microsoft.com/azure/data-factory/data-flow-script#distinct-row-using-all-columns)

6. Nell'interfaccia utente della finestra di progettazione del flusso di dati fare clic sul pulsante script in alto a destra per aprire l'editor di script dietro il grafico del flusso di dati.

    ![Frammento di codice sorgente 3](media/data-flow/snippet-adf-3.png)

7. Dopo la definizione di ```source1``` nello script, premere invio e quindi incollare il frammento di codice.

8. Questo frammento di codice incollato verrà connesso alla trasformazione origine precedente creata nel grafico digitando "source1" prima del codice incollato.

9. In alternativa, è possibile connettere la nuova trasformazione nella finestra di progettazione selezionando il flusso in ingresso dal nuovo nodo trasformazione del grafico.

    ![Frammento di origine 4](media/data-flow/snippet-adf-4.png)

10. A questo punto, il flusso di dati rimuoverà le righe duplicate dall'origine utilizzando la trasformazione aggregazione che raggruppa per tutte le righe utilizzando un hash generale in tutti i valori di colonna.
    
11. Successivamente, verrà aggiunto un frammento di codice per suddividere i dati in un flusso che contiene le righe con valori NULL e un flusso privo di valori NULL.

12. [Tornare alla libreria dei frammenti. questa volta copiare il codice per i controlli NULL.](https://docs.microsoft.com/azure/data-factory/data-flow-script#check-for-nulls-in-all-columns)

13. Nella finestra di progettazione del flusso di dati, fare di nuovo clic su script e incollare questo nuovo codice di trasformazione nella parte inferiore, connetterlo alla trasformazione precedente digitando il nome della trasformazione davanti al frammento incollato.

14. Il grafico del flusso di dati dovrebbe ora essere simile al seguente:

    ![Frammento di codice sorgente 1](media/data-flow/snippet-adf-1.png)

  A questo punto si dispone di un flusso di dati funzionante con controlli deduping e NULL generici, prendendo i frammenti di codice esistenti dalla libreria di script del flusso di dati e aggiungendoli alla progettazione esistente.

## <a name="next-steps"></a>Passaggi successivi

* Compilare il resto della logica del flusso di dati tramite il mapping delle [trasformazioni](concepts-data-flow-overview.md)dei flussi di dati.
