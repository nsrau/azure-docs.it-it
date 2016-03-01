<properties
   pageTitle="Primo approccio: Come eseguire il backup di un server o di un client Windows in Azure | Microsoft Azure"
   description="Informazioni su come eseguire il backup di un server Windows usando Backup di Azure con questi semplici passaggi"
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
	 ms.date="02/21/2016"
	 ms.author="jimpark;"/>

# Eseguire il backup di un client o server Windows in Azure

Bastano pochi passaggi per eseguire il backup del computer Windows (client Windows o server Windows) in Azure. Con i 4 passaggi seguenti si eseguiranno queste operazioni:

- Configurazione di una sottoscrizione di Azure (se necessario).
- Creazione di un insieme di credenziali di backup e download dei componenti necessari.
- Preparazione del server o client Windows tramite l'installazione e la registrazione di tali componenti.
- Backup dei dati.

## Passaggio 1: Ottenere una sottoscrizione di Azure

Se non è disponibile una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free/) che consente di accedere a qualsiasi servizio Azure.

>[AZURE.NOTE] Se è già disponibile una sottoscrizione di Azure, è possibile ignorare questo passaggio.

## Passaggio 2: Creare un insieme di credenziali per il backup

Per eseguire il backup di file e dati da un computer Windows ad Azure, è necessario creare un insieme di credenziali per il backup nell'area geografica in cui archiviare i dati.

