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
	ms.date="01/19/2016"
	ms.author="trinadhk; jimpark; markgal;"/>

# Preparare l'ambiente per il backup di macchine virtuali di Azure
Prima di eseguire il backup di una macchina virtuale di Azure, è necessario completare questi prerequisiti per preparare l'ambiente. Se questa operazione è già stata eseguita, è possibile avviare il [backup delle macchine virtuali](backup-azure-vms.md). In caso contrario, seguire questa procedura per assicurarsi che l'ambiente sia pronto.


## 1\. Insieme di credenziali per il backup

![Insieme di credenziali per il backup](./media/backup-azure-vms-prepare/step1.png)

Per avviare il backup delle macchine virtuali di Azure, è prima necessario creare un insieme di credenziali per il backup. Un insieme di credenziali è un'entità che archivia tutti i backup e i punti di ripristino che sono stati creati nel corso del tempo. L'insieme di credenziali contiene inoltre i criteri di backup che verranno applicati alle macchine virtuali di cui viene eseguito il backup.

Questa figura mostra le relazioni tra le diverse entità di Backup di Azure: ![Entità e relazioni di Backup di Azure](./media/backup-azure-vms-prepare/vault-policy-vm.png)

Per creare un insieme di credenziali per il backup:

1. Accedere al [portale di Azure](http://manage.windowsazure.com/).

2. Fare clic su **Nuovo** > **Servizi dati** > **Servizi di ripristino** > **Insieme di credenziali per il backup** > **Creazione rapida**. Se all'account aziendale sono associate più sottoscrizioni, scegliere quella corretta da associare all'insieme di credenziali per il backup. In ogni sottoscrizione di Azure possono essere presenti più insiemi di credenziali per il backup per organizzare la protezione delle macchine virtuali.

3. In **Nome** immettere un nome descrittivo per identificare l'insieme di credenziali. È necessario che il nome sia univoco per ogni sottoscrizione.

4. In **Region** selezionare l'area geografica per l'insieme di credenziali. L'insieme di credenziali deve trovarsi nella stessa area geografica delle macchine virtuali che si vuole proteggere. Se si dispone di macchine virtuali situate in aree geografiche differenti, creare un insieme di credenziali in ciascuna di esse. Per archiviare i dati di backup, non è necessario specificare account di archiviazione perché l'insieme di credenziali per il backup e il servizio Backup di Azure gestiscono questa operazione in modo automatico.

    ![Creare un insieme di credenziali per il backup](./media/backup-azure-vms-prepare/backup_vaultcreate.png)

5. Fare clic su **Crea insieme di credenziali**. La creazione dell'insieme di credenziali per il backup può richiedere alcuni minuti. Monitorare le notifiche di stato nella parte inferiore del portale.

    ![Creare una notifica di tipo avviso popup dell'insieme di credenziali](./media/backup-azure-vms-prepare/creating-vault.png)

6. La creazione corretta dell'insieme di credenziali viene confermata da un messaggio. L'insieme verrà elencato come **Attivo** nella pagina **Servizi di ripristino**. Subito dopo la creazione dell'insieme di credenziali, assicurarsi di scegliere l'opzione di ridondanza dell'archiviazione corretta. Leggere l'articolo relativo all'[impostazione dell'opzione di ridondanza nell'insieme di credenziali per il backup](backup-configure-vault.md#azure-backup---storage-redundancy-options).

    ![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-vms-prepare/backup_vaultslist.png)

7. Fare clic sull'insieme di credenziali per il backup per visualizzare la pagina **Avvio rapido** in cui sono riportate le istruzioni per il backup delle macchine virtuali di Azure.

    ![Istruzioni per il backup delle macchine virtuali nella pagina Dashboard](./media/backup-azure-vms-prepare/vmbackup-instructions.png)



## 2\. Connettività di rete

![Connettività di rete](./media/backup-azure-vms-prepare/step2.png)

Per funzionare correttamente, l'estensione di backup richiede la connettività agli indirizzi IP pubblici di Azure perché invia i comandi a un endpoint di Archiviazione di Azure (URL HTTP) per gestire gli snapshot della VM. Senza la connettività Internet appropriata, si verificherà il timeout di queste richieste HTTP dalla VM e l'operazione di backup non riuscirà.

### Restrizioni di rete con i gruppi di sicurezza di rete

Se alla distribuzione sono applicate restrizioni di accesso (ad esempio, con un gruppo di sicurezza di rete), è necessario eseguire altri passaggi per assicurarsi che il traffico di backup verso l'insieme di credenziali di backup rimanga inalterato.

Per fornire un percorso per il traffico di backup, è possibile procedere in due modi:

1. Aggiungere all'elenco elementi consentiti gli [intervalli IP dei data center di Azure](http://www.microsoft.com/it-IT/download/details.aspx?id=41653).
2. Distribuire un proxy HTTP per instradare il traffico.

Il compromesso è tra gestibilità, controllo granulare e costo.

|Opzione|Vantaggi|Svantaggi:|
|------|----------|-------------|
|OPZIONE 1: aggiungere all'elenco elementi consentiti gli intervalli IP| Nessun costo aggiuntivo. <br><br>Per aprire l'accesso in un gruppo di sicurezza di rete, usare il cmdlet <i>Set-AzureNetworkSecurityRule</i>. | Complessità di gestione perché gli intervalli IP interessati variano nel tempo.<br>Fornisce l'accesso a tutto l'ambiente Azure, non solo al servizio di Archiviazione.|
|OPZIONE 2: proxy HTTP| È consentito il controllo granulare nel proxy sugli URL di archiviazione.<br>Singolo punto di accesso a Internet alle VM.<br>Non è soggetto alle modifiche degli indirizzi IP di Azure.| Costi aggiuntivi per l'esecuzione di una VM con il software proxy.|

### Uso di un proxy HTTP per i backup delle VM
Quando si esegue il backup di una VM, i comandi di gestione snapshot vengono inviati dall'estensione di backup ad Archiviazione di Azure con un'API HTTPS. Questo traffico deve essere instradato dall'estensione attraverso il proxy, perché solo il proxy verrà configurato per poter avere accesso a Internet pubblico.

>[AZURE.NOTE]Non esiste alcuna raccomandazione per il proxy da usare. Assicurarsi di scegliere un proxy compatibile con i passaggi di configurazione descritti di seguito.

Nell'esempio seguente la VM app deve essere configurata per usare la VM proxy per tutto il traffico HTTP associato a Internet pubblico. La VM proxy deve essere configurata per consentire il traffico in ingresso dalle VM nella rete virtuale. Per il gruppo di sicurezza di rete (denominato *NSG-lockdown*), infine, è necessaria una nuova regola di sicurezza che consente il traffico Internet in uscita dalla VM proxy.

![Diagramma della distribuzione gruppo di sicurezza di rete con proxy HTTP](./media/backup-azure-vms-prepare/nsg-with-http-proxy.png)

**A) Consentire le connessioni di rete in uscita:**

1. Per i computer Windows, eseguire il comando seguente in un prompt dei comandi con privilegi elevati:

	```
	netsh winhttp set proxy http://<proxy IP>:<proxy port>
	```

	Questo comando esegue una configurazione proxy a livello di computer e verrà usato per tutto il traffico HTTP/HTTPS in uscita.

2. Per i computer Linux, aggiungere la riga seguente al file ```/etc/environment```:

 	```
 	http_proxy=http://<proxy IP>:<proxy port>
 	```

	Aggiungere le righe seguenti al file ```/etc/waagent.conf```:

	```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

**B) Consentire le connessioni sul server proxy:**

1. Aprire Windows Firewall nel server proxy. Fare clic con il pulsante destro del mouse su **Regole in ingresso** e quindi su **Nuova regola**.

	![Aprire il firewall](./media/backup-azure-vms-prepare/firewall-01.png)

	![Creare una nuova regola](./media/backup-azure-vms-prepare/firewall-02.png)
2. In **Creazione guidata nuova regola connessioni in entrata** scegliere l'opzione **Personalizzata** per **Tipo di regola** e fare clic su **Avanti**. Nella schermata di selezione del **Programma** scegliere **Tutti i programmi** e fare clic su **Avanti**.

3. Nella schermata **Protocollo e porte** usare gli input della tabella seguente e fare clic su **Avanti**:

	![Creare una nuova regola](./media/backup-azure-vms-prepare/firewall-03.png)

| Campo di input | Valore |
| --- | --- |
| Tipo di protocollo | TCP |
| Porta locale | Scegliere **Porte specifiche** dall'elenco a discesa. Nella casella di testo immettere la ```<Proxy Port>``` configurata. |
| Porta remota | Scegliere **Tutte le porte** dall'elenco a discesa. |

Completare la procedura guidata facendo clic su Avanti fino al termine e assegnare un nome a questa regola.

**C) Aggiungere una regola di eccezione al gruppo di sicurezza di rete:**

In un prompt dei comandi Azure PowerShell digitare il comando seguente:

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

Questo comando aggiunge un'eccezione al gruppo di sicurezza di rete, che consente il traffico TCP da qualsiasi porta da 10.0.0.5 a qualsiasi indirizzo Internet sulla porta 80 (HTTP) o 443 (HTTPS). Se è necessario selezionare una porta specifica in Internet pubblico, assicurarsi di aggiungerla anche a ```-DestinationPortRange```.

*Assicurarsi di sostituire i nomi nell'esempio con quelli appropriati alla distribuzione in uso.*

## 3\. Agente di macchine virtuali

![Agente di macchine virtuali](./media/backup-azure-vms-prepare/step3.png)

Prima di eseguire il backup della macchina virtuale di Azure, è consigliabile assicurarsi che l'agente di macchine virtuali di Azure (agente VM) sia installato correttamente nella macchina virtuale. Poiché al momento della creazione della macchina virtuale l'agente VM è un componente opzionale, assicurarsi che la relativa casella di controllo sia selezionata prima di eseguire il provisioning della macchina virtuale.

### Installazione e aggiornamento manuali

L'agente VM è già presente nelle VM create dalla raccolta di Azure. Nelle macchine virtuali di cui viene eseguita la migrazione da data center locali non è installato l'agente VM. Per queste VM è necessario installare esplicitamente l'agente VM. Altre informazioni sull'[installazione dell'agente di VM in una VM esistente](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

| **Operazione** | **Windows** | **Linux** |
| --- | --- | --- |
| Installazione dell'agente di VM | <li>Scaricare e installare il file [MSI per l'agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Per completare l'installazione sono necessari privilegi di amministratore. <li>[Aggiornare le proprietà della VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) per indicare che l'agente è stato installato. | <li> Installare l'[agente Linux](https://github.com/Azure/WALinuxAgent) più recente da GitHub. Per completare l'installazione sono necessari privilegi di amministratore. <li> [Aggiornare le proprietà della VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) per indicare che l'agente è stato installato. |
| Aggiornamento dell'agente di VM | L'aggiornamento dell'agente VM è semplice quanto la reinstallazione dei [file binari dell'agente VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br><br>Assicurarsi che non siano in esecuzione operazioni di backup durante l'aggiornamento dell'agente VM. | Seguire le istruzioni sull'[aggiornamento dell'agente VM Linux](../virtual-machines-linux-update-agent.md). <br><br>Assicurarsi che non siano in esecuzione operazioni di backup durante l'aggiornamento dell'agente VM. |
| Convalida dell'installazione dell'agente VM | <li>Passare alla cartella *C:\\WindowsAzure\\Packages* nella VM di Azure. <li>La cartella dovrebbe includere il file WaAppAgent.exe.<li> Fare clic con il pulsante destro del mouse sul file, scegliere **Proprietà** e quindi selezionare la scheda **Dettagli**. Il campo Versione prodotto deve essere 2.6.1198.718 o superiore. | - |


Per altre informazioni, leggere gli articoli relativi all'[agente VM](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) e all'[installazione dell'agente VM](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

### Estensione di backup

Per eseguire il backup della macchina virtuale, il servizio Backup di Azure installa un'estensione nell'agente di macchine virtuali. Il servizio Backup di Azure applica aggiornamenti e patch all'estensione di backup senza ulteriore intervento dell'utente.

L'estensione per il backup viene installata se la macchina virtuale è in esecuzione. Una macchina virtuale in esecuzione consente anche di ottenere un punto di ripristino coerente con l'applicazione. Il servizio Backup di Azure continuerà tuttavia a eseguire il backup della macchina virtuale, anche se questa è spenta e non è stato possibile installare l'estensione (macchina virtuale offline). In questo caso, il punto di ripristino sarà *coerente con l'arresto anomalo*, come indicato in precedenza.


## Limitazioni

- Il backup di macchine virtuali basate su Gestione risorse di Azure (IaaS V2) non è supportato.
- Il backup di macchine virtuali con più di 16 dischi dati non è supportato.
- Il backup di macchine virtuali con Archiviazione Premium non è supportato.
- Il backup di macchine virtuali con un indirizzo IP riservato e nessun endpoint definito non è supportato.
- La sostituzione di una macchina virtuale esistente durante il ripristino non è supportata. È necessario eliminare prima la macchina virtuale esistente e gli eventuali dischi associati e quindi ripristinare i dati dal backup.
- L'operazione di backup e ripristino tra aree geografiche diverse non è supportata.
- Il backup di macchine virtuali tramite il servizio Backup di Azure è supportato in tutte le aree pubbliche di Azure (vedere l'[elenco di controllo](http://azure.microsoft.com/regions/#services) delle aree supportate). Se l'area che si sta cercando non è attualmente supportata, tale area non verrà visualizzata nell'elenco a discesa durante la creazione dell'insieme di credenziali.
- Il backup di macchine virtuali con il servizio Backup di Azure è supportato soltanto per versioni specifiche dei sistemi operativi:
  - **Linux**: vedere [l'elenco delle distribuzioni approvate da Azure](../virtual-machines/virtual-machines-linux-endorsed-distributions.md). È possibile usare altre distribuzioni personali di Linux a condizione che l'agente VM sia disponibile nella macchina virtuale.
  - **Windows Server**: le versioni precedenti a Windows Server 2008 R2 non sono supportate.
- Il ripristino di un controller di dominio di VM che fa parte di una configurazione con controller di dominio è supportato solo tramite PowerShell. Altre informazioni sul [ripristino di un controller di dominio con più controller di dominio](backup-azure-restore-vms.md#restoring-domain-controller-vms).
- Il ripristino delle macchine virtuali che presentano le seguenti configurazioni di rete speciali è supportato solo tramite PowerShell. Le macchine virtuali create usando il flusso di lavoro di ripristino nell'interfaccia utente non potranno avere queste configurazioni di rete al termine dell'operazione di ripristino. Per altre informazioni, vedere [Ripristino delle macchine virtuali con configurazioni di rete speciali](backup-azure-restore-vms.md#restoring-vms-with-special-netwrok-configurations).
	- Macchine virtuali con configurazione del servizio di bilanciamento del carico (interno ed esterno)
	- Macchine virtuali con più indirizzi IP riservati
	- Macchine virtuali con più schede di rete

## Domande?
In caso di domande o se si vuole che venga inclusa una funzionalità, è possibile [inviare commenti e suggerimenti](http://aka.ms/azurebackup_feedback).

## Passaggi successivi

- [Pianificare l'infrastruttura di backup delle VM](backup-azure-vms-introduction.md)
- [Eseguire il backup di macchine virtuali](backup-azure-vms.md)
- [Gestire backup di macchine virtuali](backup-azure-manage-vms.md)

<!---HONumber=AcomDC_0121_2016-->