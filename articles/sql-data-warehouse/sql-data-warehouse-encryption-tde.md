---
title: Transparent Data Encryption in SQL Data Warehouse (Portale) | Documentazione Microsoft
description: Transparent Data Encryption (TDE) in SQL Data Warehouse.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/30/2019
ms.author: kavithaj
ms.reviewer: rortloff
ms.openlocfilehash: e756049110f7d4a81950abf6ebbe73edb3e3ca0a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143170"
---
# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>Introduzione a Transparent Data Encryption (TDE) di SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Proteggere un database in SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md)
> * [Autenticazione](sql-data-warehouse-authentication.md)
> * [Introduzione a Transparent Data Encryption (TDE) di SQL Data Warehouse](sql-data-warehouse-encryption-tde.md)
> * [Introduzione a Transparent Data Encryption (TDE)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Autorizzazioni necessarie
Per abilitare Transparent Data Encryption (TDE), è necessario essere un amministratore o un membro del ruolo dbmanager.

## <a name="enabling-encryption"></a>Abilitazione della crittografia
Per abilitare TDE per un SQL Data Warehouse, seguire questa procedura:

1. Aprire il database nel [portale di Azure](https://portal.azure.com)
2. Nel pannello del database fare clic sul pulsante **Impostazioni**
3. Selezionare l'opzione **Transparent data encryption**![][1]
4. Selezionare l'impostazione **Attiva** ![][2]
5. Selezionare **Salva**
   ![][3]  

## <a name="disabling-encryption"></a>Disabilitazione della crittografia
Per disabilitare TDE per un SQL Data Warehouse, seguire questa procedura:

1. Aprire il database nel [portale di Azure](https://portal.azure.com)
2. Nel pannello del database fare clic sul pulsante **Impostazioni**
3. Selezionare l'opzione **Transparent data encryption**![][1]
4. Selezionare l'impostazione **Disattiva** ![][4]
5. Selezionare **Salva**
   ![][5]  

## <a name="encryption-dmvs"></a>Viste a gestione dinamica della crittografia
La crittografia può essere confermata con le seguenti viste a gestione dinamica:

* [sys.databases]
* [sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
