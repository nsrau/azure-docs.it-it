---
title: Creare e connettersi a un database MySQL in Azure
description: Informazioni su come usare il portale di Azure per creare un database MySQL e connettersi al database da un&quot;app Web PHP in Azure.
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
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: e3d44bc4ff6ce7ebfcd510ef8b40fd0c614bc31e
ms.lasthandoff: 04/11/2017


---
# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>Creare e connettersi a un database MySQL in Azure
Questa esercitazione spiega come creare un database MySQL nel [portale di Azure](https://portal.azure.com) (provider [ClearDB](http://www.cleardb.com/)) e come connettersi al database da un'app Web PHP in esecuzione sul [Servizio app di Azure](app-service/app-service-value-prop-what-is.md).

> [!NOTE]
> È anche possibile creare un database MySQL come parte di un [modello di applicazione Marketplace](app-service-web/app-service-web-create-web-app-from-marketplace.md).
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

## <a name="connect-a-laravel-web-app-from-the-php-get-started-tutorial"></a>Connettere un'app web Laravel (dall'esercitazione introduttiva di PHP)
Si supponga che sia stata completata l'esercitazione [Creare, configurare e distribuire un'app Web PHP in Azure](app-service-web/app-service-web-php-get-started.md) e che un'app Web [Laravel](https://www.laravel.com/) sia in esecuzione su Azure. È possibile aggiungere funzionalità di database all'app Laravel in modo semplice. Seguire questa procedura:

> [!NOTE]
> Per questa procedura si presuppone che sia stata completata l'esercitazione [Creare, configurare e distribuire un'app Web PHP in Azure](app-service-web/app-service-web-php-get-started.md).
>
>

1. Configurare l'app Laravel nell'ambiente di sviluppo locale in modo che punti al database MySQL. A tale scopo, aprire `.env` dalla directory radice dell'app Laravel e configurare le opzioni del database MySQL.

        DB_CONNECTION=mysql
        DB_HOST=<HOSTNAME_from_properties_blade>
        DB_PORT=<PORT_from_properties_blade>
        DB_DATABASE=<see_note_below>
        DB_USERNAME=<USERNAME_from_properties_blade>
        DB_PASSWORD=<PASSWORD_from_properties_blade>

   > [!NOTE]
   > Nel pannello **Proprietà** il nome del database MySQL potrebbe non essere l'unico visualizzato nel campo **Nome database**. È consigliabile controllare il parametro Database nel campo **Stringa di connessione** .    
   >
   > ![Creare un database MySQL in Azure: in corso](./media/store-php-create-mysql-database/connect-db-1-database-name.png)
   >
   >
2. A questo punto, il modo più rapido per verificare di avere accesso a MySQL è usare lo [scaffolding di autenticazione predefinito di Laravel](https://laravel.com/docs/5.2/authentication#authentication-quickstart).
   Nel terminale della riga di comando eseguire i comandi seguenti dalla directory radice dell'app Laravel:

         php artisan migrate
         php artisan make:auth

    Il primo comando crea le tabelle in Azure in base alle migrazioni predefinite nella directory `database/migrations`, mentre il secondo comando esegue lo scaffolding delle viste e delle route di base per la registrazione e l'autenticazione dell'utente.
3. A questo punto, eseguire il server di sviluppo:

        php artisan serve
4. Nel browser accedere ad http://localhost:8000 e registrare un nuovo utente come illustrato:

    ![Connettersi al database MySQL in Azure: registrazione utente](./media/store-php-create-mysql-database/connect-db-2-development-server.png)

    Seguire i prompt dell'interfaccia utente per completare la registrazione. Dopo aver completato la registrazione, l'utente accede in automatico.

    ![Connettersi al database MySQL in Azure: registrazione utente](./media/store-php-create-mysql-database/connect-db-3-registered-user.png)

    Si sta sviluppando l'app con il database MySQL in Azure.
5. A questo punto, è sufficiente replicare le impostazioni `.env` nell'app Web di Azure. Eseguire i comandi seguenti dell'interfaccia della riga di comando di Azure:

        azure site appsetting add DB_CONNECTION=mysql
        azure site appsetting add DB_HOST=<HOSTNAME_from_properties_blade>
        azure site appsetting add DB_PORT=<PORT_from_properties_blade>
        azure site appsetting add DB_DATABASE=<Database_param_from_CONNECTION_INFO_from_properties_blade>
        azure site appsetting add DB_USERNAME=<USERNAME_from_properties_blade>
        azure site appsetting add DB_PASSWORD=<PASSWORD_from_properties_blade>

    Per informazioni sul funzionamento, vedere [Configure the Azure web app](app-service-web/app-service-web-get-started-php.md#configure-to-use-php)(Configurare l'app Web di Azure).
6. Successivamente, eseguire il commit e il push in Azure delle modifiche locali apportate in precedenza durante l'esecuzione di `php artisan make:auth`.

        git add .
        git commit -m "scaffold auth views and routes"
        git push azure master
7. Passare all'app Web di Azure.

        azure site browse
8. Accedere usando le credenziali utente create in precedenza.

    ![Connettersi al database MySQL in Azure: passare all'app Web di Azure](./media/store-php-create-mysql-database/connect-db-4-browse-azure-webapp.png)

    Dopo l'accesso, verrà visualizzata una schermata descrittiva.

    ![Connettersi al database MySQL in Azure: accesso eseguito](./media/store-php-create-mysql-database/connect-db-5-logged-in.png)

    Complimenti, l'app Web PHP di Azure dispone ora di accesso ai dati del database MySQL.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni, vedere il [Centro per sviluppatori di PHP](/develop/php/).

