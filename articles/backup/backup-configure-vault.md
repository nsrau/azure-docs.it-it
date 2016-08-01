<properties
	pageTitle="Eseguire il backup di Windows Server o client in Azure con Backup di Azure tramite il modello di distribuzione di Resource Manager| Microsoft Azure"
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
	ms.date="05/10/2016"
	ms.author="jimpark; trinadhk; markgal"/>

# Eseguire il backup di Windows Server o client in Azure con Backup di Azure tramite il modello di distribuzione di Resource Manager

> [AZURE.SELECTOR]
- [Portale di Azure](backup-configure-vault.md)
- [Portale classico](backup-configure-vault-classic.md)

Questo articolo illustra come eseguire il backup di file e cartelle di Windows Server o di un client Windows in Azure con Backup di Azure tramite il modello di distribuzione di Resource Manager.

![Passaggi del processo di backup](./media/backup-configure-vault/initial-backup-process.png)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] Modello di distribuzione classica.

## Prima di iniziare
Per eseguire il backup di un server o un client in Azure, è necessario un account Azure. Se non si ha un account, è possibile crearne uno[gratuito](https://azure.microsoft.com/free/) in pochi minuti.

## Passaggio 1: Creare l'insieme di credenziali dei servizi di ripristino

Un insieme di credenziali dei servizi di ripristino è un'entità che archivia tutti i backup e i punti di ripristino che sono stati creati nel corso del tempo. L'insieme di credenziali dei servizi di ripristino contiene anche i criteri di backup applicati ai file e alle cartelle protette. Quando si crea un insieme di credenziali dei servizi di ripristino, è necessario selezionare anche l'opzione di ridondanza di archiviazione appropriata.

### Per creare un insieme di credenziali di Servizi di ripristino

1. Se questa operazione non è già stata eseguita, accedere al [portale di Azure](https://portal.azure.com/), tramite la sottoscrizione di Azure.

2. Scegliere **Sfoglia** dal menu Hub e digitare **Servizi di ripristino** nell'elenco di risorse. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Fare clic su **Insiemi di credenziali dei servizi di ripristino**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-configure-vault/browse-to-rs-vaults.png) <br/>

    Viene visualizzato l'elenco degli insiemi di credenziali dei servizi di ripristino.

3. Scegliere **Aggiungi** dal menu **Insiemi di credenziali dei servizi di ripristino**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 2](./media/backup-configure-vault/rs-vault-menu.png)

    Si apre il pannello degli insiemi di credenziali dei servizi di ripristino in cui viene chiesto di specificare **Nome**, **Sottoscrizione**, **Gruppo di risorse** e **Località**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 5](./media/backup-configure-vault/rs-vault-attributes.png)

4. Nel campo **Nome** digitare un nome descrittivo per identificare l'insieme di credenziali. Il nome deve essere univoco per la sottoscrizione di Azure. Digitare un nome che contenga tra i 2 e i 50 caratteri. Deve iniziare con una lettera e può contenere solo lettere, numeri e trattini.

5. Fare clic su **Sottoscrizione** per visualizzare l'elenco di sottoscrizioni disponibili. Se non si è certi di quale sottoscrizione usare, usare la sottoscrizione predefinita (o suggerita). Sono presenti scelte multiple solo se l'account dell'organizzazione è associato a più sottoscrizioni di Azure.

6. Fare clic su **Gruppo di risorse** per visualizzare l'elenco dei gruppi di risorse disponibili oppure fare clic su **Nuovo** per crearne uno nuovo. Per informazioni complete sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../resource-group-overview.md)

7. Fare clic su **Località** per selezionare l'area geografica per l'insieme di credenziali. La scelta determina l'area geografica in cui vengono inviati i dati di backup. Scegliendo un'area geografica vicina alla propria posizione, è possibile ridurre la latenza di rete durante il backup in Azure.

8. Fare clic su **Create**. La creazione dell'insieme di credenziali dei servizi di ripristino può richiedere alcuni minuti. Monitorare le notifiche di stato nell'area superiore destra del portale. L'insieme di credenziali creato dovrebbe aprirsi nel portale. Se al termine l'insieme di credenziali non viene elencato, fare clic su **Aggiorna**. Quando l'elenco viene aggiornato, fare clic sul nome dell'insieme di credenziali.

### Per determinare la ridondanza di archiviazione
Quando si crea per la prima volta un insieme di credenziali di Servizi di ripristino, si determina come replicare lo spazio di archiviazione.

1. Nel pannello **Impostazioni**, che si apre automaticamente con il dashboard dell'insieme di credenziali, fare clic su **Infrastruttura di backup**.

2. Nel pannello Infrastruttura di backup fare clic su **Configurazione backup** per visualizzare il **Tipo di replica di archiviazione**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 5](./media/backup-configure-vault/backup-infrastructure.png)

