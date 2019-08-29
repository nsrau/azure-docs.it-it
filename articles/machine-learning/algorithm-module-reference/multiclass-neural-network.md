---
title: 'Rete neurale multiclasse: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo di rete neurale multiclasse nel servizio Azure Machine Learning per creare un modello di rete neurale che può essere usato per stimare una destinazione con più valori.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 44d1e7606efd5bc6d2286254dc4863728e3edbfd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128607"
---
# <a name="multiclass-neural-network-module"></a>Modulo di rete neurale multiclasse

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Usare questo modulo per creare un modello di rete neurale che può essere usato per stimare una destinazione con più valori. 

Ad esempio, le reti neurali di questo tipo possono essere utilizzate in attività complesse di visione artificiale, ad esempio il riconoscimento di cifre o lettere, la classificazione dei documenti e il riconoscimento del modello.

La classificazione con reti neurali è un metodo di apprendimento supervisionato e pertanto richiede un *set di dati con tag* che include una colonna di etichetta.

Per eseguire il training del modello, è possibile fornire il modello e il set di dati con tag come input per il [training del modello](./train-model.md). Il modello con Training può quindi essere usato per stimare i valori per i nuovi esempi di input.  

## <a name="about-neural-networks"></a>Informazioni sulle reti neurali

Una rete neurale è un set di livelli interconnessi. Gli input sono il primo livello e sono connessi a un livello di output da un grafico aciclici costituito da bordi ponderati e nodi.

Tra i livelli di input e di output è possibile inserire più livelli nascosti. La maggior parte delle attività predittive può essere eseguita facilmente con solo uno o pochi livelli nascosti. Tuttavia, la ricerca recente ha dimostrato che le reti neurali profonde (DNN) con molti livelli possono essere efficaci in attività complesse, ad esempio il riconoscimento vocale o di immagine. I livelli successivi vengono usati per modellare livelli crescenti di profondità semantica.

La relazione tra input e output viene appresa dal training della rete neurale sui dati di input. La direzione del grafico prosegue dagli input attraverso il livello nascosto e il livello di output. Tutti i nodi di un livello sono connessi con i bordi ponderati ai nodi nel livello successivo.

Per calcolare l'output della rete per un input specifico, viene calcolato un valore in ogni nodo nei livelli nascosti e nel livello di output. Il valore viene impostato calcolando la somma ponderata dei valori dei nodi del livello precedente. Una funzione di attivazione viene quindi applicata a tale somma ponderata.

## <a name="configure-multiclass-neural-network"></a>Configurare la rete neurale multiclasse

1. Aggiungere il modulo di **rete neurale multiclasse** all'esperimento nell'interfaccia. È possibile trovare questo modulo in **Machine Learning**, **Initialize**, nella categoria **classificazione** .

2. **Crea modalità trainer**: Utilizzare questa opzione per specificare il modo in cui si desidera eseguire il training del modello:

    - **Singolo parametro**: Scegliere questa opzione se si conosce già il modo in cui si desidera configurare il modello.

    

3. **Specifica del livello nascosto**: Consente di selezionare il tipo di architettura di rete da creare.

    - **Caso con connessione completa**: Selezionare questa opzione per creare un modello utilizzando l'architettura di rete neurale predefinita. Per i modelli di rete neurale multiclasse, i valori predefiniti sono i seguenti:

        - Un livello nascosto
        - Il livello di output è completamente connesso al livello nascosto.
        - Il livello nascosto è completamente connesso al livello di input.
        - Il numero di nodi nel livello di input è determinato dal numero di funzioni nei dati di training.
        - Il numero di nodi nel livello nascosto può essere impostato dall'utente. Il valore predefinito è 100.
        - Il numero di nodi nel livello di output dipende dal numero di classi.
  
   

5. **Numero di nodi nascosti**: Questa opzione consente di personalizzare il numero di nodi nascosti nell'architettura predefinita. Digitare il numero di nodi nascosti. Il valore predefinito è un livello nascosto con nodi 100.

6. **Velocità di apprendimento**: Definire le dimensioni del passaggio effettuato a ogni iterazione, prima della correzione. Un valore più grande per la velocità di apprendimento può comportare una maggiore velocità di convergenza del modello, ma è possibile che venga superata la minima locale.

7. **Numero di iterazioni di apprendimento**: Specificare il numero massimo di volte in cui l'algoritmo deve elaborare i case di training.

8. **Il diametro iniziale dei pesi di apprendimento**: Specificare i pesi del nodo all'inizio del processo di apprendimento.

9. **Il momento**: Specificare un peso da applicare durante l'apprendimento ai nodi dalle iterazioni precedenti.
  
11. **Esempi di riproduzione casuale**: Selezionare questa opzione per riprodurre in modo casuale i casi tra le iterazioni.

    Se si deseleziona questa opzione, i case vengono elaborati esattamente nello stesso ordine ogni volta che si esegue l'esperimento.

12. **Valore di inizializzazione numero casuale**: Digitare un valore da usare come valore di inizializzazione, se si vuole garantire la ripetibilità tra le esecuzioni dello stesso esperimento.

14. Connettere un set di dati di training e uno dei [moduli di training](module-reference.md): 

    - Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, usare [Train Model](train-model.md).  
  

## <a name="results"></a>Risultati

Al termine del training:

- Per visualizzare un riepilogo dei parametri del modello, insieme ai pesi della funzionalità appresi dal training e ad altri parametri della rete neurale, fare clic con il pulsante destro del mouse sull'outputdi [Train Model](./train-model.md) e scegliere Visualize.  

- Per salvare uno snapshot del modello con training, fare clic con il pulsante destro del mouse sull'output del **modello** sottoposto a training e selezionare **Salva come modello con training**. Questo modello non viene aggiornato nelle esecuzioni successive dello stesso esperimento.


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 