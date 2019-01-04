---
title: Eseguire il backup dei database di SQL Server in Azure | Microsoft Docs
description: Questo tutorial spiega come eseguire il backup di SQL Server in Azure. L'articolo spiega inoltre il recupero di SQL Server.
services: backup
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
editor: ''
keywords: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/02/2018
ms.author: anuragm
ms.custom: ''
ms.openlocfilehash: e2e6742fb3eda0523c7333451e836beb069e57ca
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410364"
---
# <a name="back-up-sql-server-databases-to-azure"></a>Eseguire un backup dei database SQL Server in Azure

I database di SQL Server sono carichi di lavoro critici che richiedono un obiettivo del punto di ripristino (RPO) basso e la conservazione a lungo termine. Backup di Azure offre una soluzione di backup per SQL Server che non richiede un'infrastruttura, ovvero nessun server di backup complesso, nessun agente di gestione né un archivio backup da gestire. Backup di Azure offre una gestione centralizzata dei backup in tutti i server che eseguono SQL Server o anche in carichi di lavoro diversi.

Contenuto dell'articolo:

> [!div class="checklist"]
> * Prerequisiti per l'esecuzione del backup di un’istanza SQL Server in Azure.
> * Come creare e usare un insieme di credenziali dei Servizi di ripristino.
> * Come configurare i backup dei database di SQL Server.
> * Come impostare un criterio di backup, o di conservazione, per i punti di recupero.
> * Come recuperare il database.

Prima di iniziare le procedure descritte in questo articolo, verificare che un’istanza SQL Server sia in esecuzione in Azure. [Usare le macchine virtuali del marketplace per SQL per creare rapidamente un’istanza SQL Server](../sql-database/sql-database-get-started-portal.md).

## <a name="public-preview-limitations"></a>Limiti dell'anteprima pubblica

Gli elementi seguenti sono limitazioni note per l'anteprima pubblica:

