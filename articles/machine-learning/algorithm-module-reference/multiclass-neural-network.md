---
title: 'Rete neurale multiclasse: riferimento moduloMulticlass Neural Network: Module Reference'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Rete neurale multiclasse in Azure Machine Learning per creare un modello di rete neurale che può essere usato per stimare una destinazione con più valori.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 653b12ddd54c5ec9e4e7dd23a323f34460daa962
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920417"
---
# <a name="multiclass-neural-network-module"></a>Modulo di rete neurale multiclasse

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Usare questo modulo per creare un modello di rete neurale da usare per stimare una destinazione contenente più valori. 

Ad esempio, reti neurali di questo tipo possono essere utilizzate in attività complesse di visione artificiale, ad esempio il riconoscimento di cifre o lettere, la classificazione di documenti e il riconoscimento di modelli.

La classificazione tramite reti neurali è un metodo di apprendimento supervisionato e pertanto richiede un set di *dati con tag* che includa una colonna label.

È possibile eseguire il training del modello fornendo il modello e il set di dati con tag come input per il training [del modello](./train-model.md). Il modello sottoposto a training può quindi essere utilizzato per stimare i valori per i nuovi esempi di input.  

## <a name="about-neural-networks"></a>Informazioni sulle reti neurali

Una rete neurale è un insieme di livelli interconnessi. Gli ingressi sono il primo livello e sono collegati a un livello di output da un grafico aciclico composto da bordi e nodi ponderati.

Tra i livelli di input e output è possibile inserire più livelli nascosti. Con uno solo o più livelli nascosti, è possibile eseguire facilmente la maggior parte delle attività di stima. Tuttavia, recenti ricerche hanno dimostrato che le reti neurali profonde (DNN) con molti livelli possono essere efficaci in attività complesse come il riconoscimento di immagini o vocali. I livelli successivi vengono utilizzati per modellare livelli crescenti di profondità semantica.

La relazione tra input e output viene appresa dal training della rete neurale sui dati di input. La direzione del grafico procede dagli input attraverso il livello nascosto e al livello di output. Tutti i nodi di un livello sono collegati dai bordi ponderati ai nodi nel livello successivo.

Per calcolare l'output della rete per un particolare input, viene calcolato un valore in corrispondenza di ogni nodo nei livelli nascosti e nel livello di output. Il valore viene impostato calcolando la somma ponderata dei valori dei nodi del layer precedente. Una funzione di attivazione viene quindi applicata a tale somma ponderata.

## <a name="configure-multiclass-neural-network"></a>Configurazione della rete neurale multiclasse

1. Aggiungere il modulo **Rete neurale MultiClass** alla pipeline nella finestra di progettazione. Questo modulo è disponibili in **Machine Learning**, **Initialize**, nella categoria **Classificazione.**

2. **Crea modalità trainer**: Utilizzare questa opzione per specificare come si desidera che il modello venga sottoposto a training:

    - **Parametro singolo**: Scegliere questa opzione se si conosce già come si desidera configurare il modello.

    - **Intervallo parametri**: Selezionare questa opzione se non si è sicuri dei parametri migliori e si desidera eseguire una sweep di parametro. Selezionare un intervallo di valori su cui eseguire l'iterazione e gli [Hyperparametri del modello di ottimizzazione](tune-model-hyperparameters.md) scorrono tutte le possibili combinazioni delle impostazioni fornite per determinare gli iperparametri che producono i risultati ottimali.  

3. **Specifica livello nascosto**: Selezionare il tipo di architettura di rete da creare.

    - **Caso completamente connesso:** selezionare questa opzione per creare un modello utilizzando l'architettura di rete neurale predefinita. Per i modelli di rete neurale multiclasse, i valori predefiniti sono i seguenti:For multiclass neural network models, the defaults are as follows:

        - Un livello nascosto
        - Il livello di output è completamente collegato al livello nascosto.
        - Il livello nascosto è completamente connesso al livello di input.
        - Il numero di nodi nel livello di input è determinato dal numero di funzioni nei dati di training.
        - Il numero di nodi nel livello nascosto può essere impostato dall'utente. Il valore predefinito è 100.
        - Il numero di nodi nel livello di output dipende dal numero di classi.
  
   

5. **Numero di nodi nascosti**: questa opzione consente di personalizzare il numero di nodi nascosti nell'architettura predefinita. Digitare il numero di nodi nascosti. L'impostazione predefinita è un livello nascosto con 100 nodi.

6. **La velocità**di apprendimento : Definire la dimensione del passo effettuato ad ogni iterazione, prima della correzione. Un valore maggiore per la velocità di apprendimento può causare la convergenza del modello più veloce, ma può superare i minimi locali.

7. **Numero di iterazioni di apprendimento:** specificare il numero massimo di volte in cui l'algoritmo deve elaborare i casi di training.

8. **Il diametro iniziale dei pesi**di apprendimento : Specificare i pesi del nodo all'inizio del processo di apprendimento.

9. **Lo slancio**: Specificare un peso da applicare durante l'apprendimento ai nodi delle iterazioni precedenti.
  
11. **Esempi casuali:** selezionare questa opzione per rimescolare i casi tra iterazioni.

    Se si deseleziona questa opzione, i casi vengono elaborati esattamente nello stesso ordine ogni volta che si esegue la pipeline.

12. **Numero casuale di serie**: digitare un valore da utilizzare come valore di serie, se si desidera garantire la ripetibilità tra le esecuzioni della stessa pipeline.

14. Collegare un set di dati di formazione e uno dei moduli di [formazione:](module-reference.md) 

    - Se si imposta **La modalità Crea trainer** su **Parametro singolo**, utilizzare Train [Model](train-model.md).  
  

## <a name="results"></a>Risultati

Al termine dell'allenamento:

- Per salvare un'istantanea del modello sottoposto a training, selezionare la scheda **Uscite** nel pannello destro del modulo **Modulo del modello.** Selezionare l'icona **Registra set di dati** per salvare il modello come modulo riutilizzabile.

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 