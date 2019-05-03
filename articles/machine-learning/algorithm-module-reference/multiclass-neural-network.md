---
title: 'Rete neurale multiclasse: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Multiclass Neural Network nel servizio Azure Machine Learning per creare un modello di rete neurale utilizzabile per stimare una destinazione contenente più valori.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7bef225c001ebd9bbb9a45c8bcc301cfb49edb6
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029386"
---
# <a name="multiclass-neural-network-module"></a>Modulo di rete neurale multiclasse

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per creare un modello di rete neurale utilizzabile per stimare una destinazione contenente più valori. 

Ad esempio, le reti neurali di questo tipo potrebbero essere usate nelle attività di visione artificiale complesse, ad esempio riconoscimento di cifre o lettere, classificazione di documenti e riconoscimento del modello.

Classificazione con reti neurali è un metodo di apprendimento supervisionato e pertanto richiede un *set di dati con tag* che include una colonna di etichetta.

È possibile eseguirne il training, fornendo come input per il modello e il set di dati con tag [Train Model](./train-model.md). Il modello con training può quindi utilizzabile per stimare i valori per i nuovi esempi di input.  

## <a name="about-neural-networks"></a>Sulle reti neurali

Una rete neurale è un set di livelli interconnessi. Gli input sono il primo livello e sono connessi a un livello di output da un grafo aciclico diretto, costituito da nodi e bordi ponderati.

Tra i livelli di input e outpui è possibile inserire più livelli nascosti. Più attività di stima può essere eseguita facilmente con solo una o più livelli nascosti. Tuttavia, ricerche recenti hanno evidenziato che le reti neurali profonde (DNN) con molti livelli possono risultare efficaci in attività complesse, quali il riconoscimento vocale o di immagini. I livelli successivi vengono usati per modellare livelli con maggiore profondità semantica.

La relazione tra input e output verrà acquisita dal training della rete neurale sui dati di input. La direzione del grafo procede dagli input attraverso il livello nascosto e livello di output. Tutti i nodi in un livello sono connessi tramite vertici ponderati ai nodi nel livello successivo.

Per calcolare l'output della rete per un particolare input, viene calcolato un valore in ogni nodo nei livelli nascosti e nel livello di output. Il valore viene impostato calcolando la somma ponderata dei valori dei nodi del livello precedente. Una funzione di attivazione viene quindi applicata a tale somma ponderata.

## <a name="configure-multiclass-neural-network"></a>Configurare una rete neurale Multiclasse

1. Aggiungere il **MultiClass Neural Network** modulo all'esperimento nell'interfaccia. È possibile trovare questo modulo sotto **Machine Learning**, **inizializzare**, nelle **classificazione** categoria.

2. **Creare in modalità trainer**: Usare questa opzione per specificare come si desidera che il modello di esecuzione del training:

    - **Singolo parametro**: Scegliere questa opzione se si conosce già la modalità con cui si desidera configurare il modello.

    

3. **Specifica del livello nascosto**: Selezionare il tipo di architettura di rete da creare.

    - **Completamente connesso case**: Selezionare questa opzione per creare un modello tramite l'architettura di rete neurale predefinita. Per i modelli di rete neurale multiclasse, i valori predefiniti sono i seguenti:

        - Un livello nascosto
        - Il livello di output è completamente connesso al livello nascosto.
        - Il livello nascosto è completamente connesso al livello di input.
        - Il numero di nodi nel livello di input è determinato dal numero di funzioni nei dati di training.
        - Il numero di nodi nel livello nascosto può essere impostato dall'utente. Il valore predefinito è 100.
        - Il numero di nodi nel livello di output dipende dal numero di classi.
  
   

5. **Numero di nodi nascosti**: Questa opzione consente di personalizzare il numero di nodi nascosti nell'architettura predefinita. Digitare il numero di nodi nascosti. Il valore predefinito è un livello nascosto con 100 nodi.

6. **La velocità di apprendimento**: Definire le dimensioni del passaggio eseguito a ogni iterazione, prima della correzione. Un valore maggiore per la velocità di apprendimento può causare il modello di convergenza più rapida, ma può eccedere i valori minimi locali.

7. **Numero di iterazioni di apprendimento**: Specificare il numero massimo di volte in cui che l'algoritmo deve elaborare i case di training.

8. **Consente di ponderare l'apprendimento iniziale diametro**: Specificare i pesi dei nodi all'inizio del processo di apprendimento.

9. **L'espansione**: Specificare un peso da applicare durante l'apprendimento ai nodi dalle iterazioni precedenti.
  
11. **Esempi di riproduzione casuale**: Selezionare questa opzione per riprodurre casualmente casi tra due iterazioni.

    Se si deseleziona questa opzione, casi vengono elaborati nello stesso ordine ogni volta che si esegue l'esperimento.

12. **Valore di inizializzazione di numeri casuali**: Digitare un valore da usare come valore di inizializzazione, se si vuole assicurare la ripetibilità nelle esecuzioni dello stesso esperimento.

14. Connettere un set di dati di training e uno dei [moduli di training](module-reference.md): 

    - Se si imposta **modalità di creazione trainer** al **singolo parametro**, usare [Train Model](train-model.md).  
  

## <a name="results"></a>Risultati

Dopo aver completata la formazione:

- Per visualizzare un riepilogo dei parametri del modello, con la funzionalità di pesi appreso dal training e altri parametri della rete neurale, fare doppio clic sull'output del [Train Model](./train-model.md) e selezionare **Visualize**.  

- Per salvare uno snapshot del modello con training, fare doppio clic il **Trained model** di output e selezionare **Salva come modello sottoposto a training**. Questo modello non viene aggiornato nelle esecuzioni successive dello stesso esperimento.


## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 