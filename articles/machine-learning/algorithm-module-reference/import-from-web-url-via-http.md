---
title: 'Importare da URL Web tramite HTTP: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare l'importazione dall'URL Web tramite il modulo HTTP nel servizio Azure Machine Learning per leggere i dati da una pagina Web pubblica per l'uso in un esperimento di machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2f0847e9dd90267d985b75be3c3a07ce8fae98a9
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029671"
---
# <a name="import-from-web-url-via-http-module"></a>Importare da URL Web tramite il modulo HTTP

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per leggere i dati da una pagina Web pubblica per l'uso in un esperimento di machine learning.

Le restrizioni seguenti si applicano ai dati pubblicati in una pagina web:

- Dati devono essere in uno dei formati supportati: CSV, TSV, ARFF o SvmLight. Altri dati verranno restituiti errori.
- L'autenticazione non è richiesto o supportata. I dati devono essere disponibili pubblicamente. 

Esistono due modi per ottenere i dati: usare la procedura guidata per configurare l'origine dati oppure configurarlo manualmente.

## <a name="use-the-data-import-wizard"></a>Utilizzare l'importazione guidata dati

1. Aggiungere il **Import Data** modulo nell'esperimento. È possibile trovare il modulo nell'interfaccia nel **Data Input and Output** categoria.

2. Fare clic su **avviare Importazione guidata dei dati** e selezionare un URL Web tramite HTTP.

3. Incollare l'URL e selezionare un formato di dati.

4. Configurazione quando viene completata.

Per modificare una connessione dati esistente, avviare nuovamente la procedura guidata. La procedura guidata carica tutti i dettagli di configurazione precedente in modo che non è necessario ricominciare da zero

## <a name="manually-set-properties-in-the-import-data-module"></a>Impostare manualmente le proprietà nel modulo Import Data

I passaggi seguenti descrivono come configurare l'origine di importazione.

1. Aggiungere il [Import Data](import-data.md) modulo nell'esperimento. È possibile trovare il modulo nell'interfaccia nel **Data Input and Output** categoria.

2. Per la **zdroj dat**, selezionare **URL Web tramite HTTP**.

3. Per la **URL**digitare o incollare l'URL completo della pagina che contiene i dati da caricare.

    L'URL deve includere l'URL del sito e il percorso completo, con nome e l'estensione, la pagina che contiene i dati da caricare.

    Ad esempio, la pagina seguente contiene il set di dati Iris da di machine learning repository della University of California, Irvine:

    `http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data`

4. Per la **formato dati**, selezionare uno dei dati supportati nei formati dall'elenco.

    È consigliabile controllare sempre i dati in anticipo per determinare il formato. La pagina UC Irvine utilizza il formato CSV. Altri formati di dati supportati sono TSV, ARFF e SvmLight.

5. Se i dati sono in formato CSV o TSV, usare il **File con riga di intestazione** opzione per indicare se i dati di origine includono una riga di intestazione. Consente di assegnare i nomi delle colonne della riga di intestazione.

6. Selezionare il **Usa nella cache i risultati** opzioni se si ritiene che i dati da molte modifiche, o se si desidera evitare di ricaricare i dati ogni volta che si esegue l'esperimento.

    Quando questa opzione è selezionata, l'esperimento viene caricato il tempo data il primo modulo viene eseguito e in seguito una versione memorizzata nella cache del set di dati.

    Se si vuole ricaricare il set di dati in ogni iterazione dell'esperimento di set di dati, deselezionare il **Usa nella cache i risultati** opzione. I risultati vengono ricaricati anche se sono state apportate modifiche ai parametri del [Import Data](import-data.md).

7. Eseguire l'esperimento.

## <a name="results"></a>Risultati

Al termine, fare clic sul set di dati di output e selezionare **Visualize** per vedere se i dati sono stati importati correttamente.


## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 