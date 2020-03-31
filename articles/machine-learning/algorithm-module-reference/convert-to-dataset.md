---
title: 'Converti in set di dati: riferimento al moduloConvert to Dataset: Module reference'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Converti in set di dati in Azure Machine Learning per convertire l'input dei dati nel formato di set di dati interno usato da Microsoft Azure Machine Learning.Learn how to use the Convert to Dataset module in Azure Machine Learning to convert data input to the internal dataset format used by Microsoft Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: fd45db779b7a828d247f09cae38f90fc20d12c16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456591"
---
# <a name="convert-to-dataset"></a>Eseguire la conversione in set di dati

Questo articolo descrive come usare il modulo Converti in set di dati nella finestra di progettazione di Azure Machine Learning (anteprima) per convertire i dati per una pipeline nel formato interno della finestra di progettazione.
  
La conversione non è necessaria nella maggior parte dei casi. Azure Machine Learning converte in modo implicito i dati nel formato del set di dati nativo quando viene eseguita un'operazione sui dati. 

È consigliabile salvare i dati nel formato del set di dati se è stato eseguito un tipo di normalizzazione o pulizia su un set di dati e si desidera assicurarsi che le modifiche vengano utilizzate in altre pipeline.  
  
> [!NOTE]
> Converti in set di dati modifica solo il formato dei dati. Non salva una nuova copia dei dati nell'area di lavoro. Per salvare il set di dati, fare doppio clic sulla porta di output, selezionare **Salva come set di dati**e immettere un nuovo nome.  
  
## <a name="how-to-use-convert-to-dataset"></a>Come usare Converti in set di datiHow to use Convert to Dataset  

È consigliabile utilizzare il modulo [Modifica metadati](edit-metadata.md) per preparare il set di dati prima di usare Converti in set di dati. È possibile aggiungere o modificare i nomi delle colonne, regolare i tipi di dati e apportare altre modifiche in base alle esigenze.

1.  Aggiungere il modulo Converti in set di dati alla pipeline. È possibile trovare questo modulo nella categoria Trasformazione dati nella finestra di **progettazione.** 

2. Connetterlo a qualsiasi modulo che genera un set di dati.   

    Finché i dati sono [tabulari,](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)è possibile convertirlo in un set di dati. Sono inclusi i dati caricati tramite [Importa dati](import-data.md), i dati creati tramite [Immetti dati manualmente](enter-data-manually.md)o i set di dati trasformati tramite Applica [trasformazione](apply-transformation.md).

3.  Nell'elenco a discesa **Azione** indicare se si desidera eseguire qualsiasi pulizia dei dati prima di salvare il set di dati:  
  
    - **Nessuno**: Utilizzare i dati così come sono.  
  
    - **SetMissingValue**: Imposta un valore specifico su un valore mancante nel set di dati. Il segnaposto predefinito è il carattere punto interrogativo (?), ma è possibile utilizzare l'opzione **Valore mancante personalizzato** per immettere un valore diverso. Ad esempio, se si immette **Taxi** per **Valore mancante personalizzato**, tutte le istanze di **Taxi** nel set di dati verranno modificate nel valore mancante.
  
    - **ReplaceValues**: Utilizzare questa opzione per specificare un singolo valore esatto da sostituire con qualsiasi altro valore esatto. È possibile sostituire i valori mancanti o i valori personalizzati impostando il metodo **Replace:**

      - **Mancante:** scegliere questa opzione per sostituire i valori mancanti nel set di dati di input. In **Nuovo valore**, immettere il valore con cui sostituire i valori mancanti.
      - **Personalizzato**: scegliere questa opzione per sostituire i valori personalizzati nel set di dati di input. In **Valore personalizzato**immettere il valore che si desidera trovare. Ad esempio, se i `obs` dati contengono la stringa utilizzata `obs`come segnaposto per i valori mancanti, immettere . Per **Nuovo valore**, immettere il nuovo valore con cui sostituire la stringa originale.
  
    Si noti che l'operazione **ReplaceValues** si applica solo alle corrispondenze esatte. Ad esempio, queste stringhe non `obs.` `obsolete`sarebbero interessate: , .  
 
  
5.  Inviare la pipeline.  

## <a name="results"></a>Risultati

+  Per salvare il set di dati risultante con un nuovo nome, selezionare l'icona **Registra set di dati** nella scheda **Output** nel riquadro destro del modulo.  
  
## <a name="technical-notes"></a>Note tecniche  

-   Qualsiasi modulo che accetta un set di dati come input può anche accettare dati nel file CSV o nel file TSV. Prima dell'esecuzione di qualsiasi codice del modulo, gli input vengono pre-elaborati. La pre-elaborazione equivale all'esecuzione del modulo Converti in set di dati nell'input.  
  
-   Non è possibile eseguire la conversione dal formato SVMLight a un set di dati.  
  
-   Quando si specifica un'operazione di sostituzione personalizzata, l'operazione di ricerca e sostituzione viene applicata ai valori completi. Le corrispondenze parziali non sono consentite. Ad esempio, è possibile sostituire un 3 con un -1 o 33, ma non è possibile sostituire un 3 in un numero a due cifre, ad esempio 35.  
  
-   Per le operazioni di sostituzione personalizzata, la sostituzione ha esito negativo in modo invisibile all'utente se si usa un carattere sostitutivo non conforme al tipo di dati corrente della colonna.  

  
## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 
