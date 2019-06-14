---
title: 'Two-Class Logistic Regression: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Two-Class Logistic Regression nel servizio Azure Machine Learning per creare un modello di regressione logistica che può essere usato per stimare i risultati di due (e solo due).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: aacaf6c64ef77d0e694f97e3675060eca33794ed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029251"
---
# <a name="two-class-logistic-regression-module"></a>Modulo Two-Class Logistic Regression

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per creare un modello di regressione logistica che può essere usato per stimare i risultati di due (e solo due). 

La regressione logistica è una nota tecnica statistica utilizzata per modellare molti tipi di problemi. Questo algoritmo è un *apprendimento supervisionato* ; (metodo)  Pertanto, è necessario fornire un set di dati che contiene già i risultati per il training del modello.  

### <a name="about-logistic-regression"></a>Sulla regressione logistica  

Regressione logistica è un noto metodo statistico che viene usato per stimare la probabilità di un risultato ed è particolarmente diffusi nelle attività di classificazione. L'algoritmo consente di stimare la probabilità di occorrenza di un evento adattando i dati a una funzione logistica.
  
In questo modulo, l'algoritmo di classificazione è ottimizzato per le variabili dicotomiche o binarie. Se è necessario classificare più risultati, usare il [Multiclass Logistic Regression](./multiclass-logistic-regression.md) modulo.

##  <a name="how-to-configure"></a>Come configurare  

Per eseguire il training di questo modello, è necessario fornire un set di dati che contiene una colonna di etichetta o una classe. Poiché questo modulo è destinato ai problemi di due classi, la colonna di etichetta o una classe deve contenere esattamente due valori. 

Ad esempio, la colonna etichetta potrebbe essere [votato] con i valori possibili di "Sì" o "No". In alternativa, è possibile [rischio di credito] con i valori possibili di "Alta" o "Bassa". 
  
1.  Aggiungere il **Two-Class Logistic Regression** modulo nell'esperimento.  
  
2.  Specificare come si desidera che il modello di esecuzione del training, impostando il **modalità di creazione trainer** opzione.  
  
    -   **Singolo parametro**: Se si conosce la modalità con cui si desidera configurare il modello, è possibile definire un set specifico di valori come argomenti.  
  
3.  Per la **tolleranza ottimizzazione**, specificare un valore di soglia da usare quando si ottimizza il modello. Se il miglioramento tra le iterazioni è inferiore alla soglia specificata, viene considerato l'algoritmo di avere eseguito la convergenza in una soluzione e si arresta la formazione.  
  
4.  Per la **peso della regolarizzazione L1** e **peso regolarizzazione L2**, digitare un valore da usare per i parametri di regolarizzazione L1 ed L2. È consigliabile un valore diverso da zero per entrambi.  
  
     *La regolarizzazione* è un metodo per evitare l'overfitting compromettere alle eseguite dagli modelli con valori dei coefficienti estremi. La regolarizzazione funziona aggiungendo la penalità associata ai valori del coefficiente per l'errore dell'ipotesi. Di conseguenza, un modello accurato con valori dei coefficienti estremi viene penalizzato in misura più, ma un modello meno accurato con valori più conservativi viene penalizzato in misura minore.  
  
     Regolarizzazione L1 e L2 hanno effetti diversi e Usa.  
  
    -   L1 può essere applicato ai modelli di tipo sparse, che è utile quando si lavora con i dati a elevata dimensionalità.  
  
    -   La regolarizzazione L2, invece, è preferibile per i dati che non sono di tipo sparsi.  
  
     Questo algoritmo supporta una combinazione lineare dei valori di regolarizzazione L1 ed L2: vale a dire, se <code>x = L1</code> e <code>y = L2</code>, quindi <code>ax + by = c</code> definisce l'intervallo lineare delle condizioni di regolarizzazione.  
  
    > [!NOTE]
    >  Vuoi altre informazioni su regolarizzazione L1 ed L2? L'articolo seguente fornisce una descrizione del modo in cui la regolarizzazione L1 ed L2 sono diversi e gli effetti di adattamento di modelli, esempi di codice per i modelli di reti neurali e regressione logistica:  [Regolarizzazione L1 e L2 per Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > Diverse combinazioni di lineare delle condizioni di tipo L1 e L2 hanno state concepite per i modelli di regressione logistica: ad esempio, [elastic net regolarizzazione](https://wikipedia.org/wiki/Elastic_net_regularization). Si consiglia di fare riferimento a queste combinazioni per definire una combinazione lineare che risulta valida nel modello.
      
5.  Per la **dimensione della memoria per L-BFGS**, specificare la quantità di memoria da utilizzare per *L-BFGS* ottimizzazione.  
  
     L-BFGS è l'acronimo di "limited memory Broyden-Fletcher-Goldfarb-Shanno". È un algoritmo di ottimizzazione che è più diffuso per la stima dei parametri. Questo parametro indica il numero di posizioni e gradienti passati da archiviare per il calcolo del passaggio successivo.  
  
     Questo parametro di ottimizzazione limita la quantità di memoria utilizzata per calcolare il passaggio successivo e la direzione. Quando si specifica di memoria inferiore, il training è più veloce ma meno accurato.  
  
6.  Per la **seed numeri Random**, digitare un valore intero. La definizione di un valore di inizializzazione è importante se si desidera che i risultati siano riproducibile in più esecuzioni dello stesso esperimento.  
  
  
8. Aggiungere un set di dati con tag all'esperimento e connettere tra il [moduli di training](module-reference.md).  
  
    -   Se si imposta **modalità di creazione trainer** al **singolo parametro**, usare il [Train Model](./train-model.md) modulo.  
  
9. Eseguire l'esperimento.  
  
## <a name="results"></a>Risultati

Dopo aver completata la formazione:

+ Per visualizzare un riepilogo dei parametri del modello, con i pesi delle funzionalità appreso dal training, fare doppio clic sull'output del [Train Model](./train-model.md) e selezionare **Visualize**.   
  
+ Per eseguire stime sui nuovi dati, utilizzare il modello sottoposto a training e i nuovi dati come input per il [Score Model](./score-model.md) modulo. 


## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 