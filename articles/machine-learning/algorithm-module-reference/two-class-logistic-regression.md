---
title: 'Regressione logistica a due classi: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo di regressione logistica a due classi nel servizio Azure Machine Learning per creare un modello di regressione logistica che può essere usato per stimare due (e solo due) risultati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 818b8627decd5ee7db711abc417f71c83e32b6c0
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128370"
---
# <a name="two-class-logistic-regression-module"></a>Modulo di regressione logistica a due classi

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Usare questo modulo per creare un modello di regressione logistica che può essere usato per stimare due (e solo due) risultati. 

La regressione logistica è una tecnica statistica ben nota utilizzata per la modellazione di molti tipi di problemi. Questo algoritmo è un metodo di *apprendimento* supervisionato.  Pertanto, è necessario fornire un set di dati che contenga già i risultati per il training del modello.  

### <a name="about-logistic-regression"></a>Informazioni sulla regressione logistica  

La regressione logistica è un metodo noto nelle statistiche usato per stimare la probabilità di un risultato ed è particolarmente comune per le attività di classificazione. L'algoritmo consente di stimare la probabilità di occorrenza di un evento mediante l'inserimento di dati in una funzione logistica.
  
In questo modulo, l'algoritmo di classificazione è ottimizzato per dicotomica o variabili binarie. Se è necessario classificare più risultati, usare il modulo di [regressione logistica multiclasse](./multiclass-logistic-regression.md) .

##  <a name="how-to-configure"></a>Come configurare  

Per eseguire il training di questo modello, è necessario fornire un set di dati contenente un'etichetta o una colonna di classe. Poiché questo modulo è concepito per problemi a due classi, l'etichetta o la colonna di classe deve contenere esattamente due valori. 

Ad esempio, la colonna Label potrebbe essere [voted] con i possibili valori di "Yes" o "No". In alternativa, potrebbe essere [rischio di credito], con i valori possibili "High" o "low". 
  
1.  Aggiungere il modulo di **regressione logistica a due classi** all'esperimento.  
  
2.  Specificare il modo in cui si desidera eseguire il training del modello, impostando l'opzione **crea modalità trainer** .  
  
    -   **Singolo parametro**: Se si conosce il modo in cui si desidera configurare il modello, è possibile specificare come argomenti un set specifico di valori.  
  
3.  Per la **tolleranza di ottimizzazione**, specificare un valore soglia da usare per l'ottimizzazione del modello. Se il miglioramento tra le iterazioni scende al di sotto della soglia specificata, l'algoritmo viene considerato convergente in una soluzione e il training viene arrestato.  
  
4.  Per il peso della **regolarizzazione L1** e il **peso della regolarizzazione L2**, digitare un valore da usare per i parametri di regolarizzazione L1 e L2. Per entrambi è consigliabile un valore diverso da zero.  
  
     La regolarizzazione è un metodo per evitare l'overfitting penalizzando i modelli con valori di coefficienti estremi. La regolarizzazione funziona aggiungendo la penalità associata ai valori coefficienti all'errore dell'ipotesi. Pertanto, un modello accurato con valori di coefficienti estremi verrebbe penalizzato più, ma un modello meno accurato con valori più conservativi verrebbe penalizzato meno.  
  
     La regolarizzazione L1 e L2 presenta effetti e usi diversi.  
  
    -   È possibile applicare la funzione L1 ai modelli sparse, che risulta utile quando si utilizzano dati ad alta dimensionalità.  
  
    -   Al contrario, la regolarizzazione L2 è preferibile per i dati che non sono di tipo sparse.  
  
     Questo algoritmo supporta una combinazione lineare di valori di regolarizzazione L1 e L2, ovvero se <code>x = L1</code> e <code>y = L2</code>, quindi <code>ax + by = c</code> definisce la portata lineare dei termini di regolarizzazione.  
  
    > [!NOTE]
    >  Per ulteriori informazioni sulla regolarizzazione L1 e L2, Nell'articolo seguente viene illustrato il modo in cui la regolarizzazione L1 e L2 sono diverse e il modo in cui influiscono sul montaggio del modello, con esempi di codice per i modelli di regressione logistica e di rete neurale:  [Regolarizzazione L1 e L2 per Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > Sono state concepite diverse combinazioni lineari di termini L1 e L2 per i modelli di regressione logistica: ad esempio, la regolarizzazione di [reti elastiche](https://wikipedia.org/wiki/Elastic_net_regularization). Si consiglia di fare riferimento a queste combinazioni per definire una combinazione lineare che sia efficace nel modello.
      
5.  Per le **dimensioni della memoria per l-BFGS**, specificare la quantità di memoria da usare per l'ottimizzazione *l-BFGS* .  
  
     L-BFGS è l'acronimo di "Limited Memory Broyden-Fletcher-Goldfarb-Shanno". Si tratta di un algoritmo di ottimizzazione popolare per la stima dei parametri. Questo parametro indica il numero di posizioni e sfumature precedenti da archiviare per il calcolo del passaggio successivo.  
  
     Questo parametro di ottimizzazione limita la quantità di memoria utilizzata per calcolare il passaggio e la direzione successivi. Quando si specifica una quantità di memoria inferiore, il training è più veloce ma meno accurato.  
  
6.  Per il valore di **inizializzazione numerico casuale**, digitare un valore integer. La definizione di un valore di inizializzazione è importante se si desidera che i risultati siano riproducibili su più esecuzioni dello stesso esperimento.  
  
  
8. Aggiungere un set di dati con tag all'esperimento e connettere uno dei [moduli di training](module-reference.md).  
  
    -   Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, usare il modulo [Train Model](./train-model.md) .  
  
9. Eseguire l'esperimento.  
  
## <a name="results"></a>Risultati

Al termine del training:

+ Per visualizzare un riepilogo dei parametri del modello, insieme ai pesi delle funzionalità appresi dal training, fare clic con il pulsante destro del mouse sull'output di [Train Model](./train-model.md) e selezionare Visualize ( **Visualizza**).   
  
+ Per eseguire stime sui nuovi dati, usare il modello sottoposto a training e i nuovi dati come input per il modulo [Score Model](./score-model.md) . 


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 