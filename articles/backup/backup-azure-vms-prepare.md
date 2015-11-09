<properties
	pageTitle="Preparazione dell'ambiente per il backup di macchine virtuali di Azure | Microsoft Azure"
	description="Assicurarsi che l'ambiente sia pronto per il backup di macchine virtuali di Azure"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/23/2015"
	ms.author="trinadhk; aashishr; jimpark; markgal"/>

# Preparazione dell'ambiente per il backup di macchine virtuali di Azure
Prima di eseguire il backup di una macchina virtuale di Azure, è necessario completare questi prerequisiti per preparare l'ambiente. Se è già stato fatto, è possibile avviare il [backup delle VM](backup-azure-vms.md). In caso contrario, attenersi ai passaggi seguenti per assicurarsi che l'ambiente sia pronto.

## 1\. Insieme di credenziali per il backup
Per avviare il backup delle macchine virtuali di Azure, è prima necessario creare un insieme di credenziali per il backup. Un insieme di credenziali è un'entità che archivia tutti i backup e i punti di ripristino che sono stati creati nel corso del tempo. L'insieme di credenziali contiene inoltre i criteri di backup che verranno applicati alle macchine virtuali di cui viene eseguito il backup.

Questa figura mostra le relazioni tra le diverse entità di backup di Azure: ![Entità e relazioni di Backup di Azure](./media/backup-azure-vms-prepare/vault-policy-vm.png)

Per creare un insieme di credenziali per il backup:

