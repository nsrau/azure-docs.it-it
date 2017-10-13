---
title: Creare e connettersi a un database MySQL in Azure
description: Informazioni su come usare il portale di Azure per creare un database MySQL e connettersi al database da un'app Web PHP in Azure.
documentationcenter: php
services: app-service\web
author: cephalin
manager: erikre
editor: 
tags: mysql
ms.assetid: 55465a9a-7e65-4fd9-8a65-dd83ee41f3e5
ms.service: multiple
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm;cephalin
ms.openlocfilehash: c072cb3a7d376d1e3c2b9f741f5410106e701256
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>Creare e connettersi a un database MySQL in Azure
Questa esercitazione spiega come creare un database MySQL nel [portale di Azure](https://portal.azure.com) (provider [ClearDB](http://www.cleardb.com/)) e come connettersi al database da un'app Web PHP in esecuzione sul [Servizio app di Azure](app-service/app-service-web-overview.md).

> [!NOTE]
> È anche possibile creare un database MySQL come parte di un <a href="https://portal.azure.com/#create/WordPress.WordPress" target="_blank">modello di applicazione Marketplace</a>.
>
>

## <a name="create-a-mysql-database-in-azure-portal"></a>Creare un database MySQL nel portale di Azure
Per creare un database MySQL nel portale di Azure, seguire questa procedura:

1. Accedere al [Portale di Azure](https://portal.azure.com).
2. Nel menu a sinistra fare clic su **Nuovo** > **Dati e archiviazione** > **Database MySQL**.

    ![Creare un database MySQL in Azure: avvio](./media/store-php-create-mysql-database/create-db-1-start.png)
3. Nel [pannello](azure-portal-overview.md)Nuovo database MySQL, configurare il nuovo database MySQL come indicato di seguito (*pannello*: una pagina del portale che si apre orizzontalmente):

   * **Nome database**: digitare un nome identificabile in modo univoco.
   * **Sottoscrizione**: scegliere la sottoscrizione da usare.
   * **Tipo database**: selezionare **Condiviso** per i livelli gratuiti o a basso costo oppure **Dedicato** per ottenere risorse dedicate.
   * **Gruppo di risorse**: aggiungere il database MySQL a un [gruppo di risorse](azure-resource-manager/resource-group-overview.md) esistente o inserirlo in un nuovo gruppo. Le risorse all'interno dello stesso gruppo possono essere facilmente gestite insieme.
   * **Località**: scegliere una località vicina. Quando si aggiunge il database a un gruppo di risorse esistente, l'utente è vincolato alla località del gruppo di risorse.
   * **Piano tariffario**: fare clic su **Piano tariffario**, quindi selezionare un'opzione di prezzo (il livello **Mercurio** è gratuito) e fare clic su **Seleziona**.
   * **Note legali**: fare clic su **Note legali**, esaminare i dettagli dell'acquisto e fare clic su **Acquista**.
   * **Aggiungi al dashboard**: selezionare se si desidera accedere al database direttamente dal dashboard. Questa opzione è particolarmente utile se ancora non si ha familiarità con la navigazione nel portale.

     La schermata seguente rappresenta solo un esempio delle modalità in cui è possibile configurare il database MySQL.  
     ![Creare un database MySQL in Azure: configurazione](./media/store-php-create-mysql-database/create-db-2-configure.png)
4. Al termine della configurazione, scegliere **Crea**.

    ![Creare un database MySQL in Azure: creazione](./media/store-php-create-mysql-database/create-db-3-create.png)

    Viene visualizzata una notifica popup che informa che la distribuzione è stata avviata.

    ![Creare un database MySQL in Azure: in corso](./media/store-php-create-mysql-database/create-db-4-started-status.png)

    Al termine della distribuzione verrà visualizzata un'altra finestra popup. Il portale apre il pannello del database MySQL in automatico.

<a name="connect"></a>

## <a name="connect-to-your-mysql-database"></a>Connettersi al database MySQL
Per visualizzare le informazioni di connessione del nuovo database MySQL, fare clic su **Proprietà** nel pannello dell'app Web.

![Creare un database MySQL in Azure: pannello Database MySQL](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

È ora possibile usare le informazioni di connessione in qualsiasi app Web. [Qui](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql)è disponibile un esempio che illustra come usare le informazioni di connessione da una semplice app PHP.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni, vedere il [Centro per sviluppatori di PHP](/develop/php/).
