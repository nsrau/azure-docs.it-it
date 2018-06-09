---
title: Rendere disponibili agli utenti di Azure Stack web e App per le API | Documenti Microsoft
description: Esercitazione per installare il provider di risorse del servizio App e creare offerte che consentono agli utenti di Azure Stack per creare web e App per le API.
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
ms.openlocfilehash: 0171dba639e480a04cdd1c7f23d546d01121fb42
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247399"
---
# <a name="tutorial-make-web-and-api-apps-available-to-your-azure-stack-users"></a>Esercitazione: rendere web e App per le API disponibili agli utenti di Azure Stack

Un amministratore di cloud di Azure Stack, è possibile creare offerte che consentono agli utenti di Azure funzioni e API di applicazioni web di creare (tenant). Fornendo agli utenti l'accesso a queste App on demand, basato sul cloud, è possibile salvarli tempo e risorse.

A tale scopo, è possibile:

> [!div class="checklist"]
> * Distribuire il provider di risorse del servizio App
> * Creare un'offerta
> * Testare l'offerta

## <a name="deploy-the-app-service-resource-provider"></a>Distribuire il provider di risorse del servizio App

1. [Preparare l'host di Azure Stack Development Kit](azure-stack-app-service-before-you-get-started.md). Ciò include la distribuzione di provider di risorse SQL Server, è necessario per la creazione di alcune applicazioni.
2. [Scaricare gli script di installazione e supporto](azure-stack-app-service-deploy.md).
3. [Eseguire lo script di supporto per creare certificati richiesti](azure-stack-app-service-deploy.md).
4. [Installare il provider di risorse di servizio App](azure-stack-app-service-deploy.md) (richiederà poche ore per installare e per tutti i ruoli di lavoro da visualizzare.)
5. [Convalidare l'installazione](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation).

## <a name="create-an-offer"></a>Creare un'offerta

Ad esempio, è possibile creare un'offerta che consente agli utenti di creare sistemi di gestione dei contenuti web DNN. Richiede il servizio SQL Server che già abilitata per l'installazione del provider di risorse di SQL Server.

1.  [Impostare una quota](azure-stack-setting-quotas.md) e denominarlo *AppServiceQuota*. Selezionare **Microsoft** per il **Namespace** campo.
2.  [Creare un piano](azure-stack-create-plan.md). Denominarlo *TestAppServicePlan*, selezionare il **Microsoft. SQL** servizio e il **AppService Quota** quota.

    > [!NOTE]
    > Per consentire agli utenti di creare altre App, altri servizi potrebbero essere necessario nel piano. Ad esempio, le funzioni di Azure richiede il **appartenga** servizio nel piano, mentre richiede Wordpress **Microsoft.MySQL**.

3.  [Creare un'offerta](azure-stack-create-offer.md), denominarla **TestAppServiceOffer** e selezionare il **TestAppServicePlan** piano.

## <a name="test-the-offer"></a>Testare l'offerta

Ora che è stato distribuito il provider di risorse del servizio App e creare un'offerta, è possibile accedere come utente, sottoscrivere l'offerta e creare un'app.

Per questo esempio, si creerà un sistema di gestione dei contenuti DNN piattaforma. Innanzitutto, creare un database SQL e quindi l'app web DNN.

### <a name="subscribe-to-the-offer"></a>Sottoscrivere l'offerta

1. Accedi al portale di Azure Stack (https://portal.local.azurestack.external) come tenant.
2. Selezionare **ottenere una sottoscrizione** >, immettere **TestAppServiceSubscription** sotto **nome visualizzato** > **selezionare un'offerta**  >  **TestAppServiceOffer** > **creare**.

### <a name="create-a-sql-database"></a>Creazione di un database SQL

1. Selezionare **+**  >  **dati e archiviazione** > **Database SQL**.
2. Mantenere i valori predefiniti, tranne i campi seguenti:

    - **Nome del database**: DNNdb
    - **Dimensioni massime in MB**: 100
    - **Sottoscrizione**: TestAppServiceOffer
    - **Gruppo di risorse**: DNN-RG

3. Selezionare **Login Settings**, immettere le credenziali per il database e quindi selezionare **OK**. Queste credenziali userai più avanti in questa esercitazione.
4. Sotto **SKU** > selezionare la SKU SQL creato per il Server di Hosting SQL > e quindi selezionare **OK**.
5. Selezionare **Create**.

### <a name="create-a-dnn-app"></a>Creare un'app DNN

1. Selezionare **+**  >  **vedere tutti** > **anteprima piattaforma DNN** > **crea** .
2. Immettere *DNNapp* sotto **nome App** e selezionare **TestAppServiceOffer** sotto **sottoscrizione**.
3. Selezionare **Configura le impostazioni obbligatorie** > **Crea nuovo** > Immettere un **piano di servizio App** nome.
4. Selezionare **tariffario** > **F1 libero** > **selezionare** > **OK**.
5. Selezionare **Database** e immettere le credenziali per il database SQL creato in precedenza.
6. Selezionare **Create**.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Distribuire il provider di risorse del servizio App
> * Creare un'offerta
> * Testare l'offerta

Per passare alla prossima esercitazione per informazioni su come:

> [!div class="nextstepaction"]
> [Distribuire le App in Azure e Azure Stack](user/azure-stack-solution-pipeline.md)
