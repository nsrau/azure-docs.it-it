<properties
	pageTitle="Backup di una macchina virtuale di Azure - Backup | Microsoft Azure"
	description="Informazioni su come eseguire il backup di una macchina virtuale di Azure dopo la registrazione"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="hero-article" ms.date="09/24/2015" ms.author="trinadhk"; "aashishr"; "jimpark"/>


# Eseguire il backup di macchine virtuali di Azure
Questo articolo rappresenta la guida essenziale per eseguire il backup di macchine virtuali di Azure. Prima di procedere, assicurarsi che tutti i [prerequisiti](backup-azure-vms-introduction.md#prerequisites) siano stati soddisfatti.

L'esecuzione del backup di macchine virtuali di Azure prevede tre passaggi principali:

![Three steps to backup an Azure virtual machine](./media/backup-azure-vms/3-steps-for-backup.png)

>[AZURE.NOTE]Il backup della macchina virtuale è locale. L'insieme di credenziali di backup da un'area di Azure specificata non consente di eseguire il backup delle macchine virtuali in un'altra area di Azure. Di conseguenza, per ogni area di Azure in cui sono presenti macchine virtuali per le quali deve essere eseguito il backup, è necessario creare un insieme di credenziali di backup in tale area.

## 1\. Individuare le macchine virtuali di Azure
Il processo di individuazione esegue una query su Azure per ottenere l'elenco di macchine virtuali nella sottoscrizione, insieme ad altre informazioni quali il nome del servizio cloud e l'area. Il processo di individuazione deve sempre essere eseguito come primo passaggio. Ciò consente di verificare che eventuali nuove macchine virtuali aggiunte alla sottoscrizione vengano identificate.

### Per avviare il processo di individuazione

1. Passare all'insieme di credenziali per il backup, disponibile in **Servizi di ripristino** nel portale di Azure e quindi fare clic sulla scheda **Elementi registrati**.

2. Scegliere il tipo di carico di lavoro nel menu a discesa come **Macchina virtuale di Azure** e quindi fare clic sul pulsante **Seleziona**.

    ![select workload](./media/backup-azure-vms/discovery-select-workload.png)

3. Fare clic sul pulsante **INDIVIDUA** nella parte inferiore della pagina. ![discover button](./media/backup-azure-vms/discover-button-only.png)

4. Il processo di individuazione può durare alcuni pochi minuti, mentre le macchine virtuali vengono elencate in formato tabulare. Durante l'esecuzione del processo di individuazione, viene visualizzata una notifica di tipo avviso popup nella parte inferiore della schermata.

    ![discover vms](./media/backup-azure-vms/discovering-vms.png)

5. Al termine del processo di individuazione, viene visualizzata una notifica di tipo avviso popup.

    ![discover-done](./media/backup-azure-vms/discovery-complete.png)

##  2\. Registrare le macchine virtuali di Azure
Per poter essere protetta, una macchina virtuale deve essere registrata nel servizio Backup di Azure. L'obiettivo principale del processo di registrazione consiste nell'associare la macchina virtuale al servizio Backup di Azure. La registrazione è in genere un'attività da eseguire una sola volta.

>[AZURE.NOTE]L'estensione per il backup non viene installata durante la procedura di registrazione. L'installazione e l'aggiornamento dell'agente di backup fa ora parte del processo di backup pianificato.

### Per registrare le macchine virtuali

1. Passare all'insieme di credenziali per il backup, disponibile in **Servizi di ripristino** nel portale di Azure e quindi fare clic sulla scheda **Elementi registrati**.

2. Nel menu a discesa scegliere il tipo di carico di lavoro **Macchina virtuale di Azure** e quindi fare clic sul pulsante di selezione.

    ![select workload](./media/backup-azure-vms/discovery-select-workload.png)

3. Fare clic sul pulsante **REGISTRA** nella parte inferiore della pagina.

    ![register button](./media/backup-azure-vms/register-button-only.png)

4. Nel menu di scelta rapida **Registra elementi** selezionare le macchine virtuali da registrare. Se sono presenti due o più macchine virtuali con lo stesso nome, usare il servizio cloud per distinguere tra le macchine virtuali.

    L'operazione **Registra** può essere effettuata su vasta scala, il che significa che è possibile selezionare contemporaneamente più macchine virtuali da registrare. Questo riduce notevolmente il tempo dedicato alla preparazione della macchina virtuale per il backup.

5. Viene creato un processo per ogni macchina virtuale da registrare. La notifica di tipo avviso popup visualizza lo stato di questa attività. Fare clic su **Visualizza processo** per passare alla pagina **Processi**.

    ![register job](./media/backup-azure-vms/register-create-job.png)

6. La macchina virtuale viene visualizzata anche nell'elenco di elementi registrati e viene visualizzato lo stato dell'operazione di registrazione.

    ![Registering status 1](./media/backup-azure-vms/register-status01.png)

7. Al termine dell'operazione, lo stato nel portale viene modificato per rispecchiare lo stato registrato.

    ![Registration status 2](./media/backup-azure-vms/register-status02.png)

## 3\. Protezione: eseguire il backup delle macchine virtuali di Azure
Questo passaggio implica l'impostazione di un criterio di backup e conservazione per la macchina virtuale. È possibile proteggere più macchine virtuali su vasta scala con una singola operazione.

>[AZURE.NOTE]Nell'insieme di credenziali per il backup di Azure creato dopo maggio 2015 sono integrati criteri predefiniti. Questi criteri predefiniti includono un periodo di conservazione predefinito di 30 giorni e una pianificazione per eseguire il backup una volta al giorno.

Per proteggere una macchina virtuale, eseguire la procedura seguente:

1. Passare all'insieme di credenziali per il backup disponibile in **Servizi di ripristino** nel portale di Azure e quindi fare clic sulla scheda **Elementi registrati**.
2. Nel menu a discesa scegliere il tipo di carico di lavoro **Macchina virtuale di Azure** e quindi fare clic su **Seleziona**.

    ![Select workload in portal](./media/backup-azure-vms/select-workload.png)

3. Fare clic su **PROTEGGI** nella parte inferiore della pagina. Verrà visualizzata la procedura guidata **Proteggi elementi**. Questa procedura guidata elenca solo le macchine virtuali registrate e non protette.

4. Tramite la procedura guidata **Proteggi elementi** è possibile selezionare le macchine virtuali da proteggere. Se sono presenti due o più macchine virtuali con lo stesso nome, usare il servizio cloud per distinguere tra le macchine virtuali.

    L'operazione **Proteggi** può essere effettuata su vasta scala, il che significa che è possibile selezionare contemporaneamente più macchine virtuali da proteggere. Questo riduce notevolmente il tempo di preparazione della macchina virtuale per il backup.

    ![Configurare la protezione su vasta scala](./media/backup-azure-vms/protect-at-scale.png)

5. Nella seconda schermata della procedura guidata **Proteggi elementi** scegliere una pianificazione di backup per eseguire il backup delle macchine virtuali selezionate. Scegliere da un set di criteri esistente o definire un nuovo criterio.

    In ogni insieme di credenziali per il backup è possibile avere più criteri di backup. I criteri rispecchiano i dettagli relativi alla modalità di pianificazione e conservazione del backup. Un criterio di backup potrebbe ad esempio essere destinato al backup giornaliero delle 22.00, mentre un altro potrebbe essere destinato al backup settimanale del sabato alle 06.00. Più criteri di backup consentono flessibilità nella pianificazione dei backup per l'infrastruttura delle macchine virtuali.

    Ogni criterio di backup può avere più macchine virtuali che sono associate al criterio. La macchina virtuale può essere associata a un solo criterio in un qualsiasi momento.

    ![Proteggere con nuovi criteri](./media/backup-azure-vms/policy-schedule.png)

6. Nella terza schermata della procedura guidata **Proteggi elementi** scegliere un intervallo di conservazione da associare ai backup eseguiti. Queste schermate supportano lo standard di settore GFS (Grandfather-Father-Son) basato su uno schema di conservazione. Ulteriori informazioni sulla [Conservazione a lungo termine](#long-term-retention).

    I criteri di backup comportano anche uno schema di conservazione dei backup pianificati. Se si selezionano i criteri di backup esistenti nella schermata precedente, viene disabilitata la modifica dello schema di conservazione e i backup seguono i criteri di conservazione definiti nei criteri.

    ![Proteggere con criteri di conservazione flessibili](./media/backup-azure-vms/policy-retention.png)

7. Per ogni macchina virtuale viene creato un processo per configurare il criterio di protezione e associare le macchine virtuali al criterio. Fare clic sulla scheda **Processi** e scegliere il filtro corretto per visualizzare l'elenco di processi in **Configura protezione**.

    ![Configure protection job](./media/backup-azure-vms/protect-configureprotection.png)


## Attività successive alla protezione

### Installazione dell'estensione per il backup
Il servizio Backup di Azure gestisce senza problemi l'aggiornamento e l'applicazione di patch dell'estensione per il backup senza richiedere all'utente alcun intervento complesso. Questo riduce il carico di lavoro di gestione dell'agente, che è in genere associato ai prodotti di backup.

#### Macchine virtuali offline
L'estensione per il backup viene installata se la macchina virtuale è in esecuzione. Una macchina virtuale in esecuzione consente anche di ottenere un punto di ripristino coerente con l'applicazione. Il servizio Backup di Azure continuerà tuttavia a eseguire il backup della macchina virtuale, anche se questa è spenta e non è stato possibile installare l'estensione (macchina virtuale offline). L'impatto si riflette nella coerenza, perché in questo caso il punto di ripristino sarà *coerente con l'arresto anomalo*.

### Backup iniziale
Dopo aver applicato i criteri di protezione alla macchina virtuale, questa sarà visualizzata nella scheda **Elementi protetti** con lo stato *Protetto* (in attesa di backup iniziale). Per impostazione predefinita, il primo backup pianificato è il backup iniziale. Per attivare immediatamente il backup iniziale dopo aver configurato la protezione, usare il pulsante **Esegui backup ora** in basso nella pagina **Elementi protetti**.

Il servizio Backup di Azure crea un processo di backup per l'operazione di backup iniziale. Fare clic sulla scheda **Processi** per visualizzare l'elenco dei processi. Come parte dell'operazione di backup, il servizio Backup di Azure esegue un comando nell'estensione per il backup in ogni macchina virtuale, in modo da scaricare tutte le scritture e creare uno snapshot coerente.

![Backup in progress](./media/backup-azure-vms/protect-inprogress.png)

Al termine del backup iniziale, lo *Stato di protezione* della macchina virtuale nella scheda **Elementi protetti** verrà visualizzato come *Protetto*.

![Virtual machine is backed up with recovery point](./media/backup-azure-vms/protect-backedupvm.png)

### Visualizzazione dello stato di backup e dei relativi dettagli
Dopo la protezione, il numero di macchine virtuali aumenta anche nella pagina di riepilogo **Dashboard**. Nella pagina **Dashboard** viene visualizzato anche il numero di processi delle ultime 24 ore riusciti, non riusciti e ancora in corso. Facendo clic su una qualsiasi categoria viene eseguito il drill-down in quella categoria nella pagina **Processi**.

![Status of backup in Dashboard page](./media/backup-azure-vms/dashboard-protectedvms.png)

>[AZURE.NOTE]I valori nel dashboard vengono aggiornati ogni 24 ore.

### Conservazione a lungo termine
I criteri di conservazione specificano il periodo di tempo per cui il backup deve essere archiviato. Anziché specificare solo un criterio di "conservazione semplice" per tutti i punti di backup, i clienti possono specificare criteri di conservazione diversi a seconda di quando viene eseguito il backup. Potrebbe ad esempio essere necessario, a scopo di controllo, conservare il punto di backup eseguito alla fine di ogni trimestre per un periodo di tempo più lungo, mentre il punto di backup eseguito quotidianamente, usato come punto di ripristino operativo, deve essere conservato per 90 giorni.

![Virtual machine is backed up with recovery point](./media/backup-azure-vms/long-term-retention.png)

1. **Criteri di conservazione giornaliera**: i backup eseguiti ogni giorno vengono archiviati per 30 giorni.
2. **Criteri di conservazione settimanale**: i backup eseguiti ogni domenica vengono conservati per 104 settimane
3. **Criteri di conservazione mensile**: i backup eseguiti l'ultima domenica di ogni mese vengono conservati per 120 mesi
4. **Criteri di conservazione annuale**: i backup eseguiti la prima domenica di ogni gennaio vengono conservati per 99 anni.

## Coerenza dei punti di ripristino
Quando si tratta di gestione dei dati di backup, i clienti si preoccupano per il comportamento delle VM dopo il ripristino. Le domande tipiche che i clienti pongono sono:

- La macchina virtuale si riavvierà?
- I dati saranno disponibili sul disco (o) è presente perdita di dati?
- L'applicazione sarà in grado di leggere i dati o i dati sono danneggiati?
- I dati saranno coerenti con l'applicazione con loro stessi quando vengono letti dall'applicazione?

La tabella seguente illustra i tipi di coerenza che si verificano durante il backup e il ripristino delle VM di Azure.

| Coerenza | Basato su VSS | Spiegazione e dettagli |
|-------------|-----------|---------|
| Coerenza con l'applicazione | Sì | Questa è la soluzione ideale per i carichi di lavoro Microsoft perché garantisce:<ol><li> L'*avvio* della macchina virtuale. <li>L'*assenza di qualsiasi danneggiamento*. <li>L'*assenza di perdite di dati*.<li> La coerenza dei dati con l'applicazione che li usa, grazie all'impiego dell'applicazione al momento del backup tramite il Servizio Copia Shadow del volume.</ol> Il Servizio snapshot del volume garantisce che i dati vengano scritti correttamente nell'archivio. La maggior parte dei carichi di lavoro di Microsoft ha writer del Servizio snapshot del volume che eseguono azioni specifiche di un carico di lavoro correlate alla coerenza dei dati. Ad esempio, Microsoft SQL Server ha un writer del Servizio snapshot del volume che garantisce l'esecuzione corretta delle operazioni di scrittura nel file di log delle transazioni e nel database.<br><br> Per il backup delle VM di Azure, il recupero di un punto di ripristino coerente con un'applicazione significa che l'estensione per il backup è riuscita a richiamare il flusso di lavoro del Servizio snapshot del volume e a essere completata *correttamente* prima dell'esecuzione dello snapshot della VM. Naturalmente, questo significa che anche i writer del Servizio snapshot del volume di tutte le applicazioni nella VM di Azure sono stati richiamati.<br><br>Leggere le [nozioni di base del Servizio snapshot del volume](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) per approfondimenti sui dettagli di [funzionamento](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx). |
| Coerenza con i file system | Sì, per i computer Windows | Esistono due scenari in cui il punto di ripristino può essere coerente con i file system:<ul><li>Backup di VM in Azure, poiché Linux non dispone di una piattaforma equivalente al Servizio snapshot del volume.<li>Errore del Servizio snapshot del volume durante il backup di VM in Azure.</li></ul> In entrambi i casi la soluzione ottimale è garantire: <ol><li> L'*avvio* della macchina virtuale. <li>L' *assenza di qualsiasi danneggiamento*.<li>L' *assenza di perdite di dati*.</ol> Le applicazioni devono implementare il proprio meccanismo di correzione sui dati ripristinati.|
| Coerenza dei dati | No | Questa situazione è equivalente a quella di un computer che si sia arrestato in modo anomalo (a causa di un ripristino software o hardware). Ciò si verifica in genere quando la macchina virtuale di Azure viene arrestata durante il backup. Per il backup della macchina virtuale di Azure, il recupero di un punto di ripristino coerenti con l'arresto anomalo indica che Backup di Azure non offre alcuna garanzia di coerenza dei dati sul supporto di archiviazione, dal punto di vista del sistema operativo o dal punto di vista dell'applicazione. Solo i dati già esistente sul disco al momento del backup vengono acquisiti e sottoposti a backup. <br/> <br/> Anche se non ci sono garanzie, nella maggior parte dei casi il sistema operativo verrà avviato. Questa operazione è in genere seguita da una procedura di controllo come chkdsk per risolvere eventuali errori di danneggiamento del disco. I dati in memoria o le scritture che non sono state scaricate completamente sul disco andranno persi. In genere l'applicazione esegue il proprio meccanismo di verifica nel caso in cui sia necessario effettuare il rollback di dati. Per il backup delle VM di Azure, ottenere un punto di ripristino coerente con l'arresto anomalo significa che Backup di Azure non offre il alcuna garanzia riguardo alla coerenza dei dati di archiviazione, sia dal punto di vista del sistema operativo che dell'applicazione. Questo accade solitamente quando la VM di Azure è spenta al momento del backup.<br><br>Ad esempio, se il log delle transazioni contiene voci che non sono presenti nel database, il software del database esegue un rollback fino a quando i dati non sono coerenti. Quando si usano dati distribuiti tra più dischi virtuali (ad esempio volumi con spanning), un punto di ripristino coerente con l'arresto anomalo del sistema non fornisce alcuna garanzia della correttezza dei dati.|


## Prestazioni e utilizzo delle risorse
Come il software di backup distribuito in locale, anche il backup delle macchine virtuali deve essere pianificato in termini di capacità e utilizzo delle risorse. I [limiti del servizio di archiviazione di Azure](azure-subscription-service-limits.md#storage-limits) definiscono il modo in cui sono strutturate le distribuzioni delle macchine virtuali per ottenere prestazioni ottimali con il minimo impatto sui carichi di lavoro in esecuzione. Di seguito sono riportati i due limiti principali del servizio di archiviazione di Azure che influiscono sulle prestazioni del backup:

- Numero massimo in uscita per account di archiviazione
- Frequenza delle richieste totale per account di archiviazione

Quando i dati di backup vengono copiati dall'account di archiviazione del cliente, vengono conteggiati ai fini delle metriche relative alle operazioni di I/O al secondo (IOPS) e ai dati in uscita (velocità effettiva) dell'account di archiviazione. Al contempo, le macchine virtuali sono in esecuzione e consumano operazioni di I/O al secondo (IOPS) e velocità effettiva. L'obiettivo è garantire che il traffico totale, del backup e delle macchine virtuali, non superi i limiti previsti per l'account di archiviazione.

Il backup è un processo avido e tenta di consumare il maggior numero di risorse possibile per completare il backup più rapidamente. Tutte le operazioni di I/O sono tuttavia limitate dalla *velocità effettiva da raggiungere per BLOB singolo* che ha un limite di *60 MB al secondo*. Per accelerare il processo di backup, si prova a eseguire *in parallelo* il backup di ogni disco della macchina virtuale. Quindi, se una macchina virtuale contiene 4 dischi, il servizio Backup di Azure tenterà di eseguire il backup di tutti e 4 i dischi in parallelo. Di conseguenza, il fattore più importante che determina il traffico di backup in uscita da un account di archiviazione del cliente è il **numero di dischi** di cui viene eseguito il backup.

Un secondo aspetto che influisce sulle prestazioni è la **pianificazione del backup**. Se tutte le macchine virtuali sono configurate per l'esecuzione del backup alla stessa ora, aumenta il numero di dischi di cui viene eseguito il backup *in parallelo*, perché il servizio Backup di Azure prova a eseguire il backup del maggior numero possibile di dischi. Per ridurre il traffico di backup da un account di archiviazione è possibile pianificare il backup delle singole macchine virtuali a diverse ore del giorno, senza sovrapposizioni.

### Pianificazione della capacità
Tutti questi fattori indicano che è necessario pianificare in modo appropriato l'utilizzo dell'account di archiviazione. Scaricare il [foglio di Excel per la pianificazione della capacità di backup delle macchine virtuali](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) per valutare l'impatto delle scelte relative alla pianificazione del backup e dei dischi.

### Velocità effettiva del backup
Per ogni disco di cui si esegue il backup, il servizio Backup di Azure legge i blocchi nel disco e archivia solo i dati modificati (backup incrementale). La tabella seguente illustra i valori di velocità effettiva media previsti per il servizio Backup di Azure:

| Operazione di backup | Velocità effettiva ottimale |
| ---------------- | ---------- |
| Backup iniziale | 160 Mbps |
| Backup incrementale (ripristino di emergenza) | 640 Mbps <br><br> Questo valore può abbassarsi in modo significativo in presenza di una varianza dati elevata sul disco di cui si esegue il backup |

In questo modo è possibile stimare la quantità di tempo necessaria per il backup di un disco con le dimensioni specificate.

### Tempo totale di backup della macchina virtuale
Nonostante gran parte del tempo venga impiegata per la lettura e la copia dei dati, ci sono altre operazioni che influiscono sul tempo totale necessario per il backup di una macchina virtuale:

1. Tempo impiegato per l'[installazione o l'aggiornamento dell'estensione per il backup](backup-azure-vms.md#offline-vms)
2. Tempo di attesa nella coda: poiché il servizio di backup elabora i backup di più clienti, l'operazione di backup potrebbe non essere avviata immediatamente. Nei periodi di massimo carico, sia possono estendere al massimo di 8 ore i tempi di attesa a causa del numero di backup in corso di elaborazione. Tuttavia, il tempo di backup totale della macchina virtuale sarà inferiore a 24 ore per i criteri di backup giornalieri.

## Risoluzione dei problemi
Per un elenco completo delle soluzioni alternative per risolvere gli errori riscontrati durante il backup delle macchine virtuali, vedere:

- [Risolvere i problemi relativi al backup delle macchine virtuali](backup-azure-vms-troubleshoot.md)

## Passaggi successivi
Per altre informazioni sulle operazioni iniziali Backup di Azure, vedere:

- [Ripristino di macchine virtuali](backup-azure-restore-vms.md)
- [Gestire le macchine virtuali](backup-azure-manage-vms.md)

<!---HONumber=Oct15_HO3-->