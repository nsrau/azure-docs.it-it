---
title: 'Esempio di finestra di progettazione #5: classificazione per prevedere la varianza + appetency + up-selling'
titleSuffix: Azure Machine Learning
description: Questa pipeline di esempio di progettazione (anteprima) Mostra la stima del classificatore binario della varianza, un'attività comune per la gestione delle relazioni con i clienti (CRM).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 11/04/2019
ms.openlocfilehash: 52fcebb201fbdeebd7b75d9735fd81c3a647c337
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516240"
---
# <a name="sample-5---classification-predict-churn"></a>Esempio 5-classificazione: varianza di stima
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Scopri come creare una pipeline di Machine Learning complessa senza scrivere una singola riga di codice usando la finestra di progettazione (anteprima).

Questa pipeline addestra 2 classificatori di **albero delle decisioni con boosting a due classi** per prevedere le attività comuni per i sistemi CRM (Customer Relationship Management), ovvero la varianza dei clienti. I valori dei dati e le etichette sono suddivisi in più origini dati e sono ripartiti in anonimato informazioni sui clienti. Tuttavia, è comunque possibile usare la finestra di progettazione per combinare i set di dati ed eseguire il training di un modello usando i valori nascosti.

Poiché si sta provando a rispondere alla domanda "quale?" si tratta di un problema di classificazione, ma è possibile applicare la stessa logica illustrata in questo esempio per risolvere qualsiasi tipo di problema di Machine Learning, indipendentemente dal fatto che si tratti di regressione, classificazione, clustering e così via.

Ecco il grafico completato per questa pipeline:

![Grafico della pipeline](./media/how-to-ui-sample-classification-predict-churn/pipeline-graph.png)

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Fare clic su Sample 5 per aprirlo. 

## <a name="data"></a>Dati

I dati per questa pipeline sono di KDD Cup 2009. Contiene 50.000 righe e 230 colonne di funzionalità. L'attività consiste nel prevedere la varianza, il appetency e il selling per i clienti che usano queste funzionalità. Per ulteriori informazioni sui dati e sull'attività, vedere il [sito Web KDD](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="pipeline-summary"></a>Riepilogo della pipeline

Questa pipeline di esempio nella finestra di progettazione Mostra la stima del classificatore binario della varianza, della appetency e della vendita, un'attività comune per la gestione delle relazioni con i clienti (CRM).

Prima di tutto, si tratta di una semplice elaborazione dati.

- Il set di dati non elaborato presenta molti valori mancanti. Usare il modulo **Clean Missing data** per sostituire i valori mancanti con 0.

    ![Pulire il set di dati](./media/how-to-ui-sample-classification-predict-churn/cleaned-dataset.png)

- Le funzionalità e la varianza corrispondente si trovano in set di impostazioni diversi. Usare il modulo **Add columns** per aggiungere le colonne Label alle colonne feature. La prima colonna, **col1**, è la colonna Label. Dal risultato della visualizzazione è possibile notare che il set di dati è sbilanciato. Ci sono altri esempi negativi (-1) rispetto agli esempi positivi (+ 1). Il modulo **percosse** viene usato per aumentare i casi sottorappresentati in un secondo momento.

    ![Aggiungere il set di dati della colonna](./media/how-to-ui-sample-classification-predict-churn/added-column1.png)



- Usare il modulo **Split data** per suddividere il set di dati in set di training e set di test.

- Utilizzare quindi il classificatore binario dell'albero delle decisioni con boosting con i parametri predefiniti per compilare i modelli di stima. Compilare un modello per ogni attività, ovvero un modello ciascuno per stimare il selling, appetency e la varianza.

- Nella parte destra della pipeline viene usato il modulo **percosse** per aumentare la percentuale di esempi positivi. La percentuale di PERCOSse è impostata su 100 per raddoppiare gli esempi positivi. Scopri di più sul funzionamento del modulo PERCOSse con il [modulo Reference0](../././algorithm-module-reference/SMOTE.md).

## <a name="results"></a>Risultati

Visualizzare l'output del modulo **Evaluate Model** per visualizzare le prestazioni del modello nel set di test. 

![Valutare i risultati](./media/how-to-ui-sample-classification-predict-churn/evaluate-result.png)

 È possibile spostare il dispositivo di scorrimento della **soglia** e visualizzare la modifica delle metriche per l'attività di classificazione binaria. 

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli altri esempi disponibili per la finestra di progettazione:

- [Esempio 1: regressione: stimare il prezzo di un'automobile](how-to-designer-sample-regression-automobile-price-basic.md)
- [Esempio 2: regressione: confrontare gli algoritmi per la stima del prezzo dell'automobile](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Esempio 3: classificazione con selezione delle caratteristiche: stima del reddito](how-to-designer-sample-classification-predict-income.md)
- [Esempio 4-Classificazione: stimare il rischio di credito (costo sensibile)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Esempio 6-Classificazione: stima dei ritardi dei voli](how-to-designer-sample-classification-flight-delay.md)
- [Esempio 7-classificazione di testo: set di dati di Wikipedia SP 500](how-to-designer-sample-text-classification.md)
