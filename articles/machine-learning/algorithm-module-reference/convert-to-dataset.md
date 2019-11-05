---
title: 'Converti in DataSet: riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Convert to DataSet nel servizio Azure Machine Learning per convertire l'input di dati nel formato del set di dati interno usato da Microsoft Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: b19d812d45b625531583aff726571c7da59f97f4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516253"
---
# <a name="convert-to-dataset"></a>Converti in DataSet

Questo articolo descrive come usare il modulo [Convert to DataSet](convert-to-dataset.md) in Azure Machine Learning Designer (Preview) per convertire i dati di una pipeline nel formato interno usato dalla finestra di progettazione.
  
La conversione non è necessaria nella maggior parte dei casi perché Azure Machine Learning converte in modo implicito i dati nel relativo formato di set di dati nativo quando viene eseguita un'operazione sui dati. 

Tuttavia, è consigliabile salvare i dati nel formato dataset se è stato eseguito un tipo di normalizzazione o pulizia in un set di dati e si desidera assicurarsi che le modifiche vengano utilizzate in altre pipeline.  
  
> [!NOTE]
> [Convert to DataSet](convert-to-dataset.md) cambia solo il formato dei dati. Non salva una nuova copia dei dati nell'area di lavoro. Per salvare il set di dati, fare doppio clic sulla porta di output, selezionare **Salva come set di dati**e digitare un nuovo nome.  
  
## <a name="how-to-use-convert-to-dataset"></a>Come usare Convert to DataSet  

È consigliabile usare il modulo [Modifica metadati](edit-metadata.md) per preparare il set di dati prima di usare [Convert to DataSet](convert-to-dataset.md).  È possibile aggiungere o modificare i nomi delle colonne, modificare i tipi di dati e così via.

1.  Aggiungere il modulo [Convert to DataSet](convert-to-dataset.md) alla pipeline. È possibile trovare questo modulo nella categoria **trasformazione dati** nella finestra di progettazione. 

2. Connetterlo a qualsiasi modulo che restituisce un set di dati.   

    Fino a quando i dati sono [tabulari](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py), è possibile convertirli in un set di dati. Sono inclusi i dati caricati usando [Importa dati](import-data.md), i dati creati mediante [immissione manuale dei dati](enter-data-manually.md)o i set di dati trasformati tramite [Apply Transformation](apply-transformation.md).

3.  Nell'elenco a discesa **azione** , indicare se si desidera eseguire una pulizia dei dati prima di salvare il set di dati:  
  
    - **None**: usare i dati così come sono.  
  
    - **SetMissingValue**: impostare un valore specifico su un valore mancante nel set di dati. Il segnaposto predefinito è il carattere punto interrogativo (?), ma è possibile usare l'opzione **Custom missing value** per digitare un valore diverso. Se ad esempio si digita "taxi" per un **valore mancante personalizzato**, tutti i "taxi" nel set di dati verranno modificati in valore mancante.
  
    - **ReplaceValues**: usare questa opzione per specificare un singolo valore esatto da sostituire con qualsiasi altro valore esatto.  È possibile sostituire i valori mancanti o i valori personalizzati impostando **Sostituisci** metodo:- **Missing**: selezionare questa opzione per sostituire i valori mancanti nel set di dati di input. Per **nuovo valore**, digitare il valore con cui sostituire i valori mancanti.
            - **personalizzata**: selezionare questa opzione per sostituire i valori personalizzati nel set di dati di input. Per **valore personalizzato**Digitare il valore che si desidera trovare. Ad esempio, supponendo che i dati contengano la stringa `obs` utilizzata come segnaposto per i valori mancanti, digitare `obs`. Per **nuovo valore**, digitare il nuovo valore con cui sostituire la stringa originale.
  
    Si noti che l'operazione **ReplaceValues** si applica solo alle corrispondenze esatte. Queste stringhe, ad esempio, non saranno interessate: `obs.`, `obsolete`.  
 
  
5.  Eseguire la pipeline oppure fare clic con il pulsante destro del mouse sul modulo [Convert to DataSet](convert-to-dataset.md) e scegliere **Esegui selezione**.  

## <a name="results"></a>Risultati

+  Per salvare il set di dati risultante con un nuovo nome, fare clic con il pulsante destro del mouse sull'output di [Convert to DataSet](convert-to-dataset.md) e selezionare **Salva come set di dati**.  
  
## <a name="technical-notes"></a>Note tecniche  

Questa sezione contiene informazioni dettagliate sull'implementazione, suggerimenti e risposte alle domande più frequenti.

-   Tutti i moduli che accettano un set di dati come input possono anche prendere i dati in formato CSV o TSV. Prima che venga eseguito il codice di un modulo, viene eseguita la pre-elaborazione degli input, che equivale all'esecuzione del modulo [Convert to DataSet](convert-to-dataset.md) sull'input.  
  
-   Non è possibile eseguire la conversione dal formato SVMLight al set di dati.  
  
-   Quando si specifica un'operazione di sostituzione personalizzata, l'operazione di ricerca e sostituzione viene applicata ai valori completi. non sono consentite corrispondenze parziali. Ad esempio, è possibile sostituire un 3 con un-1 o con 33, ma non è possibile sostituire un 3 in un numero a due cifre, ad esempio 35.  
  
-   Per le operazioni di sostituzione personalizzate, la sostituzione avrà esito negativo automaticamente se si utilizza come sostituzione qualsiasi carattere non conforme al tipo di dati corrente della colonna.  

  
## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 
