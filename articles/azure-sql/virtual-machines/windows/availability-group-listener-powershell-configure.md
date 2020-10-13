---
title: Configurare i listener del gruppo di disponibilità e il servizio di bilanciamento del carico (PowerShell)
description: Configurare i listener del gruppo di disponibilità nel modello di Azure Resource Manager, usando un servizio di bilanciamento del carico interno con uno o più indirizzi IP.
services: virtual-machines
documentationcenter: na
author: MashaMSFT
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/06/2019
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 3a715538afba181a067e4cecd8c1941a76ae36d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91298854"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Configurare uno o più listener di gruppi di disponibilità AlwaysOn - Resource Manager

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo documento illustra come usare PowerShell per eseguire una delle attività seguenti:
- creare un servizio di bilanciamento del carico
- aggiungere indirizzi IP a un servizio di bilanciamento del carico esistente per i gruppi di disponibilità SQL Server.

Un listener del gruppo di disponibilità è un nome di rete virtuale al quale si connettono i client per l'accesso ai database. In macchine virtuali di Azure, un servizio di bilanciamento del carico include l'indirizzo IP per il listener. Il servizio di bilanciamento del carico indirizza il traffico all'istanza di SQL Server in ascolto nella porta probe. In genere, un gruppo di disponibilità usa un servizio di bilanciamento del carico interno. Un servizio di bilanciamento del carico interno di Azure può ospitare uno o più indirizzi IP. Ogni indirizzo IP usa una porta probe specifica. 

La possibilità di assegnare più indirizzi IP a un servizio di bilanciamento del carico interno è una novità di Azure ed è disponibile solo nel modello di Gestione risorse. Per completare questa attività, è necessario disporre di un gruppo di disponibilità SQL Server distribuito in macchine virtuali di Azure nel modello di Gestione risorse. Entrambe le macchine virtuali di SQL Server devono appartenere allo stesso set di disponibilità. È possibile usare il [modello Microsoft](availability-group-azure-marketplace-template-configure.md) per creare automaticamente il gruppo di disponibilità in Azure Resource Manager. Questo modello crea automaticamente il gruppo di disponibilità, che include il servizio di bilanciamento del carico interno. Se si preferisce, è possibile [configurare manualmente un gruppo di disponibilità AlwaysOn](availability-group-manually-configure-tutorial.md).

Per completare i passaggi descritti in questo articolo, è necessario che i gruppi di disponibilità siano già configurati.  

Gli argomenti correlati includono:

