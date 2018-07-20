---
title: Eseguire il backup dei database di SQL Server in Azure | Microsoft Docs
description: Questa esercitazione illustra l'esecuzione del backup di SQL Server in Azure. L'articolo spiega inoltre il recupero di SQL Server.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/6/2018
ms.author: markgal;anuragm
ms.custom: ''
ms.openlocfilehash: 32f45b66c4b1d22da3ffc4310a8a47c17319301f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38302824"
---
# <a name="back-up-sql-server-database-in-azure"></a>Eseguire il backup del database di SQL Server in Azure

I database di SQL Server sono carichi di lavoro critici che richiedono un obiettivo del punto di ripristino (RPO) basso e la conservazione a lungo termine. Backup di Azure offre una soluzione di backup per SQL Server che non richiede un'infrastruttura, ovvero nessun server di backup complesso, nessun agente di gestione né un archivio backup da gestire. Backup di Azure offre una gestione centralizzata dei backup in tutti i server di SQL Server o anche in carichi di lavoro diversi.

 Contenuto dell'articolo:

> [!div class="checklist"]
> * Prerequisiti per l'esecuzione del backup di SQL Server in Azure
> * Creare e usare un insieme di credenziali dei Servizi di ripristino
> * Configurare i backup dei database di SQL Server
> * Impostare un criterio di backup, o di conservazione, per i punti di recupero
> * Come recuperare il database

Prima di iniziare le procedure descritte in questo articolo, verificare che SQL Server sia in esecuzione in Azure. [È possibile usare le macchine virtuali del marketplace per SQL per creare rapidamente un server SQL](../sql-database/sql-database-get-started-portal.md).

## <a name="public-preview-limitations"></a>Limiti dell'anteprima pubblica

Gli elementi seguenti sono limitazioni note per l'anteprima pubblica.

