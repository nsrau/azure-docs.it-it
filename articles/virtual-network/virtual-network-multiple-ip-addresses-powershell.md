<properties 
   pageTitle="Più indirizzi IP per le macchine virtuali - PowerShell | Microsoft Azure"
   description="Informazioni su come assegnare più indirizzi IP a una macchina virtuale usando Azure PowerShell."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>  
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="jdial" />  

# Assegnare più indirizzi IP alle macchine virtuali

Una macchina virtuale di Azure può essere associata a una o più interfacce di rete. Ogni interfaccia di rete può avere uno o più indirizzi IP pubblici o privati associati. Se non si ha familiarità con gli indirizzi IP in Azure, leggere l'articolo [Indirizzi IP in Azure](virtual-network-ip-addresses-overview-arm.md) per ottenere altre informazioni. Questo articolo illustra come usare Azure PowerShell per assegnare più indirizzi IP a un'interfaccia di rete nel modello di distribuzione Azure Resource Manager.

L'assegnazione di più indirizzi IP a un'interfaccia di rete consente alla macchina virtuale di:

- Ospitare più siti Web o servizi con indirizzi IP e certificati SSL diversi in un singolo server.
- Fungere da appliance virtuale di rete, ad esempio un firewall o un servizio di bilanciamento del carico.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

Per registrarsi all'anteprima, inviare un messaggio di posta elettronica a [IP multipli](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) con l'ID sottoscrizione e l'uso previsto.

## <a name = "create"></a>Creare una macchina virtuale con più indirizzi IP

1. Aprire un prompt dei comandi di PowerShell e completare i passaggi rimanenti in questa sezione in una singola sessione di PowerShell. Se PowerShell non è già installato e configurato, completare la procedura disponibile nell'articolo [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

2. Cambiare i "valori" delle variabili $Variables seguenti specificando il nome da usare per l'interfaccia di rete, il [gruppo di risorse](../resource-group-overview.md#resource-groups) a cui la si vuole assegnare e la [località](https://azure.microsoft.com/regions) di Azure in cui la si vuole creare.

		$NicName     = "VM1-NIC1"
		$NicRgName   = "RG1"
		$NicLocation = "westus"

	Se non si conosce il nome di una località o di un gruppo di risorse di Azure esistente, digitare i comandi seguenti:

		Get-AzureRmLocation 	 | Format-Table Location
		Get-AzureRmResourceGroup | Format-Table ResourceGroupName	
 
3. <a name="subnet"></a>L'interfaccia di rete deve essere connessa a una subnet in una rete virtuale di Azure (VNet) esistente nella stessa località e nella stessa [sottoscrizione](../azure-glossary-cloud-terminology.md#subscription) dell'interfaccia di rete. Se non si ha familiarità con le reti virtuali, vedere l'articolo [Panoramica della rete virtuale](virtual-networks-overview.md) per ottenere informazioni sulle reti virtuali oppure vedere l'articolo [Creare una rete virtuale](virtual-networks-create-vnet-arm-ps.md) per informazioni su come crearne una. Cambiare i "valori" delle variabili $Variables seguenti specificando il nome della VNet e della subnet a cui si vuole connettere l'interfaccia di rete e il nome del gruppo di risorse in cui si trova la VNet.

		$VNetName   = "VNet1"
		$SubnetName = "Subnet1"
		$VNetRgName = "Network"

	Se non si conosce il nome di una rete virtuale esistente, immettere il comando seguente e sostituire *VNet1* nella variabile precedente con il nome della rete virtuale:
		
		Get-AzureRmVirtualNetwork | Format-Table Name
		
	Se l'elenco restituito è vuoto, è necessario creare una rete virtuale. Per informazioni sulla procedura, vedere l'articolo [Creare una rete virtuale](virtual-networks-create-vnet-arm-ps.md).

	Digitare i comandi seguenti per ottenere il nome delle subnet nella rete virtuale e sostituire *Subnet1* precedente con il nome di una subnet:
		
		$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $VNetRgName
		$VNet.Subnets | Format-Table Name, AddressPrefix

4. Immettere il comando seguente per recuperare la subnet e assegnarla a una variabile.

		$Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }

