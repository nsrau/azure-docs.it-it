---
title: 'Esercitazione: Aggiungere un pool elastico del database SQL di Azure a un gruppo di failover | Microsoft Docs'
description: Aggiungere un pool elastico del database SQL di Azure a un gruppo di failover usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: 5dd241fed757669cf8bccd96a1de948e8d73a021
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033257"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Esercitazione: Aggiungere un pool elastico del database SQL di Azure a un gruppo di failover

Configurare un gruppo di failover per un pool elastico del database SQL di Azure e un failover di test usando il portale di Azure.  In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> - Creare un database singolo del database SQL di Azure.
> - Aggiungere il database singolo in un pool elastico. 
> - Creare un [gruppo di failover](sql-database-auto-failover-group.md) per un pool elastico tra due server SQL logici.
> - Failover di test.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, accertarsi di avere: 

- Una sottoscrizione di Azure. [Creare un account gratuito](https://azure.microsoft.com/free/) se non ne è già disponibile uno.


## <a name="1---create-a-single-database"></a>1-creare un database singolo 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2-aggiungere un singolo database al pool elastico

1. Scegliere **Crea una risorsa** nell'angolo superiore sinistro della [portale di Azure](https://portal.azure.com).
1. Digitare `elastic pool` nella casella di ricerca, premere INVIO, selezionare l'icona del **pool di database elastici SQL** e quindi selezionare **Crea**. 

    ![Scegliere un pool elastico dal Marketplace](media/sql-database-elastic-pool-create-failover-group-tutorial/elastic-pool-market-place.png)

1. Configurare il pool elastico con i valori seguenti:
   - **Nome**: Consente di specificare un nome univoco per il pool elastico `myElasticPool`, ad esempio. 
   - **Sottoscrizione** Selezionare la sottoscrizione dall'elenco a discesa.
   - **Gruppo di risorse**: Selezionare `myResourceGroup` dall'elenco a discesa il gruppo di risorse creato nella sezione 1. 
   - **Server**: Selezionare il server creato nella sezione 1 dall'elenco a discesa.  

       ![Crea nuovo server per il pool elastico](media/sql-database-elastic-pool-create-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **Calcolo e archiviazione**: Selezionare **Configura pool elastico** per configurare il calcolo, l'archiviazione e aggiungere il database singolo al pool elastico. Nella scheda **Impostazioni pool** lasciare l'impostazione predefinita quinta generazione, con 2 Vcore e 32 GB. 

1. Nella pagina **Configura** selezionare la scheda **database** , quindi scegliere **Aggiungi database**. Scegliere il database creato nella sezione 1 e quindi fare clic su **applica** per aggiungerlo al pool elastico. Selezionare di nuovo **applica** per applicare le impostazioni del pool elastico e chiudere la pagina **Configura** . 

    ![Aggiungere un database SQL al pool elastico](media/sql-database-elastic-pool-create-failover-group-tutorial/add-database-to-elastic-pool.png)

1. Selezionare **Verifica + crea** per esaminare le impostazioni del pool elastico e quindi selezionare **Crea** per creare il pool elastico. 


## <a name="3---create-the-failover-group"></a>3-creare il gruppo di failover 
In questo passaggio verrà creato un gruppo di [failover](sql-database-auto-failover-group.md) tra un server SQL di Azure esistente e un nuovo server SQL di Azure in un'altra area. Aggiungere quindi il pool elastico al gruppo di failover. 


1. Selezionare **tutti i servizi** nell'angolo superiore sinistro della [portale di Azure](https://portal.azure.com). 
1. Digitare `sql servers` nella casella di ricerca. 
1. Opzionale Selezionare l'icona a stella accanto a SQL Server per aggiungere i **server SQL** ai Preferiti e aggiungerla al riquadro di spostamento a sinistra. 
    
    ![Individuare i server SQL](media/sql-database-single-database-create-failover-group-tutorial/all-services-sql-servers.png)

1. Selezionare **SQL Server** e scegliere il server creato nella sezione 1.
1. Selezionare **gruppi di failover** nel riquadro **Impostazioni** e quindi selezionare **Aggiungi gruppo** per creare un nuovo gruppo di failover. 

    ![Aggiungi nuovo gruppo di failover](media/sql-database-single-database-create-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Nella pagina **gruppo di failover** immettere o selezionare i valori seguenti e quindi selezionare **Crea**:
    - **Nome del gruppo di failover**: Digitare un nome di gruppo di failover univoco, ad `failovergrouptutorial`esempio. 
    - **Server secondario**: Selezionare l'opzione per *configurare le impostazioni necessarie* e quindi scegliere di **creare un nuovo server**. In alternativa, è possibile scegliere un server già esistente come server secondario. Dopo aver immesso i valori seguenti per il nuovo server secondario, selezionare **Seleziona**. 
        - **Nome server**: Digitare un nome univoco per il server secondario, ad esempio `mysqlsecondary`. 
        - **Account di accesso amministratore server**: Tipo`azureuser`
        - **Password**: Digitare una password complessa che soddisfi i corrispondenti requisiti.
        - **Località**: Scegliere un percorso dall'elenco a discesa, ad esempio Stati Uniti orientali 2. Il percorso non può essere uguale a quello del server primario.

       > [!NOTE]
       > L'account di accesso del server e le impostazioni del firewall devono corrispondere a quelle del server primario. 
    
       ![Creazione di un server secondario per il gruppo di failover](media/sql-database-single-database-create-failover-group-tutorial/create-secondary-failover-server.png)

1. Selezionare i **database all'interno del gruppo** e quindi selezionare il pool elastico creato nella sezione 2. Verrà visualizzato un avviso che richiede di creare un pool elastico nel server secondario. Selezionare l'avviso e quindi fare clic su **OK** per creare il pool elastico nel server secondario. 
        
    ![Aggiungere un pool elastico al gruppo di failover](media/sql-database-elastic-pool-create-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Selezionare **Seleziona** per applicare le impostazioni del pool elastico al gruppo di failover e quindi selezionare **Crea** per creare il gruppo di failover. L'aggiunta del pool elastico al gruppo di failover avvierà automaticamente il processo di replica geografica. 


## <a name="4---test-failover"></a>4-failover di test 
In questo passaggio si verificherà un errore nel gruppo di failover nel server secondario e quindi si eseguirà il failback utilizzando il portale di Azure. 

1. Passare al server **SQL Server** all'interno del [portale di Azure](https://portal.azure.com). 
1. Selezionare **gruppi di failover** nel riquadro **Impostazioni** , quindi scegliere il gruppo di failover creato nella sezione 2. 
  
   ![Selezionare il gruppo di failover dal portale](media/sql-database-single-database-create-failover-group-tutorial/select-failover-group.png)

1. Esaminare il server primario e il server secondario. 
1. Selezionare **failover** dal riquadro attività per eseguire il failover del gruppo di failover che contiene il pool elastico. 
1. Selezionare **Sì** nell'avviso che informa che le sessioni TDS verranno disconnesse. 

   ![Eseguire il failover del gruppo di failover contenente il database SQL](media/sql-database-single-database-create-failover-group-tutorial/failover-sql-db.png)

1. Esaminare il server primario, il server secondario. Se il failover ha avuto esito positivo, i due server dovrebbero avere ruoli scambiati. 
1. Selezionare di nuovo **failover** per non riuscire a ripristinare le impostazioni originali del gruppo di failover. 

## <a name="clean-up-resources"></a>Pulire le risorse 
Pulire le risorse eliminando il gruppo di risorse. 

1. Passare al gruppo di risorse nel [portale di Azure](https://portal.azure.com).
1. Selezionare **Elimina gruppo di risorse** per eliminare tutte le risorse nel gruppo, nonché il gruppo di risorse stesso. 
1. Digitare il nome del gruppo di risorse, `myResourceGroup`, nella casella di testo e quindi selezionare **Elimina** per eliminare il gruppo di risorse.  


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato aggiunto un database singolo di database SQL di Azure a un gruppo di failover e testato il failover. Si è appreso come:

> [!div class="checklist"]
> - Creare un database singolo del database SQL di Azure.
> - Aggiungere il database singolo in un pool elastico. 
> - Creare un [gruppo di failover](sql-database-auto-failover-group.md) per un pool elastico tra due server SQL logici.
> - Failover di test.

Passare all'esercitazione successiva per informazioni su come eseguire la migrazione con Servizio Migrazione del database.

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire la migrazione di SQL Server a un database in pool con DMS](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
