---
title: Trasformazione Azure Data Factory Mapping dei dati del flusso esiste
description: Come controllare le righe esistenti usando dati di data factory mapping dei flussi con trasformazione Exists
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: b98b7afb21f2f50d44ba93ed793b6efb20f75164
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65235974"
---
# <a name="mapping-data-flow-exists-transformation"></a>Mapping del flusso di dati esiste trasformazione

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

La trasformazione Esiste è una trasformazione per filtrare le righe che consente o impedisce il passaggio avanti nel flusso a righe specifiche nei dati. La trasformazione Esiste è simile a ```SQL WHERE EXISTS``` e ```SQL WHERE NOT EXISTS```. Dopo la trasformazione è presente, le righe risultanti dal flusso di dati sia includerà tutte le righe in cui sono presenti nell'origine 2 valori delle colonne dall'origine 1 oppure non esistono nell'origine 2.

![Impostazioni di Esiste](media/data-flow/exists.png "Esiste 1")

Scegliere la seconda origine per la trasformazione Esiste in modo che il flusso di dati possa confrontare i valori da flusso 1 con quelli in flusso 2.

Selezionare la colonna dall'origine 1 e dall'origine 2 per cui si vuole eseguire un controllo di esistenza dei valori.

## <a name="multiple-exists-conditions"></a>Le condizioni di esiste più

Accanto a ogni riga nelle proprie condizioni di colonna per Exists, troverai un + sign disponibili quando passa il mouse sulla riga raggiungere. In questo modo sarà possibile aggiungere più righe per le condizioni di Exists. Tutte le altre condizioni sono un "E".

## <a name="custom-expression"></a>Espressione personalizzata

![Impostazioni personalizzate è presente](media/data-flow/exists1.png "esiste personalizzato")

È possibile fare clic su "Espressione personalizzata" per creare invece un'espressione in formato libero come gli esiste o non-condizione è presente. Seleziona questa casella sarà digitare un'espressione personalizzata come una condizione.

## <a name="next-steps"></a>Passaggi successivi

Sono trasformazioni analoghe [Lookup](data-flow-lookup.md) e [Join](data-flow-join.md).
