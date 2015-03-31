<properties 
	pageTitle="Come configurare un set di disponibilità per le macchine virtuali" 
	description="Vengono descritti i passaggi per configurare un gruppo di disponibilità per una macchina virtuale nuova o esistente in Azure mediante il portale di gestione di Azure e i comandi di Azure PowerShell" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/24/2015" 
	ms.author="kathydav"/>

# Come configurare un set di disponibilità per le macchine virtuali#


Un set di disponibilità aiuta a mantenere disponibili le macchine virtuali durante il tempo di inattività, ad esempio durante la manutenzione. L'inserimento di due o più macchine virtuali con configurazione simile in un set di disponibilità crea la ridondanza necessaria a mantenere la disponibilità delle applicazioni o dei servizi eseguiti sulla macchina virtuale. Per informazioni dettagliate sul funzionamento, vedere [Gestione della disponibilità delle macchine virtuali] []. 

Per assicurare la continua disponibilità e l'esecuzione efficiente dell'applicazione, è buona norma usare sia set di disponibilità sia endpoint con carico bilanciato. Per informazioni sugli endpoint con carico bilanciato, vedere [Bilanciamento del carico per i servizi di infrastruttura di Azure] [].

È possibile inserire le macchine virtuali in un set di disponibilità usando una di due opzioni:

- [Opzione 1: Creare una macchina virtuale e un set di disponibilità contemporaneamente] []. Quindi, aggiungere le nuove macchine virtuali al set.
- [Opzione 2: Aggiungere una macchina virtuale esistente a un set di disponibilità] [].


>[AZURE.NOTE] Le macchine virtuali che si vogliono inserire nello stesso set di disponibilità devono appartenere allo stesso servizio cloud.   

## <a id="createset"> </a>Opzione 1: Creare una macchina virtuale e un set di disponibilità contemporaneamente##

A tale scopo, è possibile usare il portale di gestione o i comandi di Azure PowerShell. 

Per usare il portale di gestione:

