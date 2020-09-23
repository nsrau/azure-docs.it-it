---
title: sys. sp_cleanup_data_retention (Transact-SQL)-Azure SQL Edge
description: Informazioni sull'uso di sys. sp_cleanup_data_retention (Transact-SQL) in Azure SQL Edge
keywords: sys. sp_cleanup_data_retention (Transact-SQL), SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 9c0a6700a476d4f7f875af5373e3c99bc4e25af2
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938635"
---
# <a name="syssp_cleanup_data_retention-transact-sql"></a>sys.sp_cleanup_data_retention (Transact-SQL)

**Si applica a:** Azure SQL Edge

Esegue la pulizia di record obsoleti da tabelle per cui sono abilitati i criteri di conservazione dei dati. Per altre informazioni, vedere [conservazione dei dati](data-retention-overview.md). 

## <a name="syntax"></a>Sintassi 

```sql
sys.sp_cleanup_data_retention   
    { [@schema_name = ] 'schema_name' },  
    { [@table_name = ] 'table_name' },   
    [ [@rowcount =] rowcount OUTPUT ]    

```

## <a name="arguments"></a>Argomenti  
`[ @schema_name = ] schema_name`    
 Nome dello schema proprietario della tabella in cui deve essere eseguita la pulizia. *schema_name* è un parametro obbligatorio di tipo **sysname**.
  
`[ @table_name = ] 'table_name'`    
 Nome della tabella in cui deve essere eseguita l'operazione di pulizia. *table_name* è un parametro obbligatorio di tipo **sysname**.

## <a name="output-parameter"></a>Parametro di output  

`[ @rowcount = ] rowcount OUTPUT`   
 RowCount è un parametro di OUTPUT facoltativo che rappresenta il numero di record di pulitura dalla tabella. il *conteggio delle righe* è int.
  
## <a name="permissions"></a>Autorizzazioni    
 Richiede autorizzazioni db_owner.

## <a name="next-steps"></a>Passaggi successivi
- [Conservazione dei dati e eliminazione automatica dei dati](data-retention-overview.md)
- [Gestire i dati cronologici con i criteri di conservazione](data-retention-cleanup.md) 
- [Abilitare e disabilitare la conservazione dei dati](data-retention-enable-disable.md)
