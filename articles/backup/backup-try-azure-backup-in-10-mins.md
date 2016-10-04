<properties
   pageTitle="Imparare a eseguire il backup di file e cartelle da Windows ad Azure con Backup di Azure usando il modello di distribuzione Resource Manager | Microsoft Azure"
   description="Informazioni su come eseguire il backup di dati di Windows Server creando un insieme di credenziali, installando l'agente di Servizi di ripristino ed eseguendo il backup di file e cartelle in Azure."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="cfreeman"
   editor=""
   keywords="come eseguire il backup; come eseguire il back up"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.date="09/27/2016"
   ms.author="markgal;"/>

# Primi passi: eseguire il backup di file e cartelle con Backup di Azure usando il modello di distribuzione Resource Manager

Questo articolo illustra come eseguire il backup di file e cartelle di Windows Server o di un client Windows in Azure con Backup di Azure usando Resource Manager. Si tratta di un'esercitazione che illustra le informazioni di base, l'ideale per iniziare a usare Backup di Azure.

Per altre informazioni su Backup di Azure, vedere questa [panoramica](backup-introduction-to-azure-backup.md).

Per eseguire il backup di file e cartelle in Azure, è necessario eseguire queste attività:

![Passaggio 1](./media/backup-try-azure-backup-in-10-mins/step-1.png) Ottenere una sottoscrizione di Azure (se non se ne possiede già una).<br> ![Passaggio 2](./media/backup-try-azure-backup-in-10-mins/step-2.png) Creare un insieme di credenziali di Servizi di ripristino.<br> ![Passaggio 3](./media/backup-try-azure-backup-in-10-mins/step-3.png) Scaricare i file necessari.<br> ![Passaggio 4](./media/backup-try-azure-backup-in-10-mins/step-4.png) Installare e registrare l'agente di Servizi di ripristino.<br> ![Passaggio 5](./media/backup-try-azure-backup-in-10-mins/step-5.png) Eseguire il backup dei file e delle cartelle.

![Come eseguire il backup di computer Windows con Backup di Azure](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

## Passaggio 1: Ottenere una sottoscrizione di Azure

Se non è disponibile una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) che consente di accedere a qualsiasi servizio di Azure.

## Passaggio 2: Creare l'insieme di credenziali di Servizi di ripristino

Per eseguire il backup dei file e delle cartelle, è necessario creare un insieme di credenziali di Servizi di ripristino nell'area in cui archiviare i dati. È anche necessario determinare la modalità di replica di archiviazione.

### Per creare un insieme di credenziali di Servizi di ripristino

1. Se questa operazione non è già stata eseguita, accedere al [portale di Azure](https://portal.azure.com/), tramite la sottoscrizione di Azure.

2. Scegliere **Sfoglia** dal menu Hub e nell'elenco di risorse digitare **Servizi di ripristino** e fare clic su **Insiemi di credenziali dei servizi di ripristino**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-try-azure-backup-in-10-mins/browse-to-rs-vaults.png) <br/>

3. Scegliere **Aggiungi** dal menu **Insiemi di credenziali dei servizi di ripristino**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    Si apre il pannello degli insiemi di credenziali dei servizi di ripristino in cui viene chiesto di specificare **Nome**, **Sottoscrizione**, **Gruppo di risorse** e **Località**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 5](./media/backup-try-azure-backup-in-10-mins/rs-vault-attributes.png)

4. In **Nome** immettere un nome descrittivo per identificare l'insieme di credenziali.

5. Fare clic su **Sottoscrizione** per visualizzare l'elenco di sottoscrizioni disponibili.

6. Fare clic su **Gruppo di risorse** per visualizzare l'elenco di gruppi di risorse disponibili oppure fare clic su **Nuovo** per crearne uno nuovo.

7. Fare clic su **Località** per selezionare l'area geografica per l'insieme di credenziali. La scelta determina l'area geografica in cui vengono inviati i dati di backup.

8. Fare clic su **Crea**.

    Se al termine l'insieme di credenziali non viene elencato, fare clic su **Aggiorna**. Quando l'elenco viene aggiornato, fare clic sul nome dell'insieme di credenziali.

### Per determinare la ridondanza di archiviazione
Quando si crea per la prima volta un insieme di credenziali di Servizi di ripristino, si determina come replicare lo spazio di archiviazione.

