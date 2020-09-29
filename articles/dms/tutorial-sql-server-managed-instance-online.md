---
title: 'Esercitazione: Eseguire la migrazione online di SQL Server a Istanza gestita di SQL'
titleSuffix: Azure Database Migration Service
description: Informazioni su come eseguire una migrazione online da SQL Server a Istanza gestita di SQL di Azure con Servizio Migrazione del database di Azure.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 08/04/2020
ms.openlocfilehash: 745ea7dd8b3ee74c46d4c50a872dc4995d298142
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91291164"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-online-using-dms"></a>Esercitazione: Eseguire la migrazione online di SQL Server a Istanza gestita di SQL di Azure con Servizio Migrazione del database

È possibile usare Servizio Migrazione del database di Azure per eseguire la migrazione dei database da un'istanza di SQL Server a [Istanza gestita di SQL di Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) con tempi di inattività minimi. Per altri metodi che potrebbero richiedere intervento manuale, vedere l'articolo [Migrazione di un'istanza di SQL Server a Istanza gestita di SQL di Azure](../azure-sql/managed-instance/migrate-to-instance-from-sql-server.md).

In questa esercitazione si esegue la migrazione del database **Adventureworks2012** da un'istanza locale di SQL Server a Istanza gestita di SQL con tempi di inattività minimi, usando Servizio Migrazione del database di Azure.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
>
> * Creare un'istanza del servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione e avviare la migrazione online usando Servizio Migrazione del database di Azure.
> * Monitorare la migrazione.
> * Eseguire il cutover della migrazione quando si è pronti.

> [!IMPORTANT]
> Per le migrazioni online da SQL Server a Istanza gestita di SQL con Servizio Migrazione del database di Azure, è necessario fornire il backup del database completo e i successivi backup del log nella condivisione di rete SMB che il servizio può usare per eseguire la migrazione dei database. Servizio Migrazione del database di Azure non avvia alcun backup, ma usa i backup esistenti, che potrebbero già fare parte del piano di ripristino di emergenza, per la migrazione.
> Assicurarsi di eseguire i [backup usando l'opzione WITH CHECKSUM](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017&preserve-view=true). Assicurarsi anche di non accodare più backup (ad esempio, backup completi e della parte finale del log) in un singolo supporto di backup, ma eseguire ogni backup in un file di backup separato. È infine possibile usare backup compressi per ridurre la probabilità di potenziali problemi associati alla migrazione di backup di grandi dimensioni.

> [!NOTE]
> L'uso del Servizio Migrazione del database di Azure per eseguire una migrazione online richiede la creazione di un'istanza basata sul piano tariffario Premium.

> [!IMPORTANT]
> Per un'esperienza di migrazione ottimale, Microsoft consiglia di creare un'istanza del Servizio Migrazione del database di Azure nella stessa area di Azure del database di destinazione. Lo spostamento dei dati tra regioni o aree geografiche può rallentare il processo di migrazione e causare errori.

> [!IMPORTANT]
> Ridurre il più possibile la durata del processo di migrazione online per limitare i rischi di interruzioni causate dalla riconfigurazione dell'istanza o dalla manutenzione pianificata. Se si verifica un evento di questo tipo, il processo di migrazione ricomincerà dall'inizio. In caso di manutenzione pianificata, è previsto un periodo di tolleranza di 36 ore prima che il processo di migrazione venga riavviato.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Questo articolo descrive una migrazione online da SQL Server a Istanza gestita di SQL. Per una migrazione offline, vedere [Eseguire la migrazione offline di SQL Server a Istanza gestita di SQL con Servizio Migrazione del database di Azure](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

* Creare un'istanza di Rete virtuale di Microsoft Azure per Servizio Migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager, che offre la connettività da sito a sito per i server di origine locali con [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Acquisire familiarità con le topologie di rete per le migrazioni a Istanza gestita di SQL con Servizio Migrazione del database di Azure](https://aka.ms/dmsnetworkformi). Per altre informazioni sulla creazione di una rete virtuale, vedere la [documentazione sulla rete virtuale](https://docs.microsoft.com/azure/virtual-network/) e in particolare gli articoli di avvio rapido con istruzioni dettagliate.

    > [!NOTE]
    > Durante la configurazione della rete virtuale, se si usa ExpressRoute con il peering di rete per Microsoft, aggiungere gli [endpoint](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) servizio seguenti alla subnet in cui verrà effettuato il provisioning del servizio:
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
    > * Attivare la [delega della subnet](https://docs.microsoft.com/azure/virtual-network/manage-subnet-delegation) nella subnet di Istanza gestita e aggiornare le regole del firewall dell'account di archiviazione in modo da consentire tale subnet.

* Assicurarsi che le regole del gruppo di sicurezza di rete per la rete virtuale non blocchino le porte di comunicazione in uscita per Servizio Migrazione del database di Azure: 443, 53, 9354, 445, 12000. Per informazioni dettagliate sul filtro del traffico dei gruppi di sicurezza di rete della rete virtuale di Azure, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Configurare [Windows Firewall per l'accesso al motore del database di origine](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Aprire Windows Firewall per consentire a Servizio Migrazione del database di Azure di accedere all'istanza di origine di SQL Server, che per impostazione predefinita è tramite la porta TCM 1433.
* Se si eseguono più istanze denominate di SQL Server tramite porte dinamiche, è consigliabile abilitare il servizio SQL Browser e consentire l'accesso alla porta UDP 1434 attraverso i firewall, in modo che Servizio Migrazione del database di Azure possa connettersi a un'istanza denominata nel server di origine.
* Se si usa un'appliance firewall all'ingresso dei database di origine, può essere necessario aggiungere regole del firewall per consentire a Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione, oltre che ai file, attraverso la porta SMB 445.
* Creare un'istanza di Istanza gestita di SQL seguendo le istruzioni dettagliate riportate nell'articolo [Creare un'istanza di Istanza gestita di SQL nel portale di Azure](https://aka.ms/sqldbmi).
* Assicurarsi che gli account di accesso usati per la connessione all'istanza di SQL Server di origine e all'istanza di destinazione di Istanza gestita di SQL siano membri del ruolo del server sysadmin.
* Specificare una condivisione di rete SMB contenente tutti i file del backup completo del database e i successivi file di backup del log delle transazioni, che possono essere usati da Servizio Migrazione del database di Azure per la migrazione del database.
* Verificare che l'account del servizio che esegue l'istanza di SQL Server abbia privilegi di scrittura sulla condivisione di rete creata e che l'account computer del server di origine abbia accesso in lettura/scrittura alla stessa condivisione.
* Prendere nota di un utente (e una password) di Windows con privilegi di controllo completo sulla condivisione di rete creata in precedenza. Servizio Migrazione del database di Azure rappresenta le credenziali dell'utente necessarie per caricare i file di backup nel contenitore di archiviazione di Azure per l'operazione di ripristino.
* Creare un ID applicazione di Azure Active Directory per generare la chiave ID applicazione che potrà essere usata da Servizio Migrazione del database di Azure per connettersi a Istanza gestita di database SQL di Azure e al contenitore di archiviazione di Azure. Per altre informazioni, vedere l'articolo [Usare il portale per creare un'applicazione Azure Active Directory e un'entità servizio che possano accedere alle risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

  > [!NOTE]
  > Servizio Migrazione del database di Azure richiede l'autorizzazione di collaboratore nella sottoscrizione per l'ID applicazione specificato. In alternativa, è possibile creare ruoli personalizzati che concedono le autorizzazioni specifiche richieste da Servizio Migrazione del database di Azure. Per istruzioni dettagliate sull'uso dei ruoli personalizzati, vedere l'articolo [Ruoli personalizzati per migrazioni online da SQL Server a Istanza gestita di SQL](https://docs.microsoft.com/azure/dms/resource-custom-roles-sql-db-managed-instance).

* Creare o annotare un account di archiviazione di Azure con **livello di prestazioni Standard** che possa essere usato da Servizio Migrazione del database per caricare i file di backup di database e per la migrazione di database.  Assicurarsi di creare l'account di archiviazione di Azure nella stessa area in cui viene creata l'istanza di Servizio Migrazione del database di Azure.

  > [!NOTE]
  > Quando si esegue la migrazione online di un database protetto da [Transparent Data Encryption](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview) a un'istanza gestita, è necessario eseguire la migrazione del certificato corrispondente dall'istanza di SQL Server locale o della macchina virtuale di Azure prima del ripristino del database. Per i dettagli, vedere [Eseguire la migrazione del certificato TDE a un'istanza gestita](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview).

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrare il provider di risorse Microsoft.DataMigration

1. Accedere al portale di Azure, selezionare **Tutti i servizi**, quindi selezionare **Sottoscrizioni**.

    ![Mostra le sottoscrizioni del portale](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Selezionare la sottoscrizione in cui si vuole creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.

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

5. Selezionare una rete virtuale esistente oppure crearne una nuova.

    La rete virtuale consente a Servizio Migrazione del database di Azure di accedere all'istanza di SQL Server di origine e all'istanza di destinazione di Istanza gestita di SQL.

    Per altre informazioni su come creare una rete virtuale nel portale di Azure, vedere l'articolo [Creare una rete virtuale con il portale di Azure](https://aka.ms/DMSVnet).

    Per altri dettagli, vedere l'articolo relativo alle [topologie di rete per le migrazioni a Istanza gestita di SQL con Servizio Migrazione del database di Azure](https://aka.ms/dmsnetworkformi).

6. Selezionare uno SKU del piano tariffario Premium.

    > [!NOTE]
    > Le migrazioni online sono supportate solo quando viene usato il piano tariffario Premium.

    Per altre informazioni sui costi e i piani tariffari, vedere la [pagina relativa ai prezzi](https://aka.ms/dms-pricing).

    ![Creare il Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Selezionare **Crea** per creare il servizio.

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato un'istanza del servizio, individuarlo nel portale di Azure, aprirlo e creare un nuovo progetto di migrazione.

1. Nel portale di Azure selezionare **Tutti i servizi**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure**.

    ![Individuare tutte le istanze di Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. Nella schermata **Servizio Migrazione del database di Azure** cercare il nome dell'istanza creata e quindi selezionarla.

3. Selezionare **+ Nuovo progetto di migrazione**.

4. Nella schermata **Nuovo progetto di migrazione** specificare un nome per il progetto e quindi selezionare **SQL Server** nella casella di testo **Tipo del server di origine**, **Istanza gestita di SQL di Azure** nella casella di testo **Tipo del server di destinazione** e **Migrazione dei dati online** per **Scegli il tipo di attività**.

   ![Creare un progetto di Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Selezionare **Crea ed esegui attività** per creare il progetto.

## <a name="specify-source-details"></a>Specificare le informazioni di origine

1. Nella schermata **Dettagli origine della migrazione** specificare i dettagli di connessione per l'istanza di SQL Server di origine.

2. Se nel server non è installato un certificato attendibile, selezionare la casella di controllo **Considera attendibile certificato server**.

    Quando non è installato un certificato attendibile, SQL Server genera un certificato autofirmato all'avvio dell'istanza. Questo certificato viene usato per crittografare le credenziali per le connessioni client.

    > [!CAUTION]
    > Le connessioni TLS crittografate con un certificato autofirmato non offrono sicurezza avanzata. Sono infatti suscettibili ad attacchi man-in-the-middle. Non è consigliabile affidarsi a TLS usando certificati autofirmati in un ambiente di produzione o in server connessi a Internet.

   ![Dettagli origine](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Selezionare **Salva**.

4. Nella schermata **Seleziona database di origine** selezionare il database **AdventureWorks2012** per la migrazione.

   ![Selezionare i database di origine](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Se si usa SQL Server Integration Services (SSIS), Servizio Migrazione del database non supporta attualmente la migrazione del database di catalogo per i progetti/pacchetti SSIS (SSISDB) da SQL Server a Istanza gestita di SQL. È tuttavia possibile effettuare il provisioning di SSIS in Azure Data Factory (ADF) e ridistribuire i progetti/pacchetti SSIS nel database SSISDB di destinazione ospitato da Istanza gestita di SQL. Per altre informazioni sulla migrazione dei pacchetti SSIS, vedere l'articolo [Eseguire la migrazione di pacchetti SQL Server Integration Services in Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Selezionare **Salva**.

## <a name="specify-target-details"></a>Specificare i dettagli della destinazione

1. Nella schermata **Dettagli destinazione della migrazione** specificare i valori di **ID applicazione** e **Chiave** che potranno essere usati dall'istanza di Servizio Migrazione del database per connettersi all'istanza di destinazione di Istanza gestita di SQL e all'account di archiviazione di Azure.

    Per altre informazioni, vedere l'articolo [Usare il portale per creare un'applicazione Azure Active Directory e un'entità servizio che possano accedere alle risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

2. Selezionare la **sottoscrizione** contenente l'istanza di destinazione di Istanza gestita di SQL e quindi selezionare l'istanza di destinazione.

    Se non si è ancora effettuato il provisioning di Istanza gestita di SQL, selezionare il [collegamento](https://aka.ms/SQLDBMI) fornito a tale scopo. Quando l'istanza di Istanza gestita di SQL è pronta, tornare a questo progetto specifico per eseguire la migrazione.

3. Specificare i valori di **Utente SQL** e **Password** per la connessione a Istanza gestita di SQL.

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
    |**Condivisione del percorso di rete SMB** | Condivisione di rete SMB locale o condivisione file di Azure contenente i file di backup completo del database e i file di backup del log delle transazioni, che possono essere usati da Servizio Migrazione del database di Azure per la migrazione. L'account del servizio che esegue l'istanza di SQL Server di origine deve avere privilegi di lettura/scrittura per questa condivisione di rete. Specificare l'FQDN o l'indirizzo IP del server nella condivisione di rete, ad esempio '\\\nomeserver.nomedominio.com\cartellabackup' o '\\\indirizzoIP\cartellabackup'. Per migliorare le prestazioni, è consigliabile usare una cartella separata per ogni database di cui eseguire la migrazione. Per specificare il percorso di condivisione file a livello di database, usare l'opzione **Impostazioni avanzate**. |
    |**Nome utente** | Verificare che l'utente di Windows abbia i privilegi di controllo completo sulla condivisione di rete fornita in precedenza. Servizio Migrazione del database di Azure rappresenterà le credenziali utente necessarie per caricare i file di backup nel contenitore di archiviazione di Azure per l'operazione di ripristino. Se si usa la condivisione file di Azure, usare il nome dell'account di archiviazione con il prefisso AZURE\ come nome utente. |
    |**Password** | Password per l'utente. Se si usa la condivisione file di Azure, usare una chiave dell'account di archiviazione come password. |
    |**Sottoscrizione dell'account di archiviazione di Azure** | Selezionare la sottoscrizione contenente l'account di archiviazione di Azure. |
    |**Account di archiviazione di Azure** | Selezionare l'account di archiviazione di Azure in cui Servizio Migrazione del database potrà caricare i file di backup dalla condivisione di rete SMB e che potrà essere usato per la migrazione del database.  Per prestazioni di caricamento file ottimali, è consigliabile selezionare un account di archiviazione nella stessa area dell'istanza di Servizio Migrazione del database. |

    ![Configurare le impostazioni di migrazione](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

    > [!NOTE]
    > Se Servizio Migrazione del database di Azure visualizza l'errore 'Errore di sistema 53' o 'Errore di sistema 57', è possibile che Servizio Migrazione del database di Azure non possa accedere alla condivisione file di Azure. Se si verifica uno di questi errori, concedere l'accesso all'account di archiviazione dalla rete virtuale seguendo le istruzioni riportate [qui](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network).

    > [!IMPORTANT]
    > Se la funzionalità di controllo del loopback è abilitata e la condivisione file e l'istanza di SQL Server di origine si trovano nello stesso computer, l'origine non riuscirà ad accedere alla condivisione file usando il nome di dominio completo. Per risolvere questo problema, disabilitare la funzionalità di controllo del loopback usando le istruzioni disponibili [qui](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd).

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

   ![Stato dell'attività di migrazione](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Eseguire il cutover della migrazione

Al termine del ripristino del backup completo del database nell'istanza di destinazione di Istanza gestita di SQL, il database è disponibile per l'esecuzione di un cutover della migrazione.

1. Quando si è pronti a completare la migrazione online del database, selezionare **Avvia cutover**.

2. Arrestare tutto il traffico in ingresso verso i database di origine.

3. Usare il [backup della parte finale del log], rendere disponibile il file di backup nella condivisione di rete SMB e quindi attendere il ripristino di questo backup del log delle transazioni finale.

    A questo punto, il valore visualizzato per **Modifiche in sospeso** sarà impostato su 0.

4. Selezionare **Conferma** e quindi **Applica**.

    ![Preparazione per il completamento del cutover](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

    > [!IMPORTANT]
    > Dopo il cutover, la disponibilità di Istanza gestita di SQL solo con livello di servizio business critical può richiedere molto più tempo rispetto al livello per utilizzo generico perché è necessario effettuare il seeding di tre repliche secondarie per il gruppo di disponibilità elevata AlwaysOn. La durata dell'operazione dipende dalle dimensioni dei dati. Per altre informazioni, vedere [Durata delle operazioni di gestione](../azure-sql/managed-instance/management-operations-overview.md#duration).

5. Quando lo stato di migrazione del database è **Completato**, connettere le applicazioni alla nuova istanza di destinazione di Istanza gestita di SQL.

    ![Cutover completato](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Passaggi successivi

* Per un'esercitazione su come eseguire la migrazione di un database a Istanza gestita di SQL usando il comando T-SQL RESTORE, vedere [Ripristinare un backup in Istanza gestita di SQL con il comando restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
* Per altre informazioni su Istanza gestita di SQL, vedere [Che cos'è Istanza gestita SQL di Azure?](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).
* Per informazioni sulla connessione di app a Istanza gestita di SQL, vedere [Connettere le applicazioni](../azure-sql/managed-instance/connect-application-instance.md).
