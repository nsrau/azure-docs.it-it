---
title: Transparent Data Encryption in SQL Data Warehouse (Portale) | Documentazione Microsoft
description: Transparent Data Encryption (TDE) in SQL Data Warehouse.
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 9c4abb0416acc656a4cfae332377c398260191de
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
ms.locfileid: "31524228"
---
# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>Introduzione a Transparent Data Encryption (TDE) di SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Proteggere un database in SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md)
> * [Autenticazione](sql-data-warehouse-authentication.md)
> * [Introduzione a Transparent Data Encryption (TDE) di SQL Data Warehouse](sql-data-warehouse-encryption-tde.md)
> * [Introduzione a Transparent Data Encryption (TDE)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permssions"></a>Autorizzazioni necessarie
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
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
