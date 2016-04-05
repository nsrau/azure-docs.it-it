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
	 ms.date="03/28/2016"
	 ms.author="jimpark;"/>

# Primi passi: eseguire il backup di file e cartelle da un server o client Windows in Azure

Questo articolo illustra come eseguire il backup di file e cartelle di Windows Server o di un client Windows in Azure. Backup di Azure è estremamente facile da usare.

Per il backup di file e cartelle è sufficiente seguire questa semplice procedura:

![Passaggio 1](./media/backup-try-azure-backup-in-10-mins/step-1.png) Ottenere una sottoscrizione di Azure, se necessario.<br> ![Passaggio 2](./media/backup-try-azure-backup-in-10-mins/step-2.png) Creare un insieme di credenziali di backup e scaricare i componenti necessari.<br> ![Passaggio 3](./media/backup-try-azure-backup-in-10-mins/step-3.png) Preparare il server o client Windows tramite l'installazione e la registrazione dei componenti.<br> ![Passaggio 4](./media/backup-try-azure-backup-in-10-mins/step-4.png) Eseguire il backup dei dati.


![Come eseguire il backup di computer Windows con Backup di Azure](./media/backup-try-azure-backup-in-10-mins/windows-machine-backup-process.png)

## Passaggio 1: Ottenere una sottoscrizione di Azure

Se non è disponibile una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free/) che consente di accedere a qualsiasi servizio Azure.

>[AZURE.NOTE] Se è già disponibile una sottoscrizione di Azure, è possibile ignorare questo passaggio.

## Passaggio 2: Creare un insieme di credenziali di backup e scaricare i componenti necessari

Per eseguire il backup di file e cartelle è necessario creare un insieme di credenziali di backup nell'area geografica in cui archiviare i dati.

