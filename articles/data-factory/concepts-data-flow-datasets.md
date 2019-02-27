---
title: Set di dati del flusso di dati di mapping di Azure Data Factory
description: Il flusso di dati di mapping di Azure Data Factory ha una compatibilità di set di dati specifica
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: 36ca5e07adf79de77ac4ab4149ff8e96a1dece8d
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408749"
---
# <a name="mapping-data-flow-datasets"></a>Set di dati del flusso di dati di mapping

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

I set di dati sono un costrutto di Data Factory che definisce la forma dei dati in uso nella pipeline. Nel flusso di dati, i dati a livello di colonna e riga richiedono una definizione di set di dati con granularità dettagliata. I set di dati usati nelle pipeline dei flussi di controllo non richiedono una comprensione dei dati altrettanto approfondita.

I set di dati nelle trasformazioni di origine e sink del flusso di dati vengono usati per definire lo schema dei dati di base. Se nei dati non è presente uno schema, è possibile attivare la deviazione dello schema per l'origine e il sink. Con uno schema definito dal set di dati, sono disponibili i tipi di dati correlati, i formati dei dati, il percorso dei file e le informazioni di connessione dal servizio collegato associato.

## <a name="dataset-types"></a>Tipi di set di dati

Nel flusso di dati sono attualmente disponibili quattro tipi di set di dati:

* Database SQL di Azure
* Azure SQL DW
* Parquet
* Testo delimitato

I set di dati del flusso di dati separano il *tipo* di origine dal tipo di connessione del servizio collegato. In Data Factory, in genere, si sceglie il tipo di connessione (BLOB, ADLS e così via) e quindi si definisce il tipo di file nel set di dati. All'interno del flusso di dati si selezionano i tipi di origine, che possono essere associati a tipi di connessione del servizio collegato diversi.

![Opzioni di trasformazione origine](media/data-flow/dataset1.png "origini")

## <a name="data-flow-compatible-datasets"></a>Set di dati compatibili con il flusso di dati

Quando si crea un nuovo set di dati, nella parte superiore destra del pannello è presente la casella di controllo "Data Flow Compatible" (Compatibile con il flusso di dati). Se si fa clic su tale pulsante, vengono filtrati e visualizzati solo i set di dati che possono essere usati con i flussi di dati. 

## <a name="import-schemas"></a>Importare schemi

Quando si importa lo schema dei set di dati del flusso di dati, viene visualizzato il pulsante Importa schema. Se si fa clic su questo pulsante, vengono visualizzate due opzioni: importazione dall'origine o importazione da un file locale. Nella maggior parte dei casi si importa lo schema direttamente dall'origine. Se tuttavia si ha un file di schema esistente (file Parquet o CSV con intestazioni), è possibile fare riferimento a tale file locale e Data Factory definirà lo schema in base a questo.

