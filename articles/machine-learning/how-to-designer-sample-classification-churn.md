---
title: 'Progettazione: Esempio di previsione della varianza'
titleSuffix: Azure Machine Learning
description: Seguire questo esempio di classificazione per prevedere la varianza con la finestra di progettazione di Azure Machine Learning gli alberi delle decisioni con boosting.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: sgilley
ms.date: 12/25/2019
ms.openlocfilehash: 701bf186080ef627ef4621e959b281fd58aa4132
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963368"
---
# <a name="use-boosted-decision-tree-to-predict-churn-with-azure-machine-learning-designer"></a>Usare l'albero delle decisioni con boosting per prevedere la varianza con la finestra di progettazione di Azure Machine Learning

**Finestra di progettazione (anteprima) - Esempio 5**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Informazioni su come creare una pipeline complessa di Machine Learning senza scrivere una sola riga di codice usando la finestra di progettazione (anteprima).

Questa pipeline esegue il training di due classificatori **Two-Class Boosted Decision Tree** (Albero delle decisioni con boosting a due classi) per prevedere attività comuni per i sistemi CRM (Customer Relationship Management), ovvero la varianza dei clienti. I valori dei dati e le etichette sono divisi tra più origini dati e sono ripartiti in informazioni dei clienti in forma anonima. Tuttavia, è comunque possibile usare la finestra di progettazione per combinare i set di dati ed eseguire il training di un modello usando i valori nascosti.

Poiché si sta provando a rispondere alla domanda "Quale?", si tratta di un problema di classificazione, ma è possibile applicare la stessa logica di questo esempio per affrontare qualsiasi tipo di problema di Machine Learning, ovvero regressione, classificazione, clustering e così via.

Ecco il grafo completato per questa pipeline:

![Grafo della pipeline](./media/how-to-designer-sample-classification-churn/pipeline-graph.png)

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Fare clic su Sample 5 per aprirlo. 

## <a name="data"></a>Data

I dati per questa pipeline provengono da KDD Cup 2009. Contiene 50.000 righe e 230 colonne di caratteristiche. L'attività consiste nel prevedere la varianza, l'appetibilità e l'upsell per i clienti che usano queste caratteristiche. Per altre informazioni sui dati e sull'attività, vedere il [sito Web KDD](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="pipeline-summary"></a>Riepilogo della pipeline

Questa pipeline di esempio nella finestra di progettazione mostra la previsione del classificatore binario della varianza, della appetibilità e dell'upsell, un'attività comune per i sistemi CRM (Customer Relationship Management).

Prima di tutto, eseguire una semplice elaborazione dei dati.

- Il set di dati non elaborati presenta molti valori mancanti. Usare il modulo **Clean Missing Data** (Pulisci dati mancanti) per sostituire i valori mancanti con 0.

    ![Pulire il set di dati](media/how-to-designer-sample-classification-churn/sample5-dataset-1225.png)

- Le caratteristiche e la varianza corrispondente si trovano in set di dati diversi. Usare il modulo **Add Columns** (Aggiungi colonne) per aggiungere le colonne di etichette alle colonne di caratteristiche. La prima colonna, **Col1**, è la colonna di etichette. Nel risultato della visualizzazione si può notare che il set di dati è sbilanciato. Contiene molti più esempi negativi (-1) che positivi (+ 1). Per aumentare i casi sottorappresentati, più avanti si userà il modulo **SMOTE**.

    ![Aggiungere il set di dati in colonne](./media/how-to-designer-sample-classification-churn/sample5-addcol-1225.png)



- Usare il modulo **Split Data** (Dividi dati) per dividere il set di dati in set di training e set di test.

- Usare quindi il classificatore binario Boosted Decision Tree (Albero delle decisioni in boosting) con i parametri predefiniti per creare i modelli di previsione. Creare un modello per ogni attività, ovvero un modello ciascuno per prevedere l'upsell, l'appetibilità e la varianza.

- Nella parte destra della pipeline viene usato il modulo **SMOTE** per aumentare la percentuale di esempi positivi. La percentuale SMOTE è impostata su 100 per raddoppiare gli esempi positivi. Altri dettagli sul funzionamento del modulo SMOTE sono disponibili nelle corrispondenti [informazioni di riferimento](algorithm-module-reference/smote.md).

## <a name="results"></a>Risultati

Visualizzare l'output del modulo **Evaluate Model** (Valuta modello) per verificare le prestazioni del modello nel set di test. 

![Valutare i risultati](./media/how-to-designer-sample-classification-churn/sample5-evaluate-1225.png)

 È possibile spostare il dispositivo di scorrimento **Threshold** (Soglia) e vedere come cambiano le metriche per l'attività di classificazione binaria. 

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli altri esempi disponibili per la finestra di progettazione:

- [Esempio 1 - Regressione: Prevedere il prezzo di un'automobile](how-to-designer-sample-regression-automobile-price-basic.md)
- [Esempio 2 - Regressione: Confrontare gli algoritmi per la previsione del prezzo delle automobili](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Esempio 3 - Classificazione con selezione delle caratteristiche: Previsione del reddito](how-to-designer-sample-classification-predict-income.md)
- [Esempio 4 - Classificazione: Prevedere il rischio di credito (sensibile al costo)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Esempio 6 - Classificazione: Prevedere i ritardi dei voli](how-to-designer-sample-classification-flight-delay.md)
- [Esempio 7 - Classificazione del testo: Set di dati SP 500 di Wikipedia](how-to-designer-sample-text-classification.md)