1. Accedere al [portale di Azure](https://portal.azure.com/), se questa operazione non è già stata eseguita, usando la sottoscrizione di Azure.

2. Fare clic su **Nuovo > Integrazione ibrida > Backup**.

    ![Crea insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/second-blade-backup.png)

    a. Nella schermata visualizzata per **Nome** immettere un nome descrittivo per identificare l'insieme di credenziali di backup. Digitare un nome che contenga tra i 2 e i 50 caratteri. Deve iniziare con una lettera e può contenere solo lettere, numeri e trattini.

    b. Per **Area** selezionare l'area geografica per l'insieme di credenziali per il backup. Scegliendo un'area geografica vicina alla propria posizione, è possibile ridurre la latenza di rete durante il backup in Azure.

    c. Fare clic su **Crea insieme di credenziali**.

    ![Crea insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/demo-vault-name.png)

    Per lo stato è possibile monitorare le notifiche nella parte inferiore del portale.

    ![Creazione dell'insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/creatingvault1.png)

    Una volta creato, l'insieme di credenziali di backup verrà elencato nelle risorse per Servizi di ripristino come **Attivo**.

    ![Creazione dello stato dell'insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

3. Selezionare le opzioni per la **ridondanza di archiviazione**.

    È consigliabile selezionare l'opzione per la ridondanza dell'archiviazione subito dopo la creazione di un insieme di credenziali e prima che i computer vengano registrati nell'insieme di credenziali. Dopo la registrazione di un elemento nell'insieme di credenziali, l'opzione di ridondanza di archiviazione è bloccata e non può essere modificata.

    Se si usa Azure come endpoint primario di archiviazione dei backup (se ad esempio si esegue il backup in Azure da Windows Server), è consigliabile scegliere l'opzione predefinita di archiviazione con ridondanza geografica.

    Se si usa Azure come endpoint terziario di archiviazione dei backup (se, ad esempio, si usa SCDPM per avere una copia locale del backup e si usa Azure per la conservazione a lungo termine), è consigliabile scegliere l'archiviazione con ridondanza locale. In questo modo vengono ridotti i costi di archiviazione dei dati in Azure e viene offerta una durabilità dei dati inferiore che può essere accettabile per le copie terziarie.

    a. Fare clic sull'insieme di credenziali appena creato.

    b. Nella pagina Avvio rapido selezionare **Configura**.

    ![Configurazione dello stato dell'insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/configure-vault.png)

    c. Scegliere l'opzione per la ridondanza di archiviazione appropriata.

    Sarà necessario fare clic su **Salva** se è stato selezionato **Con ridondanza locale**, perché **Con ridondanza geografica** è l'opzione predefinita.

    ![Archiviazione con ridondanza geografica](./media/backup-try-azure-backup-in-10-mins/geo-redundant.png)

    >[AZURE.NOTE] Per altre informazioni sulle opzioni per la ridondanza di archiviazione, vedere questo articolo sulla [panoramica](../storage/storage-redundancy.md).

    d. Fare clic su **Servizi di ripristino** nel riquadro di spostamento sinistro per tornare all'elenco di risorse per **Servizi di ripristino**.

    ![Selezione dell'insieme di credenziali di backup](./media/backup-try-azure-backup-in-10-mins/rs-left-nav.png)

    Il computer Windows ora deve essere autenticato con l'insieme di credenziali di backup appena creato. L'autenticazione viene eseguita mediante le credenziali dell'insieme di credenziali.

4.  Fare clic sull'insieme di credenziali appena creato.

    ![Creazione dello stato dell'insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/demo-vault-active-full-screen.png)

5. Nella pagina **Avvio rapido** fare clic su **Scarica credenziali dell'insieme di credenziali**.

    ![Scaricare](./media/backup-try-azure-backup-in-10-mins/downloadvc.png)

    Il portale genererà una credenziale di insieme usando una combinazione del nome dell'insieme di credenziali e della data attuale.

    >[AZURE.NOTE] Il file delle credenziali dell'insieme di credenziali viene usato solo durante il flusso di lavoro di registrazione e scade dopo 48 ore.

6. Fare clic su **Salva** per scaricare le credenziali dell'insieme di credenziali nella cartella **Download** dell'account locale. In alternativa, scegliere **Salva con nome** dal menu **Salva** per specificare un percorso per le credenziali dell'insieme di credenziali.

    ![Selezione dell'insieme di credenziali di backup](./media/backup-try-azure-backup-in-10-mins/save.png)

    Assicurarsi che le credenziali dell'insieme di credenziali vengano salvate in un percorso accessibile dal computer. Se il file viene archiviato in una condivisione file/SMB, verificare le autorizzazioni di accesso.

    >[AZURE.NOTE] Non è necessario aprire ora le credenziali dell'insieme di credenziali.

    Ora è necessario scaricare l'agente di backup.

7. Fare clic su **Servizi di ripristino** nel riquadro di spostamento sinistro e quindi fare clic sull'insieme di credenziali per il backup che si vuole registrare con un server.

    ![Selezione dell'insieme di credenziali di backup](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

8. Nella pagina Avvio rapido fare clic su **Agente per Windows Server, System Center Data Protection Manager o client Windows > Salva** (oppure scegliere **Salva con nome** dal menu **Salva** per specificare un percorso per l'agente).

    ![Salva agente](./media/backup-try-azure-backup-in-10-mins/agent.png)

A questo punto, la creazione di un insieme di credenziali di backup e il download dei componenti necessari sono stati completati. Ora si installerà l'agente di backup.

## Passaggio 3: Installare l'agente di Backup di Azure nel server o nel client Windows

1. Al termine del download, fare doppio clic su **MARSagentinstaller.exe** nel percorso in cui è stato salvato (oppure fare clic su **Esegui** invece di salvare il file nel passaggio precedente).

2. Scegliere la **cartella di installazione** e la **cartella della cache** necessarie per l'agente.

    Il percorso della cache specificato deve avere uno spazio disponibile pari almeno al 5% dei dati di backup.

    Fare clic su **Avanti**.

    ![Cartella della cache](./media/backup-try-azure-backup-in-10-mins/recovery-services-agent-setup-wizard-1.png)

3. È possibile continuare a connettersi a Internet con le impostazioni proxy predefinite o, se si usa un server proxy per connettersi a Internet, nella schermata **Configurazione proxy** selezionare la casella di controllo **Utilizzare le impostazioni personalizzate del proxy** e immettere i dettagli del server proxy.

    Se si usa un proxy autenticato, immettere il nome utente e la password.

    Fare clic su **Avanti**.

    ![Configurazione proxy](./media/backup-try-azure-backup-in-10-mins/proxy-configuration.png)

4. Fare clic su **Installa**.

    ![Configurazione proxy](./media/backup-try-azure-backup-in-10-mins/agent-installation.png)

    Per completare l'installazione, l'agente Backup di Azure installerà .NET Framework 4.5 e Windows PowerShell (se non sono già installati).

5. Dopo avere installato l'agente, fare clic su **Procedi alla registrazione** per continuare con il flusso di lavoro.

    ![Registra](./media/backup-try-azure-backup-in-10-mins/register.png)

6. Nella schermata **Identificazione insieme di credenziali** trovare e selezionare il **file delle credenziali dell'insieme di credenziali** scaricato prima. Verificare che il file delle credenziali dell'insieme di credenziali sia disponibile in un percorso accessibile dall'applicazione di installazione.

    Fare clic su **Avanti**.

    ![Credenziali di insieme](./media/backup-try-azure-backup-in-10-mins/vault-identification-with-creds.png)

7. Nella schermata **impostazione crittografia**, è possibile generare una passphrase o fornire una passphrase (almeno 16 caratteri). Ricordarsi di salvare la passphrase in un luogo sicuro.

    > [AZURE.WARNING] Se la passphrase viene persa o dimenticata, Microsoft non potrà fornire assistenza per il recupero dei dati di backup. L'utente finale possiede la passphrase di crittografia, che non è visibile a Microsoft. Salvare il file in un luogo sicuro, in quanto potrebbe essere necessario durante un'operazione di ripristino.

    Fare clic su **Finish**.

    ![Crittografia](./media/backup-try-azure-backup-in-10-mins/encryption.png)

    La **Registrazione guidata server** registra il server con Backup di Microsoft Azure.

    ![Crittografia](./media/backup-try-azure-backup-in-10-mins/registering-server.png)

8. Una volta impostata la **chiave di crittografia**, lasciare selezionata la casella di controllo **Avvia agente di Servizi di ripristino di Microsoft Azure** e fare clic su**Chiudi**.

    ![Crittografia](./media/backup-try-azure-backup-in-10-mins/close-server-registration.png)

    Il computer ora risulta registrato nell'insieme di credenziali ed è possibile configurare e pianificare le opzioni di backup.

## Passaggio 4: Eseguire il backup di file e cartelle

1. Nello snap-in MMC (che si apre automaticamente se si lascia selezionata la casella di controllo **Avvia agente di Servizi di ripristino di Microsoft Azure**) fare clic su **Pianifica backup**.

    ![Pianificare un backup di Windows Server](./media/backup-try-azure-backup-in-10-mins/snap-in-schedule-backup-closeup.png)

2. Nella schermata **Attività iniziali** fare clic su **Avanti**.

    ![Pianificare un backup di Windows Server](./media/backup-try-azure-backup-in-10-mins/getting-started.png)

3. Nella schermata **Seleziona elementi per backup** fare clic su **Aggiungi elementi**.

    ![Pianificare un backup di Windows Server](./media/backup-try-azure-backup-in-10-mins/add-items.png)

    Selezionare gli elementi da sottoporre a backup e fare clic su **OK**. Azure Backup in un server o client Windows consente di proteggere file e cartelle.

    ![Elementi per il backup di Windows Server](./media/backup-try-azure-backup-in-10-mins/added-items.png)

    Fare clic su **Avanti**.

    ![Elementi per il backup di Windows Server](./media/backup-try-azure-backup-in-10-mins/selected-items.png)

4. Specificare la **pianificazione backup** e fare clic su **Avanti**.

    È possibile pianificare backup giornalieri (non più di 3 al giorno) o settimanali.

    ![Elementi per il backup di Windows Server](./media/backup-try-azure-backup-in-10-mins/specify-backup-schedule.png)

5. Selezionare i **criteri di conservazione** per la copia di backup. È possibile modificare i criteri di conservazione giornalieri, settimanali, mensili e annuali in base alle proprie esigenze.

    >[AZURE.NOTE] La pianificazione del backup è illustrata in dettaglio in questo [articolo](backup-azure-backup-cloud-as-tape.md).

     Fare clic su **Avanti**

    ![Elementi per il backup di Windows Server](./media/backup-try-azure-backup-in-10-mins/select-retention-policy.png)

6. Scegliere il tipo di backup iniziale.

    È possibile eseguire il backup automaticamente in rete oppure offline. Il resto di questo articolo segue il processo di backup automatico. Se si preferisce eseguire un backup offline, vedere questo articolo per altre informazioni sul [flusso di lavoro di backup offline in Azure Backup](backup-azure-backup-import-export.md).

    Fare clic su **Avanti**

    ![Backup iniziale di Windows Server](./media/backup-try-azure-backup-in-10-mins/choose-initial-backup-type.png)

7. Nella schermata **Conferma** riesaminare le informazioni e fare clic su **Fine**.

    ![Backup iniziale di Windows Server](./media/backup-try-azure-backup-in-10-mins/confirmation.png)

8. Quando la procedura guidata termina la creazione della **pianificazione backup**, fare clic su **Chiudi**.

    ![Backup iniziale di Windows Server](./media/backup-try-azure-backup-in-10-mins/backup-schedule-created.png)

9. Nello snap-in MMC fare clic su **Esegui backup** per completare il seeding iniziale sulla rete.

    ![Eseguire ora il backup di Windows Server](./media/backup-try-azure-backup-in-10-mins/snap-in-backup-now.png)

10. Nella schermata **Conferma** riesaminare le impostazioni che la procedura guidata userà per il backup del computer e fare clic su **Backup**.

    ![Eseguire ora il backup di Windows Server](./media/backup-try-azure-backup-in-10-mins/backup-now-confirmation.png)

11. Fare clic su **Chiudi** per chiudere la procedura guidata. È possibile eseguire questa operazione prima che venga completato il **processo di backup** che continuerà a essere eseguito in background.

    ![Eseguire ora il backup di Windows Server](./media/backup-try-azure-backup-in-10-mins/backup-progress.png)

12. Dopo aver completato il backup iniziale, la visualizzazione **Processi** nella console di Backup di Azure indica lo stato "Processo completato".

    ![Completamento infrarossi](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

Il backup dei file e delle cartelle è stato eseguito in Backup di Azure.

## Passaggi successivi
- Per altre informazioni sul servizio Backup di Azure, vedere [Panoramica di Backup di Azure](backup-introduction-to-azure-backup.md)
- Sono disponibili altre informazioni sulla [preparazione dell'ambiente per il backup di computer Windows](backup-configure-vault.md)
- Sono disponibili altre informazioni sul [backup di un server Windows](backup-azure-backup-windows-server.md)
- Visitare il [Forum su Backup di Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=AcomDC_0224_2016-->