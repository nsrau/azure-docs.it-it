---
title: ALTER EXTERNAL STREAM (Transact-SQL) - SQL Edge di Azure (anteprima)
description: Informazioni sull'istruzione ALTER EXTERNAL STREAM in SQL Edge di Azure (anteprima)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 0183972b5eb92d3f081b857940609bffc183b331
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595410"
---
# <a name="alter-external-stream-transact-sql"></a>ALTER EXTERNAL STREAM (Transact-SQL)

Modifica la definizione di un flusso esterno. Non è consentito modificare un flusso esterno usato da un processo di streaming nello stato *In esecuzione*. 



## <a name="syntax"></a>Sintassi

```sql
  ALTER EXTERNAL STREAM external_stream_name 
  SET 
    [DATA_SOURCE] = <data_source_name> 
    , LOCATION = <location_name> 
    , EXTERNAL_FILE_FORMAT = <external_file_format_name> 
    , INPUT_OPTIONS = <input_options> 
    , OUTPUT_OPTIONS = <output_options> 
```

## <a name="arguments"></a>Argomenti

Per informazioni dettagliate sugli argomenti del comando ALTER EXTERNAL STREAM, vedere [CREATE EXTERNAL STREAM (Transact-SQL)](create-external-stream-transact-sql.md).

## <a name="return-code-values"></a>Valori del codice restituito

ALTER EXTERNAL STREAM restituisce 0 in caso di esito positivo. Un valore restituito diverso da zero indica un errore.


## <a name="see-also"></a>Vedere anche

- [CREATE EXTERNAL STREAM (Transact-SQL)](create-external-stream-transact-sql.md) 
- [DROP EXTERNAL STREAM (Transact-SQL)](drop-external-stream-transact-sql.md) 
