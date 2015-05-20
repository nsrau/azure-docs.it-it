<properties
	pageTitle="Backup di una macchina virtuale di Azure - Backup"
	description="Informazioni su come eseguire il backup di una macchina virtuale di Azure dopo la registrazione"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/30/2015"
	ms.author="aashishr"/>


# Eseguire il backup di una macchina virtuale di Azure

In questo articolo rappresenta la guida essenziale per eseguire il backup di macchine virtuali di Azure. Prima di procedere, assicurarsi che tutti i [prerequisiti][prereq] siano stati soddisfatti.

L'esecuzione del backup di macchine virtuali di Azure prevede tre passaggi principali:

![Three steps to backup an Azure virtual machine][three-steps]

## Individuare le macchine virtuali di Azure
Il processo di individuazione esegue una query su Azure per ottenere l'elenco di macchine virtuali nella sottoscrizione, insieme ad altre informazioni quali il nome del servizio cloud e l'area.

> [AZURE.NOTE]Il processo di individuazione deve sempre essere eseguito come primo passaggio. Ciò consente di verificare che eventuali nuove macchine virtuali aggiunte alla sottoscrizione vengano identificate.

Per attivare il processo di individuazione, eseguire la procedura seguente:

1. Passare all'insieme di credenziali per il backup, che può trovarsi in **Servizi di ripristino** nel portale di Azure e fare clic sulla scheda **Elementi registrati**.

2. Scegliere il tipo di carico di lavoro nel menu a discesa come **Macchina virtuale di Azure** e fare clic sul pulsante **Seleziona**. ![select workload][select-workload]

3. Fare clic sul pulsante **Individua** nella parte inferiore della pagina. ![discover button][discover-button]

4. Il processo di individuazione può durare alcuni pochi minuti, mentre le macchine virtuali vengono elencate in formato tabulare. Durante l'esecuzione del processo di individuazione, viene visualizzata una notifica di tipo avviso popup nella parte inferiore della schermata. ![discover vms][discover-vms]

5. Al termine del processo di individuazione, viene visualizzata una notifica di tipo avviso popup. ![discover-done][discover-done]

<br><br>
## Registrare le macchine virtuali di Azure
Per poter essere protetta una macchina deve essere registrata con il servizio Backup di Azure. Il processo di registrazione ha due obiettivi principali:

1. Inserire l'estensione di backup nell'agente VM della macchina virtuale.

2. Associare la macchina virtuale al servizio Backup di Azure.

La registrazione è in genere un'attività da eseguire una sola volta. Il servizio Backup di Azure gestisce senza problemi l'aggiornamento e l'applicazione di patch dell'estensione per il backup senza richiedere all'utente alcun intervento complesso. Questo riduce il carico di lavoro di gestione dell'agente che è in genere associato ai prodotti di backup.

### Registrare le macchine virtuali

1. Passare all'insieme di credenziali per il backup, che può trovarsi in **Servizi di ripristino** nel portale di Azure e fare clic sulla scheda **Elementi registrati**.

2. Scegliere il tipo di carico di lavoro nel menu a discesa come **Macchina virtuale di Azure** e fare clic sul pulsante Seleziona. ![select workload][select-workload]

3. Fare clic sul pulsante **Registra** nella parte inferiore della pagina. ![register button][register-button]

4. Nella finestra popup **Registra elementi** selezionare le macchine virtuali che si vuole registrare. Se sono presenti due o più macchine virtuali con lo stesso nome, usare il servizio cloud per distinguere tra le macchine virtuali.

  	L'operazione **Registra** può essere effettuata su vasta scala, il che significa che è possibile selezionare contemporaneamente più macchine virtuali da registrare. Questo riduce notevolmente il tempo dedicato alla preparazione della macchina virtuale per il backup.

  	> [AZURE.NOTE]Solo le macchine virtuali che non sono registrate e che si trovano nella stessa area dell'insieme di credenziali per il backup verranno visualizzate qui.

  	![Elenco di macchine virtuali da registrare][register-vms]

