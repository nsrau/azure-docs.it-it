---
title: sys. external_job_streams (Transact-SQL)-Azure SQL Edge
description: Informazioni sull'uso di sys. external_job_streams in Azure SQL Edge
keywords: sys.external_job_streams, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 35010d3aba7f6d5ee3185291c917ff7726ba8bd7
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90900365"
---
# <a name="sysexternal_job_streams-transact-sql"></a>sys.external_job_streams (Transact-SQL)

Restituisce una riga per ogni oggetto flusso esterno di input o di output mappato a un processo di streaming esterno.

|Nome colonna|Tipo di dati|Descrizione|  
|-----------------|---------------|-----------------|
|**job_id**|**int**| Numero di identificazione dell'oggetto per l'oggetto processo di streaming. Questa colonna è mappata alla colonna object_id di sys.external_streaming_jobs.|
|**stream_id**|**int**| Numero di identificazione dell'oggetto per l'oggetto flusso. Questa colonna è mappata alla colonna object_id di sys.external_streams. |
|**is_input**|**bit**| 1 se l'oggetto flusso viene usato come input per il processo di flusso. In caso contrario, 0.|
|**is_output**|**bit**| 1 se l'oggetto flusso viene usato come output per il processo di flusso. In caso contrario, 0.|

## <a name="example"></a>Esempio

Questa vista del catalogo viene usata insieme alle viste del catalogo `sys.external_streams` e `sys.external_streaming_jobs`. Di seguito è riportata una query di esempio

```sql
Select
    sj.Name as Job_Name,
    sj.Create_date as Job_Create_date,
    sj.modify_date as Job_Modify_date,
    sj.statement as Stream_Job_Query,
    Input_Stream_Name =
        Case js.is_input
            when 1 then s.Name
            else null
        END,
    output_Stream_Name =
        case js.is_output
            when 1 then s.Name
            else null
        END,
    s.location as Stream_Location
from sys.external_job_streams js
inner join sys.external_streams s on s.object_id = js.stream_id
inner join sys.external_streaming_jobs sj on sj.object_id = js.job_id
```

## <a name="permissions"></a>Autorizzazioni

La visibilità dei metadati nelle viste del catalogo è limitata alle entità a protezione diretta di cui l'utente è proprietario o per le quali dispone di autorizzazioni. Per altre informazioni, vedere [Metadata Visibility Configuration](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Vedere anche

- [Viste del catalogo (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Viste di sistema (Transact-SQL)](/sql/t-sql/language-reference/)
