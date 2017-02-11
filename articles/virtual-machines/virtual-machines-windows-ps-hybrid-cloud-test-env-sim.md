---
title: Ambiente di test cloud ibrido simulato | Microsoft Docs
description: Creare un ambiente cloud ibrido simulato per test per professionisti IT o test di sviluppo, usando due reti virtuali di Azure e una connessione da rete virtuale a rete virtuale.
services: virtual-machines-windows
documentationcenter: 
author: JoeDavies-MSFT
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: ca5bf294-8172-44a9-8fed-d6f38d345364
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: josephd
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 1322af70f643bcc191e3d69fe4a174adc9cd7e8d


---
# <a name="set-up-a-simulated-hybrid-cloud-environment-for-testing"></a>Configurazione di un ambiente cloud ibrido simulato per l'esecuzione di test
Questo articolo è una guida alla creazione di un ambiente cloud ibrido simulato con Microsoft Azure che usa due reti virtuali di Azure separate. Di seguito è riportata la configurazione risultante.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)

Permette di simulare un ambiente di produzione cloud ibrido e include:

* Una rete locale semplificata e simulata ospitata in una rete virtuale di Azure (la rete virtuale TestLab).
* Una rete virtuale cross-premise simulata ospitata in Azure (TestVNET).
* Una connessione da rete virtuale a rete virtuale tra le due reti virtuali.
* Un controller di dominio secondario nella rete virtuale TestVNET.

Questa configurazione fornisce una base e un punto di partenza comune da cui è possibile:

* Sviluppare e testare applicazioni in un ambiente cloud ibrido simulato.
* Creare configurazioni di test di computer, alcune nella rete virtuale TestLab e altre nella rete virtuale TestVNET, per simulare carichi di lavoro IT basati sul cloud.

La configurazione di questo ambiente di test cloud ibrido comprende quattro fasi principali:

1. Configurare la rete virtuale TestLab.
2. Creare la rete virtuale cross-premise.
3. Creare la connessione VPN da rete virtuale a rete virtuale.
4. Configurare DC2. 

Questa configurazione richiede una sottoscrizione di Azure. Se si ha un abbonamento a MSDN o una sottoscrizione di Visual Studio, vedere [Credito Azure mensile per sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

> [!NOTE]
> Le macchine virtuali e i gateway di rete virtuale in Azure generano addebiti monetari in caso di esecuzione. Il costo viene addebitato sulla base dell'abbonamento a MSDN o della sottoscrizione a pagamento. Un gateway VPN di Azure viene implementato come set di due macchine virtuali di Azure. Per ridurre al minimo i costi, creare l'ambiente di test ed eseguire il test e la dimostrazione necessari più rapidamente possibile.
> 
> 

## <a name="phase-1-configure-the-testlab-virtual-network"></a>Fase 1: Configurare la rete virtuale TestLab
Usare le istruzioni nell'argomento [Ambiente di test della configurazione di base](https://technet.microsoft.com/library/mt771177.aspx) per configurare i computer DC1, APP1 e CLIENT1 nella rete virtuale di Azure denominata TestLab. 

Avviare un prompt di Azure PowerShell.

> [!NOTE]
> Il set di comandi seguente utilizza Azure PowerShell 1.0 e versioni successive.
> 
> 

Accedere al proprio account.

    Login-AzureRMAccount

Ottenere il nome della sottoscrizione usando il comando seguente.

    Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Impostare la sottoscrizione di Azure. Usare la stessa sottoscrizione usata per la compilazione della configurazione di base nella fase 1. Sostituire tutti gli elementi racchiusi tra virgolette, inclusi i caratteri < e >, con il nome corretto.

    $subscr="<subscription name>"
    Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Aggiungere una subnet del gateway alla rete virtuale TestLab della configurazione di base che verrà usata per ospitare il gateway di Azure.

    $rgName="<name of your resource group that you used for your TestLab virtual network>"
    $locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
    $vnet=Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name TestLab
    Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 10.255.255.248/29 -VirtualNetwork $vnet
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

Richiedere un indirizzo IP pubblico da assegnare al gateway per la rete virtuale TestLab.

    $gwpip=New-AzureRmPublicIpAddress -Name TestLab_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic

Creare il gateway.

    $vnet=Get-AzureRmVirtualNetwork -Name TestLab -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name TestLab_GWConfig -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 
    New-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

Tenere presente che la creazione di nuovi gateway può richiedere più di 20 minuti.

Dal portale di Azure nel computer locale connettersi a DC1 con le credenziali CORP\User1. Per configurare il dominio CORP in modo che i computer e gli utenti usino il controller di dominio locale per l'autenticazione, eseguire questi comandi da un prompt dei comandi di Windows PowerShell a livello di amministratore in DC1.

    New-ADReplicationSite -Name "TestLab" 
    New-ADReplicationSite -Name "TestVNET"
    New-ADReplicationSubnet -Name "10.0.0.0/8" -Site "TestLab"
    New-ADReplicationSubnet -Name "192.168.0.0/16" -Site "TestVNET"

Questa è la configurazione corrente.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph1.png)

## <a name="phase-2-create-the-testvnet-virtual-network"></a>Fase 2: Creare la rete virtuale TestVNET
Creare la rete virtuale TestVNET e proteggerla con un gruppo di sicurezza di rete.

    $rgName="<name of the resource group that you used for your TestLab virtual network>"
    $locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
    $locShortName="<Azure location name from $locName in all lowercase letters with spaces removed. Example:  westus>"
    $testSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "TestSubnet" -AddressPrefix 192.168.0.0/24
    $gatewaySubnet=New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 192.168.255.248/29
    New-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName -Location $locName -AddressPrefix 192.168.0.0/16 -Subnet $testSubnet,$gatewaySubnet –DNSServer 10.0.0.4
    $rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
    New-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
    $vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name TestVNET
    $nsg=Get-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName
    Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet" -AddressPrefix 192.168.0.0/24 -NetworkSecurityGroup $nsg

