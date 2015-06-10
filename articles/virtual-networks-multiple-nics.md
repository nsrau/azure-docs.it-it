<properties 
   pageTitle="Creare una macchina virtuale con più NIC"
   description="Come creare macchine virtuali con più NIC"
   services="virtual-network, virtual-machines"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/30/2015"
   ms.author="telmos" />

# Creare una macchina virtuale con più NIC

La funzionalità Multi-NIC consente di creare e gestire più schede di interfaccia di rete virtuale (NIC) nelle macchine virtuali di Azure (VM). Multi-NIC è un requisito per molti dispositivi virtuali di rete, ad esempio distribuzione di applicazioni e soluzioni di ottimizzazione WAN. La funzionalità Multi-NIC fornisce inoltre una migliore funzionalità di gestione del traffico di rete, che include l'isolamento del traffico tra una NIC di front-end e le NIC di back-end o la separazione del traffico del piano dati dal traffico del piano di gestione.

![Più NIC per ciascuna macchina virtuale](./media/virtual-networks-multiple-nics/IC757773.png)

Nella figura precedente viene illustrata una macchina virtuale con tre NIC, ciascuna connessa a una subnet diversa.

## Requisiti e vincoli

Attualmente, la funzionalità Multi-NIC presenta i requisiti e i vincoli seguenti:

- È necessario creare macchine virtuali con funzionalità Multi-NIC in reti virtuali di Azure (Vnet). Le macchine virtuali che non si trovano in reti virtuali non sono supportate. 
- All'interno di un singolo servizio cloud, sono consentite solo le impostazioni seguenti: 
	- In tutte le macchine virtuali del servizio cloud deve essere abilitata la funzionalità Multi-NIC oppure 
	- Tutte le macchine virtuali nel servizio cloud devono disporre di una singola NIC 

>[AZURE.IMPORTANT]Se si tenta di aggiungere una macchina virtuale con più NIC a una distribuzione (servizio cloud) che contiene già una macchina virtuale con una singola NIC, si riceverà il seguente errore: le macchine virtuali con interfacce di rete secondarie e le macchine virtuali senza interfacce di rete secondarie non sono supportate nella stessa distribuzione, inoltre una macchina virtuale senza interfacce di rete secondarie non può essere aggiornata in modo da presentare interfacce di rete secondarie e viceversa.
 
- L’indirizzo VIP con connessione Internet è supportato solo sulla NIC "predefinita". Esiste un solo indirizzo VIP per l'indirizzo IP della NIC predefinita. 
- Attualmente, gli indirizzi IP pubblici a livello di istanza non sono supportati per le macchine virtuali a più NIC. 
- L'ordine delle NIC all'interno della macchina virtuale sarà casuale e potrebbe cambiare con gli aggiornamenti dell'infrastruttura di Azure. Tuttavia, gli indirizzi IP e gli indirizzi MAC ethernet corrispondenti resteranno invariati. Si supponga, ad esempio, che **Eth1** abbia l’indirizzo IP 10.1.0.100 e l'indirizzo MAC 00-0D-3A-B0-39-0D; dopo un aggiornamento dell'infrastruttura di Azure e il riavvio, potrebbe essere modificato in Eth2, ma l’abbinamento di indirizzo IP e MAC resteranno invariati. Quando un riavvio è eseguito dal cliente, l'ordine delle NIC rimane invariato. 
- L'indirizzo di ciascuna NIC su ciascuna macchina virtuale deve trovarsi in una subnet, a più NIC in una singola macchina virtuale possono essere assegnati indirizzi che si trovano nella stessa subnet. 
- Le dimensioni della macchina virtuale determinano il numero di NIC che è possibile creare per una macchina virtuale. Nella tabella seguente sono elencati i numeri di NIC corrispondenti alle dimensioni delle macchine virtuali: 

|Dimensioni macchina virtuale (SKU standard)|NIC (numero massimo consentito per ogni macchina virtuale)|
|---|---|
|Tutte le dimensioni di base|1|
|A0\molto piccola|1|
|A1\piccola|1|
|A2\media|1|
|A3\grande|2|
|A4\molto grande|4|
|A5|1|
|A6|2|
|A7|4|
|A8|2|
|A9|4|
|A10|2|
|A11|4|
|D1|1|
|D2|2|
|D3|4|
|D4|8|
|D11|2|
|D12|4|
|D13|8|
|D14|16|
|DS1|1|
|DS2|2|
|DS3|4|
|DS4|8|
|DS11|2|
|DS12|4|
|DS13|8|
|DS14|16|
|G1|1|
|G2|2|
|G3|4|
|G4|8|
|G5|16|
|Tutte le altre dimensioni|1|

