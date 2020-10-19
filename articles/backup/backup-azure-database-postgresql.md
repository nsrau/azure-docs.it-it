---
title: Eseguire il backup di Database di Azure per PostgreSQL
description: Informazioni sul backup di database di Azure per PostgreSQL con conservazione a lungo termine (anteprima)
ms.topic: conceptual
ms.date: 09/08/2020
ms.custom: references_regions
ms.openlocfilehash: 3c326ff197f18333812438719908daced2b268bb
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173574"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>Backup del database di Azure per PostgreSQL con conservazione a lungo termine (anteprima)

Backup di Azure e i servizi di database di Azure sono disponibili per creare una soluzione di backup di livello aziendale per i server di database di Azure per PostgreSQL che conserva backup per un massimo di 10 anni.

Oltre alla conservazione a lungo termine, la soluzione presenta anche molte altre funzionalità, come indicato di seguito:

- Controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per il database tramite l'autenticazione Azure Active Directory e identità del servizio gestita (MSI).
- Backup pianificato e su richiesta controllati dal cliente a livello di singolo database.
- Il ripristino a livello di database viene ripristinato in qualsiasi server Postgres o direttamente nell'archivio BLOB.
- Conservazione a lungo termine.
- Monitoraggio centralizzato di tutte le operazioni e dei processi.
- I backup vengono archiviati in domini di sicurezza e di errore distinti. Quindi, anche se il server di origine deve essere compromesso o addirittura interrotto, i backup rimarranno protetti nell'insieme di credenziali per il [backup](backup-vault-overview.md).
- L'uso di **pg_dump** consente una maggiore flessibilità nei ripristini in modo che sia possibile eseguire il ripristino in più versioni del database o anche ripristinare solo una parte del backup.

È possibile usare questa soluzione in modo indipendente o in aggiunta alla soluzione di backup nativa offerta da Azure PostgreSQL che offre una conservazione fino a 35 giorni. La soluzione nativa è adatta per i ripristini operativi, ad esempio quando si desidera eseguire il ripristino dai backup più recenti. La soluzione backup di Azure ti aiuta a soddisfare le tue esigenze di conformità e a backup e ripristino più granulari e flessibili.

## <a name="support-matrix"></a>Matrice di supporto

|Supporto  |Dettagli  |
|---------|---------|
|Distribuzioni supportate   |  Server singolo autonomo per database di Azure per PostgreSQL     |
|Aree di Azure supportate |  Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centro-meridionali, Stati Uniti occidentali |
|Versioni supportate di Azure PostgreSQL    |   9,5, 9,6, 10, 11      |

## <a name="feature-considerations-and-limitations"></a>Considerazioni sulle funzionalità e limitazioni

- Tutte le operazioni sono supportate solo dal portale di Azure.
- Il limite consigliato per le dimensioni massime del database è 400 GB.
- Il backup tra aree non è supportato. Ciò significa che non è possibile eseguire il backup di un server PostgreSQL di Azure in un insieme di credenziali in un'altra area. Analogamente, è possibile ripristinare un backup in un server all'interno della stessa area dell'insieme di credenziali.
- Solo i dati vengono recuperati al momento del ripristino. "Roles" non è stato ripristinato.
- In anteprima, si consiglia di eseguire la soluzione solo nell'ambiente di test.

## <a name="backup-process"></a>Processo di backup

1. Questa soluzione USA **pg_dump** per eseguire il backup dei database PostgreSQL di Azure.

2. Una volta specificati i database PostgreSQL di Azure di cui si vuole eseguire il backup, il servizio convalida se dispone del set corretto di autorizzazioni e accesso per eseguire l'operazione di backup nel server e nel database.

3. Backup di Azure crea un ruolo di lavoro con un'estensione di backup installata. Questa estensione comunica con il server postgres.

4. Questa estensione è costituita da un coordinatore e da un plug-in di Azure postgres. Sebbene il coordinatore sia responsabile dell'attivazione dei flussi di lavoro per varie operazioni, ad esempio la configurazione di backup, backup e ripristino, il plug-in è responsabile del flusso di dati effettivo.
  
5. Quando si attiva la configurazione della protezione nei database selezionati, il servizio di backup configura il coordinatore con le pianificazioni di backup e altri dettagli relativi ai criteri.

6. All'ora pianificata, il coordinatore comunica con il plug-in e inizia a trasmettere i dati di backup dal server Postgres usando **pg_dump**.

7. Il plug-in Invia i dati direttamente all'insieme di credenziali per il backup, eliminando la necessità di un percorso di gestione temporanea. I dati vengono crittografati usando chiavi gestite da Microsoft e archiviati dal servizio backup di Azure negli account di archiviazione.

