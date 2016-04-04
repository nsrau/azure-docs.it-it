<properties
	pageTitle="Eseguire il backup di un server o di un client Windows in Azure | Microsoft Azure"
	description="Eseguire il backup di server o client Windows in Azure creando un insieme di credenziali di backup, scaricando le credenziali, installando l'agente di backup e completando un backup iniziale dei file e delle cartelle."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""
	keywords="insieme di credenziali di backup; backup di un server Windows; backup di Windows;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/19/2016"
	ms.author="jimpark; trinadhk; markgal"/>


# Eseguire il backup di un client o server Windows in Azure
Questo articolo illustra le procedure necessarie per preparare l'ambiente ed eseguire il backup di un server o un client Windows in Azure. Contiene anche considerazioni sulla distribuzione della soluzione di backup. Se si vuole provare Backup di Azure per la prima volta, [questo articolo](backup-configure-vault.md) illustra rapidamente come fare.

![Crea insieme di credenziali](./media/backup-configure-vault/initial-backup-process.png)

## Prima di iniziare
Per eseguire il backup di un server o un client Windows in Azure, è necessario un account Azure. Se non si ha un account, è possibile creare un [account gratuito](https://azure.microsoft.com/free/) in pochi minuti.

## Passaggio 1: Creare un insieme di credenziali di backup
Per eseguire il backup di file e cartelle da un server o un client Windows è necessario creare un insieme di credenziali di backup nell'area geografica in cui archiviare i dati.

### Per creare un insieme di credenziali di backup

1. Accedere al [portale di gestione](https://manage.windowsazure.com/).

2. Fare clic su **Nuovo** -> **Servizi dati** -> **Servizi di ripristino** -> **Insieme di credenziali per il backup** e scegliere **Creazione rapida**.

3. Per il parametro **Nome** immettere un nome descrittivo per identificare l'insieme di credenziali di backup. Digitare un nome che contenga tra i 2 e i 50 caratteri. Deve iniziare con una lettera e può contenere solo lettere, numeri e trattini. È necessario che il nome sia univoco per ogni sottoscrizione.

    Per il parametro **Area** selezionare l'area geografica per l'insieme di credenziali per il backup. La scelta determina l'area geografica in cui vengono inviati i dati di backup. Scegliendo un'area geografica vicina alla propria posizione, è possibile ridurre la latenza di rete durante il backup in Azure.

    Fare clic su **Crea insieme di credenziali**.

    ![Crea insieme di credenziali](./media/backup-configure-vault/demo-vault-name.png)

    La creazione dell'insieme di credenziali per il backup può richiedere alcuni minuti. Per verificare lo stato, è possibile monitorare le notifiche nella parte inferiore del portale.

    Dopo avere creato l'archivio di backup, verrà visualizzato un messaggio che indica che l'insieme di credenziali è stato creato correttamente. L'insieme di credenziali viene inoltre elencato nelle risorse di servizi di ripristino come**Active**.

    ![Creazione dello stato dell'insieme di credenziali](./media/backup-configure-vault/recovery-services-select-vault.png)

4. Selezionare l'opzione di **ridondanza di archiviazione**.

    >[AZURE.IMPORTANT] È consigliabile identificare l'opzione per la ridondanza dell'archiviazione subito dopo la creazione dell'insieme di credenziali e prima che i computer vengano registrati nell'insieme di credenziali. Dopo la registrazione di un elemento nell'insieme di credenziali, l'opzione di ridondanza di archiviazione è bloccata e non può essere modificata.

    Se si usa Azure come endpoint primario di archiviazione dei backup, ad esempio quando si esegue il backup in Azure da Windows Server, è consigliabile scegliere l'opzione predefinita di [archiviazione con ridondanza geografica](../storage/storage-redundancy.md#geo-redundant-storage).

    Se si usa Azure come endpoint terziario di archiviazione dei backup, ad esempio quando si usa System Center Data Protection Manager per avere una copia locale del backup e si usa Azure per la conservazione a lungo termine, è consigliabile scegliere l'[archiviazione con ridondanza locale](../storage/storage-redundancy.md#locally-redundant-storage). In questo modo vengono ridotti i costi di archiviazione dei dati in Azure e viene offerta una durabilità dei dati inferiore che può essere accettabile per le copie terziarie.

    **Per selezionare l'opzione di ridondanza di archiviazione:**

    a. Fare clic sull'insieme di credenziali appena creato.

    b. Nella pagina **Avvio rapido** selezionare **Configura**.

    ![Configurazione dello stato dell'insieme di credenziali](./media/backup-configure-vault/configure-vault.png)

    c. Scegliere l'opzione per la ridondanza di archiviazione appropriata.

    È necessario fare clic su **Salva** se è stata selezionata l'opzione **Con ridondanza locale**, perché **Con ridondanza geografica** è l'opzione predefinita.

    d. Fare clic su **Servizi di ripristino** nel riquadro di spostamento sinistro per tornare all'elenco di risorse per i **Servizi di ripristino**.

## Passaggio 2: Scaricare il file delle credenziali dell'insieme di credenziali
Per poter eseguire il backup dei dati in Azure è necessario autenticare il computer locale, server o client Windows, con un insieme di credenziali di backup. L'autenticazione viene eseguita con le *credenziali dell'insieme di credenziali*. Il file delle credenziali di insieme viene scaricato dal portale di Azure tramite un canale sicuro. Il servizio Backup di Azure non è a conoscenza della chiave privata del certificato, che non è persistente nel portale o nel servizio.

Sono disponibili altre informazioni sull'[uso delle credenziali dell'insieme di credenziali per l'autenticazione con il servizio Backup di Azure](backup-introduction-to-azure-backup.md#what-is-the-vault-credential-file).

### Per scaricare le credenziali dell'insieme di credenziali in un computer locale

1. Fare clic su **Servizi di ripristino** nel riquadro di spostamento sinistro e selezionare l'insieme di credenziali per il backup creato.

    ![Completamento infrarossi](./media/backup-configure-vault/rs-left-nav.png)

2.  Nella pagina **Avvio rapido** fare clic su **Scarica credenziali dell'insieme di credenziali**.

    Il portale genererà una credenziale di insieme usando una combinazione del nome dell'insieme di credenziali e della data attuale. Il file delle credenziali dell'insieme di credenziali viene usato solo durante il flusso di lavoro di registrazione e scade dopo 48 ore.

    Il file delle credenziali dell'insieme di credenziali può essere scaricato dal portale.

3. Fare clic su **Salva** per scaricare le credenziali di insieme nella cartella Download dell'account locale. In alternativa, scegliere **Salva con nome** dal menu **Salva** per specificare un percorso per le credenziali di insieme.

    >[AZURE.NOTE] Assicurarsi che il file delle credenziali dell'insieme di credenziali sia salvato in un percorso accessibile dal computer. Se vengano archiviate in una condivisione file/SMB, verificare le autorizzazioni di accesso.

## Passaggio 3: Scaricare, installare e registrare l'agente di Backup di Azure
Dopo aver creato l'insieme di credenziali di Backup di Azure e aver scaricato le credenziali dell'insieme di credenziali, è necessario installare un agente in ogni server o client Windows.

### Per scaricare, installare e registrare l'agente

1. Fare clic su **Servizi di ripristino**, quindi selezionare l'insieme di credenziali per il backup da registrare con un server.

2. Nella pagina Avvio rapido fare clic su **Agente per Windows Server, System Center Data Protection Manager o client Windows > Salva**.

    ![Salva agente](./media/backup-configure-vault/agent.png)

3. Al termine del download di *MARSagentinstaller.exe*, fare clic su **Esegui** oppure fare doppio clic su **MARSAgentInstaller.exe** dal percorso in cui è stato salvato.

4. 	Scegliere la *cartella di installazione* e la *cartella della cache* necessarie per l'agente e fare clic su **Avanti**.

    Il percorso della cache specificato deve avere uno spazio disponibile pari almeno al 5% dei dati di backup.

5. È possibile continuare a connettersi a Internet con le impostazioni proxy predefinite o, se si usa un server proxy per connettersi a Internet, nella schermata **Configurazione proxy** selezionare la casella di controllo **Utilizzare le impostazioni personalizzate del proxy** e immettere i dettagli del server proxy.

    Se si usa un proxy autenticato, immettere il nome utente e la password.

    Fare clic su **Avanti**.

6. Fare clic su **Installa** per avviare l'installazione dell'agente.

    Per completare l'installazione, l'agente Backup di Azure installerà .NET Framework 4.5 e Windows PowerShell (se non è già installato).

7. Dopo aver installato l'agente, fare clic su **Continua con la registrazione** per continuare con il flusso di lavoro.

8. Nella schermata **Identificazione insieme di credenziali** trovare e selezionare il *file delle credenziali dell'insieme di credenziali* scaricato in precedenza.

    Il file delle credenziali dell'insieme di credenziali è valido solo per 48 ore dopo che è stato scaricato dal portale. Se si verifica un errore in questa schermata, ad esempio "Il file delle credenziali di insieme fornito è scaduto", accedere al portale di Azure e scaricare nuovamente il file delle credenziali di insieme.

    Verificare che il file delle credenziali dell'insieme di credenziali sia disponibile in un percorso accessibile dall'applicazione di installazione. Se si verificano errori relativi all'accesso, copiare il file delle credenziali di insieme in un percorso temporaneo nel computer e ripetere l'operazione.

    Se si verifica un errore di credenziali dell'insieme di credenziali non valide, ad esempio "Le credenziali dell'insieme di credenziali specificate non sono valide", il file è danneggiato o non ha le credenziali più recenti associate al servizio di ripristino. Ripetere l'operazione dopo avere scaricato un nuovo file di archivio delle credenziali dal portale. Questo errore in genere si verifica se l'utente fa clic ripetutamente sull'opzione *Scarica credenziali dell'insieme di credenziali* in rapida successione. In questo caso è valido solo l'ultimo file delle credenziali dell'insieme di credenziali.

9. Nella schermata **Impostazione crittografia** è possibile *generare* una passphrase o *fornire* una passphrase di almeno 16 caratteri. Ricordarsi di salvare la passphrase in un luogo sicuro.

    Fare clic su **Finish**.

    > [AZURE.WARNING] Se la passphrase viene persa o dimenticata, Microsoft non potrà fornire assistenza per il recupero dei dati di backup. L'utente finale possiede la passphrase di crittografia, che non è visibile a Microsoft. Salvare il file in un luogo sicuro, in quanto potrebbe essere necessario durante un'operazione di ripristino.

    La **Registrazione guidata server** registra il server con Backup di Microsoft Azure.

10. Dopo aver impostato la **chiave di crittografia**, lasciare selezionata la casella di controllo **Avvia agente di Servizi di ripristino di Microsoft Azure** e fare clic su **Chiudi**.

## Passaggio 4: Completare il backup iniziale

Il backup iniziale è costituito da due attività fondamentali: la **creazione della pianificazione del backup** e il **primo backup dei file e delle cartelle**. Dopo aver completato il primo backup in base alla pianificazione definita, i criteri di backup creeranno punti di backup che è possibile usare per ripristinare i dati.

### Pianificare il backup

1. Aprire l'**agente di Backup di Microsoft Azure**. L'agente verrà aperto automaticamente se si lascia selezionata la casella di controllo **Avvia agente di Servizi di ripristino di Microsoft Azure** quando si chiude la **Registrazione guidata server**. In alternativa è possibile trovarlo cercando *Backup di Microsoft Azure* nel computer.

    ![Pianificare un backup di Windows Server](./media/backup-configure-vault/snap-in-search.png)

2. Nell'**agente di Backup di Microsoft Azure** fare clic su **Pianifica backup**.

    ![Pianificare un backup di Windows Server](./media/backup-configure-vault/schedule-backup-close.png)

3. Nella schermata iniziale della **Pianificazione guidata backup ** fare clic su **Avanti**.

4. Nella schermata **Seleziona elementi per backup** fare clic su **Aggiungi elementi**.

5. Selezionare i file e le cartelle di cui si vuole eseguire il backup e fare clic su **Okay**.

6. Fare clic su **Avanti**.

7. Nella schermata di **Specifica la pianificazione del backup** specificare la **pianificazione di backup** e fare clic su**Avanti**.

    È possibile pianificare backup giornalieri (non più di 3 al giorno) o settimanali.

    ![Elementi per il backup di Windows Server](./media/backup-configure-vault/specify-backup-schedule-close.png)

    >[AZURE.NOTE] Per informazioni dettagliate su come specificare la pianificazione di backup, vedere questo [articolo](backup-azure-backup-cloud-as-tape.md).

8. Nella schermata **Seleziona criteri di conservazione** selezionare i **criteri di conservazione** per la copia di backup.

    I criteri di conservazione specificano il periodo di tempo per cui il backup verrà archiviato. Anziché specificare solo un "criterio semplice" per tutti i punti di backup, è possibile specificare criteri di conservazione diversi in base al momento in cui viene eseguito il backup. È possibile modificare i criteri di conservazione giornalieri, settimanali, mensili e annuali in base alle proprie esigenze.

     Fare clic su **Avanti**.

9. Nella schermata **Scegli tipo di backup iniziale** scegliere il tipo di backup iniziale**.

    È possibile eseguire il backup automaticamente in rete oppure offline. Il resto di questo articolo segue il processo di backup automatico. Se si preferisce eseguire un backup offline, vedere questo articolo per altre informazioni sul [flusso di lavoro di backup offline in Backup di Azure](backup-azure-backup-import-export.md).

    Lasciare selezionata l'opzione **Automaticamente in rete** e fare clic su **Avanti**.

10. Nella schermata **Conferma** riesaminare le informazioni e fare clic su **Fine**.

11. Al termine della creazione della **pianificazione del backup** da parte della procedura guidata, fare clic su **Chiudi**.

### Abilitare la limitazione della larghezza di banda (facoltativo)

L'agente di Backup di Azure consente di limitare la larghezza di banda della rete. La limitazione controlla l'uso della larghezza di banda della rete durante il trasferimento dati. Questo controllo può essere utile se è necessario eseguire il backup dei dati durante l'orario di lavoro, ma senza che il processo di backup interferisca con il resto del traffico Internet. La limitazione del trasferimento dati si applica alle attività di backup e ripristino.

1. Nell'**agente di backup** fare clic su **Modifica proprietà**.

    ![Modifica proprietà](./media/backup-configure-vault/change-properties.png)

2. Nella scheda **Limitazione larghezza di banda rete** selezionare la casella di controllo **Abilita la limitazione all'utilizzo della larghezza di banda Internet per le operazioni di backup**.

    ![Limitazione della larghezza di banda della rete](./media/backup-configure-vault/throttling-dialog.png)

3. Dopo aver abilitato la limitazione, specificare la larghezza di banda consentita per trasferire i dati di backup durante le **ore lavorative** e le **ore non lavorative**.

    I valori della larghezza di banda partono da 512 kilobyte al secondo (Kbps) e possono arrivare fino a 1023 megabyte al secondo (Mbps). È anche possibile definire l'inizio e la fine delle **ore lavorative** e quali giorni della settimana sono considerati lavorativi. Le ore al di fuori delle ore lavorative specificate sono considerate non lavorative.

4. Fare clic su **OK**.

### Esegui backup

1. Nell'**agente di backup** fare clic su **Esegui backup** per completare il seeding iniziale sulla rete.

    ![Eseguire ora il backup di Windows Server](./media/backup-configure-vault/backup-now.png)

2. Nella schermata **Conferma** riesaminare le impostazioni che la procedura guidata userà per il backup del computer e fare clic su **Backup**.

3. Fare clic su **Chiudi** per chiudere la procedura guidata. È possibile eseguire questa operazione prima che venga completato il **processo di backup**, che continuerà in background.

Dopo aver completato il backup iniziale, la **console di Backup di Azure** indica lo stato *Processo completato*.

![Completamento infrarossi](./media/backup-configure-vault/ircomplete.png)

## Passaggi successivi
- Iscriversi per ottenere un [account Azure gratuito](https://azure.microsoft.com/free/).

Per altre informazioni sul backup di macchine virtuali o altri carichi di lavoro, vedere:

- [Eseguire il backup di macchine virtuali IaaS](backup-azure-vms-prepare.md)
- [Eseguire il backup dei carichi di lavoro con il server di Backup di Microsoft Azure](backup-azure-microsoft-azure-backup.md)
- [Eseguire il backup dei carichi di lavoro in Azure con DPM](backup-azure-dpm-introduction.md)

<!---HONumber=AcomDC_0323_2016-->