---
title: 'Classificazione: Prevedere la varianza, stime sui desideri e up-selling '
titleSuffix: Azure Machine Learning service
description: Questo esperimento di esempio di interfaccia visiva Mostra stima classificatore binario della varianza, un'attività molto comune per la gestione delle relazioni dei clienti (CRM).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 7d10d996febd0e31c9085bf5cb82324cce101c80
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606149"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>Esempio 5 - classificazione: Prevedere la varianza, stime sui desideri e up-selling 

Informazioni su come creare un esperimento di apprendimento automatico complessi senza scrivere una singola riga di codice usando l'interfaccia visiva.

Questo esperimento esegue il training, tre **albero delle decisioni con Boosting a due classi** classificatori per stimare le attività comuni per sistemi customer relationship management (CRM): varianza, stime sui desideri e up-selling. I valori dei dati e le etichette vengono suddivise tra più origini dati e li assemblò per anonimizza le informazioni dei clienti, tuttavia, è possibile usare ancora l'interfaccia visiva per combinare set di dati e il training di un modello usando i valori codificati.

Poiché stiamo tentando di rispondere alla domanda "Quale uno?" si tratta di un problema di classificazione. Tuttavia, è possibile applicare la stessa procedura descritta in questo esperimento di affrontare qualsiasi tipo di apprendimento automatico che si tratti di regressione, classificazione, clustering e così via.

Ecco il grafico completato per questo esperimento:

![Grafico esperimento](./media/ui-sample-classification-predict-churn/experiment-graph.png)

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selezionare il **aperto** pulsante per l'esperimento di esempio 5.

    ![Aprire l'esperimento](media/ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Data

I dati che si userà per questo esperimento sono dalla competizione KDD Cup 2009. Il set di dati contiene 50.000 righe e colonne di funzioni 230. L'attività consiste nel prevedere la varianza, stime sui desideri e up-selling per i clienti che usano queste funzionalità. Per altre informazioni sui dati e l'attività, vedere la [sito Web KDD](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="experiment-summary"></a>Riepilogo di esperimento

Questo esperimento di esempio di interfaccia visiva Mostra stima classificatore binario di varianza, stime sui desideri e up-selling, un'attività molto comune per la gestione delle relazioni dei clienti (CRM).

Prima di tutto, facciamo alcune semplici operazioni di elaborazione di dati.

- Il set di dati non elaborati contiene un numero elevato di valori mancanti. Usiamo il **Clean Missing Data** valori di modulo per sostituire il mancanti con 0.

    ![Pulire il set di dati](./media/ui-sample-classification-predict-churn/cleaned-dataset.png)

- Le funzionalità e le corrispondenti della varianza, stime sui desideri, e per proporre le etichette sono nel set di dati diversi. Usiamo il **Add Columns** modulo per aggiungere le colonne di etichetta per le colonne di funzioni. La prima colonna, **Col1**, la colonna di etichetta. Il resto delle colonne, **Var1**, **Var2**e così via, sono le colonne di funzioni.

    ![Aggiungere il set di dati colonna](./media/ui-sample-classification-predict-churn/added-column1.png)

- Usiamo il **Split Data** modulo suddividere il set di dati in training e set di test.

    Utilizziamo la classificazione binaria Boosted Decision Tree con i parametri predefiniti per creare i modelli di stima. Abbiamo creato un modello per ogni attività, vale a dire, un modello per stimare up-selling, stime sui desideri e la varianza.

## <a name="results"></a>Risultati

Visualizzare l'output del **Evaluate Model** modulo per visualizzare le prestazioni del modello nel set di test. Per l'attività di up-selling, la curva ROC indica che il modello non è migliore rispetto a un modello casuale. L'area sottesa alla curva (AUC) è 0.857. Nella soglia di 0,5, la precisione è 0,7, il richiamo è 0.463 e il punteggio F1 è 0.545.

![Valutare i risultati](./media/ui-sample-classification-predict-churn/evaluate-result.png)

 È possibile spostare le **soglia** dispositivo di scorrimento e modificare le metriche per l'attività di classificazione binaria, vedere.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli altri esempi disponibili per l'interfaccia visiva:

- [Esempio 1: regressione: Stimare il prezzo di un'automobile](ui-sample-regression-predict-automobile-price-basic.md)
- [Esempio 2: regressione: Confrontare gli algoritmi per la stima di prezzo di un'automobile](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Esempio 3: classificazione: Prevedere il rischio di credito](ui-sample-classification-predict-credit-risk-basic.md)
- [Esempio 4: classificazione: Prevedere il rischio di credito (costo sensibile)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Esempio 6: classificazione: Prevedere i ritardi dei voli](ui-sample-classification-predict-flight-delay.md)