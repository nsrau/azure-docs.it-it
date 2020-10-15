---
title: sys.external_streaming_jobs (Transact-SQL)-Azure SQL Edge
description: Informazioni sull'uso di sys.external_streaming_jobs in Azure SQL Edge
keywords: sys.external_streaming_jobs, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 9643c58f5c9fa1db3e3eb7ec75ce6d3b41620aa3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90900342"
---
# <a name="sysexternal_streaming_jobs-transact-sql"></a>sys.external_streaming_jobs (Transact-SQL)

Restituisce una riga per ogni processo di streaming esterno creato nell'ambito del database.

|Nome colonna|Tipo di dati|Descrizione|  
|-----------------|---------------|-----------------|
|**nome**|**sysname**|Nome del flusso. Valore univoco all'interno del database.|
|**object_id**|**int**|Numero di identificazione dell'oggetto per l'oggetto flusso. Valore univoco all'interno del database.|
|**principal_id**|**int**|ID dell'entità di sicurezza proprietaria dell'assembly|
|**schema_id**|**int**| ID dello schema che include l'oggetto.|
|**parent_object_id**|**id**| Numero di identificazione dell'oggetto padre per il flusso. Nell'implementazione corrente questo valore è sempre Null|
|**type**|**char(2)**|Tipo di oggetto. Per gli oggetti flusso, il tipo è sempre 'EJ'|
|**type_desc**|**nvarchar(60)**| Descrizione del tipo di oggetto. Per gli oggetti flusso, il tipo è sempre 'EXTERNAL_STREAMING_JOB'|
|**create_date**|**datetime**| Data di creazione dell'oggetto.|
|**modify_date**|**datetime**| Nell'implementazione corrente questo valore è uguale a create_date per l'oggetto flusso |
|**is_ms_shipped**|**bit**| Oggetto creato da un componente interno.|  
|**is_published**|**bit**| L'oggetto viene pubblicato.|  
|**is_schema_published**|**bit**|Viene pubblicato solo lo schema dell'oggetto.|
|**uses_ansi_nulls**|**bit**| L'oggetto flusso è stato creato con l'opzione di database SET ANSI_NULLS impostata su ON|
|**istruzione**|**ntext**| Testo della query di analisi di flusso per il processo di streaming. Per altre informazioni, vedere [sp_create_streaming_job](overview.md) |
|**Stato**|**int**| Stato corrente del processo di streaming. I valori possibili sono <br /><br /> **Created** = 0. Il processo di streaming è stato creato, ma non è ancora stato avviato. <br /><br /> **Starting** = 1. Il processo di streaming si trova nella fase di avvio. <br /><br /> **Failed** = 6. Il processo di streaming non è riuscito. Si tratta in genere di un'indicazione di un errore irreversibile durante l'elaborazione. <br /><br /> **Stopped** = 4. Il processo di streaming è stato arrestato. <br /><br /> **Idle** = 7. Il processo di streaming è in esecuzione, ma non è presente alcun input da elaborare. <br /><br /> **Processing** = 8. Il processo di streaming è in esecuzione ed è in corso l'elaborazione degli input. Questo stato indica uno stato integro per il processo di streaming. <br /><br /> **Degraded** = 9. Il processo di streaming è in esecuzione, tuttavia si sono verificati alcuni errori di serializzazione/deserializzazione di input/output non irreversibili durante l'elaborazione dell'input. L'esecuzione del processo di input continuerà, ma verranno eliminati gli input per cui si verificano errori.|

## <a name="permissions"></a>Autorizzazioni

La visibilità dei metadati nelle viste del catalogo è limitata alle entità a protezione diretta di cui l'utente è proprietario o per le quali dispone di autorizzazioni. Per altre informazioni, vedere [Metadata Visibility Configuration](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Vedere anche

- [Viste del catalogo di Streaming T-SQL](overview.md)
- [Viste del catalogo (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Viste di sistema (Transact-SQL)](/sql/t-sql/language-reference/)

