---
title: 'Macchina vettoriale di supporto a due classi: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo **Computer vettore** di supporto a due classi in Azure Machine Learning per creare un modello basato sull'algoritmo della macchina vettoriale di supporto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: ba788518951e72c1701d99decf46350e8665dbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455809"
---
# <a name="two-class-support-vector-machine-module"></a>Modulo Vector Machine di supporto a due classi

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Utilizzare questo modulo per creare un modello basato sull'algoritmo della macchina vettoriale di supporto. 

Le macchine vettoriali di supporto (SVM) sono una classe ben studiata di metodi di apprendimento supervisionati. Questa particolare implementazione è adatta alla previsione di due possibili risultati, basati su variabili continue o categoriche.

Dopo aver definito i parametri del modello, eseguire il training del modello usando i moduli di training e aver fornito un set di *dati con tag* che include un'etichetta o una colonna dei risultati.

## <a name="about-support-vector-machines"></a>Informazioni sulle macchine vettoriali di supporto

Le macchine a vettori di supporto (SVM) sono tra gli algoritmi di Machine Learning meno recenti e i modelli SVM sono stati usati in molte applicazioni, dal recupero di informazioni alla classificazione di testo e immagini. Le SVM possono essere usate sia per le attività di classificazione che per le attività di regressione.

Questo modello SVM è un modello di apprendimento supervisionato che richiede dati etichettati. Nel processo di training, l'algoritmo analizza i dati di input e riconosce i modelli in uno spazio di funzionalità multidimensionale denominato *hyperplane*.  Tutti gli esempi di input sono rappresentati come punti in questo spazio e sono mappati alle categorie di output in modo che le categorie siano suddivise per il più ampio e chiaro possibile.

Per la stima, l'algoritmo SVM assegna nuovi esempi in una categoria o nell'altra, mappandoli nello stesso spazio. 

## <a name="how-to-configure"></a>Modalità di configurazione 

Per questo tipo di modello, è consigliabile normalizzare il set di dati prima di utilizzarlo per eseguire il training del classificatore.
  
1.  Aggiungere il modulo **Macchina vettoriale** di supporto a due classi alla pipeline.  
  
2.  Specificare la modalità di training del modello impostando l'opzione **Crea modalità trainer.**  
  
    -   **Parametro singolo**: se si conosce la configurazione del modello, è possibile fornire un set specifico di valori come argomenti.  

    -   **Intervallo parametri**: se non si è sicuri dei parametri migliori, è possibile trovare i parametri ottimali utilizzando il modulo [Ottimizzazione dei peridi modello.](tune-model-hyperparameters.md) Si fornisce un certo intervallo di valori e il trainer scorre più combinazioni delle impostazioni per determinare la combinazione di valori che produce il risultato migliore.

3.  Per **Numero di iterazioni**, digitare un numero che denoti il numero di iterazioni utilizzate durante la compilazione del modello.  
  
     Questo parametro può essere usato per controllare il compromesso tra velocità e precisione di training.  
  
4.  Per **Lambda**, digitare un valore da utilizzare come peso per la regolarizzazione L1.  
  
     Questo coefficiente di regolarizzazione consente di ottimizzare il modello. I valori più elevati penalizzano i modelli più complessi.  
  
5.  Selezionare l'opzione **Normalizza funzioni,** se si desidera normalizzare le feature prima dell'allenamento.
  
     Se si applica la normalizzazione, prima del training, i punti dati vengono centrati sulla media e scalati in modo da avere un'unità di deviazione standard.
  
6.  Selezionare l'opzione **Progetto nella sfera unità**per normalizzare i coefficienti.
  
     La proiezione dei valori nello spazio unitario significa che prima del training, i punti dati vengono centrati su 0 e scalati per avere un'unità di deviazione standard.
  
7.  In **Valore di controllo di serie con numero casuale**digitare un valore intero da utilizzare come valore di serie se si desidera garantire la riproducibilità tra le esecuzioni.  In caso contrario, un valore di clock di sistema viene utilizzato come valore di serie, che può comportare risultati leggermente diversi tra le esecuzioni.
  
9. Collegare un set di dati con etichetta e uno dei moduli di [formazione:](module-reference.md)
  
    -   Se si imposta **La modalità Crea trainer** su **Parametro singolo**, utilizzare il modulo Modello di [treno.](train-model.md)
  
10. Inviare la pipeline.

## <a name="results"></a>Risultati

Al termine dell'allenamento:

+ Per salvare un'istantanea del modello sottoposto a training, selezionare la scheda **Uscite** nel pannello destro del modulo **Modulo del modello.** Selezionare l'icona **Registra set di dati** per salvare il modello come modulo riutilizzabile.

+ Per usare il modello per il punteggio, aggiungere il modulo Modello di **punteggio** a una pipeline.


## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 