1. Accedere al [portale di Azure](https://portal.azure.com/), se questa operazione non è già stata eseguita, usando la sottoscrizione di Azure.

2. Fare clic su **Nuovo > Integrazione ibrida > Backup**.

    ![Iniziare a prepararsi per eseguire il backup di file e cartelle](./media/backup-try-azure-backup-in-10-mins/second-blade-backup.png)

    a. Nella schermata visualizzata per **Nome** immettere un nome descrittivo per identificare l'insieme di credenziali di backup. Digitare un nome che contenga tra i 2 e i 50 caratteri. Deve iniziare con una lettera e può contenere solo lettere, numeri e trattini.

    b. Per **Area** selezionare l'area geografica per l'insieme di credenziali per il backup. Scegliendo un'area geografica vicina alla propria posizione, è possibile ridurre la latenza di rete durante il backup in Azure.

    c. Fare clic su **Crea insieme di credenziali**.

    ![Creare un insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/demo-vault-name.png)

    Per lo stato è possibile monitorare le notifiche nella parte inferiore del portale.

    ![Processo dell'insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/creatingvault1.png)

    L'insieme di credenziali di backup appena creato viene elencato tra le risorse per Servizi di ripristino come **Attivo**.

    ![Lo stato dell'insieme di credenziali è attivo](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

3. Selezionare le opzioni di **ridondanza di archiviazione**.

    È consigliabile selezionare l'opzione per la ridondanza dell'archiviazione subito dopo la creazione di un insieme di credenziali e prima che i computer vengano registrati nell'insieme di credenziali. Dopo la registrazione di un elemento nell'insieme di credenziali, l'opzione di ridondanza di archiviazione è bloccata e non può essere modificata.

    Se si usa Azure come endpoint primario di archiviazione dei backup, è consigliabile scegliere l'opzione di [archiviazione con ridondanza geografica](../storage/storage-redundancy.md#geo-redundant-storage).

    Se si usa Azure come endpoint terziario di archiviazione dei backup, è consigliabile scegliere l'opzione di [archiviazione con ridondanza locale](../storage/storage-redundancy.md#locally-redundant-storage). Questa opzione permette di ridurre i costi di archiviazione dei dati in Azure e offre un livello di durabilità inferiore che può essere accettabile per le copie terziarie.

    a. Fare clic sull'insieme di credenziali appena creato.

    b. Nella pagina Avvio rapido selezionare **Configura**.

    ![Configurare l'insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/configure-vault.png)

    c. Scegliere l'opzione per la ridondanza di archiviazione appropriata.

    ![Scegliere l'opzione di ridondanza di archiviazione](./media/backup-try-azure-backup-in-10-mins/geo-redundant.png)

    Se è stata selezionata l'opzione **Con ridondanza locale**, è necessario fare clic su **Salva** perché l'opzione predefinita è **Con ridondanza geografica**.

    d. Per tornare all'elenco di risorse per i **Servizi di ripristino**, fare clic su **Servizi di ripristino** nel riquadro di spostamento sinistro.

    ![Servizi di ripristino](./media/backup-try-azure-backup-in-10-mins/rs-left-nav.png)

    Ora è necessario autenticare il computer Windows con l'insieme di credenziali di backup appena creato. L'autenticazione viene eseguita mediante le credenziali dell'insieme di credenziali.

4.  Fare clic sull'insieme di credenziali appena creato.

    ![Selezionare il nuovo insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/demo-vault-active-full-screen.png)

5. Nella pagina **Avvio rapido** fare clic su **Scarica credenziali dell'insieme di credenziali**.

    ![Scaricare le credenziali dell'insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/downloadvc.png)

    Il portale genera una credenziale di insieme usando una combinazione costituita dal nome dell'insieme e dalla data corrente.

    >[AZURE.NOTE] Il file delle credenziali dell'insieme di credenziali viene usato solo durante il flusso di lavoro di registrazione e scade dopo 48 ore.

6. Fare clic su **Salva** per scaricare le credenziali dell'insieme di credenziali nella cartella **Download** locale. È anche possibile selezionare **Salva con nome** nel menu **Salva** per specificare un percorso per le credenziali dell'insieme di credenziali.

    Assicurarsi che le credenziali dell'insieme di credenziali vengano salvate in un percorso accessibile dal computer. Se il file viene archiviato in una condivisione file/SMB, verificare le autorizzazioni di accesso.

    >[AZURE.NOTE] Non è necessario aprire le credenziali dell'insieme di credenziali a questo punto della procedura.

    Ora è necessario scaricare l'agente di backup.

7. Fare clic su **Servizi di ripristino** nel riquadro di spostamento sinistro e quindi fare clic sull'insieme di credenziali di backup da registrare con un server.

    ![Selezionare l'insieme di credenziali di backup](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

8. Nella pagina Avvio rapido fare clic su **Agente per Windows Server, System Center Data Protection Manager o client Windows > Salva**.

    ![Salvare l'agente di backup](./media/backup-try-azure-backup-in-10-mins/agent.png)

A questo punto, la creazione di un insieme di credenziali di backup e il download dei componenti necessari sono stati completati. Ora viene installato l'agente di backup.

## Passaggio 3: Preparare il server o client Windows tramite l'installazione e la registrazione dei componenti di backup

1. Dopo aver completato il download, fare doppio clic su **MARSagentinstaller.exe** nel percorso in cui è stato salvato. In alternativa è possibile fare clic su **Esegui** anziché salvare il file nel passaggio precedente.

2. Scegliere la **cartella di installazione** e la **cartella cache** necessarie per l'agente.

    Il percorso della cache specificato deve avere uno spazio disponibile pari almeno al 5% dei dati di backup.

    Fare clic su **Avanti**.

    ![Cartella di installazione e della cache](./media/backup-try-azure-backup-in-10-mins/recovery-services-agent-setup-wizard-1.png)

3. (Facoltativo) Se si usa un server proxy per connettersi a Internet, nella schermata **Configurazione proxy** selezionare la casella di controllo **Utilizzare le impostazioni personalizzate del proxy** e immettere i dettagli del server proxy.

    Se si usa un proxy autenticato, immettere il nome utente e la password.

    Fare clic su **Avanti**.

    ![Configurazione proxy](./media/backup-try-azure-backup-in-10-mins/proxy-configuration.png)

4. Fare clic su **Installa**.

    Per completare l'installazione, l'agente Backup di Azure installerà .NET Framework 4.5 e Windows PowerShell (se non sono già installati).

5. Dopo aver installato l'agente, fare clic su **Continua con la registrazione** per continuare con il flusso di lavoro.

    ![Registra](./media/backup-try-azure-backup-in-10-mins/register.png)

6. Nella schermata **Identificazione insieme di credenziali** trovare e selezionare il **file delle credenziali dell'insieme di credenziali** scaricato in precedenza. Verificare che il file delle credenziali dell'insieme di credenziali sia disponibile in un percorso accessibile dall'applicazione di installazione.

    Fare clic su **Avanti**.

    ![Identificare l'insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/vault-identification-with-creds.png)

7. Nella schermata **impostazione crittografia**, è possibile generare una passphrase o fornire una passphrase (almeno 16 caratteri). Ricordarsi di salvare la passphrase in un luogo sicuro.

    > [AZURE.WARNING] Se la passphrase viene persa o dimenticata, Microsoft non potrà fornire assistenza per il recupero dei dati di backup. L'utente è proprietario della passphrase di crittografia, che non è visibile a Microsoft. Salvare il file in un luogo sicuro, in quanto potrebbe essere necessario durante un'operazione di ripristino.

    Fare clic su **Finish**.

    ![Impostazione di crittografia](./media/backup-try-azure-backup-in-10-mins/encryption.png)

    La **Registrazione guidata server** registra il server con Backup di Microsoft Azure.

8. Dopo aver impostato la **chiave di crittografia**, lasciare selezionata la casella di controllo **Avvia agente di Servizi di ripristino di Microsoft Azure** e fare clic su **Chiudi**.

    Il computer ora risulta registrato nell'insieme di credenziali ed è possibile configurare e pianificare le opzioni di backup.

## Passaggio 4: Eseguire il backup dei dati

1. Nell'**agente di backup** che viene aperto automaticamente se si lascia selezionata la casella di controllo **Avvia agente di Servizi di ripristino di Microsoft Azure** fare clic su **Pianifica backup**.

    ![Pianificare un backup di Windows Server](./media/backup-try-azure-backup-in-10-mins/snap-in-schedule-backup-closeup.png)

2. Nella schermata **Attività iniziali** fare clic su **Avanti**.

3. Nella schermata **Seleziona elementi per backup** fare clic su **Aggiungi elementi**. Backup di Azure in un server o client Windows consente di proteggere file e cartelle.

    ![Pianificare un backup di Windows Server](./media/backup-try-azure-backup-in-10-mins/add-items.png)

    Selezionare gli elementi da sottoporre a backup e fare clic su **OK**.

    Fare clic su **Avanti**.

4. Specificare la **pianificazione del backup** e fare clic su **Avanti**.

    È possibile pianificare backup giornalieri, da eseguire non più di tre volte al giorno, o settimanali.

    ![Specificare la pianificazione dei backup](./media/backup-try-azure-backup-in-10-mins/specify-backup-schedule.png)

5. Selezionare i **criteri di conservazione** per la copia di backup. È possibile modificare i criteri di conservazione giornalieri, settimanali, mensili e annuali in base alle proprie esigenze.

    >[AZURE.NOTE] La pianificazione del backup è illustrata in dettaglio in questo [articolo](backup-azure-backup-cloud-as-tape.md).

     Fare clic su **Avanti**

    ![Selezionare i criteri di conservazione](./media/backup-try-azure-backup-in-10-mins/select-retention-policy.png)

6. Scegliere il tipo di backup iniziale.

    È possibile eseguire il backup automaticamente in rete oppure offline. Il resto di questo articolo segue il processo di backup automatico. Se si preferisce eseguire un backup offline, vedere questo articolo per altre informazioni sul [flusso di lavoro di backup offline in Backup di Azure](backup-azure-backup-import-export.md).

    Fare clic su **Avanti**

    ![Tipo di backup iniziale](./media/backup-try-azure-backup-in-10-mins/choose-initial-backup-type.png)

7. Nella schermata **Conferma** riesaminare le informazioni e fare clic su **Fine**.

8. Al termine della creazione della **pianificazione del backup** da parte della procedura guidata, fare clic su **Chiudi**.

9. Nell'**agente di backup** fare clic su **Esegui backup** per completare il seeding iniziale sulla rete.

    ![Eseguire ora il backup di Windows Server](./media/backup-try-azure-backup-in-10-mins/snap-in-backup-now.png)

10. Nella schermata **Conferma** riesaminare le impostazioni che la procedura guidata userà per il backup del computer e fare clic su **Backup**.

11. Fare clic su **Chiudi** per chiudere la procedura guidata. È possibile chiudere la procedura guidata prima che venga completato il processo di backup, che continuerà in background.

12. Dopo aver completato il backup iniziale, la visualizzazione **Processi** nella console di Backup di Azure indica lo stato "Processo completato".

    ![Backup iniziale completato](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

Il backup dei file e delle cartelle è stato eseguito in Backup di Azure.

## Passaggi successivi
- Per ulteriori informazioni sul servizio Backup di Azure, vedere [Panoramica di Azure Backup](backup-introduction-to-azure-backup.md).
- Sono disponibili altre informazioni sulla [preparazione dell'ambiente e il backup di computer Windows](backup-configure-vault.md).
- Per informazioni su come eseguire il backup di macchine virtuali di Azure, vedere l'articolo relativo al [primo approccio](backup-azure-vms-first-look.md).
- Leggere le discussioni più recenti su Backup di Azure nel [forum di Backup di Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=AcomDC_0330_2016-->