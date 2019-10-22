---
title: 'Importa da URL Web tramite HTTP: informazioni di riferimento sui moduli'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare l'importazione da URL Web tramite il modulo HTTP nel servizio Azure Machine Learning per leggere i dati da una pagina Web pubblica da usare in una pipeline di machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: e2521dabdab8e9365019f35514f2d8d235c9c014
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693128"
---
# <a name="import-from-web-url-via-http-module"></a>Importa da URL Web tramite modulo HTTP

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Usare questo modulo per leggere i dati da una pagina Web pubblica da usare in una pipeline di machine learning.

Ai dati pubblicati in una pagina Web si applicano le restrizioni seguenti:

- I dati devono essere in uno dei formati supportati: CSV, TSV, ARFF o SvmLight. Gli altri dati provocheranno errori.
- Nessuna autenticazione richiesta o supportata. I dati devono essere disponibili pubblicamente. 

Esistono due modi per ottenere i dati: usare la procedura guidata per configurare l'origine dati o configurarla manualmente.

## <a name="use-the-data-import-wizard"></a>Utilizzare l'importazione guidata dati

1. Aggiungere il modulo **Import Data (Importa dati** ) alla pipeline. È possibile trovare il modulo nell'interfaccia, nella categoria **input e output dei dati** .

2. Fare clic su **Avvia importazione guidata dati** e selezionare URL Web tramite http.

3. Incollare l'URL e selezionare un formato dati.

4. Al termine della configurazione.

Per modificare una connessione dati esistente, avviare di nuovo la procedura guidata. La procedura guidata carica tutti i dettagli di configurazione precedenti in modo da non dover riavviarsi da zero

## <a name="manually-set-properties-in-the-import-data-module"></a>Impostare manualmente le proprietà nel modulo Import Data

Nei passaggi seguenti viene descritto come configurare manualmente l'origine di importazione.

1. Aggiungere il modulo [Import Data (Importa dati](import-data.md) ) alla pipeline. È possibile trovare il modulo nell'interfaccia, nella categoria **input e output dei dati** .

2. Per **origine dati**selezionare **URL Web tramite http**.

3. Per **URL**Digitare o incollare l'URL completo della pagina che contiene i dati che si desidera caricare.

    L'URL deve includere l'URL del sito e il percorso completo, con il nome e l'estensione del file, alla pagina che contiene i dati da caricare.

    La pagina seguente, ad esempio, contiene il set di dati Iris del repository di Machine Learning della University of California, Irvine:

    `https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data`

4. Per **formato dati**selezionare uno dei formati di dati supportati dall'elenco.

    È consigliabile controllare sempre i dati prima di determinare il formato. La pagina UC Irvine usa il formato CSV. Altri formati di dati supportati sono TSV, ARFF e SvmLight.

5. Se i dati sono in formato CSV o TSV, utilizzare l'opzione **file con riga di intestazione** per indicare se i dati di origine includono o meno una riga di intestazione. La riga di intestazione viene utilizzata per assegnare i nomi di colonna.

6. Selezionare le opzioni **USA risultati memorizzati nella cache** se non si prevede che i dati cambino molto o se si vuole evitare di ricaricare i dati ogni volta che si esegue la pipeline.

    Quando questa opzione è selezionata, la pipeline carica i dati la prima volta che il modulo viene eseguito e successivamente usa una versione memorizzata nella cache del set di dati.

    Se si vuole ricaricare il set di dati in ogni iterazione del set di dati della pipeline, deselezionare l'opzione **USA risultati memorizzati nella cache** . I risultati vengono ricaricati anche se sono state apportate modifiche ai parametri dei [dati di importazione](import-data.md).

7. Eseguire la pipeline.

## <a name="results"></a>Risultati

Al termine, fare clic sul set di dati di output e selezionare **Visualizza** per verificare se i dati sono stati importati correttamente.


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 