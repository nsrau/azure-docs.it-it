<properties 
	pageTitle="Ambienti di test cloud ibridi | Microsoft Azure" 
	description="Informazioni su come creare un ambiente cloud ibrido per professionisti IT o per il test di sviluppo, inclusa una rete locale semplificata." 
	services="virtual-machines-windows" 
	documentationCenter="" 
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

# Configurazione di un ambiente cloud ibrido per l'esecuzione di test
 
Questo argomento illustra la creazione di un ambiente cloud ibrido in Microsoft Azure per attività di test. Di seguito è riportata la configurazione risultante.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph5.png)

Viene simulato un ambiente di produzione reale ibrido dal percorso in Internet. È costituita da:

-  Una rete locale semplificata (la subnet Corpnet).
-  Una rete virtuale cross-premise ospitata in Azure (TestVNET).
-  Una connessione VPN da sito a sito.
-  Un controller di dominio secondario nella rete virtuale TestVNET.

Questa configurazione rappresenta una base e un punto di partenza comune da cui è possibile:

-  Sviluppare e testare applicazioni in un ambiente cloud ibrido.
-  Creare configurazioni di test di computer, alcune nella subnet Corpnet e altre nella rete virtuale TestVNET, per carichi di lavoro IT basati sul cloud.

L'impostazione di un ambiente di test del cloud ibrido comporta cinque fasi principali:

1.	Configurare i computer nella subnet Corpnet.
2.	Configurare RRAS1.
3.	Creare la rete virtuale di Azure cross-premise.
4.	Creare la connessione VPN da sito a sito.
5.	Configurare DC2. 

