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
ms.openlocfilehash: 2d46e6f1d5c7079ab5bbfea39a85ea0a7592afc8
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099227"
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

1. Selezionare **Azure SQL** nel menu a sinistra nel portale di Azure. Se **SQL di Azure** non è presente nell'elenco, selezionare **tutti i servizi**, quindi digitare Azure SQL nella casella di ricerca. Opzionale Selezionare la stella accanto a **SQL di Azure** per favorirla e aggiungerla come elemento nel menu di spostamento a sinistra. 
1. Selezionare **+ Aggiungi** per aprire la pagina **Seleziona opzione distribuzione SQL** . È possibile visualizzare informazioni aggiuntive sui diversi database selezionando Mostra dettagli nel riquadro database.
1. Selezionare **pool elastico** dall'elenco a discesa **tipo di risorsa** nel riquadro **database SQL** . Selezionare **Crea** per creare il pool elastico. 

    ![Seleziona pool elastico](media/sql-database-elastic-pool-failover-group-tutorial/select-azure-sql-elastic-pool.png)

1. Configurare il pool elastico con i valori seguenti:
   - **Nome**: Consente di specificare un nome univoco per il pool elastico `myElasticPool`, ad esempio. 
   - **Sottoscrizione** Selezionare la sottoscrizione dall'elenco a discesa.
   - **Gruppo di risorse**: Selezionare `myResourceGroup` dall'elenco a discesa il gruppo di risorse creato nella sezione 1. 
   - **Server**: Selezionare il server creato nella sezione 1 dall'elenco a discesa.  

       ![Crea nuovo server per il pool elastico](media/sql-database-elastic-pool-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **Calcolo e archiviazione**: Selezionare **Configura pool elastico** per configurare il calcolo, l'archiviazione e aggiungere il database singolo al pool elastico. Nella scheda **Impostazioni pool** lasciare l'impostazione predefinita quinta generazione, con 2 Vcore e 32 GB. 

1. Nella pagina **Configura** selezionare la scheda **database** , quindi scegliere **Aggiungi database**. Scegliere il database creato nella sezione 1 e quindi fare clic su **applica** per aggiungerlo al pool elastico. Selezionare di nuovo **applica** per applicare le impostazioni del pool elastico e chiudere la pagina **Configura** . 

    ![Aggiungere un database SQL al pool elastico](media/sql-database-elastic-pool-failover-group-tutorial/add-database-to-elastic-pool.png)

1. Selezionare **Verifica + crea** per esaminare le impostazioni del pool elastico e quindi selezionare **Crea** per creare il pool elastico. 


## <a name="3---create-the-failover-group"></a>3-creare il gruppo di failover 
In questo passaggio verrà creato un gruppo di [failover](sql-database-auto-failover-group.md) tra un server SQL di Azure esistente e un nuovo server SQL di Azure in un'altra area. Aggiungere quindi il pool elastico al gruppo di failover. 

1. Selezionare **Azure SQL** nel menu a sinistra del [portale di Azure](https://portal.azure.com). Se **SQL di Azure** non è presente nell'elenco, selezionare **tutti i servizi**, quindi digitare Azure SQL nella casella di ricerca. Opzionale Selezionare la stella accanto a **SQL di Azure** per favorirla e aggiungerla come elemento nel menu di spostamento a sinistra. 
1. Selezionare il pool elastico creato nella sezione precedente, ad esempio `myElasticPool`. 
1. Nel riquadro **Panoramica** selezionare il nome del server in **nome server** per aprire le impostazioni per il server.
  
    ![Apri server per pool elastico](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Selezionare **gruppi di failover** nel riquadro **Impostazioni** e quindi selezionare **Aggiungi gruppo** per creare un nuovo gruppo di failover. 

    ![Aggiungi nuovo gruppo di failover](media/sql-database-elastic-pool-failover-group-tutorial/elastic-pool-failover-group.png)

1. Nella pagina **gruppo di failover** immettere o selezionare i valori seguenti e quindi selezionare **Crea**:
    - **Nome del gruppo di failover**: Digitare un nome di gruppo di failover univoco, ad `failovergrouptutorial`esempio. 
    - **Server secondario**: Selezionare l'opzione per *configurare le impostazioni necessarie* e quindi scegliere di **creare un nuovo server**. In alternativa, è possibile scegliere un server già esistente come server secondario. Dopo aver immesso i valori seguenti per il nuovo server secondario, selezionare **Seleziona**. 
        - **Nome server**: Digitare un nome univoco per il server secondario, ad esempio `mysqlsecondary`. 
        - **Account di accesso amministratore server**: Tipo`azureuser`
        - **Password**: Digitare una password complessa che soddisfi i corrispondenti requisiti.
        - **Località**: scegliere una località dall'elenco a discesa, ad esempio `East US`. Il percorso non può essere uguale a quello del server primario.

       > [!NOTE]
       > L'account di accesso del server e le impostazioni del firewall devono corrispondere a quelle del server primario. 
    
       ![Creazione di un server secondario per il gruppo di failover](media/sql-database-elastic-pool-failover-group-tutorial/create-secondary-failover-server.png)

1. Selezionare i **database all'interno del gruppo** e quindi selezionare il pool elastico creato nella sezione 2. Verrà visualizzato un avviso che richiede di creare un pool elastico nel server secondario. Selezionare l'avviso e quindi fare clic su **OK** per creare il pool elastico nel server secondario. 
        
    ![Aggiungere un pool elastico al gruppo di failover](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Selezionare **Seleziona** per applicare le impostazioni del pool elastico al gruppo di failover e quindi selezionare **Crea** per creare il gruppo di failover. L'aggiunta del pool elastico al gruppo di failover avvierà automaticamente il processo di replica geografica. 


## <a name="4---test-failover"></a>4-failover di test 
In questo passaggio si verificherà un errore nel gruppo di failover nel server secondario e quindi si eseguirà il failback utilizzando il portale di Azure. 

1. Selezionare **Azure SQL** nel menu a sinistra del [portale di Azure](https://portal.azure.com). Se **SQL di Azure** non è presente nell'elenco, selezionare **tutti i servizi**, quindi digitare Azure SQL nella casella di ricerca. Opzionale Selezionare la stella accanto a **SQL di Azure** per favorirla e aggiungerla come elemento nel menu di spostamento a sinistra. 
1. Selezionare il pool elastico creato nella sezione precedente, ad esempio `myElasticPool`. 
1. Selezionare il nome del server in **nome server** per aprire le impostazioni per il server.

    ![Apri server per pool elastico](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Selezionare **gruppi di failover** nel riquadro **Impostazioni** , quindi scegliere il gruppo di failover creato nella sezione 2. 
  
   ![Selezionare il gruppo di failover dal portale](media/sql-database-elastic-pool-failover-group-tutorial/select-failover-group.png)

1. Esaminare il server primario e il server secondario. 
1. Selezionare **failover** dal riquadro attività per eseguire il failover del gruppo di failover che contiene il pool elastico. 
1. Selezionare **Sì** nell'avviso che informa che le sessioni TDS verranno disconnesse. 

   ![Eseguire il failover del gruppo di failover contenente il database SQL](media/sql-database-elastic-pool-failover-group-tutorial/failover-sql-db.png)

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
