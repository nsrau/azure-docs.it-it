---
title: Creare un'istanza del Servizio Migrazione del database di Azure mediante il portale di Azure | Microsoft Docs
description: Usare il portale di Azure per creare un'istanza del Servizio Migrazione del database di Azure
services: database-migration
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/28/2017
ms.openlocfilehash: 7fc4f8521afa41f21cda6576459a0794bef9ad3b
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2017
---
# <a name="create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Creare un'istanza del Servizio Migrazione del database di Azure usando il portale di Azure
In questa guida introduttiva usare il portale di Azure per creare un'istanza del servizio Migrazione del database di Azure.  Dopo aver creato il servizio, è possibile usarlo per eseguire la migrazione dei dati da SQL Server locale a un database SQL di Azure.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure.
Aprire il Web browser e passare al [portale di Microsoft Azure](https://portal.azure.com/). Immettere le credenziali per accedere al portale. La visualizzazione predefinita è il dashboard del servizio.

## <a name="register-the-resource-provider"></a>Registrare il provider di risorse
Prima di creare la prima istanza del servizio Migrazione del database, registrare il provider di risorse Microsoft.DataMigration.

1. Nel portale di Azure selezionare **Tutti i servizi** e quindi **Sottoscrizioni**.

2. Selezionare la sottoscrizione in cui si vuole creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.

3. Cercare la migrazione e quindi a destra di Microsoft.DataMigration selezionare **Registra**.

![Registrare il provider di risorse](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>Creare un'istanza del servizio
1. Fare clic su **+ Crea una risorsa** per creare un'istanza del servizio Migrazione del database di Azure, attualmente in anteprima.

2. Cercare "migrazione" nel marketplace, selezionare **Servizio Migrazione del database di Azure** e quindi nella schermata **Azure Database Migration Service (preview)** (Servizio Migrazione del database di Azure - Anteprima) fare clic su **Crea**.

3. Nella schermata **Servizio Migrazione del database**: 

    - Scegliere un **nome servizio** facile da ricordare e univoco per identificare l'istanza del servizio Migrazione del database di Azure.
    - Selezionare la **sottoscrizione** di Azure in cui creare l'istanza.
    - Creare una nuova **Rete** con un nome univoco.
    - Scegliere la **Posizione** più vicina al server di origine o di destinazione.
    - Selezionare Basic: 1 vCore per il **Piano tariffario**.

    ![Creare il servizio migrazione](media/quickstart-create-data-migration-service-portal/dms-create-service.png)
4. Selezionare **Crea**.

Dopo qualche istante, l'istanza del servizio Migrazione del database di Azure viene creata ed è pronta per l'uso. Il servizio Migrazione del database viene visualizzato come illustrato nell'immagine seguente:

![Creare il servizio migrazione](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Pulire le risorse
È possibile pulire le risorse create in questa guida introduttiva eliminando il [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md).  Per eliminare il gruppo di risorse, passare all'istanza del servizio Migrazione del database di Azure creata. Selezionare il nome **Gruppo di risorse** e quindi selezionare **Elimina gruppo di risorse**.  Questa azione elimina tutti gli asset nel gruppo di risorse, nonché il gruppo stesso.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Eseguire la migrazione di SQL Server locale nel database SQL di Azure](tutorial-sql-server-to-azure-sql.md)