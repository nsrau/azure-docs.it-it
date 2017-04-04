---
title: 'Portale di Azure: creare un database SQL | Microsoft Docs'
description: Informazioni su come creare un server logico di database SQL, una regola del firewall a livello di server e database nel portale di Azure. Viene illustrato anche come effettuare una query su un database SQL di Azure usando il portale di Azure.
keywords: esercitazione sul database sql, creare un database sql
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: be5839e04fae457b889db11dffe56f31afe723a5
ms.lasthandoff: 03/28/2017


---
# <a name="create-an-azure-sql-database-in-the-azure-portal"></a>Creare un database SQL di Azure nel portale di Azure

Questa esercitazione introduttiva illustra come creare un database SQL in Azure.  Il database SQL di Azure è un'offerta di "database distribuito come servizio" che consente di eseguire e ridimensionare i database di SQL Server a disponibilità elevata nel cloud.  Questa guida introduttiva illustra come iniziare a creare un nuovo database SQL con il portale di Azure.

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure.

Accedere al [Portale di Azure](https://portal.azure.com/).

## <a name="create-a-sql-database"></a>Creazione di un database SQL

Un database SQL di Azure viene creato con un set definito di [risorse di calcolo e di archiviazione](sql-database-service-tiers.md). Il database viene creato in un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) e in un [server logico di database SQL di Azure](sql-database-features.md). 

Seguire questi passaggi per creare un database SQL contenente i dati di esempio di Adventure Works LT. 

1. Fare clic sul pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.

2. Selezionare **Database** nella pagina **Nuovo** e quindi **Database SQL** nella pagina **Database**.

    ![Creare il database 1](./media/sql-database-get-started/create-database-1.png)

3. Compilare il modulo Database SQL con le informazioni seguenti, come illustrato nell'immagine precedente: 
   - Nome database: usare **mySampleDatabase**
   - Gruppo di risorse: usare **myResourceGroup**
   - Origine: selezionare **Sample (AdventureWorksLT)**

4. Fare clic su **Server** per creare e configurare un nuovo server per il nuovo database. Compilare il **modulo Nuovo server** specificando un nome server univoco globale, immettere un nome per l'accesso amministratore server e quindi specificare la password scelta. 

    ![Creare il server di database](./media/sql-database-get-started/create-database-server.png)
5. Fare clic su **Seleziona**.

6. Fare clic su **Piano tariffario** per specificare il livello di servizio e il livello delle prestazioni per il nuovo database. Per questa guida introduttiva selezionare **20 DTU** e **250** GB di memoria

    ![Creare il database s1](./media/sql-database-get-started/create-database-s1.png)

7. Fare clic su **Apply**.  

8. Fare clic su **Crea** per effettuare il provisioning del database. Il provisioning richiede alcuni minuti. 

9. Sulla barra degli strumenti fare clic su **Notifiche** per monitorare il processo di distribuzione.

    ![notifica](./media/sql-database-get-started/notification.png)


## <a name="create-a-server-level-firewall-rule"></a>Creare una regola del firewall a livello di server

Il servizio di database SQL crea un firewall a livello di server che impedisce alle applicazioni e agli strumenti esterni di connettersi al server o ai database sul server a meno che non venga creata una regola del firewall per aprire il firewall per indirizzi IP specifici. Seguire questi passaggi per creare una [regola del firewall a livello di server di database SQL](sql-database-firewall-configure.md) per l'indirizzo IP del client e abilitare la connettività esterna tramite il firewall del database SQL solo per il proprio indirizzo IP. 

1. Al termine della distribuzione, scegliere **Database SQL** dal menu a sinistra e fare clic sul nuovo database, **mySampleDatabase**, nella pagina **Database SQL**. Si apre la pagina di panoramica per il database che visualizza il nome completo del server (ad esempio, **mynewserver20170327.database.windows.net**) e fornisce altre opzioni di configurazione.

      ![Regola del firewall del server](./media/sql-database-get-started/server-firewall-rule.png) 

2. Fare clic su **Imposta firewall server** sulla barra degli strumenti, come illustrato nell'immagine precedente. Si apre la pagina **Impostazioni del firewall** per il server del database SQL. 

3. Fare clic su **Aggiungi IP client** sulla barra degli strumenti e fare clic su **Salva**. Una regola del firewall a livello di server viene creata per l'indirizzo IP corrente.

      ![Impostare la regola del firewall del server](./media/sql-database-get-started/server-firewall-rule-set.png) 

4. Fare clic su **OK** e quindi sulla **X** per chiudere la pagina **Impostazioni del firewall**.

È ora possibile connettersi al database e al server usando SQL Server Management Studio o un altro strumento di propria scelta.

## <a name="query-the-sql-database"></a>Effettuare una query sul database SQL

Quando è stato creato il database SQL, è stato popolato con il database di esempio **AdventureWorksLT**, che era una delle opzioni selezionate prima nell'interfaccia utente di creazione di questa guida introduttiva. Ora verrà usato lo strumento di query predefinito nel portale di Azure per effettuare una query dei dati. 

1. Nella pagina Database SQL del database fare clic su **Strumenti** sulla barra degli strumenti. Si apre la pagina **Strumenti**.

     ![Menu Strumenti](./media/sql-database-get-started/tools-menu.png) 

2. Fare clic su **Editor di query (anteprima)**, fare clic sulla casella di controllo **Condizioni per l'anteprima** e quindi su **OK**. Si apre la pagina Editor di query.

3. Fare clic su **Accedi** e quindi, quando viene richiesto, selezionare **Autenticazione di SQL Server** e infine immettere l'account e la password di accesso amministratore server creata prima.

    ![Accesso](./media/sql-database-get-started/login.png) 

4. Fare clic su **OK** per accedere.

5. Dopo l'autenticazione, digitare la query seguente nel riquadro dell'editor di query.

   ```
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

6. Fare clic su **Esegui** e quindi esaminare i risultati della query nel riquadro **Risultati**.

    ![Risultati dell'Editor di query](./media/sql-database-get-started/query-editor-results.png)

7. Fare clic sulla **X** per chiudere la pagina **Editor di query** e fare di nuovo clic sulla **X** per chiudere la pagina **Strumenti**.

## <a name="clean-up-resources"></a>Pulire le risorse

Altre guide introduttive di questa raccolta si basano sui valori di questa guida introduttiva. Se si prevede di continuare a usare le guide introduttive successive o le esercitazioni, non eliminare le risorse create in questa guida introduttiva. Se non si prevede di continuare, seguire questa procedura per eliminare tutte le risorse create da questa guida introduttiva nel portale di Azure.

1. Scegliere **Gruppi di risorse** dal menu a sinistra del portale di Azure e quindi fare clic su **myResourceGroup**. 
2. Nella pagina del gruppo di risorse fare clic su **Elimina**, digitare **myResourceGroup** nella casella di testo e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

- Per connettersi ed effettuare una query usando SQL Server Management Studio, vedere [Connettersi ed eseguire una query con SSMS](sql-database-connect-query-ssms.md)
- Per connettersi usando Visual Studio, vedere [Connettersi ed eseguire query con Visual Studio](sql-database-connect-query.md).
- Per una panoramica tecnica del database SQL, vedere [Informazioni sul servizio di database SQL](sql-database-technical-overview.md).

