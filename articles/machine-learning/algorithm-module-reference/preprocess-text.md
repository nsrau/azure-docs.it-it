---
title: 'Testo pre-elaborazione: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo di testo pre-elaborazione nel servizio Azure Machine Learning per pulire e semplificare il testo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 09/01/2019
ms.openlocfilehash: 4ee5e90b36f7a8fb3bfb42cad425cbb272553b3a
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210832"
---
# <a name="preprocess-text"></a>Preprocess Text

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Usare il modulo di **testo pre-elaborazione** per pulire e semplificare il testo. Supporta le seguenti operazioni comuni di elaborazione del testo:

* Rimozione di parole non significative
* Utilizzo di espressioni regolari per la ricerca e la sostituzione di stringhe di destinazione specifiche
* Lemmatizzazione, che converte più parole correlate in un unico formato canonico
* Normalizzazione maiuscole/minuscole
* Rimozione di determinate classi di caratteri, ad esempio numeri, caratteri speciali e sequenze di caratteri ripetuti come "aaaa"
* Identificazione e rimozione di messaggi di posta elettronica e URL

Il modulo di **testo pre-elaborazione** supporta attualmente solo l'inglese.

## <a name="configure-text-preprocessing"></a>Configurare la pre-elaborazione del testo  

1.  Aggiungere il modulo di **testo di pre-elaborazione** all'esperimento nel servizio Azure Machine Learning. È possibile trovare questo modulo in **analisi del testo**.

1. Connettere un set di dati con almeno una colonna contenente testo.

1. Selezionare la lingua dall'elenco a discesa **lingua** .

1. **Colonna di testo da pulire**: Selezionare la colonna che si desidera pre-elaborare.

1. **Rimuovi parole**non significative: Selezionare questa opzione se si desidera applicare un elenco parola non significativa predefinito alla colonna di testo. 

    Gli elenchi parola non significativa sono dipendenti dalla lingua e personalizzabili.

1. **Lemmatizzazione**: Selezionare questa opzione se si desidera che le parole vengano rappresentate nel formato canonico. Questa opzione è utile per ridurre il numero di occorrenze univoche di token di testo altrimenti simili.

    Il processo lemmatizzazione dipende dalla lingua.

1. **Rileva frasi**: Selezionare questa opzione se si desidera che il modulo inserisca un contrassegno limite frase durante l'esecuzione dell'analisi.

    Questo modulo usa una serie di tre caratteri di `|||` pipe per rappresentare il carattere di terminazione della frase.

1. Eseguire operazioni di ricerca e sostituzione facoltative mediante espressioni regolari.

    * **Espressione regolare personalizzata**: Definire il testo che si sta cercando.
    * **Stringa di sostituzione personalizzata**: Definire un singolo valore di sostituzione.

1. **Normalizza maiuscole**/minuscole: Selezionare questa opzione se si desidera convertire i caratteri ASCII maiuscoli nelle forme minuscole.

    Se i caratteri non vengono normalizzati, la stessa parola in lettere maiuscole e minuscole viene considerata come due parole diverse.

1. È anche possibile rimuovere i seguenti tipi di caratteri o sequenze di caratteri dal testo di output elaborato:

    * **Rimuovere i numeri**: Selezionare questa opzione per rimuovere tutti i caratteri numerici per la lingua specificata. I numeri di identificazione sono dipendenti dal dominio e la lingua. Se i caratteri numerici sono parte integrante di una parola nota, il numero potrebbe non essere rimosso.
    
    * **Rimuovere i caratteri speciali**: Usare questa opzione per rimuovere i caratteri speciali non alfanumerici.
    
    * **Rimuovi caratteri duplicati**: Selezionare questa opzione per rimuovere i caratteri aggiuntivi in qualsiasi sequenza ripetuta per più di due volte. Ad esempio, una sequenza come "aaaaa" verrebbe ridotta a "AA".
    
    * **Rimuovere gli indirizzi di posta elettronica**: Selezionare questa opzione per rimuovere qualsiasi sequenza del formato `<string>@<string>`.  
    * **Rimuovi URL**: Selezionare questa opzione per rimuovere qualsiasi sequenza che includa i prefissi URL seguenti `http`: `https`, `ftp`,,`www`
    
1. **Espandi contrazioni verbo**: Questa opzione si applica solo ai linguaggi che usano le contrazioni di verbi; Attualmente, solo in lingua inglese. 

    Se, ad esempio, si seleziona questa opzione, è possibile sostituire la frase *"non rimanere lì* " con *"non rimanere lì"* .

1. **Normalizzare le barre**rovesciate sulle barre: Selezionare questa opzione per eseguire il mapping di `\\` tutte `/`le istanze di a.

1. **Suddividere i token su caratteri speciali**: Selezionare questa opzione se si desidera interrompere le parole per i caratteri `&`, ad esempio, `-`e così via. Questa opzione può anche ridurre i caratteri speciali quando si ripete più di due volte. 

    La stringa `MS---WORD` , ad esempio, viene suddivisa in tre token `-`, `MS`, e `WORD`.

1. Eseguire l'esperimento.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 