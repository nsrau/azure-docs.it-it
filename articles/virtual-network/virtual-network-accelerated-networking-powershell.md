<properties 
   pageTitle="Rete accelerata per macchine virtuali | Microsoft Azure"
   description="Informazioni su come configurare la funzionalità Rete accelerata per una macchina virtuale di Azure tramite PowerShell."
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

# Rete accelerata per macchine virtuali

La funzionalità Rete accelerata abilita Single Root I/O Virtualization (SR-IOV) per le macchine virtuali, migliorandone le prestazioni di rete. Questo percorso a prestazioni elevate ignora l'host del percorso dati riducendo la latenza, l'instabilità e l'utilizzo della CPU e può essere usato con i carichi di lavoro di rete più gravosi nei tipi di macchine virtuali supportati. Questo articolo spiega come usare PowerShell per configurare Rete accelerata nel modello di distribuzione Azure Resource Manager.

L'immagine seguente illustra le comunicazioni tra due macchine virtuali (VM), con e senza Rete accelerata:

![Confronto](./media/virtual-network-accelerated-networking-powershell/image1.png)

Senza Rete accelerata, tutto il traffico di rete in ingresso e in uscita dalla macchina virtuale deve attraversare l'host e il commutatore virtuale. Quest'ultimo è responsabile dell'applicazione di tutti i criteri al traffico di rete, ad esempio gruppi di sicurezza di rete, elenchi di controllo di accesso, isolamento e altri servizi di rete virtualizzati. Per altre informazioni, vedere l'articolo [Hyper-V Network Virtualization and Virtual Switch](https://technet.microsoft.com/library/jj945275.aspx) (Virtualizzazione rete Hyper-V e commutatore virtuale).

Con Rete accelerata il traffico di rete raggiunge la scheda di rete (NIC) e viene quindi inoltrato alla macchina virtuale. Il carico di tutti i criteri di rete applicati dal commutatore virtuale senza Rete accelerata viene ripartito e applicato all'hardware. L'applicazione dei criteri all'hardware permette alla scheda di rete di inoltrare il traffico di rete direttamente alla macchina virtuale ignorando l'host e il commutatore virtuale, pur mantenendo tutti i criteri applicati all'host.

I vantaggi della funzionalità Rete accelerata si applicano solo alla macchina virtuale in cui è abilitata. Per ottenere risultati ottimali, è consigliabile abilitarla in almeno due macchine virtuali connesse alla stessa rete virtuale. Nella comunicazione tra reti virtuali o nella connessione locale questa funzionalità ha un impatto minimo sulla latenza complessiva.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

##Vantaggi

- **Latenza più bassa e più pacchetti al secondo (pps):** rimuovendo il commutatore virtuale dal percorso dati viene eliminato il tempo di attesa dei pacchetti nell'host per l'elaborazione dei criteri, aumentando così il numero di pacchetti che possono essere elaborati all'interno della macchina virtuale.
- **Instabilità ridotta:** l'elaborazione del commutatore dipende dalla quantità di criteri da applicare e dal carico di lavoro della CPU che esegue l'elaborazione. La ripartizione del carico di applicazione dei criteri nell'hardware elimina tale variabilità recapitando i pacchetti direttamente alla macchina virtuale, rimuovendo l'host dalle comunicazioni con la macchina virtuale nonché tutte le interruzioni software e i cambi di contesto.
- **Utilizzo ridotto della CPU:** ignorando il commutatore virtuale nell'host si ottiene un minore utilizzo della CPU per l'elaborazione del traffico di rete.

## Limitazioni

L'uso della funzionalità Rete accelerata presenta le limitazioni seguenti:
 
- **Creazione di un'interfaccia di rete:** la funzionalità Rete accelerata può essere abilitata solo per una nuova interfaccia di rete. Non può essere abilitata su un'interfaccia di rete esistente.
- **Creazione di una macchina virtuale:** un'interfaccia di rete con Rete accelerata abilitata può essere associata a una macchina virtuale solo durante la creazione della macchina virtuale. Non è possibile collegare l'interfaccia di rete a una macchina virtuale esistente.
- **Aree:** sono disponibili solo le aree di Azure Stati Uniti centro-occidentali ed Europa occidentale. In futuro il set di aree disponibili verrà ampliato.
- **Sistemi operativi supportati:** Microsoft Windows Server 2012 R2 e Windows Server 2016 Technical Preview 5. Presto verrà aggiunto il supporto per Linux e Windows Server 2012.
- **Dimensioni della macchina virtuale:** le uniche dimensioni delle istanze di macchina virtuale supportate sono Standard\_D15\_v2 e Standard\_DS15\_v2. Per altre informazioni, vedere l'articolo [Dimensioni delle macchine virtuali in Azure](../virtual-machines/virtual-machines-windows-sizes.md). In futuro il set di dimensioni delle istanze di macchina virtuale supportate verrà ampliato.

Le modifiche apportate a queste limitazioni verranno annunciate nella pagina relativa agli [aggiornamenti sulla rete virtuale di Azure](https://azure.microsoft.com/updates/?product=virtual-network&updatetype=&platform=).

## Creare una macchina virtuale Windows con Rete accelerata

1. Aprire un prompt dei comandi di PowerShell e completare i passaggi rimanenti in questa sezione in una singola sessione di PowerShell. Se PowerShell non è già installato e configurato, completare la procedura disponibile nell'articolo [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).
2. Per registrarsi all'anteprima, inviare un messaggio di posta elettronica a [Sottoscrizioni di Rete accelerata](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) con l'ID sottoscrizione e l'uso previsto.
3. Registrare la funzionalità nella sottoscrizione immettendo i comandi seguenti:

		Register-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network
		Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network

4. Se necessario, è possibile sostituire *westcentralus* con il nome di un'altra località supportata da questa funzionalità tra quelle indicate nella sezione [Limitazioni](#limitations) di questo articolo. Immettere il comando seguente per impostare una variabile per la località:

		$locName = "westcentralus"

5. Sostituire *RG1* con un nome per il gruppo di risorse che conterrà la nuova interfaccia di rete e immettere i comandi seguenti per crearlo:

		$rgName = "RG1"
		New-AzureRmResourceGroup -Name $rgName -Location $locName

6. Sostituire *VM1-NIC1* con il nome da usare per l'interfaccia di rete e quindi immettere il comando seguente:

		$NICName = "VM1-NIC1"

7. L'interfaccia di rete deve essere connessa a una subnet in una rete virtuale di Azure (VNet) esistente nella stessa località e nella stessa [sottoscrizione](../azure-glossary-cloud-terminology.md#subscription) dell'interfaccia di rete. Se non si ha familiarità con le reti virtuali, vedere l'articolo [Panoramica della rete virtuale](virtual-networks-overview.md). Per creare una rete virtuale, seguire la procedura disponibile nell'articolo [Creare una rete virtuale usando PowerShell](virtual-networks-create-vnet-arm-ps.md). Cambiare i "valori" delle variabili $Variables seguenti specificando il nome della rete virtuale e della subnet a cui si vuole connettere l'interfaccia di rete nonché il nome del gruppo di risorse in cui si trova la rete virtuale.

		$VNetName   = "VNet1"
		$SubnetName = "Subnet1"

	Se non si conosce il nome di una rete virtuale esistente nella località scelta al passaggio 3, immettere i comandi seguenti:
		
		$VirtualNetworks = Get-AzureRmVirtualNetwork
		$VirtualNetworks | Where-Object {$_.Location -eq $locName} | Format-Table Name, Location
		
	Se l'elenco restituito è vuoto, è necessario creare una rete virtuale nella località. Per creare una rete virtuale, seguire la procedura disponibile nell'articolo [Creare una rete virtuale usando PowerShell](virtual-networks-create-vnet-arm-ps.md).

	Per ottenere il nome delle subnet nella rete virtuale, digitare i comandi seguenti e sostituire *Subnet1* precedente con il nome di una subnet:
		
		$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
		$VNet.Subnets | Format-Table Name, AddressPrefix

8. Immettere i comandi seguenti per recuperare la rete virtuale e la subnet e assegnarle a variabili.

		$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
		$Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }

9. Identificare una risorsa indirizzo IP pubblico esistente che possa essere associata all'interfaccia di rete per la connessione tramite Internet. Se non si vuole accedere alla macchina virtuale con l'interfaccia di rete tramite Internet, è possibile ignorare questo passaggio. Senza un indirizzo IP pubblico, è necessario connettersi alla macchina virtuale da un'altra macchina virtuale connessa alla stessa rete virtuale.

	Sostituire *PIP1* con il nome di una risorsa indirizzo IP pubblico esistente nella località in cui si sta creando l'interfaccia di rete e attualmente non associata a un'altra interfaccia di rete. Se necessario, sostituire $rgName con il nome del gruppo di risorse in cui è presente la risorsa indirizzo IP pubblico e immettere il comando seguente:

		$PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $rgName

	Se non si conosce il nome di una risorsa indirizzo IP pubblico esistente, immettere i comandi seguenti:

		$PublicIPAddresses = Get-AzureRmPublicIPAddress
		$PublicIPAddresses | Where-Object { $_.Location -eq $locName } |Format-Table Name, Location, IPAddress, IpConfiguration

	Se la colonna **IPConfiguration** non include alcun valore nell'output restituito, la risorsa indirizzo IP pubblico non è associata a un'interfaccia di rete esistente e può essere usata. Se l'elenco è vuoto o non sono disponibili risorse indirizzo IP pubblico, è possibile crearne una usando il comando New-AzureRmPublicIPAddress.

	>[AZURE.NOTE] Per gli indirizzi IP pubblici è prevista una tariffa nominale. Per altre informazioni sui prezzi, vedere la pagina [Prezzi per gli indirizzi IP](https://azure.microsoft.com/pricing/details/ip-addresses).
10. Se si sceglie di non aggiungere una risorsa indirizzo IP pubblico all'interfaccia, rimuovere *-PublicIPAddress $PIP1* alla fine del comando seguente. Creare l'interfaccia di rete con la funzionalità Rete accelerata immettendo il comando seguente:

		$nic = New-AzureRmNetworkInterface -Location $locName -Name $NICName -ResourceGroupName $rgName -Subnet $Subnet -EnableAcceleratedNetworking -PublicIpAddress $PIP1 

11. Per assegnare l'interfaccia di rete a una macchina virtuale durante la creazione della macchina virtuale, seguire le istruzioni riportate nei passaggi da 3 e 6 dell'articolo [Creare una VM Windows con Resource Manager e PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md). Nel passaggio 6.2, sostituire *Standard\_A1* con una delle dimensioni di macchina virtuale indicate nella sezione [Limitazioni](#limitations) di questo articolo.

	>[AZURE.NOTE] Se sono stati modificati i *nomi* delle variabili $locName, $rgName o $nic in questo articolo, il passaggio 6 dell'articolo sulla creazione di una macchina virtuale avrà esito negativo. È tuttavia possibile modificare i *valori* delle variabili.

12. Dopo aver creato la macchina virtuale, scaricare il [driver di Rete accelerata](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84), connettersi alla macchina virtuale ed eseguire il programma di installazione del driver all'interno della macchina virtuale.

13. Fare clic con il pulsante destro del mouse sul pulsante Windows e scegliere **Gestione dispositivi**. Espandere l'opzione **Rete** e verificare che la voce **Scheda Ethernet VF Mellanox ConnectX-3** sia visualizzata, come illustra la figura seguente:

	![Gestione dispositivi](./media/virtual-network-accelerated-networking-powershell/image2.png)

<!---HONumber=AcomDC_0928_2016-->