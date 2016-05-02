
<properties
	pageTitle="Ripristinare una macchina virtuale dal backup | Microsoft Azure"
	description="Informazioni su come ripristinare una macchina virtuale di Azure da un punto di ripristino"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""
	keywords="ripristinare il backup; come ripristinare; punto di ripristino."/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/22/2016"
	ms.author="trinadhk; jimpark;"/>


# Ripristinare macchine virtuali in Azure

> [AZURE.SELECTOR]
- [Ripristinare VM nel portale di Azure](backup-azure-arm-restore-vms.md)
- [Ripristinare VM nel portale classico](backup-azure-restore-vms.md)


Ripristinare una macchina virtuale in una nuova dai backup archiviati in un insieme di credenziali per il backup di Azure, seguendo questa procedura.

## Ripristinare un flusso di lavoro

### 1\. Scegliere un elemento da ripristinare

1. Passare alla scheda **Elementi protetti** e selezionare la macchina virtuale che si desidera ripristinare in una nuova macchina virtuale.

    ![Elementi protetti](./media/backup-azure-restore-vms/protected-items.png)

    Nella colonna **Punto di ripristino** della pagina **Elementi protetti** verrà visualizzato il numero di punti di ripristino per una macchina virtuale. Nella colonna **Punto di ripristino più recente** viene visualizzata l'ora del backup più recente da cui è possibile ripristinare una macchina virtuale.

2. Fare clic su **Ripristina** per aprire la procedura guidata **Ripristina elemento**.

    ![Ripristinare un elemento](./media/backup-azure-restore-vms/restore-item.png)

### 2\. Scegliere un punto di ripristino

1. Nella schermata per la **selezione di un punto di ripristino** è possibile eseguire il ripristino dal punto più recente o da un punto precedente. L'opzione predefinita selezionata all'avvio della procedura guidata è *Punto di ripristino più recente*.

    ![Selezionare un punto di ripristino](./media/backup-azure-restore-vms/select-recovery-point.png)

2. Per selezionare un punto precedente, scegliere l'opzione **Seleziona data** nell'elenco a discesa e selezionare una data nel controllo calendario facendo clic sull'**icona del calendario**. Nel controllo tutte le date in cui sono presenti punti di ripristino hanno un'ombreggiatura di colore grigio chiaro e sono selezionabili dall'utente.

    ![Selezionare una data](./media/backup-azure-restore-vms/select-date.png)

    Quando si fa clic su una data nel controllo calendario, i punti di ripristino disponibili in quella data verranno visualizzati nella tabella dei punti di ripristino riportata di seguito. Nella colonna **Ora** viene visualizzata l'ora in cui è stato creato lo snapshot. Nella colonna **Tipo** viene visualizzata la [coerenza](https://azure.microsoft.com/documentation/articles/backup-azure-vms/#consistency-of-recovery-points) del punto di ripristino. L'intestazione della tabella mostra il numero di punti di ripristino disponibili per il giorno in parentesi.

    ![Punti di ripristino](./media/backup-azure-restore-vms/recovery-points.png)

3. Selezionare il punto di ripristino dalla tabella **Punti di ripristino** e fare clic sulla freccia Avanti per passare alla schermata successiva.

### 3\. Specificare un percorso di destinazione

