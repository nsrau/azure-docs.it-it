<properties title="How to Configure An Availability Set for Virtual Machines" pageTitle="Come configurare un set di disponibilità per le macchine virtuali" description="Illustra la procedura di configurazione di un set di disponibilità per una VM in Azure" metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="kathydav" manager="timlt" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="11/17/2014" ms.author="kathydav" />

#Come configurare un set di disponibilità per le macchine virtuali#

Un set di disponibilità aiuta a mantenere disponibili le macchine virtuali durante il tempo di inattività, ad esempio durante la manutenzione. L'inserimento di due o più macchine virtuali con configurazione simile in un set di disponibilità crea la ridondanza necessaria a mantenere la disponibilità delle applicazioni o dei servizi eseguiti sulla macchina virtuale. Per informazioni dettagliate sul funzionamento, vedere [Gestire la disponibilità delle macchine virtuali] []. 

Per assicurare la continua disponibilità e l'esecuzione efficiente dell'applicazione, è buona norma usare sia set di disponibilità sia endpoint con carico bilanciato. Per informazioni sugli endpoint con bilanciamento del carico, vedere [Bilanciamento del carico per i servizi di infrastruttura di Azure] [].

È possibile inserire le macchine virtuali in un set di disponibilità usando una di due opzioni:

- [Opzione 1: Creare una macchina virtuale e un set di disponibilità contemporaneamente] []. Quindi, aggiungere le nuove macchine virtuali al set.
- [Opzione 2: Aggiungere una macchina virtuale esistente a un set di disponibilità] [].


>[WACOM.NOTE] Le macchine virtuali che si vogliono inserire nello stesso set di disponibilità devono appartenere allo stesso servizio cloud.   

## <a id="createset"> </a>Opzione 1: Creare una macchina virtuale e un set di disponibilità contemporaneamente##

A tale scopo, è possibile usare il portale di gestione o i cmdlet di PowerShell. 

Per usare il portale di gestione:

