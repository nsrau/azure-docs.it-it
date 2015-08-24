<properties
	pageTitle="Come configurare un set di disponibilità per le macchine virtuali"
	description="Vengono descritti i passaggi per configurare un set di disponibilità per una macchina virtuale nuova o esistente in Azure usando il portale di Azure e i comandi di Azure PowerShell"
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/10/2015"
	ms.author="kathydav"/>

#Come configurare un set di disponibilità per le macchine virtuali#

Un set di disponibilità aiuta a mantenere disponibili le macchine virtuali durante il tempo di inattività, ad esempio durante la manutenzione. L'inserimento di due o più macchine virtuali con configurazione simile in un set di disponibilità crea la ridondanza necessaria a mantenere la disponibilità delle applicazioni o dei servizi eseguiti sulla macchina virtuale. Per i dettagli sul funzionamento, vedere [Gestione della disponibilità delle macchine virtuali][].

Per assicurare la continua disponibilità e l'esecuzione efficiente dell'applicazione, è buona norma usare sia set di disponibilità sia endpoint con carico bilanciato. Per informazioni sugli endpoint con bilanciamento del carico, vedere [Bilanciamento del carico per i servizi di infrastruttura di Azure][].

È possibile inserire le macchine virtuali in un set di disponibilità usando una di queste due opzioni:

- [Opzione 1: Creare una macchina virtuale e un set di disponibilità contemporaneamente][]. Quindi, aggiungere le nuove macchine virtuali al set.
- [Opzione 2: Aggiungere una macchina virtuale esistente a un set di disponibilità][].

>[AZURE.NOTE]Le macchine virtuali che si vogliono inserire nello stesso set di disponibilità devono appartenere allo stesso servizio cloud.

## <a id="createset"> </a>Opzione 1: Creare una macchina virtuale e un set di disponibilità contemporaneamente.##

A tale scopo, è possibile usare il portale di Azure o i comandi di Azure PowerShell.

Per usare il portale:

1. Accedere al [portale](http://manage.windowsazure.com), se questa operazione non è già stata eseguita.

2. Sulla barra dei comandi fare clic su **Nuovo**.

3. Fare clic su **Virtual Machine**, quindi su **From Gallery**.

4. Usare le prime due schermate per selezionare un'immagine, un nome utente e una password e così via. Per informazioni dettagliate, vedere [Creazione di una macchina virtuale che esegue Windows][].

5. Nella terza schermata è possibile configurare le risorse per le connessioni di rete, l'archiviazione e la disponibilità. Eseguire le operazioni seguenti:

	1. Scegliere il nome del servizio cloud appropriato. Non modificare l'impostazione **Crea un nuovo servizio cloud** (a meno che non si stia aggiungendo una nuova macchina virtuale a un servizio cloud per la macchina virtuale esistente). Quindi, sotto **Nome DNS del servizio cloud**, digitare un nome. Il nome DNS diventa parte dell'URI usato per contattare la macchina virtuale. Il servizio cloud agisce da gruppo di comunicazione e isolamento. Tutte le macchine virtuali nello stesso servizio cloud possono comunicare tra loro, essere impostate per il bilanciamento del carico e inserite nello stesso set di disponibilità.

	2. In **Regione/Gruppo di affinità/Rete virtuale** specificare una rete virtuale se si pianifica di usarne una. **Importante**: se si vuole che una macchina virtuale usi una rete virtuale, è necessario aggiungerla alla rete virtuale quando si crea la macchina virtuale. Non è possibile aggiungere la macchina virtuale a una rete virtuale dopo averla creata. Per altre informazioni, vedere [Panoramica di Rete virtuale][].

	3. Creare il set di disponibilità. In **Set di disponibilità**, non modificare l'impostazione **Crea set di disponibilità**. Quindi, digitare un nome per il set.

	4. Creare gli endpoint predefiniti e, se necessario, aggiungere altri endpoint. È inoltre possibile aggiungere gli endpoint in un secondo momento.

	![Creare un set di disponibilità per una nuova macchina virtuale](./media/virtual-machines-how-to-configure-availability/VMavailabilityset.png)

6. Nella quarta schermata scegliere le estensioni da installare. Le estensioni forniscono funzionalità che facilitano la gestione della macchina virtuale, ad esempio l'esecuzione di un programma antimalware o la reimpostazione delle password. Per informazioni dettagliate, vedere [Agente VM ed estensioni VM di Azure](virtual-machines-extensions-agent-about.md).

7.	Fare clic sulla freccia per creare la macchina virtuale e il set di disponibilità.

	Nel dashboard della nuova macchina virtuale è possibile fare clic su **Configure** per verificare che la macchina virtuale appartenga al nuovo set di disponibilità.

Se si vogliono usare i comandi di Azure PowerShell per creare una macchina virtuale di Azure e aggiungerla a un set di disponibilità nuovo o esistente, vedere il seguente argomento:

- [Uso di Azure PowerShell per creare e preconfigurare macchine virtuali basate su Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)
- [Usare Azure PowerShell per creare e preconfigurare macchine virtuali basate su Linux](virtual-machines-ps-create-preconfigure-linux-vms.md)

## <a id="addmachine"></a>Opzione 2: Aggiungere una macchina virtuale esistente a un set di disponibilità.##

Nel portale è possibile aggiungere macchine virtuali esistenti a un set di disponibilità esistente oppure crearne una nuova. (si noti che le macchine virtuali presenti nello stesso set di disponibilità devono appartenere allo stesso servizio cloud). La procedura è quasi la stessa: con Azure PowerShell è possibile aggiungere la macchina virtuale a un set di disponibilità esistente.

1. Accedere al [portale](http://manage.windowsazure.com), se questa operazione non è già stata eseguita.

2. Nella barra dei comandi fare clic su **Macchine virtuali**.

3. Nell'elenco delle macchine virtuali fare clic sul nome di quelle che si vuole aggiungere al set.

4. Nelle schede sotto il nome della macchina virtuale fare clic su **Configura**.

5. Nella sezione Impostazioni cercare **Set di disponibilità**. Eseguire una di queste operazioni:

	R. Scegliere **Crea set di disponibilità** e quindi digitare un nome per il set.

	B. Scegliere **Seleziona set di disponibilità** e quindi scegliere un set dall'elenco.

	![Creare un set di disponibilità per una macchina virtuale esistente](./media/virtual-machines-how-to-configure-availability/VMavailabilityExistingVM.png)

6. Fare clic su **Save**.

Per usare i comandi di Azure PowerShell, aprire una sessione di Azure PowerShell con privilegi di amministratore ed attivare il seguente comando. Per i segnaposto, ad esempio &lt;VmCloudServiceName&gt;, sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con i nomi corretti.

	Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

>[AZURE.NOTE]Potrebbe essere necessario riavviare la macchina virtuale per completarne l'aggiunta al set di disponibilità.

##Risorse aggiuntive

[Informazioni sulle impostazioni di configurazione delle macchine virtuali di Azure]

<!-- LINKS -->
[Opzione 1: Creare una macchina virtuale e un set di disponibilità contemporaneamente]: #createset
[Opzione 2: Aggiungere una macchina virtuale esistente a un set di disponibilità]: #addmachine

[Bilanciamento del carico per i servizi di infrastruttura di Azure]: virtual-machines-load-balance.md
[Gestione della disponibilità delle macchine virtuali]: virtual-machines-manage-availability.md
[Creazione di una macchina virtuale che esegue Windows]: virtual-machines-windows-tutorial.md
[Panoramica di Rete virtuale]: virtual-networks-overview.md
[Informazioni sulle impostazioni di configurazione delle macchine virtuali di Azure]: http://msdn.microsoft.com/library/azure/dn763935.aspx

<!---HONumber=August15_HO7-->