1. Nella schermata per la **selezione di un'istanza di ripristino** specificare i dettagli del percorso in cui ripristinare la macchina virtuale.

  - Specificare il nome della macchina virtuale: in un determinato servizio cloud il nome della macchina virtuale deve essere univoco. Se si prevede di sostituire una macchina virtuale esistente con lo stesso nome, eliminare prima la macchina virtuale esistente e i relativi dischi dati, quindi ripristinare i dati da Backup di Azure.
  - Selezionare un servizio cloud per la macchina virtuale: questa operazione è necessaria per la creazione di una macchina virtuale. È possibile scegliere di usare un servizio cloud esistente o di crearne uno nuovo.

        Whatever cloud service name is picked should be globally unique. Typically, the cloud service name gets associated with a public-facing URL in the form of [cloudservice].cloudapp.net. Azure will not allow you to create a new cloud service if the name has already been used. If you choose to create select create a new cloud service, it will be given the same name as the virtual machine – in which case the VM name picked should be unique enough to be applied to the associated cloud service.

        We only display cloud services and virtual networks that are not associated with any affinity groups in the restore instance details. [Learn More](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

2. Selezionare un account di archiviazione per la macchina virtuale: questa operazione è necessaria per la creazione di una macchina virtuale. È possibile selezionare uno degli account di archiviazione esistenti nella stessa area geografica dell'insieme di credenziali di Backup di Azure. Gli account di archiviazione con ridondanza della zona o con archiviazione di tipo Premium non sono supportati.

    Se non è presente alcun account di archiviazione con una configurazione supportata, crearne uno prima di avviare l'operazione di ripristino.

    ![Selezionare una rete virtuale](./media/backup-azure-restore-vms/restore-sa.png)

3. Selezionare una rete virtuale: la rete virtuale (VNET) per la macchina virtuale dovrebbe essere selezionata al momento della creazione della macchina virtuale. L'interfaccia utente di ripristino mostra tutte le reti virtuali all'interno della sottoscrizione che possono essere usate. Non è obbligatorio selezionare una rete virtuale per la macchina virtuale ripristinata: sarà possibile connettersi alla macchina virtuale ripristinata tramite Internet, anche se non viene applicata la rete virtuale.

    Se il servizio cloud selezionato è associato a una rete virtuale, non è possibile modificare la rete virtuale.

    ![Selezionare una rete virtuale](./media/backup-azure-restore-vms/restore-cs-vnet.png)

4. Selezionare una subnet: se la rete virtuale dispone di più subnet, per impostazione predefinita verrà selezionata la prima subnet. Scegliere la subnet desiderata tra le opzioni nell'elenco a discesa. Per visualizzare informazioni dettagliate sulla subnet, passare all'estensione Reti nella [home page del portale](https://manage.windowsazure.com/), passare a **Reti virtuali**, selezionare la rete virtuale ed eseguire il drill-down in Configura.

    ![Selezionare una subnet](./media/backup-azure-restore-vms/select-subnet.png)

5. Fare clic sull'icona **Invia** nella procedura guidata per inviare i dettagli e creare un processo di ripristino.

## Tenere traccia dell'operazione di ripristino
Dopo aver immesso tutte le informazioni nella procedura guidata di ripristino e averle inviate, Backup di Azure tenterà di creare un processo per tenere traccia dell'operazione di ripristino.

![Creazione di un processo di ripristino](./media/backup-azure-restore-vms/create-restore-job.png)

Se la creazione del processo ha esito positivo, verrà visualizzata una notifica di tipo avviso popup di conferma della creazione del processo. È possibile ottenere altri dettagli facendo clic sul pulsante **Visualizza processo** che consentirà di passare alla scheda **Processi**.

![Processo di ripristino creato](./media/backup-azure-restore-vms/restore-job-created.png)

Al termine dell'operazione di ripristino, l'operazione verrà contrassegnata come completata nella scheda **Processi**.

![Processo di ripristino completato](./media/backup-azure-restore-vms/restore-job-complete.png)

Dopo aver ripristinato la macchina virtuale, può essere necessario reinstallare le estensioni esistenti nella macchina virtuale originale e [modificare gli endpoint](virtual-machines-set-up-endpoints) per la macchina virtuale nel portale di Azure.

## Ripristino delle macchine virtuali del controller di dominio
L'esecuzione del backup delle macchine virtuali del controller di dominio (DC) è uno scenario supportato da Backup di Azure. Tuttavia è necessario prestare attenzione ad alcuni aspetti durante il processo di ripristino. L'esperienza di ripristino è notevolmente diversa per le macchine virtuali del controller di dominio in una configurazione con un singolo controller di dominio rispetto a quella per le macchine virtuali in una configurazione con più controller di dominio.

### Controller di dominio singolo
La macchina virtuale può essere ripristinata (come qualsiasi altra macchina virtuale) dal portale di Azure o tramite PowerShell.

### Più controller di dominio
Se si dispone di un ambiente con più controller di dominio, i controller di dominio adottano una particolare procedura per mantenere i dati sincronizzati. Quando viene ripristinato un punto di backup meno recente *senza le dovute precauzioni*, il processo di rollback di un numero di sequenza di aggiornamento può causare seri problemi in un ambiente con più controller di dominio. Il modo migliore per ripristinare questa macchina virtuale è l'avvio in modalità DSRM.

Il problema si verifica perché la modalità DSRM non è presente in Azure. Per ripristinare questa macchina virtuale, non è quindi possibile usare il portale di Azure. L'unico meccanismo di ripristino supportato è quello basato su disco tramite PowerShell.

>[AZURE.WARNING] Per le macchine virtuali del controller di dominio in un ambiente con più controller di dominio, non usare il portale di Azure per il ripristino. È supportato solo il ripristino basato su PowerShell.

Altre informazioni sul [problema del rollback di un numero di sequenza di aggiornamento](https://technet.microsoft.com/library/dd363553) e sulle strategie consigliate per risolverlo.

## Ripristino delle macchine virtuali con configurazioni di rete speciali
Backup di Azure supporta il backup per le configurazioni di rete speciali seguenti delle macchine virtuali.

- Macchine virtuali nel servizio di bilanciamento del carico (interno ed esterno)
- Macchine virtuali con più indirizzi IP riservati
- Macchine virtuali con più NIC

Queste configurazioni rendono necessarie le considerazioni seguenti durante il ripristino.

>[AZURE.TIP] Usare il flusso di ripristino basato su PowerShell per ricreare la configurazione di rete speciale delle macchine virtuali dopo il ripristino.

### Ripristino dall'interfaccia utente:
Durante il ripristino dall'interfaccia utente, **scegliere sempre un nuovo servizio cloud**. Poiché il portale accetta solo parametri obbligatori durante il flusso di ripristino, tenere presente che le macchine virtuali ripristinate tramite l'interfaccia utente perderanno la configurazione di rete speciale di cui dispongono. In altre parole, le macchine virtuali ripristinate saranno macchine virtuali normali senza la configurazione del bilanciamento del carico o di più NIC o di più indirizzi IP riservati.

### Ripristino da PowerShell:
PowerShell offre la possibilità di ripristinare solo i dischi della macchina virtuale da un backup, senza creare la macchina virtuale. Questa soluzione è utile durante il ripristino di macchine virtuali che richiedono le configurazioni di rete speciali descritte in precedenza.

Per poter ricreare completamente i dischi della macchina virtuale dopo il ripristino, seguire questa procedura:

1. Ripristinare i dischi dall'insieme di credenziali di backup tramite [PowerShell per il Backup di Azure](../backup-azure-vms-automation.md#restore-an-azure-vm)

2. Creare la configurazione della macchina virtuale necessaria per il bilanciamento del carico/per più NIC/per più indirizzi IP riservati tramite i cmdlet di PowerShell e usarla per creare la macchina virtuale con la configurazione desiderata.
	- Creare una macchina virtuale nel servizio cloud con [bilanciamento del carico interno](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)
	- Creare una macchina virtuale connessa al [servizio di bilanciamento del carico con connessione Internet](https://azure.microsoft.com/it-IT/documentation/articles/load-balancer-internet-getstarted/)
	- Creare una macchina virtuale con [più NIC](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/)
	- Creare una macchina virtuale con [più indirizzi IP riservati](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/)


## Passaggi successivi
- [Risoluzione dei problemi](backup-azure-vms-troubleshoot.md#restore)
- [Gestire le macchine virtuali](backup-azure-manage-vms.md)

<!---HONumber=AcomDC_0420_2016-->