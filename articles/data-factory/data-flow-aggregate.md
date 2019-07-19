---
title: Trasformazione aggregazione nel flusso di dati di mapping-Azure Data Factory | Microsoft Docs
description: Informazioni su come aggregare i dati su larga scala in Azure Data Factory con la trasformazione aggregazione del flusso di dati di mapping.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 778aefc05a9b12648e60d752a3c281cb18323125
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314237"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Trasformazione aggregazione nel flusso di dati di mapping 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

La trasformazione Aggregazione è la posizione in cui verranno definite le aggregazioni delle colonne nei flussi di dati. Utilizzando il generatore di espressioni, è possibile definire diversi tipi di aggregazione, ad esempio SUM, MIN, MAX e COUNT, che possono essere raggruppati in base alle colonne calcolate o esistenti.

## <a name="group-by"></a>Raggruppa per
Consente di selezionare una colonna esistente o di creare una nuova colonna calcolata da utilizzare come clausola Group by per l'aggregazione. Per utilizzare una colonna esistente, selezionare la colonna desiderata dall'elenco a discesa. Per creare una nuova colonna calcolata, passare il puntatore del mouse sulla clausola e fare clic su "colonna calcolata". Verrà aperto il generatore di [espressioni del flusso di dati](concepts-data-flow-expression-builder.md). Dopo aver creato la colonna calcolata, immettere il nome della colonna di output nel campo "nome come". Se si desidera aggiungere una clausola Group by aggiuntiva, passare il puntatore del mouse su una clausola esistente e fare clic su' +'.

![Raggruppamento della trasformazione aggregazione per impostazioni](media/data-flow/agg.png "Raggruppamento della trasformazione aggregazione per impostazioni")

> [!NOTE]
> Una clausola Group by è facoltativa in una trasformazione aggregazione.

## <a name="aggregate-column"></a>Colonna aggregazione 
Scegliere la scheda "aggregazioni" per compilare le espressioni di aggregazione. È possibile scegliere una colonna esistente e sovrascrivere il valore con l'aggregazione oppure creare un nuovo campo con un nuovo nome. L'espressione di aggregazione viene immessa nella casella a destra accanto al selettore del nome di colonna. Per modificare l'espressione, fare clic sulla casella di testo per aprire il generatore di espressioni. Per aggiungere un'aggregazione aggiuntiva, passare il puntatore del mouse su un'espressione esistente e fare clic su' +' per creare un nuovo [modello](concepts-data-flow-column-pattern.md)di colonna o colonna di aggregazione.

![Impostazioni aggregate trasformazione aggregazione](media/data-flow/agg2.png "Impostazioni aggregate trasformazione aggregazione")

> [!NOTE]
> Ogni espressione di aggregazione deve contenere almeno una funzione di aggregazione.

> [!NOTE]
> In modalità di debug, il generatore di espressioni non può produrre anteprime dei dati con funzioni di aggregazione. Per visualizzare le anteprime dei dati per le trasformazioni aggregate, chiudere il generatore di espressioni e visualizzare i dati tramite la scheda "Anteprima dati".

## <a name="reconnect-rows-and-columns"></a>Riconnettere righe e colonne
Le trasformazioni di aggregazione sono strettamente equivalenti alle query SELECT di aggregazione SQL. Le colonne che non sono incluse nella clausola Group by o nelle funzioni di aggregazione non passano attraverso l'output della trasformazione aggregazione. Se sono presenti altre colonne che si desidera includere nell'output delle righe aggregate, è necessario eseguire una delle operazioni seguenti:

* Utilizzare una funzione di aggregazione per includere la colonna aggiuntiva, ad esempio Last () o First ()
* Aggiungere di nuovo le colonne prima dell'aggregazione usando il [modello self join](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/).

## <a name="next-steps"></a>Passaggi successivi

* Definire un'aggregazione basata su finestra mediante la [trasformazione finestra](data-flow-window.md)
