<properties
	pageTitle="Introduzione al backup virtual machine Azure"
	description="Un'introduzione al backup di macchine virtuali in Azure tramite il servizio di Backup di Azure"
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
	ms.date="05/26/2015"
	ms.author="aashishr"/>

# Backup della macchina virtuale di Azure - introduzione

In questa sezione viene fornita un'introduzione all'utilizzo di Microsoft Azure Backup per proteggere le macchine virtuali di Azure. Leggendo l'articolo si apprenderà:

- Funzionamento di Azure funziona backup macchina virtuale
- La procedura per eseguire il backup della macchina virtuale di Azure
- I prerequisiti per ottenere un'esperienza uniforme di backup
- Gli errori tipici rilevati e come gestirli
- L'elenco di scenari non supportati e come influiscono le modifiche apportate al prodotto

Per ulteriori informazioni sulle macchine virtuali di Azure rapidamente, vedere il [documentazione di Virtual Machine](https://azure.microsoft.com/documentation/services/virtual-machines/).

## Eseguire il backup perché la macchina virtuale di Azure?
Il cloud computing consente alle applicazioni di eseguire in un ambiente altamente disponibile e scalabile, per questo motivo Microsoft ha sviluppato le macchine virtuali di Azure. I dati generati da queste macchine virtuali di Azure sono importanti e richiede di backup per motivi di sicurezza. Scenari tipici che richiedono dati da ripristinare dal backup sono:

- Eliminazione dei file accidentale o intenzionali
- Danneggiamento della macchina virtuale durante l'aggiornamento patch
- Eliminazione accidentale o intenzionali dell'intera macchina virtuale

Dati possono essere eseguiti da queste macchine virtuali in due modi diversi:

- Eseguire il backup di singole origini di dati all'interno della macchina virtuale
- Eseguire il backup intera macchina virtuale

Eseguire il backup dell'intera macchina virtuale è diffuso poiché è molto più semplice gestire e facilita inoltre facile ripristino del sistema operativo e dell'intera applicazione. Azure Backup può essere utilizzato per backup dei dati nei guest o della macchina virtuale completata.

I vantaggi aziendali di con Azure Backup per il backup della macchina virtuale sono:

- Automazione dei flussi di lavoro di backup e ripristino per le macchine virtuali
- I backup coerenti con l'applicazione per garantire che recuperati dati inizia da uno stato coerenza.
- Senza tempi di inattività coinvolti durante il backup della macchina virtuale.
- Windows o le macchine virtuali Linux possono eseguire il backup.
- Punti di ripristino sono disponibili per il ripristino semplice nell'archivio di Backup di Azure.
- Automatico eliminazione e garbage collection di punti di ripristino precedenti.

## Funzionamento della macchina virtuale di Azure backup
Per eseguire il backup di una macchina virtuale, è necessario innanzitutto snapshot dei dati a un punto nel tempo. Il servizio di Backup di Azure avvia il processo di backup all'ora pianificata e attiva l'estensione di backup per eseguire uno snapshot. L'estensione per il backup coordinato con il servizio VSS nel guest per ottenere la consistenza e richiama l'API snapshot del blob del servizio di archiviazione di Azure una volta raggiunta la coerenza. Questa operazione viene eseguita per ottenere uno snapshot coerente dei dischi della macchina virtuale senza che sia necessario arrestarlo.

Dopo l'esecuzione dell'istantanea, i dati vengono trasferiti dal servizio di Backup di Azure all'archivio di backup. Il servizio si occupa di identificazione e trasferire solo i blocchi che sono stati modificati dall'ultimo backup – rendendo efficiente l'archiviazione dei backup. Quando viene completato il trasferimento dei dati, lo snapshot viene rimosso e viene creato un punto di ripristino. Il punto di ripristino può essere visualizzato nel portale di gestione di Azure.

![Architettura di macchina virtuale di Azure backup](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

>[AZURE.NOTE]Per le macchine virtuali Linux, è possibile coerente solo file di backup.

## Calcolo protetti istanze
Macchine virtuali di Azure viene eseguito il backup tramite Backup di Azure sarà soggetto a [dei prezzi di Azure Backup](http://azure.microsoft.com/pricing/details/backup/). Il calcolo istanze protetti si basa sul *effettivo* dimensioni della macchina virtuale, che corrisponde alla somma di tutti i dati nella macchina virtuale, escludendo il disco"risorse". Si è *non* fatturato in base alla dimensione massima supportata per ogni disco dati collegato alla macchina virtuale, ma i dati effettivi archiviati nel disco dati. Analogamente, l'effetto di archiviazione di backup si basa sulla quantità di dati archiviati con Azure Backup, che è la somma dei dati effettivi presenti in ogni punto di ripristino.

Ad esempio, richiedere una macchina virtuale di dimensioni Standard A2 che dispone di due dischi dati aggiuntivi con una dimensione massima di 1TB. Nella tabella seguente fornisce i dati effettivi archiviati in ognuno di questi dischi:

|Tipo di disco|Dimensioni massime|Dati effettivi presenti|
|---------|--------|------|
| Disco del sistema operativo | 1023GB | 17GB |
| Disco locale o su disco di risorsa | 135GB | 5GB (non incluso per il backup) |
| 1 disco dati |	1023GB | 30GB |
| Disco dati 2 | 1023GB | 0GB |

Il *effettivo* dimensioni della macchina virtuale in questo caso sono pari a 17 GB + 30 GB + 0 GB = 47 GB. Questo diventa la dimensione di istanza protetto fattura mensile è basata su. Man mano che aumenta la quantità di dati nella macchina virtuale, la dimensione di istanza protetta utilizzata per la fatturazione anche verrà modificato in modo appropriato.

La fatturazione non viene avviato fino al completamento del primo backup riuscito. A questo punto verrà avviata la fatturazione per l'archiviazione e le istanze protette. La fatturazione continua finché è presente *qualsiasi backup dei dati archiviati con Azure Backup* per la macchina virtuale. L'operazione Arresta protezione dati non interrompe la fatturazione se vengono mantenuti i dati di backup. La fatturazione per una macchina virtuale specificata verrà sospesi solo se viene arrestata la protezione *e* eventuali dati di backup viene eliminati. Quando non sono disponibili processi di backup attivi (quando viene interrotta la protezione), le dimensioni della macchina virtuale al momento dell'ultimo backup completato diventa la dimensione di istanza protetto fattura mensile è basata su.

## Prerequisiti
### 1. Insieme di credenziali di backup
Per avviare il backup delle macchine virtuali di Azure, è necessario innanzitutto creare un archivio di backup. L'insieme di credenziali è un'entità che tutti i backup e i punti di ripristino che sono stati creati nel corso del tempo. L'insieme di credenziali contiene inoltre i criteri di backup che verranno applicati alle macchine virtuali viene eseguito il backup.

Nell'immagine seguente vengono illustrate le relazioni tra le varie entità di Backup di Azure: ![Relazione e l'entità di azure Backup](./media/backup-azure-vms-introduction/vault-policy-vm.png)

### Per creare un archivio di backup

1. Accedere al [portale di gestione](http://manage.windowsazure.com/).

2. Fare clic su **nuova** > **Data Services** > **servizi di ripristino** > **insieme di credenziali di Backup** > **Quick Create**. Se si dispone di più sottoscrizioni associate all'account aziendale, scegliere la sottoscrizione corretta per associare l'insieme di credenziali di backup. In ogni sottoscrizione di Azure è possibile avere più archivi di backup per organizzare le macchine virtuali protette.

3. In **Name** immettere un nome descrittivo per identificare l'insieme di credenziali. Questo deve essere univoco per ogni sottoscrizione.

4. In **Region** selezionare l'area geografica per l'insieme di credenziali. Si noti che l'insieme di credenziali deve essere nella stessa area le macchine virtuali che si desidera proteggere. Se si dispone di macchine virtuali in diverse aree di creare un insieme di credenziali in ognuno di essi. Non è necessario specificare gli account di archiviazione per archiviare i dati di backup: l'archivio di backup e il servizio di Backup di Azure verrà gestita automaticamente. ![Creare un archivio di backup](./media/backup-azure-vms-introduction/backup_vaultcreate.png)

    >[AZURE.NOTE]Macchina virtuale backup utilizzando il servizio di Backup di Azure è supportato solo in selezionare le aree. Controllare l'elenco di [supportati aree](http://azure.microsoft.com/regions/#services). Se l'area che si sta cercando è attualmente supportata, non verrà visualizzata nell'elenco a discesa durante la creazione dell'insieme di credenziali.

5. Fare clic su **creare insieme di credenziali**. La creazione dell'insieme di credenziali per il backup può richiedere alcuni minuti. Monitorare le notifiche di stato nella parte inferiore del portale. ![Creare la notifica popup insieme di credenziali](./media/backup-azure-vms-introduction/creating-vault.png)

6. Un messaggio di conferma che l'insieme di credenziali è stato creato correttamente e verrà elencato nella pagina Servizi di ripristino come attivo. ![Elenco degli archivi di backup](./media/backup-azure-vms-introduction/backup_vaultslist.png)

7. Facendo clic su credenziali per il backup passa per la **Quick Start** pagina, in cui sono visualizzate le istruzioni per il backup di macchine virtuali di Azure. ![Istruzioni backup macchina virtuale nella pagina del Dashboard](./media/backup-azure-vms-introduction/vmbackup-instructions.png)

    >[AZURE.NOTE]Assicurarsi che l'opzione di ridondanza di archiviazione appropriato viene selezionato subito dopo aver creato l'insieme di credenziali. Ulteriori informazioni su [impostazione dell'opzione di ridondanza di archiviazione nell'archivio di backup][vault-storage-redundancy].

### 2. Agente di macchine virtuali
Prima di iniziare a eseguire il backup di macchina virtuale di Azure, assicurarsi che l'agente VM di Azure sia installato correttamente nella macchina virtuale. Per eseguire il backup della macchina virtuale, il servizio di Backup di Azure consente di installare un'estensione per l'agente VM. Poiché l'agente VM è un componente facoltativo nel momento in cui viene creata la macchina virtuale, è necessario verificare che sia selezionata la casella di controllo per l'agente VM prima che venga effettuato il provisioning della macchina virtuale.

Ulteriori informazioni, vedere il [agente VM](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) e [modalità di installazione](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

>[AZURE.NOTE]Se si intende eseguire la migrazione della macchina virtuale dal Data Center locale a Azure, assicurarsi di scaricare e installare l'agente VM MSI prima l'inizio del processo di migrazione. Questo vale anche per le macchine virtuali protette in Azure con Site Recovery di Azure.

## Limitazioni durante l'anteprima

- Backup delle macchine virtuali con più di 5 dischi non è supportata.
- Backup di macchine virtuali tramite archiviazione Premium non è supportato.
- Backup di macchine virtuali che utilizzano più schede di rete o in una configurazione con carico bilanciato non è supportato.
- Sostituzione di una macchina virtuale esistente durante il ripristino non è supportata. Eliminare prima la macchina virtuale esistente e i dischi associati e quindi ripristinare i dati dal backup.
- Backup delle macchine virtuali ripristinato utilizzando Azure Site Recovery non è supportata.
- Tra aree di backup e ripristino non è supportata.
- Macchina virtuale backup utilizzando il servizio di Backup di Azure è supportato solo in selezionare le aree. Controllare l'elenco di [supportati aree](http://azure.microsoft.com/regions/#services). Se l'area che si sta cercando è attualmente supportata, non verrà visualizzata nell'elenco a discesa durante la creazione dell'insieme di credenziali.
- Backup della macchina virtuale utilizzando il servizio di Backup di Azure solo è supportata solo per alcune versioni del sistema operativo:
  - **Linux**: l'elenco delle distribuzioni approvate da Azure è disponibile [qui](virtual-machines-linux-endorsed-distributions.md). Altri porta-del-proprietari-distribuzioni Linux inoltre dovrebbero funzionare fino a quando l'agente VM è disponibile nella macchina virtuale.
  - **Windows Server**: versioni antecedenti a Windows Server 2008 R2 non sono supportate.

Se tutte le funzionalità che si desidera vedere incluso, [inviare commenti e suggerimenti](http://aka.ms/azurebackup_feedback).

## Passaggi successivi
Per iniziare a utilizzare il backup della macchina virtuale, informazioni su come:

- [Individuare, registrare e proteggere le macchine virtuali](backup-azure-vms.md)

- [Ripristino di macchine virtuali](backup-azure-restore-vms.md)

+ Monitorare i processi di backup

<!---HONumber=GIT-SubDir-->