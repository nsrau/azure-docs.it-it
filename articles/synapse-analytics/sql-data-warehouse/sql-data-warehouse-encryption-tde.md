---
title: Transparent Data Encryption (portale)
description: Transparent Data Encryption (Transparent Data Encryption) in Azure sinapsi Analytics
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: b530b3a049f41a54ab98cc7d1454018cfc990f75
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86495670"
---
# <a name="get-started-with-transparent-data-encryption-tde-in-azure-synapse-analytics"></a>Introduzione a Transparent Data Encryption (Transparent Data Encryption) in Azure sinapsi Analytics

> [!div class="op_single_selector"]
>
> * [Panoramica della sicurezza](sql-data-warehouse-overview-manage-security.md)
> * [autenticazione](sql-data-warehouse-authentication.md)
> * [Introduzione a Transparent Data Encryption (TDE) di SQL Data Warehouse](sql-data-warehouse-encryption-tde.md)
> * [Introduzione a Transparent Data Encryption (TDE)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>Autorizzazioni necessarie

Per abilitare Transparent Data Encryption (TDE), è necessario essere un amministratore o un membro del ruolo dbmanager.

## <a name="enabling-encryption"></a>Abilitazione della crittografia

Per abilitare Transparent Data Encryption, attenersi alla procedura seguente:

1. Aprire il database nel [portale di Azure](https://portal.azure.com)
2. Nel pannello del database fare clic sul pulsante **Impostazioni**
3. Selezionare le impostazioni del portale per l'opzione **Transparent Data Encryption** ![](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Selezionare l' **On** impostazione ![ Impostazioni portale on](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. Selezionare **Salva** 
    ![ Impostazioni portale Salva](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>Disabilitazione della crittografia

Per disabilitare Transparent Data Encryption, attenersi alla procedura seguente:

1. Aprire il database nel [portale di Azure](https://portal.azure.com)
2. Nel pannello del database fare clic sul pulsante **Impostazioni**
3. Selezionare le impostazioni del portale per l'opzione **Transparent Data Encryption** ![](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Selezionare Off **impostazione** ![ Impostazioni portale disattivato](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. Selezionare **Salva** 
    ![ impostazione del portale Salva 2](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>Viste a gestione dinamica della crittografia

La crittografia può essere confermata con le seguenti viste a gestione dinamica:

* [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