1. Accedere al [portale di gestione](http://manage.windowsazure.com/).

2. Fare clic su **Nuovo** > **Servizi dati** > **Servizi di ripristino** > **Insieme di credenziali per il backup** > **Creazione rapida**. Se all'account aziendale sono associate più sottoscrizioni, scegliere quella corretta da associare all'insieme di credenziali per il backup. In ogni sottoscrizione di Azure possono essere presenti più insiemi di credenziali per il backup per organizzare la protezione delle macchine virtuali.

3. In **Nome** immettere un nome descrittivo per identificare l'insieme di credenziali. È necessario che il nome sia univoco per ogni sottoscrizione.

4. In **Region** selezionare l'area geografica per l'insieme di credenziali. L'insieme di credenziali deve trovarsi nella stessa area geografica delle macchine virtuali che si vuole proteggere. Se si dispone di macchine virtuali situate in aree geografiche differenti, creare un insieme di credenziali in ciascuna di esse. Per archiviare i dati di backup, non è necessario specificare account di archiviazione perché l'insieme di credenziali per il backup e il servizio Backup di Azure gestiscono questa operazione in modo automatico.

    ![Creare un insieme di credenziali per il backup](./media/backup-azure-vms-prepare/backup_vaultcreate.png)

5. Fare clic su **Crea insieme di credenziali**. La creazione dell'insieme di credenziali per il backup può richiedere alcuni minuti. Monitorare le notifiche di stato nella parte inferiore del portale.

    ![Creare una notifica di tipo avviso popup dell'insieme di credenziali](./media/backup-azure-vms-prepare/creating-vault.png)

6. Viene visualizzato un messaggio per confermare che l'insieme di credenziali è stato creato correttamente. L'insieme di credenziali verrà quindi elencato come attivo nella pagina Servizi di ripristino. Subito dopo la creazione dell'insieme di credenziali, assicurarsi di avere scelto l'opzione di ridondanza dell'archiviazione corretta. Leggere l'articolo relativo all'[impostazione dell'opzione di ridondanza nell'insieme di credenziali per il backup](backup-configure-vault.md#azure-backup---storage-redundancy-options).

    ![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-vms-prepare/backup_vaultslist.png)

7. Fare clic sull'insieme di credenziali per il backup per visualizzare la pagina **Guida introduttiva** in cui sono riportate le istruzioni per il backup delle macchine virtuali di Azure.

    ![Istruzioni per il backup delle macchine virtuali nella pagina Dashboard](./media/backup-azure-vms-prepare/vmbackup-instructions.png)

## 2\. Agente di macchine virtuali
Prima di eseguire il backup della macchina virtuale di Azure, è consigliabile assicurarsi che l'agente di macchine virtuali di Azure (agente VM) sia installato correttamente nella macchina virtuale. Poiché al momento della creazione della macchina virtuale l'agente VM è un componente opzionale, assicurarsi che la relativa casella di controllo sia selezionata prima di eseguire il provisioning della macchina virtuale.

Per altre informazioni, leggere gli articoli relativi all'[agente VM](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) e all'[installazione dell'agente VM](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

### Estensione di backup
Per eseguire il backup della macchina virtuale, il servizio Backup di Azure installa un'estensione nell'agente di macchine virtuali. Il servizio Backup di Azure applica aggiornamenti e patch all'estensione di backup senza ulteriore intervento dell'utente.

L'estensione per il backup viene installata se la macchina virtuale è in esecuzione. Una macchina virtuale in esecuzione consente anche di ottenere un punto di ripristino coerente con l'applicazione. Il servizio Backup di Azure continuerà tuttavia a eseguire il backup della macchina virtuale, anche se questa è spenta e non è stato possibile installare l'estensione (macchina virtuale offline). In questo caso, il punto di ripristino sarà *coerente con l'arresto anomalo*, come indicato in precedenza.

## 3\. Connessione di rete
Per funzionare correttamente, l'estensione di backup richiede la connettività a Internet perché invia i comandi a un endpoint di archiviazione di Azure (URL HTTP) per gestire gli snapshot della VM. Senza la connettività a Internet, si verificherà il timeout di queste richieste HTTP dalla VM e l'operazione di backup non riuscirà.

## Limitazioni

- Il backup di macchine virtuali basate su Gestione risorse di Azure (IaaS V2) non è supportato.
- Il backup di macchine virtuali con più di 16 dischi dati non è supportato.
- Il backup di macchine virtuali con Archiviazione Premium non è supportato.
- Il backup di macchine virtuali con più IP riservati non è supportato.
- Il backup di macchine virtuali con un indirizzo IP riservato e nessun endpoint definito non è supportato.
- Il backup di macchine virtuali con più schede di rete non è supportato.
- Il backup di macchine virtuali in una configurazione con carico bilanciato (interna e su Internet) non è supportato.
- La sostituzione di una macchina virtuale esistente durante il ripristino non è supportata. È necessario eliminare prima la macchina virtuale esistente e gli eventuali dischi associati e quindi ripristinare i dati dal backup.
- L'operazione di backup e ripristino tra aree geografiche diverse non è supportata.
- Il backup di macchine virtuali con il servizio Backup di Azure è supportato in tutte le aree pubbliche. Ecco un [elenco di controllo](http://azure.microsoft.com/regions/#services) delle aree geografiche supportate. Se l'area che si sta cercando non è attualmente supportata, tale area non verrà visualizzata nell'elenco a discesa durante la creazione dell'insieme di credenziali.
- Il backup di macchine virtuali con il servizio Backup di Azure è supportato soltanto per versioni specifiche dei sistemi operativi seguenti:
  - **Linux**: l'elenco delle distribuzioni approvate da Azure è disponibile [qui](../virtual-machines-linux-endorsed-distributions.md). È possibile usare altre distribuzioni personali di Linux a condizione che l'agente VM sia disponibile nella macchina virtuale.
  - **Windows Server**: le versioni precedenti a Windows Server 2008 R2 non sono supportate.
- Il ripristino di un controller di dominio di VM che fa parte di una configurazione con controller di dominio è supportato solo tramite PowerShell. Altre informazioni sul [ripristino di un controller di dominio con più controller di dominio](backup-azure-restore-vms.md#restoring-domain-controller-vms)

## Domande?
In caso di domande o se si vuole che venga inclusa una funzionalità, è possibile [inviare commenti e suggerimenti](http://aka.ms/azurebackup_feedback).

## Passaggi successivi

- [Pianificare l'infrastruttura di backup delle VM](backup-azure-vms-introduction.md)
- [Eseguire il backup di macchine virtuali](backup-azure-vms.md)
- [Gestire i backup delle macchine virtuali](backup-azure-manage-vms.md)

<!---HONumber=Nov15_HO1-->