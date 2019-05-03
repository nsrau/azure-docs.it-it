---
title: 'Importare i dati: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Import Data nel servizio Azure Machine Learning per caricare i dati in un esperimento di machine learning dai servizi dati cloud esistenti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ed51c4e7b6c7d226c7827d1ba00bc96a7be1e6b0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028306"
---
# <a name="import-data-module"></a>Modulo di importazione dati

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per caricare i dati in un esperimento di machine learning dai servizi dati cloud esistenti.  
Il modulo include ora una procedura guidata per aiutarti a scegliere un'opzione di archiviazione e una selezione tra le sottoscrizioni esistenti e gli account per configurare rapidamente tutte le opzioni. È necessario modificare una connessione dati esistente? Nessun problema; la procedura guidata carica tutti i dettagli di configurazione precedente in modo che non è necessario ricominciare da zero. 
  
Dopo aver definito i dati desiderati e connettersi all'origine, [Import Data](./import-data.md) deduce il tipo di dati di ogni colonna in base ai valori contiene e carica i dati nell'area di lavoro di Azure Machine Learning. L'output del [Import Data](./import-data.md) è un set di dati che può essere usato con qualsiasi esperimento creato.

  
Se i dati di origine vengono modificati, è possibile aggiornare il set di dati e aggiungere nuovi dati eseguendo [Import Data](./import-data.md). Tuttavia, se non si desidera leggere nuovamente dall'origine ogni volta che si esegue l'esperimento, selezionare la **Usa nella cache i risultati** opzione su TRUE. Quando questa opzione è selezionata, il modulo di verifica se l'esperimento è eseguito in precedenza usando la stessa origine e le stesse opzioni di input. Se viene trovata un'esecuzione precedente, i dati nella cache vengono usati, anziché ricaricare i dati dall'origine.
 

## <a name="data-sources"></a>Origini dati

Il modulo Import Data supporta le origini dati seguenti. Fare clic sul collegamento per istruzioni dettagliate ed esempi di utilizzo di ogni origine dati. 
 
Se non si conosce come o dove è consigliabile archiviare i dati, vedere questa Guida agli scenari comuni di dati nel processo di analisi scientifica dei dati:  [Scenari per l'analitica avanzata in Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-plan-sample-scenarios). 


|Origine dati| Usare con|
|-----------|-----------|  
|[URL Web tramite HTTP](./import-from-web-url-via-http.md)|Ottenere i dati che sono ospitati in un URL web che utilizza il protocollo HTTP e che è stato fornito nei formati CSV, TSV, ARFF o SvmLight|  
|[Importare da archiviazione Blob di Azure](./import-from-azure-blob-storage.md) |Ottenere i dati archiviati nel servizio blob di Azure|  

## <a name="how-to-use-import-data"></a>Come usare l'importazione dei dati
 
1. Aggiungere il **Import Data** modulo nell'esperimento. È possibile trovare questo modulo nel **Data Input and Output** categoria nell'interfaccia.

2. Fare clic su **avviare Importazione guidata dati** per configurare l'origine dati tramite una procedura guidata.

    La procedura guidata Ottiene il nome dell'account e credenziali e consentono di configurare altre opzioni. Se si modifica una configurazione esistente, carica i valori correnti prima di tutto.

3. Se non si desidera utilizzare la procedura guidata, fare clic su **zdroj dat**, quindi scegliere il tipo di archiviazione basata su cloud si legge da. 

    Impostazioni aggiuntive dipendono dal tipo di archiviazione desiderato, e se lo spazio di archiviazione è o non protetto. Si potrebbe essere necessario fornire il nome dell'account, il tipo di file o le credenziali. Alcune origini non richiedono l'autenticazione. per altri utenti, è necessario conoscere il nome dell'account, una chiave o nome del contenitore.

4. Selezionare il **Usa nella cache i risultati** opzione se si vuole memorizzare nella cache il set di dati per il riutilizzo in esecuzioni successive.

    Supponendo che sono non state altre modifiche ai parametri del modulo, l'esperimento viene caricato l'ora di dati solo il primo modulo viene eseguito e in seguito una versione memorizzata nella cache del set di dati.

    Deselezionare questa opzione se è necessario ricaricare i dati ogni volta che si esegue l'esperimento.

5. Eseguire l'esperimento.

    Durante l'importazione dei dati carica i dati nell'interfaccia, deduce il tipo di dati di ogni colonna in base ai valori che contiene, sia numerici che categorici.

    - Se è presente un'intestazione, l'intestazione viene utilizzato per denominare le colonne del set di dati di output.

    - Se non sono presenti intestazioni di colonna esistenti nei dati, vengono generati nuovi nomi di colonna usando il formato col1, col2,... , coln *.

## <a name="results"></a>Risultati

Al termine dell'importazione, fare clic sul set di dati di output e selezionare **Visualize** per vedere se i dati sono stati importati correttamente.

Se si desidera salvare i dati per il riutilizzo, piuttosto che l'importazione di un nuovo set di dati ogni volta che viene eseguito l'esperimento, fare doppio clic sull'output e selezionare **Save as Dataset**. Scegliere un nome per il set di dati. Il set di dati salvato mantiene i dati al momento del salvataggio e dati non vengono aggiornati quando viene eseguito di nuovo l'esperimento, anche se viene modificato il set di dati nell'esperimento. Ciò può essere utile per eseguire gli snapshot dei dati.

Dopo aver importato i dati, potrebbe essere necessario alcuni processi di preparazione aggiuntivi per la modellazione e l'analisi:


- Uso [Edit Metadata](./edit-metadata.md) per modificare i nomi di colonna, per la gestione di una colonna come un tipo di dati diverso oppure per indicare che alcune colonne sono etichette o funzionalità.

- Usare [Select Columns in Dataset](./select-columns-in-dataset.md) per selezionare un subset di colonne da trasformare o usare nella modellazione. Le colonne trasformate o rimosse possono facilmente aggiungere di nuovo al set di dati originale utilizzando il [Add Columns](./add-columns.md) modulo.  

- Uso [Partition and Sample](./partition-and-sample.md) per dividere il set di dati, eseguire il campionamento o ottenere le prime n righe.

## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 