8. Al termine del trasferimento dei dati, il coordinatore conferma il commit con il servizio di backup.

    ![Processo di backup](./media/backup-azure-database-postgresql/backup-process.png)

## <a name="configure-backup-on-azure-postgresql-databases"></a>Configurare il backup nei database PostgreSQL di Azure

È possibile configurare il backup su più database in più server PostgreSQL di Azure. Verificare che le [autorizzazioni dei prerequisiti](#prerequisite-permissions-for-configure-backup-and-restore) richieste dal servizio per eseguire il backup dei server Postgres siano già configurate.

Le istruzioni seguenti sono una guida dettagliata alla configurazione del backup nei database PostgreSQL di Azure con backup di Azure:

1. Esistono due modi per avviare il processo:

    1. Passare a [Backup Center](backup-center-overview.md)  ->  **Panoramica**  ->  **backup**.

        ![Vai a backup Center](./media/backup-azure-database-postgresql/backup-center.png)

        In **Avvia: configurare il backup**selezionare il **tipo di origine** dati come **database di Azure per PostgreSQL**.

        ![In avvio: Configura backup selezionare tipo di origine dati](./media/backup-azure-database-postgresql/initiate-configure-backup.png)

    1. In alternativa, è possibile passare direttamente al backup degli insiemi di credenziali di [backup](backup-vault-overview.md)  ->  **Backup**.

        ![Passa a insiemi di credenziali di backup](./media/backup-azure-database-postgresql/backup-vaults.png)

        ![Selezionare backup nell'insieme di credenziali di backup](./media/backup-azure-database-postgresql/backup-backup-vault.png)

1. In **Configura backup**selezionare l'insieme di credenziali per il **backup** in cui si vuole eseguire il backup dei database postgres. Queste informazioni sono precompilate se si è già nel contesto dell'insieme di credenziali.

    ![Selezionare l'insieme di credenziali per il backup in Configura backup](./media/backup-azure-database-postgresql/configure-backup.png)

1. Selezionare o creare un **criterio di backup**.

    ![Scegliere i criteri di backup](./media/backup-azure-database-postgresql/backup-policy.png)

1. Per eseguire il backup, selezionare risorse o database postgres.

    ![Selezionare le risorse di cui eseguire il backup](./media/backup-azure-database-postgresql/select-resources.png)

1. È possibile scegliere dall'elenco di tutti i server PostgreSQL di Azure tra le sottoscrizioni se si trovano nella stessa area dell'insieme di credenziali. Espandere la freccia per visualizzare l'elenco dei database all'interno di un server.

    ![Selezione server](./media/backup-azure-database-postgresql/choose-servers.png)

1. Il servizio esegue questi controlli sui database selezionati per verificare se l'insieme di credenziali dispone delle autorizzazioni per eseguire il backup dei server e dei database Postgres selezionati.
    1. L'idoneità per il **backup** per tutti i database deve avere **esito positivo** per poter continuare.
    1. Se si verifica un errore, **correggere** l'errore e **rivalidare** o rimuovere il database dalle selezioni.

    ![Errori di convalida da correggere](./media/backup-azure-database-postgresql/validation-errors.png)

1. Confermare tutti i dettagli in **Verifica e configura** e selezionare **Configura backup** per inviare l'operazione.

    ![Confermare i dettagli in revisione e configurazione](./media/backup-azure-database-postgresql/review-and-configure.png)

1. Una volta attivato, l'operazione di **configurazione backup** creerà un'istanza di backup. È possibile tenere traccia dello stato dell'operazione nel riquadro [istanze di backup](backup-center-monitor-operate.md#backup-instances) nella vista centro backup o insieme di credenziali.

    ![Istanze di backup](./media/backup-azure-database-postgresql/backup-instances.png)

1. I backup vengono attivati in base alla pianificazione del backup definita nei criteri. I processi possono essere rilevati in [processi di backup](backup-center-monitor-operate.md#backup-jobs). Attualmente, è possibile visualizzare i processi negli ultimi sette giorni.

    ![Processi di backup](./media/backup-azure-database-postgresql/backup-jobs.png)

## <a name="create-backup-policy"></a>Creare criteri di backup

1. Passare a **Backup Center**  ->  **backup criteri**  ->  **Aggiungi**. In alternativa, è possibile passare a **backup**dell'insieme di credenziali di backup  ->  **Backup policy**  ->  **Aggiungi**.

    ![Aggiungi criteri di backup](./media/backup-azure-database-postgresql/add-backup-policy.png)

1. Immettere un **nome** per il nuovo criterio.

    ![Immettere il nome dei criteri](./media/backup-azure-database-postgresql/enter-policy-name.png)

1. Definire la pianificazione del backup. È attualmente supportato il backup **settimanale** . È possibile pianificare i backup in uno o più giorni della settimana.

    ![Definire la pianificazione del backup](./media/backup-azure-database-postgresql/define-backup-schedule.png)

1. Definire le impostazioni di **conservazione** . È possibile aggiungere una o più regole di conservazione. Ogni regola di conservazione presuppone gli input per backup specifici, l'archivio dati e la durata di conservazione per tali backup.

1. È possibile scegliere di archiviare i backup in uno dei due archivi dati (o livelli): archivio dati di **backup** (livello critico) o archivio **dati di archivio** (in anteprima). È possibile scegliere tra **due opzioni** di suddivisione in livelli per definire quando i backup sono suddivisi in livelli tra i due archivi dati:

    - Scegliere di copiare **immediatamente** se si preferisce una copia di backup sia negli archivi dati di backup che in quelli di archivio simultaneamente.
    - Scegliere di spostarsi **in scadenza** se si preferisce spostare il backup nell'archivio dati di archiviazione alla scadenza nell'archivio dati di backup.

1. La **regola di conservazione predefinita** viene applicata in assenza di altre regole di conservazione e ha un valore predefinito di tre mesi.

    - La durata della conservazione è compresa tra sette giorni e 10 anni nell' **archivio dati di backup**.
    - La durata della conservazione è compresa tra sei mesi e 10 anni nell'archivio **dati di archiviazione**.

    ![Modifica Durata conservazione](./media/backup-azure-database-postgresql/edit-retention.png)

>[!NOTE]
>Le regole di conservazione vengono valutate in base a un ordine di priorità predeterminato. La priorità è la più alta per la regola **annuale** , seguita dalla regola **mensile** e quindi dalla regola **settimanale** . Quando non sono valide altre regole, vengono applicate le impostazioni di conservazione predefinite. Ad esempio, lo stesso punto di ripristino può essere il primo backup eseguito correttamente, eseguito ogni settimana, nonché il primo backup eseguito correttamente ogni mese. Tuttavia, poiché la priorità della regola mensile è superiore a quella della regola settimanale, viene applicata la conservazione corrispondente al primo backup completato correttamente eseguito ogni mese.

## <a name="restore"></a>Restore

È possibile ripristinare un database in qualsiasi server PostgreSQL di Azure all'interno della stessa sottoscrizione, se il servizio dispone del set appropriato di autorizzazioni nel server di destinazione. Verificare che le [autorizzazioni prerequisiti](#prerequisite-permissions-for-configure-backup-and-restore) richieste dal servizio per eseguire il backup dei server Postgres siano già configurate.

Seguire questa guida dettagliata per attivare un ripristino:

1. Esistono due modi per avviare il processo di ripristino:
    1. Passare a [Backup Center](backup-center-overview.md)  ->  **Panoramica**  ->  **ripristino**.

    ![Selezionare Ripristina in centro backup](./media/backup-azure-database-postgresql/backup-center-restore.png)

    In **Avvia: ripristino**selezionare il tipo di **origine** dati come **database di Azure per PostgreSQL**. Selezionare l' **istanza di backup**.

    ![Selezionare il tipo di origine dati in avvio: ripristino](./media/backup-azure-database-postgresql/initiate-restore.png)

    1. In alternativa, è possibile passare direttamente alle istanze di backup dell'insieme di credenziali di **backup**  ->  **Backup Instances**. Selezionare l' **istanza di backup** corrispondente al database che si desidera ripristinare.

    ![Istanze di backup per il ripristino](./media/backup-azure-database-postgresql/backup-instances-restore.png)

    ![Elenco di istanze di backup](./media/backup-azure-database-postgresql/list-backup-instances.png)

    ![Selezionare Ripristina](./media/backup-azure-database-postgresql/select-restore.png)

1. **Selezionare punto di ripristino** dall'elenco di tutti i backup completi disponibili per l'istanza di backup selezionata. Per impostazione predefinita, è selezionato il punto di ripristino più recente.

    ![Seleziona punto di ripristino](./media/backup-azure-database-postgresql/select-recovery-point.png)

    ![Elenco dei punti di ripristino](./media/backup-azure-database-postgresql/list-recovery-points.png)

1. **Parametri di ripristino**dell'input. A questo punto, è possibile scegliere tra due tipi di ripristino: **Restore As database** and **Restore As files**.

1. **Restore As database**: ripristinare i dati di backup per creare un nuovo database nel server PostgreSQL di destinazione.

    - Il server di destinazione può essere uguale al server di origine. Tuttavia, la sovrascrittura del database originale non è supportata.
    - È possibile scegliere dal server tra tutte le sottoscrizioni, ma nella stessa area dell'insieme di credenziali.
    - Selezionare **revisione e ripristino**. Verrà attivata la convalida per verificare se il servizio dispone delle autorizzazioni di ripristino appropriate per il server di destinazione.

    ![Ripristina come database](./media/backup-azure-database-postgresql/restore-as-database.png)

1. **Ripristina come file**: consente di eseguire il dump dei file di backup nell'account di archiviazione di destinazione (BLOB).

    - È possibile scegliere tra gli account di archiviazione in tutte le sottoscrizioni, ma nella stessa area dell'insieme di credenziali.
    - Selezionare il contenitore di destinazione dall'elenco dei contenitori filtrato per l'account di archiviazione selezionato.
    - Selezionare **revisione e ripristino**. Verrà attivata la convalida per verificare se il servizio dispone delle autorizzazioni di ripristino appropriate per il server di destinazione.

    ![Ripristinare come file](./media/backup-azure-database-postgresql/restore-as-files.png)

1. Esaminare le informazioni e selezionare **Ripristina**. Verrà attivato un processo di ripristino corrispondente di cui è possibile tenere traccia nei **processi di backup**.

## <a name="prerequisite-permissions-for-configure-backup-and-restore"></a>Autorizzazioni prerequisiti per la configurazione di backup e ripristino

Backup di Azure segue rigide linee guida sulla sicurezza. Anche se si tratta di un servizio nativo di Azure, le autorizzazioni per la risorsa non vengono presupposte e devono essere fornite in modo esplicito dall'utente.  Analogamente, le credenziali per la connessione al database non vengono archiviate. Questo è importante per salvaguardare i dati. Viene invece usata l'autenticazione Azure Active Directory.

[Scaricare questo documento](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) per ottenere uno script automatizzato e le relative istruzioni. Concederà un set di autorizzazioni appropriato a un server PostgreSQL di Azure per il backup e il ripristino.

## <a name="manage-the-backed-up-azure-postgresql-databases"></a>Gestire i database PostgreSQL di Azure di cui è stato eseguito il backup

Di seguito sono riportate le operazioni di gestione che è possibile eseguire sulle **istanze di backup**:

### <a name="on-demand-backup"></a>Backup su richiesta

Per attivare un backup non nella pianificazione specificata nel criterio, passare a backup **istanze**  ->  **Backup Now**.
Scegliere dall'elenco delle regole di conservazione definite nei criteri di backup associati.

![Attiva ora backup](./media/backup-azure-database-postgresql/backup-now.png)

![Scegliere un elenco di regole di conservazione](./media/backup-azure-database-postgresql/retention-rules.png)

### <a name="stop-protection"></a>Arresta protezione

È possibile arrestare la protezione dati su un elemento di backup. Questa operazione eliminerà anche i punti di ripristino associati per tale elemento di backup. Non è ancora disponibile l'opzione per arrestare la protezione, mantenendo i punti di ripristino esistenti.

![Arresta protezione](./media/backup-azure-database-postgresql/stop-protection.png)

### <a name="change-policy"></a>Modificare i criteri

È possibile modificare il criterio associato con un'istanza di backup.

1. Selezionare i criteri di modifica dell' **istanza di backup**  ->  **Change Policy**.

    ![Modificare i criteri](./media/backup-azure-database-postgresql/change-policy.png)

1. Selezionare il nuovo criterio che si desidera applicare al database.

    ![Riassegna criteri](./media/backup-azure-database-postgresql/reassign-policy.png)

## <a name="troubleshooting"></a>Risoluzione dei problemi

Questa sezione fornisce informazioni sulla risoluzione dei problemi per il backup dei database PostgreSQL di Azure con backup di Azure.

### <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

Concedere all'insieme di credenziali per il backup l'accesso in **lettura** MSI al server PG di cui si vuole eseguire il backup o il ripristino:

Per stabilire una connessione sicura al database PostgreSQL, backup di Azure usa il modello di autenticazione [identità del servizio gestita (MSI)](../active-directory/managed-identities-azure-resources/overview.md) . Questo significa che l'insieme di credenziali di backup avrà accesso solo alle risorse a cui l'utente ha concesso esplicitamente l'autorizzazione.

Un MSI di sistema viene assegnato automaticamente all'insieme di credenziali al momento della creazione. È necessario assegnare a questo insieme di credenziali MSI l'accesso ai server PostgreSQL da cui si intende eseguire il backup dei database.

Passaggi:

1. Nel server Postgres passare al riquadro controllo di **accesso (IAM)** .

    ![Riquadro di controllo di accesso](./media/backup-azure-database-postgresql/access-control-pane.png)

1. Selezionare **Aggiungi un'assegnazione di ruolo**.

    ![Aggiungi un'assegnazione di ruolo](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. Nel riquadro del contesto destro visualizzato, immettere quanto segue:<br>

    **Ruolo:** Lettore<br>
    **Assegnare l'accesso a:** Scegliere l'insieme di credenziali per il **backup**<br>
    Se l'opzione dell'insieme di credenziali per il **backup** non è disponibile nell'elenco a discesa, scegliere l' **opzione Azure ad utente, gruppo o entità servizio** .<br>

    ![Seleziona il ruolo](./media/backup-azure-database-postgresql/select-role.png)

    **Selezionare:** Immettere il nome dell'insieme di credenziali di backup in cui si vuole eseguire il backup del server e dei relativi database.<br>

    ![Immettere il nome dell'insieme di credenziali di backup](./media/backup-azure-database-postgresql/enter-backup-vault-name.png)

### <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

Creare un utente di backup del database in grado di eseguire l'autenticazione con Azure Active Directory:

Questo errore può derivare da un'assenza di un amministratore Azure Active Directory per il server PostgreSQL o in assenza di un utente di backup che può eseguire l'autenticazione con Azure Active Directory.

Passaggi:

Aggiungere un amministratore Active Directory al server OSS:

Questo passaggio è necessario per connettersi al database tramite un utente che può eseguire l'autenticazione con Azure Active Directory anziché una password. L'utente amministratore di Azure AD nel database di Azure per PostgreSQL avrà il ruolo **azure_ad_admin**. Solo un ruolo **azure_ad_admin** può creare nuovi utenti del database in grado di eseguire l'autenticazione con Azure ad.

1. Passare alla scheda amministratore Active Directory nel riquadro di spostamento a sinistra della visualizzazione server e aggiungere se stessi (o un altro utente) come amministratore Active Directory.

    ![Imposta Active Directory amministratore](./media/backup-azure-database-postgresql/set-admin.png)

1. Assicurarsi di **salvare** l'impostazione utente amministratore di Active Directory.

    ![Salva Active Directory impostazioni utente amministratore](./media/backup-azure-database-postgresql/save-admin-setting.png)

Fare riferimento a [questo documento](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) per l'elenco dei passaggi da eseguire per completare la procedura di concessione delle autorizzazioni.

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

Stabilire la linea di rete abilitando il flag **Consenti l'accesso a servizi di Azure** nella visualizzazione server. Nella visualizzazione server, nel riquadro **sicurezza connessione** , impostare il flag **Consenti l'accesso a servizi di Azure** su **Sì**.

![Consentire l'accesso ai servizi di Azure](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

### <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

#### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>Autorizzazione per eseguire il ripristino in un contenitore dell'account di archiviazione durante il ripristino come file

1. Assegnare all'insieme di credenziali per il backup l'autorizzazione per accedere ai contenitori dell'account di archiviazione usando il portale di Azure.
    1. Passare a controllo di accesso dell' **account di archiviazione**  ->  **Access Control**  ->  **Aggiungi assegnazione di ruolo**.
    1. Assegnare il ruolo di **collaboratore dati BLOB di archiviazione** all'insieme di credenziali per il backup MSI.

    ![Assegnare il ruolo di collaboratore dati BLOB di archiviazione](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. In alternativa, assegnare autorizzazioni granulari al contenitore specifico in cui si esegue il ripristino usando l'interfaccia della riga di comando di Azure [AZ Role Assignment create](/cli/azure/role/assignment) .

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. Sostituire il parametro assegnatario con l' **ID applicazione** del file MSI dell'insieme di credenziali e il parametro scope per fare riferimento al contenitore specifico.
    1. Per ottenere l' **ID applicazione** dell'MSI dell'insieme di credenziali, selezionare **tutte le applicazioni** in **tipo di applicazione**:

        ![Seleziona tutte le applicazioni](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. Cercare il nome dell'insieme di credenziali e copiare l'ID applicazione:

        ![Cercare il nome dell'insieme di credenziali](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica degli insiemi di credenziali di backup](backup-vault-overview.md)