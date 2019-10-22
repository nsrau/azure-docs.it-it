---
title: 'Split Data: riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Split data nel servizio Azure Machine Learning per dividere un set di dati in due set distinti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 3fa6b61b1b1761756339d6a7f05c74eb4a968777
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692660"
---
# <a name="split-data-module"></a>Modulo Split data

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Utilizzare questo modulo per dividere un set di dati in due set distinti.

Questo modulo è particolarmente utile quando è necessario separare i dati in set di training e di testing. È possibile personalizzare anche il modo in cui i dati vengono divisi. Alcune opzioni supportano la sequenza casuale dei dati; altre sono personalizzate per un tipo di dati o un tipo di modello specifico.

## <a name="how-to-configure"></a>Come configurare

> [!TIP]
> Prima di scegliere la modalità di suddivisione, leggere tutte le opzioni per determinare il tipo di suddivisione necessario.
> Se si modifica la modalità di suddivisione, tutte le altre opzioni potrebbero essere reimpostate.

1. Aggiungere il modulo **Split data** alla pipeline nell'interfaccia. È possibile trovare questo modulo in **trasformazione dati**, nella categoria **esempio e divisione** .

2. **Modalità di suddivisione**: scegliere una delle modalità seguenti, a seconda del tipo di dati disponibili e di come si desidera suddividerla. Ogni modalità di suddivisione ha opzioni diverse. Per istruzioni dettagliate ed esempi, fare clic sugli argomenti seguenti. 

    - **Dividi righe**: usare questa opzione se si vogliono dividere solo i dati in due parti. È possibile specificare la percentuale di dati da inserire in ogni divisione, ma per impostazione predefinita i dati sono divisi 50-50.

        È anche possibile ordinare in modo casuale la selezione delle righe in ogni gruppo e usare il campionamento stratificato. Per il campionamento stratificato, è necessario selezionare una singola colonna di dati per cui si desidera che i valori vengano ripartiti equamente tra i due set di dati del risultato.  

    - **Suddivisione espressione regolare**  Scegliere questa opzione quando si desidera dividere il set di dati testando una singola colonna per un valore.

        Se ad esempio si analizzano i sentimenti, è possibile verificare la presenza di un determinato nome di prodotto in un campo di testo, quindi dividere il set di dati in righe con il nome del prodotto di destinazione e quelli senza.

    - **Suddivisione espressione relativa**: usare questa opzione quando si vuole applicare una condizione a una colonna numerica. Il numero può essere un campo di data/ora, una colonna contenente importi di età o dollaro o addirittura una percentuale. È ad esempio possibile dividere il set di dati in base al costo degli elementi, raggruppare le persone in base all'intervallo di età o separare i dati in base a una data di calendario.

### <a name="split-rows"></a>Suddividere le righe
1.  Aggiungere il modulo [Split data](./split-data.md) alla pipeline nell'interfaccia e connettere il set di dati che si desidera suddividere.
  
2.  Per la **modalità di suddivisione**, scegliere **Dividi righe**. 

3.  **Frazione di righe nel primo set di dati di output**. Usare questa opzione per determinare il numero di righe che vengono inserite nel primo output (a sinistra). Tutte le altre righe verranno indirizzate al secondo output (destro).

    Il rapporto rappresenta la percentuale di righe inviate al primo set di dati di output, quindi è necessario digitare un numero decimale compreso tra 0 e 1.
     
     Se, ad esempio, si digita 0,75 come valore, il set di dati verrebbe suddiviso usando un rapporto 75:25, con 75% delle righe inviate al primo set di dati di output e il 25% inviato al secondo set di dati di output.
  
4. Selezionare l'opzione **suddivisione casuale** se si desidera eseguire la selezione casuale dei dati nei due gruppi. Questa è l'opzione preferita per la creazione di set di risultati di training e di test.

5.  **Seed casuale**: digitare un valore integer non negativo per inizializzare la sequenza di istanze di pseudocasuale da usare. Questo valore di inizializzazione predefinito viene usato in tutti i moduli che generano numeri casuali. 

     La specifica di un valore di inizializzazione rende i risultati generalmente riproducibili. Se è necessario ripetere i risultati di un'operazione Split, è necessario specificare un valore di inizializzazione per il generatore di numeri casuali. In caso contrario, il valore di inizializzazione casuale viene impostato su 0 per impostazione predefinita, il che significa che il valore di inizializzazione iniziale viene ottenuto dal clock di sistema. Di conseguenza, la distribuzione dei dati potrebbe essere leggermente diversa a ogni esecuzione di una divisione. 

6. **Suddivisione stratificata**: impostare questa opzione su **true** per assicurarsi che i due set di risultati di output contengano un campione rappresentativo dei valori nella *colonna Strata* o nella *colonna chiave di stratificazione*. 

    Con il campionamento stratificato, i dati vengono divisi in modo che ogni set di dati di output ottenga approssimativamente la stessa percentuale di ogni valore di destinazione Ad esempio, potrebbe essere necessario assicurarsi che i set di training e di testing siano approssimativamente bilanciati rispetto al risultato oppure, in relazione ad altre colonne, ad esempio il sesso.

7. Eseguire la pipeline.


## <a name="regular-expression-split"></a>Suddivisione espressione regolare

1.  Aggiungere il modulo [Split data](./split-data.md) alla pipeline e connetterlo come input al set di dati che si desidera suddividere.  
  
2.  Per la **modalità di suddivisione**selezionare **espressione regolare divisa**.

3. Nella casella **espressione regolare** Digitare un'espressione regolare valida. 
  
   L'espressione regolare deve seguire la sintassi delle espressioni regolari di Python.


4. Eseguire la pipeline.

    In base all'espressione regolare fornita, il set di dati è suddiviso in due set di righe: righe con valori corrispondenti all'espressione e tutte le righe rimanenti. 

## <a name="relative-expression-split"></a>Suddivisione espressione relativa.

1. Aggiungere il modulo [Split data](./split-data.md) alla pipeline e connetterlo come input al set di dati che si desidera suddividere.
  
2. Per la **modalità di suddivisione**selezionare **espressione relativa divisa**.
  
3. Nella casella di testo **espressione relazionale** Digitare un'espressione che esegue un'operazione di confronto in una singola colonna:


 - Colonna numerica:
    - La colonna contiene numeri di qualsiasi tipo di dati numerico, inclusi i tipi di dati di data/ora.

    - L'espressione può fare riferimento a un massimo di un nome di colonna.

    - Usare il carattere e commerciale (&) per l'operazione AND e usare il carattere barra verticale (|) per l'operazione OR.

    - Sono supportati gli operatori seguenti: `<`, `>`, `<=`, `>=`, `==` `!=`

    - Non è possibile raggruppare le operazioni usando `(` e `)`.

 - Colonna stringa: 
    - Sono supportati gli operatori seguenti: `==`, `!=`



4. Eseguire la pipeline.

    L'espressione divide il set di dati in due set di righe: righe con valori che soddisfano la condizione e tutte le righe rimanenti.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 