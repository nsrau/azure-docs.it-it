<properties 
	pageTitle="Applicazione line-of-business fase 1 | Microsoft Azure" 
	description="Creare la rete virtuale e altri elementi dell'infrastruttura di Azure nella fase 1 dell'applicazione line-of-business di Azure." 
	documentationCenter=""
	services="virtual-machines-windows" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/01/2016" 
	ms.author="josephd"/>

# Carico di lavoro dell'applicazione line-of-business - Fase 1: Configurare Azure
 
In questa fase della distribuzione di un'applicazione line-of-business a disponibilità elevata in servizi di infrastruttura di Azure viene creata l'infrastruttura di rete e di archiviazione di Azure. È necessario completare questa fase prima di passare alla [Fase 2](virtual-machines-windows-ps-lob-ph2.md). Per informazioni su tutte le fasi, vedere [Distribuire un'applicazione line-of-business a disponibilità elevata in Azure](virtual-machines-windows-lob-overview.md).

È necessario eseguire il provisioning di Azure con questi componenti di rete di base:

- Una rete virtuale cross-premise con una subnet per ospitare le macchine virtuali di Azure
- Due account di archiviazione di Azure per archiviare le immagini di dischi rigidi virtuali e i dischi dati aggiuntivi
- Tre set di disponibilità

## Prima di iniziare

Prima di iniziare la configurazione dei componenti di Azure, è necessario compilare le tabelle seguenti. Per agevolare le procedure per la configurazione di Azure, stampare questa sezione e prendere nota delle informazioni necessarie o copiare questa sezione in un documento e procedere con la compilazione.

Per le impostazioni della rete virtuale (VNet), compilare la Tabella V.

Elemento | Elemento di configurazione | Descrizione | Valore 
--- | --- | --- | --- 
1\. | Nome della rete virtuale | Nome da assegnare alla rete virtuale di Azure (ad esempio SPFarmNet). | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | Posizione della rete virtuale | Il centro dati di Azure che conterrà la rete virtuale. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | Nome della rete locale | Nome da assegnare alla rete dell'organizzazione. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
4\. | Indirizzo IP del dispositivo VPN | Indirizzo IPv4 pubblico dell'interfaccia del dispositivo VPN su Internet. Collaborare con il reparto IT per determinare questo indirizzo. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
5\. | Spazio degli indirizzi di rete virtuale | Lo spazio degli indirizzi (definito in un singolo prefisso di indirizzo privato) per la rete virtuale. Collaborare con il reparto IT per determinare questo spazio degli indirizzi. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
6\. | Primo server DNS per la rete virtuale | Il quarto indirizzo IP possibile per lo spazio degli indirizzi della seconda subnet della rete virtuale (vedere la Tabella S). Collaborare con il reparto IT per determinare questo indirizzo. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
7\. | Secondo server DNS per la rete virtuale | Il quinto indirizzo IP possibile per lo spazio degli indirizzi della seconda subnet della rete virtuale (vedere la Tabella S). Collaborare con il reparto IT per determinare questo indirizzo. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
8\. | Chiave condivisa IPSec | Stringa alfanumerica casuale composta da 32 caratteri che verrà usata per l'autenticazione di entrambi i lati della connessione VPN da sito a sito. Collaborare con il reparto IT o addetto alla sicurezza per stabilire il valore di questa chiave. In alternativa, vedere [Creare una stringa casuale per una chiave precondivisa IPsec](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx).| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabella V: Configurazione della rete virtuale cross-premise**

Compilare la Tabella S per la subnet di questa soluzione.

- Per la prima subnet stabilire uno spazio degli indirizzi a 29 bit (con lunghezza del prefisso /29) per la subnet gateway di Azure.
- Per la seconda subnet specificare un nome descrittivo, un spazio degli indirizzi IP basato sullo spazio degli indirizzi di rete virtuale e uno scopo descrittivo. 

Collaborare con il reparto IT per determinare questi spazi degli indirizzi dallo spazio degli indirizzi di rete virtuale. Per gli spazi degli indirizzi usare il formato Classless Interdomain Routing (CIDR), noto anche come formato di prefisso di rete. Un esempio è 10.24.64.0/20.

Elemento | Nome della subnet | Spazio degli indirizzi della subnet | Scopo 
--- | --- | --- | --- 
1\. | Subnet gateway | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | La subnet usata dalle macchine virtuali del gateway di Azure.
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabella S: Subnet nella rete virtuale**

> [AZURE.NOTE] Questa architettura predefinita usa una singola subnet per motivi di semplicità. Se si desidera sovrapporre un set di filtri di traffico per emulare l'isolamento di subnet, è possibile utilizzare i [Gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) di Azure.

Per i due server DNS locali da usare quando si impostano inizialmente i controller di dominio nella rete virtuale, compilare la Tabella D. Assegnare a ciascun server DNS un nome descrittivo e un singolo indirizzo IP. Non è necessario che il nome descrittivo corrisponda al nome host o al nome del computer del server DNS. Si noti che sono elencate due voci vuote, ma è possibile aggiungerne altre. Collaborare con il reparto IT per stabilire questo elenco.

Elemento | Indirizzo IP del server DNS 
--- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 

**Tabella D: Server DNS locali**

Per distribuire pacchetti dalla rete virtuale di Azure alla rete dell'organizzazione attraverso la connessione VPN da sito a sito, è necessario configurare la rete virtuale con una rete locale che contiene un elenco di spazi degli indirizzi (in notazione CIDR) per tutte le posizioni raggiungibili nella rete locale dell'organizzazione. L'elenco di spazi degli indirizzi che definiscono la rete locale deve essere univoco e non deve sovrapporsi allo spazio degli indirizzi usato per altre reti virtuali o altre reti locali.

Per il set di spazi degli indirizzi della rete locale, compilare la Tabella L. Si noti che sono elencate tre voci vuote, ma in genere ne occorrono di più. Collaborare con il reparto IT per determinare questo elenco di spazi degli indirizzi.

Elemento | Spazio degli indirizzi della rete virtuale 
--- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabella L: Prefissi degli indirizzi per la rete locale**

In primo luogo, avviare un prompt di Azure PowerShell.

> [AZURE.NOTE] Il set di comandi seguente utilizza Azure PowerShell 1.0 e versioni successive. Per altre informazioni, vedere [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Innanzitutto, avviare un prompt di Azure PowerShell e accedere al proprio account.

	Login-AzureRMAccount

Ottenere il nome della sottoscrizione usando il comando seguente.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Impostare la sottoscrizione di Azure. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con i nomi corretti.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

A questo punto, creare un nuovo gruppo di risorse per l'applicazione line-of-business. Per determinare un nome di gruppo di risorse univoco, usare il comando seguente per elencare i gruppi di risorse esistenti.

	Get-AzureRMResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Creare il nuovo gruppo di risorse con questi comandi.

	$rgName="<resource group name>"
	$locName="<an Azure location, such as West US>"
	New-AzureRMResourceGroup -Name $rgName -Location $locName

Le macchine virtuali basate su Gestione risorse richiedono un account di archiviazione basato su Gestione risorse.

Elemento | Nome dell'account di archiviazione | Scopo 
--- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | L'account di archiviazione Premium usato dalle macchine virtuali di SQL Server.
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | L'account di archiviazione standard usato da tutte le altre macchine virtuali presenti nel carico di lavoro. 

**Tabella ST: Account di archiviazione**

Questi nomi saranno necessari quando si creano le macchine virtuali nelle fasi 2, 3 e 4.

Per ogni account di archiviazione è necessario selezionare un nome univoco globale che contenga solo lettere minuscole e numeri. È possibile usare il comando seguente per elencare gli account di archiviazione esistenti.

	Get-AzureRMStorageAccount | Sort StorageAccountName | Select StorageAccountName

Per creare il primo account di archiviazione, eseguire i comandi seguenti.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<Table ST – Item 1 - Storage account name column>"
	New-AzureRMStorageAccount -Name $saName -ResourceGroupName $rgName –Type Premium_LRS -Location $locName

Per creare il secondo account di archiviazione, eseguire i comandi seguenti.

	$saName="<Table ST – Item 2 - Storage account name column>"
	New-AzureRMStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

A questo punto, creare quindi la rete virtuale di Azure che ospiterà l'applicazione line-of-business.

	$rgName="<name of your new resource group>"
	$locName="<Azure location of the new resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$vnetAddrPrefix="<Table V – Item 5 – Value column>"
	$lobSubnetName="<Table S – Item 2 – Subnet name column>"
	$lobSubnetPrefix="<Table S – Item 2 – Subnet address space column>"
	$gwSubnetPrefix="<Table S – Item 1 – Subnet address space column>"
	$dnsServers=@( "<Table D – Item 1 – DNS server IP address column>", "<Table D – Item 2 – DNS server IP address column>" )
	$gwSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $gwSubnetPrefix
	$lobSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name $lobSubnetName -AddressPrefix $lobSubnetPrefix
	New-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix $vnetAddrPrefix -Subnet $gwSubnet,$lobSubnet -DNSServer $dnsServers

A questo punto, usare i comandi seguenti per creare i gateway per la connessione VPN da sito a sito.

	$vnetName="<Table V – Item 1 – Value column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	
	# Attach a virtual network gateway to a public IP address and the gateway subnet
	$publicGatewayVipName="LOBAppPublicIPAddress"
	$vnetGatewayIpConfigName="LOBAppPublicIPConfig"
	New-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$publicGatewayVip=Get-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName
	$vnetGatewayIpConfig=New-AzureRMVirtualNetworkGatewayIpConfig -Name $vnetGatewayIpConfigName -PublicIpAddressId $publicGatewayVip.Id -SubnetId $vnet.Subnets[0].Id

	# Create the Azure gateway
	$vnetGatewayName="LOBAppAzureGateway"
	$vnetGateway=New-AzureRMVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $rgName -Location $locName -GatewayType Vpn -VpnType RouteBased -IpConfigurations $vnetGatewayIpConfig
	
	# Create the gateway for the local network
	$localGatewayName="LOBAppLocalNetGateway"
	$localGatewayIP="<Table V – Item 4 – Value column>"
	$localNetworkPrefix=@( <comma-separated, double-quote enclosed list of the local network address prefixes from Table L, example: "10.1.0.0/24", "10.2.0.0/24"> )
	$localGateway=New-AzureRMLocalNetworkGateway -Name $localGatewayName -ResourceGroupName $rgName -Location $locName -GatewayIpAddress $localGatewayIP -AddressPrefix $localNetworkPrefix
	
	# Define the Azure virtual network VPN connection
	$vnetConnectionName="LOBAppS2SConnection"
	$vnetConnectionKey="<Table V – Item 8 – Value column>"
	$vnetConnection=New-AzureRMVirtualNetworkGatewayConnection -Name $vnetConnectionName -ResourceGroupName $rgName -Location $locName -ConnectionType IPsec -SharedKey $vnetConnectionKey -VirtualNetworkGateway1 $vnetGateway -LocalNetworkGateway2 $localGateway

A questo punto, configurare il dispositivo VPN locale per la connessione al gateway VPN di Azure. Per altre informazioni, vedere [Configurare il dispositivo VPN](../virtual-network/vpn-gateway-configure-vpn-gateway-mp.md#configure-your-vpn-device).

Per configurare il dispositivo VPN locale, è necessario quanto segue:

- L'indirizzo IPv4 pubblico del gateway VPN di Azure per la rete virtuale (visualizzato dopo aver eseguito il comando **Get-AzureRMPublicIpAddress -Name $publicGatewayVipName -ResourceGroupName $rgName**)
- La chiave precondivisa IPSec per la connessione VPN da sito a sito (Tabella V - Elemento 8 - colonna Valore)

Successivamente, verificare che lo spazio degli indirizzi della rete virtuale sia raggiungibile da una rete locale. Ciò avviene di solito aggiungendo una route corrispondente allo spazio di indirizzi di rete virtuale al dispositivo VPN e annunciando quindi tale route al resto dell'infrastruttura di della rete dell'organizzazione. Collaborare con il reparto IT per stabilire come fare.

A questo punto, definire i nomi di tre set di disponibilità. Compilare la Tabella A.

Elemento | Scopo | Nome set di disponibilità 
--- | --- | --- 
1\. | Controller di dominio | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | Server di SQL | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | Server Web | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabella A: Nome set di disponibilità**

Questi nomi saranno necessari quando si creano le macchine virtuali nelle fasi 2, 3 e 4.

Usare i comandi di Azure PowerShell seguenti per creare questi nuovi set di disponibilità.

	$rgName="<your new resource group name>"
	$locName="<the Azure location for your new resource group>"
	$avName="<Table A – Item 1 – Availability set name column>"
	New-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 2 – Availability set name column>"
	New-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 3 – Availability set name column>"
	New-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

Questa è la configurazione risultante dal corretto completamento di questa fase.

![](./media/virtual-machines-windows-ps-lob-ph1/workload-lobapp-phase1.png)

## Passaggio successivo

- Seguire la [Fase 2](virtual-machines-windows-ps-lob-ph2.md) per continuare con la configurazione di questo carico di lavoro.

<!---HONumber=AcomDC_0601_2016-->