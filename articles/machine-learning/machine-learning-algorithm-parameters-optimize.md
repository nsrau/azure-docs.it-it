---
title: Ottimizzare gli algoritmi in Azure Machine Learning | Documentazione Microsoft
description: Viene illustrato come scegliere il set di parametri ottimale per un algoritmo in Azure Machine Learning.
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 6717e30e-b8d8-4cc1-ad0b-1d4727928d32
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: bradsev
ms.translationtype: Human Translation
ms.sourcegitcommit: e899487e9445955cea3a9387c73ea7c5dca37ddc
ms.openlocfilehash: b3be7f31ac31c656744fb809e3972af0ac4ad4f1
ms.contentlocale: it-it
ms.lasthandoff: 01/30/2017

---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning"></a>Scegliere i parametri per ottimizzare gli algoritmi in Azure Machine Learning
In questo argomento viene descritto come scegliere il set di iperparametri corretto per un algoritmo in Azure Machine Learning. Per la maggior parte degli algoritmi di Machine Learning è necessario impostare i parametri. Quando si esegue il training di un modello, è necessario specificare valori per questi parametri. L'efficacia del modello di cui è stato eseguito il training dipende dai parametri scelti per il modello. Il processo per trovare il set ottimale di parametri è noto come *selezione del modello*.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Ci sono vari modi per selezionare un modello. In Machine Learning, la convalida incrociata è uno dei metodi più diffusi per la selezione del modello ed è il meccanismo di selezione del modello predefinito in Azure Machine Learning. Poiché Azure Machine Learning supporta R e Python, è sempre possibile implementare i relativi meccanismi di selezione del modello tramite R o Python.

Il processo per trovare il migliore set di parametri è costituito da quattro passaggi:

1. **Definire lo spazio del parametro**: prima decidere i valori esatti dei parametri che si vogliono prendere in considerazione per l'algoritmo.
2. **Definire le impostazioni di convalida incrociata**: decidere come scegliere le riduzioni di convalida incrociata per il set di dati.
3. **Definire la metrica**: decidere quale metrica usare per determinare il migliore set di parametri, ad esempio, l'accuratezza, l'errore quadratico medio, la precisione, il richiamo, o il punteggio f.
4. **Eseguire il training, valutare e confrontare**: per ogni combinazione univoca dei valori dei parametri, la convalida incrociata viene eseguita e basata sulla metrica di errore definita dall'utente. Dopo valutazione e confronto, è possibile scegliere il modello con le prestazioni migliori.

Le figure seguenti illustrano come raggiungere l'obiettivo in Azure Machine Learning.

![Trovare il miglior set di parametri](./media/machine-learning-algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Definire lo spazio dei parametri
Il set di parametri può essere definito nella fase di inizializzazione del modello. Il pannello dei parametri di tutti gli algoritmi di Machine Learning presenta due modalità di training: *parametro singolo* e *intervallo di parametri*. Scegliere la modalità intervallo di parametri. Nella modalità intervallo di parametri è possibile immettere più valori per ogni parametro. Nella casella di testo è possibile immettere valori delimitati da virgole.

![Albero delle decisioni a due classi con innalzamento, parametro singolo](./media/machine-learning-algorithm-parameters-optimize/fig2.png)

 In alternativa è possibile definire il punto massimo e minimo della griglia e il numero totale dei punti da generare con **Use Range Builder**. Per impostazione predefinita, i valori dei parametri vengono generati su una scala lineare. Ma se è selezionata l'opzione **Scala logaritmica**, i valori vengono generati in scala logaritmica (ovvero, il rapporto dei punti adiacenti è costante invece di rappresentare la loro differenza). Per i parametri Integer, è possibile definire un intervallo tramite un segno meno. Ad esempio, "1-10" indica che tutti i numeri interi compresi tra 1 e 10 (entrambi compresi) costituiscono il set di parametri. È supportata anche una modalità mista. Ad esempio, il set di parametri "1-10, 20, 50" include numeri interi da 1 a 10, 20 e 50.

![Albero delle decisioni a due classi con innalzamento, intervallo di parametri](./media/machine-learning-algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Definire le riduzioni di convalida incrociata
Il modulo [Partition and Sample][partition-and-sample] (Partizionamento e campionamento) può essere usato per assegnare riduzioni ai dati in modo casuale. Nella configurazione di esempio seguente del modulo, vengono definite cinque riduzioni e viene assegnato in modo casuale un numero di riduzione alle istanze dell'esempio.

![Partition and sample (Partizione ed esempio)](./media/machine-learning-algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Definire la metrica
Il modulo [Tune Model Hyperparameters][tune-model-hyperparameters] fornisce supporto per scegliere in modo empirico il miglior set di parametri per un algoritmo e un set di dati specifici. Oltre ad altre informazioni sul training del modello, il riquadro **Properties** (Proprietà) di questo modulo include la metrica per determinare il miglior set di parametri. E ha due elenchi a discesa rispettivamente per gli algoritmi di classificazione e di regressione. Se l'algoritmo in esame è un algoritmo di classificazione, la metrica di regressione viene ignorata e viceversa. In questo esempio specifico la metrica è **Accuracy** (Accuratezza).   

![Organizzare i parametri](./media/machine-learning-algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Eseguire il training, valutare e confrontare
Lo stesso modulo [Tune Model Hyperparameters][tune-model-hyperparameters] esegue il training di tutti i modelli corrispondenti al set di parametri, valuta diverse metriche e crea il miglior modello in base alla metrica scelta. Tale modulo dispone di due input obbligatori:

* Allievo non formato
* Set di dati

Il modulo contiene anche un set di dati di input facoltativo. Connettere il set di dati con le informazioni di riduzione all'input del set di dati obbligatorio. Se al set di dati non vengono assegnate informazioni di riduzione, per impostazione predefinita viene eseguita automaticamente una convalida incrociata a 10 riduzioni. Se non viene eseguita l'assegnazione di riduzione e viene specificato un set di dati di convalida nella porta del set di dati facoltativo, viene scelta la modalità di test del training e viene usato il primo set di dati per eseguire il training del modello per ogni combinazione di parametri.

![Classificatore dell'albero delle decisioni con innalzamento](./media/machine-learning-algorithm-parameters-optimize/fig6a.png)

Il modello viene quindi valutato sul set di dati di convalida. La porta di output sinistra del modulo visualizza metriche diverse come funzioni dei valori del parametro. La porta di output destra offre il modello di cui è stato eseguito il training che corrisponde al modello con le migliori prestazioni in base alla metrica scelta (in questo caso **Accuracy**).  

![Set di dati di convalida](./media/machine-learning-algorithm-parameters-optimize/fig6b.png)

È possibile vedere esattamente i parametri scelti visualizzando la porta di output destra. Questo modello può essere utilizzato per la valutazione in un set di test o in un servizio web operativo dopo il salvataggio come modello per il training.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/

