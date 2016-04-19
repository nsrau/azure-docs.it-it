<properties
   pageTitle="Eseguire il backup di file e cartelle da Windows in Azure | Microsoft Azure"
   description="Informazioni su come eseguire il backup di dati di Windows Server creando un insieme di credenziali, installando l'agente di backup ed eseguendo il backup di file e cartelle in Azure."
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""
   keywords="come eseguire il backup; come eseguire il back up"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.date="04/09/2016"
   ms.author="jimpark;"/>

# Primi passi: eseguire il backup di file e cartelle da un server o client Windows in Azure

Questo articolo illustra come eseguire il backup di file e cartelle di Windows Server o di un client Windows in Azure. Si tratta di un'esercitazione che illustra le informazioni di base, l'ideale per iniziare a usare Backup di Azure.

Per altre informazioni su Backup di Azure, vedere questa [panoramica](backup-introduction-to-azure-backup.md).

Per eseguire il backup di file e cartelle in Azure, è necessario eseguire queste attività:

![Passaggio 1](./media/backup-try-azure-backup-in-10-mins/step-1.png) Ottenere una sottoscrizione di Azure (se non se ne possiede già una).<br> ![Passaggio 2](./media/backup-try-azure-backup-in-10-mins/step-2.png) Creare un insieme di credenziali di backup e scaricare gli elementi necessari.<br> ![Passaggio 3](./media/backup-try-azure-backup-in-10-mins/step-3.png) Installare e registrare l'agente di Backup.<br> ![Passaggio 4](./media/backup-try-azure-backup-in-10-mins/step-4.png) Eseguire il backup di file e cartelle.


![Come eseguire il backup di computer Windows con Backup di Azure](./media/backup-try-azure-backup-in-10-mins/windows-machine-backup-process.png)

## Passaggio 1: Ottenere una sottoscrizione di Azure

Se non è disponibile una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) che consente di accedere a qualsiasi servizio Azure.

## Passaggio 2: Creare un insieme di credenziali di backup e scaricare gli elementi necessari

Per eseguire il backup di file e cartelle è necessario creare un insieme di credenziali di backup nell'area in cui archiviare i dati. Determinare anche come viene replicata la risorsa di archiviazione e scaricare le credenziali e l'agente di backup.

### Per creare un insieme di credenziali per il backup

