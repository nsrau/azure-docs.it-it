---
title: sys. external_streams (Transact-SQL)-Azure SQL Edge
description: Informazioni sull'uso di sys. external_streams in Azure SQL Edge
keywords: sys.external_streams, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 04950f01c06bc3c8ed3bb11a790310c2319a0579
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90900299"
---
# <a name="sysexternal_streams-transact-sql"></a>sys.external_streams (Transact-SQL)

Restituisce una riga per ogni oggetto flusso esterno creato nell'ambito del database.

|Nome colonna|Tipo di dati|Descrizione|  
|-----------------|---------------|-----------------|
|**nome**|**sysname**|Nome del flusso. Valore univoco all'interno del database.|
|**object_id**|**int**|Numero di identificazione dell'oggetto per l'oggetto flusso. Valore univoco all'interno del database.|
|**principal_id**|**int**|ID dell'entità di sicurezza proprietaria dell'assembly|
|**schema_id**|**int**| ID dello schema che include l'oggetto.|
|**parent_object_id**|**id**| Numero di identificazione dell'oggetto padre per il flusso. Nell'implementazione corrente questo valore è sempre Null|
|**type**|**char(2)**|Tipo di oggetto. Per gli oggetti flusso, il tipo è sempre 'ES'|
|**type_desc**|**nvarchar(60)**| Descrizione del tipo di oggetto. Per gli oggetti flusso, il tipo è sempre 'EXTERNAL_STREAM'|
|**create_date**|**datetime**| Data di creazione dell'oggetto.|
|**modify_date**|**datetime**| Data dell'ultima modifica apportata all'oggetto mediante un'istruzione ALTER.|
|**is_ms_shipped**|**bit**| Oggetto creato da un componente interno.|  
|**is_published**|**bit**|L'oggetto viene pubblicato.|  
|**is_schema_published**|**bit**|Viene pubblicato solo lo schema dell'oggetto.|
|**max_column_id_used**|**bit**| Questa colonna viene usata per scopi interni e verrà rimossa in futuro|  
|**uses_ansi_nulls**|**bit**| L'oggetto flusso è stato creato con l'opzione di database SET ANSI_NULLS impostata su ON|
|**data_source_id**|**int**| ID oggetto per l'origine dati esterna rappresentata dall'oggetto flusso |  
|**file_format_id**|**int**| ID oggetto per il formato di file esterno usato dall'oggetto flusso. Il formato di file esterno è richiesto per specificare il layout effettivo dei dati a cui fa riferimento un flusso esterno| 
|**location**|**ntext**| Destinazione per l'oggetto flusso esterno. Per altre informazioni, vedere [Creare un flusso esterno](overview.md) |
|**input_option**|**ntext**| Opzioni di input usate durante la creazione del flusso esterno. Per altre informazioni, vedere [Creare un flusso esterno](overview.md) |
|**output_option**|**ntext**| Opzioni di output usate durante la creazione del flusso esterno. Per altre informazioni, vedere [Creare un flusso esterno](overview.md) | 

## <a name="permissions"></a>Autorizzazioni

La visibilità dei metadati nelle viste del catalogo è limitata alle entità a protezione diretta di cui l'utente è proprietario o per le quali dispone di autorizzazioni. Per altre informazioni, vedere [Metadata Visibility Configuration](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Vedere anche

- [Viste del catalogo (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Viste di sistema (Transact-SQL)](/sql/t-sql/language-reference/)
