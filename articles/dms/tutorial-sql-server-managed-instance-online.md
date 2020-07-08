---
title: 'Esercitazione: eseguire la migrazione di SQL Server online a SQL Istanza gestita'
titleSuffix: Azure Database Migration Service
description: Informazioni su come eseguire una migrazione in linea da SQL Server a un Istanza gestita SQL di Azure tramite il servizio migrazione del database di Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/10/2020
ms.openlocfilehash: 3d462fa0fa2afe5937c60985938c8268991dfa41
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86084223"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-online-using-dms"></a>Esercitazione: eseguire la migrazione di SQL Server a un Istanza gestita SQL di Azure in linea con DMS

È possibile usare il servizio migrazione del database di Azure per eseguire la migrazione dei database da un'istanza di SQL Server a un [istanza gestita SQL di Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) con tempi di inattività minimi. Per ulteriori metodi che possono richiedere un intervento manuale, vedere l'articolo [SQL Server migrazione dell'istanza ad Azure SQL istanza gestita](../azure-sql/managed-instance/migrate-to-instance-from-sql-server.md).

In questa esercitazione si esegue la migrazione del database **AdventureWorks2012** da un'istanza locale di SQL Server a un istanza gestita SQL con un tempo di inattività minimo usando il servizio migrazione del database di Azure.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
>
> * Creare un'istanza del Servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione e avviare la migrazione online usando Servizio Migrazione del database di Azure.
> * Monitorare la migrazione.
> * Eseguire il cutover della migrazione quando si è pronti.

> [!IMPORTANT]
> Per le migrazioni online da SQL Server a SQL Istanza gestita tramite il servizio migrazione del database di Azure, è necessario fornire il backup completo del database e i backup del log successivi nella condivisione di rete SMB che il servizio può utilizzare per eseguire la migrazione dei database. Servizio Migrazione del database di Azure non avvia alcun backup, ma usa i backup esistenti, che potrebbero già fare parte del piano di ripristino di emergenza, per la migrazione.
> Assicurarsi di eseguire i [backup usando l'opzione WITH CHECKSUM](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017). Assicurarsi anche di non accodare più backup (ad esempio, backup completi e della parte finale del log) in un singolo supporto di backup, ma eseguire ogni backup in un file di backup separato. È infine possibile usare backup compressi per ridurre la probabilità di potenziali problemi associati alla migrazione di backup di grandi dimensioni.

> [!NOTE]
> L'uso del Servizio Migrazione del database di Azure per eseguire una migrazione online richiede la creazione di un'istanza basata sul piano tariffario Premium.

> [!IMPORTANT]
> Per un'esperienza di migrazione ottimale, Microsoft consiglia di creare un'istanza del Servizio Migrazione del database di Azure nella stessa area di Azure del database di destinazione. Lo spostamento dei dati tra regioni o aree geografiche può rallentare il processo di migrazione e causare errori.

> [!IMPORTANT]
> Ridurre il più possibile la durata del processo di migrazione online per ridurre al minimo il rischio di interruzione causata dalla riconfigurazione dell'istanza o dalla manutenzione pianificata. In caso di un evento di questo tipo, il processo di migrazione inizierà dall'inizio. In caso di manutenzione pianificata, è previsto un periodo di tolleranza di 36 ore prima che il processo di migrazione venga riavviato.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Questo articolo descrive una migrazione in linea da SQL Server a un Istanza gestita SQL. Per una migrazione offline, vedere [eseguire la migrazione di SQL Server a SQL istanza gestita offline con DMS](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

* Creare una Rete virtuale di Microsoft Azure per il servizio migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager, che fornisce la connettività da sito a sito ai server di origine locali usando [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Informazioni sulle topologie di rete per le migrazioni di SQL istanza gestita usando il servizio migrazione del database di Azure](https://aka.ms/dmsnetworkformi). Per ulteriori informazioni sulla creazione di una rete virtuale, vedere la [documentazione relativa alla rete virtuale](https://docs.microsoft.com/azure/virtual-network/)e, in particolare, gli articoli introduttivi con informazioni dettagliate.

    > [!NOTE]
    > Durante la configurazione della rete virtuale, se si usa ExpressRoute con il peering di rete a Microsoft, aggiungere i seguenti [endpoint](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) di servizio alla subnet in cui verrà eseguito il provisioning del servizio:
    >
    > * Endpoint del database di destinazione (ad esempio endpoint SQL, endpoint Cosmos DB e così via)
    > * Endpoint di archiviazione
    > * Endpoint bus di servizio
    >
    > Questa configurazione è necessaria perché il Servizio Migrazione del database di Azure non ha connettività Internet.
    >
    >Se la connettività da sito a sito tra la rete locale e Azure non è disponibile oppure se la larghezza di banda della connettività da sito a sito è limitata, provare a usare il Servizio Migrazione del database di Azure in modalità ibrida (anteprima). Con la modalità ibrida si usa un ruolo di lavoro della migrazione locale unitamente a un'istanza del Servizio Migrazione del database di Azure in esecuzione nel cloud. Per creare un'istanza del Servizio Migrazione del database di Azure in modalità ibrida, vedere l'articolo [Creare un'istanza del Servizio Migrazione del database di Azure in modalità ibrida con il portale di Azure](https://aka.ms/dms-hybrid-create).

    > [!IMPORTANT]
    > Per quanto riguarda l'account di archiviazione usato come parte della migrazione, è necessario effettuare una delle operazioni seguenti:
    > * Scegliere di consentire a tutta la rete di accedere all'account di archiviazione.
    > * Attivare la [delega della subnet](https://docs.microsoft.com/azure/virtual-network/manage-subnet-delegation) nella subnet mi e aggiornare le regole del firewall dell'account di archiviazione per consentire la subnet.

* Verificare che le regole del gruppo di sicurezza di rete della rete virtuale non blocchino le porte di comunicazione in ingresso seguenti al servizio migrazione del database di Azure: 443, 53, 9354, 445, 12000. Per informazioni più dettagliate sul filtro del traffico NSG per la rete virtuale, vedere l'articolo [filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Configurare [Windows Firewall per l'accesso al motore del database di origine](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Aprire Windows Firewall per consentire a Servizio Migrazione del database di Azure di accedere all'istanza di origine di SQL Server, che per impostazione predefinita è tramite la porta TCM 1433.
* Se si eseguono più istanze denominate di SQL Server tramite porte dinamiche, è consigliabile abilitare il servizio SQL Browser e consentire l'accesso alla porta UDP 1434 attraverso i firewall, in modo che Servizio Migrazione del database di Azure possa connettersi a un'istanza denominata nel server di origine.
* Se si usa un'appliance firewall all'ingresso dei database di origine, può essere necessario aggiungere regole del firewall per consentire a Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione, oltre che ai file, attraverso la porta SMB 445.
* Per creare un Istanza gestita SQL, seguire le istruzioni riportate nell'articolo [creare una istanza gestita SQL nella portale di Azure](https://aka.ms/sqldbmi).
* Verificare che gli account di accesso utilizzati per connettere la SQL Server di origine e il Istanza gestita SQL di destinazione siano membri del ruolo del server sysadmin.
* Specificare una condivisione di rete SMB contenente tutti i file del backup completo del database e i successivi file di backup del log delle transazioni, che possono essere usati da Servizio Migrazione del database di Azure per la migrazione del database.
* Verificare che l'account del servizio che esegue l'istanza di SQL Server abbia privilegi di scrittura sulla condivisione di rete creata e che l'account computer del server di origine abbia accesso in lettura/scrittura alla stessa condivisione.
* Prendere nota di un utente (e una password) di Windows con privilegi di controllo completo sulla condivisione di rete creata in precedenza. Il servizio migrazione del database di Azure rappresenta le credenziali dell'utente per caricare i file di backup nel contenitore di archiviazione di Azure per l'operazione di ripristino.
* Creare un Azure Active Directory ID applicazione che genera la chiave ID applicazione che il servizio migrazione del database di Azure può usare per connettersi al database di Azure di destinazione Istanza gestita e al contenitore di archiviazione di Azure. Per altre informazioni, vedere l'articolo [Usare il portale per creare un'applicazione Azure Active Directory e un'entità servizio che possano accedere alle risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

  > [!NOTE]
  > Servizio Migrazione del database di Azure richiede l'autorizzazione di collaboratore nella sottoscrizione per l'ID applicazione specificato. In alternativa, è possibile creare ruoli personalizzati che concedono le autorizzazioni specifiche richieste da Servizio Migrazione del database di Azure. Per istruzioni dettagliate sull'uso dei ruoli personalizzati, vedere l'articolo [ruoli personalizzati per SQL Server a SQL istanza gestita migrazioni online](https://docs.microsoft.com/azure/dms/resource-custom-roles-sql-db-managed-instance).

* Creare o annotare un account di archiviazione di Azure con **livello di prestazioni Standard** che possa essere usato da Servizio Migrazione del database per caricare i file di backup di database e per la migrazione di database.  Assicurarsi di creare l'account di archiviazione di Azure nella stessa area in cui viene creata l'istanza di Servizio Migrazione del database di Azure.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrare il provider di risorse Microsoft.DataMigration

1. Accedere al portale di Azure, selezionare **Tutti i servizi**, quindi selezionare **Sottoscrizioni**.

    ![Mostra le sottoscrizioni del portale](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Selezionare la sottoscrizione in cui si vuole creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.

    ![Visualizzare i provider di risorse](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Ricercare la migrazione e quindi a destra del **Microsoft.DataMigration** selezionare **Registro**.

    ![Registrare il provider di risorse](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Creare un'istanza del Servizio Migrazione del database di Azure

1. Nella portale di Azure selezionare + **Crea una risorsa**, cercare **servizio migrazione del database di Azure**e quindi selezionare **servizio migrazione del database di Azure** dall'elenco a discesa.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. Nella schermata **Servizio Migrazione del database di Azure** selezionare **Crea**.

    ![Creare l'istanza del Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. Nella schermata **Crea servizio Migrazione** specificare un nome per il servizio, la sottoscrizione e un gruppo di risorse nuovo o esistente.

4. Selezionare la posizione in cui creare l'istanza del Servizio Migrazione del database di Azure.

5. Selezionare una rete virtuale esistente o crearne una.

    La rete virtuale fornisce al servizio migrazione del database di Azure l'accesso alla SQL Server di origine e a SQL Istanza gestita di destinazione.

    Per altre informazioni su come creare una rete virtuale in portale di Azure, vedere l'articolo [creare una rete virtuale usando il portale di Azure](https://aka.ms/DMSVnet).

    Per altri dettagli, vedere l'articolo [topologie di rete per le migrazioni di SQL istanza gestita con il servizio migrazione del database di Azure](https://aka.ms/dmsnetworkformi).

6. Selezionare uno SKU del piano tariffario Premium.

    > [!NOTE]
    > Le migrazioni online sono supportate solo quando viene usato il piano tariffario Premium.

    Per ulteriori informazioni sui costi e sui piani tariffari, vedere la [pagina](https://aka.ms/dms-pricing)relativa ai prezzi.

    ![Creare il Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Selezionare **Crea** per creare il servizio.

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato un'istanza del servizio, individuarlo nel portale di Azure, aprirlo e creare un nuovo progetto di migrazione.

1. Nel portale di Azure selezionare **Tutti i servizi**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure**.

    ![Individuare tutte le istanze di Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. Nella schermata **servizio migrazione del database di Azure** cercare il nome dell'istanza creata e quindi selezionare l'istanza.

3. Selezionare + **nuovo progetto di migrazione**.

4. Nella schermata **nuovo progetto di migrazione** specificare un nome per il progetto, nella casella di **testo tipo server di origine** Selezionare **SQL Server**, nella casella di testo tipo di **server di destinazione** selezionare **istanza gestita SQL di Azure**e quindi selezionare **migrazione dati online**per **scegliere il tipo di attività**.

   ![Creare un progetto di Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Selezionare **Crea ed esegui attività** per creare il progetto.

## <a name="specify-source-details"></a>Specificare le informazioni di origine

1. Nella schermata **Dettagli origine della migrazione** specificare i dettagli di connessione per l'istanza di SQL Server di origine.

2. Se nel server non è installato un certificato attendibile, selezionare la casella di controllo **Considera attendibile certificato server**.

    Quando non è installato un certificato attendibile, SQL Server genera un certificato autofirmato all'avvio dell'istanza. Questo certificato viene usato per crittografare le credenziali per le connessioni client.

    > [!CAUTION]
    > Le connessioni TLS crittografate con un certificato autofirmato non forniscono una sicurezza avanzata. Sono infatti suscettibili ad attacchi man-in-the-middle. Non fare affidamento su TLS utilizzando certificati autofirmati in un ambiente di produzione o su server connessi a Internet.

   ![Dettagli origine](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Selezionare **Salva**.

4. Nella schermata **Seleziona database di origine** selezionare il database **AdventureWorks2012** per la migrazione.

   ![Selezionare i database di origine](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Se si usa SQL Server Integration Services (SSIS), DMS non supporta attualmente la migrazione del database del catalogo per i progetti/pacchetti SSIS (SSISDB) da SQL Server a SQL Istanza gestita. Tuttavia, è possibile eseguire il provisioning di SSIS in Azure Data Factory (ADF) e ridistribuire i progetti/pacchetti SSIS nel database SSISDB di destinazione ospitato da SQL Istanza gestita. Per altre informazioni sulla migrazione dei pacchetti SSIS, vedere l'articolo [Eseguire la migrazione di pacchetti SQL Server Integration Services in Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Selezionare **Salva**.

## <a name="specify-target-details"></a>Specificare i dettagli della destinazione

1. Nella schermata **Dettagli destinazione migrazione** specificare l'ID e la **chiave** dell' **applicazione** che l'istanza DMS può usare per connettersi all'istanza di destinazione di SQL istanza gestita e l'account di archiviazione di Azure.

    Per altre informazioni, vedere l'articolo [Usare il portale per creare un'applicazione Azure Active Directory e un'entità servizio che possano accedere alle risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

2. Selezionare la **sottoscrizione** contenente l'istanza di destinazione di SQL istanza gestita, quindi selezionare l'istanza di destinazione.

    Se non è già stato effettuato il provisioning della Istanza gestita SQL, selezionare il [collegamento](https://aka.ms/SQLDBMI) per eseguire il provisioning dell'istanza. Quando il Istanza gestita SQL è pronto, tornare a questo progetto specifico per eseguire la migrazione.

3. Specificare l'utente e la **password** **SQL** per la connessione al istanza gestita SQL.

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
    |**Condivisione del percorso di rete SMB** | Condivisione di rete SMB locale o condivisione file di Azure contenente i file di backup completo del database e i file di backup del log delle transazioni, che possono essere usati da Servizio Migrazione del database di Azure per la migrazione. L'account del servizio che esegue l'istanza di SQL Server di origine deve avere privilegi di lettura/scrittura per questa condivisione di rete. Specificare l'FQDN o l'indirizzo IP del server nella condivisione di rete, ad esempio '\\\nomeserver.nomedominio.com\cartellabackup' o '\\\indirizzoIP\cartellabackup'. Per migliorare le prestazioni, è consigliabile usare una cartella separata per ogni database di cui eseguire la migrazione. È possibile specificare il percorso di condivisione file a livello di database usando l'opzione **Impostazioni avanzate** . |
    |**Nome utente** | Verificare che l'utente di Windows abbia i privilegi di controllo completo sulla condivisione di rete fornita in precedenza. Il servizio migrazione del database di Azure rappresenterà le credenziali dell'utente per caricare i file di backup nel contenitore di archiviazione di Azure per l'operazione di ripristino. Se si usa la condivisione file di Azure, usare il nome dell'account di archiviazione con il prefisso AZURE\ come nome utente. |
    |**Password** | Password per l'utente. Se si usa la condivisione file di Azure, usare una chiave dell'account di archiviazione come password. |
    |**Sottoscrizione dell'account di archiviazione di Azure** | Selezionare la sottoscrizione contenente l'account di archiviazione di Azure. |
    |**Account di archiviazione di Azure** | Selezionare l'account di archiviazione di Azure in cui Servizio Migrazione del database potrà caricare i file di backup dalla condivisione di rete SMB e che potrà essere usato per la migrazione del database.  Per prestazioni di caricamento file ottimali, è consigliabile selezionare un account di archiviazione nella stessa area dell'istanza di Servizio Migrazione del database. |

    ![Configurare le impostazioni di migrazione](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

    > [!NOTE]
    > Se il servizio migrazione del database di Azure Visualizza l'errore "errore di sistema 53" o "errore di sistema 57", la causa potrebbe essere l'impossibilità del servizio migrazione del database di Azure di accedere alla condivisione file di Azure. Se si verifica uno di questi errori, concedere l'accesso all'account di archiviazione dalla rete virtuale seguendo le istruzioni riportate [qui](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network).

    > [!IMPORTANT]
    > Se è abilitata la funzionalità di controllo di loopback e la SQL Server di origine e la condivisione file si trovano nello stesso computer, l'origine non sarà in grado di accedere ai file Hare utilizzando FQDN. Per risolvere questo problema, disabilitare la funzionalità di verifica loopback usando le istruzioni riportate [qui](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd).

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

Al termine del ripristino del backup completo del database nell'istanza di destinazione di SQL Istanza gestita, il database è disponibile per l'esecuzione di un cutover di migrazione.

1. Quando si è pronti a completare la migrazione online del database, selezionare **Avvia cutover**.

2. Arrestare tutto il traffico in ingresso verso i database di origine.

3. Usare il [backup della parte finale del log], rendere disponibile il file di backup nella condivisione di rete SMB e quindi attendere il ripristino di questo backup del log delle transazioni finale.

    A questo punto, il valore visualizzato per **Modifiche in sospeso** sarà impostato su 0.

4. Selezionare **Conferma** e quindi **Applica**.

    ![Preparazione per il completamento del cutover](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. Quando lo stato della migrazione del database viene visualizzato **completato**, connettere le applicazioni alla nuova istanza di destinazione di SQL istanza gestita.

    ![Cutover completato](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Passaggi successivi

* Per un'esercitazione che illustra come eseguire la migrazione di un database a SQL Istanza gestita usando il comando T-SQL RESTOre, vedere [ripristinare un backup in sql istanza gestita usando il comando Restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
* Per informazioni su SQL Istanza gestita, vedere [che cos'è sql istanza gestita](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).
* Per informazioni sulla connessione di app a SQL Istanza gestita, vedere [connettere le applicazioni](../azure-sql/managed-instance/connect-application-instance.md).