Richiedere un indirizzo IP pubblico da allocare al gateway per la rete virtuale TestVNET e creare il gateway.

    $gwpip=New-AzureRmPublicIpAddress -Name TestVNET_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $vnet=Get-AzureRmVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name "TestVNET_GWConfig" -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
    New-AzureRmVirtualNetworkGateway -Name "TestVNET_GW" -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

Questa è la configurazione corrente.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph2.png)

## <a name="phase-3-create-the-vnet-to-vnet-connection"></a>Fase 3: Creare la connessione da rete virtuale a rete virtuale
Richiedere all'amministratore di rete o della sicurezza una chiave casuale, crittograficamente complessa, a 32 caratteri, precondivisa. In alternativa, usare le informazioni fornite nell'articolo [Create a random string for an IPsec preshared key](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx) (Creare una stringa casuale per una chiave precondivisa IPsec) per ottenere una chiave precondivisa.

Usare quindi i comandi seguenti per creare la connessione VPN da rete virtuale a rete virtuale. L'operazione potrebbe richiedere tempo.

    $sharedKey="<pre-shared key value>"
    $gwTestLab=Get-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName
    $gwTestVNET=Get-AzureRmVirtualNetworkGateway -Name TestVNET_GW -ResourceGroupName $rgName
    New-AzureRmVirtualNetworkGatewayConnection -Name TestLab_to_TestVNET -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestLab -VirtualNetworkGateway2 $gwTestVNET -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey
    New-AzureRmVirtualNetworkGatewayConnection -Name TestVNET_to_TestLab -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestVNET -VirtualNetworkGateway2 $gwTestLab -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey

Dopo alcuni minuti, la connessione dovrebbe stabilirsi.

Questa è la configurazione corrente.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph3.png)

## <a name="phase-4-configure-dc2"></a>Fase 4: Configurare DC2.
Creare prima di tutto una macchina virtuale per DC2. Eseguire questi comandi nel prompt dei comandi di Azure PowerShell nel computer locale.

    $rgName="<your resource group name>"
    $locName="<your Azure location, such as West US>"
    $saName="<the storage account name for the base configuration>"
    $vnet=Get-AzureRMVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
    $pip=New-AzureRMPublicIpAddress -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 192.168.0.4
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

Connettersi quindi alla nuova macchina virtuale DC2 dal portale di Azure.

Configurare quindi una regola di Windows Firewall per permettere il traffico per il test della connettività di base. Da un prompt dei comandi di Windows PowerShell a livello di amministratore in DC2 eseguire questi comandi.

    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc1.corp.contoso.com

Il comando ping dovrebbe restituire quattro risposte dall'indirizzo IP 10.0.0.4. Questo è un test del traffico nella connessione da rete virtuale a rete virtuale.

Aggiungere quindi il disco dati aggiuntivo in DC2 come nuovo volume con la lettera di unità F:.

1. Nel riquadro sinistro di Server Manager fare clic su **Servizi file e archiviazione**, quindi scegliere **Dischi**.
2. Nel riquadro del contenuto nel gruppo **Dischi** fare clic su **disco 2** (con la **Partizione** impostata su **Sconosciuto**).
3. Fare clic su **Attività**, quindi su **Nuovo volume**.
4. Nella pagina Operazioni preliminari della creazione guidata nuovo volume fare clic su **Avanti**.
5. Nella pagina Selezionare il server e il disco fare clic su **Disco 2**, quindi selezionare **Avanti**. Quando richiesto, fare clic su **OK**.
6. Nella pagina Specifica dimensioni del volume fare clic su **Avanti**.
7. Nella pagina Assegnare a una lettera di unità o cartella fare clic su **Avanti**.
8. Nella pagina Selezionare le impostazioni del file system fare clic su **Avanti**.
9. Nella pagina Conferma selezioni, fare clic su **Crea**.
10. Al termine, fare clic su **Chiudi**.

Configurare quindi DC2 come controller di dominio di replica per il dominio corp.contoso.com. Eseguire questi comandi dal prompt dei comandi di Windows PowerShell in DC2:

    Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
    Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

Si noti che viene richiesto di specificare sia la password per CORP\User1 che la password per la Modalità ripristino servizi directory (DSRM, Directory Services Restore Mode) e quindi di riavviare DC2.

Quando la rete virtuale TestVNET è stata associata a un server DNS specifico (DC2), sarà necessario configurare la rete virtuale TestVNET per l'uso di tale server DNS.

1. Nel riquadro sinistro del portale di Azure fare clic sull'icona delle reti virtuali e quindi su **TestVNET**.
2. Nella scheda **Impostazioni** fare clic su **Server DNS**.
3. In **Server DNS primario** digitare**192.168.0.4** per sostituire 10.0.0.4.
4. Fare clic su **Save**.

Questa è la configurazione corrente. 

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)

L'ambiente cloud ibrido simulato è ora pronto per il testing.

## <a name="next-step"></a>Passaggio successivo
* Configurare un'[applicazione line-of-business basata sul Web](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) in questo ambiente.




<!--HONumber=Nov16_HO3-->


