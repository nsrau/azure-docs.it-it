<properties
   pageTitle="Creare un servizio di bilanciamento del carico interno usando PowerShell nel modello di distribuzione classica | Microsoft Azure"
   description="Informazioni su come creare un servizio di bilanciamento del carico interno usando PowerShell nel modello di distribuzione classica"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="sewhee" />  

# Introduzione alla creazione di un servizio di bilanciamento del carico interno (classico) tramite PowerShell

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] Informazioni su come [eseguire questa procedura con il modello di Resource Manager](load-balancer-get-started-ilb-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


## Creare un set con servizio di bilanciamento del carico interno per le macchine virtuali

Per creare un set con servizio di bilanciamento del carico e i server che invieranno il traffico a esso, è necessario eseguire le operazioni seguenti:

1. Creare un'istanza della funzionalità di bilanciamento del carico interno che sarà l'endpoint del traffico in ingresso da configurare con carico bilanciato tra i server di un set con carico bilanciato.

1. Aggiungere gli endpoint corrispondenti alle macchine virtuali che riceveranno il traffico in ingresso.

1. Configurare i server che invieranno il traffico con carico bilanciato all'indirizzo IP virtuale (indirizzo VIP) dell'istanza del bilanciamento del carico interno.


### Passaggio 1: Creare un'istanza del bilanciamento del carico interno

Per un servizio cloud esistente o un servizio cloud distribuito in una rete virtuale dell'area, è possibile creare un'istanza del bilanciamento del carico interno con i comandi di Windows PowerShell seguenti:

	$svc="<Cloud Service Name>"
	$ilb="<Name of your ILB instance>"
	$subnet="<Name of the subnet within your virtual network>"
	$IP="<The IPv4 address to use on the subnet-optional>"

	Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP


Si noti che questo uso del cmdlet di Windows PowerShell [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx) richiede il set di parametri DefaultProbe. Per altre informazioni sui set di parametri aggiuntivi, vedere [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx).

### Passaggio 2: Aggiungere endpoint all'istanza del bilanciamento del carico interno

Di seguito è fornito un esempio:

	$svc="mytestcloud"
	$vmname="DB1"
	$epname="TCP-1433-1433"
	$lbsetname="lbset"
	$prot="tcp"
	$locport=1433
	$pubport=1433
	$ilb="ilbset"
	Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -Lbset $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM


### Passaggio 3: Configurare i server per l'invio del traffico al nuovo endpoint del bilanciamento del carico interno

È necessario configurare i server il cui traffico verrà configurato con carico bilanciato per l'uso del nuovo indirizzo IP (indirizzo VIP) dell'istanza del bilanciamento del carico interno. Si tratta dell'indirizzo su cui è in ascolto l'istanza del bilanciamento del carico interno. Nella maggior parte dei casi, è sufficiente aggiungere o modificare un record DNS per l'indirizzo VIP dell'istanza del bilanciamento del carico interno.

Se l'indirizzo IP è stato specificato durante la creazione dell'istanza del bilanciamento del carico interno, si ha già l'indirizzo VIP. In caso contrario, è possibile visualizzare l'indirizzo VIP eseguendo i comandi seguenti:

	$svc="<Cloud Service Name>"
	Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer



Per usare questi comandi, inserire i valori e rimuovere < and >. Di seguito è fornito un esempio:

	$svc="mytestcloud"
	Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer


Dalla visualizzazione del comando Get-AzureInternalLoadBalancer prendere nota dell'indirizzo IP e apportare le modifiche necessarie ai server o ai record DNS per assicurarsi che il traffico venga inviato all'indirizzo VIP.

>[AZURE.NOTE] La piattaforma Microsoft Azure usa un indirizzo IPv4 statico e instradabile pubblicamente per un'ampia gamma di scenari di amministrazione. L'indirizzo IP è 168.63.129.16. Questo indirizzo IP non deve essere bloccato da alcun firewall, perché potrebbe causare un comportamento imprevisto. Per quanto riguarda il bilanciamento del carico interno di Azure, questo indirizzo IP viene usato da probe di monitoraggio del servizio di bilanciamento del carico per determinare lo stato di integrità delle macchine virtuali in un set con carico bilanciato. Se si usa un gruppo di sicurezza di rete per limitare il traffico alle macchine virtuali di Azure in un set con carico bilanciato internamente o lo si applica a una subnet di rete virtuale, assicurarsi di aggiungere una regola di sicurezza di rete per consentire il traffico dall'indirizzo 168.63.129.16.


## Esempio di bilanciamento del carico interno

Per una descrizione del processo end-to-end di creazione di un set con carico bilanciato per due configurazioni di esempio, vedere le sezioni seguenti.

### Applicazione multilivello con connessione Internet

Si desidera fornire un servizio di database con carico bilanciato per un gruppo di server Web con connessione Internet. Entrambi i set di server sono ospitati in un unico servizio cloud di Azure. Il traffico dei server Web verso la porta TCP 1433 deve essere distribuito tra due macchine virtuali nel livello database. La figura 1 illustra la configurazione.

![Set con carico bilanciato interno per il livello database](./media/load-balancer-internal-getstarted/IC736321.png)


La configurazione è costituita dai seguenti elementi:

- Il servizio cloud esistente che ospita le macchine virtuali è denominato mytestcloud.

- I due server di database esistenti sono denominati DB1, DB2.

- I server Web nel livello Web si connettono ai server di database nel livello database usando l’indirizzo IP privato. Un'altra opzione consiste nell'utilizzare il proprio DNS per la rete virtuale e registrare manualmente un record A per il set di bilanciamento del carico interno.

I comandi seguenti configurano una nuova istanza del bilanciamento del carico interno denominata **ILBset** e aggiungono endpoint alle macchine virtuali corrispondenti ai due server di database:

	$svc="mytestcloud"
	$ilb="ilbset"
	Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb
	$prot="tcp"
	$locport=1433
	$pubport=1433
	$epname="TCP-1433-1433"
	$lbsetname="lbset"
	$vmname="DB1"
	Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

	$epname="TCP-1433-1433-2"
	$vmname="DB2"
	Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM


## Rimuovere una configurazione del bilanciamento del carico interno

Per rimuovere una macchina virtuale come endpoint da un'istanza del servizio di bilanciamento del carico interno, usare i comandi seguenti:

	$svc="<Cloud service name>"
	$vmname="<Name of the VM>"
	$epname="<Name of the endpoint>"
	Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM

Per usare questi comandi, inserire i valori rimuovendo < and >.

Di seguito è fornito un esempio:

	$svc="mytestcloud"
	$vmname="DB1"
	$epname="TCP-1433-1433"
	Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM

Per rimuovere un'istanza del servizio di bilanciamento del carico interno da un servizio cloud, usare i comandi seguenti:

	$svc="<Cloud service name>"
	Remove-AzureInternalLoadBalancer -ServiceName $svc

Per usare questi comandi, inserire il valore e rimuovere < and >.

Di seguito è fornito un esempio:

	$svc="mytestcloud"
	Remove-AzureInternalLoadBalancer -ServiceName $svc



## Altre informazioni sui cmdlet per servizio di bilanciamento del carico interno


Per ottenere altre informazioni sui cmdlet per il bilanciamento del carico interno, eseguire i comandi seguenti da un prompt di Windows PowerShell:

- Get-help New-AzureInternalLoadBalancerConfig -full

- Get-help Add-AzureInternalLoadBalancer -full

- Get-help Get-AzureInternalLoadbalancer -full

- Get-help Remove-AzureInternalLoadBalancer -full

## Passaggi successivi

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico utilizzando l’affinità dell’IP di origine](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0824_2016-->