Se non si ha già una sottoscrizione di Azure, è possibile iscriversi per ottenere un account gratuito nella pagina [Versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/). Se si ha una sottoscrizione di MSDN o di Visual Studio, vedere [Credito Azure mensile per sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

>[AZURE.NOTE] Le macchine virtuali e i gateway di rete virtuale in Azure generano addebiti monetari in caso di esecuzione. Un gateway VPN di Azure viene implementato come set di due macchine virtuali di Azure. Per informazioni dettagliate, vedere [Rete virtuale - Prezzi](https://azure.microsoft.com/pricing/details/virtual-network/). Per ridurre al minimo i costi di esecuzione del gateway VPN, creare l'ambiente di test ed eseguire il test e la dimostrazione necessari il più rapidamente possibile.

Questa configurazione richiede una subnet di test di fino a quattro computer connessi direttamente a Internet utilizzando un indirizzo IP pubblico. Se non si hanno queste risorse, è anche possibile [configurare un ambiente cloud ibrido simulato per l'esecuzione di test](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md). L'ambiente di test cloud ibrida simulato richiede una sottoscrizione di Azure.

## Fase 1: Configurare i computer nella subnet Corpnet.

Utilizzare le istruzioni nella sezione "Passaggi per configurare la Subnet Corpnet" del [Guida dell'ambiente di Test: Base configurazione per Windows Server 2012 R2](http://www.microsoft.com/download/details.aspx?id=39638) per configurare i computer DC1, APP1 e CLIENT1 in una subnet denominata Corpnet. **Questa subnet deve essere isolata dalla rete dell'organizzazione perché verrà connessa direttamente a Internet tramite il computer RRAS1.**

Accedere a DC1 con le credenziali CORP\\User1. Per configurare il dominio CORP in modo che i computer e gli utenti usino il controller di dominio locale per l'autenticazione, eseguire questi comandi da un prompt dei comandi di Windows PowerShell a livello di amministratore.

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet “Name "10.0.0.0/8" “Site "TestLab"
	New-ADReplicationSubnet “Name "192.168.0.0/16" “Site "TestVNET

Questa è la configurazione corrente.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph1.png)
 
## Fase 2: Configurare RRAS1.

RRAS1 fornisce servizi di indirizzamento del traffico e servizi di dispositivi VPN tra i computer della subnet Corpnet e la rete virtuale TestVNET. RRAS1 deve disporre di due schede di rete installate.

Innanzitutto, installare il sistema operativo in RRAS1.

1.	Avviare l'installazione di Windows Server 2012 R2.
2.	Seguire le istruzioni per completare l'installazione, specificando una password complessa per l'account amministratore locale. Accedere usando l'account amministratore locale.
3.	Connettere RRAS1 a una rete con accesso a Internet ed eseguire Windows Update per installare gli aggiornamenti più recenti per Windows Server 2012 R2.
4.	Connettere una scheda di rete alla subnet Corpnet e l'altra direttamente a Internet. RRAS1 può trovarsi dietro un firewall Internet ma non deve trovarsi dietro un dispositivo NAT (Network Address Translator).

Configurare quindi le proprietà TCP/IP di RRAS1. Sarà necessaria una configurazione di indirizzo IP pubblico che includa indirizzo, subnet mask (o lunghezza del prefisso) e il gateway predefinito con i server DNS del provider di servizi Internet (ISP). L'indirizzo IP pubblico sarà necessario per la fase 3.

Usare questi comandi in un prompt dei comandi di Windows PowerShell di livello amministratore su RRAS1. Per usare questi comandi, inserire i valori delle variabili e rimuovere i caratteri < and >. È possibile ottenere i nomi delle schede di rete correnti dalla visualizzazione del comando **Get-NetAdapter**.

	$corpnetAdapterName="<Name of the adapter attached to the Corpnet subnet>"
	$internetAdapterName="<Name of the adapter attached to the Internet>"
	[Ipaddress]$publicIP="<Your public IP address>"
	$publicIPpreflength=<Prefix length of your public IP address>
	[IPAddress]$publicDG="<Your ISP default gateway>"
	[IPAddress]$publicDNS="<Your ISP DNS server(s)>"
	Rename-NetAdapter -Name $corpnetAdapterName -NewName Corpnet
	Rename-NetAdapter -Name $internetAdapterName -NewName Internet
	New-NetIPAddress -InterfaceAlias "Internet" -IPAddress $publicIP -PrefixLength $publicIPpreflength “DefaultGateway $publicDG
	Set-DnsClientServerAddress -InterfaceAlias Internet -ServerAddresses $publicDNS
	New-NetIPAddress -InterfaceAlias "Corpnet" -IPAddress 10.0.0.2 -AddressFamily IPv4 -PrefixLength 24
	Set-DnsClientServerAddress -InterfaceAlias "Corpnet" -ServerAddresses 10.0.0.1
	Set-DnsClient -InterfaceAlias "Corpnet" -ConnectionSpecificSuffix corp.contoso.com
	New-NetFirewallRule -DisplayName "Allow ICMPv4-Input" -Protocol ICMPv4
	New-NetFirewallRule -DisplayName "Allow ICMPv4-Output" -Protocol ICMPv4 -Direction Outbound
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_msclient
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_server
	ping dc1.corp.contoso.com

Per l'ultimo comando, verificare che siano presenti quattro risposte dall'indirizzo IP 10.0.0.1.

Questa è la configurazione corrente.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph2.png)

## Fase 3: Creare la rete virtuale di Azure cross-premise.

Avviare un prompt di Azure PowerShell.

> [AZURE.NOTE] Il set di comandi seguente utilizza Azure PowerShell 1.0 e versioni successive. Per altre informazioni, vedere [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Accedere all'account.

	Login-AzureRMAccount

Ottenere il nome della sottoscrizione usando il comando seguente.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Impostare la sottoscrizione di Azure. Usare la stessa sottoscrizione usata per la compilazione della configurazione di base. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con i nomi corretti.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Creare quindi un nuovo gruppo di risorse per l'ambiente di testing ibrido.

Per determinare un nome di gruppo di risorse univoco, usare il comando seguente per elencare i gruppi di risorse esistenti.

	Get-AzureRMResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Creare il nuovo gruppo di risorse con questi comandi.

	$rgName="<resource group name>"
	$locName="<an Azure location, such as West US>"
	New-AzureRMResourceGroup -Name $rgName -Location $locName

Le macchine virtuali basate su Gestione risorse richiedono un account di archiviazione basato su Gestione risorse. Per ogni account di archiviazione è necessario selezionare un nome univoco globale che contenga solo lettere minuscole e numeri. È possibile usare il comando seguente per elencare gli account di archiviazione esistenti.

	Get-AzureRMStorageAccount | Sort Name | Select Name

Creare un nuovo account di archiviazione con i seguenti comandi.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<unique storage account name >"
	New-AzureRMStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

Si crea quindi la rete virtuale di Azure che ospiterà l'ambiente cloud ibrido e la si protegge con un gruppo di sicurezza di rete.

	$locShortName="<the location of your new resource group in lowercase with spaces removed, example: westus>"
	$gwSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.255.248/29"
	$vmSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "TestSubnet" -AddressPrefix "192.168.0.0/24"
	New-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName -Location $locName -AddressPrefix "192.168.0.0/16" -Subnet $gwSubnet,$vmSubnet -DNSServer "10.0.0.1"
	$rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
	New-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
	$vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name "TestVNET"
	$nsg=Get-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName
	Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name TestSubnet -AddressPrefix "192.168.0.0/24" -NetworkSecurityGroup $nsg

A questo punto, usare i comandi seguenti per creare i gateway per la connessione VPN da sito a sito. Sarà necessario l'indirizzo IP pubblico dell'interfaccia Internet di RRAS1 della fase 2.

	$localGatewayIP="<the public IP address assigned to the Internet interface of RRAS1>"
	$vnet=Get-AzureRMVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
	
	# Attach a virtual network gateway to a public IP address and the gateway subnet
	$publicGatewayVipName="HybCloudPublicIPAddress"
	$vnetGatewayIpConfigName="HybCloudPublicIPConfig"
	New-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$publicGatewayVip=Get-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName
	$vnetGatewayIpConfig=New-AzureRMVirtualNetworkGatewayIpConfig -Name $vnetGatewayIpConfigName -PublicIpAddressId $publicGatewayVip.Id -SubnetId $vnet.Subnets[0].Id
	
	# Create the Azure gateway
	$vnetGatewayName="HybCloudAzureGateway"
	$vnetGateway=New-AzureRMVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $rgName -Location $locName -GatewayType Vpn -VpnType RouteBased -IpConfigurations $vnetGatewayIpConfig
	
	# Create the gateway for the local network
	$localGatewayName="HybCloudLocalNetGateway"
	$localNetworkPrefix="10.0.0.0/8"
	$localGateway=New-AzureRMLocalNetworkGateway -Name $localGatewayName -ResourceGroupName $rgName -Location $locName -GatewayIpAddress $localGatewayIP -AddressPrefix $localNetworkPrefix

Tenere presente che la creazione di nuovi gateway può richiedere più di 20 minuti.

Usare quindi il comando seguente per determinare l'indirizzo IP pubblico del gateway VPN di Azure per la rete virtuale.

	Get-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName

Prendere nota dell'indirizzo IP nel campo **IPAddress** dello schermo. Sarà necessario per la fase 4.

Richiedere quindi all'amministratore di rete o della sicurezza una chiave casuale, crittograficamente complessa, a 32 caratteri, precondivisa. In alternativa, usare le informazioni fornite nell'articolo [Create a random string for an IPsec preshared key](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx) (Creare una stringa casuale per una chiave precondivisa IPsec) per ottenere una chiave precondivisa.

Usare i comandi seguenti per creare la connessione VPN da sito a sito in Azure.

	# Define the Azure virtual network VPN connection
	$vnetConnectionKey="<32-character pre-shared key>"
	$vnetConnectionName="HybCloudS2SConnection"
	$vnetConnection=New-AzureRMVirtualNetworkGatewayConnection -Name $vnetConnectionName -ResourceGroupName $rgName -Location $locName -ConnectionType IPsec -SharedKey $vnetConnectionKey -VirtualNetworkGateway1 $vnetGateway -LocalNetworkGateway2 $localGateway

Questa è la configurazione corrente.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph3.png)
 
## Fase 4: Creare la connessione VPN da sito a sito

Configurare prima di tutto RRAS1 con il servizio Routing e Accesso remoto in modo che agisca come dispositivo VPN per la subnet Corpnet. Accedere a RRAS1 come amministratore locale ed eseguire i seguenti comandi al prompt dei comandi di Windows PowerShell.

	Import-Module ServerManager
	Install-WindowsFeature RemoteAccess -IncludeManagementTools
	Add-WindowsFeature -name Routing -IncludeManagementTools

Configurare quindi RRAS1 per ricevere la connessione VPN da sito a sito dal gateway VPN di Azure. Riavviare RRAS1, accedere come amministratore locale e attivare i seguenti comandi al prompt dei comandi di Windows PowerShell. È necessario fornire l'indirizzo IP pubblico del gateway VPN di Azure e il valore della chiave precondivisa della fase 3.

	$PresharedKey="<32-character pre-shared key>"
	Import-Module RemoteAccess
	Install-RemoteAccess -VpnType VpnS2S
	Add-VpnS2SInterface -Protocol IKEv2 -AuthenticationMethod PSKOnly -Persistent -NumberOfTries 3 -ResponderAuthenticationMethod PSKOnly -Name S2StoTestVNET -Destination "<public IP address of the Azure VPN gateway>" -IPv4Subnet @("192.168.0.0/24:100") -SharedSecret $PresharedKey
	Set-VpnServerIPsecConfiguration -EncryptionType MaximumEncryption
	Set-VpnServerIPsecConfiguration -SADataSizeForRenegotiationKilobytes 33553408
	New-ItemProperty -Path HKLM:\System\CurrentControlSet\Services\RemoteAccess\Parameters\IKEV2 -Name SkipConfigPayload -PropertyType DWord -Value 1
	Restart-Service RemoteAccess

Attendere alcuni minuti mentre viene stabilita la connessione tra RRAS1 e il gateway VPN di Azure.

Configurare quindi RRAS1 per supportare il traffico tradotto in percorsi Internet. In RRAS1:

1.	Nella schermata iniziale digitare**rras**, quindi fare clic su **Routing e Accesso remoto**. 
2.	Nell'albero della console, aprire il nome del server, quindi fare clic su **IPv4**.
3.	Fare doppio clic su **Generale**, quindi fare clic su **Nuovo protocollo di Routing**.
4.	Fare clic su **NAT**, quindi su **OK**.
5.	Nell'albero della console fare clic con il pulsante destro del mouse su **NAT**, fare clic su **Nuova interfaccia**, quindi su **Corpnet** e infine due volte su **OK**.
6.	Fare clic con il pulsante destro del mouse su **NAT**, scegliere **Nuova interfaccia**, fare clic su **Internet**, quindi su **OK**.
7.	Nella scheda **NAT** fare clic su **Interfaccia pubblica connessa a Internet**, selezionare **Abilita NAT su questa interfaccia**, quindi fare clic su **OK**.

Configurare DC1, APP1 e CLIENT1 per usare RRAS1 come gateway predefinito.
 
In DC1 eseguire questi comandi in un prompt dei comandi di Windows PowerShell a livello di amministratore.

	New-NetRoute -DestinationPrefix "0.0.0.0/0" -InterfaceAlias "Ethernet" -NextHop 10.0.0.2
	Set-DhcpServerv4OptionValue -Router 10.0.0.2

Se il nome dell'interfaccia non è Ethernet, usare il comando **Get-NetAdapter** per determinare il nome dell'interfaccia.

In APP1 eseguire questo comando in un prompt dei comandi di Windows PowerShell a livello di amministratore.

	New-NetRoute -DestinationPrefix "0.0.0.0/0" -InterfaceAlias "Ethernet" -NextHop 10.0.0.2

In CLIENT1 eseguire questo comando in un prompt dei comandi di Windows PowerShell a livello di amministratore.

	ipconfig /renew

Questa è la configurazione corrente.
 

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph4.png)


## Fase 5: configurare DC2.

Innanzitutto, creare una macchina virtuale di Azure per DC2 con questi comandi al prompt dei comandi di Azure PowerShell nel computer locale.

	$rgName="<your resource group name>"
	$locName="<your Azure location, such as West US>"
	$saName="<your storage account name>"
	
	$vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
	$pip=New-AzureRMPublicIpAddress -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip -PrivateIpAddress 192.168.0.4
	$vm=New-AzureRMVMConfig -VMName DC2 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestVNET-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DC2 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name DC2-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Usare quindi il portale di Azure per connettersi alla nuova macchina virtuale DC2 con le credenziali dell'account amministratore locale.

Configurare quindi una regola di Windows Firewall per permettere il traffico per il test della connettività di base. Da un prompt dei comandi di Windows PowerShell a livello di amministratore in DC2 eseguire:

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Il comando ping dovrebbe restituire quattro risposte dall'indirizzo IP 10.0.0.1. Se si sta usando la *configurazione del cloud ibrido simulato*, verranno visualizzate quattro risposte corrette dall'indirizzo IP 10.0.0.4. Questo è un test del traffico per la connessione VPN da sito a sito o da rete virtuale a rete virtuale.

Quindi, aggiungere un altro disco dati come nuovo volume con la lettera di unità F:.

1.	Nel riquadro sinistro di Server Manager fare clic su **Servizi file e archiviazione**, quindi scegliere **Dischi**.
2.	Nel riquadro del contenuto nel gruppo **Dischi** fare clic su **disco 2** (con la **Partizione** impostata su **Sconosciuto**).
3.	Fare clic su **Attività**, quindi su **Nuovo volume**.
4.	Nella pagina Operazioni preliminari della creazione guidata nuovo volume fare clic su **Avanti**.
5.	Nella pagina Selezionare il server e il disco fare clic su **Disco 2**, quindi fare clic su **Avanti**. Quando richiesto, fare clic su **OK**.
6.	Nella pagina Specifica dimensioni del volume fare clic su **Avanti**.
7.	Nella pagina Assegnare a una lettera di unità o cartella fare clic su **Avanti**.
8.	Nella pagina Selezionare le impostazioni del file system fare clic su **Avanti**.
9.	Nella pagina Conferma selezioni, fare clic su **Crea**.
10.	Al termine, fare clic su **Chiudi**.

Configurare quindi DC2 come controller di dominio di replica per il dominio corp.contoso.com. Eseguire questi comandi dal prompt dei comandi di Windows PowerShell in DC2:

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

Si noti che viene richiesto di specificare sia la password per CORP\\User1 che la password per la Modalità ripristino servizi directory (DSRM, Directory Services Restore Mode) e quindi di riavviare DC2.

Quando la rete virtuale TestVNET è stata associata a un server DNS specifico (DC2), sarà necessario configurare la rete virtuale TestVNET per l'uso di tale server DNS.

1.	Nel riquadro sinistro del portale di Azure fare clic sull'icona delle reti virtuali e quindi su **TestVNET**.
2.	Nella scheda **Impostazioni** fare clic su **Server DNS**.
3.	In **Server DNS primario** digitare **192.168.0.4** per sostituire 10.0.0.4.
4.	Fare clic su Save.

Riavviare quindi DC2 in modo che usi la nuova configurazione del server DNS con questi comandi al prompt dei comandi di Azure PowerShell.

	Stop-AzureRmVM -ResourceGroupName $rgName -Name DC2
	Start-AzureRmVM -ResourceGroupName $rgName -Name DC2

Questa è la configurazione corrente.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph5.png)

L'ambiente cloud ibrido è ora pronto per il testing.
 
## Passaggi successivi

- Impostare una [farm Intranet di SharePoint](virtual-machines-windows-ps-hybrid-cloud-test-env-sp.md), un'[applicazione LOB basata sul Web](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md) o un [server di sincronizzazione della directory di Office 365 (DirSync)](virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync.md) in questo ambiente.

<!---HONumber=AcomDC_0601_2016-->