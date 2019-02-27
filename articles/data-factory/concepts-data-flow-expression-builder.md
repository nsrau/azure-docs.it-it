---
title: Generatore di espressioni dei flussi di dati di mapping di Azure Data Factory
description: Generatore di espressioni per i flussi di dati di mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: dab0a6a5eee8893f28a221b44d57afe255841fa0
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2019
ms.locfileid: "56329746"
---
# <a name="mapping-data-flow-expression-builder"></a>Generatore di espressioni dei flussi di dati di mapping

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Nel flusso di dati di mapping di Azure Data Factory, sono disponibili finestre di espressioni in cui è possibile immettere espressioni per la trasformazione dei dati. Usare le colonne, campi, variabili, parametri, funzioni dal flusso di dati in queste finestre. Per compilare l'espressione, usare il Generatore di espressioni, che viene avviato facendo clic nell'espressione della casella di testo all'interno della trasformazione. A volte saranno visibili anche le opzioni "Colonna calcolata" quando si selezionano le colonne per la trasformazione. Quando si fa clic su una di essa, verrà avviato anche il Generatore di espressioni.

![Generatore di espressioni](media/data-flow/expression.png "Generatore di espressioni")

L'opzione predefinita dello strumento Generatore di espressioni è l'editor di testo. la funzionalità di completamento automatico legge l'intero modello a oggetti del flusso di dati di Azure Data Factory con il controllo e l'evidenziazione della sintassi.

![Completamento automatico del Generatore di espressioni](media/data-flow/expb1.png "Completamento automatico del Generatore di espressioni")

## <a name="currently-working-on-field"></a>Operazione attualmente in corso sul campo

![Generatore di espressioni](media/data-flow/exp3.png "attualmente in corso su")

Nella parte superiore sinistra dell'interfaccia utente del Generatore di espressioni, si noterà un campo denominato "Attualmente in corso su" con il nome del campo sul quale si sta attualmente lavorando. L'espressione compilata nell'interfaccia utente verrà applicata solo al campo attualmente attivo. Se si desidera trasformare un altro campo, salvare il lavoro corrente e usare questo elenco a discesa per selezionare un altro campo e compilare un'espressione per gli altri campi.

## <a name="data-preview-in-debug-mode"></a>Anteprima dei dati in modalità di Debug

![Generatore di espressioni](media/data-flow/exp4b.png "Anteprima dei dati dell'espressione")

Quando si lavora sulle espressioni è possibile, facoltativamente, attivare la modalità di Debug dall'area di progettazione del flusso di dati di Azure Data Factory, abilitando l'anteprima in corso in tempo reale dei risultati dei dati dall'espressione che si sta compilando. Il debug in tempo reale è abilitato per le espressioni.

![Modalità di debug](media/data-flow/debugbutton.png "pulsante di Debug")


![Generatore di espressioni](media/data-flow/exp5.png "Anteprima dei dati dell'espressione")

## <a name="comments"></a>Commenti

Aggiungere commenti per le espressioni che usano sintassi dei commenti su una riga singola e su righe multiple:

![Commenti](media/data-flow/comments.png "Commenti")

## <a name="regular-expressions"></a>Espressioni regolari

Linguaggio delle espressioni del flusso di dati di Azure Data Factory, [documentazione di riferimento completa qui](http://aka.ms/dataflowexpressions), abilita le funzioni che includono sintassi delle espressioni regolari. Quando si usano funzioni di espressione regolare, il Generatore di espressioni tenterà di interpretare la barra rovesciata (\) come una sequenza di caratteri di escape. Quando si usano le barre rovesciate nell'espressione regolare, racchiudere l'espressione regolare intera espressa in tick ` ` oppure usare una doppia barra rovesciata.

Esempio di utilizzo di tick

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

o della barra doppia

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>Indirizzamento degli indici di matrice

Con funzioni per le espressioni che restituiscono matrici, usare le parentesi quadre [] per indicare indici specifici all'interno di tale oggetto di restituzione della matrice. La matrice è basata sulle unità.

![Matrice del Generatore di espressioni](media/data-flow/expb2.png "Anteprima dei dati dell'espressione")
