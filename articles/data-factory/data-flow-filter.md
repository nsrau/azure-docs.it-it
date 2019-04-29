---
title: Trasformazione Filtro per il flusso di dati di mapping di Azure Data Factory
description: Trasformazione Filtro per il flusso di dati di mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: d1751c47ad4507260d9f8d6ea44fcb32ed0e7338
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347430"
---
# <a name="azure-data-factoryfilter-transformation"></a>Trasformazione FactoryFilter dei dati di Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

La trasformazione Filtro consente di filtrare le righe. Creare un'espressione che definisce la condizione di filtro. Fare clic nella casella di testo per avviare il generatore di espressioni. Nel generatore di espressioni creare un'espressione di filtro per controllare le righe del flusso di dati corrente che possono passare (tramite filtro) alla trasformazione successiva. Considerare la trasformazione di filtro come la clausola WHERE di un'istruzione SQL.

## <a name="filter-on-loanstatus-column"></a>Filtrare loan_status colonna:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Filtrare in base alla colonna year nella demo Movies:

```
year > 1980
```

## <a name="next-steps"></a>Passaggi successivi

Provare a una colonna di trasformazione, filtro di [selezionare trasformazione](data-flow-select.md)
