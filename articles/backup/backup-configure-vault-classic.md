<properties
	pageTitle="Eseguire il backup di Windows Server o client Windows in Azure tramite il modello di distribuzione classica | Microsoft Azure"
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
	ms.date="05/09/2016"
	ms.author="jimpark; trinadhk; markgal"/>


# Eseguire il backup di Windows Server o client Windows in Azure tramite il modello di distribuzione classica

> [AZURE.SELECTOR]
- [Portale classico](backup-configure-vault-classic.md)
- [Portale di Azure](backup-configure-vault.md)

L'articolo illustra le procedure necessarie per preparare l'ambiente ed eseguire il backup di un server o un client Windows in Azure. Contiene anche considerazioni sulla distribuzione della soluzione di backup. Se si vuole provare Azure Backup per la prima volta, questo articolo illustra rapidamente come fare.

![Crea insieme di credenziali](./media/backup-configure-vault-classic/initial-backup-process.png)

>[AZURE.IMPORTANT] Azure offre due diversi modelli di distribuzione per creare e usare le risorse: Resource Manager e distribuzione classica. Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.

## Prima di iniziare
Per eseguire il backup di un server o un client in Azure, è necessario un account Azure. Se non si ha un account, è possibile crearne uno[gratuito](https://azure.microsoft.com/free/) in pochi minuti.

## Passaggio 1: Creare un insieme di credenziali di backup
Per eseguire il backup di file e cartelle da un server o un client è necessario creare un insieme di credenziali di backup nell'area geografica in cui si intende archiviare i dati.

### Per creare un insieme di credenziali per il backup

1. Accedere al [portale classico](https://manage.windowsazure.com/).

2. Fare clic su **Nuovo** > **Servizi dati** > **Servizi di ripristino** > **Insieme di credenziali di backup** e scegliere **Creazione rapida**.

3. Nel campo **Nome** digitare un nome descrittivo per identificare l'insieme di credenziali di backup. Digitare un nome che contenga tra i 2 e i 50 caratteri. Deve iniziare con una lettera e può contenere solo lettere, numeri e trattini. È necessario che il nome sia univoco per ogni sottoscrizione.

4. Per il parametro **Area** selezionare l'area geografica per l'insieme di credenziali per il backup. La scelta determina l'area geografica in cui vengono inviati i dati di backup. Scegliendo un'area geografica vicina alla propria posizione, è possibile ridurre la latenza di rete durante il backup in Azure.

5. Fare clic su **Crea insieme di credenziali**.

    ![Creare un insieme di credenziali per il backup](./media/backup-configure-vault-classic/demo-vault-name.png)

    La creazione dell'insieme di credenziali per il backup può richiedere alcuni minuti. Per verificare lo stato, monitorare le notifiche nella parte inferiore del portale classico.

    Dopo avere creato l'insieme di credenziali di backup, verrà visualizzato un messaggio che indica che l'insieme di credenziali è stato creato correttamente. Viene inoltre visualizzato come **Attivo** nell'elenco delle risorse di **Servizi di ripristino**.

    ![Creazione dello stato dell'insieme di credenziali](./media/backup-configure-vault-classic/recovery-services-select-vault.png)

4. Selezionare l'opzione di ridondanza di archiviazione attenendosi alla procedura descritta di seguito.

    >[AZURE.IMPORTANT] È consigliabile identificare l'opzione per la ridondanza dell'archiviazione subito dopo la creazione dell'insieme di credenziali e prima che i computer vengano registrati nell'insieme di credenziali. Dopo la registrazione di un elemento nell'insieme di credenziali, l'opzione di ridondanza di archiviazione è bloccata e non può essere modificata.

    Se si usa Azure come endpoint primario di archiviazione dei backup, ad esempio quando si esegue il backup in Azure da Windows Server, è consigliabile scegliere l'opzione predefinita di [archiviazione con ridondanza geografica](../storage/storage-redundancy.md#geo-redundant-storage).

    Se si usa Azure come endpoint terziario di archiviazione dei backup, ad esempio quando si usa System Center Data Protection Manager per archiviare una copia locale del backup e si usa Azure per la conservazione a lungo termine, è consigliabile scegliere l'[archiviazione con ridondanza locale](../storage/storage-redundancy.md#locally-redundant-storage). In questo modo vengono ridotti i costi di archiviazione dei dati in Azure e viene offerta una durabilità dei dati inferiore che può essere accettabile per le copie terziarie.

    **Per selezionare l'opzione di ridondanza di archiviazione:**

    a. Fare clic sull'insieme di credenziali appena creato.

    b. Nella pagina Avvio rapido selezionare **Configura**.

    ![Configurazione dello stato dell'insieme di credenziali](./media/backup-configure-vault-classic/configure-vault.png)

    c. Scegliere l'opzione per la ridondanza di archiviazione appropriata.

    Se si seleziona **Con ridondanza locale** è necessario fare clic su **Salva**, perché l'opzione predefinita è **Con ridondanza geografica**.

    d. Fare clic su **Servizi di ripristino** nel riquadro di spostamento sinistro per tornare all'elenco di risorse di Servizi di ripristino.

## Passaggio 2: Scaricare il file delle credenziali dell'insieme di credenziali
Per eseguire il backup dei dati in Azure è necessario autenticare il computer locale con un insieme di credenziali di backup. L'autenticazione viene eseguita tramite le *credenziali dell'insieme di credenziali*. Il file delle credenziali di insieme delle credenziali viene scaricato dal portale classico tramite un canale sicuro. La chiave privata del certificato non viene conservata nel portale o nel servizio.

Vedere altre informazioni sull'[uso delle credenziali dell'insieme di credenziali per l'autenticazione con il servizio Backup](backup-introduction-to-azure-backup.md#what-is-the-vault-credential-file).

### Per scaricare le credenziali dell'insieme di credenziali in un computer locale

1. Fare clic su **Servizi di ripristino** nel riquadro di spostamento sinistro e selezionare l'insieme di credenziali per il backup creato.

    ![Completamento infrarossi](./media/backup-configure-vault-classic/rs-left-nav.png)

2.  Nella pagina Avvio rapido fare clic su **Scarica credenziali di insieme**.

    Il portale genera una credenziale dell'insieme di credenziali usando una combinazione del nome dell'insieme e della data corrente. Il file delle credenziali dell'insieme di credenziali viene usato solo durante il flusso di lavoro di registrazione e scade dopo 48 ore.

    Il file delle credenziali dell'insieme di credenziali può essere scaricato dal portale.

3. Fare clic su **Salva** per scaricare il file delle credenziali dell'insieme di credenziali nella cartella Downloads dell'account locale. È anche possibile scegliere **Salva con nome** dal menu **Salva** per specificare un percorso per il file dell'insieme di credenziali.

    >[AZURE.NOTE] Assicurarsi che il file delle credenziali dell'insieme di credenziali sia salvato in un percorso accessibile dal computer. Se viene archiviato in una condivisione di file o in un Server Message Block, controllare di avere le autorizzazioni per accedervi.

## Passaggio 3: Scaricare, installare e registrare l'agente di Backup
Dopo aver creato l'insieme di credenziali per il backup e aver scaricato il file dell'insieme di credenziali, è necessario installare un agente in ogni computer Windows.

### Per scaricare, installare e registrare l'agente

1. Fare clic su **Servizi di ripristino** e selezionare l'insieme di credenziali per il backup da registrare con un server.

2. Nella pagina Avvio rapido fare clic su **Agente per Windows Server, System Center Data Protection Manager o client Windows**. Fare quindi clic su **Salva**.

    ![Salva agente](./media/backup-configure-vault-classic/agent.png)

3. Dopo aver scaricato il file MARSagentinstaller.exe, fare clic su **Esegui** oppure fare doppio clic sul file **MARSAgentInstaller.exe** nel percorso in cui è stato salvato.

4. Scegliere la cartella di installazione e la cartella della cache necessarie per l'agente e fare clic su **Avanti**. Il percorso della cache specificato deve avere uno spazio disponibile pari almeno al 5% dei dati di backup.

5. È possibile continuare a connettersi a Internet tramite le impostazioni predefinite del proxy. Se si usa un server proxy per connettersi a Internet, nella pagina Configurazione proxy selezionare la casella di controllo **Utilizzare le impostazioni personalizzate del proxy** e specificare i dettagli del server proxy. Se si usa un proxy autenticato, digitare il nome utente e la password e fare clic su **Avanti**.

7. Fare clic su **Installa** per avviare l'installazione dell'agente. Per completare l'installazione, l'agente Backup installerà .NET Framework 4.5 e Windows PowerShell, se non è già installato.

8. Dopo aver installato l'agente fare clic su **Continua con la registrazione** per continuare con il flusso di lavoro.

9. Nella pagina Identificazione insieme di credenziali trovare e selezionare il file dell'insieme di credenziali scaricato in precedenza.

    Il file dell'insieme di credenziali è valido soltanto per 48 ore dopo essere stato scaricato dal portale. Se si verifica un errore in questa pagina, ad esempio "Il file delle credenziali dell'insieme di credenziali specificato è scaduto", accedere al portale e scaricare nuovamente il file delle credenziali dell'insieme.

    Verificare che il file delle credenziali dell'insieme sia disponibile in un percorso accessibile dall'applicazione di installazione. Se si verificano errori relativi all'accesso, copiare il file delle credenziali di insieme in un percorso temporaneo nello stesso computer e ripetere l'operazione.

    Se si verifica un errore di credenziali dell'insieme, ad esempio "È stato fornito un insieme di credenziali non valido", il file è danneggiato o non ha le credenziali più recenti associate al servizio di ripristino. Ripetere l'operazione dopo avere scaricato un nuovo file di archivio delle credenziali dal portale. Questo errore si può verificare anche quando l'utente fa clic ripetutamente sull'opzione **Scarica credenziale dell'insieme di credenziali** in rapida successione. In questo caso è valido solo l'ultimo file delle credenziali dell'insieme di credenziali.

9. Nella pagina Impostazione crittografia è possibile generare o specificare una passphrase composta da almeno 16 caratteri. Ricordarsi di salvare la passphrase in un luogo sicuro.

10. Fare clic su **Finish**. La Registrazione guidata server registra il server con Backup.

    >[AZURE.WARNING] Se la passphrase viene persa o dimenticata, Microsoft non potrà offrire assistenza per il recupero dei dati di backup. L'utente è proprietario della passphrase di crittografia e Microsoft non ha visibilità sulla passphrase in uso. Salvare il file in un luogo sicuro, in quanto sarà necessario durante un'operazione di ripristino.

11. Dopo avere impostato la chiave di crittografia, lasciare selezionata la casella di controllo **Avvia agente dei servizi di ripristino di Microsoft Azure** e fare clic su **Chiudi**.

## Passaggio 4: Completare il backup iniziale

Il backup iniziale comprende due attività fondamentali:

- Creazione della pianificazione dei backup
- Primo backup di file e cartelle

Al termine del backup iniziale, il criterio di backup crea i punti di backup da usare per ripristinare i dati. I criteri di backup procedono in base alla pianificazione definita.

### Per pianificare il backup

1. Aprire l'agente Backup di Microsoft Azure. L'agente verrà aperto automaticamente se si lascia selezionata la casella di controllo **Avvia agente dei servizi di ripristino di Microsoft Azure** quando si chiude la Registrazione guidata server. È possibile trovarlo se si cerca **Backup di Microsoft Azure** nel computer.

    ![Avviare Azure Backup Agent](./media/backup-configure-vault-classic/snap-in-search.png)

2. Nell'agente di Backup fare clic su **Pianifica backup**.

    ![Pianificare un backup di Windows Server](./media/backup-configure-vault-classic/schedule-backup-close.png)

3. Nella pagina Guida introduttiva della Pianificazione guidata backup fare clic su **Avanti**.

4. Nella pagina Seleziona elementi per backup fare clic su **Aggiungi elementi**.

5. Selezionare i file e le cartelle di cui si vuole eseguire il backup e fare clic su **OK**.

6. Fare clic su **Avanti**.

7. Nella pagina **Specifica la pianificazione del backup** specificare la **pianificazione del backup** e fare clic su **Avanti**.

    È possibile pianificare backup giornalieri, da eseguire non più di tre volte al giorno, o settimanali.

    ![Elementi per il backup di Windows Server](./media/backup-configure-vault-classic/specify-backup-schedule-close.png)

    >[AZURE.NOTE] Per altre informazioni su come specificare la pianificazione del backup vedere l'articolo [Usare Backup di Azure per sostituire l'infrastruttura basata su nastro](backup-azure-backup-cloud-as-tape.md).

8. Nella pagina **Seleziona criteri di conservazione** selezionare i **criteri di conservazione** per la copia di backup.

    I criteri di conservazione specificano il periodo di tempo per cui il backup verrà archiviato. Anziché specificare solo un "criterio semplice" per tutti i punti di backup, è possibile specificare criteri di conservazione diversi in base al momento in cui viene eseguito il backup. È possibile modificare i criteri di conservazione giornalieri, settimanali, mensili e annuali in base alle proprie esigenze.

9. Nella pagina Scegliere il tipo di backup iniziale selezionare il tipo di backup iniziale. Lasciare selezionata l'opzione **Automaticamente tramite la rete** e fare clic su **Avanti**.

    È possibile eseguire il backup automaticamente in rete oppure offline. Il resto di questo articolo descrive il processo di backup automatico. Se si preferisce eseguire un backup offline, vedere l'articolo [Flusso di lavoro di backup offline in Backup di Azure](backup-azure-backup-import-export.md) per altre informazioni.

10. Nella pagina Conferma esaminare le informazioni e fare clic su **Fine**.

11. Dopo aver creato la pianificazione del backup tramite la procedura guidata, fare clic su **Chiudi**.

### Abilitare la limitazione della larghezza di banda (facoltativo)

L'agente di Backup consente di limitare la larghezza di banda. La limitazione controlla l'uso della larghezza di banda della rete durante il trasferimento dati. Questo controllo può essere utile se è necessario eseguire il backup dei dati durante l'orario di lavoro, ma senza che il processo di backup interferisca con il resto del traffico Internet. La limitazione si applica alle attività di backup e ripristino.

**Per abilitare la limitazione larghezza di banda**

1. Nell'agente di Backup fare clic su **Modifica proprietà**.

    ![Modifica proprietà](./media/backup-configure-vault-classic/change-properties.png)

2. Nella scheda **Limitazione larghezza di banda rete** selezionare la casella di controllo **Abilita la limitazione all'utilizzo della larghezza di banda Internet per le operazioni di backup**.

    ![Limitazione della larghezza di banda della rete](./media/backup-configure-vault-classic/throttling-dialog.png)

3. Dopo avere abilitato la limitazione, specificare la larghezza di banda consentita per trasferire i dati di backup durante le **ore lavorative** e le **ore non lavorative**.

    I valori della larghezza di banda partono da 512 kilobit al secondo (Kbps) e possono arrivare fino a 1.023 megabyte al secondo (Mbps). È anche possibile definire l'inizio e la fine delle **ore lavorative** e i giorni della settimana da considerare come giorni lavorativi. Gli orari al di fuori delle ore lavorative definite vengono considerati ore non lavorative.

4. Fare clic su **OK**.

### Per esegui immediatamente il backup

1. Nell'agente di Backup fare clic su **Esegui backup** per completare il seeding iniziale sulla rete.

    ![Eseguire ora il backup di Windows Server](./media/backup-configure-vault-classic/backup-now.png)

2. Nella pagina Conferma riesaminare le impostazioni che l'Esecuzione guidata backup userà per il backup del computer. Fare clic su **Backup**.

3. Fare clic su **Chiudi** per chiudere la procedura guidata. Se quest'operazione viene svolta prima che venga completato il processo di backup, l'esecuzione guidata proseguirà in background.

Al termine del backup iniziale, nella console Backup apparirà lo stato **Processo completato**.

![Completamento infrarossi](./media/backup-configure-vault-classic/ircomplete.png)

## Passaggi successivi
- Sottoscrivere un [account Azure gratuito](https://azure.microsoft.com/free/).

Per altre informazioni sul backup di macchine virtuali o altri carichi di lavoro, vedere:

- [Eseguire il backup di macchine virtuali IaaS](backup-azure-vms-prepare.md)
- [Eseguire il backup dei carichi di lavoro con il server di Backup di Microsoft Azure](backup-azure-microsoft-azure-backup.md)
- [Eseguire il backup dei carichi di lavoro in Azure con Data Protection Manager](backup-azure-dpm-introduction.md)

<!---HONumber=AcomDC_0518_2016-->