5. <a name="ipconfigs"></a>Definire le configurazioni IP da assegnare all'interfaccia di rete. Ogni configurazione può avere una risorsa indirizzo IP privato statico o dinamico e una risorsa indirizzo IP pubblico associata con un indirizzo statico o dinamico.

	Le configurazioni di esempio seguenti verranno create e assegnate a un'interfaccia di rete, a cui saranno assegnati tre indirizzi IP privati e un indirizzo IP pubblico.

	- **IPConfig-1**: un indirizzo IP privato dinamico (impostazione predefinita) e una risorsa indirizzo IP pubblico denominata *PIP1*.
	- **IPConfig-2**: un indirizzo IP privato statico e nessun indirizzo IP pubblico.
	- **IPConfig-3**: un indirizzo IP privato dinamico e nessun indirizzo IP pubblico.

	Aggiungere o rimuovere il numero desiderato di configurazioni seguenti in base al numero di indirizzi IP da associare all'interfaccia di rete e alle impostazioni da configurare.

	**IPConfig-1**

	Cambiare il valore *PIP1* specificando il nome di una risorsa indirizzo IP pubblico esistente nella località in cui si sta creando l'interfaccia di rete e non attualmente associata a un'altra interfaccia di rete. Cambiare il valore *RG1* specificando il nome del gruppo di risorse in cui esiste la risorsa indirizzo IP pubblico. Cambiare il valore *IPConfig-1* specificando il nome da assegnare alla prima configurazione IP. Immettere i comandi seguenti:

		$PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName "RG1"

		$IpConfigName1 = "IPConfig-1"
		$IPConfig1     = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName1 -Subnet $Subnet -PublicIpAddress $PIP1 -Primary 

	Si noti l'opzione *-Primary*. Quando si assegnano più configurazioni IP a un'interfaccia di rete, è necessario assegnare una configurazione a *Primary*. Se non si conosce il nome di una risorsa indirizzo IP pubblico esistente, immettere il comando seguente:

		Get-AzureRMPublicIPAddress |Format-Table Name, Location, IPAddress, IpConfiguration

	Se la colonna **IPConfiguration** non include alcun valore per l'output restituito, la risorsa indirizzo IP pubblico non è associata a un'interfaccia di rete esistente e può essere usata. Se l'elenco è vuoto o non sono disponibili risorse indirizzo IP pubblico, è possibile crearne una usando il comando **New-AzureRmPublicIPAddress**.

	>[AZURE.NOTE] Per gli indirizzi IP pubblici è prevista una tariffa nominale. Per altre informazioni sui prezzi degli indirizzi IP, vedere la pagina [Prezzi per gli indirizzi IP](https://azure.microsoft.com/pricing/details/ip-addresses).

	**IPConfig-2**

	Cambiare il valore *IPConfig-2* specificando il nome da assegnare alla seconda configurazione IP e cambiare *10.0.0.5* specificando un indirizzo IP non usato valido per la subnet a cui si sta assegnando l'interfaccia di rete. Immettere i comandi seguenti:

		$IPConfigName2 = "IPConfig-2"
		$IPAddress = 10.0.0.5

		$IPConfig2 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName2 -Subnet $Subnet -PrivateIpAddress

	Immettere il comando seguente, se non si conosce l'intervallo di indirizzi IP assegnati alla subnet:

		$VNet.Subnets | Format-Table Name, AddressPrefix
		
	**IPConfig-3**

	Cambiare il valore *IPConfig-3* specificando il nome da assegnare alla terza configurazione IP e immettere i comandi seguenti:

		$IPConfigName3 = "IPConfig-3"
		$IPConfig3 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName3 -Subnet $Subnet
		
	>[AZURE.NOTE] È possibile assegnare al massimo 250 indirizzi IP privati a un'interfaccia di rete. È previsto un limite per il numero di indirizzi IP pubblici che possono essere usati in una sottoscrizione. Per altre informazioni, vedere l'articolo [Limiti di Azure](../azure-subscription-service-limits.md#networking-limits---azure-resource-manager).

6. Creare l'interfaccia di rete usando le configurazioni IP definite nel passaggio precedente.

		$nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRgName -Location $NicLocation -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3

7. Collegare l'interfaccia di rete durante la creazione di una macchina virtuale seguendo la procedura illustrata nell'articolo [Creare una macchina virtuale](../virtual-machines/virtual-machines-windows-ps-create.md). Anche se l'articolo crea una macchina virtuale che esegue Windows Server, la procedura è uguale per una macchina virtuale Linux, ma si seleziona un sistema operativo diverso. Completare i passaggi 1-3 dell'articolo. Ignorare i passaggi 4 e 5 e quindi completare il passaggio 6 nell'articolo Creare una macchina virtuale.

	>[AZURE.WARNING] Il passaggio 6 nell'articolo Creare una macchina virtuale avrà esito negativo se si modifica la variabile denominata $nic specificando un valore diverso nel passaggio 6 di questo articolo o se non sono stati completati i passaggi precedenti di questo articolo.

8. Visualizzare gli indirizzi IP assegnati da Azure DHCP all'interfaccia di rete e la risorsa indirizzo IP pubblico assegnata all'interfaccia di rete immettendo il comando seguente:

		$nic.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary

9. <a name="os"></a>Aggiungere manualmente tutti gli indirizzi IP privati secondari, ovvero gli indirizzi IP con *False* nella colonna **Primary** dell'output nel passaggio precedente, alla configurazione TCP/IP nel sistema operativo. L'indirizzo IP assegnato a *IPConfig-1* nel passaggio 5 viene assegnato automaticamente al sistema operativo tramite Azure DHCP, perché corrisponde alla configurazione di *Primary*.

	**Windows**

	1. Da un prompt dei comandi digitare ipconfig /all. Viene visualizzato solo l'indirizzo IP privato *Primary*, tramite DHCP.
	2. Aprire le proprietà per la scheda di rete.
	3. Aprire le proprietà per **Protocollo Internet versione 4**.
		- Fare clic su **Utilizza il seguente indirizzo IP** e immettere i valori seguenti:
			- **Indirizzo IP**: immettere l'indirizzo IP privato *Primary*.
			- **Subnet mask**: configurare questo valore in base alla subnet. Se, ad esempio, la subnet è di tipo /24, la subnet mask è 255.255.255.0.
			- **Gateway predefinito**: primo indirizzo IP nella subnet. Se la subnet è 10.0.0.0/24m, l'indirizzo IP del gateway è 10.0.0.1.
		- Fare clic su **Utilizza i seguenti indirizzi server DNS** e immettere i valori seguenti:
			- **Server DNS preferito**: immettere 168.63.129.16 se non si usa il proprio server DNS. Se si usa il proprio server DNS, immettere l'indirizzo IP corrispondente.
		- Fare clic sul pulsante **Avanzate** e aggiungere altri indirizzi IP. Aggiungere ogni indirizzo IP privato secondario elencato nel passaggio 8 all'interfaccia di rete con la stessa subnet specificata per l'indirizzo IP primario.
		- Fare clic su **OK** per chiudere le impostazioni TCP/IP e quindi di nuovo su **OK** per chiudere le impostazioni della scheda.
	4. Da un prompt dei comandi digitare *ipconfig /all*. Tutti gli indirizzi IP aggiunti vengono visualizzati e DHCP viene disattivato.

	**Linux (Ubuntu)**
	
	1. Aprire una finestra del terminale.
	2. Assicurarsi di essere l'utente ROOT. In caso contrario, è possibile eseguire questa operazione usando il comando seguente:
	
			sudo -i 
	3. Aggiornare il file di configurazione dell'interfaccia di rete, presupponendo 'eth0'.
		- Mantenere la voce esistente per dhcp. L'indirizzo IP primario verrà configurato specificando i valori precedenti.
		- Aggiungere una configurazione per un indirizzo IP statico aggiuntivo con i comandi seguenti:

				cd /etc/network/interfaces.d/
				ls

		Dovrebbe essere visualizzato un file con estensione cfg.
	4. Aprire il file: vi *filename*.

		Dovrebbero essere visualizzate le righe seguenti alla fine del file:

			auto eth0
			iface eth0 inet dhcp
	5. Aggiungere le righe seguenti dopo le righe esistenti nel file:

			iface eth0 inet static
			address <your private IP address here>
	6. Salvare il file usando il comando seguente:

			:wq
	7.  Reimpostare l'interfaccia di rete con il comando seguente:

			sudo ifdown eth0 && sudo ifup eth0

		>[AZURE.IMPORTANT]Eseguire ifdown e ifup nella stessa riga se si usa una connessione remota.
	8. Verificare che l'indirizzo IP venga aggiunto all'interfaccia di rete con il comando seguente:

			ip addr list eth0

		L'indirizzo IP aggiunto dovrebbe essere incluso nell'elenco.

	**Linux (Redhat, CentOS e altro)**
	
	1. Aprire una finestra del terminale.
	2. Assicurarsi di essere l'utente ROOT. In caso contrario, è possibile eseguire questa operazione usando il comando seguente:

			sudo -i
	3. Immettere la password e seguire le istruzioni visualizzate. Quando si è l'utente ROOT, passare alla cartella degli script di rete con il comando seguente:

			cd /etc/sysconfig/network-scripts
	4. Elencare i file ifcfg correlati usando il comando seguente:

			ls ifcfg-*

		Uno dei file visualizzati dovrebbe essere *ifcfg-eth0*.
	5. Copiare il file *ifcfg-eth0* e denominarlo *ifcfg-eth0:0* con il comando seguente:

			cp ifcfg-eth0 ifcfg-eth0:0
	6. Modificare il file *ifcfg-eth0:0* con il comando seguente:

			vi ifcfg-eth1
	7. Cambiare il dispositivo specificando il nome appropriato nel file, in questo caso *eth0:0*, con il comando seguente:

			DEVICE=eth0:0
	8. Cambiare la riga *IPADDR = YourPrivateIPAddress* in modo che rispecchi l'indirizzo IP.
	9. Salvare il file usando il comando seguente:

			(:wq)
	10. Riavviare i servizi di rete e assicurarsi che le modifiche siano riuscite eseguendo i comandi seguenti:

			/etc/init.d/network restart
			Ipconfig

		L'indirizzo IP aggiunto, *eth0:0*, dovrebbe essere incluso nell'elenco restituito.

## <a name="add"></a>Aggiungere indirizzi IP a una macchina virtuale esistente

Completare la procedura seguente per aggiungere altri indirizzi IP a un'interfaccia di rete esistente:

1. Aprire un prompt dei comandi di PowerShell e completare i passaggi rimanenti in questa sezione in una singola sessione di PowerShell. Se PowerShell non è già installato e configurato, completare la procedura disponibile nell'articolo [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

2. Cambiare i "valori" delle variabili $Variables seguenti specificando il nome dell'interfaccia di rete a cui si vogliono aggiungere gli indirizzi IP e il gruppo di risorse e la località in cui esiste l'interfaccia di rete:

		$NicName     = "RG1-VM1-NI1"
		$NicRgName   = "RG1"
		$NicLocation = "westus"

	Se non si conosce il nome dell'interfaccia di rete da modificare, immettere i comandi seguenti e quindi cambiare i valori delle variabili precedenti:

		Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location

3. Creare una variabile e impostarla sull'interfaccia di rete digitando il comando seguente:

		$nic = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRgName

4. Recuperare l'ID della subnet a cui è connessa l'interfaccia di rete completando il [Passaggio 3](#subnet) della sezione Creare una macchina virtuale con più indirizzi IP di questo articolo.

5. Creare le configurazioni IP da aggiungere alla rete seguendo le istruzioni disponibili nel [Passaggio 5](#ipconfigs) della sezione Creare una macchina virtuale con più indirizzi IP di questo articolo.

6. Cambiare il valore *$IPConfigName4* specificando il nome della configurazione IP creata nel passaggio precedente. Per aggiungere la configurazione, immettere il comando seguente:

		Add-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName4 -NetworkInterface $nic -Subnet $Subnet1 

7. Per configurare l'interfaccia di rete con la configurazione IP, immettere il comando seguente:

		Set-AzureRmNetworkInterface -NetworkInterface $nic

8. Aggiungere gli indirizzi IP aggiunti all'interfaccia di rete al sistema operativo della macchina virtuale seguendo le istruzioni disponibili nel [Passaggio 9](#os) della sezione Creare una macchina virtuale con più indirizzi IP di questo articolo.

<!---HONumber=AcomDC_0928_2016-->