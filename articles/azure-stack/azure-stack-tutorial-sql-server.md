---
title: Rendere disponibili agli utenti dello Stack di Azure SQL database | Documenti Microsoft
description: Esercitazione per installare il provider di risorse di SQL Server e creare offerte che consentono agli utenti di Azure Stack di creare il database SQL.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 7/03/2017
ms.author: erikje
ms.custom: mvc
ms.openlocfilehash: f774888ba3921d0688feddac669ed1dca4667441
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="make-sql-databases-available-to-your-azure-stack-users"></a>Rendere disponibili agli utenti dello Stack di Azure SQL database

Un amministratore di cloud di Azure Stack, è possibile creare offerte che consentono agli utenti di database SQL che possono essere utilizzati con le app cloud nativo, siti Web e i carichi di lavoro di creare (tenant). Grazie agli utenti i database personalizzati, su richiesta, basato su cloud, è possibile salvarli tempo e risorse. A tale scopo, è possibile:

> [!div class="checklist"]
> * Distribuire il provider di risorse di SQL Server
> * Creare un'offerta
> * Testare l'offerta

## <a name="deploy-the-sql-server-resource-provider"></a>Distribuire il provider di risorse di SQL Server

Viene descritto il processo di distribuzione in modo dettagliato nel [i database SQL di utilizzo per l'articolo Azure Stack](azure-stack-sql-resource-provider-deploy.md)e include i passaggi principali seguenti:

1. [Distribuire il provider di risorse SQL]( azure-stack-sql-resource-provider-deploy.md#deploy-the-resource-provider).
2. [Verificare la distribuzione]( azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal).
3. Connessione a un host di SQL server per fornire capacità.

## <a name="create-an-offer"></a>Creare un'offerta

1.  [Impostare una quota](azure-stack-setting-quotas.md) e denominarlo *SQLServerQuota*. Selezionare **Microsoft.SQLAdapter** per il **Namespace** campo.
2.  [Creare un piano](azure-stack-create-plan.md). Il nome *TestSQLServerPlan*, selezionare il **Microsoft.SQLAdapter** servizio, e **SQLServerQuota** quota.

    > [!NOTE]
    > Per consentire agli utenti di creare altre App, altri servizi potrebbero essere necessario nel piano. Ad esempio, le funzioni di Azure richiede che il piano include il **Microsoft.Storage** il servizio, mentre richiede Wordpress **Microsoft.MySQLAdapter**.
    > 
    >

3.  [Creare un'offerta](azure-stack-create-offer.md), denominarla **TestSQLServerOffer** e selezionare il **TestSQLServerPlan** piano.

## <a name="test-the-offer"></a>Testare l'offerta

Ora che è stato distribuito il provider di risorse di SQL Server e creare un'offerta, è possibile accedere come utente, sottoscrivere l'offerta e creare un database.

### <a name="subscribe-to-the-offer"></a>Sottoscrivere l'offerta
1. Accedere al portale di Azure Stack (https://portal.local.azurestack.external) come un tenant.
2. Fare clic su **ottenere una sottoscrizione** e quindi digitare **TestSQLServerSubscription** in **nome visualizzato**.
3. Fare clic su **selezionare un'offerta** > **TestSQLServerOffer** > **crea**.
4. Fare clic su **più servizi** > **sottoscrizioni** > **TestSQLServerSubscription** > **risorse provider**.
5. Fare clic su **registrare** accanto al **Microsoft.SQLAdapter** provider.

### <a name="create-a-sql-database"></a>Creazione di un database SQL

1. Fare clic su  **+**   >  **dati e archiviazione** > **Database SQL**.
2. Lasciare le impostazioni predefinite per i campi oppure è possibile utilizzare questi esempi:
    - **Nome del database**: SQLdb
    - **Dimensioni massime in MB**: 100
    - **Sottoscrizione**: TestSQLOffer
    - **Gruppo di risorse**: SQL-RG
3. Fare clic su **impostazioni di accesso**, immettere le credenziali per il database e quindi fare clic su **OK**.
4. Fare clic su **SKU** > selezionare la SKU SQL creata per il Server di Hosting SQL > **OK**.
5. Fare clic su **Crea**.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Distribuire il provider di risorse di SQL Server
> * Creare un'offerta
> * Testare l'offerta

Per passare alla prossima esercitazione per informazioni su come:

> [!div class="nextstepaction"]
> [Rendere disponibili agli utenti web, dispositivi mobili e App per le API]( azure-stack-tutorial-app-service.md)

