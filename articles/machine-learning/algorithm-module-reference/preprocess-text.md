---
title: 'Testo di pre-elaborazione: riferimento modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Pre-process Text in Azure Machine Learning per pulire e semplificare il testo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: 6e4d4c8f798418e090caeba091dec33c71f0458f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477494"
---
# <a name="preprocess-text"></a>Preprocess Text

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Utilizzare il modulo **Pre-elaborazione del testo** per pulire e semplificare il testo. Supporta queste operazioni comuni di elaborazione del testo:It supports these common text processing operations:

* Rimozione delle parole stop
* Utilizzo di espressioni regolari per cercare e sostituire stringhe di destinazione specificheUsing regular expressions to search for and replace specific target strings
* Lemmatizzazione, che converte più parole correlate in una singola forma canonica
* Normalizzazione dei casi
* Rimozione di alcune classi di caratteri, ad esempio numeri, caratteri speciali e sequenze di caratteri ripetuti, ad esempio "aaaa"
* Identificazione e rimozione di e-mail e URL

Il modulo **Pre-process Text** attualmente supporta solo l'inglese.

## <a name="configure-text-preprocessing"></a>Configurare la pre-elaborazione del testo  

1.  Aggiungere il modulo di testo di **pre-elaborazione** alla pipeline in Azure Machine Learning.Add the Preprocess Text module to your pipeline in Azure Machine Learning. È possibile trovare questo modulo in **Analisi del testo**.

1. Connettere un set di dati con almeno una colonna contenente testo.

1. Selezionare la lingua dall'elenco a discesa **Lingua.**

1. **Colonna di testo da pulire:** selezionare la colonna che si desidera pre-elaborare.

1. **Rimuovi parole non significative**: Selezionare questa opzione se si desidera applicare un elenco di parole non significative predefinito alla colonna di testo. 

    Gli elenchi di parole non in ordine popup dipendono dalla lingua e sono personalizzabili.

1. **Lemmatizzazione**: Selezionare questa opzione se si desidera che le parole siano rappresentate nella loro forma canonica. Questa opzione è utile per ridurre il numero di occorrenze univoche di token di testo altrimenti simili.

    Il processo di lemmatizzazione dipende fortemente dalla lingua..

1. **Rileva frasi**: Selezionare questa opzione se si desidera che il modulo inserisca un contrassegno del contorno della frase durante l'analisi.

    Questo modulo utilizza una serie `|||` di tre caratteri barra verticale per rappresentare il terminatore di frase.

1. Eseguire operazioni facoltative di ricerca e sostituzione utilizzando espressioni regolari.

    * **Espressione regolare personalizzata**: Definire il testo che si sta cercando.
    * **Stringa di sostituzione personalizzata**: Definire un singolo valore di sostituzione.

1. **Normalizza maiuscole/minuscole:** selezionare questa opzione se si desidera convertire i caratteri maiuscoli ASCII nelle relative maschere minuscole.

    Se i caratteri non vengono normalizzati, la stessa parola in lettere maiuscole e minuscole viene considerata due parole diverse.

1. È inoltre possibile rimuovere i seguenti tipi di caratteri o sequenze di caratteri dal testo di output elaborato:

    * **Rimuovi numeri**: Selezionare questa opzione per rimuovere tutti i caratteri numerici per la lingua specificata. I numeri di identificazione dipendono dal dominio e dipendono dalla lingua. Se i caratteri numerici sono parte integrante di una parola nota, il numero potrebbe non essere rimosso.
    
    * **Rimuovi caratteri speciali**: utilizzare questa opzione per rimuovere eventuali caratteri speciali non alfanumerici.
    
    * **Rimuovi caratteri duplicati**: Selezionare questa opzione per rimuovere i caratteri aggiuntivi in tutte le sequenze che si ripetono per più di due volte. Ad esempio, una sequenza come "aaaaa" verrebbe ridotta a "aa".
    
    * **Rimuovi indirizzi e-mail**: Selezionare questa `<string>@<string>`opzione per rimuovere qualsiasi sequenza del formato .  
    * **Rimuovi URL**: Selezionare questa opzione per rimuovere qualsiasi sequenza `http` `https`che `ftp`includa i seguenti prefissi URL: , , ,`www`
    
1. **Espandi contrazioni verbali**: questa opzione si applica solo alle lingue che utilizzano contrazioni verbali; attualmente, solo in inglese. 

    Ad esempio, selezionando questa opzione, è possibile sostituire la frase *"non ci starebbe"* con *"non ci starebbe".*

1. **Normalizza barre rovesciate in barre**: selezionare `\\` questa `/`opzione per mappare tutte le istanze di a .

1. **Dividi token su caratteri speciali:** selezionare questa opzione `&`se `-`si desidera interrompere le parole su caratteri quali , e così via. Questa opzione può anche ridurre i caratteri speciali quando si ripete più di due volte. 

    Ad esempio, `MS---WORD` la stringa verrebbe suddivisa `MS` `-`in `WORD`tre token, , e .

1. Inviare la pipeline.

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 