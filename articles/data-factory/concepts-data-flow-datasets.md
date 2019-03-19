---
title: Set di dati del flusso di dati di mapping di Azure Data Factory
description: Azure Data Factory di Mapping del flusso di dati ha sepecific compatibilità di set di dati
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: ad6cfdad519ab3901c58979970ea07439b3106e9
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726924"
---
# <a name="mapping-data-flow-datasets"></a>Set di dati del flusso di dati di mapping

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

I set di dati sono un costrutto di Data Factory che definisce la forma dei dati in uso nella pipeline. Nel flusso di dati, i dati a livello di colonna e riga richiedono una definizione di set di dati con granularità dettagliata. I set di dati usati nelle pipeline dei flussi di controllo non richiedono una comprensione dei dati altrettanto approfondita.

Set di dati nel flusso di dati vengono usati nelle trasformazioni di origine e Sink. Vengono utilizzati per definire gli schemi di dati di base. Se nei dati non è presente uno schema, è possibile attivare la deviazione dello schema per l'origine e il sink. Con uno schema definito dal set di dati, sono disponibili i tipi di dati correlati, i formati dei dati, il percorso dei file e le informazioni di connessione dal servizio collegato associato. I metadati dei set di dati verranno visualizzato nella trasformazione origine come source "Proiezione". Lo schema del set di dati rappresenta il tipo di dati fisico e la forma durante la proiezione nella trasformazione origine rappresenta la rappresentazione del flusso di dati dei dati con i nomi definiti e tipi.

## <a name="dataset-types"></a>Tipi di set di dati

Nel flusso di dati sono attualmente disponibili quattro tipi di set di dati:

* Database SQL di Azure
* Azure SQL DW
* Parquet (da Azure Data Lake Store e Blob)
* Testo delimitato (da Azure Data Lake Store e Blob)

Set di dati del flusso dati separati le *tipo di origine* dalle *tipo di connessione del servizio collegato*. In Data Factory, in genere, si sceglie il tipo di connessione (BLOB, ADLS e così via) e quindi si definisce il tipo di file nel set di dati. All'interno del flusso di dati si selezionano i tipi di origine, che possono essere associati a tipi di connessione del servizio collegato diversi.

![Opzioni di trasformazione origine](media/data-flow/dataset1.png "origini")

## <a name="data-flow-compatible-datasets"></a>Set di dati compatibili con il flusso di dati

Quando si crea un nuovo set di dati, nella parte superiore destra del pannello è presente la casella di controllo "Data Flow Compatible" (Compatibile con il flusso di dati). Se si fa clic su tale pulsante, vengono filtrati e visualizzati solo i set di dati che possono essere usati con i flussi di dati. 

## <a name="import-schemas"></a>Importare schemi

Quando si importa lo schema dei set di dati del flusso di dati, viene visualizzato il pulsante Importa schema. Se si fa clic su questo pulsante, vengono visualizzate due opzioni: importazione dall'origine o importazione da un file locale. Nella maggior parte dei casi si importa lo schema direttamente dall'origine. Se tuttavia si ha un file di schema esistente (file Parquet o CSV con intestazioni), è possibile fare riferimento a tale file locale e Data Factory definirà lo schema in base a questo.

## <a name="delimited-text-dataset"></a>Set di dati di testo delimitato

Nel set di dati di testo delimitato, si imposterà il delimitatore per gestire entrambi delimitatori singoli ('\t 'per TSV,',' per CSV, ' |'...) o usare più caratteri per il delimitatore. Impostare l'interruttore di riga di intestazione e quindi analizzare la trasformazione di origine può rilevare automaticamente i tipi di dati.

## <a name="next-steps"></a>Passaggi successivi

Avviarsi [creando un nuovo flusso di dati](data-flow-create.md) e aggiungere una trasformazione di origine. Configurare quindi il set di dati per l'origine.

Usare la [attività di copia](copy-activity-overview.md) per recuperare dati da qualsiasi ADF origine dati e inserirli temporaneamente nel Blob o Azure Data Lake Store per l'accesso tramite flusso di dati.

