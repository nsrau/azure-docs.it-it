<properties 
	pageTitle="Carico di lavoro dell'applicazione line-of-business - Fase 1: Configurare Azure" 
	description="In questa prima fase della distribuzione di un'applicazione line-of-business a disponibilità elevata in servizi di infrastruttura di Azure vengono creati la rete virtuale e altri elementi dell'infrastruttura di Azure." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/11/2015" 
	ms.author="josephd"/>

# Carico di lavoro dell'applicazione line-of-business - Fase 1: Configurare Azure

In questa fase della distribuzione di un'applicazione line-of-business a disponibilità elevata in servizi di infrastruttura di Azure viene creata l'infrastruttura di rete e di archiviazione di Azure. È necessario completare questa fase prima di passare alla [Fase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md). Per informazioni su tutte le fasi, vedere [Distribuire un'applicazione line-of-business a disponibilità elevata in Azure](virtual-machines-workload-high-availability-LOB-application-overview.md).

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
8\. | Chiave condivisa IPSec | Stringa alfanumerica casuale composta da 128 caratteri che verrà usata per l'autenticazione di entrambi i lati della connessione VPN da sito a sito. Collaborare con il reparto IT o addetto alla sicurezza per stabilire il valore di questa chiave. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_


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

> [AZURE.NOTE]Questa architettura predefinita usa una singola subnet per motivi di semplicità. Per sovrapporre un set di filtri di traffico per emulare l'isolamento di subnet, è possibile usare i [Gruppi di sicurezza di rete](https://msdn.microsoft.com/library/azure/dn848316.aspx) di Azure.

Per i due server DNS locali da usare quando si impostano inizialmente i controller di dominio nella rete virtuale, compilare la Tabella D. Assegnare a ciascun server DNS un nome descrittivo e un singolo indirizzo IP. Non è necessario che il nome descrittivo corrisponda al nome host o al nome del computer del server DNS. Si noti che sono elencate due voci vuote, ma è possibile aggiungerne altre. Collaborare con il reparto IT per stabilire questo elenco.

Elemento | Nome descrittivo per il server DNS | Indirizzo IP del server DNS 
--- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 

**Tabella D: Server DNS locali**

Per distribuire pacchetti dalla rete virtuale di Azure alla rete dell'organizzazione attraverso la connessione VPN da sito a sito, è necessario configurare la rete virtuale con una rete locale che contiene un elenco di spazi degli indirizzi (in notazione CIDR) per tutte le posizioni raggiungibili nella rete locale dell'organizzazione. L'elenco di spazi degli indirizzi che definiscono la rete locale deve essere univoco e non deve sovrapporsi allo spazio degli indirizzi usato per altre reti virtuali o altre reti locali.

Per il set di spazi degli indirizzi della rete locale, compilare la Tabella L. Si noti che sono elencate tre voci vuote, ma in genere ne occorrono di più. Collaborare con il reparto IT per determinare questo elenco di spazi degli indirizzi.

Elemento | Spazio degli indirizzi della rete virtuale 
--- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabella L: Prefissi degli indirizzi per la rete locale**

A questo punto, è necessario aver installato Azure PowerShell 0.9.5 o versione successiva. Per verificare la versione installata di Azure PowerShell, eseguire il comando seguente:

	Get-Module azure | format-table version

Se è necessario installare la versione più recente di Azure PowerShell, usare **Pannello di controllo - Programmi e funzionalità** per rimuovere la versione corrente. Usare quindi le istruzioni disponibili in [Come installare e configurare Azure PowerShell](../install-configure-powershell.md) per installare Azure PowerShell nel computer locale. Aprire un prompt dei comandi di Azure PowerShell.

Selezionare innanzitutto la sottoscrizione di Azure corretta con questi comandi. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con i nomi corretti.

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

È possibile ottenere il nome della sottoscrizione dalla proprietà **SubscriptionName** dell'output del comando **Get-AzureSubscription**.

A questo punto, usare questo comando per passare ad Azure PowerShell in modalità Gestione risorse.

	Switch-AzureMode AzureResourceManager 

A questo punto, creare un nuovo gruppo di risorse per l'applicazione line-of-business.

Per determinare un nome di gruppo di risorse univoco, usare il comando seguente per elencare i gruppi di risorse esistenti.

	Get-AzureResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Per ottenere un elenco dei percorsi di Azure in cui è possibile creare macchine virtuali basate su Gestione risorse, usare i comandi seguenti.

	$loc=Get-AzureLocation | where { $_.Name –eq "Microsoft.Compute/virtualMachines" }
	$loc.Locations

Creare il nuovo gruppo di risorse con questi comandi.

	$rgName="<resource group name>"
	$locName="<an Azure location, such as West US>"
	New-AzureResourceGroup -Name $rgName -Location $locName

Le macchine virtuali basate su Gestione risorse richiedono un account di archiviazione basato su Gestione risorse.

Elemento | Nome dell'account di archiviazione | Scopo 
--- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | L'account di archiviazione Premium usato dalle macchine virtuali di SQL Server.
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | L'account di archiviazione standard usato da tutte le altre macchine virtuali presenti nel carico di lavoro. 

**Tabella ST: Account di archiviazione**

Questi nomi saranno necessari quando si creano le macchine virtuali nelle fasi 2, 3 e 4.

Per ogni account di archiviazione è necessario selezionare un nome univoco globale che contenga solo lettere minuscole e numeri. È possibile usare il comando seguente per elencare gli account di archiviazione esistenti.

	Get-AzureStorageAccount | Sort Name | Select Name

Per verificare che quello prescelto sia un nome univoco globale, è necessario eseguire il comando **Test-AzureName** nella modalità di gestione del servizio Azure di PowerShell. Usare i comandi seguenti.

	Switch-AzureMode AzureServiceManagement
	Test-AzureName -Storage <Proposed storage account name>

Se il comando Test-AzureName visualizza **False**, il nome proposto è univoco. Dopo aver stabilito un nome univoco per entrambi gli account di archiviazione, aggiornare la Tabella ST e tornare alla modalità Gestione risorse di Azure PowerShell usando il comando seguente.

	Switch-AzureMode AzureResourceManager 

Per creare il primo account di archiviazione, eseguire i comandi seguenti.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<Table ST – Item 1 - Storage account name column>"
	New-AzureStorageAccount -Name $saName -ResourceGroupName $rgName –Type Premium_LRS -Location $locName

Per creare il secondo account di archiviazione, eseguire i comandi seguenti.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<Table ST – Item 2 - Storage account name column>"
	New-AzureStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

A questo punto, creare quindi la rete virtuale di Azure che ospiterà l'applicazione line-of-business.

	$rgName="<name of your new resource group>"
	$locName="<Azure location of the new resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$vnetAddrPrefix="<Table V – Item 5 – Value column>"
	$lobSubnetName="<Table S – Item 2 – Subnet name column>"
	$lobSubnetPrefix="<Table S – Item 2 – Subnet address space column>"
	$gwSubnetPrefix="<Table S – Item 1 – Subnet address space column>"
	$dnsServers=@( "<Table D – Item 1 – DNS server IP address column>", "<Table D – Item 2 – DNS server IP address column>" )
	$gwSubnet=New-AzureVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $gwSubnetPrefix
	$lobSubnet=New-AzureVirtualNetworkSubnetConfig -Name $lobSubnetName -AddressPrefix $lobSubnetPrefix
	New-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix $vnetAddrPrefix -Subnet $gwSubnet,$lobSubnet -DNSServer $dnsServers

A questo punto, usare i comandi seguenti per creare i gateway per la connessione VPN da sito a sito.

	$vnetName="<Table V – Item 1 – Value column>"
	$vnet=Get-AzureVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	
	# Attach a virtual network gateway to a public IP address and the gateway subnet
	$publicGatewayVipName="LOBAppPublicIPAddress"
	$vnetGatewayIpConfigName="LOBAppPublicIPConfig"
	New-AzurePublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$publicGatewayVip=Get-AzurePublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName
	$vnetGatewayIpConfig=New-AzureVirtualNetworkGatewayIpConfig -Name $vnetGatewayIpConfigName -PublicIpAddressId $publicGatewayVip.Id -SubnetId $vnet.Subnets[0].Id

	# Create the Azure gateway
	$vnetGatewayName="LOBAppAzureGateway"
	$vnetGateway=New-AzureVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $rgName -Location $locName -GatewayType Vpn -VpnType RouteBased -IpConfigurations $vnetGatewayIpConfig
	
	# Create the gateway for the local network
	$localGatewayName="LOBAppLocalNetGateway"
	$localGatewayIP="<Table V – Item 4 – Value column>"
	$localNetworkPrefix=@( <comma-separated, double-quote enclosed list of the local network address prefixes from Table L, example: "10.1.0.0/24", "10.2.0.0/24"> )
	$localGateway=New-AzureLocalNetworkGateway -Name $localGatewayName -ResourceGroupName $rgName -Location $locName -GatewayIpAddress $localGatewayIP -AddressPrefix $localNetworkPrefix
	
	# Define the Azure virtual network VPN connection
	$vnetConnectionName="LOBAppS2SConnection"
	$vnetConnectionKey="<Table V – Item 8 – Value column>"
	$vnetConnection=New-AzureVirtualNetworkGatewayConnection -Name $vnetConnectionName -ResourceGroupName $rgName -Location $locName -ConnectionType IPsec -SharedKey $vnetConnectionKey -VirtualNetworkGateway1 $vnetGateway -LocalNetworkGateway2 $localGateway

A questo punto, configurare il dispositivo VPN locale per la connessione al gateway VPN di Azure. Per altre informazioni, vedere [Configurare il dispositivo VPN](../virtual-networks/vpn-gateway-configure-vpn-gateway-mp.md#configure-your-vpn-device).

Per configurare il dispositivo VPN locale, è necessario quanto segue:

- L'indirizzo IPv4 pubblico del gateway VPN di Azure per la rete virtuale (visualizzato dopo aver eseguito il comando **Get-AzurePublicIpAddress -Name $publicGatewayVipName -ResourceGroupName $rgName**)
- La chiave precondivisa IPSec per la connessione VPN da sito a sito (Tabella V - Elemento 8 - colonna Valore)

A questo punto, verificare che lo spazio degli indirizzi della rete virtuale sia raggiungibile da una rete locale. Ciò avviene di solito aggiungendo una route corrispondente allo spazio di indirizzi di rete virtuale al dispositivo VPN e annunciando quindi tale route al resto dell'infrastruttura di della rete dell'organizzazione. Collaborare con il reparto IT per stabilire come fare.

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
	New-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 2 – Availability set name column>"
	New-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 3 – Availability set name column>"
	New-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

Questa è la configurazione risultante dal corretto completamento di questa fase.

![](./media/virtual-machines-workload-high-availability-LOB-application-phase1/workload-lobapp-phase1.png)

## Passaggio successivo

Per continuare con la configurazione di questo carico di lavoro, passare a [Fase 2: Configurare di controller di dominio](virtual-machines-workload-high-availability-LOB-application-phase2.md).

## Risorse aggiuntive

[Distribuire un'applicazione line-of-business a disponibilità elevata in Azure](virtual-machines-workload-high-availability-LOB-application-overview.md)

[Progetti dell'architettura per applicazioni line-of-business](http://msdn.microsoft.com/dn630664)

[Configurare un'applicazione LOB basata sul Web in un cloud ibrido per l'esecuzione di test](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Linee guida sull'implementazione dei servizi di infrastruttura di Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Carico di lavoro dei servizi di infrastruttura di Azure: farm di SharePoint Server 2013](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=August15_HO8-->