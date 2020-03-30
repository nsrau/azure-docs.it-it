---
title: Crittografia dei dati trasparente (T-SQL)Transparent data encryption (T-SQL)
description: Crittografia dei dati trasparente (TDE) in Azure Synapse Analytics (T-SQL)Transparent data encryption (TDE) in Azure Synapse Analytics (T-SQL)
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 14058a91ee21bd792f9be1a64d554092c4f5aedd
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351501"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Introduzione a Transparent Data Encryption (TDE)
> [!div class="op_single_selector"]
> * [Panoramica della sicurezza](sql-data-warehouse-overview-manage-security.md)
> * [Autenticazione](sql-data-warehouse-authentication.md)
> * [Introduzione a Transparent Data Encryption (TDE) di SQL Data Warehouse](sql-data-warehouse-encryption-tde.md)
> * [Introduzione a Transparent Data Encryption (TDE)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Autorizzazioni necessarie
Per abilitare Transparent Data Encryption (TDE), è necessario essere un amministratore o un membro del ruolo dbmanager.

## <a name="enabling-encryption"></a>Abilitazione della crittografia
Attenersi alla seguente procedura per abilitare TDE:

1. Connettere il database *master* sul server che ospita il database usando un account di accesso di un amministratore o di un membro del ruolo **dbmanager** nel database master.
2. Eseguire l'istruzione seguente per crittografare il database.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Disabilitazione della crittografia
Attenersi alla seguente procedura per disattivare TDE:

1. Connettere il database *master* usando un account di accesso di un amministratore o di un membro del ruolo **dbmanager** nel database master.
2. Eseguire l'istruzione seguente per crittografare il database.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> Un pool SQL in pausa deve essere ripreso prima di apportare modifiche alle impostazioni TDE.
> 
> 

## <a name="verifying-encryption"></a>Verifica della crittografia
Per verificare lo stato di crittografia, attenersi alla seguente procedura:

1. Connettere il database *master* o dell'istanza usando un account di accesso di un amministratore o di un membro del ruolo **dbmanager** nel database master.
2. Eseguire l'istruzione seguente per crittografare il database.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Il risultato ```1``` indica un database crittografato, ```0``` indica un database non crittografato.

## <a name="encryption-dmvs"></a>Viste a gestione dinamica della crittografia
* [sys.databases][sys.databases] 
* [sys.dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
