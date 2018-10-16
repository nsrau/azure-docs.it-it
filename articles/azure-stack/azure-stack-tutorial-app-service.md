---
title: Rendere disponibili agli utenti di Azure Stack web e App per le API | Microsoft Docs
description: Esercitazione per installare il provider di risorse del servizio App e creare offre agli utenti di Azure Stack che offrono la possibilità di creare App web e API.
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
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 0a9b87ccfd49ba04a8dff8ef48bea023ff94b222
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340730"
---
# <a name="tutorial-make-web-and-api-apps-available-to-your-azure-stack-users"></a>Esercitazione: rendere web e App per le API disponibili per gli utenti di Azure Stack

In qualità di amministratore di cloud di Azure Stack, è possibile creare offerte di consentono agli utenti (tenant) creare applicazioni web e funzioni di Azure e API. Assegnando agli utenti l'accesso a queste App on demand, basato sul cloud, è possibile salvarli tempo e risorse.

Per configurare questa impostazione, si apprenderà come:

> [!div class="checklist"]
> * Distribuire il provider di risorse del servizio App
> * Creare un'offerta
> * Testare l'offerta

## <a name="deploy-the-app-service-resource-provider"></a>Distribuire il provider di risorse del servizio App

1. [Preparare l'host di Azure Stack Development Kit](azure-stack-app-service-before-you-get-started.md). Ciò include la distribuzione il provider di risorse SQL Server, è necessario per la creazione di alcune app.
2. [Scaricare il programma di installazione e script helper](azure-stack-app-service-deploy.md).
3. [Eseguire lo script helper per creare i certificati richiesti](azure-stack-app-service-deploy.md).
4. [Installare il provider di risorse del servizio App](azure-stack-app-service-deploy.md) (saranno necessarie alcune ore per installare e per tutti i ruoli di lavoro da visualizzare.)
5. [Convalidare l'installazione](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation).

## <a name="create-an-offer"></a>Creare un'offerta

Ad esempio, è possibile creare un'offerta che consente agli utenti di creare sistemi di gestione dei contenuti web di rete neurale profonda. Richiede il servizio SQL Server che già abilitata per l'installazione del provider di risorse di SQL Server.

1.  [Impostare una quota](azure-stack-setting-quotas.md) e denominarla *AppServiceQuota*. Selezionare **Microsoft. Web** per il **Namespace** campo.
2.  [Creare un piano](azure-stack-create-plan.md). Denominarlo *TestAppServicePlan*, selezionare la **Microsoft. SQL** servizio e il **AppService Quota** quota.

    > [!NOTE]
    > Per consentire agli utenti di creare altre App, potrebbero essere necessario ad altri servizi nel piano. Ad esempio, funzioni di Azure richiede la **Microsoft. Storage** servizio nel piano, mentre necessita di Wordpress **Microsoft.MySQL**.

3.  [Creare un'offerta](azure-stack-create-offer.md), denominarlo **TestAppServiceOffer** e selezionare il **TestAppServicePlan** piano.

## <a name="test-the-offer"></a>Testare l'offerta

Ora che è stato distribuito il provider di risorse del servizio App e creato un'offerta, è possibile accedere come un utente sottoscrive l'offerta e creare un'app.

In questo esempio si creerà un sistema di gestione dei contenuti di piattaforma DNN. In primo luogo, si crea un database SQL e quindi l'app web di rete neurale profonda.

### <a name="subscribe-to-the-offer"></a>Sottoscrive l'offerta

1. Accedere al portale di Azure Stack (https://portal.local.azurestack.external) come tenant.
2. Selezionare **ottenere una sottoscrizione** >, immettere **TestAppServiceSubscription** sotto **nome visualizzato** > **selezionare un'offerta**  >  **TestAppServiceOffer** > **creare**.

### <a name="create-a-sql-database"></a>Creazione di un database SQL

1. Selezionare **+**  >  **dati + archiviazione** > **SQL Database**.
2. Mantenere i valori predefiniti, fatta eccezione per i campi seguenti:

    - **Nome del database**: DNNdb
    - **Dimensioni massime in MB**: 100
    - **Sottoscrizione**: TestAppServiceOffer
    - **Gruppo di risorse**: rete neurale profonda-RG

3. Selezionare **Login Settings**, immettere le credenziali per il database e quindi selezionare **OK**. Si userà queste credenziali più avanti in questa esercitazione.
4. Sotto **SKU** > selezionare lo SKU di SQL creato per il Server di Hosting SQL > e quindi selezionare **OK**.
5. Selezionare **Create**.

### <a name="create-a-dnn-app"></a>Creare un'app di rete neurale profonda

1. Selezionare **+**  >  **Vedi tutto** > **anteprima piattaforma DNN** > **crea** .
2. Immettere *DNNapp* sotto **nome App** e selezionare **TestAppServiceOffer** sotto **sottoscrizione**.
3. Selezionare **configurare le impostazioni necessarie** > **Crea nuovo** > Immettere un **piano di servizio App** nome.
4. Selezionare **piano tariffario** > **F1 gratuito** > **selezionare** > **OK**.
5. Selezionare **Database** e immettere le credenziali per il database SQL creato in precedenza.
6. Selezionare **Create**.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Distribuire il provider di risorse del servizio App
> * Creare un'offerta
> * Testare l'offerta

Passare all'esercitazione successiva per apprendere come:

> [!div class="nextstepaction"]
> [Distribuire le App in Azure e Azure Stack](user/azure-stack-solution-pipeline.md)
