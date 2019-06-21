---
title: Mapping di parametri del flusso di dati di Azure Data Factory
description: Informazioni su come impostare i parametri per un flusso di dati di mapping dalla pipeline di data factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: ef97f17bf159511ce94f90cd00623e05489acb92
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274697"
---
# <a name="mapping-data-flow-parameters"></a>Mapping di parametri del flusso di dati

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Mapping di flusso dei dati in Azure Data Factory supporta l'utilizzo di parametri. È possibile definire i parametri all'interno di definizione del flusso di dati, è quindi possibile usare in tutta le espressioni. I valori dei parametri possono essere impostati dalla pipeline chiama tramite l'attività di esecuzione del flusso di dati. Sono disponibili tre opzioni per l'impostazione dei valori nel flusso di dati le espressioni di attività:

* Usare il linguaggio delle espressioni della pipeline del flusso controllo per impostare un valore dinamico
* Usare il linguaggio delle espressioni del flusso dei dati per impostare un valore dinamico
* Usare entrambi i linguaggi di espressione per impostare un valore letterale statico

Usare questa funzionalità per rendere i flussi di dati per utilizzo generico, flessibile e riutilizzabile. È possibile parametrizzare le impostazioni del flusso di dati e le espressioni con questi parametri.

> [!NOTE]
> Per usare espressioni del flusso di controllo della pipeline, il parametro di flusso di dati deve essere di tipo stringa.

## <a name="create-parameters-in-mapping-data-flow"></a>Creare i parametri nel Mapping di flusso di dati

Per aggiungere parametri al flusso di dati, fare clic sulla parte vuota dell'area di disegno per visualizzare le proprietà generali del flusso dei dati. Nel riquadro impostazioni, verrà visualizzata una scheda denominata 'Parameters'. Fare clic sul pulsante 'Nuovo' per generare un nuovo parametro. Per ogni parametro, è necessario assegnare un nome, selezionare un tipo e, facoltativamente, impostare un valore predefinito.

![Creare i parametri del flusso di dati](media/data-flow/create-params.png "parametri di creazione del flusso di dati")

I parametri possono essere utilizzati in qualsiasi espressione di flusso di dati. I parametri iniziano con $ e non sono modificabili. Si noterà l'elenco dei parametri disponibili all'interno del generatore di espressioni nella scheda 'Parameters'.

![Espressione per i parametri del flusso di dati](media/data-flow/parameter-expression.png "espressione per i parametri del flusso di dati")

## <a name="set-mapping-data-flow-parameters-from-pipeline"></a>Impostare i parametri di Mapping del flusso di dati da pipeline

Dopo aver creato il flusso di dati con parametri, è possibile eseguire da una pipeline con l'eseguire attività del flusso di dati. Dopo aver aggiunto l'attività all'area di disegno della pipeline, parametri del flusso nella scheda 'Parameters' dell'attività verrà visualizzata con i dati disponibili.

![Impostazione di un parametro del flusso di dati](media/data-flow/parameter-assign.png "impostando un parametro del flusso di dati")

Se il tipo di dati del parametro è stringa, quando si seleziona la casella di testo per impostare i valori dei parametri, è possibile scegliere di immettere una pipeline o un'espressione di flusso di dati. Se si sceglie di espressione della pipeline, verrà visualizzato il pannello di espressione della pipeline. Assicurarsi di includere le funzioni della pipeline all'interno di sintassi di interpolazione di stringa con ' @{<expression>}', ad esempio:

```'@{pipeline().RunId}'```

Se il parametro non è di tipo stringa, verrà sempre visualizzato con il generatore di espressioni del flusso dei dati. In questo caso, è possibile immettere un'espressione o valori letterali che si desidera che corrisponde al tipo di dati del parametro. Di seguito sono riportati esempi di espressione di flusso di dati e una stringa letterale dal generatore di espressioni:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Ogni flusso di dati di Mapping può avere qualsiasi combinazione di parametri di espressioni del flusso di dati e pipeline. 

![Esempio di parametri del flusso di dati](media/data-flow/parameter-example.png "campione di parametri del flusso di dati")



## <a name="next-steps"></a>Passaggi successivi
* [Eseguire l'attività flusso di dati](control-flow-execute-data-flow-activity.md)
* [Espressioni del flusso di controllo](control-flow-expression-language-functions.md)