1. Se necessario, accedere al [portale di gestione](http://manage.windowsazure.com) di Azure.

2. Sulla barra dei comandi fare clic su **Nuovo**.

3. Fare clic su **Macchina virtuale**, quindi su **Da raccolta**.

4. Usare le prime due schermate per scegliere un'immagine, un nome utente e una password, e così via. Per altre informazioni, vedere [Creare una macchina virtuale che esegue Windows][].
 
5. Nella terza schermata è possibile configurare le risorse per le connessioni di rete, l'archiviazione e la disponibilità. Eseguire le operazioni seguenti:
	 
	1. Scegliere il servizio cloud appropriato. Non modificare l'impostazione **Crea un nuovo servizio cloud** (a meno che non si stia aggiungendo una nuova macchina virtuale a un servizio cloud VM esistente). Quindi, sotto **Nome DNS del servizio cloud**, digitare un nome. Il nome DNS diventa parte dell'URI usato per contattare la macchina virtuale. Il servizio cloud agisce da gruppo di comunicazione e isolamento. Tutte le macchine virtuali nello stesso servizio cloud possono comunicare tra loro, essere impostate per il bilanciamento del carico e inserite nello stesso set di disponibilità. 

	2. In **Regione/Gruppo di affinità/Rete virtuale** specificare una rete virtuale se si pianifica di usarne una. **Importante**: se si vuole che una macchina virtuale usi una rete virtuale, è necessario aggiungere la VM alla rete virtuale quando si crea la macchina virtuale. Non è possibile aggiungere la macchina virtuale a una rete virtuale dopo averla creata. Per altre informazioni, vedere [Panoramica di Rete virtuale][]. 
	
	3. Creare il set di disponibilità. In **Set di disponibilità** non modificare l'impostazione **Crea set di disponibilità**. Quindi, digitare un nome per il set. 
	4. Creare gli endpoint predefiniti e, se necessario, aggiungere altri endpoint. È inoltre possibile aggiungere gli endpoint in un secondo momento. 

	![Create an availabililty set for a new VM](./media/virtual-machines-how-to-configure-availability/VMavailabilityset.png) 

6. Nella quarta schermata, scegliere le estensioni da installare. Le estensioni forniscono funzionalità che facilitano la gestione della macchina virtuale, ad esempio l'esecuzione di un programma antimalware o la reimpostazione delle password. Per informazioni dettagliate, vedere [Agente VM ed estensioni VM di Azure](http://go.microsoft.com/fwlink/p/?LinkId=XXX).

7.	Fare clic sulla freccia per creare la macchina virtuale e il set di disponibilità.

	Nel dashboard della nuova macchina virtuale è possibile fare clic su **Configura** per verificare che la macchina virtuale appartenga al nuovo set di disponibilità.

Per usare i cmdlet di Azure:

1.	Aprire una sessione di Azure PowerShell ed eseguire comandi simili agli esempi seguenti. Notare che in questi esempi si presuppone che si stia creando la macchina virtuale, il servizio cloud e il set di disponibilità.

2.	Ottenere il nome dell'immagine da usare per la creazione della macchina virtuale e archiviarlo in una variabile. Il comando di esempio seguente usa il numero dell'indice e la proprietà ImageName dell'oggetto immagine. Si presuppone che l'utente conosca il numero di indice corrente dell'immagine desiderata e che sostituirà tale numero per &lt;index_number&gt;. <br>

	`C:\PS> $image = (Get-AzureVMImage)[<index_number>].ImageName`

	>[WACOM.NOTE] Eseguire `Get-AzureVMImage` senza parametri per ottenere un elenco di tutte le immagini che si applicano alla propria sottoscrizione. Questa operazione potrebbe restituire un elenco di grandi dimensioni. Per abbreviare l'elenco, usare proprietà quali il nome della famiglia di immagini. Per suggerimenti ed esempi su come effettuare questa operazione per trovare un'immagine specifica, vedere [Gestire le immagini mediante Windows PowerShell](http://msdn.microsoft.com/it-it/library/azure/dn790330.aspx).

3.	Specificare la configurazione per la nuova macchina virtuale e quindi usare la pipeline per passare un oggetto di configurazione al cmdlet che crea la VM. Assicurarsi di sostituire i segnaposto, ad esempio &lt;VmName&gt; e &lt;VmSize&gt;, con i valori personalizzati.

	`C:\PS> New-AzureVMConfig -Name "<VmName>" -InstanceSize <VmSize> -AvailabilitySetName "<SetName>" -ImageName $image | Add-AzureProvisioningConfig -Windows -AdminUsername "<UserName>" -Password "<MyPassword>" | Add-AzureDataDisk -CreateNew -DiskSizeInGB 50 -DiskLabel 'datadisk1' -LUN 0 | New-AzureVM -ServiceName "<MySvc1>" `

## <a id="addmachine"> </a>Opzione 2: Aggiungere una macchina virtuale esistente a un set di disponibilità##

Nel portale di gestione è possibile aggiungere macchine virtuali esistenti a un set di disponibilità esistente oppure crearne una nuova (tenere presente che le macchine virtuali devono appartenere allo stesso servizio cloud). La procedura è quasi la stessa: in Azure PowerShell, è possibile aggiungere la macchina virtuale a un set di disponibilità esistente. 

1. Accedere al [portale di gestione](http://manage.windowsazure.com) di Azure se questa operazione non è già stata eseguita.

2. Nella barra di spostamento, fare clic su **Macchine virtuali**.

3. Scegliere dall'elenco una delle macchine virtuali che si vuole aggiungere al set. Per aprire il relativo dashboard, fare clic sulla riga della macchina virtuale.

4. Nelle schede sotto il nome della macchina virtuale fare clic su **Configura**. 

5. Nella sezione Impostazioni cercare **Set di disponibilità**. Eseguire una delle operazioni seguenti:

	A. Scegliere **Crea set di disponibilità**, quindi digitare un nome per il set.

	B. Scegliere **Seleziona set di disponibilità**, quindi scegliere un set dall'elenco.

	![Create an availabililty set for an existing VM](./media/virtual-machines-how-to-configure-availability/VMavailabilityExistingVM.png) 

6. Fare clic su **Salva**.

Per usare i cmdlet di Azure:

Aprire una sessione di Azure PowerShell ed eseguire il comando riportato di seguito. Assicurarsi di sostituire i segnaposto, ad esempio &lt;VmCloudServiceName&gt; e &lt;VmName&gt;, con i valori personalizzati.

	C:\PS> Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<MyAvSet>" | Update-AzureVM

>[WACOM.NOTE] È possibile riavviare la macchina virtuale per completarne l'aggiunta al set di disponibilità.


##Risorse aggiuntive
[Informazioni sulle impostazioni di configurazione delle macchine virtuali di Azure]

[Opzione 1: Creare una macchina virtuale e un set di disponibilità contemporaneamente]: #createset
[Opzione 2: Aggiungere una macchina virtuale esistente a un set di disponibilità]: #addmachine

<!-- LINKS -->
[Bilanciamento del carico per i servizi di infrastruttura di Azure]: ../virtual-machines-load-balance
[Gestire la disponibilità delle macchine virtuali]: ../virtual-machines-manage-availability
[Creare una macchina virtuale che esegue Windows]: ../virtual-machines-windows-tutorial
[Rete virtuale di Azure]: http://go.microsoft.com/fwlink/p/?linkid=294063
[Informazioni sui dispositivi VPN per Rete virtuale]: http://msdn.microsoft.com/library/windowsazure/jj156085.aspx
[Come connettere macchine virtuali in un servizio cloud]: ../virtual-machines-connect-cloud-service
[Informazioni sulle impostazioni di configurazione delle macchine virtuali di Azure]: http://msdn.microsoft.com/it-it/library/azure/dn763935.aspx


<!--HONumber=35.1-->