5. Viene creato un processo per ogni macchina virtuale da registrare. La notifica di tipo avviso popup visualizza lo stato di questa attività. Fare clic su **Visualizza processo** per passare alla pagina **Processi**. ![register job][register-job]

6. La macchina virtuale viene visualizzata anche nell'elenco di elementi registrati e viene visualizzato lo stato dell'operazione di registrazione ![Registering status 1][register-status1]

7. Al termine dell'operazione, lo stato nel portale viene modificato per rispecchiare lo stato registrato. ![Registration status 2][register-status2]

<br><br>
## Eseguire il backup di una macchina virtuale di Azure
Questo passaggio implica l'impostazione di un criterio di backup e conservazione per la macchina virtuale. Per proteggere una macchina virtuale, eseguire la procedura seguente:

1. Passare all'insieme di credenziali per il backup, che può trovarsi in **Servizi di ripristino** nel portale di Azure e fare clic sulla scheda **Elementi registrati**.

2. Scegliere il tipo di carico di lavoro nel menu a discesa come **Macchina virtuale di Azure** e fare clic sul pulsante Seleziona. ![Select workload in portal][select-workload]

3. Fare clic sul pulsante **Proteggi** nella parte inferiore della pagina.

4. In questo modo verrà visualizzata la procedura guidata **Proteggi elementi** in cui è possibile selezionare le macchine virtuali da proteggere. Se sono presenti due o più macchine virtuali con lo stesso nome, usare il servizio cloud per distinguere tra le macchine virtuali. L'operazione **Proteggi** può essere effettuata su vasta scala, il che significa che è possibile selezionare contemporaneamente più macchine virtuali da registrare. Questo riduce notevolmente il tempo di preparazione della macchina virtuale per il backup.

	> [AZURE.NOTE]Solo le macchine virtuali che sono state registrate correttamente con il servizio Backup di Azure e che si trovano nella stessa area dell'insieme di credenziali per il backup verranno visualizzati qui.

	![Selezionare elementi da proteggere][protect-wizard1]

5. Nella seconda schermata della procedura guidata **Proteggi elementi** selezionare un criterio di backup e conservazione per eseguire il backup delle macchine virtuali selezionate. Scegliere da un set di criteri esistente o definire un nuovo criterio.

	> [AZURE.NOTE]Per l'anteprima di backup sono supportati fino a 30 giorni di conservazione e massimo un backup giornaliero.

	![Selezionare i criteri di protezione][protect-wizard2]

	In ogni insieme di credenziali per il backup è possibile avere più criteri di backup. I criteri rispecchiano i dettagli relativi alla modalità di pianificazione e conservazione del backup. Ad esempio, un criterio di backup potrebbe essere destinato al backup giornaliero delle 22.00, mentre un altro criterio di backup potrebbe essere destinato al backup settimanale delle 6.00. Più criteri di backup consentono flessibilità nella pianificazione dei backup per l'infrastruttura delle macchine virtuali. Ogni criterio di backup può avere più macchine virtuali che sono associate al criterio. La macchina virtuale può essere associata a un solo criterio in un qualsiasi momento.

6. Per ogni macchina virtuale viene creato un processo per configurare il criterio di protezione e associare le macchine virtuali al criterio. Fare clic sulla scheda **Processi** scheda e scegliere il filtro appropriato per visualizzare l'elenco dei processi **Configura protezione**. ![Configure protection job][protect-configure]

7. Al termine, le macchine virtuali sono protette con un criterio e devono attendere il completamento del tempo di backup pianificato per il backup iniziale. La macchina virtuale verrà visualizzata nella scheda **Elementi protetti** e avrà lo stato di protezione *Protetto* \(in attesa di backup iniziale\).

	> [AZURE.NOTE]L'avvio del backup iniziale immediatamente dopo la configurazione della protezione non è un'azione attualmente disponibile.

8. All'ora pianificata, il servizio Backup di Azure crea un processo di backup per ogni macchina virtuale di cui eseguire il backup. Fare clic sulla scheda **Processi** per visualizzare l'elenco dei processi di **Backup**. Come parte dell'operazione di backup, il servizio Backup di Azure esegue un comando di estensione per il backup in ogni macchina virtuale per scaricare tutte le scritture e creare uno snapshot coerente. ![Backup in progress][protect-inprogress]