- La macchina virtuale (VM) SQL richiede la connettività Internet per accedere agli indirizzi IP pubblici di Azure. Per maggiori dettagli, vedere [Stabilire la connettività di rete](backup-azure-sql-database.md#establish-network-connectivity).
- È possibile proteggere fino a 2,000 database SQL in un insieme di credenziali di Servizi di ripristino. I database SQL aggiuntivi devono essere archiviati in un insieme di credenziali di Servizi di ripristino separato.
- [I backup dei gruppi di disponibilità distribuiti](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017) presentano limitazioni.
- Le istanze del cluster di failover Always On di SQL Server (FCI) non sono supportate.
- Usare il portale di Azure per configurare Backup di Azure per proteggere i database di SQL Server. Azure PowerShell, l’interfaccia della riga di comando di Azure e le API REST non sono attualmente supportate.
- Le operazioni di backup/ripristino per i database mirror, gli snapshot di database e i database in istanze di cluster di failover non sono supportate.
- I database con un numero elevato di file non possono essere protetti. Il numero massimo di file supportati non è un numero molto deterministico, perché dipende non solo dal numero di file ma anche dalla lunghezza del percorso dei file. Questi casi sono tuttavia meno frequenti. Microsoft sta creando una soluzione per gestire situazioni di questo tipo.

Per ulteriori dettagli sugli scenari supportati e non supportati, vedere la [sezione delle domande frequenti](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#faq).

## <a name="support-for-azure-geos"></a>Supporto per aree geografiche di Azure

Backup di Azure è supportato per le seguenti aree geografiche:

- Australia sud-orientale (ASE)
- Brasile meridionale (BRS)
- Canada centrale (CNC)
- Canada orientale (CE)
- Stati Uniti centrali (CUS)
- Asia orientale (EA)
- Australia orientale (AE)
- Stati Uniti orientali (EUS)
- Stati Uniti orientali 2 (EUS2)
- India centrale (INC)
- India meridionale (INS)
- Giappone orientale (JPE)
- Giappone occidentale (JPW)
- Corea del Sud centrale (KRC)
- Corea del Sud meridionale (KRS)
- Stati Uniti centro-settentrionali (NCUS)
- Europa settentrionale (NE)
- Stati Uniti a centro-meridionali (SCUS)
- Asia sud-orientale (SEA)
- Regno Unito meridionale (UKS)
- Regno Unito occidentale (UKW)
- Stati Uniti centro occidentali (WCUS)
- Europa occidentale (OE)
- Stati Uniti occidentali (WUS)
- Stati Uniti occidentali 2 (WUS 2)

## <a name="support-for-operating-systems-and-sql-server-versions"></a>Supporto per versioni di SQL Server e sistemi operativi

In questa sezione viene descritto il supporto di Backup di Azure per i sistemi operativi e le versioni di SQL Server. Sono supportate macchine virtuali di Azure e del marketplace SQL e macchine virtuali non del marketplace (in cui SQL Server è installato manualmente).

### <a name="supported-operating-systems"></a>Sistemi operativi supportati

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

Linux non è attualmente supportato.

### <a name="supported-sql-server-versions-and-editions"></a>Versioni ed edizioni di SQL Server supportate

- SQL Server 2012 Enterprise, Standard, Web, Developer, Express
- SQL Server 2014 Enterprise, Standard, Web, Developer, Express
- SQL Server 2016 Enterprise, Standard, Web, Developer, Express
- SQL Server 2017 Enterprise, Standard, Web, Developer, Express

## <a name="prerequisites"></a>Prerequisiti

Prima di eseguire il backup del database di SQL Server, verificare le condizioni seguenti:

- Identificare o [creare un insieme di credenziali di Servizi di ripristino](backup-azure-sql-database.md#create-a-recovery-services-vault) nella stessa area, o nelle stesse impostazioni locali, della macchina virtuale che ospita l’istanza SQL Server.
- [Verificare le autorizzazioni per la macchina virtuale](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms) necessarie per eseguire il backup dei database SQL.
- Verificare che [la macchina virtuale SQL disponga della connettività di rete](backup-azure-sql-database.md#establish-network-connectivity).
- Per una corretta esecuzione dei backup, controllare se i nomi dei database SQL sono conformi alle [linee guida per la denominazione](backup-azure-sql-database.md#sql-database-naming-guidelines-for-azure-backup) per Backup di Azure.

> [!NOTE]
> È possibile avere solo una soluzione di backup alla volta per eseguire il backup dei database di SQL Server. Disabilitare tutti gli altri backup SQL prima di usare questa funzionalità; in caso contrario, i backup interferiranno e non verranno eseguiti correttamente. È possibile abilitare Backup di Azure per la macchina virtuale IaaS insieme al backup di SQL senza alcun conflitto.
>

Se queste condizioni esistono nell'ambiente in uso, passare a [Configurare il backup per database di SQL Server](backup-azure-sql-database.md#configure-backup-for-sql-server-databases). Se uno dei prerequisiti non esiste, continuare a leggere qui.


## <a name="establish-network-connectivity"></a>Stabilire la connettività di rete

Per tutte le operazioni, la macchina virtuale SQL richiede la connettività agli indirizzi IP pubblici di Azure. Le operazioni della macchina virtuale SQL, ad esempio individuazione dei database, configurazione dei backup, pianificazione dei backup, ripristino dei punti di recupero e così via, non riescono in assenza di connessioni agli indirizzi IP pubblici. Eseguire una delle operazioni seguenti per specificare un percorso chiaro per il traffico di backup:

- Aggiungere gli intervalli IP dei data center di Azure all'elenco elementi consentiti: a tale scopo, usare la [pagina dell'Area download per informazioni dettagliate su intervalli IP e istruzioni](https://www.microsoft.com/download/details.aspx?id=41653).
- Distribuire un server proxy HTTP per il routing del traffico: quando si esegue il backup di un database SQL in una macchina virtuale, l'estensione di backup nella macchina virtuale usa le API HTTPS per inviare i comandi di gestione a Backup di Azure e i dati ad Archiviazione di Azure. L'estensione di backup usa anche Azure Active Directory (Azure AD) per l'autenticazione. Eseguire il routing del traffico di estensione per il backup di questi tre servizi attraverso il proxy HTTP. L'unico componente configurato per l'accesso a Internet pubblico è l'estensione.

I compromessi tra le opzioni disponibili sono: gestibilità, controllo granulare e costi.

> [!NOTE]
> I tag di servizio per Backup di Azure devono essere disponibili in base alla disponibilità generale.
>

| Opzione | Vantaggi | Svantaggi: |
| ------ | ---------- | ------------- |
| Aggiungere gli intervalli IP all'elenco elementi consentiti  | Senza costi aggiuntivi. <br/> Per aprire l'accesso in un gruppo di sicurezza di rete, usare il cmdlet **Set-AzureNetworkSecurityRule**. | Complessità di gestione a causa della variazione nel tempo degli intervalli IP interessati. <br/>Possibilità di accesso a tutto l'ambiente Azure, non solo al servizio di Archiviazione di Azure.|
| Usare un proxy HTTP   | Possibilità di controllo granulare nel proxy sugli URL di archiviazione. <br/>Singolo punto di accesso Internet alle VM. <br/> Non è soggetto alle modifiche degli indirizzi IP di Azure. | Costi aggiuntivi per l'esecuzione di una macchina virtuale con il software proxy. |

## <a name="set-permissions-for-non-marketplace-sql-vms"></a>Impostare le autorizzazioni per le macchine virtuali SQL non del marketplace

Per eseguire il backup di una macchina virtuale, Backup di Azure richiede l'installazione dell'estensione **AzureBackupWindowsWorkload**. Se si usano macchine virtuali di Azure Marketplace, continuare fino a [Individuare database di SQL Server](backup-azure-sql-database.md#discover-sql-server-databases). Se la macchina virtuale che ospita i database SQL non è stata creata da Azure Marketplace, completare la procedura seguente per installare l'estensione e impostare le autorizzazioni appropriate. Oltre all'estensione **AzureBackupWindowsWorkload**, Backup di Azure richiede privilegi sysadmin SQL per proteggere i database SQL. Per individuare i database nella macchina virtuale, Backup di Azure crea l'account **NT Service\AzureWLBackupPluginSvc**. Questo account viene usato per il backup e il ripristino e deve disporre dell'autorizzazione sysadmin SQL. Inoltre, Backup di Azure userà l'account **NT AUTHORITY\SYSTEM** per l'individuazione o l'interrogazione dei database e pertanto questo account deve essere un account di accesso pubblico in SQL.

Per configurare le autorizzazioni:

1. Nel [portale di Azure](https://portal.azure.com) aprire l'insieme di credenziali di Servizi di ripristino usato per proteggere i database SQL.

2. Nel dashboard di **Insiemi di credenziali dei Servizi di ripristino** selezionare **Backup**. Si apre il menu **Obiettivo del backup**.

   ![Selezionare Backup per aprire il menu Obiettivo di backup](./media/backup-azure-sql-database/open-backup-menu.png)

3. Nel menu **Obiettivo di backup** impostare **Posizione di esecuzione del carico di lavoro** sul valore predefinito: **Azure**.

4. Espandere l’elenco a discesa **Elementi di cui eseguire il backup** e selezionare **SQL Server in una macchina virtuale di Azure**.

    ![Selezionare SQL Server in macchine virtuali di Azure per il backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Il menu **Obiettivo di backup** visualizza due passaggi: **Individuare i database nelle macchine virtuali**  e **Configurare il servizio Backup**. La fase **Individuare i database nelle macchine virtuali** avvia una ricerca per le macchine virtuali di Azure.

    ![Esaminare i due passaggi di Obiettivo di backup](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

5. In **Individuare i database nelle macchine virtuali**, selezionare **Avvia individuazione** per cercare le macchine virtuali non protette nella sottoscrizione. Cercare tutte le macchine virtuali può richiedere un po' di tempo. Il tempo di ricerca dipende dal numero di macchine virtuali non protette presenti nella sottoscrizione.

    ![Il backup è in sospeso durante la ricerca dei database nelle macchine virtuali](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Selezionare dall'elenco di macchine virtuali la macchina contenente il database SQL di cui si deve eseguire il backup e selezionare quindi **Individua database**.

    Il processo di individuazione consente di installare l'estensione **AzureBackupWindowsWorkload** nella macchina virtuale. L'estensione consente al servizio Backup di Azure di comunicare con la macchina virtuale in modo che esegua il backup dei database SQL. Dopo l'installazione dell'estensione, Backup di Azure crea l'account del servizio virtuale di Windows, **NT Service\AzureWLBackupPluginSvc**, nella macchina virtuale. L'account del servizio virtuale richiede l'autorizzazione sysadmin SQL. Durante il processo di installazione dell'account di servizio virtuale, se si riceve l'errore `UserErrorSQLNoSysadminMembership`, vedere [Correzione delle autorizzazioni sysadmin SQL](backup-azure-sql-database.md#fix-sql-sysadmin-permissions).

    L'area delle **notifiche** mostra lo stato di avanzamento dell'individuazione dei database. Il completamento del processo può richiedere un po' di tempo. Il tempo di processo dipende dal numero di database presenti sulla macchina virtuale. Quando i database selezionati sono stati individuati, viene visualizzato un messaggio di conferma.

    ![Messaggio di distribuzione riuscita](./media/backup-azure-sql-database/notifications-db-discovered.png)

Dopo aver associato il database all'insieme di credenziali di Servizi di ripristino, il passaggio successivo è [configurare il processo di backup](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

### <a name="fix-sql-sysadmin-permissions"></a>Correzione delle autorizzazioni sysadmin SQL

Durante il processo di installazione, se viene visualizzato l'errore `UserErrorSQLNoSysadminMembership`, usare un account con le autorizzazioni sysadmin SQL Server per accedere a SQL Server Management Studio (SSMS). A meno che non siano necessarie autorizzazioni speciali, l'autenticazione di Windows dovrebbe funzionare.

1. In SQL Server aprire la cartella Sicurezza/Account di accesso.

    ![Aprire la cartella Sicurezza/Account di accesso per vedere gli account](./media/backup-azure-sql-database/security-login-list.png)

2. Fare clic con il pulsante destro del mouse sulla cartella Account di accesso e scegliere **Nuovo account accesso**. Nella finestra di dialogo **Account di accesso - Nuovo** selezionare **Cerca**.

    ![Nella finestra di dialogo Account di accesso - Nuovo selezionare Cerca](./media/backup-azure-sql-database/new-login-search.png)

3. L'account di servizio virtuale di Windows **NT Service\AzureWLBackupPluginSvc** è stato creato durante la fase di registrazione della macchina virtuale e di rilevamento SQL. Immettere il nome dell'account, come illustrato nella casella **Immettere il nome dell'oggetto da selezionare**. Selezionare **Controlla nomi** per risolvere il nome.

    ![Selezionare Controlla nomi per risolvere il nome del servizio sconosciuto](./media/backup-azure-sql-database/check-name.png)

4. Selezionare **OK** per chiudere la finestra di dialogo.

5. Nella casella **Ruoli del server** verificare che sia selezionato il ruolo **sysadmin**. Selezionare **OK** per chiudere la finestra di dialogo.

    ![Verificare che sia selezionato il ruolo del server sysadmin](./media/backup-azure-sql-database/sysadmin-server-role.png)

    Le autorizzazioni necessarie a questo punto devono essere presenti.

6. Anche se è stato corretto l'errore relativo alle autorizzazioni, è necessario associare il database all'insieme di credenziali di Servizi di ripristino. Nell'elenco dei **server protetti** del portale di Azure fare clic con il pulsante destro del mouse sul server in stato di errore e selezionare **Individua di nuovo i database**.

    ![Verificare che il server abbia le autorizzazioni appropriate](./media/backup-azure-sql-database/check-erroneous-server.png)

    L'area delle **notifiche** mostra lo stato di avanzamento dell'individuazione dei database. Il completamento del processo può richiedere un po' di tempo. Il tempo di processo dipende dal numero di database presenti sulla macchina virtuale. Quando i database selezionati sono stati individuati, viene visualizzato un messaggio di conferma.

    ![Messaggio di distribuzione riuscita](./media/backup-azure-sql-database/notifications-db-discovered.png)

Dopo aver associato il database all'insieme di credenziali di Servizi di ripristino, il passaggio successivo è [configurare il processo di backup](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

## <a name="sql-database-naming-guidelines-for-azure-backup"></a>Linee guida per la denominazione di database SQL per Backup di Azure
Per una corretta esecuzione dei backup con Backup di Azure per SQL Server in macchine virtuali IaaS, evitare di usare i caratteri seguenti per i nomi dei database:

  * Spazi finali/iniziali
  * Punto esclamativo (!) finale

È previsto l'uso di alias per i caratteri non supportati dalle tabelle di Azure, ma è consigliabile evitare anche tali caratteri. Per altre informazioni, vedere questo [articolo](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Individuare i database SQL Server

Backup di Azure consente di individuare tutti i database in un'istanza di SQL Server. È possibile proteggere i database in base ai requisiti di backup. Usare la procedura seguente per identificare la macchina virtuale che ospita i database SQL. Dopo aver identificato la macchina virtuale, Backup di Azure installa un'estensione semplificata che consente di individuare i database di SQL Server.

1. Accedere alla propria sottoscrizione nel [portale di Azure](https://portal.azure.com/).

2. Nel menu a sinistra selezionare **Tutti i servizi**.

    ![Selezionare tutti i servizi](./media/backup-azure-sql-database/click-all-services.png) <br/>

3. Nella finestra di dialogo **Tutti i servizi** inserire **Servizi di ripristino**. Non appena si inizia a digitare, l'elenco delle risorse viene filtrato in base all'input. Selezionare **Insiemi di credenziali di Servizi di ripristino** dall’elenco.

  ![Inserire e selezionare le insiemi di credenziali di Servizi di ripristino](./media/backup-azure-sql-database/all-services.png) <br/>

    Viene visualizzato l'elenco degli insiemi di credenziali di Servizi di ripristino.

4. Nell'elenco degli insiemi di credenziali di Servizi di ripristino selezionare l'insieme di credenziali da usare per proteggere i database SQL.

5. Nel dashboard di **Insiemi di credenziali dei Servizi di ripristino** selezionare **Backup**. Si apre il menu **Obiettivo del backup**.

   ![Selezionare Backup per aprire il menu Obiettivo di backup](./media/backup-azure-sql-database/open-backup-menu.png)

6. Nel menu **Obiettivo di backup** impostare **Posizione di esecuzione del carico di lavoro** sul valore predefinito: **Azure**.

7. Espandere l’elenco a discesa **Elementi di cui eseguire il backup** e selezionare **SQL Server in una macchina virtuale di Azure**.

    ![Selezionare SQL Server in macchine virtuali di Azure per il backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Il menu **Obiettivo di backup** visualizza due passaggi: **Individuare i database nelle macchine virtuali**  e **Configurare il servizio Backup**.

    ![Esaminare i due passaggi di Obiettivo di backup](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

8. In **Individuare i database nelle macchine virtuali**, selezionare **Avvia individuazione** per cercare le macchine virtuali non protette nella sottoscrizione. Cercare in tutte le macchine virtuali può richiedere un po' di tempo. Il tempo di ricerca dipende dal numero di macchine virtuali non protette presenti nella sottoscrizione.

    ![Il backup è in sospeso durante la ricerca dei database nelle macchine virtuali](./media/backup-azure-sql-database/discovering-sql-databases.png)

    Dopo che una macchina virtuale non protetta viene rilevata, viene visualizzata nell'elenco. Più macchine virtuali possono avere lo stesso nome. Tuttavia, le macchine virtuali con lo stesso nome appartengono a gruppi di risorse diversi. Le macchine virtuali non protette sono elencate in base al nome di macchina virtuale e al gruppo di risorse. Se, contrariamente a quanto previsto, una macchina virtuale non è indicata, verificare se è già protetta da un insieme di credenziali.

9. Selezionare dall'elenco di macchine virtuali la macchina contenente il database SQL di cui si deve eseguire il backup e selezionare quindi **Individua database**.

    Backup di Azure individua tutti i database SQL nella macchina virtuale. Per informazioni su cosa accade durante la fase di individuazione di database, vedere [Operazioni in background](backup-azure-sql-database.md#background-operations). Dopo aver individuato i database SQL, si è pronti a [configurare il processo di backup](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

### <a name="background-operations"></a>Operazioni in background

Quando si usa lo strumento di **individuazione dei database** Backup di Azure esegue queste operazioni in background:

- Registrare la macchina virtuale con l'insieme di credenziali di Servizi di ripristino per il backup del carico di lavoro. Per tutti i database nella macchina virtuale registrata il backup può essere eseguito solo per questo insieme di credenziali di Servizi di ripristino.

- Installare l'estensione **AzureBackupWindowsWorkload** nella macchina virtuale. L’esecuzione del backup di un database SQL è una soluzione senza agente. L'estensione viene installata nella macchina virtuale e nessun agente viene installato nel database SQL.

- Creare l'account del servizio, **NT Service\AzureWLBackupPluginSvc**, nella macchina virtuale. Tutte le operazioni di backup e ripristino usano l'account del servizio. Per **NT Service\AzureWLBackupPluginSvc** sono necessarie le autorizzazioni sysadmin SQL. Tutte le macchine virtuali SQL Marketplace sono fornite con **SqlIaaSExtension** installato. L'estensione **AzureBackupWindowsWorkload** utilizza l'estensione **SQLIaaSExtension** per ottenere automaticamente le autorizzazioni richieste. Se nella macchina virtuale non è installato **SqlIaaSExtension**, l'operazione di individuazione dei database non riesce e viene visualizzato il messaggio di errore `UserErrorSQLNoSysAdminMembership`. Per aggiungere l'autorizzazione sysadmin per il backup, seguire le istruzioni in [Impostazione di autorizzazioni di Backup di Azure per le macchine virtuali SQL non del marketplace](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms).

    ![Selezionare la macchina virtuale e il database](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup-for-sql-server-databases"></a>Configurare il backup per i database di SQL Server

Backup di Azure offre servizi di gestione per proteggere i database di SQL Server e gestire i processi di backup. La funzioni di gestione e monitoraggio dipendono dall'insieme di credenziali di Servizi di ripristino.

> [!NOTE]
> È possibile avere solo una soluzione di backup alla volta per eseguire il backup dei database di SQL Server. Disabilitare tutti gli altri backup SQL prima di usare questa funzionalità; in caso contrario, i backup interferiranno e non verranno eseguiti correttamente. È possibile abilitare Backup di Azure per la macchina virtuale IaaS insieme al backup di SQL senza alcun conflitto.
>

Per configurare la protezione per un database SQL:

1. Aprire l'insieme di credenziali di Servizi di ripristino registrato con la macchina virtuale SQL.

2. Nel dashboard di **Insiemi di credenziali dei Servizi di ripristino** selezionare **Backup**. Si apre il menu **Obiettivo del backup**.

   ![Selezionare Backup per aprire il menu Obiettivo di backup](./media/backup-azure-sql-database/open-backup-menu.png)

3. Nel menu **Obiettivo di backup** impostare **Posizione di esecuzione del carico di lavoro** sul valore predefinito: **Azure**.

4. Espandere l’elenco a discesa **Elementi di cui eseguire il backup** e selezionare **SQL Server in una macchina virtuale di Azure**.

    ![Selezionare SQL Server in macchine virtuali di Azure per il backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Il menu **Obiettivo di backup** visualizza due passaggi: **Individuare i database nelle macchine virtuali**  e **Configurare il servizio Backup**.

    Se i passaggi di questo articolo sono stati eseguiti in ordine, l'utente ha individuato le macchine virtuali non protette e questo insieme di credenziali è registrato con una macchina virtuale. A questo punto si è pronti a configurare la protezione per i database SQL.

5. Nel menu **Obiettivo di backup** selezionare **Configura backup**.

    ![Selezionare Configurare il backup](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    Il servizio Backup di Azure visualizza tutte le istanze di SQL Server con database autonomi e gruppi di disponibilità AlwaysOn di SQL Server. Per visualizzare i database autonomi nell'istanza di SQL Server, fare clic sulla freccia di espansione alla sinistra del nome dell'istanza. In modo analogo, selezionare la freccia di espansione a sinistra del gruppo di disponibilità AlwaysOn per visualizzare l'elenco dei database. L'immagine seguente è un esempio di un'istanza autonoma e un gruppo di disponibilità AlwaysOn.

      ![Visualizzazione di tutte le istanze di SQL Server con i database autonomi](./media/backup-azure-sql-database/list-of-sql-databases.png)

6. Nell'elenco dei database selezionare tutti i database da proteggere e fare clic su **OK**.

    ![Protezione del database](./media/backup-azure-sql-database/select-database-to-protect.png)

    È possibile selezionare fino a 50 database alla volta. Se i database da proteggere sono più di 50, eseguire più volte l'operazione. Dopo aver protetto i primi 50 database, ripetere questo passaggio per proteggere il set successivo di database.

    > [!Note]
    > Per ottimizzare i carichi di backup, Backup di Azure suddivide i processi di backup di grandi dimensioni in più batch. Il numero massimo di database in un solo processo di backup è 50.
    >

      In alternativa, è possibile abilitare la [protezione automatica](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) sull'intera istanza o sul gruppo di disponibilità AlwaysOn selezionando l'opzione **ON** nell'elenco a discesa corrispondente nella colonna **AUTOPROTECT**. La funzionalità di [protezione automatica](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) non solo abilita la protezione per tutti i database esistenti in un'unica operazione, ma protegge anche automaticamente i nuovi database che verranno aggiunti in futuro a tale istanza o gruppo di disponibilità.  

      ![Abilitare la protezione automatica per il gruppo di disponibilità AlwaysOn](./media/backup-azure-sql-database/enable-auto-protection.png)

7. Per creare o scegliere un criterio di backup, nel menu **Backup** selezionare **Criterio di backup**. Viene visualizzato il menu **Criterio di backup**.

    ![Selezionare il criterio di backup](./media/backup-azure-sql-database/select-backup-policy.png)

8. Scegliere i criteri di backup nell'elenco a discesa **Scegliere i criteri di backup** e selezionare **OK**. Per informazioni su come creare un criterio di backup, vedere [Definire un criterio di backup](backup-azure-sql-database.md#define-a-backup-policy).

   > [!NOTE]
   > Durante l'anteprima non è possibile modificare i criteri di backup. Se è necessario un criterio diverso rispetto a quello disponibile nell'elenco, è necessario crearlo. Per informazioni sulla creazione di un nuovo criterio di backup, vedere la sezione [Definire un criterio di backup](backup-azure-sql-database.md#define-a-backup-policy).

    ![Scegliere un criterio di backup dall’elenco](./media/backup-azure-sql-database/select-backup-policy-steptwo.png)

    Nel menu **Criteri di backup**, dall’elenco a discesa **Scegliere i criteri di backup** è possibile:
    - Selezionare i criteri predefiniti: **HourlyLogBackup**.
    - Scegliere un criterio di backup creato in precedenza per SQL.
    - [Definire un nuovo criterio](backup-azure-sql-database.md#define-a-backup-policy) basato sull'obiettivo del punto di recupero (RPO) e sull'intervallo di conservazione.

    > [!Note]
    > Backup di Azure supporta la conservazione a lungo termine che si basa sullo schema di backup GFS (grandfather-father-son, nonno-padre-figlio). Lo schema ottimizza il consumo di storage back-end, soddisfacendo al contempo le esigenze di conformità.
    >

9. Dopo aver scelto un criterio di backup, nel menu **Backup** selezionare **Abilita backup**.

    ![Abilitare i criteri di backup scelti](./media/backup-azure-sql-database/enable-backup-button.png)

    È possibile monitorare l'avanzamento della configurazione nell'area delle **notifiche** del portale.

    ![Area delle notifiche](./media/backup-azure-sql-database/notifications-area.png)


## <a name="auto-protect-sql-server-in-azure-vm"></a>Abilitare la protezione automatica per SQL Server in Macchine virtuali di Azure  

La funzionalità di protezione automatica consente di proteggere automaticamente tutti i database esistenti e quelli che si aggiungeranno in futuro a un'istanza autonoma di SQL Server o a un gruppo di disponibilità AlwaysOn di SQL Server. L'**attivazione** della protezione automatica e la scelta di un criterio di backup verranno applicate ai nuovi database protetti, mentre i database protetti esistenti continueranno a usare i criteri precedenti.

![Abilitare la protezione automatica per il gruppo di disponibilità AlwaysOn](./media/backup-azure-sql-database/enable-auto-protection.png)

Non sono previsti limiti al numero di database che possono essere selezionati in un'unica operazione con la funzionalità di protezione automatica. La funzionalità Configura backup viene attivata per tutti i database e può essere monitorata in **Processi di backup**.

Se per qualche motivo è necessario disabilitare la protezione automatica in un'istanza, fare clic sul nome dell'istanza in **Configura backup** per aprire il pannello di informazioni a destra nella cui parte superiore è visualizzata l'opzione **Disabilita protezione automatica**. Fare clic su **Disabilita protezione automatica** per disabilitare la protezione automatica per tale istanza.

![Disabilitare la protezione automatica per l'istanza](./media/backup-azure-sql-database/disable-auto-protection.png)

Tutti i database in tale istanza continueranno a essere protetti. Tuttavia, questa azione disabiliterà la protezione automatica per qualsiasi database aggiunto in futuro.

### <a name="define-a-backup-policy"></a>Definire un criterio di backup

Un criterio di backup definisce una matrice relativa a quando sono stati acquisiti i backup e per quanto tempo vengono conservati. È possibile usare Backup di Azure per pianificare tre tipi di backup per i database SQL:

* Backup completo: un backup completo è un backup eseguito per l'intero database. Un backup completo contiene tutti i dati in un database specifico o un set di file o gruppi di file e un numero sufficiente di log per recuperare quei dati. Al massimo, è possibile attivare un backup completo al giorno. È possibile scegliere di eseguire un backup completo con frequenza giornaliera o settimanale.
* Backup differenziale: un backup differenziale si basa sul precedente backup completo dei dati più recente. Un backup differenziale acquisisce solo i dati che sono stati modificati dopo il backup completo. Al massimo, è possibile attivare un backup differenziale al giorno. Non è possibile configurare un backup completo e un backup differenziale nella stessa giornata.
* Backup del log delle transazioni: un backup del log consente il ripristino temporizzato fino a uno specifico secondo. Al massimo, è possibile configurare backup del log delle transazioni ogni 15 minuti.

Il criterio viene creato a livello di insieme di credenziali di Servizi di ripristino. Più insiemi di credenziali possono usare gli stessi criteri di backup, ma è necessario applicare i criteri di backup a ogni insieme di credenziali. Quando si crea un criterio di backup, il backup completo giornaliero è l'impostazione predefinita. È possibile aggiungere un backup differenziale, ma solo se si configura l'esecuzione settimanale del backup completo. La procedura seguente illustra come creare un criterio di backup per un’istanza SQL Server in una macchina virtuale di Azure.

> [!NOTE]
> Nell'anteprima non è possibile modificare un criterio di backup. È invece necessario creare un nuovo criterio con i dettagli desiderati.  

Per creare un criterio di backup:

1. Nell'insieme di credenziali Servizi di ripristino che protegge il database SQL fare clic sui **criteri di backup**, quindi scegliere **Aggiungi**.

   ![Aprire la finestra di dialogo crea un nuovo criterio di backup](./media/backup-azure-sql-database/new-policy-workflow.png)

   Viene visualizzato il menu **Aggiungi**.

2. Nel menu **Aggiungi** fare clic su **SQL Server nella macchina virtuale di Azure**.

   ![Scegliere un tipo per il nuovo criterio di backup](./media/backup-azure-sql-database/policy-type-details.png)

   La selezione di SQL Server nella macchina virtuale di Azure definisce il tipo di criterio e apre il menu Criteri di backup. Il menu **Criteri di backup** mostra i campi necessari per qualsiasi nuovo criterio di backup di SQL Server.

3. In **Nome criterio** immettere un nome per il nuovo criterio.

4. È obbligatorio eseguire un backup completo. Non è possibile disattivare l'opzione **Backup completo**. Fare clic su **Backup completo** per visualizzare e modificare il criterio. Anche se non si cambia il criterio di backup, è necessario visualizzarne i dettagli.

    ![Nuovi campi di criteri di backup](./media/backup-azure-sql-database/full-backup-policy.png)

    All'interno del menu **criterio Backup completo** per **Frequenza di backup** scegliere **Giornaliero** o **Settimanale**. Per **Giornaliero**, scegliere l'ora e il fuso orario per l'inizio del processo di backup. Se si sceglie di eseguire backup completi giornalieri, non è possibile creare backup differenziali.

   ![Impostazione intervallo giornaliero](./media/backup-azure-sql-database/daily-interval.png)

    Per **Settimanale**, selezionare il giorno della settimana, l'ora e il fuso orario per l'inizio del processo di backup.

   ![Impostazione intervallo settimanale](./media/backup-azure-sql-database/weekly-interval.png)

5. Per impostazione predefinita, sono selezionate tutte le opzioni relative all'**intervallo di conservazione**: giornaliero, settimanale, mensile e annuale. Deselezionare eventuali limiti di intervallo di conservazione indesiderati. Impostare gli intervalli da utilizzare. Nel menu del **criterio Backup completo** selezionare **OK** per accettare le impostazioni.

   ![Impostazione dell'intervallo di conservazione](./media/backup-azure-sql-database/retention-range-interval.png)

    I punti di recupero vengono contrassegnati per la conservazione, in base al relativo intervallo. Ad esempio, se si seleziona un backup completo giornaliero, viene attivato solo un backup completo ogni giorno. Il backup del giorno specifico viene contrassegnato e conservato in base all'intervallo di conservazione settimanale e alla conservazione settimanale. L'intervallo di conservazione mensile e annuale si comporta allo stesso modo.

6. Per aggiungere un criterio di backup differenziale, selezionare **Backup differenziale**. Viene visualizzato il menu **Criterio di backup differenziale**.

   ![Aprire il menu del criterio di backup differenziale](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

    Nel menu del **criterio Backup differenziale** selezionare **Abilita** per aprire i controlli di frequenza e conservazione. Al massimo, è possibile attivare un backup differenziale al giorno.

    > [!Important]
    > I backup differenziali possono essere conservati al massimo per 180 giorni. Se è necessario conservarli più a lungo, usare i backup completi.
    >

   ![Modificare il criterio di backup differenziale](./media/backup-azure-sql-database/enable-differential-backup-policy.png)

    Selezionare **OK** per salvare il criterio e tornare al menu principale **Criteri di backup**.

7. Per aggiungere un criterio di backup del log transazionale, selezionare **Backup del log**. Viene visualizzato il menu di **backup del log**.

    Nel menu **Backup del log** selezionare **Abilita** e quindi impostare i controlli di frequenza e conservazione. I backup del log possono verificarsi ogni 15 minuti ed essere conservati per un massimo di 35 giorni. Selezionare **OK** per salvare il criterio e tornare al menu principale **Criteri di backup**.

   ![Modificare i criteri di backup del log](./media/backup-azure-sql-database/log-backup-policy-editor.png)

8. Nel menu **Criterio di backup**, scegliere se abilitare la **compressione dei backup SQL**. La compressione è disabilitata per impostazione predefinita.

    Nel back-end Backup di Azure usa la compressione di backup nativa di SQL.

9. Dopo aver completato le modifiche ai criteri di backup, selezionare **OK**.

   ![Accettare il nuovo criterio di backup](./media/backup-azure-sql-database/backup-policy-click-ok.png)

## <a name="restore-a-sql-database"></a>Ripristinare un database SQL
Backup di Azure offre una funzionalità che consente di ripristinare singoli database fino a una data o un'ora specifica (fino a un secondo) usando i backup del log delle transazioni. In base ai tempi di ripristino specificati, Backup di Azure determina automaticamente i backup completi, differenziali e la catena di backup del log necessari per ripristinare i dati.

In alternativa è possibile selezionare un backup completo o differenziale specifico per il ripristino fino a un punto di recupero specifico anziché un'ora specifica.

### <a name="pre-requisite-before-triggering-a-restore"></a>Pre-requisito prima di attivare un ripristino

1. È possibile ripristinare il database in un'istanza di SQL Server nella stessa area di Azure. Il server di destinazione deve essere registrato nello stesso insieme di credenziali Servizi di ripristino dell'origine.  
2. Per ripristinare un database crittografato TDE in un altro SQL Server, ripristinare innanzitutto il certificato nel server di destinazione eseguendo i passaggi documentati [qui](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
3. Prima di attivare il ripristino del database "master", avviare un’istanza SQL Server in modalità utente singolo con opzione `-m AzureWorkloadBackup`. L'argomento dell’opzione `-m` corrisponde al nome del client. Per aprire la connessione è consentito solo questo client. Per tutti i database di sistema (modello, master, msdb), arrestare il servizio SQL Agent prima di attivare il ripristino. Chiudere tutte le applicazioni che potrebbero provare a usare una connessione di uno di questi database.

### <a name="steps-to-restore-a-database"></a>Passaggi per ripristinare un database:

1. Aprire l'insieme di credenziali di Servizi di ripristino registrato con la macchina virtuale SQL.

2. Nel dashboard **Insieme di credenziali di Servizi di ripristino**, in **Uso** selezionare **Elementi di backup** per aprire il menu **Elementi di backup**.

    ![Si aprirà l'elenco Elementi di backup](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. Nel menu **Elementi di backup** in **Tipo di gestione dei backup** selezionare **SQL nella macchina virtuale di Azure**.

    ![Selezionare SQL nella macchina virtuale di Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    Il menu **Elementi di backup** mostra l'elenco di database SQL.

4. Selezionare il database da ripristinare dall'elenco dei database SQL.

    ![Selezionare il database da ripristinare](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Quando si seleziona il database, viene aperto il relativo menu. Il menu contiene i dettagli sul backup per il database, tra cui:

    * I punti di ripristino meno recenti e più recenti.
    * Lo stato del backup del log per le ultime 24 ore (per i database con modello di recupero con registrazione completa e con registrazione minima delle operazioni bulk, se configurato per i backup del log delle transazioni).

5. Nel menu per il database selezionato, selezionare **Ripristina database**. Si apre il menu **Ripristino**.

    ![Selezionare Ripristina database](./media/backup-azure-sql-database/restore-db-button.png)

    Quando il menu **Ripristina** si apre, si apre anche il menu **Configurazione di ripristino**. Il menu **Configurazione di ripristino** è il primo passaggio della configurazione del ripristino. In questo menu si seleziona il punto in cui ripristinare i dati. Le opzioni sono:
    - **Percorso alternativo**: consente di ripristinare il database in un percorso diverso mantenendo il database di origine.
    - **Sovrascrivi database**: ripristina i dati nella stessa istanza di SQL Server dell'origine. L'effetto di questa opzione è che si sovrascrive il database originale.

    > [!Important]
    > Se il database selezionato appartiene a un gruppo di disponibilità Always On, SQL Server non consente di sovrascrivere il database. In questo caso è abilitata solo l'opzione **Percorso alternativo**.
    >

    ![Menu Configurazione di ripristino](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Ripristinare in un percorso alternativo

Questa procedura illustra in dettaglio il ripristino dei dati in un percorso alternativo. Per sovrascrivere il database durante il ripristino, passare a [Ripristinare e sovrascrivere il database](backup-azure-sql-database.md#restore-and-overwrite-the-database). In questa fase, l'insieme di credenziali di Servizi di ripristino è aperto ed è visibile il menu **Configurazione di ripristino**. Se non ci si trova in questa fase, iniziare con il [ripristino di un database SQL](backup-azure-sql-database.md#restore-a-sql-database).

> [!NOTE]
> È possibile ripristinare il database in un'istanza di SQL Server nella stessa area di Azure. Il server di destinazione deve essere registrato nell’insieme di credenziali di Servizi di ripristino.
>

Nel menu **Configurazione di ripristino**, l’elenco a discesa **Server** mostra solo le istanze SQL Server registrate nell’insieme di credenziali di Servizi di ripristino. Se il server che si vuole usare non appare nell'elenco, vedere [Individuare i database di SQL Server](backup-azure-sql-database.md#discover-sql-server-databases) per trovarlo. Durante il processo di individuazione, eventuali nuovi server vengono registrati nell'insieme di credenziali di Servizi di ripristino.

1. Nel menu **Configurazione di ripristino**:

    * In **Dove eseguire il ripristino**, selezionare **Percorso alternativo**.
    * Aprire l’elenco a discesa **Server** e selezionare l'istanza di SQL Server per ripristinare il database.
    * Aprire l’elenco a discesa **Istanza** e selezionare un'istanza di SQL Server.
    * Nella finestra di dialogo **Nome del database ripristinato** inserire il nome del database di destinazione.
    * Se applicabile, scegliere di **sovrascrivere se il database con lo stesso nome esiste già nell'istanza selezionata di SQL**.
    * Selezionare **OK** per completare la configurazione della destinazione e continuare per scegliere un punto di ripristino.

    ![Fornire valori per il menu Configurazione di ripristino](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. Nel menu **Seleziona punto di ripristino** scegliere un punto di ripristino **Log (temporizzato)** o con **registrazione completa e differenziale**. Per eseguire il ripristino fino a un log specifico temporizzato, continuare con questo passaggio. Per ripristinare un punto di ripristino di backup completo e differenziale, procedere al passaggio 3.

    ![Selezionare il menu del punto di ripristino](./media/backup-azure-sql-database/recovery-point-menu.png)

    Il ripristino temporizzato è disponibile solo per i backup del log per i database con modello di recupero con registrazione completa e con registrazione minima delle operazioni bulk. Per ripristinare uno specifico punto nel tempo:

    1. Selezionare il ripristino di tipo **Log (temporizzato)**.

        ![Scegliere il tipo di ripristino temporizzato](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. Sotto **Data/ora di ripristino** selezionare l'icona del calendario per aprire il **calendario**. Nel **calendario**, le date in grassetto contengono punti di recupero e la data corrente è evidenziata. Selezionare una data con punti di recupero. Non è possibile selezionare le date senza punti di ripristino.

        ![Aprire il calendario](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Dopo aver selezionato una data, il grafico della sequenza temporale indica i punti di recupero disponibili in un intervallo continuo.

    3. Usare il grafico della sequenza temporale o la finestra di dialogo **Ora** per specificare un orario specifico per il punto di ripristino. Selezionare **OK** per completare il passaggio del punto di ripristino.

       ![Aprire il calendario](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        Il menu di **selezione del punto di ripristino** si chiude e si apre il menu della **configurazione avanzata**.

       ![Menu configurazione avanzata](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Nel menu **Configurazione avanzata**:

        * Per mantenere il database non operativo dopo il ripristino, impostare il **ripristino con NORECOVERY** su **Abilitato**.
        * Per modificare il percorso di ripristino nel server di destinazione, specificare un nuovo percorso nella colonna **Destinazione**.
        * Scegliere **OK** per approvare le impostazioni. Chiudere il menu **Configurazione avanzata**.

    5. Nel menu **Ripristina** selezionare **Ripristina** per avviare il processo di ripristino. Tenere traccia dell'avanzamento dell'operazione di ripristino nell’area **notifiche** oppure scegliere **Processi di ripristino** dal menu del database.

       ![Avanzamento del processo di ripristino](./media/backup-azure-sql-database/restore-job-notification.png)

3. Nel menu **Seleziona punto di ripristino** scegliere un punto di ripristino **Log (temporizzato)** o con **registrazione completa e differenziale**. Per eseguire un ripristino del log temporizzato, tornare al passaggio 2. Questo passaggio consente di ripristinare un punto specifico di un ripristino con registrazione completa o differenziale. In questo modo è possibile visualizzare tutti i punti di ripristino completo e differenziale degli ultimi 30 giorni. Per visualizzare i punti di recupero risalenti a più di 30 giorni fa, selezionare **Filtro** per aprire il menu **Filtrare i punti di ripristino**. Se si sceglie un punto di recupero differenziale, Backup di Azure prima ripristina il punto di recupero con registrazione completa appropriato e quindi applica il punto di recupero differenziale selezionato.

    ![Selezionare il menu del punto di ripristino](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. Nel menu **Seleziona punto di ripristino** selezionare il ripristino con registrazione **completa e differenziale**.

       ![Selezionare backup completi e differenziali](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        Il menu mostra l'elenco dei punti di recupero disponibili.

    2. Selezionare un punto di recupero dall’elenco e fare clic su **OK** per completare la procedura dei punti di ripristino.

        ![Scegliere un punto di ripristino con registrazione completa](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        Il menu **Punto di ripristino** si chiude e si apre il menu **Configurazione avanzata**.

        ![Menu configurazione avanzata](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Nel menu **Configurazione avanzata**:

        * Per mantenere il database non operativo dopo il ripristino, impostare il **ripristino con NORECOVERY** su **Abilitato**. L'opzione di **ripristino con NORECOVERY** è disabilitata per impostazione predefinita.
        * Per modificare il percorso di ripristino nel server di destinazione, specificare un nuovo percorso nella colonna **Destinazione**.
        * Scegliere **OK** per approvare le impostazioni. Chiudere il menu **Configurazione avanzata**.

    4. Nel menu **Ripristina** selezionare **Ripristina** per avviare il processo di ripristino. Tenere traccia dell'avanzamento dell'operazione di ripristino nell’area **notifiche** oppure scegliere **Processi di ripristino** dal menu del database.

       ![Avanzamento del processo di ripristino](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-and-overwrite-the-database"></a>Ripristinare e sovrascrivere il database

Questa procedura illustra in dettaglio il ripristino dei dati e la sovrascrittura di un database. Per eseguire il ripristino in un percorso alternativo, passare a [Ripristinare in un percorso alternativo](backup-azure-sql-database.md#restore-to-an-alternate-location). In questa fase, l'insieme di credenziali di Servizi di ripristino è aperto ed è visibile il menu **Configurazione di ripristino** (vedere l’immagine seguente). Se non ci si trova in questa fase, iniziare con il [ripristino di un database SQL](backup-azure-sql-database.md#restore-a-sql-database).

![Menu Configurazione di ripristino](./media/backup-azure-sql-database/restore-overwrite-db.png)

Nel menu **Configurazione di ripristino**, l’elenco a discesa **Server** mostra solo le istanze SQL Server registrate nell’insieme di credenziali di Servizi di ripristino. Se il server che si vuole usare non appare nell'elenco, vedere [Individuare i database di SQL Server](backup-azure-sql-database.md#discover-sql-server-databases) per trovarlo. Durante il processo di individuazione, eventuali nuovi server vengono registrati nell'insieme di credenziali di Servizi di ripristino.

1. Nel menu **Configurazione di ripristino** selezionare **Sovrascrivi database**, quindi selezionare **OK** per completare la configurazione della destinazione.

   ![Selezionare Sovrascrivi database](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

    Le impostazioni **Server**, **Istanza** e **Nome del database ripristinato** non sono necessarie.

2. Nel menu **Seleziona punto di ripristino** scegliere un punto di ripristino **Log (temporizzato)** o con **registrazione completa e differenziale**. Per eseguire il ripristino fino a un log specifico temporizzato, continuare con questo passaggio. Per ripristinare un punto di ripristino di backup completo e differenziale, procedere al passaggio 3.

    ![menu di selezione punto di ripristino](./media/backup-azure-sql-database/recovery-point-menu.png)

    Il ripristino temporizzato è disponibile solo per i backup del log per i database con modello di recupero con registrazione completa e con registrazione minima delle operazioni bulk. Per ripristinare in un secondo specifico:

    1. Selezionare il punto di ripristino di tipo **Log (temporizzato)**.

        ![Scegliere il tipo di ripristino temporizzato](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. Sotto **Data/ora di ripristino** selezionare l'icona del calendario per aprire il **calendario**. Nel **calendario**, le date in grassetto contengono punti di recupero e la data corrente è evidenziata. Selezionare una data con punti di recupero. Non è possibile selezionare le date senza punti di ripristino.

        ![Aprire il calendario](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Dopo aver selezionato una data, il grafico della sequenza temporale indica i punti di recupero disponibili.

    3. Usare il grafico della sequenza temporale o la finestra di dialogo **Ora** per specificare un orario specifico per il punto di ripristino. Selezionare **OK** per completare il passaggio del punto di ripristino.

       ![Aprire il calendario](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        Il menu di **selezione del punto di ripristino** si chiude e si apre il menu della **configurazione avanzata**.

       ![Menu configurazione avanzata](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Nel menu **Configurazione avanzata**:

        * Per mantenere il database non operativo dopo il ripristino, impostare il **ripristino con NORECOVERY** su **Abilitato**.
        * Per modificare il percorso di ripristino nel server di destinazione, specificare un nuovo percorso nella colonna **Destinazione**.
        * Scegliere **OK** per approvare le impostazioni. Chiudere il menu **Configurazione avanzata**.

    5. Nel menu **Ripristina** selezionare **Ripristina** per avviare il processo di ripristino. Tenere traccia dell'avanzamento dell'operazione di ripristino nell’area **notifiche** oppure scegliere **Processi di ripristino** dal menu del database.

       ![Avanzamento del processo di ripristino](./media/backup-azure-sql-database/restore-job-notification.png)

3. Nel menu **Seleziona punto di ripristino** scegliere un punto di ripristino **Log (temporizzato)** o con **registrazione completa e differenziale**. Per eseguire un ripristino del log temporizzato, tornare al passaggio 2. Questo passaggio consente di ripristinare un punto specifico di un ripristino con registrazione completa o differenziale. In questo modo è possibile visualizzare tutti i punti di ripristino completo e differenziale degli ultimi 30 giorni. Per visualizzare i punti di recupero risalenti a più di 30 giorni fa, selezionare **Filtro** per aprire il menu **Filtrare i punti di ripristino**. Se si sceglie un punto di recupero differenziale, Backup di Azure prima ripristina il punto di recupero con registrazione completa appropriato e quindi applica il punto di recupero differenziale selezionato.

    ![Selezionare il menu del punto di ripristino](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. Nel menu **Seleziona punto di ripristino** selezionare il ripristino con registrazione **completa e differenziale**.

       ![Selezionare backup completi e differenziali](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        Il menu mostra l'elenco dei punti di recupero disponibili.

    2. Selezionare un punto di recupero dall’elenco e fare clic su **OK** per completare la procedura dei punti di ripristino.

        ![Scegliere un punto di ripristino con registrazione completa](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        Il menu **Punto di ripristino** si chiude e si apre il menu **Configurazione avanzata**.

        ![Menu configurazione avanzata](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Nel menu **Configurazione avanzata**:

        * Per mantenere il database non operativo dopo il ripristino, impostare il **ripristino con NORECOVERY** su **Abilitato**. L'opzione di **ripristino con NORECOVERY** è disabilitata per impostazione predefinita.
        * Per modificare il percorso di ripristino nel server di destinazione, specificare un nuovo percorso nella colonna **Destinazione**.
        * Scegliere **OK** per approvare le impostazioni. Chiudere il menu **Configurazione avanzata**.

    4. Nel menu **Ripristina** selezionare **Ripristina** per avviare il processo di ripristino. Tenere traccia dell'avanzamento dell'operazione di ripristino nell’area **notifiche** oppure selezionando **Processi di ripristino** dal menu del database.

       ![Avanzamento del processo di ripristino](./media/backup-azure-sql-database/restore-job-notification.png)

## <a name="manage-azure-backup-operations-for-sql-on-azure-vms"></a>Gestire le operazioni di Backup di Azure per SQL nelle macchine virtuali di Azure

Questa sezione contiene informazioni sulle varie operazioni di gestione di Backup di Azure disponibili per SQL nelle macchine virtuali di Azure. Sono disponibili le seguenti operazioni di alto livello:

* Monitorare i processi
* Avvisi di backup
* Interruzione della protezione per un database SQL
* Riprendere la protezione per un database SQL
* Attivazione di un processo di backup ad hoc
* Annullare la registrazione di un server che esegue SQL Server

### <a name="monitor-backup-jobs"></a>Monitorare i processi di backup
Poiché Backup di Azure è una soluzione di classe Enterprise, offre notifiche e avvisi di backup avanzati per rilevare eventuali errori. (Vedere [Visualizzare gli avvisi di backup](backup-azure-sql-database.md#view-backup-alerts)). Per monitorare i processi specifici, è possibile usare una delle opzioni seguenti in base ai requisiti.

#### <a name="use-the-azure-portal-for-adhoc-operations"></a>Usare il portale di Azure per le operazioni ad hoc
Backup di Azure consente di visualizzare tutti i processi attivati manualmente oppure i processi ad hoc nel portale dei **processi di backup**. I processi che sono disponibili nel portale **Processi di backup** includono:
- Tutte le operazioni di backup configurate.
- Operazioni di backup attivate manualmente.
- Operazioni di ripristino.
- Registrazione e individuazione di operazioni nel database.
- Arresto delle operazioni di backup.

![Portale dei processi di backup](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Tutti i processi di backup pianificati, tra cui backup completi, differenziali e backup del log, non vengono visualizzati nel portale **Processi di backup**. Usare SQL Server Management Studio per monitorare i processi di backup pianificati, come descritto nella sezione successiva.
>

#### <a name="use-sql-server-management-studio-for-backup-jobs"></a>Usare SQL Server Management Studio per i processi di backup
Backup di Azure usa le API native di SQL per tutte le operazioni di backup. Con le API native, è possibile recuperare tutte le informazioni sui processi dalla [tabella dei set di backup di SQL](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) nel database msdb.

L'esempio seguente è una query per recuperare tutti i processi di backup per un database denominato **DB1**. Personalizzare la query per eseguire un monitoraggio avanzato.

```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  

```

### <a name="view-backup-alerts"></a>Visualizzare gli avvisi di backup

Con i backup del log eseguiti ogni 15 minuti, monitorare i processi di backup a volte può essere noioso. Backup di Azure offre assistenza in questa situazione. Per tutti gli errori di backup vengono inviati avvisi tramite posta elettronica. Gli avvisi vengono consolidati a livello di database in base al codice di errore. Viene inviato un avviso tramite posta elettronica solo per il primo errore di backup di un database. Accedere al portale di Azure per monitorare tutti gli errori di un database.

Per monitorare gli avvisi di backup:

1. Accedere alla propria sottoscrizione di Azure nel [portale di Azure](https://portal.azure.com).

2. Aprire l'insieme di credenziali di Servizi di ripristino registrato con la macchina virtuale SQL.

3. Nel dashboard dell'**insieme di credenziali di Servizi di ripristino** selezionare **Avvisi ed eventi**.

   ![Selezionare Avvisi ed eventi](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. Nel menu **Avvisi ed eventi** selezionare **Avvisi di backup** per visualizzare l'elenco degli avvisi.

   ![Selezionare Avvisi di backup](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

### <a name="stop-protection-for-a-sql-server-database"></a>Interrompere la protezione di un database di SQL Server

Se si interrompe la protezione per un database di SQL Server, Backup di Azure richiede se si vogliono conservare i punti di recupero. Per interrompere la protezione del database SQL è possibile procedere in due modi:

* Interrompere tutti i processi di backup futuri ed eliminare tutti i punti di recupero.
* Interrompere tutti i processi di backup futuri mantenendo però i punti di recupero.

Se si sceglie di interrompere il backup con conservazione dei dati, verranno eliminati i punti di ripristino in base ai criteri di backup. Ciò implica un addebito per l'esecuzione dell'istanza SQL protetta, oltre allo spazio di archiviazione utilizzato fino all'eliminazione di tutti i punti di ripristino. Per altre informazioni sui prezzi di Backup di Azure per SQL, vedere la [pagina dei prezzi di Backup di Azure](https://azure.microsoft.com/pricing/details/backup/).

Per interrompere la protezione per un database:

1. Aprire l'insieme di credenziali di Servizi di ripristino registrato con la macchina virtuale SQL.

2. Nel dashboard **Insieme di credenziali di Servizi di ripristino**, in **Uso** selezionare **Elementi di backup** per aprire il menu **Elementi di backup**.

    ![Si aprirà l'elenco Elementi di backup](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. Nel menu **Elementi di backup** in **Tipo di gestione dei backup** selezionare **SQL nella macchina virtuale di Azure**.

    ![Selezionare SQL nella macchina virtuale di Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    Il menu **Elementi di backup** mostra l'elenco di database SQL.

4. Selezionare il database di cui interrompere la protezione dall'elenco dei database SQL.

    ![Selezionare il database per interrompere la protezione](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Quando si seleziona il database, viene aperto il relativo menu.

5. Nel menu per il database selezionato, selezionare **Interrompi backup**.

    ![Selezionare Interrompi backup](./media/backup-azure-sql-database/stop-db-button.png)

    Viene visualizzato il menu **Interrompi backup**.

6. Nel menu **Interrompi backup** scegliere se **conservare** o **eliminare** i dati di backup. Opzionalmente, è possibile indicare un motivo per l'interruzione della protezione e aggiungere un commento.

    ![Menu Interrompi backup](./media/backup-azure-sql-database/stop-backup-button.png)

7. Selezonare **Interrompi backup** per interrompere la protezione per il database.

  Si noti che l'opzione **Interrompi backup** non funzionerà per un database in un'istanza con [protezione automatica](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm). L'unico modo per arrestare la protezione di questo database consiste nel disabilitare temporaneamente la [protezione automatica](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) nell'istanza e quindi scegliere l'opzione **Interrompi backup** in **Elementi di backup** per tale database.<br>
  Dopo aver disabilitato la protezione automatica, è possibile scegliere **Interrompi backup** per il database in **Elementi di backup**. A questo punto, l'istanza può essere di nuovo abilitata per la protezione automatica.


### <a name="resume-protection-for-a-sql-database"></a>Riprendere la protezione per un database SQL

Se è stata selezionata l'opzione **Conserva i dati di backup** quando è stata interrotta la protezione per il database SQL, è possibile riprendere la protezione. Se i dati di backup non vengono conservati, non sarà possibile riprendere la protezione.

1. Per riprendere la protezione del database SQL, aprire l'elemento di backup e selezionare **Riprendi backup**.

    ![Selezionare Riprendi backup per riprendere la protezione del database](./media/backup-azure-sql-database/resume-backup-button.png)

   Viene visualizzato il menu **Criterio di backup**.

2. Selezionare un criterio dal menu **Criteri di backup** e quindi selezionare **Salva**.

### <a name="trigger-an-adhoc-backup"></a>Attivare un backup ad hoc

Attivare i backup adhoc in base alle esigenze. Esistono quattro tipi di backup ad hoc:

* Backup completo
* Backup completo solo copia
* Backup differenziale
* Backup dei log

Per informazioni dettagliate su ogni tipo, vedere [Tipi di backup per SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017#types-of-backups).

### <a name="unregister-a-sql-server-instance"></a>Annullare un’istanza SQL &Server

Per annullare la registrazione di un'istanza di SQL Server dopo aver rimosso la protezione, ma prima di eliminare l'insieme di credenziali:

1. Aprire l'insieme di credenziali di Servizi di ripristino registrato con la macchina virtuale SQL.

2. Nel dashboard **Insieme di credenziali di Servizi di ripristino**, in **Gestisci**, selezionare **Infrastruttura di backup**.  

   ![Selezionare Infrastruttura di backup](./media/backup-azure-sql-database/backup-infrastructure-button.png)

3. In **Server di gestione** selezionare **Server protetti**.

   ![Selezionare Server protetti](./media/backup-azure-sql-database/protected-servers.png)

    Si apre il menu **Server protetti**.

4. Nel menu **Server protetti** selezionare il server di cui si vuole annullare la registrazione. Per eliminare l'insieme di credenziali, è necessario annullare la registrazione di tutti i server.

5. Nel menu **Server protetti**, fare clic con il pulsante destro del mouse sul server protetto e selezionare **Elimina**.

   ![Selezionare Elimina](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="faq"></a>Domande frequenti

Nella sezione seguente sono disponibili informazioni aggiuntive sul backup del database SQL.

### <a name="can-i-throttle-the-speed-of-the-sql-server-backup-policy"></a>È possibile limitare la velocità del criterio di backup di SQL Server?

Sì. È possibile imitare la velocità di esecuzione del criterio di backup per ridurre al minimo l'impatto su un'istanza di SQL Server.
Per modificare l'impostazione:
1. Nell'istanza di SQL Server, nella *cartella C:\Programmi\Azure Workload Backup\bin*, creare il file **ExtensionSettingsOverrides.json**.
2. Nel file **ExtensionSettingsOverrides.json** impostare **DefaultBackupTasksThreshold** su un valore inferiore, ad esempio 5. <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. Salvare le modifiche. Chiudere il file.
4. Nell'istanza di SQL Server, aprire **Task Manager**. Riavviare il servizio **AzureWLBackupCoordinatorSvc**.

### <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>È possibile eseguire un backup completo da una replica secondaria?
 No. Questa funzionalità non è supportata.

### <a name="do-successful-backup-jobs-create-alerts"></a>I processi di backup con esito positivo determinano la creazione di avvisi?

 No. I processi di backup con esito positivo non generano avvisi. Gli avvisi vengono inviati solo per i processi di backup con esito negativo.

### <a name="can-i-see-details-for-scheduled-backup-jobs-in-the-jobs-menu"></a>Possono vedere i dettagli dei processi di backup pianificati nel menu Processi?

 No. Il menu **Processi di backup** mostra i dettagli dei processi ad hoc, ma non i processi di backup pianificati. Se un processo di backup pianificato ha esito positivo, i dettagli sono disponibili negli avvisi relativi. Per monitorare tutti i processo di backup ad hoc e pianificati, [usare SQL Server Management Studio](backup-azure-sql-database.md#use-sql-server-management-studio-for-backup-jobs).

### <a name="when-i-select-a-sql-server-instance-are-future-databases-automatically-added"></a>Se si seleziona un’istanza SQL Server, vengono aggiunti automaticamente database futuri?

 No. Quando si configura la protezione per un'istanza di SQL Server, se si seleziona l'opzione a livello di server, vengono aggiunti tutti i database. Se si aggiungono database a un’istanza server SQL dopo la configurazione della protezione, è necessario aggiungere manualmente i nuovi database per proteggerli. I database non sono inclusi automaticamente nella protezione configurata.

### <a name="if-i-change-the-recovery-model-how-do-i-restart-protection"></a>Se si modifica il modello di recupero, come è possibile riavviare la protezione?

Attivare un backup completo. I backup del log iniziano come previsto.

### <a name="can-i-protect-sql-always-on-availability-groups-where-the-primary-replica-is-on-premises"></a>È possibile proteggere i gruppi di disponibilità AlwaysOn SQL in cui la replica primaria si trova in locale?

 No. Backup di Azure protegge gli SQL Server in esecuzione in Azure. Se un gruppo di disponibilità (AG) è sparso tra le macchine Azure e in locale, l'AG può essere protetto solo se la replica primaria è in esecuzione in Azure. Inoltre, Backup di Azure protegge solo i nodi in esecuzione nella stessa area di Azure come l'insieme di credenziali di Servizi di ripristino.

### <a name="can-i-protect-sql-always-on-availability-groups-which-are-spread-across-azure-regions"></a>È possibile proteggere i gruppi di disponibilità AlwaysOn SQL distribuiti nelle aree di Azure?

I servizi di ripristino di Backup di Azure possono rilevare e proteggere tutti i nodi che sono nella stessa area dell'insieme di credenziali di Servizi di ripristino. Se si dispone di un gruppo di disponibilità Always On SQL che si estende su più aree di Azure, è necessario configurare il backup dall'area che contiene il nodo primario. Backup di Azure sarà in grado di rilevare e proteggere tutti i database nel gruppo di disponibilità in base alle preferenze di backup. Se le preferenze di backup non vengono soddisfatte, i backup avranno esito negativo e verrà visualizzato l'avviso di errore.

### <a name="while-i-want-to-protect-most-of-the-databases-in-an-instance-i-would-like-to-exclude-a-few-is-it-possible-to-still-use-the-auto-protection-feature"></a>Se l'esigenza è quella di proteggere la maggior parte dei database in un'istanza escludendone alcuni, è comunque possibile usare la funzionalità di protezione automatica?

No, la [protezione automatica](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) si applica all'intera istanza. Non è possibile proteggere in modo selettivo i database in un'istanza usando la protezione automatica.

### <a name="can-i-have-different-policies-for-different-databases-in-an-auto-protected-instance"></a>È possibile definire criteri diversi per database diversi in un'istanza protetta automaticamente?

Se un'istanza include già alcuni database protetti, questi continueranno a essere protetti tramite i rispettivi criteri anche dopo che l'opzione di [protezione automatica](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) è stata **attivata**. Tuttavia, a tutti i database non protetti e a quelli che verranno aggiunti in futuro verranno applicati solo i criteri definiti in **Configura backup** dopo aver selezionato i database. Infatti, diversamente dagli altri database protetti, non è possibile modificare i criteri per un database in un'istanza protetta automaticamente.
Se necessario, l'unico modo consiste nel disabilitare temporaneamente la protezione automatica per l'istanza e quindi modificare i criteri per il database, per poi riabilitare la protezione automatica per l'istanza.

### <a name="if-i-delete-a-database-from-an-auto-protected-instance-will-the-backups-for-that-database-also-stop"></a>Se si elimina un database da un'istanza protetta automaticamente, verranno interrotti anche i backup per tale database?

No, se un database viene eliminato da un'istanza protetta automaticamente, i backup per tale database vengono comunque tentati. Ciò implica che il database eliminato inizia a essere visualizzato come non integro in **Elementi di backup** e viene ancora considerato come protetto.

L'unico modo per arrestare la protezione di questo database consiste nel disabilitare temporaneamente la [protezione automatica](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) nell'istanza e quindi scegliere l'opzione **Interrompi backup** in **Elementi di backup** per tale database. È a questo punto possibile riabilitare la protezione automatica per questa istanza.

###  <a name="why-cant-i-see-the-newly-added-database-to-an-auto-protected-instance-under-the-protected-items"></a>Per quale motivo il database appena aggiunto a un'istanza protetta automaticamente non compare tra gli elementi protetti?

È possibile che un database appena aggiunto a un'istanza [protetta automaticamente](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) non venga visualizzato immediatamente come protetto. Il motivo è che l'individuazione viene eseguita in genere ogni 8 ore. Tuttavia, è possibile eseguire un'individuazione manuale tramite l'opzione **Individua di nuovo i database** per individuare e proteggere immediatamente i nuovi database, come illustrato nell'immagine seguente:

  ![Visualizzare il database appena aggiunto](./media/backup-azure-sql-database/view-newly-added-database.png)


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Backup di Azure, vedere l'esempio Azure PowerShell per il backup di macchine virtuali crittografate.

> [!div class="nextstepaction"]
> [Eseguire il backup di una macchina virtuale crittografata](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
