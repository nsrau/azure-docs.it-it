---
title: Rendere disponibili agli utenti dello Stack di Azure SQL database | Documenti Microsoft
description: Esercitazione per installare il provider di risorse di SQL Server e creare offerte che consentono agli utenti di Azure Stack di creare il database SQL.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/05/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: b9ba2bb89bb0d7e16a28a165cf14530a7a10f71b
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234751"
---
# <a name="tutorial-make-sql-databases-available-to-your-azure-stack-users"></a>Esercitazione: rendere disponibili i database SQL per gli utenti di Azure Stack

Un amministratore di cloud di Azure Stack, è possibile creare offerte che consentono agli utenti di database SQL che possono essere utilizzati con le app cloud nativo, siti Web e i carichi di lavoro di creare (tenant). Grazie agli utenti i database personalizzati, su richiesta, basato su cloud, è possibile salvarli tempo e risorse. A tale scopo, è possibile:

> [!div class="checklist"]
> * Distribuire il provider di risorse di SQL Server
> * Creare un'offerta
> * Testare l'offerta

## <a name="deploy-the-sql-server-resource-provider"></a>Distribuire il provider di risorse di SQL Server

Viene descritto il processo di distribuzione in modo dettagliato nel [database SQL di utilizzo in articolo Azure Stack](azure-stack-sql-resource-provider-deploy.md)e prevede i passaggi principali seguenti:

1. [Distribuire il provider di risorse SQL](azure-stack-sql-resource-provider-deploy.md).
2. [Verificare la distribuzione](azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal).
3. Connessione a un host di SQL server per fornire capacità. Per altre informazioni, vedere [Aggiungi server di hosting](azure-stack-sql-resource-provider-hosting-servers.md)

## <a name="create-an-offer"></a>Creare un'offerta

1.  [Impostare una quota](azure-stack-setting-quotas.md) e denominarlo *SQLServerQuota*. Selezionare **Microsoft.SQLAdapter** per il **Namespace** campo.
2.  [Creare un piano](azure-stack-create-plan.md). Il nome *TestSQLServerPlan*, selezionare il **Microsoft.SQLAdapter** servizio, e **SQLServerQuota** quota.

    > [!NOTE]
    > Per consentire agli utenti di creare altre App, altri servizi potrebbero essere necessario nel piano. Ad esempio, le funzioni di Azure richiede il **appartenga** servizio nel piano, mentre richiede Wordpress **Microsoft.MySQLAdapter**.

3.  [Creare un'offerta](azure-stack-create-offer.md), denominarla **TestSQLServerOffer** e selezionare il **TestSQLServerPlan** piano.

## <a name="test-the-offer"></a>Testare l'offerta

Ora che è stato distribuito il provider di risorse di SQL Server e creare un'offerta, è possibile accedere come utente, sottoscrivere l'offerta e creare un database.

### <a name="subscribe-to-the-offer"></a>Sottoscrivere l'offerta

1. Accedi al portale di Azure Stack (https://portal.local.azurestack.external) come tenant.
2. Selezionare **ottenere una sottoscrizione** e quindi immettere **TestSQLServerSubscription** sotto **nome visualizzato**.
3. Selezionare **selezionare un'offerta** > **TestSQLServerOffer** > **crea**.
4. Selezionare **altri servizi** > **sottoscrizioni** > **TestSQLServerSubscription** > **risorse provider**.
5. Selezionare **registrare** accanto al **Microsoft.SQLAdapter** provider.

### <a name="create-a-sql-database"></a>Creazione di un database SQL

1. Selezionare **+**  >  **dati e archiviazione** > **Database SQL**.
2. Mantenere i valori predefiniti o usare questi esempi per i campi seguenti:
    - **Nome del database**: SQLdb
    - **Dimensioni massime in MB**: 100
    - **Sottoscrizione**: TestSQLOffer
    - **Gruppo di risorse**: SQL-RG
3. Selezionare **Login Settings**, immettere le credenziali per il database e quindi selezionare **OK**.
4. Selezionare **SKU** > selezionare la SKU SQL creato per il Server di Hosting SQL > e quindi selezionare **OK**.
5. Selezionare **Create**.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Distribuire il provider di risorse di SQL Server
> * Creare un'offerta
> * Testare l'offerta

Per passare alla prossima esercitazione per informazioni su come:

> [!div class="nextstepaction"]
> [Rendere disponibili agli utenti web, dispositivi mobili e App per le API]( azure-stack-tutorial-app-service.md)