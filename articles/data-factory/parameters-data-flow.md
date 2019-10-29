---
title: Parametri del flusso di dati del mapping Azure Data Factory
description: Informazioni su come parametrizzare un flusso di dati di mapping da pipeline di data factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 0a1051d67bf45e96f82833ef8190008204cdc90b
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "72387543"
---
# <a name="mapping-data-flow-parameters"></a>Mapping di parametri del flusso di dati



Il mapping di flussi di dati in Azure Data Factory supporta l'utilizzo di parametri. È possibile definire parametri all'interno della definizione del flusso di dati, che è quindi possibile usare in tutte le espressioni. I valori dei parametri possono essere impostati dalla pipeline chiamante tramite l'attività Esegui flusso di dati. Sono disponibili tre opzioni per l'impostazione dei valori nelle espressioni di attività flusso di dati:

* Usare il linguaggio delle espressioni del flusso di controllo della pipeline per impostare un valore dinamico
* Usare il linguaggio delle espressioni del flusso di dati per impostare un valore dinamico
* Usare il linguaggio delle espressioni per impostare un valore letterale statico

Usare questa funzionalità per rendere i flussi di dati generici, flessibili e riutilizzabili. Con questi parametri è possibile parametrizzare le impostazioni e le espressioni del flusso di dati.

> [!NOTE]
> Per utilizzare le espressioni del flusso di controllo della pipeline, il parametro del flusso di dati deve essere di tipo String.

## <a name="create-parameters-in-mapping-data-flow"></a>Creazione di parametri nel flusso di dati di mapping

Per aggiungere parametri al flusso di dati, fare clic sulla parte vuota dell'area di disegno del flusso di dati per visualizzare le proprietà generali. Nel riquadro impostazioni viene visualizzata una scheda denominata "Parameters". Fare clic sul pulsante "nuovo" per generare un nuovo parametro. Per ogni parametro, è necessario assegnare un nome, selezionare un tipo e, facoltativamente, impostare un valore predefinito.

![Creare parametri del flusso di dati](media/data-flow/create-params.png "Creare parametri del flusso di dati")

I parametri possono essere utilizzati in qualsiasi espressione del flusso di dati. I parametri iniziano con $ e non sono modificabili. È possibile trovare l'elenco dei parametri disponibili all'interno del generatore di espressioni nella scheda ' parameters '.

![Espressione parametro flusso di dati](media/data-flow/parameter-expression.png "Espressione parametro flusso di dati")

## <a name="use-parameters-in-your-data-flow"></a>Usare i parametri nel flusso di dati

* È possibile utilizzare i valori dei parametri all'interno delle espressioni di trasformazione. L'elenco di parametri è presente nella scheda parametri nel generatore di espressioni. ![Usare i parametri del flusso di dati](media/data-flow/params9.png "Uparametri del flusso di dati se ")

* I parametri vengono usati anche per configurare i valori dinamici per le impostazioni di trasformazione di origine e sink. Quando si fa clic all'interno di campi configurabili, viene visualizzato il collegamento "Aggiungi contect dinamico". Se si fa clic su un generatore di espressioni in cui è possibile usare i parametri per usare i valori dinamici. ![Contenuto dinamico del flusso di dati](media/data-flow/params6.png "Dcontenuto dinamico di ATA Flow ")

## <a name="set-mapping-data-flow-parameters-from-pipeline"></a>Impostare i parametri del flusso di dati di mapping dalla pipeline

Dopo aver creato il flusso di dati con parametri, è possibile eseguirlo da una pipeline con l'attività Esegui flusso di dati. Dopo aver aggiunto l'attività nell'area di disegno della pipeline, verranno visualizzati i parametri del flusso di dati disponibili nella scheda ' parametri ' dell'attività.

![Impostazione di un parametro del flusso di dati](media/data-flow/parameter-assign.png "Impostazione di un parametro del flusso di dati")

Se il tipo di dati del parametro è String, quando si fa clic sulla casella di testo per impostare i valori dei parametri, è possibile scegliere di immettere una pipeline o un'espressione del flusso di dati. Se si sceglie espressione pipeline, verrà visualizzato il pannello espressione pipeline. Assicurarsi di includere le funzioni della pipeline all'interno della sintassi di interpolazione di stringhe usando `'@{<expression>}'`, ad esempio:

```'@{pipeline().RunId}'```

Se il parametro non è di tipo stringa, viene sempre visualizzato il generatore di espressioni del flusso di dati. Qui è possibile immettere qualsiasi espressione o valore letterale che si desidera corrisponda al tipo di dati del parametro. Di seguito sono riportati esempi di espressione del flusso di dati e una stringa letterale dal generatore di espressioni:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Ogni flusso di dati di mapping può includere qualsiasi combinazione di parametri dell'espressione del flusso di dati e della pipeline. 

![Esempio di parametri del flusso di dati](media/data-flow/parameter-example.png "Esempio di parametri del flusso di dati")



## <a name="next-steps"></a>Passaggi successivi
* [Attività Esegui flusso di dati](control-flow-execute-data-flow-activity.md)
* [Espressioni del flusso di controllo](control-flow-expression-language-functions.md)