1. Fare clic sul nuovo insieme di credenziali per aprire il dashboard.

2. Nel pannello **Impostazioni**, che viene visualizzato automaticamente con il dashboard dell'insieme di credenziali, fare clic su **Infrastruttura di backup**.

3. Nel pannello Infrastruttura di backup fare clic su **Configurazione backup** per visualizzare il **Tipo di replica di archiviazione**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 5](./media/backup-try-azure-backup-in-10-mins/backup-infrastructure.png)

4. Scegliere l'opzione di replica di archiviazione appropriata per l'insieme di credenziali.

    ![Elenco di insiemi di credenziali dei Servizi di ripristino](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Per impostazione predefinita, l'insieme di credenziali prevede l'archiviazione con ridondanza geografica. Se si usa Azure come endpoint primario di archiviazione dei backup, continuare a usare l'archiviazione con ridondanza geografica. Se si usa Azure come endpoint non primario di archiviazione dei backup, scegliere l'archiviazione con ridondanza locale, che consente di ridurre i costi di archiviazione dei dati in Azure. Per altre informazioni sulle opzioni di archiviazione [con ridondanza geografica](../storage/storage-redundancy.md#geo-redundant-storage) e [con ridondanza locale](../storage/storage-redundancy.md#locally-redundant-storage), vedere questa [panoramica](../storage/storage-redundancy.md).

Ora che è stato creato un insieme di credenziali, preparare l'infrastruttura per il backup di file e cartelle scaricando l'agente di Servizi di ripristino di Microsoft Azure e le credenziali dell'insieme di credenziali.

## Passaggio 3: Scaricare file

1. Fare clic su **Impostazioni** nel dashboard dell'insieme di credenziali di Servizi di ripristino.

    ![Aprire il Pannello Obiettivo di backup](./media/backup-try-azure-backup-in-10-mins/settings-button.png)

2. Fare clic su **Attività iniziali > Backup** nel pannello Impostazioni.

    ![Aprire il Pannello Obiettivo di backup](./media/backup-try-azure-backup-in-10-mins/getting-started-backup.png)

3. Fare clic su **Obiettivo di backup** nel pannello Backup.

    ![Aprire il Pannello Obiettivo di backup](./media/backup-try-azure-backup-in-10-mins/backup-goal.png)

4. Scegliere **Locale** dal menu Dove è in esecuzione il carico di lavoro?

5. Scegliere **File e cartelle** dal menu Di che cosa si vuole eseguire il backup? e fare clic su **OK**.

### Scaricare l'agente di Servizi di ripristino

1. Fare clic su **Scarica agente per Windows Server o client Windows** nel pannello **Preparare l'infrastruttura**.

    ![Preparare l'infrastruttura](./media/backup-try-azure-backup-in-10-mins/prepare-infrastructure-short.png)

2. Fare clic su **Salva** nel popup del download. Per impostazione predefinita, il file **MARSagentinstaller.exe** viene salvato nella cartella Downloads.

### Scaricare le credenziali dell’insieme di credenziali

1. Fare clic su **Scarica > Salva** nel pannello Prepara infrastruttura.

    ![Preparare l'infrastruttura](./media/backup-try-azure-backup-in-10-mins/prepare-infrastructure-download.png)

## Passaggio 4: Installare e registrare l'agente

>[AZURE.NOTE] L'abilitazione del backup tramite il portale di Azure verrà illustrata tra poco. Ora invece si userà l'agente di Servizi di ripristino di Microsoft Azure in locale per eseguire il backup dei file e delle cartelle.

1. Trovare e fare doppio clic su **MARSagentinstaller.exe** nella cartella Download o in un'altra posizione in cui è stato salvato.

2. Completare l'Installazione guidata di Agente servizi di ripristino di Microsoft Azure. Per completare la procedura guidata, è necessario:

    - Scegliere un percorso per la cartella di installazione e della cache.
    - Fornire le informazioni sul server proxy se si usa un server proxy per connettersi a Internet.
    - Se si usa un proxy autenticato, immettere il nome utente e la password.
    - Fornire le credenziali dell'insieme di credenziali scaricate.
    - Salvare la passphrase di crittografia in un luogo sicuro.

    >[AZURE.NOTE] Se la passphrase viene persa o dimenticata, Microsoft non potrà offrire assistenza per il recupero dei dati di backup. Salvare il file in una posizione sicura. È necessario per ripristinare un backup.

L'agente ora è installato e il computer è registrato nell'insieme di credenziali. Ora è possibile configurare e pianificare il backup.

## Passaggio 5: Eseguire il backup di file e cartelle

Il backup iniziale comprende due attività fondamentali:

- Pianificare il backup
- Eseguire il backup di file e cartelle per la prima volta

Per completare il backup iniziale, si usa l'agente di Servizi di ripristino di Microsoft Azure.

### Per pianificare il backup

1. Aprire l'agente di Servizi di ripristino di Microsoft Azure. È possibile trovarlo se si cerca **Backup di Microsoft Azure** nel computer.

    ![Avviare l'agente di Servizi di ripristino di Azure](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. Nell'agente di Servizi di ripristino fare clic su **Pianifica backup**.

    ![Pianificare un backup di Windows Server](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. Nella pagina Guida introduttiva della Pianificazione guidata backup fare clic su **Avanti**.

4. Nella pagina Seleziona elementi per backup fare clic su **Aggiungi elementi**.

5. Selezionare i file e le cartelle di cui si vuole eseguire il backup e fare clic su **OK**.

6. Fare clic su **Avanti**.

7. Nella pagina **Specifica la pianificazione del backup** specificare la **pianificazione del backup** e fare clic su **Avanti**.

    È possibile pianificare backup giornalieri, da eseguire non più di tre volte al giorno, o settimanali.

    ![Elementi per il backup di Windows Server](./media/backup-try-azure-backup-in-10-mins/specify-backup-schedule-close.png)

    >[AZURE.NOTE] Per altre informazioni su come specificare la pianificazione del backup vedere l'articolo [Usare Backup di Azure per sostituire l'infrastruttura basata su nastro](backup-azure-backup-cloud-as-tape.md).

8. Nella pagina **Seleziona criteri di conservazione** selezionare i **criteri di conservazione** per la copia di backup.

    I criteri di conservazione specificano il periodo di tempo per cui il backup verrà archiviato. Anziché specificare solo un "criterio semplice" per tutti i punti di backup, è possibile specificare criteri di conservazione diversi in base al momento in cui viene eseguito il backup. È possibile modificare i criteri di conservazione giornalieri, settimanali, mensili e annuali in base alle proprie esigenze.

9. Nella pagina Scegliere il tipo di backup iniziale selezionare il tipo di backup iniziale. Lasciare selezionata l'opzione **Automaticamente tramite la rete** e fare clic su **Avanti**.

    È possibile eseguire il backup automaticamente in rete oppure offline. Il resto di questo articolo descrive il processo di backup automatico. Se si preferisce eseguire un backup offline, vedere l'articolo [Flusso di lavoro di backup offline in Backup di Azure](backup-azure-backup-import-export.md) per altre informazioni.

10. Nella pagina Conferma esaminare le informazioni e fare clic su **Fine**.

11. Dopo aver creato la pianificazione del backup tramite la procedura guidata, fare clic su **Chiudi**.

### Per eseguire il backup di file e cartelle per la prima volta

1. Nell'agente di Servizi di ripristino fare clic su **Esegui backup** per completare il seeding iniziale sulla rete.

    ![Eseguire ora il backup di Windows Server](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

2. Nella pagina Conferma riesaminare le impostazioni che l'Esecuzione guidata backup userà per il backup del computer. Fare clic su **Backup**.

3. Fare clic su **Chiudi** per chiudere la procedura guidata. Se quest'operazione viene svolta prima che venga completato il processo di backup, l'esecuzione guidata proseguirà in background.

Al termine del backup iniziale, nella console Backup comparirà lo stato **Processo completato**.

![Completamento infrarossi](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## Domande?
In caso di domande o se si vuole che venga inclusa una funzionalità, è possibile [inviare commenti e suggerimenti](http://aka.ms/azurebackup_feedback).

## Passaggi successivi
- Sono disponibili altre informazioni sul [backup di computer Windows](backup-configure-vault.md).
- Ora che si è eseguito il backup dei file e delle cartelle, è possibile [gestire l'insieme di credenziali e i server](backup-azure-manage-windows-server.md).
- Se è necessario ripristinare un backup, usare questo articolo per [ripristinare i file in un computer Windows](backup-azure-restore-windows-server.md).

<!---HONumber=AcomDC_0928_2016-->