9. Al termine, lo stato di protezione della macchina virtuale nella scheda **Elementi protetti** verrà visualizzato come *Protetto*. ![Virtual machine is backed up with recovery point][protect-backedup]


## Visualizzazione dello stato di backup e dei relativi dettagli
Dopo la protezione, il numero di macchine virtuali aumenta anche nella pagina di riepilogo **Dashboard**. Inoltre, nella pagina Dashboard viene mostrato il numero di processi che hanno avuto esito positivo e che non sono riusciti nelle ultime 24 ore, nonché quelli ancora in corso. Facendo clic su qualsiasi categoria verrà eseguito il drill-down in quella categoria nella pagina **Processi**. ![Status of backup in Dashboard page][dashboard]


## Risoluzione dei problemi
È possibile risolvere i problemi di individuazione e registrazione usando le informazioni seguenti.

| Operazione di backup | Dettagli errore | Soluzione alternativa |
| -------- | -------- | -------|
| Individuazione | Non è stato possibile individuare nuovi elementi. Si è verificato un errore interno del backup di Microsoft Azure. Attendere alcuni minuti e ripetere l'operazione. | Ripetere il processo di individuazione dopo 15 minuti.
| Individuazione | Non è stato possibile individuare nuovi elementi. Un'altra operazione di individuazione è già in corso. Attendere fino al completamento dell'operazione di individuazione corrente. | Attendere il completamento dell'operazione di individuazione esistente. |
| Opzione Register | Lo stato del ruolo VM di Azure non consente di installare l'estensione. Verificare che la VM si trovi nello stato di esecuzione. L'estensione dei servizi di ripristino di Azure richiede che VM sia in esecuzione. | Avviare la macchina virtuale e quando è nello stato di esecuzione, ripetere l'operazione di registrazione.|


## Coerenza dei punti di ripristino
Nella gestione dei dati di backup, i clienti si preoccupano del comportamento della VM dopo averne effettuato il ripristinato. Le domande tipiche che i clienti pongono sono:

+ La macchina virtuale si riavvierà?

+ I dati saranno disponibili sul disco \(o\) è presente perdita di dati?

+ L'applicazione sarà in grado di leggere i dati o i dati sono danneggiati?

+ I dati saranno coerenti con l'applicazione con loro stessi quando vengono letti dall'applicazione?

Nella tabella seguente vengono illustrati i tipi di coerenza che si verificano durante il backup e il ripristino delle VM di Azure:

