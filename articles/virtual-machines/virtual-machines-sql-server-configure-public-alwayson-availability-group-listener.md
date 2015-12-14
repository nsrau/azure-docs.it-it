<properties 
	pageTitle="Configurare un listener esterno per i gruppi di disponibilità AlwaysOn | Microsoft Azure"
	description="Questa esercitazione illustra in modo dettagliato la procedura per la creazione di un listener del gruppo di disponibilità AlwaysOn in Azure accessibile esternamente mediante l'indirizzo IP virtuale pubblico del servizio cloud associato."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management" />
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="11/13/2015"
	ms.author="jroth" />

# Configurare un listener esterno per i gruppi di disponibilità AlwaysOn in Azure

> [AZURE.SELECTOR]
- [Internal Listener](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [External Listener](virtual-machines-sql-server-configure-public-alwayson-availability-group-listener.md)

Questo argomento illustra come configurare un listener per un gruppo di disponibilità AlwaysOn accessibile esternamente su Internet. Ciò è possibile tramite l'associazione dell'indirizzo **IP virtuale pubblico** del servizio cloud con il listener.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.
 

Il gruppo di disponibilità può contenere repliche solo locali, solo di Azure oppure sia locali che di Azure per le configurazioni ibride. Le repliche di Azure possono trovarsi nella stessa area o in più aree grazie a più reti virtuali (VNet). I passaggi seguenti presuppongono che sia già stato [configurato un gruppo di disponibilità](virtual-machines-sql-server-alwayson-availability-groups-gui.md) ma che non sia stato configurato un listener.

## Linee guida e limitazioni per listener esterni

Occorre notare le linee guida seguenti relative al listener del gruppo di disponibilità in Azure quando si esegue la distribuzione usando l'indirizzo IP virtuale pubblico del servizio cloud:

- Il listener del gruppo di disponibilità è supportato su Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.

- L'applicazione client deve trovarsi in un servizio cloud diverso rispetto a quello che contiene le VM del gruppo di disponibilità. Azure non supporta Direct Server Return con client e server residenti nello stesso servizio cloud.

- Per impostazione predefinita, la procedura descritta in questo articolo descrive come configurare un listener per usare l'indirizzo IP virtuale (VIP) del servizio cloud. Tuttavia, è possibile riservare e creare più indirizzi VIP per il servizio cloud. In questo modo è possibile usare la procedura di questo articolo per creare più listener, ciascuno di essi associato a un indirizzo IP virtuale diverso. Per informazioni su come creare più indirizzi VIP, vedere [più indirizzi IP virtuali per il servizio cloud](load-balancer-multivip.md).

- Se si sta creando un listener per un ambiente ibrido, la rete locale deve disporre di connettività a Internet pubblico oltre che al VPN da sito a sito con la rete virtuale di Azure. Quando nella subnet di Azure, il listener del gruppo di disponibilità è raggiungibile solo tramite indirizzo IP pubblico del rispettivo servizio cloud.

- Non è supportata la creazione di un listener esterno nello stesso servizio cloud in cui è presente anche un listener interno utilizzando il servizio di bilanciamento del carico interno (ILB).

## Determinare l'accessibilità del listener

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Questo articolo illustra la creazione di un listener che usa il **bilanciamento del carico esterno**. Se si vuole un listener privato per la rete virtuale, vedere la versione di questo articolo che illustra la procedura per la configurazione di un [listener con ILB](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)

## Creare endpoint VM con bilanciamento del carico con Direct Server Return

Il servizio di bilanciamento del carico esterno usa l'indirizzo IP virtuale pubblico del servizio cloud che ospita le VM. Non è quindi necessario creare o configurare il servizio di bilanciamento del carico in questo caso.

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. Copiare lo script di PowerShell seguente in un editor di testi e impostare i valori variabili in base all'ambiente. Sono stati forniti valori predefiniti per alcuni parametri. Si noti che se il gruppo di disponibilità si estende tra diverse aree di Azure, è necessario eseguire lo script una volta in ogni data center per il servizio cloud e i nodi che risiedono nel data center.

		# Define variables
		$ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
		$AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
		
		# Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
		ForEach ($node in $AGNodes)
		{
		    Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
		}

1. Dopo aver impostato le variabili, copiare lo script dall'editor di testo nella sessione di Azure PowerShell per eseguirlo. Se nel prompt viene ancora visualizzato >>, digitare di nuovo ENTER per assicurarsi che l'esecuzione dello script sia stata avviata.

## Se necessario, verificare che KB2854082 sia installato.

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## Aprire le porte firewall nei nodi del gruppo di disponibilità in

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## Creare il listener del gruppo di disponibilità

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Per il servizio di bilanciamento del carico esterno, è necessario ottenere l'indirizzo IP virtuale pubblico del servizio cloud contenente le repliche. Accedere al portale di Azure classico. Passare al servizio cloud che contiene la macchina virtuale del gruppo di disponibilità. Aprire la visualizzazione **Dashboard**. 

3. Annotare l'indirizzo visualizzato sotto **Indirizzo IP virtuale pubblico (VIP)**. Se la soluzione interessa più reti virtuali, ripetere questo passaggio per ogni servizio cloud che contiene una macchina virtuale che ospita una replica.

4. Su una delle macchine virtuali copiare lo script di PowerShell seguente in un editor di testo e impostare le variabili sui valori annotati prima.

		# Define variables
		$ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
		$IPResourceName = "<IPResourceName>" # the IP Address resource name 
		$CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
		
		Import-Module FailoverClusters
		
		# If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code. 
		
		# Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
		# cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255


1. Dopo aver impostato le variabili, aprire una finestra con privilegi elevati di Windows PowerShell, quindi copiare lo script dall'editor di testo e incollarlo nella sessione di Azure PowerShell per eseguirlo. Se nel prompt viene ancora visualizzato >>, digitare di nuovo ENTER per assicurarsi che l'esecuzione dello script sia stata avviata.

1. Ripetere questo passaggio su ogni macchina virtuale. Questo script consente di configurare la risorsa Indirizzo IP con l'indirizzo IP del servizio cloud e di impostare altri parametri come la porta probe. Quando la risorsa Indirizzo IP viene portata online, può quindi rispondere al polling sulla porta probe dall'endpoint con carico bilanciato, creato in precedenza in questa esercitazione.

## Portare online il listener

[AZURE.INCLUDE [Portare online il listener](../../includes/virtual-machines-ag-listener-bring-online.md)]

## Operazioni di completamento della procedura

[AZURE.INCLUDE [Completamento della procedura](../../includes/virtual-machines-ag-listener-follow-up.md)]

## Testare il listener del gruppo di disponibilità (entro la stessa rete virtuale)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## Testare il listener del gruppo di disponibilità (su Internet)

Per accedere al listener dall'esterno della rete virtuale, è necessario usare il servizio di bilanciamento del carico esterno/pubblico (illustrato in questo argomento) invece del servizio di bilanciamento del carico interno, che è accessibile solo entro la stessa rete virtuale. Nella stringa di connessione specificare il nome del servizio cloud. Ad esempio, se è disponibile un servizio cloud con nome *mycloudservice*, l'istruzione sqlcmd sarà analoga alla seguente:

	sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

A differenza dell'esempio precedente, è necessario usare l'autenticazione SQL, perché il chiamante non può usare l'autenticazione di Windows su Internet. Per altre informazioni, vedere [Gruppo di disponibilità AlwaysOn nelle macchine virtuali di Azure: scenari di connettività client](http://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Quando si usa l'autenticazione SQL, assicurarsi di creare lo stesso account di accesso in entrambe le repliche. Per altre informazioni sulla risoluzione dei problemi degli account di accesso con gruppi di disponibilità, vedere il post di blog relativo a [come mappare gli account di accesso o usare un utente di database SQL contenuto per connettersi ad altre repliche ed eseguire il mapping ai database di disponibilità](http://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Se le repliche AlwaysOn si trovano in subnet diverse, i client devono specificare **MultisubnetFailover=True** nella stringa di connessione. Di conseguenza, vengono eseguiti tentativi di connessione paralleli alle repliche nelle diverse subnet. Notare che questo scenario include la distribuzione di un gruppo di disponibilità AlwaysOn tra più aree.

## Passaggi successivi

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]

<!---HONumber=AcomDC_1203_2015-->