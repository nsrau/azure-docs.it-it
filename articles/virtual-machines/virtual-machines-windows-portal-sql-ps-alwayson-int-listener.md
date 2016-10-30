<properties
   pageTitle="Configurare i listener dei gruppi di disponibilità AlwaysOn | Microsoft Azure"
   description="Configurare i listener dei gruppi di disponibilità nel modello di Azure Resource Manager usando un servizio di bilanciamento del carico interno con uno o più indirizzi IP."
   services="virtual-machines"
   documentationCenter="na"
   authors="MikeRayMSFT"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="MikeRayMSFT"/>


# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manger"></a>Configurare uno o più listener nei gruppi di disponibilità AlwaysOn - Resource Manger 

Questo argomento illustra come eseguire due operazioni:

- Creare un servizio di bilanciamento del carico interno per gruppi di disponibilità di SQL Server usando i cmdlet PowerShell.

- Aggiungere altri indirizzi IP a un servizio di bilanciamento del carico per supportare più di un gruppo di disponibilità di SQL Server. 

> **Importante** Il supporto di più listener per i gruppi di disponibilità AlwaysOn nelle macchine virtuali di Azure è una funzionalità in anteprima ed è soggetta alle condizioni per l'anteprima previste nel contratto di licenza (ad esempio il Contratto Enterprise, il Contratto di Microsoft Azure o il Contratto di Sottoscrizione Microsoft Online), nonché alle [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](http://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

In SQL Server un listener del gruppo di disponibilità è un nome di rete virtuale a cui i client si connettono per accedere a un database nella replica primaria o secondaria. Nelle macchine virtuali di Azure, un servizio di bilanciamento del carico contiene l'indirizzo IP del listener. Il servizio di bilanciamento del carico indirizza il traffico all'istanza di SQL Server in ascolto nella porta probe. Nella maggior parte dei casi, un gruppo di disponibilità usa un servizio di bilanciamento del carico interno. Un servizio di bilanciamento del carico interno di Azure può ospitare uno o più indirizzi IP. Ogni indirizzo IP usa una porta probe specifica. In questo documento viene illustrato come usare PowerShell per creare un nuovo servizio di bilanciamento del carico o aggiungere indirizzi IP a un servizio di bilanciamento del carico esistente per i gruppi di disponibilità di SQL Server. 

