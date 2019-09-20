---
title: 'Esempio di interfaccia visiva #5: Classificazione per prevedere la varianza + appetency + up-selling'
titleSuffix: Azure Machine Learning
description: Questo esperimento di esempio dell'interfaccia visiva mostra la stima del classificatore binario della varianza, un'attività comune per la gestione delle relazioni con i clienti (CRM).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 260d94ddf2572979e819ee89dfcbd315ef3c4769
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71131935"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>Esempio 5-classificazione: Prevedere varianza, appetency e vendita 

Informazioni su come creare un esperimento di apprendimento automatico complesso senza scrivere una singola riga di codice usando l'interfaccia visiva.

Questo esperimento addestra tre classificatori di **albero delle decisioni con boosting a due classi** per prevedere le attività comuni per i sistemi CRM (Customer Relationship Management): varianza, appetency e vendita. I valori e le etichette dei dati sono suddivisi in più origini dati e sono ripartiti in anonimato informazioni sui clienti. Tuttavia, è comunque possibile usare l'interfaccia visiva per combinare i set di dati ed eseguire il training di un modello usando i valori con codifica.

Poiché si sta provando a rispondere alla domanda "quale?" si tratta di un problema di classificazione, ma è possibile applicare la stessa logica in questo progetto per risolvere qualsiasi tipo di problema di Machine Learning, indipendentemente dal fatto che si tratti di regressione, classificazione, clustering e così via.

Ecco il grafo completato per questo esperimento:

![Grafico dell'esperimento](./media/how-to-ui-sample-classification-predict-churn/experiment-graph.png)

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selezionare il pulsante **Apri** per l'esperimento Sample 5.

    ![Aprire l'esperimento](media/how-to-ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Data

I dati per questo esperimento sono di KDD Cup 2009. Contiene 50.000 righe e 230 colonne di funzionalità. L'attività consiste nel prevedere la varianza, il appetency e il selling per i clienti che usano queste funzionalità. Per ulteriori informazioni sui dati e sull'attività, vedere il [sito Web KDD](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="experiment-summary"></a>Riepilogo esperimento

Questo esperimento di esempio dell'interfaccia visiva mostra la stima del classificatore binario della varianza, della appetency e della vendita, un'attività comune per la gestione delle relazioni con i clienti (CRM).

Per prima cosa, eseguire alcune semplici operazioni di elaborazione dei dati.

- Il set di dati non elaborato contiene molti valori mancanti. Usare il modulo **Clean Missing data** per sostituire i valori mancanti con 0.

    ![Pulire il set di dati](./media/how-to-ui-sample-classification-predict-churn/cleaned-dataset.png)

- Le funzionalità e le etichette varianza, appetency e selling corrispondenti si trovano in set di impostazioni diversi. Usare il modulo **Add columns** per aggiungere le colonne Label alle colonne feature. La prima colonna, **col1**, è la colonna Label. Le altre colonne, **var1**, **var2**e così via, sono le colonne della funzionalità.

    ![Aggiungere il set di dati della colonna](./media/how-to-ui-sample-classification-predict-churn/added-column1.png)

- Usare il modulo **Split data** per suddividere il set di dati in set di training e set di test.

    Utilizzare quindi il classificatore binario dell'albero delle decisioni con boosting con i parametri predefiniti per compilare i modelli di stima. Compilare un modello per ogni attività, ovvero un modello ciascuno per stimare il selling, appetency e la varianza.

## <a name="results"></a>Risultati

Visualizzare l'output del modulo **Evaluate Model** per visualizzare le prestazioni del modello nel set di test. Per l'attività di vendita, la curva ROC indica che il modello è migliore rispetto a un modello casuale. L'area sotto la curva (AUC) è 0,857. Alla soglia 0,5, la precisione è 0,7, il richiamo è 0,463 e il Punteggio F1 è 0,545.

![Valutare i risultati](./media/how-to-ui-sample-classification-predict-churn/evaluate-result.png)

 È possibile spostare il dispositivo di scorrimento della **soglia** e visualizzare la modifica delle metriche per l'attività di classificazione binaria.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli altri esempi disponibili per l'interfaccia visiva:

- [Esempio 1: regressione: Stimare il prezzo di un'automobile](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [Esempio 2: regressione: Confrontare gli algoritmi per la stima del prezzo dell'automobile](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Esempio 3-classificazione: Stima del rischio di credito](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [Esempio 4-Classificazione: Stimare il rischio di credito (costo sensibile)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Esempio 6-Classificazione: Stimare i ritardi dei voli](how-to-ui-sample-classification-predict-flight-delay.md)