* [Configurare i gruppi di disponibilità AlwaysOn nelle VM di Azure (GUI)](availability-group-manually-configure-tutorial.md)   
* [Configurare una connessione da VNet a VNet tramite Azure Resource Manager e PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="verify-powershell-version"></a>Verificare la versione di PowerShell

Gli esempi di questo articolo sono stati testati usando la versione 5.4.1 del modulo di Azure PowerShell.

Verificare che il modulo di PowerShell sia 5.4.1 o versione successiva.

Vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="configure-the-windows-firewall"></a>Configurare Windows Firewall

Configurare Windows Firewall per consentire l'accesso a SQL Server. Le regole del firewall consentono le connessioni TCP alle porte usate dall'istanza di SQL Server e al probe di listener. Per informazioni dettagliate, vedere [Configurazione di Windows Firewall per l'accesso al Motore di database](https://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Creare una regola in entrata per la porta SQL Server e per la porta probe.

Se si limita l'accesso con un gruppo di sicurezza di rete di Azure, assicurarsi che le regole di autorizzazione includano gli indirizzi IP della macchina virtuale di SQL Server di back-end e gli indirizzi IP del bilanciamento del carico mobile per il listener AG e l'indirizzo IP principale del cluster, se disponibile.

## <a name="determine-the-load-balancer-sku-required"></a>Determinare lo SKU di Load Balancer necessario

Il servizio di [bilanciamento del carico di Azure](../../../load-balancer/load-balancer-overview.md) è disponibile in due SKU: Basic & standard. È consigliato l'uso di Load Balancer Standard. Se, tuttavia, le macchine virtuali sono in un set di disponibilità, è consentito l'uso di Load Balancer Basic. Se le macchine virtuali sono in una zona di disponibilità, è necessario l'uso di Load Balancer Standard. Load Balancer Standard richiede che tutti gli indirizzi IP delle macchine virtuali usino indirizzi IP standard.

Il [modello Microsoft](availability-group-azure-marketplace-template-configure.md) corrente per un gruppo di disponibilità usa un'istanza di Load Balancer Basic con indirizzi IP di base.

   > [!NOTE]
   > Se si usa un Load Balancer Standard e l'Archiviazione di Azure per il cloud di controllo, è necessario configurare un [endpoint di servizio](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network). 
   > 

Gli esempi in questo articolo si riferiscono a un'istanza di Load Balancer Standard. Negli esempi lo script include `-sku Standard`.

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe -sku Standard
```

Per creare un'istanza di Load Balancer Basic, rimuovere `-sku Standard` dalla riga che consente di creare le istanze di Load Balancer. Ad esempio:

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe
```

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Script di esempio: Creare un servizio di bilanciamento del carico interno con PowerShell

> [!NOTE]
> Se il gruppo di disponibilità è stato creato con il [modello Microsoft](availability-group-azure-marketplace-template-configure.md), il bilanciamento del carico interno è già stato creato.

Lo script di PowerShell seguente crea un servizio di bilanciamento del carico interno, configura le regole di bilanciamento del carico e imposta un indirizzo IP per il servizio di bilanciamento del carico. Per eseguire lo script, aprire Windows PowerShell ISE, quindi incollare lo script nel riquadro di script. Usare `Connect-AzAccount` per l'accesso a PowerShell. Se si dispone di più sottoscrizioni di Azure, usare `Select-AzSubscription` per impostare la sottoscrizione. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

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

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the front-end configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the back-end configuration

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($vm.NetworkProfile.NetworkInterfaces.Id.split('/') | select -last 1)
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzNetworkInterface -NetworkInterface $NIC
        start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="example-script-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a><a name="Add-IP"></a> Script di esempio: Aggiungere un indirizzo IP a un servizio di bilanciamento del carico esistente con PowerShell

Per usare più di un gruppo di disponibilità, aggiungere un altro indirizzo IP al bilanciamento del carico. Ogni indirizzo IP richiede la propria regola di bilanciamento del carico, la porta Probe e la porta anteriore.

La porta front-end è quella usata dalle applicazioni per connettersi all'istanza di SQL Server. Gli indirizzi IP per i diversi gruppi di disponibilità possono usare la stessa porta front-end.

> [!NOTE]
> Per i gruppi di disponibilità di SQL Server, ogni indirizzo IP richiede una porta probe specifica. Ad esempio, se un indirizzo IP su un servizio di bilanciamento del carico usa la porta probe 59999, nessun altro indirizzo IP in tale servizio di bilanciamento del carico può usare la porta probe 59999.

* Per informazioni sui limiti del servizio di bilanciamento del carico, vedere **IP front-end privato per ogni servizio di bilanciamento del carico** in [Limiti relativi alle reti - Azure Resource Manager](../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Per informazioni sui limiti dei gruppi di disponibilità, vedere [Restrizioni (gruppi di disponibilità)](https://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

Lo script seguente aggiunge un nuovo indirizzo IP a un servizio di bilanciamento del carico esistente. ILB usa la porta del listener per la porta front-end di bilanciamento del carico. Questa porta può essere la porta su cui SQL Server è in ascolto. Per le istanze predefinite di SQL Server, la porta è la numero 1433. La regola di bilanciamento del carico per un gruppo di disponibilità richiede un indirizzo IP mobile (Direct Server Return), quindi la porta back-end corrisponde alla porta front-end. Aggiornare le variabili per l'ambiente. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzLoadBalancer 

$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzLoadBalancer   
```

## <a name="configure-the-listener"></a>Configurare il listener

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>Impostare la porta del listener in SQL Server Management Studio

1. Avviare SQL Server Management Studio e connettersi alla replica primaria.

1. Passare a **Disponibilità elevata AlwaysOn** > **Gruppi di disponibilità** > **Listener gruppo di disponibilità**. 

1. Viene visualizzato il nome del listener creato in Gestione Cluster di Failover. Fare clic con il pulsante destro del mouse sul nome del listener e scegliere **Proprietà**.

1. Nella casella **porta** specificare il numero di porta per il listener del gruppo di disponibilità usando il $EndpointPort usato in precedenza (1433 è il valore predefinito), quindi fare clic su **OK**.

## <a name="test-the-connection-to-the-listener"></a>Testare la connessione al listener

Per testare la connessione:

1. Usare Remote Desktop Protocol (RDP) per connettersi a una SQL Server che si trova nella stessa rete virtuale, ma non è proprietaria della replica. Potrebbe trattarsi degli altri SQL Server nel cluster.

1. Usare l'utilità **sqlcmd** per testare la connessione. Lo script seguente, ad esempio, stabilisce una connessione **sqlcmd** alla replica primaria tramite il listener con l'autenticazione di Windows:
   
    ```
    sqlcmd -S <listenerName> -E
    ```
   
    Se il listener usa una porta diversa da quella predefinita (1433), specificare la porta nella stringa di connessione. Il seguente comando sqlcmd, ad esempio, si connette a un listener nella porta 1435: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

La connessione SQLCMD si connette automaticamente a qualsiasi istanza di SQL Server ospiti la replica primaria. 

> [!NOTE]
> Verificare che la porta specificata sia aperta nel firewall di entrambe le istanze di SQL Server. Per entrambi i server è necessaria una regola in ingresso per la porta TCP usata. Per altre informazioni, vedere [Aggiungere o modificare una regola del firewall](https://technet.microsoft.com/library/cc753558.aspx). 
> 

## <a name="guidelines-and-limitations"></a>Linee guida e limitazioni

Tenere presente le linee guida seguenti per il listener del gruppo di disponibilità in Azure con il servizio di bilanciamento del carico interno:

* Con un servizio di bilanciamento del carico interno è possibile accedere al listener solo dalla stessa rete virtuale.

* Se si limita l'accesso con un gruppo di sicurezza di rete di Azure, assicurarsi che le regole Consenti includano:
  - Back-end SQL Server indirizzi IP della VM
  - Indirizzi IP mobili del servizio di bilanciamento del carico per il listener del gruppo di disponibilità
  - Indirizzo IP principale del cluster, se applicabile.

* Creare un endpoint di servizio quando si usa un Load Balancer Standard con l'Archiviazione di Azure per il cloud di controllo. Per altre informazioni, vedere [Concedere l'accesso da una rete virtuale](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network).

## <a name="for-more-information"></a>Per ulteriori informazioni

Per altre informazioni, vedere [Configurare manualmente i gruppi di disponibilità AlwaysOn nelle VM di Azure](availability-group-manually-configure-tutorial.md).

## <a name="powershell-cmdlets"></a>Cmdlet PowerShell

Usare i cmdlet di PowerShell seguenti per creare un servizio di bilanciamento del carico interno per le macchine virtuali di Azure.

* [New-AzLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) crea un'istanza di Load Balancer. 
* [New-AzLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) crea una configurazione IP front-end per un'istanza di Load Balancer. 
* [New-AzLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) crea una regola di configurazione per un'istanza di Load Balancer. 
* [New-AzLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) crea una configurazione di pool di indirizzi back-end per un'istanza di Load Balancer. 
* [New-AzLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) crea una configurazione di probe per un'istanza di Load Balancer.
* [Remove-AzLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) rimuove un'istanza di Load Balancer da un gruppo di risorse di Azure.