La possibilità di assegnare più indirizzi IP a un servizio di bilanciamento del carico interno è una novità di Azure ed è disponibile solo nel modello di Resource Manager. Per completare questa attività, è necessario un gruppo di disponibilità di SQL Server distribuito su macchine virtuali di Azure nel modello di Resource Manager. Entrambe le macchine virtuali di SQL Server devono appartenere allo stesso set di disponibilità. È possibile usare il [modello Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) per creare automaticamente il gruppo di disponibilità in Azure Resource Manager. Questo modello crea automaticamente il gruppo di disponibilità, che include il servizio di bilanciamento del carico interno. Se si preferisce, è possibile [configurare manualmente un gruppo di disponibilità AlwaysOn](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Per questo argomento è necessario che i gruppi di disponibilità siano già configurati.  

Gli argomenti correlati includono:

 - [Configurare i gruppi di disponibilità AlwaysOn nelle VM di Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
 
 - [Configurare una connessione da VNet a VNet tramite Azure Resource Manager e PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-vm-powershell.md)]

## <a name="configure-the-windows-firewall"></a>Configurare Windows Firewall

Configurare Windows Firewall per consentire l'accesso a SQL Server. È necessario configurare il firewall in modo da consentire le connessioni TCP alle porte usate dall'istanza di SQL Server, oltre alle porte usate dalla porta probe del listener. Per informazioni dettagliate vedere [Configurazione di Windows Firewall per l'accesso al Motore di database](http://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Creare una regola in entrata per la porta SQL Server e per la porta probe.

## <a name="example-script:-create-an-internal-load-balancer-with-powershell"></a>Script di esempio: creare un servizio di bilanciamento del carico interno con PowerShell

> [AZURE.NOTE] Se è stato creato il gruppo di disponibilità con il [modello Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) , non è necessario completare questo passaggio. 

Il seguente script di PowerShell crea un servizio di bilanciamento del carico interno, configura le regole di bilanciamento del carico e imposta un indirizzo IP per il bilanciamento del carico. Per eseguire lo script, aprire Windows PowerShell ISE e copiare lo script nel riquadro Script. Usare `Login-AzureRMAccount` per l'accesso a PowerShell. Se si dispone di più sottoscrizioni di Azure, usare `Select-AzureRmSubscription ` per impostare la sottoscrizione. 

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the Front End configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the Back End configuration

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzureRMLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="example-script:-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a>Script di esempio: aggiungere un indirizzo IP a un servizio di bilanciamento del carico esistente con PowerShell

Per usare più di un gruppo di disponibilità, usare PowerShell per aggiungere un altro indirizzo IP a un servizio di bilanciamento del carico esistente. Ogni indirizzo IP richiede la sua regola di bilanciamento, la sua porta probe e la sua porta front-end.

La porta front-end è la porta usata dalle applicazioni per connettersi all'istanza di SQL Server. Gli indirizzi IP per i diversi gruppi di disponibilità possono usare la stessa porta front-end.

>[AZURE.NOTE] Per i gruppi di disponibilità di SQL Server, ogni indirizzo IP richiede una porta probe specifica. Ad esempio, se un indirizzo IP su un servizio di bilanciamento del carico usa la porta probe 59999, nessun altro indirizzo IP in tale servizio di bilanciamento del carico può usare la porta probe 59999. 

- Per informazioni sui limiti del servizio di bilanciamento del carico vedere **IP front-end privato per ogni servizio di bilanciamento del carico** in [Limiti relativi alle reti - Azure Resource Manager](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

- Per informazioni sui limiti dei gruppi di disponibilità vedere [Restrizioni (gruppi di disponibilità)](http://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

Lo script seguente aggiunge un nuovo indirizzo IP a un servizio di bilanciamento del carico esistente. Aggiornare le variabili per l'ambiente. Il servizio di bilanciamento del carico interno usa la porta del listener per la porta front-end di bilanciamento del carico. Questa porta può essere la porta su cui SQL Server è in ascolto. Per le istanze predefinite di SQL Server, è la porta 1433. La regola di bilanciamento del carico per un gruppo di disponibilità richiede un indirizzo IP mobile (Direct Server Return), quindi la porta back-end corrisponde alla porta front-end.

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzureRmLoadBalancer 

$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzureRmLoadBalancer   
``` 



## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configurare il cluster per usare l'indirizzo IP del servizio di bilanciamento del carico 

Il passaggio successivo consiste nel configurare il listener nel cluster e nel portare il listener online. A tale scopo, eseguire queste operazioni: 

1. Creare il listener del gruppo di disponibilità nel cluster di failover 

1. Portare online il listener

## <a name="1.-create-the-availability-group-listener-on-the-failover-cluster"></a>1. Creare il listener del gruppo di disponibilità nel cluster di failover

In questo passaggio, aggiungere un punto di accesso client al cluster di failover con Gestione cluster di failover, quindi usare PowerShell per configurare la risorsa cluster per l'ascolto sulla porta probe. 

- Usare RDP per connettersi alla macchina virtuale di Azure che ospita la replica primaria. 

- Aprire Gestione cluster di failover.

- Selezionare il nodo **Reti** e annotare il nome di rete del cluster. Usare questo nome nella variabile `$ClusterNetworkName` nello script di PowerShell.

- Espandere il nome di cluster, quindi fare clic su **Ruoli**.

- Nel pannello **Ruoli** fare clic con il pulsante destro del mouse sul nome del gruppo di disponibilità e quindi scegliere **Aggiungi risorsa** > **Punto di accesso Client**.

- Nella casella **Nome** creare un nome per il nuovo listener, fare clic su **Avanti** due volte, quindi fare clic su **Fine**. Non portare il listener o la risorsa in linea a questo punto.

 >[AZURE.NOTE] Il nome del nuovo listener è il nome della rete che le applicazioni useranno per connettersi ai database nel gruppo di disponibilità di SQL Server.

- Scegliere la scheda **Risorse** , quindi espandere il punto di accesso Client appena creato. Fare clic con il pulsante destro del mouse sulla risorsa IP e scegliere Proprietà. Prendere nota del nome dell'indirizzo IP. Si userà questo nome nella variabile `$IPResourceName` nello script di PowerShell.

- In **IP Address** click **Indirizzo IP statico** and set the static Indirizzo IP to the same address that you used when you set the load balancer Indirizzo IP on the Azure portal. 

- Disabilitare NetBIOS per questo indirizzo e fare clic su **OK**. Ripetere questo passaggio per ogni risorsa IP se la soluzione si estende su più reti virtuali di Azure. 

- Rendere la risorsa del gruppo di disponibilità di SQL Server dipendente dall'indirizzo IP. Fare clic con il pulsante destro del mouse sulla risorsa in Gestione cluster, presente nella scheda **Risorse** in **Altre risorse**. 

- Nel nodo del cluster che attualmente ospita la replica primaria, aprire un'istanza di PowerShell ISE con privilegi elevati e incollare i comandi seguenti in un nuovo script. Nella scheda **Dipendenze** fare clic sul nome del listener. 
        
    ```powershell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>

    Import-Module FailoverClusters
    
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```

- Aggiornare le variabili ed eseguire lo script di PowerShell per configurare l'indirizzo IP e la porta per il nuovo listener.

 >[AZURE.NOTE] Se le istanze di SQL Server sono in aree separate, è necessario eseguire lo script di PowerShell due volte. La prima volta usare il nome della rete di cluster, il nome della risorsa IP cluster e l'indirizzo IP del servizio di bilanciamento del carico del primo gruppo di risorse. La seconda volta usare il nome della rete di cluster, il nome della risorsa IP cluster e l'indirizzo IP del servizio di bilanciamento del carico del secondo gruppo di risorse.

Ora il cluster ha una risorsa listener del gruppo di disponibilità.

## <a name="2.-bring-the-listener-online"></a>2. Portare online il listener

Con la risorsa listener del gruppo di disponibilità configurata, è possibile portare online il listener in modo che le applicazioni possano connettersi ai database nel gruppo di disponibilità con il listener.

- Tornare a gestione Cluster di Failover. Espandere **Ruoli** ed evidenziare il gruppo di disponibilità. Nella scheda **Risorse**, fare clic con il pulsante destro del mouse sul nome del listener e scegliere **Proprietà**.

- Selezionare la scheda **Dipendenze** . Se sono presenti più risorse elencate, verificare che gli indirizzi abbiano le dipendenze OR, e non quelle AND. Fare clic su **OK**.

- Fare clic con il pulsante destro del mouse sul nome del listener e scegliere **Porta in linea**.


- Quando il listener è online, dalla scheda **Risorse** fare clic con il pulsante destro del mouse sul gruppo di disponibilità e scegliere **Proprietà**.

- Creare una dipendenza sulla risorsa nome del listener (non nome risorse indirizzo IP). Fare clic su **OK**.


- Avviare SQL Server Management Studio e connettersi alla replica primaria.


- Passare a **Disponibilità elevata AlwaysOn** | **Gruppi di disponibilità** | **Listener gruppo di disponibilità**. 


- Viene visualizzato il nome del listener creato in Gestione Cluster di Failover. Fare clic con il pulsante destro del mouse sul nome del listener e quindi su **Proprietà**.


- Nella casella **Porta** specificare il numero di porta per il listener del gruppo di disponibilità usando il valore di $EndpointPort usato in precedenza (l'impostazione predefinita era 1433), quindi fare clic su **OK**.

Ora si ha un gruppo di disponibilità di SQL Server nelle macchine virtuali di Azure in esecuzione in modalità Resource Manager. 

## <a name="test-the-connection-to-the-listener"></a>Testare la connessione al listener

Per testare la connessione:

1. Usare RDP per connettersi a un'istanza di SQL Server che si trova nella stessa rete virtuale, ma non è proprietaria della replica. Può trattarsi dell'altra istanza di SQL Server nel cluster.

1. Usare l'utilità **sqlcmd** per testare la connessione. Lo script seguente, ad esempio, stabilisce una connessione **sqlcmd** alla replica primaria tramite il listener con l'autenticazione di Windows:

        sqlmd -S <listenerName> -E

    Se il listener usa una porta diversa da quella predefinita (1433), specificare la porta nella stringa di connessione. Il seguente comando sqlcmd, ad esempio, si connette a un listener nella porta 1435: 
    
        sqlcmd -S <listenerName>,1435 -E

La connessione SQLCMD si connette automaticamente a qualsiasi istanza di SQL Server ospiti la replica primaria. 

>[AZURE.NOTE] Verificare che la porta specificata sia aperta nel firewall di entrambe le istanze di SQL Server. Per entrambi i server è necessaria una regola in ingresso per la porta TCP usata. Per altre informazioni, vedere [Aggiungere o modificare una regola del firewall](http://technet.microsoft.com/library/cc753558.aspx) . 

## <a name="guidelines-and-limitations"></a>Linee guida e limitazioni

Tenere presenti le linee guida seguenti per il listener del gruppo di disponibilità in Azure con il servizio di bilanciamento del carico interno:

- Con un servizio di bilanciamento del carico interno è possibile accedere al listener solo dalla stessa rete virtuale.

## <a name="for-more-information"></a>Per altre informazioni

Per altre informazioni, vedere [Configurare manualmente i gruppi di disponibilità AlwaysOn nelle VM di Azure](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

### <a name="powershell-cmdlets"></a>Cmdlet PowerShell

Usare i seguenti cmdlet PowerShell per creare un servizio di bilanciamento del carico interno per le macchine virtuali di Azure.

- `New-AzureRmLoadBalancer` crea un servizio di bilanciamento del carico. Per ulteriori informazioni vedere [New AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt619450.aspx) . 

- `New-AzureRMLoadBalancerFrontendIpConfig` crea una configurazione IP front-end per un servizio di bilanciamento del carico. Per ulteriori informazioni vedere [New-AzureRMLoadBalancerFrontendIpConfig](http://msdn.microsoft.com/library/mt603510.aspx) .

- `New-AzureRmLoadBalancerRuleConfig` crea la configurazione di una regola per un servizio di bilanciamento del carico. Per ulteriori informazioni vedere [New-AzureRmLoadBalancerRuleConfig](http://msdn.microsoft.com/library/mt619391.aspx) . 

- `New-AzureRMLoadBalancerBackendAddressPoolConfig` crea la configurazione di un pool di indirizzi back-end per un servizio di bilanciamento del carico. Per ulteriori informazioni vedere [New-AzureRmLoadBalancerBackendAddressPoolConfig](http://msdn.microsoft.com/library/mt603791.aspx) . 

- `New-AzureRmLoadBalancerProbeConfig` crea la configurazione di una porta probe per un servizio di bilanciamento del carico. Per ulteriori informazioni vedere [New-AzureRmLoadBalancerProbeConfig](http://msdn.microsoft.com/library/mt603847.aspx) .

Se è necessario rimuovere un servizio di bilanciamento del carico da un gruppo di risorse di Azure, usare `Remove-AzureRmLoadBalancer`. Per ulteriori informazioni vedere [Remove-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt603862.aspx).



<!--HONumber=Oct16_HO2-->


