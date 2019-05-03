---
title: 'Divisione dei dati: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Split Data nel servizio Azure Machine Learning per dividere un set di dati in due set distinti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7395280ed9a2e9dcb94a081f0b3bf10a28da719
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029401"
---
# <a name="split-data-module"></a>Modulo Split Data

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per dividere un set di dati in due set distinti.

Questo modulo è particolarmente utile quando è necessario separare i dati in set di training e set di testing. È possibile personalizzare il modo che i dati vengono divisi anche. Alcune opzioni di supportano della sequenza casuale dei dati. altre sono specifiche per un determinato tipo di dati o un tipo di modello.

## <a name="how-to-configure"></a>Come configurare

> [!TIP]
> Prima di scegliere la modalità di suddivisione, leggere tutte le opzioni per determinare il tipo di divisione che è necessario.
> Se si modifica la modalità di suddivisione, è possibile reimpostare tutte le altre opzioni.

1. Aggiungere il **Split Data** modulo all'esperimento nell'interfaccia. È possibile trovare questo modulo sotto **Data Transformation**, nella **Sample and Split** categoria.

2. **Modalità di suddivisione**: Scegliere una delle modalità seguenti, a seconda del tipo di dati che disponibili e la modalità di divisione. Ogni modalità di suddivisione ha opzioni diverse. Fare clic sugli argomenti seguenti per istruzioni dettagliate ed esempi. 

    - **Suddividere le righe**: Usare questa opzione se si desidera dividere i dati in due parti. È possibile specificare la percentuale di dati da inserire in ciascuna suddivisione, ma per impostazione predefinita, i dati vengano divisi 50-50.

        È anche possibile impostare in modo casuale la selezione delle righe in ogni gruppo e usare il campionamento stratificato. In un campionamento stratificato, è necessario selezionare una singola colonna di dati per il quale si desidera che i valori per essere ripartite in modo uniforme tra i set di dati di due risultati.  

    - **Suddivisione di espressione regolare** scegliere questa opzione se si desidera dividere il set di dati testando una singola colonna per un valore.

        Ad esempio, se si effettua un'analisi del sentiment, è possibile verificare la presenza di un nome di prodotto specifico in un campo di testo e dividere il set di dati le righe con il nome di prodotto di destinazione e quelli senza.

    - **Espressione relativa suddivisione**:  Usare questa opzione ogni volta che si desidera applicare una condizione a una colonna numerica. Il numero potrebbe essere un campo Data/ora, età contenente una colonna o quantità di denaro o persino una percentuale. Ad esempio, è possibile dividere il set di dati in base al costo degli elementi, raggruppare le persone da intervalli di età o dati separati da una data del calendario.

### <a name="split-rows"></a>Suddividere le righe
1.  Aggiungere il [Split Data](./split-data.md) all'esperimento nell'interfaccia del modulo e connettere il set di dati da suddividere.
  
2.  Per la **Splitting mode**, scegliere **suddividere le righe**. 

3.  **Frazione di righe nel primo set di dati di output**. Usare questa opzione per determinare il numero di righe inserito il primo output (a sinistra). Tutte le altre righe entra nel secondo output (destra).

    La percentuale rappresenta la percentuale di righe inviate al primo set di dati di output, pertanto è necessario digitare un numero decimale compreso tra 0 e 1.
     
     Ad esempio, se si digita 0,75 come valore, il set di dati viene suddiviso con un rapporto 75:25, 75% delle righe viene inviato al primo set di dati di output e il 25% per il secondo set di dati di output.
  
4. Selezionare il **suddivisione casuale** opzione se si desidera impostare in modo casuale di selezione dei dati in due gruppi. Questo è l'opzione preferita durante la creazione di set di dati di training e test.

5.  **Valore di inizializzazione casuale**: Digitare un valore intero non negativo per inizializzare la sequenza pseudocasuale di istanze da usare. Questo valore di inizializzazione predefinito viene usato in tutti i moduli che generano numeri casuali. 

     Specificare un valore di inizializzazione risulta i risultati generalmente riproducibili. Se è necessario ripetere i risultati di un'operazione split, è necessario specificare un valore di inizializzazione per il generatore di numeri casuali. In caso contrario, il valore di inizializzazione casuale è impostato per impostazione predefinita su 0, ovvero che il valore di inizializzazione iniziale viene ottenuto dal clock di sistema. Di conseguenza, la distribuzione dei dati potrebbe essere leggermente diversa ogni volta che si esegue una divisione. 

6. **Suddivisione stratificata**: Impostare questa opzione su **True** per garantire che i set di dati di due output contengano un campione rappresentativo dei valori nel *colonna degli strati* oppure *colonna chiave di stratificazione*. 

    Con il campionamento stratificato, i dati vengono divisi in modo che ogni set di dati di output Ottiene approssimativamente la stessa percentuale di ogni valore di destinazione. Si potrebbe ad esempio, per assicurarsi che il training e set di testing all'incirca siano bilanciati per quanto riguarda il risultato o con considerano ot ad altre colonne quali gender.

7. Eseguire l'esperimento.


## <a name="regular-expression-split"></a>Suddivisione di espressioni regolari

1.  Aggiungere il [Split Data](./split-data.md) modulo nell'esperimento e connetterli come input per il set di dati da suddividere.  
  
2.  Per la **Splitting mode**, selezionare **suddivisione di espressioni regolari**.

3. Nel **espressione regolare** , digitare un'espressione regolare valida. 
  
   L'espressione regolare deve seguire la sintassi di espressione regolare di Python.


4. Eseguire l'esperimento.

    In base all'espressione regolare è fornire, il set di dati è suddiviso in due set di righe: le righe con valori corrispondono all'espressione e tutte le righe rimanenti. 

## <a name="relative-expression-split"></a>Espressione relativa suddivisione.

1. Aggiungere il [Split Data](./split-data.md) modulo nell'esperimento e connetterli come input per il set di dati da suddividere.
  
2. Per la **Splitting mode**, selezionare **espressione relativa suddivisione**.
  
3. Nel **espressione relazionale** casella di testo, digitare un'espressione che esegue un'operazione di confronto, per una singola colonna:


 - Colonna numerica:
    - La colonna contiene numeri di qualsiasi tipo di dati numerici, inclusi i tipi di dati Data/ora.

    - L'espressione può fare riferimento a un massimo di un nome di colonna.

    - Usare il carattere e commerciale (&) per l'operazione AND e usare il carattere barra verticale (|) per l'operazione.

    - Sono supportati gli operatori seguenti: `<`, `>`, `<=`, `>=`, `==`, `!=`

    - È possibile raggruppare le operazioni usando `(` e `)`.

 - Colonna di tipo stringa: 
    - Sono supportati gli operatori seguenti: `==`, `!=`



4. Eseguire l'esperimento.

    L'espressione divide il set di dati in due set di righe: le righe con valori che soddisfano la condizione e tutte le righe rimanenti.

## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 