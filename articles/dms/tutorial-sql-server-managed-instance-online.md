---
title: "Esercitazione: Usare Servizio Migrazione del database di Azure per eseguire una migrazione online di SQL Server a un'istanza gestita di database SQL di Azure | Microsoft Docs"
description: Informazioni su come eseguire una migrazione online da SQL Server in locale a un'istanza gestita di database SQL di Azure con Servizio Migrazione del database di Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: d9d57df3ec8e859a1f3257cb54e423d0006286b1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58880186"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-online-using-dms"></a>Esercitazione: Eseguire la migrazione online di SQL Server a un'istanza gestita di database SQL di Azure con Servizio Migrazione del database

È possibile usare Servizio Migrazione del database di Azure per eseguire la migrazione dei database da un'istanza locale di SQL Server a un'[istanza gestita di database SQL di Azure](../sql-database/sql-database-managed-instance.md) con tempi di inattività minimi. Per altri metodi che potrebbero richiedere intervento manuale, vedere l'articolo [Migrazione di un'istanza di SQL Server a un'istanza gestita di database SQL di Azure](../sql-database/sql-database-managed-instance-migrate.md).

In questa esercitazione si esegue la migrazione del database **AdventureWorks2012** da un'istanza locale di SQL Server a un'istanza gestita di database SQL di Azure con tempi di inattività minimi, usando Servizio Migrazione del database di Azure.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> - Creare un'istanza del Servizio Migrazione del database di Azure.
> - Creare un progetto di migrazione e avviare la migrazione online usando Servizio Migrazione del database di Azure.
> - Monitorare la migrazione.
> - Eseguire il cutover della migrazione quando si è pronti.