1. Accedere al [portale di Azure](https://portal.azure.com/), se questa operazione non è già stata eseguita, usando la sottoscrizione di Azure.

2. Fare clic su **Nuovo > Integrazione ibrida > Backup**.

    ![Iniziare a prepararsi per eseguire il backup di file e cartelle](./media/backup-try-azure-backup-in-10-mins/second-blade-backup.png)

3. In **Nome** immettere un nome descrittivo per identificare l'insieme di credenziali per il backup.

4. In **Area** selezionare l'area più vicina alla propria località per trasferire più velocemente i file.

5. Fare clic su **CREA INSIEME DI CREDENZIALI**.

    ![Creare un insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/demo-vault-name.png)

    Quando l'insieme di credenziali per il backup è pronto, viene elencato tra le risorse per Servizi di ripristino come **Attivo**.

    ![Lo stato dell'insieme di credenziali è attivo](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

Dopo avere creato l'insieme di credenziali, scegliere come replicare la risorsa di archiviazione.

>[AZURE.NOTE] È necessario scegliere come replicare la risorsa di archiviazione subito dopo avere creato un insieme di credenziali e prima di registrarvi i computer. Dopo la registrazione di un elemento nell'insieme di credenziali, la replica della risorsa di archiviazione viene bloccata e non può essere modificata.

### Per scegliere come replicare la risorsa di archiviazione

1. Fare clic sull'insieme di credenziali creato.
2. Nella pagina Avvio rapido selezionare **Configura**.

    ![Configurare l'insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/configure-vault.png)

3. Scegliere l'opzione di archiviazione appropriata.

    Se si usa Azure come backup primario, scegliere l'[archiviazione con ridondanza geografica](../storage/storage-redundancy.md#geo-redundant-storage). Se si usa Azure come backup terziario, scegliere l'[archiviazione con ridondanza locale](../storage/storage-redundancy.md#locally-redundant-storage).

    ![Scegliere un'opzione di replica di archiviazione](./media/backup-try-azure-backup-in-10-mins/geo-redundant.png)

4. Se è stata selezionata l'opzione **Con ridondanza locale**, è necessario fare clic su **Salva** perché l'opzione predefinita è **Con ridondanza geografica**.

Usare le credenziali dell'insieme di credenziali per l'autenticazione del computer con l'insieme di credenziali per il backup. Ecco come scaricare tali credenziali.

### Per scaricare le credenziali dell'insieme di credenziali
Il file delle credenziali dell'insieme di credenziali viene usato solo durante il processo di registrazione e scade dopo 48 ore.

1. Per tornare alla pagina **Avvio rapido** dell'insieme di credenziali, fare clic su ![Selezionare il nuovo insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/quick-start-icon.png)

2. Fare clic su **Scarica credenziali dell'insieme di credenziali > Salva**.

Ora scaricare l'agente di backup.

### Per scaricare l'agente di backup

Fare clic su **Agente per Windows Server, System Center Data Protection Manager o client Windows > Salva**.

![Salvare l'agente di backup](./media/backup-try-azure-backup-in-10-mins/agent.png)

Ora che l'insieme di credenziali è stato creato e si è scaricato tutto, installare e registrare l'agente di backup.

## Passaggio 3: Installare e registrare l'agente di backup

1. Fare doppio clic su **MARSagentinstaller.exe** nel percorso in cui è stato salvato.
2. Completare l'Installazione guidata di Agente servizi di ripristino di Microsoft Azure. Per completare la procedura guidata, sarà necessario:
    - Scegliere un percorso per la cartella di installazione e della cache.
    - Fornire le informazioni sul server proxy se si usa un server proxy per connettersi a Internet.
    - Se si usa un proxy autenticato, immettere il nome utente e la password.
    - Salvare la passphrase di crittografia in un luogo sicuro.

    >[AZURE.NOTE] Se la passphrase viene persa o dimenticata, Microsoft non potrà offrire assistenza per il recupero dei dati di backup. Salvare il file in una posizione sicura. È necessario per ripristinare un backup.

L'agente ora è installato e il computer è registrato nell'insieme di credenziali. Ora è possibile configurare e pianificare il backup.

## Passaggio 4: Eseguire il backup di file e cartelle
Se l'agente di backup non è già aperto, è possibile trovarlo se si cerca Backup di Microsoft Azure nel computer.

1. Nell'**agente di Backup** fare clic su **Pianifica backup**.

    ![Pianificare un backup di Windows Server](./media/backup-try-azure-backup-in-10-mins/snap-in-schedule-backup-closeup.png)

2. Completare la Pianificazione guidata backup. Mentre si completa la procedura guidata, sarà necessario:

    - Selezionare i file e le cartelle di cui si vuole eseguire il backup.
    - Specificare la pianificazione per il backup (giornaliera o settimanale).
    - Determinare i criteri di conservazione.
    - Scegliere come completare il backup iniziale (in rete o offline).

    Altre informazioni sul [completamento del backup iniziale offline](backup-azure-backup-import-export.md). <br><br>

3. Al termine della procedura guidata, tornare all'**agente di Backup** e fare clic su **Esegui backup** per completare il backup iniziale in rete.

    ![Eseguire ora il backup di Windows Server](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

4. Nella schermata **Conferma** fare clic su **Backup**. Il processo di backup continuerà in background se si chiude la procedura guidata prima che venga completato.

    Al termine del backup iniziale, la visualizzazione **Processi** nella console indica che il processo è stato completato.

    ![Backup iniziale completato](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

Il backup dei file e delle cartelle è stato eseguito in Backup di Azure.

## Passaggi successivi
- Sono disponibili altre informazioni sul [backup di computer Windows](backup-configure-vault.md).
- Ora che si è eseguito il backup dei file e delle cartelle, è possibile [gestire l'insieme di credenziali e i server](backup-azure-manage-windows-server.md).
- Se è necessario ripristinare un backup, usare questo articolo per [ripristinare i file in un computer Windows](backup-azure-restore-windows-server.md).

<!---HONumber=AcomDC_0413_2016-->