<properties 
	pageTitle="Configurare un listener ILB per gruppi di disponibilità AlwaysOn | Microsoft Azure"
	description="Questa esercitazione utilizza le risorse create con il modello di distribuzione classica e crea un Listener del gruppo di disponibilità AlwaysOn in Azure utilizzando un servizio di bilanciamento del carico interno (ILB)."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" 
	tags="azure-service-management"/>
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="11/06/2015"
	ms.author="jroth" />

# Configurare un listener ILB per gruppi di disponibilità AlwaysOn in Azure

> [AZURE.SELECTOR]
- [Internal Listener](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [External Listener](virtual-machines-sql-server-configure-public-alwayson-availability-group-listener.md)

## Panoramica

Questo argomento illustra come configurare un listener per un gruppo di disponibilità AlwaysOn usando il **servizio di bilanciamento del carico interno**.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.
 

Il gruppo di disponibilità può contenere repliche solo locali, solo di Azure oppure sia locali che di Azure per le configurazioni ibride. Le repliche di Azure possono trovarsi nella stessa area o in più aree grazie a più reti virtuali (VNet). I passaggi seguenti presuppongono che sia già stato [configurato un gruppo di disponibilità](virtual-machines-sql-server-alwayson-availability-groups-gui.md) ma che non sia stato configurato un listener.

## Linee guida e limitazioni per listener interni
Tenere presente le linee guida seguenti per il listener del gruppo di disponibilità in Azure con ILB:

- Il listener del gruppo di disponibilità è supportato su Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.

- Per ogni servizio cloud è supportato un solo listener del gruppo di disponibilità interno, perché il listener è configurato solo per ILB, e c’è un solo ILB per ogni servizio cloud. Tuttavia, è possibile creare più listener esterni. Per altre informazioni, vedere l'articolo relativo alla [Configurazione di un listener esterno per gruppi di disponibilità AlwaysOn in Azure](virtual-machines-sql-server-configure-public-alwayson-availability-group-listener.md).

- Non è supportata la creazione di un listener interno nello stesso servizio cloud in cui è presente anche un listener esterno utilizzando l’indirizzo VIP pubblico del servizio cloud.

## Determinare l'accessibilità del listener

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Questo articolo illustra la creazione di un listener che usa il **Servizio di bilanciamento del carico interno (ILB)**. Se è necessario un listener pubblico/esterno, vedere la versione di questo articolo che illustra la procedura per la configurazione di un [listener esterno](virtual-machines-sql-server-configure-public-alwayson-availability-group-listener.md).

## Creare endpoint VM con bilanciamento del carico con Direct Server Return

Per ILB è necessario creare prima di tutto il servizio di bilanciamento del carico interno. Questa operazione viene eseguita nello script seguente.

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. È consigliabile assegnare un indirizzo IP statico a **ILB**. Esaminare prima di tutto la configurazione attuale della rete virtuale eseguendo il comando seguente:

		(Get-AzureVNetConfig).XMLConfiguration

1. Annotare il nome **Subnet** per la subnet contenente le VM che ospitano le repliche. Questo nome verrà usato nel parametro **$SubnetName** nello script.

1. Annotare quindi il valore **VirtualNetworkSite** e il valore iniziale **AddressPrefix** per la subnet contenente le VM che ospitano le repliche. Cercare un indirizzo IP disponibile passando entrambi i valori al comando **Test-AzureStaticVNetIP** ed esaminando il valore **AvailableAddresses**. Se, ad esempio, il nome della rete virtuale è *MyVNet* e l'inizio dell'intervallo di indirizzi della subnet corrisponde a *172.16.0.128*, il comando seguente elencherà gli indirizzi disponibili:

		(Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses

1. Scegliere uno degli indirizzi disponibili e usarlo nel parametro **$ILBStaticIP** dello script seguente.

3. Copiare lo script di PowerShell seguente in un editor di testi e impostare i valori variabili in base all'ambiente. Si noti che sono stati forniti valori predefiniti per alcuni parametri. Si noti che le distribuzioni esistenti che usano i gruppi di affinità non potranno aggiungere il Bilanciamento del carico interno. Per altre informazioni sui requisiti per il servizio di bilanciamento del carico interno, vedere [Bilanciamento del carico interno](../load-balancer/load-balancer-internal-overview.md). Se il gruppo di disponibilità si estende tra diverse aree di Azure, è necessario eseguire lo script una volta in ogni data center per il servizio cloud e i nodi che risiedono nel data center.

		# Define variables
		$ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
		$AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
		$SubnetName = "<MySubnetName>" # subnet name that the replicas use in the VNet
		$ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
		$ILBName = "AGListenerLB" # customize the ILB name or use this default value
		
		# Create the ILB
		Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP
		
		# Configure a load balanced endpoint for each node in $AGNodes using ILB
		ForEach ($node in $AGNodes)
		{
			Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM 
		}

1. Dopo aver impostato le variabili, copiare lo script dall'editor di testo nella sessione di Azure PowerShell per eseguirlo. Se nel prompt viene ancora visualizzato >>, digitare di nuovo ENTER per assicurarsi che l'esecuzione dello script sia stata avviata. Nota

>[AZURE.NOTE]Il portale di Azure classico non supporta attualmente il servizio di bilanciamento del carico interno, quindi il servizio di bilanciamento del carico interno o gli endpoint non saranno visualizzati nel portale di Azure classico. **Get-AzureEndpoint** restituisce tuttavia un indirizzo IP interno, se nel portale è in esecuzione il servizio di bilanciamento del carico. In caso contrario, restituirà Null.

## Se necessario, verificare che KB2854082 sia installato.

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## Aprire le porte firewall nei nodi del gruppo di disponibilità in

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## Creare il listener del gruppo di disponibilità

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Per il servizio di bilanciamento del carico interno è necessario usare l'indirizzo IP del servizio di bilanciamento del carico interno creato in precedenza. Usare lo script seguente per ottenere questo indirizzo IP in PowerShell.

		# Define variables
		$ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
		(Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

1. Su una delle macchine virtuali copiare lo script di PowerShell seguente in un editor di testo e impostare le variabili sui valori annotati prima.

		# Define variables
		$ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
		$IPResourceName = "<IPResourceName>" # the IP Address resource name 
		$ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)
		
		Import-Module FailoverClusters
		
		# If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code. 
		
		# Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
		# cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

1. Dopo aver impostato le variabili, aprire una finestra con privilegi elevati di Windows PowerShell, quindi copiare lo script dall'editor di testo e incollarlo nella sessione di Azure PowerShell per eseguirlo. Se nel prompt viene ancora visualizzato >>, digitare di nuovo ENTER per assicurarsi che l'esecuzione dello script sia stata avviata.

2. Ripetere questo passaggio su ogni macchina virtuale. Questo script consente di configurare la risorsa Indirizzo IP con l'indirizzo IP del servizio cloud e di impostare altri parametri come la porta probe. Quando la risorsa Indirizzo IP viene portata online, può quindi rispondere al polling sulla porta probe dall'endpoint con carico bilanciato, creato in precedenza in questa esercitazione.

## Portare online il listener

[AZURE.INCLUDE [Portare online il listener](../../includes/virtual-machines-ag-listener-bring-online.md)]

## Operazioni di completamento della procedura

[AZURE.INCLUDE [Completamento della procedura](../../includes/virtual-machines-ag-listener-follow-up.md)]

## Testare il listener del gruppo di disponibilità (entro la stessa rete virtuale)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## Passaggi successivi

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]

<!---HONumber=AcomDC_1203_2015-->