3. Scegliere l'opzione di replica di archiviazione per l'insieme di credenziali.

    ![Elenco di insiemi di credenziali dei Servizi di ripristino](./media/backup-configure-vault/choose-storage-configuration.png)

    Per impostazione predefinita, l'insieme di credenziali prevede l'archiviazione con ridondanza geografica. Se si usa Azure come endpoint primario di archiviazione dei backup, continuare a usare l'archiviazione con ridondanza geografica. Se si usa Azure come endpoint non primario di archiviazione dei backup, scegliere l'archiviazione con ridondanza locale, che consente di ridurre i costi di archiviazione dei dati in Azure. Per altre informazioni sulle opzioni di archiviazione [con ridondanza geografica](../storage/storage-redundancy.md#geo-redundant-storage) e [con ridondanza locale](../storage/storage-redundancy.md#locally-redundant-storage), vedere questa [panoramica](../storage/storage-redundancy.md).

    Dopo aver scelto l'opzione di archiviazione per l'insieme di credenziali, è possibile associare file e cartelle all'insieme di credenziali.

Dopo aver creato un insieme di credenziali, preparare l'infrastruttura per il backup di file e cartelle scaricando e installando l'agente di Servizi di ripristino di Microsoft Azure, scaricando le credenziali dell'insieme di credenziali e usandole per registrare l'agente con l'insieme di credenziali.

## Passaggio 2: Scaricare file

>[AZURE.NOTE] L'abilitazione del backup tramite il portale di Azure verrà illustrata tra poco. Ora invece si userà l'agente di Servizi di ripristino di Microsoft Azure in locale per eseguire il backup dei file e delle cartelle.

1. Fare clic su **Impostazioni** nel dashboard dell'insieme di credenziali dei servizi di ripristino.

    ![Aprire il Pannello Obiettivo di backup](./media/backup-configure-vault/settings-button.png)

2. Fare clic su **Attività iniziali > Backup** nel pannello Impostazioni.

    ![Aprire il Pannello Obiettivo di backup](./media/backup-configure-vault/getting-started-backup.png)

3. Fare clic su **Obiettivo di backup** nel pannello Backup.

    ![Aprire il Pannello Obiettivo di backup](./media/backup-configure-vault/backup-goal.png)

4. Scegliere **Locale** dal menu Dove è in esecuzione il carico di lavoro?

5. Scegliere **File e cartelle** dal menu Di che cosa si vuole eseguire il backup? e fare clic su **OK**.

#### Scaricare l'agente di Servizi di ripristino

1. Fare clic su **Scarica agente per Windows Server o client Windows** nel pannello **Preparare l'infrastruttura**.

    ![Preparare l'infrastruttura](./media/backup-configure-vault/prepare-infrastructure-short.png)

2. Fare clic su **Salva** nel popup del download. Per impostazione predefinita, il file **MARSagentinstaller.exe** viene salvato nella cartella Downloads.

#### Scaricare le credenziali dell’insieme di credenziali

1. Fare clic su **Scarica > Salva** nel pannello Prepara infrastruttura.

    ![Preparare l'infrastruttura](./media/backup-configure-vault/prepare-infrastructure-download.png)

## Passaggio 3: Installare e registrare l'agente

1. Cercare e fare doppio clic sul file **MARSagentinstaller.exe** nella cartella Downloads o nella cartella in cui è stato salvato.

2. Completare l'Installazione guidata di Agente servizi di ripristino di Microsoft Azure. Per completare la procedura guidata, è necessario:

    - Scegliere un percorso per la cartella di installazione e della cache.
    - Fornire le informazioni sul server proxy se si usa un server proxy per connettersi a Internet.
    - Se si usa un proxy autenticato, immettere il nome utente e la password.
    - Fornire le credenziali dell'insieme di credenziali scaricate.
    - Salvare la passphrase di crittografia in un luogo sicuro.

    >[AZURE.NOTE] Se la passphrase viene persa o dimenticata, Microsoft non potrà offrire assistenza per il recupero dei dati di backup. Salvare il file in una posizione sicura. È necessario per ripristinare un backup.

L'agente ora è installato e il computer è registrato nell'insieme di credenziali. Ora è possibile configurare e pianificare il backup.

## Passaggio 4: Completare il backup iniziale

Il backup iniziale comprende due attività fondamentali:

- Pianificare il backup
- Eseguire il backup di file e cartelle per la prima volta

Per completare il backup iniziale, si usa l'agente di Backup di Microsoft Azure.

### Per pianificare il backup

1. Aprire l'agente Backup di Microsoft Azure. È possibile trovarlo se si cerca **Backup di Microsoft Azure** nel computer.

    ![Avviare Azure Backup Agent](./media/backup-configure-vault/snap-in-search.png)

2. Nell'agente di Backup fare clic su **Pianifica backup**.

    ![Pianificare un backup di Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

3. Nella pagina Guida introduttiva della Pianificazione guidata backup fare clic su **Avanti**.

4. Nella pagina Seleziona elementi per backup fare clic su **Aggiungi elementi**.

5. Selezionare i file e le cartelle di cui si vuole eseguire il backup e fare clic su **OK**.

6. Fare clic su **Avanti**.

7. Nella pagina **Specifica la pianificazione del backup** specificare la **pianificazione del backup** e fare clic su **Avanti**.

    È possibile pianificare backup giornalieri, da eseguire non più di tre volte al giorno, o settimanali.

    ![Elementi per il backup di Windows Server](./media/backup-configure-vault/specify-backup-schedule-close.png)

    >[AZURE.NOTE] Per altre informazioni su come specificare la pianificazione del backup vedere l'articolo [Usare Backup di Azure per sostituire l'infrastruttura basata su nastro](backup-azure-backup-cloud-as-tape.md).

8. Nella pagina **Seleziona criteri di conservazione** selezionare i **criteri di conservazione** per la copia di backup.

    I criteri di conservazione specificano il periodo di tempo per cui il backup verrà archiviato. Anziché specificare solo un "criterio semplice" per tutti i punti di backup, è possibile specificare criteri di conservazione diversi in base al momento in cui viene eseguito il backup. È possibile modificare i criteri di conservazione giornalieri, settimanali, mensili e annuali in base alle proprie esigenze.

9. Nella pagina Scegliere il tipo di backup iniziale selezionare il tipo di backup iniziale. Lasciare selezionata l'opzione **Automaticamente tramite la rete** e fare clic su **Avanti**.

    È possibile eseguire il backup automaticamente in rete oppure offline. Il resto di questo articolo descrive il processo di backup automatico. Se si preferisce eseguire un backup offline, vedere l'articolo [Flusso di lavoro di backup offline in Backup di Azure](backup-azure-backup-import-export.md) per altre informazioni.

10. Nella pagina Conferma esaminare le informazioni e fare clic su **Fine**.

11. Dopo aver creato la pianificazione del backup tramite la procedura guidata, fare clic su **Chiudi**.

### Abilitare la limitazione della larghezza di banda (facoltativo)

L'agente di backup consente di limitare la larghezza di banda. La limitazione controlla l'uso della larghezza di banda della rete durante il trasferimento dati. Questo controllo può essere utile se è necessario eseguire il backup dei dati durante l'orario di lavoro, ma senza che il processo di backup interferisca con il resto del traffico Internet. La limitazione si applica alle attività di backup e ripristino.

>[AZURE.NOTE] La limitazione di rete non è disponibile su Windows Server 2008 R2 SP1, Windows Server 2008 SP2 o Windows 7 (con i pacchetti di servizio). La funzione di limitazione della rete di Backup di Azure attiva il QoS ( Quality of Service) sul sistema operativo locale. Anche se il Backup di Azure è in grado di proteggere questi sistemi operativi, la versione del QoS disponibile su queste piattaforme non funziona con la limitazione di rete di Backup di Azure. La limitazione di rete può essere utilizzata in tutti gli altri [sistemi operativi supportati](backup-azure-backup-faq.md#installation-amp-configuration).

**Per abilitare la limitazione larghezza di banda**

1. Nell'agente di Backup, fare clic su **Modifica proprietà**.

    ![Modifica proprietà](./media/backup-configure-vault/change-properties.png)

2. Nella scheda **Limitazione larghezza di banda rete** selezionare la casella di controllo **Abilita la limitazione all'utilizzo della larghezza di banda Internet per le operazioni di backup**.

    ![Limitazione della larghezza di banda della rete](./media/backup-configure-vault/throttling-dialog.png)

3. Dopo avere abilitato la limitazione, specificare la larghezza di banda consentita per trasferire i dati di backup durante le **ore lavorative** e le **ore non lavorative**.

    I valori della larghezza di banda partono da 512 kilobit al secondo (Kbps) e possono arrivare fino a 1.023 megabyte al secondo (Mbps). È anche possibile definire l'inizio e la fine delle **ore lavorative** e i giorni della settimana da considerare come giorni lavorativi. Gli orari al di fuori delle ore lavorative definite vengono considerati ore non lavorative.

4. Fare clic su **OK**.

### Per eseguire il backup di file e cartelle per la prima volta

1. Nell'agente di Backup fare clic su **Esegui backup** per completare il seeding iniziale sulla rete.

    ![Eseguire ora il backup di Windows Server](./media/backup-configure-vault/backup-now.png)

2. Nella pagina Conferma riesaminare le impostazioni che l'Esecuzione guidata backup userà per il backup del computer. Fare clic su **Backup**.

3. Fare clic su **Chiudi** per chiudere la procedura guidata. Se quest'operazione viene svolta prima che venga completato il processo di backup, l'esecuzione guidata proseguirà in background.

Al termine del backup iniziale, nella console Backup comparirà lo stato **Processo completato**.

![Completamento infrarossi](./media/backup-configure-vault/ircomplete.png)

## Domande?
In caso di domande o se si vuole che venga inclusa una funzionalità, è possibile [inviare commenti e suggerimenti](http://aka.ms/azurebackup_feedback).

## Passaggi successivi
Per altre informazioni sul backup di macchine virtuali o altri carichi di lavoro, vedere:

- Ora che si è eseguito il backup dei file e delle cartelle, è possibile [gestire l'insieme di credenziali e i server](backup-azure-manage-windows-server.md).
- Se è necessario ripristinare un backup, usare questo articolo per [ripristinare i file in un computer Windows](backup-azure-restore-windows-server.md).

<!---HONumber=AcomDC_0720_2016-->