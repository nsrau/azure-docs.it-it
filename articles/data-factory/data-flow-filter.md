---
title: Trasformazione Filtro per il flusso di dati di mapping di Azure Data Factory
description: Trasformazione Filtro per il flusso di dati di mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: dd29753ff14d16081a46eebbc2ea02d94e5985f1
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029352"
---
# <a name="azure-data-factory-filter-transformation"></a>Trasformazione filtro data factory di Azure



La trasformazione Filtro consente di filtrare le righe. Creare un'espressione che definisce la condizione di filtro. Fare clic nella casella di testo per avviare il generatore di espressioni. Nel generatore di espressioni creare un'espressione di filtro per controllare le righe del flusso di dati corrente che possono passare (tramite filtro) alla trasformazione successiva. Si pensi alla trasformazione filtro come clausola WHERE di un'istruzione SQL.

## <a name="filter-on-loan_status-column"></a>Filtra sulla colonna loan_status:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Filtrare in base alla colonna year nella demo Movies:

```
year > 1980
```

## <a name="next-steps"></a>Passaggi successivi

Provare una trasformazione filtro colonne, la [trasformazione selezione](data-flow-select.md)
