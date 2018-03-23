---
title: Usare il servizio Migrazione del database di Azure per eseguire la migrazione di SQL Server all'istanza gestita di database SQL di Azure | Microsoft Docs
description: Informazioni su come eseguire la migrazione da SQL Server locale all'istanza gestita di database SQL di Azure con il servizio Migrazione del database di Azure.
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: d74a273061912ea2bdcc39301ce9a727b07ade41
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance"></a>Eseguire la migrazione di SQL Server all'istanza gestita di database SQL di Azure
È possibile usare Servizio Migrazione del database di Azure per migrare i database da un'istanza di SQL Server locale al database SQL di Azure. In questa esercitazione si eseguirà la migrazione del database **Adventureworks2012** da un'istanza locale di SQL Server a un database SQL di Azure tramite il servizio Migrazione del database di Azure.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Creare un'istanza del Servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione tramite il Servizio Migrazione del database di Azure.
> * Eseguire la migrazione.
> * Monitorare la migrazione.

## <a name="prerequisites"></a>prerequisiti
Per completare questa esercitazione, è necessario:

- Creare una rete virtuale per il Servizio Migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager, che fornisce la connettività da sito a sito ai server di origine locali usando [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [ VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Acquisire familiarità con le topologie di rete per la migrazione all'istanza gestita del database SQL di Azure tramite il servizio Migrazione del database di Azure](https://aka.ms/dmsnetworkformi).
- Verificare che le regole dei gruppi di sicurezza di rete relativi alla rete virtuale di Azure non blocchino le porte di comunicazione seguenti: 443, 53, 9354, 445, 12000. Per informazioni dettagliate sui filtri del traffico dei gruppi di sicurezza di rete relativi alla rete virtuale di Azure, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg).
- [Configurare Windows Firewall per l'accesso al motore del database di origine](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Aprire Windows Firewall per consentire al servizio Migrazione del database di Azure di accedere all'istanza di SQL Server di origine.
- Se si usa un'appliance firewall all'ingresso dei database di origine, può essere necessario aggiungere regole del firewall per consentire al servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione e ai file attraverso la porta SMB 445.
- Creare un'istanza gestita di database SQL di Azure seguendo le istruzioni riportate nell'articolo [Creare un'istanza gestita di database SQL di Azure nel portale di Azure](https://aka.ms/sqldbmi).
- Verificare che gli accessi usati per connettersi all'istanza di SQL Server di origine e all'istanza gestita di destinazione siano membri del ruolo sysadmin del server.
- Creare una condivisione di rete che può essere usata dal servizio Migrazione del database di Azure per il backup del database di origine.
- Verificare che l'account del servizio che esegue l'istanza di SQL Server di origine abbia privilegi di scrittura nella condivisione di rete creata.
- Prendere nota di un utente (e una password) di Windows con privilegi di controllo completo sulla condivisione di rete creata in precedenza. Il servizio Migrazione del database di Azure rappresenterà le credenziali utente per caricare i file di backup nel contenitore di archiviazione di Azure per l'operazione di ripristino.
- Creare un contenitore BLOB e recuperare il relativo URI di firma di accesso condiviso seguendo i passaggi descritti nell'articolo [Gestire le risorse dell'archivio BLOB di Azure con Storage Explorer (anteprima)](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Selezionare tutte le autorizzazioni (Read, Write, Delete, List – lettura, scrittura, eliminazione, elenco) nella finestra dei criteri durante la creazione dell'URI di firma di accesso condiviso. Ciò consentirà al servizio Migrazione del database di Azure di accedere al contenitore dell'account di archiviazione dell'utente per il caricamento dei file di backup, che verrà usato per la migrazione dei database all'istanza gestita di database SQL di Azure.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrare il provider di risorse Microsoft.DataMigration

1.  Accedere al portale di Azure, selezionare **Tutti i servizi**, quindi selezionare **Sottoscrizioni**.
![Visualizzare le sottoscrizioni del portale](media\tutorial-sql-server-to-managed-instance\portal-select-subscription.png)

1.  Selezionare la sottoscrizione in cui si desidera creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.
![Visualizzare i provider di risorse](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

1.  Ricercare la migrazione e quindi a destra del **Microsoft.DataMigration** selezionare **Registro**.
![Registrare un provider di risorse](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Creare un'istanza

1.  Nel portale di Azure selezionare **+ Crea una risorsa**, cercare **Servizio Migrazione del database di Azure** e quindi selezionare **Servizio Migrazione del database di Azure** dall'elenco a discesa.

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

1.  Nella schermata **Servizio Migrazione del database di Azure (anteprima)** selezionare **Crea**.

    ![Creare l'istanza del Servizio Migrazione del database di Azure](media\tutorial-sql-server-to-managed-instance\dms-create.png)

1.  Nella schermata **Servizio Migrazione del database** specificare un nome per il servizio, la sottoscrizione, il gruppo di risorse, una rete virtuale e il piano tariffario.

    Per altre informazioni sui costi e i piani tariffari, vedere la [pagina relativa ai prezzi](https://aka.ms/dms-pricing). *Il servizio Migrazione del database di Azure è attualmente in anteprima e non verrà addebitato alcun costo.*

    **Rete:** selezionare una rete virtuale esistente o crearne una nuova, per consentire al servizio Migrazione del database di Azure di accedere all'istanza di SQL Server di origine e all'istanza gestita di database SQL di Azure di destinazione. [Acquisire familiarità con le topologie di rete per la migrazione all'istanza gestita del database SQL di Azure tramite il servizio Migrazione del database di Azure](https://aka.ms/dmsnetworkformi).

    Per altre informazioni su come creare la rete virtuale nel portale di Azure, vedere [Creare una rete virtuale con più subnet usando il portale di Azure](https://aka.ms/DMSVnet).

    ![Creare il servizio Migrazione del database di Azure](media\tutorial-sql-server-to-managed-instance\dms-create-service.png)

1.  Selezionare **Crea** per creare il servizio.


## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato il servizio, individuarlo nel portale di Azure e aprirlo.

1.  Selezionare **+ New Migration Project** (+ Nuovo progetto di migrazione).

1.  Nella schermata **New migration project** (Nuovo progetto di migrazione) specificare il nome del progetto, nella casella di testo **Source server type** (Tipo di server di origine) selezionare **SQL Server** e quindi nella casella di testo **Target server type** (Tipo di server di destinazione) selezionare **Azure SQL Database Managed Instance** (Istanza gestita di database SQL di Azure).

    ![Creare il progetto del servizio Migrazione del database di Azure](media\tutorial-sql-server-to-managed-instance\dms-create-project.png)

1.  Selezionare **Crea** per creare il progetto.

## <a name="specify-source-details"></a>Specificare le informazioni di origine

1.  Nella schermata **Dettagli origine** specificare i dettagli di connessione per SQL Server di origine.

    ![Dettagli origine](media\tutorial-sql-server-to-managed-instance\dms-source-details.png)

1.  Selezionare **Salva** e quindi selezionare il database **Adventureworks2012** per la migrazione.

    ![Selezionare i database di origine](media\tutorial-sql-server-to-managed-instance\dms-source-database.png)

## <a name="specify-target-details"></a>Specificare i dettagli della destinazione

1.  Selezionare **Salva** e quindi, nella schermata **Dettagli destinazione**, specificare i dettagli di connessione per la destinazione, ovvero l'Istanza gestita di database SQL di Azure di cui è già stato eseguito il provisioning e in cui verrà eseguita la migrazione del database **Adventureworks2012**.

    ![Selezionare la destinazione](media\tutorial-sql-server-to-managed-instance\dms-target-details.png)

1.  Selezionare **Salva**.

1.  Nella schermata **Project summary** (Riepilogo progetto) esaminare e verificare i dettagli associati al progetto di migrazione.

## <a name="run-the-migration"></a>Eseguire la migrazione

1.  Selezionare il progetto salvato di recente, selezionare **+ Nuova attività** e quindi selezionare **Run migration** (Esegui migrazione).

    ![Creare una nuova attività](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity.png)

1.  Quando richiesto, immettere le credenziali dei server di origine e di destinazione e quindi selezionare **Salva**.

1.  Nella schermata **Map to target databases** (Esegui mapping nei database di destinazione) selezionare i database di origine di cui eseguire la migrazione.

    ![Selezionare i database di origine](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases.png)

1.  Selezionare **Salva** e quindi, nella schermata **Configure migration settings** (Configura impostazioni di migrazione), specificare i dettagli seguenti:

    | | |
    |--------|---------|
    |**Sever backup location** (Percorso backup server) | Condivisione di rete locale dove il Servizio Migrazione del database di Azure può salvare i backup del database di origine. L'account del servizio che esegue l'istanza di SQL Server di origine deve disporre dei privilegi di scrittura in questa condivisione di rete. |
    |**Nome utente** | Il nome utente di Windows che il Servizio Migrazione del database di Azure può rappresentare per caricare i file di backup nel contenitore di archiviazione di Azure per l'operazione di ripristino. |
    |**Password** | Password dell'utente indicato sopra. |
    |**Storage SAS URI** (URI di firma di accesso condiviso di archiviazione) | URI di firma di accesso condiviso che consente al servizio Migrazione del database di Azure di accedere al contenitore dell'account di archiviazione dell'utente per il caricamento dei file di backup e che verrà usato per la migrazione dei database all'istanza gestita di database SQL di Azure.  [Informazioni su come ottenere l'URI di firma di accesso condiviso per un contenitore BLOB](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    
    ![Configurare le impostazioni di migrazione](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings.png)

1.  Selezionare **Salva** e, nella schermata Migration summary (Riepilogo migrazione), specificare un nome per l'attività di migrazione nella casella di testo **Nome attività**.

    ![Riepilogo della migrazione](media\tutorial-sql-server-to-managed-instance\dms-migration-summary.png)


## <a name="monitor-the-migration"></a>Monitorare la migrazione

1.  Selezionare l'attività di migrazione per esaminare lo stato dell'attività.

1.  Al termine della migrazione verificare i database di destinazione nell'istanza gestita di database SQL di Azure di destinazione.

    ![Monitorare la migrazione](media\tutorial-sql-server-to-managed-instance\dms-monitor-migration.png)

