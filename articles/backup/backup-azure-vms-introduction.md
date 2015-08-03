<properties
	pageTitle="Introduzione al backup delle macchine virtuali di Azure"
	description="Introduzione al backup delle macchine virtuali di Azure usando il servizio Backup di Azure"
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

# Backup di macchine virtuali di Azure - Introduzione

Questa sezione offre un'introduzione all'uso del servizio Backup di Microsoft Azure per la protezione delle macchine virtuali di Azure. Leggendo l'articolo si apprenderà quanto segue:

- Come viene eseguito il backup di una macchina virtuale di Azure
- La procedura per eseguire il backup della macchina virtuale di Azure
- I prerequisiti per eseguire un backup senza problemi
- Gli errori tipici rilevati e come gestirli
- L'elenco degli scenari non supportati e come suggerire modifiche al prodotto

Per altre informazioni sulle macchine virtuali, vedere [Macchine virtuali - Documentazione](https://azure.microsoft.com/documentation/services/virtual-machines/).

## Perché eseguire il backup della macchina virtuale di Azure?
Il cloud computing consente l'esecuzione delle applicazioni in un ambiente scalabile a disponibilità elevata ed è questo il motivo per cui Microsoft ha sviluppato le macchine virtuali di Azure. I dati generati dalle macchine virtuali di Azure sono importanti ed è necessario eseguirne il backup per motivi di sicurezza. Di seguito sono indicati gli scenari tipici che rendono necessario il ripristino da backup:

- Eliminazione di file accidentale o da parte di utenti malintenzionati
- Danneggiamento della macchina virtuale durante l'aggiornamento di una patch
- Eliminazione dell'intera macchina virtuale accidentale o da parte di utenti malintenzionati

È possibile eseguire il backup dei dati da queste macchine virtuali in due modi diversi:

- Eseguire il backup delle singole origini dati dall'interno della macchina virtuale
- Eseguire il backup dell'intera macchina virtuale

L'esecuzione del backup dell'intera macchina virtuale è il metodo più comune perché è molto più semplice da gestire e facilita il completo ripristino dell'applicazione e del sistema operativo. È possibile usare Backup di Azure per eseguire il backup dei dati in-guest o dell'intera macchina virtuale.

Di seguito sono elencati i vantaggi per l'azienda derivanti dall'uso del servizio Backup di Azure per il backup delle macchine virtuali:

- Automazione dei flussi di lavoro di backup e ripristino delle macchine virtuali
- Backup coerenti con l'applicazione per assicurare che i dati ripristinati vengano avviati da uno stato coerente.
- Nessun tempo di inattività durante il backup della macchina virtuale.
- Possibilità di eseguire il backup di macchine virtuali Windows o Linux.
- Disponibilità di punti di ripristino per un facile ripristino nell'insieme di credenziali di Backup di Azure.
- Eliminazione e garbage collection automatiche dei punti di ripristino precedenti.

## Come viene eseguito il backup di una macchina virtuale di Azure?
Per eseguire il backup di una macchina virtuale è prima necessario acquisire uno snapshot dei dati in un momento specifico. Il servizio Backup di Azure avvia il processo di backup all'ora pianificata e avvia l'estensione per il backup per acquisire uno snapshot. L'estensione per il backup si coordina con il servizio VSS in-guest per assicurare coerenza e, dopo averla ottenuta, richiama l'API snapshot del BLOB del servizio Archiviazione di Azure. Questa operazione viene eseguita per ottenere uno snapshot coerente dei dischi della macchina virtuale senza che sia necessario arrestarla.

Dopo l'acquisizione dello snapshot, il servizio Backup di Azure trasferisce i dati all'insieme di credenziali per il backup. Il servizio provvede a identificare e trasferire soltanto i blocchi che sono stati modificati dopo l'ultimo backup, rendendo in questo modo efficiente l'archiviazione dei backup. Quando il trasferimento dei dati è completato, lo snapshot viene rimosso e viene creato un punto di ripristino. È possibile visualizzare tale punto di ripristino nel portale di gestione di Azure.

![Architettura del backup delle macchine virtuali di Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

>[AZURE.NOTE]Per quanto riguarda le macchine virtuali Linux, è possibile eseguire soltanto backup coerenti a livello di file.

## Calcolo delle istanze protette
Le macchine virtuali di Azure di cui viene eseguito il backup mediante Backup di Azure sono soggette a fatturazione sulla base dei [prezzi di tale servizio](http://azure.microsoft.com/pricing/details/backup/). Il calcolo delle istanze protette si basa sulle dimensioni *effettive* della macchina virtuale, ovvero sul totale di tutti i dati presenti in quest'ultima con esclusione del “disco risorse”. La fatturazione *non* sarà basata sulle dimensioni massime supportate per ogni disco dati collegato alla macchina virtuale, ma sui dati effettivi archiviati nel disco dati. Analogamente, la fattura relativa all'archiviazione dei backup è basata sulla quantità di dati archiviata con Backup di Azure, ovvero sul totale dei dati effettivi presenti in ogni punto di ripristino.

Si prenda ad esempio una macchina virtuale di dimensioni A2-Standard dotata di due dischi dati aggiuntivi con capacità massima di 1 TB ciascuno. La tabella seguente indica i dati effettivi archiviati in ciascuno di tali dischi:

|Tipo di disco|Dimensioni massime|Dati effettivi presenti|
|---------|--------|------|
| Disco del sistema operativo | 1023 GB | 17 GB |
| Disco locale/Disco risorse | 135 GB | 5 GB (non incluso per il backup) |
| Disco dati 1 |	1023 GB | 30 GB |
| Disco dati 2 | 1023 GB | 0 GB |

In questo caso, le dimensioni *effettive* della macchina virtuale sono 17 GB + 30 GB + 0 GB = 47 GB. Queste dimensioni diventano le dimensioni dell'istanza protetta su cui è basata la fattura mensile. Con l'aumentare della quantità di dati presente nella macchina virtuale, le dimensioni dell'istanza protetta usata per la fatturazione cambiano di conseguenza.

La fatturazione inizia solo dopo il completamento del primo backup corretto. A questo punto inizierà la fatturazione sia per le istanze di archiviazione sia per quelle protette. La fatturazione continua fino a quando per la macchina virtuale sono presenti *dati di backup archiviati con Backup di Azure*. Se i dati di backup vengono mantenuti, l'esecuzione dell'operazione Arresta protezione non interrompe la fatturazione. La fatturazione relativa a una macchina virtuale specificata verrà interrotta solo se la protezione viene arrestata *e* i dati di backup vengono eliminati. Quando non sono presenti processi di backup attivi (quando la protezione è stata interrotta), le dimensioni della macchina virtuale al momento dell'ultimo backup corretto diventano le dimensioni dell'istanza protetta su cui è basata la fattura mensile.

## Prerequisiti
### 1. Insieme di credenziali per il backup
Per avviare il backup delle macchine virtuali di Azure è prima necessario creare un insieme di credenziali per il backup. L'insieme di credenziali per il backup è un'entità che archivia tutti i backup e i punti di ripristino che sono stati creati nel corso del tempo. L'insieme di credenziali contiene inoltre i criteri di backup che verranno applicati alle macchine virtuali di cui viene eseguito il backup.

La figura seguente mostra le relazioni tra le diverse entità di backup di Azure: ![Entità e relazioni di Backup di Azure](./media/backup-azure-vms-introduction/vault-policy-vm.png)

### Per creare un insieme di credenziali per il backup

1. Accedere al [portale di gestione](http://manage.windowsazure.com/).

2. Fare clic su **Nuovo** > **Servizi dati** > **Servizi di ripristino** > **Insieme di credenziali per il backup** > **Creazione rapida**. Se all'account aziendale sono associate più sottoscrizioni, scegliere quella corretta da associare all'insieme di credenziali per il backup. In ogni sottoscrizione di Azure possono essere presenti più insiemi di credenziali per il backup per organizzare la protezione delle macchine virtuali.

3. In **Nome** immettere un nome descrittivo per identificare l'insieme di credenziali. È necessario che il nome sia univoco per ogni sottoscrizione.

4. In **Area** selezionare l'area geografica per l'insieme di credenziali. Si tenga presente che l'insieme di credenziali deve trovarsi nella stessa area geografica delle macchine virtuali che si desidera proteggere. Se si dispone di macchine virtuali situate in aree geografiche differenti, creare un insieme di credenziali in ciascuna di esse. Per archiviare i dati di backup non è necessario specificare account di archiviazione perché l'insieme di credenziali per il backup e il servizio Backup di Azure gestiranno questa operazione in modo automatico. ![Creare un insieme di credenziali per il backup](./media/backup-azure-vms-introduction/backup_vaultcreate.png)

    >[AZURE.NOTE]Il backup di macchine virtuali mediante il servizio Backup di Azure è supportato solo in specifiche aree geografiche. Controllare l'elenco delle [aree geografiche supportate](http://azure.microsoft.com/regions/#services). Se l'area che si sta cercando non è attualmente supportata, tale area non verrà visualizzata nell'elenco a discesa durante la creazione dell'insieme di credenziali.

5. Fare clic su **Crea insieme di credenziali**. La creazione dell'insieme di credenziali per il backup può richiedere alcuni minuti. Monitorare le notifiche di stato nella parte inferiore del portale. ![Creare una notifica di tipo avviso popup dell'insieme di credenziali](./media/backup-azure-vms-introduction/creating-vault.png)

6. Viene visualizzato un messaggio per confermare che l'insieme di credenziali è stato creato correttamente. L'insieme di credenziali verrà quindi elencato come attivo nella pagina Servizi di ripristino. ![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-vms-introduction/backup_vaultslist.png)

7. Se si fa clic sull'insieme di credenziali per il backup, viene visualizzata la pagina **Guida introduttiva** in cui sono riportate le istruzioni per il backup delle macchine virtuali di Azure. ![Istruzioni per il backup delle macchine virtuali nella pagina Dashboard](./media/backup-azure-vms-introduction/vmbackup-instructions.png)

    >[AZURE.NOTE]Subito dopo la creazione dell'insieme di credenziali, assicurarsi di aver scelto l'opzione di ridondanza dell'archiviazione corretta. Leggere l'articolo relativo all'[impostazione dell'opzione di ridondanza nell'insieme di credenziali per il backup[vault-archiviazione-ridondanza].

### 2. Agente di macchine virtuali
Prima di iniziare ad eseguire il backup della macchina virtuale di Azure, assicurarsi che l'agente di macchine virtuali di Azure (agente VM) sia installato correttamente nella macchina virtuale. Per eseguire il backup della macchina virtuale, il servizio Backup di Azure installa un'estensione nell'agente di macchine virtuali. Poiché al momento della creazione della macchina virtuale l'agente VM è un componente opzionale, è necessario assicurarsi che la relativa casella di controllo sia selezionata prima di eseguire il provisioning della macchina virtuale.

Per altre informazioni, leggere gli articoli relativi all'[agente VM](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) e all'[installazione dell'agente VM](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

>[AZURE.NOTE]Se si intende eseguire la migrazione della macchina virtuale dal data center locale a Azure, assicurarsi di scaricare e installare il file con estensione msi dell'agente VM prima di avviare il processo di migrazione. Questo vale anche per le macchine virtuali protette in Azure mediante Azure Site Recovery.

## Limitazioni durante l'anteprima

- Il backup di macchine virtuali con più di 5 dischi non è supportato.
- Il backup di macchine virtuali con Archiviazione Premium non è supportato.
- Il backup di macchine virtuali con più NIC (Network Interface Card) o presenti in una configurazione con bilanciamento del carico non è supportato.
- La sostituzione di una macchina virtuale esistente durante il ripristino non è supportata. È necessario eliminare prima la macchina virtuale esistente e gli eventuali dischi associati e quindi ripristinare i dati dal backup.
- Il backup di macchine virtuali ripristinate mediante Azure Site Recovery non è supportato.
- L'operazione di backup e ripristino tra aree geografiche diverse non è supportata.
- Il backup di macchine virtuali mediante il servizio Backup di Azure è supportato solo in specifiche aree geografiche. Controllare l'elenco delle [aree geografiche supportate](http://azure.microsoft.com/regions/#services). Se l'area che si sta cercando non è attualmente supportata, tale area non verrà visualizzata nell'elenco a discesa durante la creazione dell'insieme di credenziali.
- Il backup di macchine virtuali mediante il servizio Backup di Azure è supportato soltanto per versioni specifiche dei sistemi operativi seguenti:
  - **Linux**: l'elenco delle distribuzioni approvate da Azure è disponibile [qui](../virtual-machines-linux-endorsed-distributions.md). È possibile usare altre distribuzioni personali di Linux a condizione che l'agente VM sia disponibile nella macchina virtuale.
  - **Windows Server**: le versioni precedenti a Windows Server 2008 R2 non sono supportate.

Se si desidera includere funzionalità aggiuntive, è possibile [inviare commenti e suggerimenti](http://aka.ms/azurebackup_feedback).

## Passaggi successivi
Per iniziare a eseguire il backup di macchine virtuali, leggere le informazioni relative a:

- [Individuare, registrare e proteggere le macchine virtuali](backup-azure-vms.md)

- [Ripristino di macchine virtuali](backup-azure-restore-vms.md)

+ Monitorare i processi di backup


 

<!---HONumber=July15_HO4-->