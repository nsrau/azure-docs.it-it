---
title: Generatore di espressioni del flusso di dati del mapping Azure Data Factory
description: Generatore di espressioni per i flussi di dati di mapping Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 872c7ce6a0c39ab19165a5f16ea3e4f6ef8bd6a5
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388043"
---
# <a name="mapping-data-flow-expression-builder"></a>Generatore di espressioni del flusso di dati di mapping



Nel flusso di dati Azure Data Factory mapping sono disponibili caselle espressioni in cui è possibile immettere espressioni per la trasformazione dei dati. Usare le colonne, campi, variabili, parametri, funzioni dal flusso di dati in queste finestre. Per compilare l'espressione, usare il Generatore di espressioni, che viene avviato facendo clic nell'espressione della casella di testo all'interno della trasformazione. A volte saranno visibili anche le opzioni "Colonna calcolata" quando si selezionano le colonne per la trasformazione. Quando si fa clic su una di essa, verrà avviato anche il Generatore di espressioni.

![Generatore di espressioni](media/data-flow/xpb1.png "Generatore di espressioni")

L'opzione predefinita dello strumento Generatore di espressioni è l'editor di testo. la funzionalità di completamento automatico legge l'intero modello a oggetti del flusso di dati di Azure Data Factory con il controllo e l'evidenziazione della sintassi.

![Completamento automatico del generatore di espressioni](media/data-flow/expb1.png "Completamento automatico del generatore di espressioni")

## <a name="build-schemas-in-output-schema-pane"></a>Schemi di compilazione nel riquadro Schema di output

![Aggiungi colonna complessa](media/data-flow/complexcolumn.png "Aggiungi colonne")

Nel riquadro dello schema di output a sinistra verranno visualizzate le colonne che si stanno modificando e aggiungendo allo schema. Qui è possibile compilare in modo interattivo strutture di dati semplici e complesse. Aggiungere ulteriori campi utilizzando "Aggiungi colonna" e compilare gerarchie utilizzando "Aggiungi sottocolonna".

![Aggiungi sottocolonna](media/data-flow/addsubcolumn.png "Aggiungi sottocolonna")

## <a name="data-preview-in-debug-mode"></a>Anteprima dei dati in modalità debug

![Generatore di espressioni](media/data-flow/exp4b.png "Anteprima dati espressione")

Quando si utilizzano le espressioni del flusso di dati, attivare la modalità di debug dall'area di progettazione del flusso di dati Azure Data Factory, abilitando l'anteprima in tempo reale dei risultati dei dati dall'espressione che si sta compilando. Il debug in tempo reale è abilitato per le espressioni.

![Modalità di debug](media/data-flow/debugbutton.png "Pulsante debug")

Fare clic sul pulsante Aggiorna per aggiornare i risultati dell'espressione rispetto a un campione Live dell'origine in tempo reale.

![Generatore di espressioni](media/data-flow/exp5.png "Anteprima dati espressione")

## <a name="comments"></a>Commenti

Aggiungere commenti per le espressioni che usano sintassi dei commenti su una riga singola e su righe multiple:

![Commenti](media/data-flow/comments.png "Commenti")

## <a name="regular-expressions"></a>Espressioni regolari

Linguaggio delle espressioni del flusso di dati di Azure Data Factory, [documentazione di riferimento completa qui](https://aka.ms/dataflowexpressions), abilita le funzioni che includono sintassi delle espressioni regolari. Quando si utilizzano le funzioni di espressione regolare, il generatore di espressioni tenterà di interpretare la barra rovesciata (\\) come sequenza di caratteri di escape. Quando si usano le barre rovesciate nell'espressione regolare, racchiudere l'intera espressione regolare in cicli (\`) o usare una doppia barra rovesciata.

Esempio di utilizzo di tick

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

o della barra doppia

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>Indirizzamento degli indici della matrice

Con funzioni per le espressioni che restituiscono matrici, usare le parentesi quadre [] per indicare indici specifici all'interno di tale oggetto di restituzione della matrice. La matrice è basata sulle unità.

![Matrice generatore di espressioni](media/data-flow/expb2.png "Anteprima dati espressione")

## <a name="handling-names-with-special-characters"></a>Gestione dei nomi con caratteri speciali

Quando sono presenti nomi di colonne che includono caratteri speciali o spazi, racchiudere il nome tra parentesi graffe.
* ```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>Passaggi successivi

[Inizia a creare espressioni di trasformazione dei dati](data-flow-expression-functions.md)