- La macchina virtuale SQL richiede la connettività Internet per accedere agli indirizzi IP pubblici di Azure. Per maggiori dettagli, vedere la sezione [Stabilire la connettività di rete](backup-azure-sql-database.md#establish-network-connectivity).
- È possibile proteggere fino a 2000 database SQL in un insieme di credenziali di Servizi di ripristino. I database SQL aggiuntivi devono essere archiviati in un insieme di credenziali di Servizi di ripristino separato.
- [Il backup dei gruppi di disponibilità distribuiti presenta limitazioni](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017).
- Le istanze del cluster di Failover di SQL non sono supportate.
- Usare il portale di Azure per configurare Backup di Azure per proteggere i database di SQL Server. Il supporto per Azure PowerShell, CLI e API REST non è attualmente disponibile.

## <a name="supported-azure-geos"></a>Aree geografiche di Azure supportate

- Australia sud-orientale (ASE) 
- Brasile meridionale (BRS)
- Canada centrale (CNC)
- Canada orientale (CE)
- Stati Uniti centrali (CUS)
- Asia orientale (EA)
- Australia orientale (AE) 
- Stati Uniti orientali (EUS)
- Stati Uniti orientali 2 (EUS2)
- Giappone orientale (JPE)
- Giappone occidentale (JPW)
- India centrale (INC) 
- India meridionale (INS)
- Corea del Sud centrale (KRC)
- Corea del Sud meridionale (KRS)
- Stati Uniti centro-settentrionali (NCUS) 
- Europa settentrionale (NE) 
- Stati Uniti a centro-meridionali (SCUS) 
- Asia sud-orientale (SEA)
- Regno Unito meridionale (UKS) 
- Regno Unito occidentale (UKW) 
- Europa occidentale (OE) 
- Stati Uniti occidentali (WUS)
- Stati Uniti centro occidentali (WCUS)
- Stati Uniti occidentali 2 (WUS 2) 

## <a name="supported-operating-systems-and-versions-of-sql-server"></a>Versioni di SQL Server e sistemi operativi supportati

Sono supportati i sistemi operativi seguenti: Sono supportate macchine virtuali di Azure e del marketplace SQL e macchine virtuali non del marketplace (in cui SQL Server è installato manualmente).

### <a name="supported-operating-systems"></a>Sistemi operativi supportati

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

Linux non è attualmente supportato.

### <a name="supported-versionseditions-of-sql-server"></a>Versioni/edizioni di SQL Server supportate

- SQL 2012 Enterprise, Standard, Web, Developer, Express
- SQL 2014 Enterprise, Standard, Web, Developer, Express
- SQL 2016 Enterprise, Standard, Web, Developer, Express
- SQL 2017 Enterprise, Standard, Web, Developer, Express


## <a name="prerequisites-for-using-azure-backup-to-protect-sql-server"></a>Prerequisiti per l'uso di Backup di Azure per proteggere SQL Server 

Prima di eseguire il backup del database di SQL Server, verificare le condizioni seguenti. :

- Identificare o [creare un insieme di credenziali di Servizi di ripristino](backup-azure-sql-database.md#create-a-recovery-services-vault) nella stessa area, o nelle stesse impostazioni locali, della macchina virtuale che ospita SQL Server.
- [Verificare le autorizzazioni per la macchina virtuale](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms) necessarie per eseguire il backup dei database SQL.
- [La macchina virtuale SQL ha la connettività di rete](backup-azure-sql-database.md#establish-network-connectivity).

> [!NOTE]
> È possibile avere solo una soluzione di backup alla volta per eseguire il backup dei database di SQL Server. Disabilitare eventuali altri backup di SQL prima di usare questa funzionalità, altrimenti i backup interferiscono e hanno esito negativo. È possibile abilitare Backup di Azure per la macchina virtuale IaaS insieme al backup di SQL senza alcun conflitto 
>

Se queste condizioni esistono nell'ambiente in uso, passare alla sezione relativa alla [configurazione dell'insieme di credenziali per proteggere un database SQL](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database). Se uno dei prerequisiti non esiste, continuare a leggere questa sezione.


## <a name="establish-network-connectivity"></a>Stabilire la connettività di rete

Per tutte le operazioni, la macchina virtuale SQL richiede la connettività agli indirizzi IP pubblici di Azure. Le operazioni della macchina virtuale SQL, ad esempio individuazione dei database, configurazione del backup, backup pianificati, ripristino dei punti di recupero e così via, non riescono in assenza di connessioni agli indirizzi IP pubblici. Eseguire una delle operazioni seguenti per specificare un percorso chiaro per il traffico di backup:

- Aggiungere gli intervalli IP dei data center di Azure all'elenco elementi consentiti usando la [pagina dell'Area download per informazioni dettagliate su intervalli IP e istruzioni](https://www.microsoft.com/download/details.aspx?id=41653). 
- Distribuire un server proxy HTTP per il routing del traffico: quando si esegue il backup di un database SQL in una macchina virtuale, l'estensione di backup nella macchina virtuale usa le API HTTPS per inviare i comandi di gestione a Backup di Azure e i dati ad Archiviazione di Azure. L'estensione di backup usa anche Azure Active Directory (AAD) per l'autenticazione. Indirizzare il traffico dell'estensione di backup per questi tre servizi attraverso il proxy HTTP perché è l'unico componente configurato per l'accesso alla rete Internet pubblica.

I compromessi tra le scelte disponibili sono: gestibilità, controllo granulare e costi.

> [!NOTE]
>I tag di servizio per Backup di Azure devono essere disponibili in base alla disponibilità generale.
>

| Opzione | Vantaggi | Svantaggi: |
| ------ | ---------- | ------------- |
| Aggiungere gli intervalli IP all'elenco elementi consentiti  | Senza costi aggiuntivi. <br/> Per aprire l'accesso in un gruppo di sicurezza di rete, usare il cmdlet **Set-AzureNetworkSecurityRule**. | Complessità di gestione a causa della variazione nel tempo degli intervalli IP interessati. <br/>Possibilità di accesso a tutto l'ambiente Azure, non solo al servizio di archiviazione.|
| Usare un proxy HTTP   | Possibilità di controllo granulare nel proxy sugli URL di archiviazione. <br/>Singolo punto di accesso Internet alle VM. <br/> Non è soggetto alle modifiche degli indirizzi IP di Azure. | Costi aggiuntivi per l'esecuzione di una VM con il software proxy. |

## <a name="set-permissions-for-non-marketplace-sql-vms"></a>Impostare le autorizzazioni per le macchine virtuali SQL non del marketplace

Per eseguire il backup di una macchina virtuale, Backup di Azure richiede l'installazione dell'estensione **AzureBackupWindowsWorkload**. Se si usano macchine virtuali di Azure Marketplace, ignorare i passaggi fino a [Individuare database di SQL Server](backup-azure-sql-database.md#discover-sql-server-databases). Se la macchina virtuale che ospita i database SQL non è stata creata da Azure Marketplace, completare la sezione seguente per installare l'estensione e impostare le autorizzazioni appropriate. Oltre all'estensione **AzureBackupWindowsWorkload**, Backup di Azure richiede privilegi sysadmin SQL per proteggere i database SQL. Durante l'individuazione dei database nella macchina virtuale, Backup di Azure crea un account, NT Service\AzureWLBackupPluginSvc. Perché Backup di Azure sia in grado di individuare i database SQL, l'account NT Service\AzureWLBackupPluginSvc deve disporre delle autorizzazioni SQL e sysadmin SQL. La procedura seguente illustra come specificare queste autorizzazioni.

Per configurare le autorizzazioni:

1. Nel [portale di Azure](https://portal.azure.com) aprire l'insieme di credenziali di Servizi di ripristino usato per proteggere i database SQL.
2. Fare clic su **+ Backup** nel menu del dashboard dell'insieme di credenziali per aprire il menu **Obiettivo di backup**.

   ![Fare clic su + Backup per aprire il menu Obiettivo di backup](./media/backup-azure-sql-database/open-backup-menu.png)

3. Nel menu **Posizione di esecuzione del carico di lavoro** del menu **Obiettivo di backup** lasciare **Azure** come impostazione predefinita.

4. Nel menu **Elementi di cui eseguire il backup** espandere il menu a discesa e selezionare **SQL Server in una macchina virtuale di Azure**.

    ![Fare clic su + Backup per aprire il menu Obiettivo di backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Il menu **Obiettivo di backup** visualizza due nuovi passaggi: **Individuare i database nelle macchine virtuali** e **Configurare il backup**. **Individuare i database nelle macchine virtuali** avvia una ricerca di macchine virtuali di Azure.

    ![Vengono illustrati i nuovi passaggi dell'obiettivo di backup](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

5. Fare clic su **Avvia individuazione** per cercare le macchine virtuali non protette nella sottoscrizione. In base al numero di macchine virtuali non protette nella sottoscrizione, scorrere tutte le macchine virtuali può richiedere un certo tempo.

    ![Backup in sospeso](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    Quando una macchina virtuale non protetta viene rilevata, viene visualizzata nell'elenco. Le macchine virtuali non protette sono elencate in base al nome di macchina virtuale e al gruppo di risorse. È possibile che più macchine virtuali abbiano lo stesso nome. Tuttavia, le macchine virtuali con lo stesso nome appartengono a gruppi di risorse diversi. Se, contrariamente a quanto previsto, una macchina virtuale non appare nell'elenco, verificare se è già protetta da un insieme di credenziali.

6. Selezionare dall'elenco di macchine virtuali la macchina contenente il database SQL di cui si vuole eseguire il backup e fare clic su **Individua database**. 

    Il processo di individuazione consente di installare l'estensione **AzureBackupWindowsWorkload** nella macchina virtuale. L'estensione consente al servizio Backup di Azure di comunicare con la macchina virtuale in modo che esegua il backup dei database SQL. Dopo l'installazione dell'estensione, Backup di Azure crea l'account del servizio virtuale di Windows, **NT Service\AzureWLBackupPluginSvc**, nella macchina virtuale. L'account del servizio virtuale richiede l'autorizzazione sysadmin SQL. Durante il processo di installazione dell'account del servizio virtuale, se viene visualizzato l'errore **UserErrorSQLNoSysadminMembership**, vedere la sezione [Correzione delle autorizzazioni sysadmin SQL](backup-azure-sql-database.md#fixing-sql-sysadmin-permissions).

    L'area delle notifiche mostra lo stato di avanzamento dell'individuazione dei database. In base al numero di database presenti nella macchina virtuale, il completamento del processo può richiedere tempo. Quando i database selezionati sono stati individuati, viene visualizzato un messaggio di conferma.

    ![messaggio di notifica per distribuzione eseguita](./media/backup-azure-sql-database/notifications-db-discovered.png)

Dopo aver associato il database all'insieme di credenziali di Servizi di ripristino, il passaggio successivo è [configurare il backup](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

### <a name="fixing-sql-sysadmin-permissions"></a>Correzione delle autorizzazioni sysadmin SQL

Durante il processo di installazione, se viene visualizzato l'errore **UserErrorSQLNoSysadminMembership**, usare un account con le autorizzazioni sysadmin SQL per accedere a SQL Server Management Studio (SSMS). A meno che non siano necessarie autorizzazioni speciali, l'autenticazione di Windows dovrebbe funzionare.

1. In SQL Server aprire la cartella **Sicurezza/Account di accesso**.

    ![Aprire le cartelle di sicurezza e account di accesso di SQL Server per visualizzare gli account](./media/backup-azure-sql-database/security-login-list.png)

2. Nella cartella Account di accesso fare clic con il pulsante destro del mouse e selezionare **Nuovo account di accesso** e quindi fare clic su **Ricerca** nella finestra di dialogo Account di accesso - Nuovo

    ![Aprire Ricerca nella finestra di dialogo Account di accesso - Nuovo](./media/backup-azure-sql-database/new-login-search.png)

3. Poiché l'account del servizio virtuale Windows **NT Service\AzureWLBackupPluginSvc** è già stato creato durante la registrazione della macchina virtuale e la fase di individuazione SQL, immettere il nome dell'account come appare nella finestra di dialogo  **Immettere il nome dell'oggetto da selezionare**. Fare clic su **Controlla nomi** per risolvere il nome. 

    ![Fare clic sul pulsante Controlla nomi per risolvere il nome del servizio sconosciuto](./media/backup-azure-sql-database/check-name.png)

4. Fare clic su **OK** per chiudere la finestra di dialogo Seleziona utente o gruppo.

5. Nella finestra di dialogo **Ruoli del server** verificare che sia selezionato il ruolo **sysadmin**. Quindi fare clic su **OK** per chiudere **Account di accesso - Nuovo**.

    ![Verificare che sia selezionato il ruolo del server sysadmin](./media/backup-azure-sql-database/sysadmin-server-role.png)

    Le autorizzazioni necessarie a questo punto devono essere presenti.

6. Anche se è stato corretto l'errore relativo alle autorizzazioni, è comunque necessario associare il database all'insieme di credenziali di Servizi di ripristino. Nell'elenco dei **server protetti** del portale di Azure fare clic con il pulsante destro del mouse sul server in errore e selezionare **Individua di nuovo i database**.

    ![Verificare che il server abbia le autorizzazioni appropriate](./media/backup-azure-sql-database/check-erroneous-server.png)

    L'area delle notifiche mostra lo stato di avanzamento dell'individuazione dei database. In base al numero di database presenti nella macchina virtuale, il completamento del processo può richiedere tempo. Se i database selezionati vengono trovati, nell'area delle notifiche appare un messaggio di conferma.

    ![messaggio di notifica per distribuzione eseguita](./media/backup-azure-sql-database/notifications-db-discovered.png)

Dopo aver associato il database all'insieme di credenziali di Servizi di ripristino, il passaggio successivo è [configurare il backup](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

[!INCLUDE [Section explaining how to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Individuare i database SQL Server

Backup di Azure è in grado di individuare tutti i database in un'istanza di SQL Server in modo che sia possibile proteggerli in base alle esigenze di backup. Usare la procedura seguente per identificare la macchina virtuale che ospita i database SQL. Dopo aver identificato la macchina virtuale, Backup di Azure installa un'estensione semplificata che consente di individuare i database di SQL Server.

1. Accedere alla propria sottoscrizione nel [portale di Azure](https://portal.azure.com/).
2. Nel menu a sinistra selezionare **Tutti i servizi**.

    ![Scegliere l'opzione Tutti i servizi nel menu principale](./media/backup-azure-sql-database/click-all-services.png) <br/>

3. Nella finestra di dialogo Tutti i servizi digitare *Servizi di ripristino*. Non appena si inizia a digitare, l'elenco delle risorse viene filtrato in base all'input. Quando viene visualizzata, selezionare l'opzione **Insiemi di credenziali di Servizi di ripristino**.

    ![Digitare Servizi di ripristino nella finestra di dialogo Tutti i servizi](./media/backup-azure-sql-database/all-services.png) <br/>

    Viene visualizzato l'elenco degli insiemi di credenziali di Servizi di ripristino. 

4. Nell'elenco degli insiemi di credenziali di Servizi di ripristino selezionare l'insieme di credenziali che si vuole usare per proteggere i database SQL.

5. Fare clic su **+ Backup** nel menu del dashboard dell'insieme di credenziali per aprire il menu **Obiettivo di backup**.

   ![Fare clic su + Backup per aprire il menu Obiettivo di backup](./media/backup-azure-sql-database/open-backup-menu.png)

6. Nel menu **Posizione di esecuzione del carico di lavoro** del menu **Obiettivo di backup** lasciare **Azure** come impostazione predefinita.

7. Nel menu **Elementi di cui eseguire il backup** espandere il menu a discesa e selezionare **SQL Server in una macchina virtuale di Azure**.

    ![Fare clic su + Backup per aprire il menu Obiettivo di backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Se selezionato, il menu **Obiettivo di backup** visualizza due passaggi: Individuare i database nelle macchine virtuali e Configurare il backup. 

    ![Vengono illustrati i nuovi passaggi dell'obiettivo di backup](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

8. Fare clic su **Avvia individuazione** per cercare le macchine virtuali non protette nella sottoscrizione. In base al numero di macchine virtuali non protette nella sottoscrizione, scorrere tutte le macchine virtuali può richiedere un certo tempo.

    ![Backup in sospeso](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    Quando una macchina virtuale non protetta viene rilevata, viene visualizzata nell'elenco. Più macchine virtuali possono avere lo stesso nome. Tuttavia, più virtuali con lo stesso nome appartengono a gruppi di risorse diversi. Le macchine virtuali non protette sono elencate in base al nome di macchina virtuale e al gruppo di risorse. Se, contrariamente a quanto previsto, una macchina virtuale non è indicata, verificare se è già protetta da un insieme di credenziali.

9. Nell'elenco di macchine virtuali selezionare la casella di controllo della macchina virtuale che contiene i database SQL da proteggere e fare clic su **Individua database**.

    Backup di Azure individua tutti i database SQL nella macchina virtuale. Per informazioni su cosa accade durante la fase di individuazione dei database, vedere la sezione seguente, [Operazioni di back-end durante l'individuazione dei database SQL](backup-azure-sql-database.md#backend-operations-when-discovering-sql-databases). Dopo aver individuato i database SQL, si è pronti a [configurare il processo di backup](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

### <a name="backend-operations-when-discovering-sql-databases"></a>Operazioni di back-end durante l'individuazione dei database SQL

Quando si usa lo strumento di **individuazione dei database** Backup di Azure esegue queste operazioni in background:

- registra la macchina virtuale con l'insieme di credenziali di Servizi di ripristino per il backup del carico di lavoro. Per tutti i database nella macchina virtuale registrata il backup può essere eseguito solo per questo insieme di credenziali di Servizi di ripristino. 

- installa l'estensione **AzureBackupWindowsWorkload** nella macchina virtuale. Il backup di un database SQL è una soluzione senza agente, ovvero con l'estensione installata nella macchina virtuale, non viene installato alcun agente nel database SQL.

- crea l'account del servizio, **NT Service\AzureWLBackupPluginSvc**, nella macchina virtuale. Tutte le operazioni di backup e ripristino usano l'account del servizio. Per **NT Service\AzureWLBackupPluginSvc** sono necessarie le autorizzazioni sysadmin SQL. In tutte le macchine virtuali di SQL Marketplace è installato SqlIaaSExtension e AzureBackupWindowsWorkload usa SQLIaaSExtension per ottenere automaticamente le autorizzazioni necessarie. Se nella macchina virtuale non è installato SqlIaaSExtension, l'operazione di individuazione dei database non riesce e viene visualizzato il messaggio di errore **UserErrorSQLNoSysAdminMembership**. Per aggiungere l'autorizzazione sysadmin per il backup, seguire le istruzioni in [Impostazione di autorizzazioni di Backup di Azure per le macchine virtuali SQL non del marketplace](backup-azure-sql-database.md#set-permissions-for-non--marketplace-sql-vms).

    ![selezionare la macchina virtuale e il database](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup-for-sql-server-database"></a>Configurare il backup per il database di SQL Server

Backup di Azure offre servizi di gestione per proteggere i database di SQL Server e gestire i processi di backup. La funzionalità di gestione e monitoraggio dipendono dall'insieme di credenziali di Servizi di ripristino. 

> [!NOTE]
> È possibile avere solo una soluzione di backup alla volta per eseguire il backup dei database di SQL Server. Disabilitare eventuali altri backup di SQL prima di usare questa funzionalità, altrimenti i backup interferiscono e hanno esito negativo. È possibile abilitare Backup di Azure per la macchina virtuale IaaS insieme al backup di SQL senza alcun conflitto 
>

Per configurare la protezione per il database SQL:

1. Aprire l'insieme di credenziali di Servizi di ripristino registrato con la macchina virtuale SQL.

2. Fare clic su **+ Backup** nel menu del dashboard dell'insieme di credenziali per aprire il menu **Obiettivo di backup**.

    ![Fare clic su + Backup per aprire il menu Obiettivo di backup](./media/backup-azure-sql-database/open-backup-menu.png)

3. Nel menu **Posizione di esecuzione del carico di lavoro** del menu **Obiettivo di backup** lasciare **Azure** come impostazione predefinita.

4. Nel menu **Elementi di cui eseguire il backup** espandere il menu a discesa e selezionare **SQL Server in una macchina virtuale di Azure**.

    ![Fare clic su + Backup per aprire il menu Obiettivo di backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Se selezionato, il menu **Obiettivo di backup** visualizza due passaggi: Individuare i database nelle macchine virtuali e Configurare il backup. Se questo articolo è stato consultato in ordine, l'utente ha già individuato le macchine virtuali non protette e questo insieme di credenziali è registrato con una macchina virtuale. A questo punto si è pronti a configurare la protezione per i database SQL.

5. Nel menu Obiettivo di backup fare clic su **Configura backup**.

    ![Vengono illustrati i nuovi passaggi dell'obiettivo di backup](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    Il servizio Backup di Azure visualizza tutte le istanze SQL con database autonomi, nonché i gruppi di disponibilità SQL AlwaysOn. Per visualizzare i database autonomi nell'istanza di SQL, fare clic sulla freccia di espansione accanto al nome dell'istanza per visualizzare i database. Le immagini seguenti contengono esempi di un'istanza autonoma e un gruppo di disponibilità Always On.

    > [!NOTE]
    > Nel caso di Gruppi di disponibilità AlwaysOn, vengono usate le preferenze di backup di SQL. A causa di limitazione di piattaforma SQL, è necessario eseguire backup differenziali e completi dal nodo primario. Il backup del log può essere eseguito in base alle proprie preferenze di backup. A causa di questa limitazione, il nodo primario deve essere sempre registrato per i gruppi di disponibilità.
    >

    ![Elenco di database nell'istanza di SQL](./media/backup-azure-sql-database/discovered-databases.png)

    Fare clic sulla freccia di espansione accanto ai gruppi di disponibilità AlwaysOn per visualizzare l'elenco dei database.

    ![Elenco dei database in un gruppo di disponibilità AlwaysOn](./media/backup-azure-sql-database/discovered-database-availability-group.png)

6. Nell'elenco dei database selezionare tutti gli elementi da proteggere e fare clic su **OK**.

    ![selezionare più database per la protezione](./media/backup-azure-sql-database/select-multiple-database-protection.png)

    È possibile selezionare fino a 50 database alla volta. Se i database da proteggere sono più di 50, eseguire più volte l'operazione. Dopo aver protetto i primi 50 database, ripetere questo passaggio per proteggere il set successivo di database.
    > [!Note] 
    > Per ottimizzare i carichi di backup, Backup di Azure suddivide i processi di backup di grandi dimensioni in più batch. Il numero massimo di database in un solo processo di backup è 50.
    >
    >

7. Per creare o scegliere un criterio di backup, nel menu **Backup** selezionare **Criterio di Backup** per aprire il menu.

    ![selezionare l'opzione criterio di backup](./media/backup-azure-sql-database/select-backup-policy.png)

8. Dal menu a discesa **Scegliere i criteri di backup** scegliere un criterio di backup e fare clic su **OK**. Per informazioni sulla creazione di propri criteri di backup, vedere la sezione [Definire un criterio di backup](backup-azure-sql-database.md#define-a-backup-policy).

    ![scegliere un criterio di backup dal menu a discesa](./media/backup-azure-sql-database/select-backup-policy-steptwo.png)

    Nel menu dei criteri di backup, dal menu a discesa **Scegliere i criteri di backup** è possibile scegliere: 
    - il criterio HourlyLogBackup predefinito, 
    - un criterio di backup creato in precedenza per SQL,
    - di [definire un nuovo criterio](backup-azure-sql-database.md#define-a-backup-policy) basato sull'obiettivo del punto di recupero e sull'intervallo di conservazione. 

    > [!Note]
    > Backup di Azure supporta la conservazione a lungo termine basata sullo schema di backup nonno-padre-figlio per ottimizzare il consumo di archiviazione back-end rispettando le esigenze di conformità.
    >

9. Dopo aver scelto un criterio di backup, nel **menu Backup** fare clic su **Abilita backup**.

    ![abilitare i criteri di backup scelti](./media/backup-azure-sql-database/enable-backup-button.png)

    È possibile monitorare l'avanzamento della configurazione nell'area delle notifiche del portale.

    ![visualizzare l'area delle notifiche](./media/backup-azure-sql-database/notifications-area.png)


### <a name="define-a-backup-policy"></a>Definire un criterio di backup

Un criterio di backup definisce una matrice relativa a quando sono stati acquisiti i backup e per quanto tempo tali backup vengono conservati. È possibile usare Backup di Azure per pianificare tre tipi di backup per i database SQL:

* Backup completo: un backup completo è un backup eseguito per l'intero database. Un backup completo contiene tutti i dati di un database o di un set di file o filegroup specifico e un log sufficiente per recuperare i dati. Al massimo, è possibile attivare un backup completo al giorno. È possibile scegliere di eseguire un backup completo con frequenza giornaliera o settimanale. 
* Backup differenziale: un backup differenziale si basa sul precedente backup completo dei dati più recente. Un backup differenziale acquisisce solo i dati che sono stati modificati dopo il backup completo. Al massimo, è possibile attivare un backup differenziale al giorno. Non è possibile configurare un backup completo e un backup differenziale nella stessa giornata.
* Backup del log delle transazioni: un backup del log consente il ripristino temporizzato fino a uno specifico secondo. Al massimo, è possibile configurare backup del log delle transazioni ogni 15 minuti.

I criteri vengono creati a livello di insieme di credenziali di Servizi di ripristino. Se si hanno più insiemi di credenziali, gli insiemi possono usare gli stessi criteri di backup, ma è necessario applicare i criteri di backup a ogni insieme di credenziali. Quando si crea un criterio di backup, il backup completo giornaliero è l'impostazione predefinita. È possibile aggiungere un backup differenziale, ma solo se si seleziona l'esecuzione settimanale del backup completo. La procedura seguente illustra come creare un criterio di backup per un server SQL in una macchina virtuale di Azure.

Per creare un criterio di backup

1. Nel menu dei criteri di Backup selezionare **Crea nuovo** dal menu a discesa **Scegliere i criteri di backup**.

   ![creare un nuovo criterio di backup](./media/backup-azure-sql-database/create-new-backup-policy.png)

    Il menu Criteri di backup ora mette a disposizione i campi necessari per qualsiasi nuovo criterio di backup di SQL Server.

   ![nuovi campi di criteri di backup](./media/backup-azure-sql-database/blank-new-policy.png)

2. Specificare un nome in **Nome criterio**. 

3. Un backup completo è obbligatorio. È possibile accettare i valori predefiniti per il backup completo o fare clic su **Backup completo** per modificare il criterio.

    ![nuovi campi di criteri di backup](./media/backup-azure-sql-database/full-backup-policy.png)

    All'interno del criterio Backup completo scegliere Giornaliero o Settimanale per la frequenza. Se si sceglie Giornaliero, scegliere l'ora e il fuso orario per l'inizio del processo di backup. Se si sceglie di eseguire backup completi giornalieri, non è possibile creare backup differenziali.

   ![impostazione intervallo giornaliero](./media/backup-azure-sql-database/daily-interval.png)

    Se si sceglie Settimanale, scegliere il giorno della settimana, l'ora e il fuso orario per l'inizio del processo di backup.

   ![impostazione intervallo settimanale](./media/backup-azure-sql-database/weekly-interval.png)

4. Per impostazione predefinita, tutte le opzioni relative all'intervallo di conservazione (giornaliero, settimanale, mensile e annuale) sono selezionate. Deselezionare i limiti dell'intervallo di conservazione da non usare e impostare gli intervalli da usare. Nel menu del criterio Backup completo fare clic su **OK** per accettare le impostazioni.

   ![impostazione dell'intervallo di conservazione](./media/backup-azure-sql-database/retention-range-interval.png)

    I punti di recupero vengono contrassegnati per la conservazione, in base al relativo intervallo. Ad esempio, se si seleziona un backup completo giornaliero, viene attivato solo un backup completo ogni giorno. In base alla conservazione settimanale, il backup del giorno specifico viene contrassegnato e conservato in base all'intervallo di conservazione settimanale. L'intervallo di conservazione mensile e annuale si comporta allo stesso modo.

5. Per aggiungere un criterio di backup differenziale, fare clic su **Backup differenziale** per aprire il relativo menu. 

   ![aprire criteri differenziali](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

    Nel menu del criterio Backup differenziale selezionare **Abilita** per aprire i controlli di frequenza e conservazione. Può essere attivato al massimo un backup differenziale al giorno.
    > [!Important] 
    > I backup differenziali possono essere conservati al massimo per 180 giorni. Se è necessario conservarli più a lungo, usare i backup completi, poiché non è possibile usare i backup differenziali.
    >

   ![modificare criteri differenziali](./media/backup-azure-sql-database/enable-differential-backup-policy.png)

    Fare clic su **OK** per salvare il criterio e tornare al menu principale Criteri di backup.

6. Per aggiungere un criterio di backup del log transazionale, fare clic su **Backup del log** per aprire il relativo menu. Nel menu Backup del log selezionare **Abilita**e impostare i controlli di frequenza e conservazione. I backup del log possono verificarsi ogni 15 minuti ed essere conservati per un massimo di 35 giorni. Fare clic su **OK** per salvare il criterio e tornare al menu principale Criteri di backup.

   ![modificare i criteri di backup del log](./media/backup-azure-sql-database/log-backup-policy-editor.png)

7. Scegliere se abilitare la compressione dei backup SQL. La compressione è disabilitata per impostazione predefinita.

    Nel back-end Backup di Azure usa la compressione di backup nativa di SQL.

8. Dopo aver effettuato tutte le modifiche ai criteri di backup, fare clic su **OK**. 

   ![accettare i nuovi criteri](./media/backup-azure-sql-database/backup-policy-click-ok.png)

## <a name="restore-a-sql-database"></a>Ripristinare un database SQL

Backup di Azure offre una funzionalità che consente di ripristinare singoli database fino a una data o un'ora specifica, fino a un secondo specifico, usando i backup del log delle transazioni. In base ai tempi di ripristino specificati, Backup di Azure determina automaticamente i backup completi, differenziali e la catena di backup del log necessari per ripristinare i dati.

In alternativa, è possibile selezionare un backup completo o differenziale specifico per il ripristino fino a un punto di recupero specifico anziché un'ora specifica.
 > [!Note]
 > Prima di attivare il ripristino del database master, avviare SQL Server in modalità utente singolo con opzione di "-m AzureWorkloadBackup". L'argomento a -m è il nome dell'unico client autorizzato ad aprire la connessione. Per tutti i database di sistema (modello, master, msdb), arrestare il servizio SQL Agent prima di attivare il ripristino. Chiudere tutte le applicazioni che potrebbero provare a usare una connessione di uno di questi database.
>

Per ripristinare un database

1. Aprire l'insieme di credenziali di Servizi di ripristino registrato con la macchina virtuale SQL.

2. Nel dashboard dell'insieme di credenziali selezionare **Utilizzo** sotto Elementi di backup per aprire il menu Elementi di backup.

    ![Fare clic su + Backup per aprire il menu Obiettivo di backup](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. Nel menu **Elementi di backup** selezionare il tipo di gestione dei backup, **SQL nella macchina virtuale di Azure**. 

    ![Fare clic su + Backup per aprire il menu Obiettivo di backup](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    L'elenco degli elementi di backup si adegua in modo da visualizzare l'elenco di database SQL. 

4. Selezionare il database da ripristinare dall'elenco dei database SQL.

    ![selezionare SQL nella macchina virtuale di Azure dall'elenco](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Quando si seleziona il database, viene aperto il relativo menu. Il menu contiene i dettagli sul backup per il database, tra cui:

    * i punti di ripristino meno recenti e più recenti,
    * lo stato del backup del log per le ultime 24 ore (per i database con modello di recupero con registrazione completa e con registrazione minima delle operazioni bulk, se configurato per i backup del log delle transazioni)

5. Nel menu del database selezionato fare clic su **Ripristina database** per aprire il menu Ripristina.

    ![selezionare ripristino del database](./media/backup-azure-sql-database/restore-db-button.png)

    Il menu **Ripristina** si apre, così come il menu **Configurazione di ripristino**. Il menu **Configurazione di ripristino** è il primo passaggio della configurazione del ripristino. In questo menu si seleziona il punto in cui si vogliono ripristinare i dati. Le opzioni sono:
    * Percorso alternativo: usare questa opzione per ripristinare il database in un percorso diverso pur conservando il database di origine.
    * Sovrascrivi database: ripristina i dati nella stessa istanza di SQL Server dell'origine. L'effetto di questa opzione è che si sovrascrive il database originale.

    > [!Important]
    > Se il database selezionato appartiene a un gruppo di disponibilità Always On, SQL non consente di sovrascrivere il database. In questo caso è abilitata solo l'opzione **Percorso alternativo**.
    >

    ![fare clic su Configura per aprire il menu Configurazione di ripristino](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Ripristinare in un percorso alternativo

Questa procedura illustra in dettaglio il ripristino dei dati in un percorso alternativo. Se si vuole sovrascrivere il database durante il ripristino, passare alla sezione [Ripristinare e sovrascrivere il database](backup-azure-sql-database.md#restore-and-overwrite-the-database). Questa procedura presuppone che sia aperto l'insieme di credenziali di Servizi di ripristino e sia visualizzato il menu Configurazione di ripristino. In caso contrario, iniziare con la sezione [Ripristinare un database SQL](backup-azure-sql-database.md#restore-a-sql-database).

> [!NOTE]
> È possibile ripristinare il database in un'istanza di SQL Server nella stessa area di Azure e il server di destinazione deve essere registrato per l'insieme di credenziali di Servizi di ripristino. 
>

Il menu a discesa **Server** visualizza solo i server SQL registrati con l'insieme di credenziali di Servizi di ripristino. Se il server che si vuole usare non appare nell'elenco **Server**, vedere la sezione [Individuare i database di SQL Server](backup-azure-sql-database.md#discover-sql-server-databases) per trovarlo. Durante il processo di individuazione dei database, eventuali nuovi server vengono registrati nell'insieme di credenziali di Servizi di ripristino.

1. Nel menu **Configurazione di ripristino**:

    * selezionare **Percorso alternativo**,
    * per **Server**, scegliere il server SQL in cui si vuole ripristinare il database.
    * Nel menu a discesa **Istanza** scegliere un'istanza di SQL
    * nella finestra di dialogo **Nome del database ripristinato** specificare il nome del database di destinazione.
    * se applicabile, scegliere di **sovrascrivere se il database con lo stesso nome esiste già nell'istanza selezionata di SQL**.
    * fare clic su **OK** per completare la configurazione della destinazione e passare alla scelta di un punto di ripristino.

    ![selezionare un percorso, un'istanza e un nome alternativi nel menu Configurazione di ripristino](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. Nel menu **Seleziona punto di ripristino** è possibile scegliere un punto di ripristino temporizzato o con registrazione completa e differenziale. Se si vuole eseguire il ripristino fino a un log specifico temporizzato, continuare con questo passaggio. Se si vuole eseguire il ripristino fino a un punto di ripristino completo o differenziale, andare al passaggio 3.

    ![menu di selezione punto di ripristino](./media/backup-azure-sql-database/recovery-point-menu.png)

    Il ripristino temporizzato è disponibile solo per i backup del log per i database con modello di recupero con registrazione completa e con registrazione minima delle operazioni bulk. Per eseguire il ripristino temporizzato fino a un punto specifico:

    1. Selezionare il ripristino di tipo **log (temporizzato)**.

        ![Scegliere il ripristino temporizzato](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. Sotto **Data/ora di ripristino** fare clic sull'icona del calendario per aprire il calendario. Le date in grassetto contengono punti di recupero e la data corrente è evidenziata. Selezionare una data sul calendario con punti di recupero. Non è possibile selezionare date senza punti di recupero.

        ![aprire il calendario](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Dopo aver selezionato una data, il grafico della sequenza temporale indica i punti di recupero disponibili in un intervallo continuo.

    3. Usando il grafico della sequenza temporale o la finestra di dialogo dell'ora, specificare un orario specifico per il punto di recupero e fare clic su **OK** per completare il passaggio del punto di ripristino.
    
       ![aprire il calendario](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        Il menu di **selezione del punto di ripristino** si chiude e si apre il menu della **configurazione avanzata**.

       ![menu configurazione avanzata](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Dal menu **Configurazione avanzata**:

        * Per mantenere il database non operativo dopo il ripristino, nel menu del **ripristino con NORECOVERY** selezionare **Abilitato**.
        * Per modificare il percorso di ripristino nel server di destinazione, specificare un nuovo percorso nella colonna **Destinazione**.
        * Fare clic su **OK** per approvare le impostazioni e chiudere **Configurazione avanzata**.

    5. Nel menu **Ripristina** fare clic su **Ripristina** per avviare il processo di ripristino. Nell'area delle notifiche è possibile rilevare lo stato di avanzamento. L'avanzamento può essere monitorato anche nei processi di ripristino per i database.

       ![menu configurazione avanzata](./media/backup-azure-sql-database/restore-job-notification.png)

3. Nel menu **Seleziona punto di ripristino** scegliere un punto di recupero. È possibile scegliere un ripristino temporizzato o un ripristino con registrazione completa e differenziale. Per eseguire un ripristino del log temporizzato, tornare al passaggio 2. Questo passaggio consente di ripristinare un punto specifico di un ripristino con registrazione completa o differenziale. Con questa opzione è possibile visualizzare tutti i punti di recupero completo e differenziale degli ultimi 30 giorni. Per visualizzare i punti di recupero risalenti a più di 30 giorni fa, fare clic su **Filtro** per aprire il menu **Filtrare i punti di ripristino**. Se si sceglie un punto di recupero differenziale, Backup di Azure prima ripristina il punto di recupero con registrazione completa appropriato e quindi applica il punto di recupero differenziale selezionato.

    ![menu di selezione punto di ripristino](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. Nel menu **Seleziona punto di ripristino** selezionare il ripristino con registrazione **completa e differenziale**.

       ![menu di selezione punto di ripristino](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        Viene visualizzato l'elenco dei punti di recupero disponibili.

    2. Nell'elenco dei punti di recupero selezionare un punto di recupero e fare clic su **OK** per completare la procedura dei punti di ripristino. 

        ![scegliere il punto di recupero con registrazione completa](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        Il menu **Punto di ripristino** si chiude e si apre il menu **Configurazione avanzata**.

        ![menu configurazione avanzata](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Dal menu **Configurazione avanzata**:

        * Per mantenere il database non operativo dopo il ripristino, nel menu del **ripristino con NORECOVERY** selezionare **Abilitato**. L'opzione di **ripristino con NORECOVERY** è disabilitata per impostazione predefinita.
        * Per modificare il percorso di ripristino nel server di destinazione, specificare un nuovo percorso nella colonna **Destinazione**.
        * Fare clic su **OK** per approvare le impostazioni e chiudere **Configurazione avanzata**.

    4. Nel menu **Ripristina** fare clic su **Ripristina** per avviare il processo di ripristino. Nell'area delle notifiche è possibile rilevare lo stato di avanzamento. L'avanzamento può essere monitorato anche nei processi di ripristino per i database.

       ![menu configurazione avanzata](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-and-overwrite-the-database"></a>Ripristinare e sovrascrivere il database

Questa procedura illustra in dettaglio il ripristino dei dati e la sovrascrittura del database. Se si vuole eseguire il ripristino in un percorso alternativo, passare alla sezione [Ripristinare in un percorso alternativo](backup-azure-sql-database.md#restore-to-an-alternate-location). Questa procedura presuppone che sia aperto l'insieme di credenziali di Servizi di ripristino e sia visualizzato il menu **Configurazione di ripristino** (vedere l'immagine seguente). In caso contrario, iniziare con la sezione [Ripristinare un database SQL](backup-azure-sql-database.md#restore-a-sql-database).

![fare clic su Configura per aprire il menu Configurazione di ripristino](./media/backup-azure-sql-database/restore-overwrite-db.png)

Il menu a discesa **Server** visualizza solo i server SQL registrati con l'insieme di credenziali di Servizi di ripristino. Se il server che si vuole usare non appare nell'elenco **Server**, vedere la sezione [Individuare i database di SQL Server](backup-azure-sql-database.md#discover-sql-server-databases) per trovarlo. Durante il processo di individuazione dei database, eventuali nuovi server vengono registrati nell'insieme di credenziali di Servizi di ripristino.

1. Nel menu **Configurazione di ripristino** selezionare **Sovrascrivi database** e fare clic su **OK** per completare la configurazione della destinazione. 

   ![fare clic su Sovrascrivi database](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

    Le finestre di dialogo **Server**, **Istanza** e **Nome del database ripristinato** non sono necessarie.

2. Nel menu **Seleziona punto di ripristino** è possibile scegliere un punto di ripristino temporizzato o con registrazione completa e differenziale. Per eseguire il ripristino di un log temporizzato, continuare con questo passaggio. Se si vuole eseguire il ripristino di un punto di ripristino con registrazione completa o differenziale, andare al passaggio 3.

    ![menu di selezione punto di ripristino](./media/backup-azure-sql-database/recovery-point-menu.png)

    Il ripristino temporizzato è disponibile solo per i backup del log per i database con modello di recupero con registrazione completa e con registrazione minima delle operazioni bulk. Per scegliere un ripristino temporizzato fino a un secondo specifico:

    1. Selezionare il ripristino di tipo **log (temporizzato)**.

        ![Scegliere il ripristino temporizzato](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. Sotto **Data/ora di ripristino** fare clic sull'icona del calendario per aprire il calendario. Le date in grassetto contengono punti di recupero e la data corrente è evidenziata. Selezionare una data sul calendario con punti di recupero. Non è possibile selezionare date senza punti di recupero.

        ![aprire il calendario](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Dopo aver selezionato una data, il grafico della sequenza temporale indica i punti di recupero disponibili.

    3. Usando il grafico della sequenza temporale o la finestra di dialogo dell'ora, specificare un orario specifico per il punto di recupero e fare clic su **OK** per completare il passaggio del punto di ripristino.
    
       ![aprire il calendario](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        Il menu di **selezione del punto di ripristino** si chiude e si apre il menu della **configurazione avanzata**.

       ![menu configurazione avanzata](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Dal menu **Configurazione avanzata**:

        * Per mantenere il database non operativo dopo il ripristino, nel menu del **ripristino con NORECOVERY** selezionare **Abilitato**.
        * Per modificare il percorso di ripristino nel server di destinazione, specificare un nuovo percorso nella colonna **Destinazione**.
        * Fare clic su **OK** per approvare le impostazioni e chiudere **Configurazione avanzata**.

    5. Nel menu **Ripristina** fare clic su **Ripristina** per avviare il processo di ripristino. Nell'area delle notifiche è possibile rilevare lo stato di avanzamento. L'avanzamento può essere monitorato anche nei processi di ripristino per i database.

       ![menu configurazione avanzata](./media/backup-azure-sql-database/restore-job-notification.png)

3. Nel menu **Seleziona punto di ripristino** scegliere un punto di recupero. È possibile scegliere un ripristino temporizzato o un ripristino con registrazione completa e differenziale. Per eseguire un ripristino del log temporizzato, tornare al passaggio 2. Questo passaggio consente di ripristinare un punto specifico di un ripristino con registrazione completa o differenziale. Con questa opzione è possibile visualizzare tutti i punti di recupero completo e differenziale degli ultimi 30 giorni. Per visualizzare i punti di recupero risalenti a più di 30 giorni fa, fare clic su **Filtro** per aprire il menu **Filtrare i punti di ripristino**. Se si sceglie un punto di recupero differenziale, Backup di Azure prima ripristina il punto di recupero con registrazione completa appropriato e quindi applica il punto di recupero differenziale selezionato.

    ![menu di selezione punto di ripristino](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. Nel menu **Seleziona punto di ripristino** selezionare il ripristino con registrazione **completa e differenziale**.

       ![menu di selezione punto di ripristino](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        Viene visualizzato l'elenco dei punti di recupero disponibili.

    2. Nell'elenco dei punti di recupero selezionare un punto di recupero e fare clic su **OK** per completare la procedura dei punti di ripristino. 

        ![scegliere il punto di recupero con registrazione completa](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        Il menu **Punto di ripristino** si chiude e si apre il menu **Configurazione avanzata**.

        ![menu configurazione avanzata](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Dal menu **Configurazione avanzata**:

        * Per mantenere il database non operativo dopo il ripristino, nel menu del **ripristino con NORECOVERY** selezionare **Abilitato**. L'opzione di **ripristino con NORECOVERY** è disabilitata per impostazione predefinita.
        * Per modificare il percorso di ripristino nel server di destinazione, specificare un nuovo percorso nella colonna **Destinazione**.
        * Fare clic su **OK** per approvare le impostazioni e chiudere **Configurazione avanzata**.

    4. Nel menu **Ripristina** fare clic su **Ripristina** per avviare il processo di ripristino. Nell'area delle notifiche è possibile rilevare lo stato di avanzamento. L'avanzamento può essere monitorato anche nei processi di ripristino per i database.

       ![menu configurazione avanzata](./media/backup-azure-sql-database/restore-job-notification.png)


## <a name="manage-azure-backup-operations-for-sql-on-azure-vms"></a>Gestire le operazioni di Backup di Azure per SQL nelle macchine virtuali di Azure

Questa sezione contiene informazioni sulle varie operazioni di gestione di Backup di Azure disponibili per SQL nelle macchine virtuali di Azure. Sono disponibili le seguenti operazioni di alto livello:

* Monitoraggio dei processi
* Avvisi di backup
* Interruzione della protezione per un database SQL
* Ripresa della protezione per un database SQL
* Attivazione di un processo di backup ad hoc
* Annullamento della registrazione di un server SQL

### <a name="monitor-jobs"></a>Monitoraggio dei processi
Poiché Backup di Azure è una soluzione di classe Enterprise, offre notifiche e avvisi di backup avanzati per rilevare eventuali errori (vedere la sezione Avvisi di backup di seguito). Se si intende monitorare i processi specifici, è possibile usare una delle opzioni seguenti in base ai requisiti:

#### <a name="use-azure-portal-for-all-adhoc-operations"></a>Usare il portale di Azure per tutte le operazioni ad hoc
Backup di Azure consente di visualizzare tutti i processi attivati manualmente oppure i processi ad hoc nel portale dei processi di backup. I processi disponibili nel portale includono tutte le operazioni di configurazione del backup, le operazioni di backup attivate manualmente, le operazioni di ripristino, le operazioni di registrazione e individuazione dei database e le operazioni di arresto del backup. 
![menu configurazione avanzata](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Tutti i processi di backup pianificati, inclusi i backup completi, differenziali e del logo, non vengono visualizzati nel portale e possono essere monitorati tramite SQL Server Management Studio, come descritto di seguito.
>

#### <a name="use-sql-server-management-studio-for-backup-jobs"></a>Usare SQL Server Management Studio per i processi di backup
Backup di Azure usa le API native di SQL per tutte le operazioni di backup. Con le API native, è possibile recuperare tutte le informazioni sui processi dalla [tabella dei set di backup di SQL](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) nel database msdb.

L'esempio seguente è una query per recuperare tutti i processi di backup per un database denominato **DB1**. Personalizzare la query per eseguire un monitoraggio più avanzato.
```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  
 
```

### <a name="backup-alerts"></a>Avvisi di backup

Con i backup del log eseguiti ogni 15 minuti, monitorare i processi di backup a volte può essere noioso. Backup di Azure consente di evitare questa situazione grazie ad avvisi di posta elettronica attivati in caso di errori durante il backup. Gli avvisi vengono consolidati a livello di database in base al codice di errore. Ad esempio, se in un database si verificano diversi errori di backup, anziché ricevere un avviso per ogni errore, si riceve un messaggio di posta elettronica per il primo errore. È quindi possibile accedere al portale di Azure per monitorare gli eventuali errori successivi per quel database. 

Per monitorare gli avvisi di backup:

1. Accedere alla propria sottoscrizione di Azure nel [portale di Azure](https://portal.azure.com).

2. Aprire l'insieme di credenziali di Servizi di ripristino registrato con la macchina virtuale SQL.

3. Nel menu dell'insieme di credenziali di Servizi di ripristino selezionare **Avvisi ed eventi**. 

   ![menu configurazione avanzata](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. Nel menu **Avvisi ed eventi** selezionare **Avvisi di backup** per visualizzare l'elenco degli avvisi.

   ![menu configurazione avanzata](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

### <a name="stop-protection-on-a-sql-server-database"></a>Interrompere la protezione di un database di SQL Server

Se si interrompe la protezione di un database di SQL Server, Backup di Azure chiede se si vogliono conservare i punti di recupero. Per interrompere la protezione del database SQL è possibile procedere in due modi:

* Interrompere tutti i processi di backup futuri ed eliminare tutti i punti di recupero,
* Interrompere tutti i processi di backup futuri mantenendo però i punti di recupero 

Lasciare i punti di recupera comporta un costo poiché i punti di recupero per SQL implicano un addebito per l'esecuzione dell'istanza SQL protetta, oltre alla memoria usata. Per altre informazioni sui prezzi di Backup di Azure per SQL, vedere la [pagina dei prezzi di Backup di Azure](https://azure.microsoft.com/pricing/details/backup/). Per interrompere la protezione per il database:

1. Aprire l'insieme di credenziali di Servizi di ripristino registrato con la macchina virtuale SQL.

2. Nel dashboard dell'insieme di credenziali selezionare **Utilizzo** sotto Elementi di backup per aprire il menu Elementi di backup.

    ![Fare clic su + Backup per aprire il menu Obiettivo di backup](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. Nel menu **Elementi di backup** selezionare il tipo di gestione dei backup, **SQL nella macchina virtuale di Azure**. 

    ![Fare clic su + Backup per aprire il menu Obiettivo di backup](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    L'elenco degli elementi di backup si adegua in modo da visualizzare l'elenco di database SQL. 

4. Selezionare il database per cui si vuole interrompere la protezione dall'elenco dei database SQL.

    ![selezionare SQL nella macchina virtuale di Azure dall'elenco](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Quando si seleziona il database, viene aperto il relativo menu. 

5. Nel menu del database selezionato fare clic su **Interrompi backup** per interrompere la protezione del database.

    ![selezionare ripristino del database](./media/backup-azure-sql-database/stop-db-button.png)

    Viene visualizzato il menu **Interrompi backup**.

6. Nel menu **Interrompi backup** scegliere se conservare o eliminare i dati di backup. Se necessario, è possibile indicare un motivo per l'interruzione della protezione e aggiungere un commento.

    ![selezionare ripristino del database](./media/backup-azure-sql-database/stop-backup-button.png)

7. Fare clic su **Interrompi backup** per interrompere la protezione per il database. 

### <a name="resume-protection-for-a-sql-database"></a>Riprendere la protezione per un database SQL

Se è stata selezionata l'opzione **Conserva i dati di backup** quando è stata interrotta la protezione per il database SQL, è possibile riprendere la protezione. Se i dati di backup non vengono conservati, non sarà possibile riprendere la protezione. 

1. Per riprendere la protezione del database SQL, aprire l'elemento di backup e fare clic su **Riprendi backup**.

    ![riprendere la protezione del database](./media/backup-azure-sql-database/resume-backup-button.png)

   Viene visualizzato il menu Criteri di backup.

2. Selezionare un criterio dal menu **Criteri di backup** e fare clic su **Salva**.

### <a name="trigger-an-adhoc-backup"></a>Attivare un backup ad hoc

È possibile attivare un backup ad hoc in qualsiasi momento. Esistono quattro tipi di backup ad hoc. Per informazioni dettagliate su ogni tipo, vedere l'articolo relativo ai [tipi di backup per SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017#types-of-backups).

* Backup completo
* Backup completo solo copia
* Backup differenziale
* Backup del log

### <a name="unregister-a-sql-server"></a>Annullare la registrazione di un server SQL

Per annullare la registrazione di un server SQL dopo aver rimosso la protezione, ma prima di eliminare l'insieme di credenziali

1. Aprire l'insieme di credenziali di Servizi di ripristino registrato con la macchina virtuale SQL.

2. Nella sezione **Gestisci** del menu dell'insieme di credenziali fare clic su **Infrastruttura di backup**.  

   ![riprendere la protezione del database](./media/backup-azure-sql-database/backup-infrastructure-button.png)

3. Nella sezione dei **server di gestione** fare clic su **Server protetti**.

   ![riprendere la protezione del database](./media/backup-azure-sql-database/protected-servers.png)

    Si apre il menu Server protetti. 

4. Nel menu **Server protetti** selezionare il server di cui si vuole annullare la registrazione. Per eliminare l'insieme di credenziali, è necessario annullare la registrazione di tutti i server.

5. Nel menu Server protetti, fare clic con il pulsante destro del mouse sul server protetto e selezionare **Elimina**. 

   ![riprendere la protezione del database](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="sql-database-backup-faq"></a>Domande frequenti sul backup del database SQL

Nella sezione seguente sono disponibili informazioni aggiuntive sul backup del database SQL.

### <a name="can-i-throttle-the-speed-of-the-sql-backup-policy-so-it-minimizes-impact-on-the-sql-server"></a>È possibile limitare la velocità dei criteri di backup SQL in modo da ridurre al minimo l'impatto sul server SQL?

Sì, è possibile limitare la frequenza con cui vengono eseguiti i criteri di backup. Per modificare l'impostazione:

1. Nella cartella `C:\Program Files\Azure Workload Backup\bin` di SQL Server aprire **TaskThrottlerSettings.json**.

2. Nel file **TaskThrottlerSettings.json** impostare **DefaultBackupTasksThreshold** su un valore inferiore, ad esempio 5.

3. Salvare le modifiche apportate e chiudere il file.

4. In SQL Server aprire Gestione attività e riavviare il **servizio coordinatore del carico di lavoro di Backup di Azure**.

### <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>È possibile eseguire un backup completo da una replica secondaria?

No, questa funzionalità non è supportata.

### <a name="do-successful-backup-jobs-create-alerts"></a>I processi di backup con esito positivo determinano la creazione di avvisi?

No. I processi di backup con esito positivo non generano avvisi. Gli avvisi vengono inviati solo per i processi di backup con esito negativo.

### <a name="are-scheduled-backup-job-details-shown-in-the-jobs-menu"></a>I dettagli dei processi di backup pianificati vengono visualizzati nel menu Processi?

No. Il menu Processi mostra i dettagli dei processi ad hoc, ma non mostra i processi di backup pianificati. Se un processo di backup pianificato ha esito positivo, tutti i dettagli sono disponibili negli avvisi relativi. Se si intende monitorare tutti i processo di backup ad hoc e pianificati, [usare SQL Server Management Studio](backup-azure-sql-database.md#use-sql-server-management-studio-for-backup-jobs).

### <a name="if-i-select-a-sql-server-will-future-databases-automatically-be-added"></a>Se si seleziona un server SQL, vengono aggiunti automaticamente database futuri?

No. Quando si configura la protezione per un server SQL, se si seleziona la casella di controllo a livello di server vengono aggiunti tutti i database. Se si aggiungono database al server SQL dopo la configurazione della protezione, è necessario aggiungere manualmente i nuovi database per proteggerli. I database non sono inclusi automaticamente nella protezione configurata.

### <a name="if-i-change-the-recovery-model-how-do-i-restart-protection"></a>Se si modifica il modello di recupero, come è possibile riavviare la protezione?

Se si modifica il modello di recupero, attivare un backup completo e i backup del log verranno avviati come previsto.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Backup di Azure, vedere l'esempio PowerShell per il backup di macchine virtuali crittografate.

> [!div class="nextstepaction"]
> [Back up encrypted VM](./scripts/backup-powershell-sample-backup-encrypted-vm.md) (Eseguire il backup di VM crittografate)