1. Se necessario, accedere al [portale di gestione di Azure](http://manage.windowsazure.com).

2. Sulla barra dei comandi fare clic su **Nuovo**.

3. Fare clic su **Macchina virtuale**, quindi su **Da raccolta**.

4. Usare le prime due schermate per scegliere un'immagine, un nome utente e una password, e così via. Per altre informazioni, vedere [Creazione di una macchina virtuale che esegue Windows][].
 
5. Nella terza schermata è possibile configurare le risorse per le connessioni di rete, l'archiviazione e la disponibilità. Seguire questa procedura:
	 
	1. Scegliere il servizio cloud appropriato. Non modificare l'impostazione **Crea un nuovo servizio cloud** (a meno che non si stia aggiungendo una nuova macchina virtuale a un servizio cloud VM esistente). Quindi, sotto **Nome DNS del servizio cloud**, digitare un nome. Il nome DNS diventa parte dell'URI usato per contattare la macchina virtuale. Il servizio cloud agisce da gruppo di comunicazione e isolamento. Tutte le macchine virtuali nello stesso servizio cloud possono comunicare tra loro, essere impostate per il bilanciamento del carico e inserite nello stesso set di disponibilità. 

	2. In **Regione/Gruppo di affinità/Rete virtuale** specificare una rete virtuale se si pianifica di usarne una. **Importante**: se si vuole che una macchina virtuale usi una rete virtuale, è necessario aggiungere la VM alla rete virtuale quando si crea la macchina virtuale. Non è possibile aggiungere la macchina virtuale a una rete virtuale dopo averla creata. Per altre informazioni, vedere [Panoramica di Rete virtuale][]. 
	
	3. Creare il set di disponibilità. In **Set di disponibilità** non modificare l'impostazione **Crea set di disponibilità**. Quindi, digitare un nome per il set. 

	4. Creare gli endpoint predefiniti e, se necessario, aggiungere altri endpoint. È inoltre possibile aggiungere gli endpoint in un secondo momento. 

	![Creare un set di disponibilità per una nuova macchina virtuale](./media/virtual-machines-how-to-configure-availability/VMavailabilityset.png) 

6. Nella quarta schermata, scegliere le estensioni da installare. Le estensioni forniscono funzionalità che facilitano la gestione della macchina virtuale, ad esempio l'esecuzione di un programma antimalware o la reimpostazione delle password. Per informazioni dettagliate, vedere [Informazioni generali sull'agente VM e sulle estensioni VM](http://go.microsoft.com/fwlink/p/?LinkId=XXX).

7.	Fare clic sulla freccia per creare la macchina virtuale e il set di disponibilità.

	Nel dashboard della nuova macchina virtuale è possibile fare clic su **Configura** per verificare che la macchina virtuale appartenga al nuovo set di disponibilità.

Se si desidera usare i comandi di Azure PowerShell per creare una macchina virtuale di Azure e aggiungerla a un set di disponibilità nuovo o esistente, vedere il seguente argomento:

- [Uso di Azure PowerShell per creare e preconfigurare macchine virtuali basate su Windows](../virtual-machines-ps-create-preconfigure-windows-vms/)
- [Uso di Azure PowerShell per creare e preconfigurare macchine virtuali basate su Linux](../virtual-machines-ps-create-preconfigure-linux-vms/)

	>[AZURE.NOTE] Eseguire `Get-AzureVMImage` senza parametri per ottenere un elenco di tutte le immagini che si applicano alla propria sottoscrizione. Questa operazione potrebbe restituire un elenco di grandi dimensioni. Per abbreviare l'elenco, usare proprietà quali il nome della famiglia di immagini. Per suggerimenti ed esempi su come effettuare questa operazione per trovare un'immagine specifica, vedere [Gestire le immagini mediante Windows PowerShell](http://msdn.microsoft.com/library/azure/dn790330.aspx).

3.	Specificare la configurazione per la nuova macchina virtuale e quindi usare la pipeline per passare un oggetto di configurazione al cmdlet che crea la VM. Assicurarsi di sostituire i segnaposto, ad esempio  &lt;VmName&gt; e &lt;VmSize&gt;, con i valori personalizzati.

	`C:\PS> New-AzureVMConfig -Name "<VmName>" -InstanceSize <VmSize> -AvailabilitySetName "<SetName>" -ImageName $image | Add-AzureProvisioningConfig -Windows -AdminUsername "<UserName>" -Password "<MyPassword>" | Add-AzureDataDisk -CreateNew -DiskSizeInGB 50 -DiskLabel 'datadisk1' -LUN 0 | New-AzureVM -ServiceName "<MySvc1>" `

## <a id="addmachine"> </a>Opzione 2: Aggiungere una macchina virtuale esistente a un set di disponibilità##

Nel portale di gestione è possibile aggiungere macchine virtuali esistenti a un set di disponibilità esistente oppure crearne una nuova (si noti che le macchine virtuali presenti nello stesso set di disponibilità devono appartenere allo stesso servizio cloud). La procedura è quasi la stessa: con Azure PowerShell è possibile aggiungere la macchina virtuale a un set di disponibilità esistente. 

1. Se questa operazione non è già stata eseguita, accedere al [portale di gestione di Azure](http://manage.windowsazure.com).

2. Nella barra di spostamento, fare clic su **Macchine virtuali**.

3. Nell'elenco delle macchine virtuali fare clic sul nome di quelle che si desidera aggiungere al set.

4. Nelle schede sotto il nome della macchina virtuale fare clic su **Configura**. 

5. Nella sezione Impostazioni cercare **Set di disponibilità**. Eseguire una di queste operazioni:

	A. Scegliere **Crea set di disponibilità**, quindi digitare un nome per il set.

	B. Scegliere **Seleziona set di disponibilità**, quindi scegliere un set dall'elenco.

	![Creazione di un set di disponibilità per una VM esistente](./media/virtual-machines-how-to-configure-availability/VMavailabilityExistingVM.png) 

6. Fare clic su **Salva**.

Per usare i comandi di Azure PowerShell, aprire una sessione di Azure PowerShell con privilegi di amministratore ed attivare il seguente comando. Per i segnaposto, ad esempio &lt;VmCloudServiceName&gt;, sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < e >, con i nomi corretti.

	Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

>[AZURE.NOTE] È possibile riavviare la macchina virtuale per completarne l'aggiunta al set di disponibilità.


## Risorse aggiuntive
[Informazioni sulle impostazioni di configurazione delle macchine virtuali di Azure]

[Opzione 1: Creare una macchina virtuale e un set di disponibilità contemporaneamente]: #createset
[Opzione 2: Aggiungere una macchina virtuale esistente a un set di disponibilità]: #addmachine

<!-- LINKS -->
[Bilanciamento del carico per i servizi di infrastruttura di Azure]: ../virtual-machines-load-balance
[Gestione della disponibilità delle macchine virtuali]: ../virtual-machines-manage-availability
[Creazione di una macchina virtuale che esegue Windows]: ../virtual-machines-windows-tutorial
[Panoramica di Rete virtuale]: http://go.microsoft.com/fwlink/p/?linkid=294063
[Informazioni sui gruppi di affinità per Rete virtuale]: http://msdn.microsoft.com/library/windowsazure/jj156085.aspx
<<<<<<< HEAD
[Come connettere macchine virtuali in un servizio cloud]: ../virtual-machines-connect-cloud-service
[Informazioni sulle impostazioni di configurazione delle macchine virtuali di Azure]: http://msdn.microsoft.com/library/azure/dn763935.aspx
=======
[Come connettere macchine virtuali in un servizio cloud]: ../cloud-services-connect-virtual-machine
[Informazioni sulle impostazioni di configurazione delle macchine virtuali di Azure]: http://msdn.microsoft.com/library/azure/dn763935.aspx
>>>>>>> 1dc830a16302e6b943a5d55ebacef659451b22d6


<!--HONumber=47-->