## Gruppi di sicurezza di rete
Qualsiasi NIC in una macchina virtuale può essere associata a un Gruppo di sicurezza di rete, incluse eventuali NIC in una macchina virtuale che presenta la funzionalità Multi-NIC abilitata. Se a una NIC viene assegnato un indirizzo all'interno di una subnet dove la subnet è associata a un Gruppo di sicurezza di rete, le regole del Gruppo di sicurezza di rete della subnet si applicano anche a tale NIC. Oltre alle subnet, è possibile associare ai Gruppi di sicurezza di rete anche una NIC.

Se una subnet è associata a un Gruppo di sicurezza di rete e una NIC all'interno di tale subnet è associata singolarmente a un Gruppo di sicurezza di rete, le regole del Gruppo di sicurezza di rete associato vengono applicate in "**ordine flusso**",  in base alla direzione del traffico in entrata e in uscita dalla NIC:

- **Il traffico in entrata**, la cui destinazione è la NIC in questione, passa innanzitutto attraverso la subnet, attivando le regole del Gruppo di sicurezza di rete della subnet, prima di passare nella NIC, attivando quindi le regole del Gruppo di sicurezza di rete della NIC. - **Il traffico in uscita**, la cui origine è la NIC in questione, fuoriesce innanzitutto dalla subnet, attivando le regole del Gruppo di sicurezza di rete della NIC, prima di passare attraverso la sunet, attivando quindi le regole del Gruppo di sicurezza di rete della subnet. 

Nella figura precedente viene illustrato come viene eseguita l’applicazione delle regole del Gruppo di sicurezza di rete in base al flusso di traffico (dalla macchina virtuale alla subnet o dalla subnet alla macchina virtuale).

## Come configurare una macchina virtuale Multi-NIC

Le istruzioni seguenti consentono di creare una macchina virtuale Multi-NIC contenente 3 NIC: una NIC predefinita e due NIC aggiuntive. La procedura di configurazione consente di creare una macchina virtuale che verrà configurata in base al frammento del file di configurazione del servizio riportato di seguito:

	<VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
	<AddressSpace>
	  <AddressPrefix>10.1.0.0/16</AddressPrefix>
	    </AddressSpace>
	    <Subnets>
	      <Subnet name="Frontend">
	        <AddressPrefix>10.1.0.0/24</AddressPrefix>
	      </Subnet>
	      <Subnet name="Midtier">
	        <AddressPrefix>10.1.1.0/24</AddressPrefix>
	      </Subnet>
	      <Subnet name="Backend">
	        <AddressPrefix>10.1.2.0/23</AddressPrefix>
	      </Subnet>
	      <Subnet name="GatewaySubnet">
	        <AddressPrefix>10.1.200.0/28</AddressPrefix>
	      </Subnet>
	    </Subnets>
	… Skip over the remainder section …
	</VirtualNetworkSite>


Per tentare di eseguire i comandi PowerShell riportati nell’esempio sono necessari i seguenti prerequisiti.

- Una sottoscrizione di Azure.
- Una rete virtuale configurata. Per altre informazioni sulle reti virtuali, vedere [Panoramica di Rete virtuale](https://msdn.microsoft.com/library/azure/jj156007.aspx).
- La versione più recente di Azure PowerShell scaricata e installata. Vedere [Come installare e configurare Azure PowerShell](../install-configure-powershell).

1. Selezionare un'immagine di macchina virtuale dalla raccolta immagini della macchina virtuale di Azure. Le immagini cambiano frequentemente e sono disponibili per area geografica. L'immagine specificata nell'esempio riportato di seguito può cambiare o potrebbe non trovarsi nell’area desiderata, assicurarsi pertanto di specificare l'immagine necessaria. 

	    $image = Get-AzureVMImage `
	    	-ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"

1. Creare una configurazione di macchina virtuale.

		$vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
			-Image $image.ImageName –AvailabilitySetName "MyAVSet"

1. Creare l’account di accesso dell’amministratore predefinito.

		Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
			-Password "<YourAdminPassword>"

1. Aggiungere le schede NIC aggiuntive alla configurazione della macchina virtuale.

		Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
			-SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm 
		Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
			-SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm

1. Specificare la subnet e l’indirizzo IP per la NIC predefinita.

		Set-AzureSubnet -SubnetNames "Frontend" -VM $vm Set-AzureStaticVNetIP  `
			-IPAddress "10.1.0.100" -VM $vm

1. Creare la macchina virtuale nella rete virtuale.

		New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm

>[AZURE.NOTE]La rete virtuale specificata deve essere già esistente (come indicato nei prerequisiti). Nell'esempio seguente viene specificata una rete virtuale denominata "MultiNIC-VNet".

## Vedere anche

[Panoramica di Rete virtuale](https://msdn.microsoft.com/library/azure/jj156007.aspx).

[Attività di configurazione della rete virtuale](https://msdn.microsoft.com/library/azure/jj156206.aspx)

[Post di blog: macchine virtuali con più NIC e dispositivi di rete virtuale in Azure](../multiple-vm-nics-and-network-virtual-appliances-in-azure)

<!---HONumber=58-->