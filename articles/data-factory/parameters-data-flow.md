---
title: Parametri del flusso di dati di mapping di Azure Data Factory
description: Informazioni su come impostare i parametri per un flusso di dati di mapping dalla pipeline di data factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: af5f421cc3802f3a7ad44bb294f5066c32569f8b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082885"
---
# <a name="mapping-data-flow-parameters"></a>Parametri del flusso di dati di mapping

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Mapping dei dati dei flussi in data factory di supportano l'uso dei parametri. È possibile definire i parametri all'interno di definizione del flusso di dati, è quindi possibile usare in tutta le espressioni. I parametri possono quindi essere impostati dalla pipeline chiama tramite l'attività di esecuzione del flusso di dati. Sono disponibili tre opzioni da utilizzare per impostare i valori nel flusso di dati con le espressioni di attività:

* Usare il linguaggio delle espressioni della pipeline del flusso controllo per impostare un valore dinamico
* Usare il linguaggio delle espressioni del flusso dei dati per impostare un valore dinamico
* Usare entrambi i linguaggi di espressione per impostare un valore letterale statico

Usare questa funzionalità per rendere i flussi di dati per utilizzo generico, flessibile e riutilizzabile. È possibile parametrizzare le impostazioni del flusso di dati e le espressioni con questi parametri.

> [!NOTE]
> Per usare espressioni del flusso di controllo della pipeline, il parametro di flusso di dati deve essere di tipo stringa.

* Aggiungere un'attività di esecuzione del flusso di dati all'area di disegno della pipeline.
* Se il flusso di dati include parametri, verrà visualizzato l'elenco dei parametri disponibili in tab.* di parametri * fare clic sulla casella di testo accanto a ogni parametro di immettere il valore del parametro.
* È possibile scegliere di creare l'espressione per i parametri tramite il linguaggio delle espressioni della pipeline del flusso controllo o espressioni del flusso di dati.

![Parametri di 3 del flusso di dati](media/data-flow/params3.png "3 parametri del flusso di dati")

## <a name="create-parameters-in-data-flow"></a>Creare i parametri nel flusso di dati

![I parametri 1 del flusso di dati](media/data-flow/params1.png "parametri 1 del flusso di dati")

Per aggiungere parametri al flusso di dati, fare clic sulla parte vuota dell'area di disegno per visualizzare le proprietà generali del flusso dei dati. Nel riquadro impostazioni, verrà visualizzata una scheda denominata parametri. Fare clic sul pulsante nuovo per generare nuovi parametri, che quindi possono essere impostati dalla pipeline, il passaggio di valori nel flusso di dati. Immettere un nome di parametro e selezionare il tipo di dati per ogni parametro.

All'interno di espressioni del flusso di dati, è possibile usare i parametri con valori impostati dalla pipeline. I parametri iniziano con $ e non sono modificabili. Troverai anche l'elenco di parametri disponibili all'interno del generatore di espressioni nella scheda parametri. È possibile usare questi valori all'interno di espressioni, anche se non si possono assegnare nuovi valori ai parametri.

![I parametri da 2 del flusso di dati](media/data-flow/params2.png "parametri 2 del flusso di dati")

## <a name="set-data-flow-parameters-from-pipeline"></a>Impostare i parametri del flusso di dati dalla pipeline

Dopo aver creato il flusso di dati con parametri, è ora possibile eseguire quel flusso di dati da una pipeline con l'eseguire attività del flusso di dati. Dopo aver aggiunto tale attività all'area di disegno di progettazione della pipeline, i parametri di flusso nei parametri dell'attività impostazione scheda verrà visualizzata con i dati disponibili.

![Linguaggio delle espressioni i parametri del flusso di dati](media/data-flow/params4.png "linguaggio delle espressioni i parametri del flusso di dati")

Quando si fa clic nella casella di testo per i valori dei parametri da compilare, verrà visualizzato con il generatore di espressioni del flusso dei dati. In questo caso, è possibile immettere un'espressione o valori letterali che si desidera che corrispondono al tipo di dati del parametro. Di seguito sono riportati esempi di espressione di flusso di dati e una stringa letterale dal generatore di espressioni:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Se il tipo di dati del parametro è una stringa, è possibile scegliere di immettere una pipeline o un'espressione di flusso di dati. Se si sceglie di espressione della pipeline, verrà invece visualizzato il pannello di espressione della pipeline. Assicurarsi di includere le funzioni della pipeline all'interno di sintassi di interpolazione di stringa con ' @{<expression>}', ad esempio:

```'@{pipeline().RunId}'```

![Esempio di parametri del flusso di dati](media/data-flow/params5.png "campione di parametri del flusso di dati")

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire l'attività flusso di dati](control-flow-execute-data-flow-activity.md)
* [Espressioni del flusso di controllo](control-flow-expression-language-functions.md)