> [!NOTE]
> L'uso del Servizio Migrazione del database di Azure per eseguire una migrazione online richiede la creazione di un'istanza basata sul piano tariffario Premium.
> [!IMPORTANT]
> Per un'esperienza di migrazione ottimale, Microsoft consiglia di creare un'istanza del Servizio Migrazione del database di Azure nella stessa area di Azure del database di destinazione. Lo spostamento dei dati tra regioni o aree geografiche può rallentare il processo di migrazione e causare errori.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Questo articolo descrive una migrazione online da SQL Server a un'istanza gestita di database SQL di Azure. Per una migrazione offline, vedere [Eseguire la migrazione offline di SQL Server a un'istanza gestita di database SQL di Azure con Servizio Migrazione del database di Azure](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

- Creare una rete virtuale di Azure per Servizio Migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager, che offre la connettività da sito a sito per i server di origine locali con [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Acquisire familiarità con le topologie di rete per le migrazioni a istanze gestite di database SQL di Azure con Servizio Migrazione del database di Azure](https://aka.ms/dmsnetworkformi).

    > [!NOTE]
    > Durante la configurazione della rete virtuale, se si usa ExpressRoute con peering di rete per Microsoft, aggiungere gli [endpoint](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) di servizio seguenti alla subnet in cui verrà effettuato il provisioning del servizio:
    > - Endpoint del database di destinazione (ad esempio endpoint SQL, endpoint Cosmos DB e così via)
    > - Endpoint di archiviazione
    > - Endpoint bus di servizio
    >
    > Questa configurazione è necessaria perché Servizio Migrazione del database di Azure non dispone di connettività Internet.

- Verificare che le regole del gruppo di sicurezza di rete per la rete virtuale non blocchino le porte di comunicazione in ingresso nel Servizio Migrazione del database di Azure: 443, 53, 9354, 445, 12000. Per informazioni dettagliate sui filtri del traffico dei gruppi di sicurezza di rete relativi alla rete virtuale di Azure, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Configurare [Windows Firewall per l'accesso al motore del database di origine](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Aprire Windows Firewall per consentire al Servizio Migrazione del database di Azure di accedere a SQL Server di origine, che per impostazione predefinita è la porta TCP 1433.
- Se si eseguono più istanze di SQL Server denominate usando le porte dinamiche, è consigliabile abilitare il Servizio browser SQL e consentire l'accesso alla porta UDP 1434 attraverso i firewall, in modo che il Servizio Migrazione del database di Azure possa connettersi a un'istanza denominata nel server di origine.
- Se si usa un'appliance firewall all'ingresso dei database di origine, può essere necessario aggiungere regole del firewall per consentire al servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione e ai file attraverso la porta SMB 445.
- Creare un'istanza gestita di database SQL di Azure seguendo le istruzioni dettagliate riportate nell'articolo [Creare un'istanza gestita di database SQL di Azure nel portale di Azure](https://aka.ms/sqldbmi).
- Verificare che gli account di accesso usati per la connessione all'istanza di SQL Server di origine e all'istanza gestita di destinazione siano membri del ruolo del server sysadmin.
- Specificare una condivisione di rete SMB contenente tutti i file di backup completo del database e i successivi file di backup del log delle transazioni che possono essere usati da Servizio Migrazione del database di Azure per la migrazione del database.
- Verificare che l'account del servizio che esegue l'istanza di SQL Server abbia privilegi di scrittura sulla condivisione di rete creata e che l'account computer del server di origine abbia accesso in lettura/scrittura alla stessa condivisione.
- Prendere nota di un utente (e una password) di Windows con privilegi di controllo completo sulla condivisione di rete creata in precedenza. Il Servizio Migrazione del database di Azure rappresenta le credenziali dell'utente necessarie per caricare i file di backup nel contenitore di archiviazione di Azure per l'operazione di ripristino.
- Creare un ID applicazione di Azure Active Directory per generare la chiave ID applicazione che potrà essere usata da Servizio Migrazione del database per connettersi all'istanza gestita di database di Azure di destinazione e al contenitore di archiviazione di Azure. Per altre informazioni, vedere l'articolo [Usare il portale per creare un'applicazione Azure Active Directory e un'entità servizio che possano accedere alle risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).
- Creare o annotare un account di archiviazione di Azure con **livello di prestazioni Standard** che possa essere usato da Servizio Migrazione del database per caricare i file di backup di database e per la migrazione di database.  Assicurarsi di creare l'account di archiviazione di Azure nella stessa area in cui viene creata l'istanza di Servizio Migrazione del database.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrare il provider di risorse Microsoft.DataMigration

1. Accedere al portale di Azure, selezionare **Tutti i servizi**, quindi selezionare **Sottoscrizioni**.

    ![Mostra le sottoscrizioni del portale](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)        
2. Selezionare la sottoscrizione in cui si desidera creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.

    ![Visualizzare i provider di risorse](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Ricercare la migrazione e quindi a destra del **Microsoft.DataMigration** selezionare **Registro**.

    ![Registrare il provider di risorse](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Creare un'istanza del Servizio Migrazione del database di Azure

1. Nel portale di Azure selezionare **+ Crea una risorsa**, cercare **Servizio Migrazione del database di Azure** e quindi selezionare **Servizio Migrazione del database di Azure** dall'elenco a discesa.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. Nella schermata **Servizio Migrazione del database di Azure** selezionare **Crea**.

    ![Creare l'istanza del Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. Nella schermata **Crea servizio Migrazione** specificare un nome per il servizio, la sottoscrizione e un gruppo di risorse nuovo o esistente.

4. Selezionare la posizione in cui creare l'istanza del Servizio Migrazione del database di Azure.

5. Selezionare una rete virtuale (VNET) esistente o crearne una.

    La rete virtuale consente a Servizio Migrazione del database di Azure di accedere all'istanza di SQL Server di origine e all'istanza gestita di database SQL di Azure di destinazione.

    Per altre informazioni su come creare una VNET nel portale di Azure, vedere l'articolo [Creare una rete virtuale usando il portale di Azure](https://aka.ms/DMSVnet).

    Per altri dettagli, vedere l'articolo relativo alle [topologie di rete per le migrazioni a istanze gestite di database SQL di Azure con Servizio Migrazione del database di Azure](https://aka.ms/dmsnetworkformi).

6. Selezionare uno SKU del piano tariffario Premium.

    > [!NOTE]
    > Le migrazioni online sono supportate solo quando viene usato il piano tariffario Premium. 

    Per altre informazioni sui costi e i piani tariffari, vedere la [pagina relativa ai prezzi](https://aka.ms/dms-pricing).

    ![Creare il Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Selezionare **Crea** per creare il servizio.

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato un'istanza del servizio, individuarlo nel portale di Azure, aprirlo e creare un nuovo progetto di migrazione.

1. Nel portale di Azure selezionare **Tutti i servizi**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure**.

    ![Individuare tutte le istanze del Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. Nella schermata **Servizio Migrazione del database di Azure** cercare il nome dell'istanza creata e quindi selezionarla.

3. Selezionare **+ Nuovo progetto di migrazione**.

4. Nella schermata **Nuovo progetto di migrazione** specificare un nome per il progetto e quindi selezionare **SQL Server** nella casella di testo **Tipo del server di origine**, **Istanza gestita di database SQL di Azure** nella casella di testo **Tipo del server di destinazione** e **Migrazione dei dati online** per **Scegli il tipo di attività**.

   ![Creare il progetto del Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Selezionare **Crea ed esegui attività** per creare il progetto.

## <a name="specify-source-details"></a>Specificare le informazioni di origine

1. Nella schermata **Dettagli origine della migrazione** specificare i dettagli di connessione per l'istanza di SQL Server di origine.

2. Se nel server non è installato un certificato attendibile, selezionare la casella di controllo **Considera attendibile certificato server**.

    Quando non è installato un certificato attendibile, SQL Server genera un certificato autofirmato all'avvio dell'istanza. Questo certificato viene usato per crittografare le credenziali per le connessioni client.

    > [!CAUTION]
    > Le connessioni SSL crittografate con un certificato autofirmato non forniscono una sicurezza avanzata. Sono infatti suscettibili ad attacchi man-in-the-middle. È consigliabile non usare SSL con i certificati autofirmati in un ambiente di produzione o in server connessi a Internet.

   ![Dettagli origine](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Selezionare **Salva**.

4. Nella schermata **Seleziona database di origine** selezionare il database **AdventureWorks2012** per la migrazione.

   ![Selezionare i database di origine](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Se si usa SQL Server Integration Services (SSIS), Servizio Migrazione del database non supporta attualmente la migrazione del database di catalogo per i progetti/pacchetti SSIS (SSISDB) da SQL Server a un'istanza gestita di database SQL di Azure. È tuttavia possibile effettuare il provisioning di SSIS in Azure Data Factory (ADF) e ridistribuire i progetti/pacchetti SSIS nel database SSISDB di destinazione ospitato dall'istanza gestita di database SQL di Azure. Per altre informazioni sulla migrazione dei pacchetti SSIS, vedere l'articolo [Eseguire la migrazione di pacchetti SQL Server Integration Services in Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Selezionare **Salva**.

## <a name="specify-target-details"></a>Specificare i dettagli della destinazione

1. Nella schermata **Dettagli destinazione della migrazione** specificare i valori di **ID applicazione** e **Chiave** che potranno essere usati dall'istanza di Servizio Migrazione del database per connettersi all'istanza gestita di database SQL di Azure di destinazione e all'account di archiviazione di Azure.

    Per altre informazioni, vedere l'articolo [Usare il portale per creare un'applicazione Azure Active Directory e un'entità servizio che possano accedere alle risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

2. Selezionare la **sottoscrizione** contenente l'istanza gestita di database SQL di Azure di destinazione e quindi l'istanza di destinazione.

    Se non si è ancora effettuato il provisioning dell'istanza gestita di database SQL di Azure, selezionare il [collegamento](https://aka.ms/SQLDBMI) a tale scopo. Quando l'istanza gestita di database SQL di Azure è pronta, tornare a questo progetto specifico per eseguire la migrazione.

3. Specificare i valori di **Utente SQL** e **Password** per la connessione all'istanza gestita di database SQL di Azure.

    ![Selezionare la destinazione](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. Selezionare **Salva**.

## <a name="select-source-databases"></a>Selezionare i database di origine

1. Nella schermata **Seleziona database di origine** selezionare il database di origine di cui eseguire la migrazione.

    ![Selezionare i database di origine](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

2. Selezionare **Salva**.

## <a name="configure-migration-settings"></a>Configurare le impostazioni di migrazione

1. Nella schermata **Configura le impostazioni di migrazione** specificare i dettagli seguenti:

    | | |
    |--------|---------|
    |**Condivisione del percorso di rete SMB** | Condivisione di rete SMB contenente i file di backup completo del database e i file di backup del log delle transazioni che possono essere usati da Servizio Migrazione del database di Azure per la migrazione. L'account del servizio che esegue l'istanza di SQL Server di origine deve avere privilegi di lettura/scrittura per questa condivisione di rete. Specificare l'FQDN o l'indirizzo IP del server nella condivisione di rete, ad esempio '\\\nomeserver.nomedominio.com\cartellabackup' o '\\\indirizzoIP\cartellabackup'.|
    |**Nome utente** | Verificare che l'utente di Windows abbia i privilegi di controllo completo sulla condivisione di rete fornita in precedenza. Il Servizio Migrazione del database di Azure rappresenterà le credenziali utente necessarie per caricare i file di backup nel contenitore di archiviazione di Azure per l'operazione di ripristino. |
    |**Password** | Password per l'utente. |
    |**Sottoscrizione dell'account di archiviazione di Azure** | Selezionare la sottoscrizione contenente l'account di archiviazione di Azure. |
    |**Account di archiviazione di Azure** | Selezionare l'account di archiviazione di Azure in cui Servizio Migrazione del database potrà caricare i file di backup dalla condivisione di rete SMB e che potrà essere usato per la migrazione del database.  Per prestazioni di caricamento file ottimali, è consigliabile selezionare un account di archiviazione nella stessa area dell'istanza di Servizio Migrazione del database. |

    ![Configurare le impostazioni di migrazione](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

2. Selezionare **Salva**.

## <a name="review-the-migration-summary"></a>Esaminare il riepilogo della migrazione

1. Nella schermata **Riepilogo migrazione** specificare un nome per l'attività di migrazione nella casella di testo **Nome attività**.

2. Esaminare e verificare i dettagli associati al progetto di migrazione.

    ![Riepilogo del progetto di migrazione](media/tutorial-sql-server-to-managed-instance-online/dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>Eseguire e monitorare la migrazione

1. Selezionare **Esegui migrazione**.

2. Nella schermata dell'attività di migrazione selezionare **Aggiorna** per aggiornare la visualizzazione.

   ![Attività di migrazione in corso](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration2.png)

    È possibile espandere ulteriormente le categorie di database e account di accesso per monitorare lo stato di migrazione dei rispettivi oggetti server.

   ![Attività di migrazione in corso](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Eseguire il cutover della migrazione

Al termine del ripristino del backup completo del database nell'istanza gestita di database SQL di Azure di destinazione, il database è disponibile per l'esecuzione di un cutover della migrazione.

1. Quando si è pronti a completare la migrazione online del database, selezionare **Avvia cutover**.

2. Arrestare tutto il traffico in ingresso verso i database di origine.

3. Usare il [backup della parte finale del log], rendere disponibile il file di backup nella condivisione di rete SMB e quindi attendere il ripristino di questo backup del log delle transazioni finale.

    A questo punto, il valore visualizzato per **Modifiche in sospeso** sarà impostato su 0.

4. Selezionare **Conferma** e quindi **Applica**.

    ![Preparazione per il completamento del cutover](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. Quando lo stato di migrazione del database è **Completato**, connettere le applicazioni alla nuova istanza gestita di database SQL di Azure di destinazione.

    ![Cutover completato](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Passaggi successivi

- Per un'esercitazione su come eseguire la migrazione di un database a un'istanza gestita usando il comando T-SQL RESTORE, vedere l'articolo relativo al [ripristino di un backup in un'istanza gestita con il comando restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Per altre informazioni in proposito, vedere l'articolo di [informazioni su Istanza gestita](../sql-database/sql-database-managed-instance.md).
- Per informazioni sulla connessione di app a un'istanza gestita, vedere [Connettere le applicazioni](../sql-database/sql-database-managed-instance-connect-app.md).
