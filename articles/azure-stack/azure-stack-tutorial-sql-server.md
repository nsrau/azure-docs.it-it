---
title: Rendere disponibili i database SQL per gli utenti di Azure Stack | Microsoft Docs
description: Esercitazione per installare il provider di risorse di SQL Server e creare offerte che consentono agli utenti di Azure Stack creare database SQL.
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
ms.date: 11/05/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.custom: mvc
ms.openlocfilehash: ae32138dffa296a4aad9917897e180d364f189fc
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037831"
---
# <a name="tutorial-make-sql-databases-available-to-your-azure-stack-users"></a>Esercitazione: rendere disponibili i database SQL per gli utenti di Azure Stack

In qualità di amministratore di cloud di Azure Stack, è possibile creare offerte di consentono agli utenti (tenant) creare i database SQL che possono essere utilizzati con le app native del cloud, siti Web e i carichi di lavoro. Fornendo questi database personalizzati, on demand, basato sul cloud per gli utenti, è possibile salvarli tempo e risorse. Per configurare questa impostazione, si apprenderà come:

> [!div class="checklist"]
> * Distribuire il provider di risorse di SQL Server
> * Creare un'offerta
> * Testare l'offerta

## <a name="deploy-the-sql-server-resource-provider"></a>Distribuire il provider di risorse di SQL Server

Il processo di distribuzione viene descritto dettagliatamente il [database SQL Usa sull'articolo di Azure Stack](azure-stack-sql-resource-provider-deploy.md)e prevede i passaggi principali seguenti:

1. [Distribuire il provider di risorse SQL](azure-stack-sql-resource-provider-deploy.md).
2. [Verificare la distribuzione](azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal).
3. Offrono capacità tramite la connessione a un server di hosting SQL. Per altre informazioni, vedere [Aggiungi server di hosting](azure-stack-sql-resource-provider-hosting-servers.md)

## <a name="create-an-offer"></a>Creare un'offerta

1.  [Impostare una quota](azure-stack-setting-quotas.md) e denominarla *SQLServerQuota*. Selezionare **Microsoft.SQLAdapter** per il **Namespace** campo.
2.  [Creare un piano](azure-stack-create-plan.md). Denominarlo *TestSQLServerPlan*, selezionare la **Microsoft.SQLAdapter** servizio, e **SQLServerQuota** quota.

    > [!NOTE]
    > Per consentire agli utenti di creare altre App, potrebbero essere necessario ad altri servizi nel piano. Ad esempio, funzioni di Azure richiede la **Microsoft. Storage** servizio nel piano, mentre necessita di Wordpress **Microsoft.MySQLAdapter**.

3.  [Creare un'offerta](azure-stack-create-offer.md), denominarlo **TestSQLServerOffer** e selezionare il **TestSQLServerPlan** piano.

## <a name="test-the-offer"></a>Testare l'offerta

Ora che è stato distribuito il provider di risorse di SQL Server e creato un'offerta, è possibile accedere come un utente sottoscrive l'offerta e creare un database.

### <a name="subscribe-to-the-offer"></a>Sottoscrive l'offerta

1. Accedere al portale di Azure Stack (https://portal.local.azurestack.external) come tenant.
2. Selezionare **ottenere una sottoscrizione** e quindi immettere **TestSQLServerSubscription** sotto **nome visualizzato**.
3. Selezionare **selezionare un'offerta** > **TestSQLServerOffer** > **Create**.
4. Selezionare **tutti i servizi** > **sottoscrizioni** > **TestSQLServerSubscription** > **risorsa i provider**.
5. Selezionare **registrare** accanto al **Microsoft.SQLAdapter** provider.

### <a name="create-a-sql-database"></a>Creazione di un database SQL

1. Selezionare **+**  >  **dati + archiviazione** > **SQL Database**.
2. Mantenere i valori predefiniti o usare questi esempi per i campi seguenti:
    - **Nome del database**: database SQL
    - **Dimensioni massime in MB**: 100
    - **Sottoscrizione**: TestSQLOffer
    - **Gruppo di risorse**: SQL-RG
3. Selezionare **Login Settings**, immettere le credenziali per il database e quindi selezionare **OK**.
4. Selezionare **SKU** > selezionare lo SKU di SQL creato per il Server di Hosting SQL > e quindi selezionare **OK**.
5. Selezionare **Create**.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Distribuire il provider di risorse di SQL Server
> * Creare un'offerta
> * Testare l'offerta

Passare all'esercitazione successiva per apprendere come:

> [!div class="nextstepaction"]
> [Rendere disponibili agli utenti, dispositivi mobili, App web e API]( azure-stack-tutorial-app-service.md)