| Coerenza | Basata su VSS | Spiegazione e dettagli |
|-------------|-----------|---------|
| Coerenza con l'applicazione | Sì | Si tratta della posizione ideale per i carichi di lavoro Microsoft in quanto garantisce:<ol><li> che la VM *si riavvii* <li>che *non siano presenti danneggiamento dei dati*, <li>*perdita dei dati* e che<li> i dati siano coerenti con l'applicazione che li usa, usando l'applicazione al momento del backup tramite il Servizio snapshot del volume.</ol> Il Servizio snapshot del volume garantisce che i dati vengano scritti correttamente nell'archivio. La maggior parte dei carichi di lavoro di Microsoft ha writer del Servizio snapshot del volume che eseguono azioni specifiche di un carico di lavoro correlate alla coerenza dei dati. Ad esempio, Microsoft SQL Server ha un writer del Servizio snapshot del volume che garantisce l'esecuzione corretta delle operazioni di scrittura nel file di log delle transazioni e nel database.<br><br> Per il backup delle VM di Azure, il recupero di un punto di ripristino coerente con un'applicazione significa che l'estensione per il backup è riuscita a richiamare il flusso di lavoro del Servizio snapshot del volume e a essere completata *correttamente* prima dell'esecuzione dello snapshot della VM. Naturalmente, questo significa che anche i writer del Servizio snapshot del volume di tutte le applicazioni nella VM di Azure sono stati richiamati.<br><br>Leggere le [nozioni di base del Servizio snapshot del volume](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) per approfondimenti sui dettagli di [funzionamento](https://technet.microsoft.com/it-it/library/cc785914%28v=ws.10%29.aspx). |
| Coerenza con i file system | Sì, per i computer Windows | Esistono due scenari in cui il punto di ripristino può essere coerente con i file system:<ul><li>Backup di VM in Azure, poiché Linux non dispone di una piattaforma equivalente al Servizio snapshot del volume.<li>Errore del Servizio snapshot del volume durante il backup di VM in Azure.</li></ul> In entrambi i casi, la cosa migliore da fare è di garantire: <ol><li> che la VM *si avvii* <li>non sia presente *danneggiamento dei dati* e <li>che non sia presente *perdita di dati*.</ol> Le applicazioni devono implementare il proprio meccanismo di correzione sui dati ripristinati.|
| Coerenza dei dati | No | Questa situazione è equivalente a quella di un computer che si sia arrestato in modo anomalo \(a causa di un ripristino software o hardware\). Non esiste alcuna garanzia circa la coerenza dei dati sul supporto di archiviazione. Solo i dati già esistente sul disco al momento del backup vengono acquisiti e sottoposti a backup. <ol><li>Anche se non vi sono garanzie, nella maggior parte dei casi il sistema operativo si avvierà.<li>Questa operazione è in genere seguita da una procedura di controllo del disco come chkdsk per correggere eventuali errori di danneggiamento del disco.<li> I dati in memoria o le scritture che non sono state scaricate completamente sul disco andranno persi.<li> In genere l'applicazione esegue il proprio meccanismo di verifica nel caso in cui sia necessario effettuare il rollback di dati. </ol>Per il backup delle VM di Azure, ottenere un punto di ripristino coerente con l'arresto anomalo significa che Backup di Azure non offre il alcuna garanzia riguardo alla coerenza dei dati di archiviazione, sia dal punto di vista del sistema operativo che dell'applicazione. Questo accade solitamente quando la VM di Azure viene arrestata al momento del backup.<br><br>Ad esempio, se il log delle transazioni contiene voci che non sono presenti nel database, il software del database esegue un rollback fino a quando i dati sono coerenti. Quando si usano dati distribuiti tra più dischi virtuali \(ad esempio volumi con spanning\), un punto di ripristino coerente con l'arresto anomalo del sistema non fornisce alcuna garanzia della correttezza dei dati.|



## Passaggi successivi
Per altre informazioni sulle operazioni iniziali Backup di Azure, vedere:

- [Ripristino di macchine virtuali](backup-azure-restore-vms.md)

[three-steps]: ./media/backup-azure-vms/3-steps-for-backup.png
[select-workload]: ./media/backup-azure-vms/discovery-select-workload.png
[discover-button]: ./media/backup-azure-vms/discover-button.png
[discover-vms]: ./media/backup-azure-vms/discovering-vms.png
[discover-done]: ./media/backup-azure-vms/discovery-complete.png
[register-button]: ./media/backup-azure-vms/register-button.png
[register-job]: ./media/backup-azure-vms/register-create-job.png
[register-vms]: ./media/backup-azure-vms/register-popup.png
[register-status1]: ./media/backup-azure-vms/register-status01.png
[register-status2]: ./media/backup-azure-vms/register-status02.png
[select-workload]: ./media/backup-azure-vms/select-workload.png
[protect-wizard1]: ./media/backup-azure-vms/protect-wizard1.png
[protect-wizard2]: ./media/backup-azure-vms/protect-wizard2.png
[protect-configure]: ./media/backup-azure-vms/protect-configureprotection.png
[protect-inprogress]: ./media/backup-azure-vms/protect-inprogress.png
[protect-backedup]: ./media/backup-azure-vms/protect-backedupvm.png
[dashboard]: ./media/backup-azure-vms/dashboard-protectedvms.png
[prereq]: http://azure.microsoft.com/documentation/articles/backup-azure-vms-introduction/#prerequisites

<!--HONumber=54-->