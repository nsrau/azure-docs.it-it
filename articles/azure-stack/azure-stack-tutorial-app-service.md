---
title: Rendere disponibili agli utenti di Azure Stack web e App per le API | Documenti Microsoft
description: Esercitazione per installare il provider di risorse del servizio App e creare offerte che consentono agli utenti di Azure Stack per creare web e App per le API.
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 7/03/2017
ms.author: brenduns
ms.reviewer: 
ms.custom: mvc
ms.openlocfilehash: 5978706f2cab69c83a49bfd0e15ae904a38c7bab
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
---
# <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>Rendere disponibili agli utenti dello Stack di Azure, App web e API

Un amministratore di cloud di Azure Stack, è possibile creare offerte che consentono agli utenti di Azure funzioni e API di applicazioni web di creare (tenant). Fornendo l'accesso a queste App su richiesta, basato su cloud agli utenti, è possibile salvarli tempo e risorse. A tale scopo, è possibile:

> [!div class="checklist"]
> * Distribuire il provider di risorse del servizio App
> * Creare un'offerta
> * Testare l'offerta

## <a name="deploy-the-app-service-resource-provider"></a>Distribuire il provider di risorse del servizio App

1. [Preparare l'host di Azure Stack Development Kit](azure-stack-app-service-before-you-get-started.md). Ciò include la distribuzione di provider di risorse SQL Server, è necessario per la creazione di alcune applicazioni.
2. [Scaricare gli script di installazione e supporto](azure-stack-app-service-deploy.md).
3. [Eseguire lo script di supporto per creare certificati richiesti](azure-stack-app-service-deploy.md).
4. [Installare il provider di risorse del servizio App](azure-stack-app-service-deploy.md) (richiederà un paio di ore per installare e per tutti i ruoli di lavoro da visualizzare).
5. [Convalidare l'installazione](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation).

## <a name="create-an-offer"></a>Creare un'offerta

Ad esempio, è possibile creare un'offerta che consente agli utenti di creare sistemi di gestione dei contenuti web DNN. Richiede il servizio SQL Server che già abilitata per l'installazione del provider di risorse di SQL Server.

1.  [Impostare una quota](azure-stack-setting-quotas.md) e denominarlo *AppServiceQuota*. Selezionare **Microsoft** per il **Namespace** campo.
2.  [Creare un piano](azure-stack-create-plan.md). Il nome *TestAppServicePlan*, selezionare il **Microsoft.SQL** servizio, e **AppService Quota** quota.

    > [!NOTE]
    > Per consentire agli utenti di creare altre App, altri servizi potrebbero essere necessario nel piano. Ad esempio, le funzioni di Azure richiede che il piano include il **Microsoft.Storage** il servizio, mentre richiede Wordpress **Microsoft.MySQL**.
    > 
    >

3.  [Creare un'offerta](azure-stack-create-offer.md), denominarla **TestAppServiceOffer** e selezionare il **TestAppServicePlan** piano.

## <a name="test-the-offer"></a>Testare l'offerta

Ora che è stato distribuito il provider di risorse del servizio App e creare un'offerta, è possibile accedere come utente, sottoscrivere l'offerta e creare un'app. Per questo esempio, si creerà un sistema di gestione dei contenuti DNN piattaforma. È necessario innanzitutto creare un database SQL e quindi l'app web DNN.

### <a name="subscribe-to-the-offer"></a>Sottoscrivere l'offerta
1. Accedere al portale di Azure Stack (https://portal.local.azurestack.external) come un tenant.
2. Fare clic su **ottenere una sottoscrizione** > tipo **TestAppServiceSubscription** in **nome visualizzato** > **selezionare un'offerta**  >  **TestAppServiceOffer** > **creare**.

### <a name="create-a-sql-database"></a>Creazione di un database SQL

1. Fare clic su  **+**   >  **dati e archiviazione** > **Database SQL**.
2. Lasciare le impostazioni predefinite per i campi, ad eccezione di, come indicato di seguito:
    - **Nome del database**: DNNdb
    - **Dimensioni massime in MB**: 100
    - **Sottoscrizione**: TestAppServiceOffer
    - **Gruppo di risorse**: DNN-RG
3. Fare clic su **impostazioni di accesso**, immettere le credenziali per il database e quindi fare clic su **OK**. Utilizzare queste credenziali più avanti in questa procedura.
4. Fare clic su **SKU** > selezionare la SKU SQL creata per il Server di Hosting SQL > **OK**.
5. Fare clic su **Crea**.

### <a name="create-a-dnn-app"></a>Creare un'app DNN    

1. Fare clic su  **+**   >  **tutti** > **anteprima piattaforma DNN** > **crea**.
2. Tipo *DNNapp* in **nome App** e selezionare **TestAppServiceOffer** in **sottoscrizione**.
3. Fare clic su **Configura le impostazioni obbligatorie** > **Crea nuovo** > tipo un **piano di servizio App** nome.
4. Fare clic su **tariffario** > **F1 libero** > **selezionare** > **OK**.
5. Fare clic su **Database** e immettere le informazioni per il database SQL creato in precedenza.
6. Fare clic su **Crea**.

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Distribuire il provider di risorse del servizio App
> * Creare un'offerta
> * Testare l'offerta

Per passare alla prossima esercitazione per informazioni su come:

> [!div class="nextstepaction"]
> [Distribuire le App in Azure e Azure Stack](user/azure-stack-solution-pipeline.md)
