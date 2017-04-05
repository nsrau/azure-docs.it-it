---
title: 'Eseguire la migrazione di circuiti ExpressRoute e delle reti virtuali associate dalla distribuzione classica a Resource Manager: Azure | Microsoft Docs'
description: Questa pagina illustra come eseguire la migrazione di un circuito classico e delle reti virtuali associate in Resource Manager.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 08152836-23e7-42d1-9a56-8306b341cd91
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: ganesr;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 9a3ad5be7cc12f1c82eab4a8b3089276a434c234
ms.lasthandoff: 03/29/2017


---
# <a name="migrate-expressroute-circuits-and-associated-virtual-networks-from-the-classic-to-the-resource-manager-deployment-model"></a>Eseguire la migrazione di circuiti ExpressRoute e delle reti virtuali associate dalla distribuzione classica al modello di distribuzione Resource Manager

Questo articolo illustra come eseguire la migrazione di circuiti di Azure ExpressRoute e delle reti virtuali associate dal modello di distribuzione classica al modello di distribuzione Azure Resource Manager. 


## <a name="before-you-begin"></a>Prima di iniziare
* Verificare di avere la versione più recente dei moduli di Azure PowerShell. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Prima di procedere con la configurazione, assicurarsi di avere verificato i [prerequisiti](expressroute-prerequisites.md), i [requisiti di routing](expressroute-routing.md) e i [flussi di lavoro](expressroute-workflows.md).
* Rivedere le informazioni disponibili in [Spostamento dei circuiti ExpressRoute dal modello di distribuzione classica al modello di distribuzione Resource Manager](expressroute-move.md). Assicurarsi di aver compreso pienamente i limiti e le limitazioni.
* Verificare che il circuito sia completamente operativo nel modello di distribuzione classica.
* Assicurarsi che sia disponibile un gruppo di risorse creato nel modello di distribuzione Resource Manager.
* Vedere la documentazione seguente sulla migrazione delle risorse:

    * [Migrazione supportata dalla piattaforma di risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [FAQs: Platform-supported migration of IaaS resources from classic to Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md) (Domande frequenti: Migrazione supportata dalla piattaforma per risorse IaaS dalla distribuzione classica ad Azure Resource Manager)
    * [Errori comuni durante la migrazione dalla distribuzione classica ad Azure Resource Manager](../virtual-machines/virtual-machines-migration-errors.md)

## <a name="supported-and-unsupported-scenarios"></a>Scenari supportati e non supportati

* È possibile eseguire la migrazione di un circuito ExpressRoute dalla distribuzione classica all'ambiente Resource Manager senza tempi di inattività. È possibile spostare qualsiasi circuito ExpressRoute dal modello di distribuzione classica all'ambiente Resource Manager senza tempi di inattività. Seguire le istruzioni disponibili in [Spostare i circuiti ExpressRoute dal modello di distribuzione classica a quello Resource Manager usando PowerShell](expressroute-howto-move-arm.md). Questo è un prerequisito per lo spostamento di risorse connesse alla rete virtuale.
* È possibile eseguire la migrazione nell'ambiente Resource Manager di reti virtuali, gateway e distribuzioni associate che si trovano nella rete virtuale e sono associati al circuito ExpressRoute nella stessa sottoscrizione, senza tempi di inattività. È possibile seguire la procedura illustrata più avanti per eseguire la migrazione di risorse come reti virtuali, gateway e macchine virtuali distribuiti nella rete virtuale. È necessario assicurare che le reti virtuali siano configurate correttamente prima della migrazione. 
* Le reti virtuali, i gateway e le distribuzioni associate che si trovano nella rete virtuale ma non sono inclusi nella stessa sottoscrizione del circuito ExpressRoute richiedono del tempo di inattività per il completamento della migrazione. L'ultima sezione del documento illustra la procedura da seguire per eseguire la migrazione delle risorse.

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Spostare un circuito ExpressRoute dal modello di distribuzione classica a Resource Manager
È necessario spostare un circuito ExpressRoute da una distribuzione classica a un ambiente Resource Manager prima di provare a eseguire la migrazione delle risorse associate al circuito ExpressRoute. Per ottenere questo risultato, vedere gli articoli seguenti:

* Rivedere le informazioni disponibili in [Spostamento dei circuiti ExpressRoute dal modello di distribuzione classica al modello di distribuzione Resource Manager](expressroute-move.md).
* [Spostare un circuito dal modello di distribuzione classica a quello Resource Manager usando Azure PowerShell](expressroute-howto-move-arm.md).
* Usare il portale di gestione dei servizi di Azure. È possibile seguire il flusso di lavoro per [creare un nuovo circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e selezionare l'opzione di importazione. 

Questa operazione non comporta tempi di inattività. È possibile continuare a trasferire dati tra l'istanza locale e Microsoft durante la migrazione.

## <a name="prepare-your-virtual-network-for-migration"></a>Preparare la rete virtuale per la migrazione
È necessario assicurarsi che la rete della rete virtuale da sottoporre a migrazione non includa elementi superflui. Per scaricare la configurazione della rete virtuale e aggiornarla in base alla necessità, eseguire il cmdlet di PowerShell seguente:

    Add-AzureAccount
    Select-AzureSubscription -SubscriptionName <VNET Subscription>
    Get-AzureVNetConfig -ExportToFile C:\virtualnetworkconfig.xml
      
È necessario assicurarsi che tutti i riferimenti a <ConnectionsToLocalNetwork> vengano rimossi dalle reti virtuali da sottoporre a migrazione. Il frammento di codice seguente mostra una configurazione di rete di esempio:

    <VirtualNetworkSite name="MyVNet" Location="East US">
        <AddressSpace>
            <AddressPrefix>10.0.0.0/8</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="Subnet-1">
                <AddressPrefix>10.0.0.0/11</AddressPrefix>
            </Subnet>
            <Subnet name="GatewaySubnet">
                <AddressPrefix>10.32.0.0/28</AddressPrefix>
            </Subnet>
        </Subnets>
        <Gateway>
            <ConnectionsToLocalNetwork>
            </ConnectionsToLocalNetwork>
        </Gateway>
    </VirtualNetworkSite>
 
Se <ConnectionsToLocalNetwork> non è vuoto, eliminare i riferimenti sottostanti e inviare di nuovo la configurazione di rete. È possibile ottenere questo risultato eseguendo il cmdlet di PowerShell seguente:

    Set-AzureVNetConfig -ConfigurationPath c:\virtualnetworkconfig.xml

## <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Eseguire la migrazione di reti virtuali, gateway e distribuzioni associate nella stessa sottoscrizione del circuito ExpressRoute
Questa sezione illustra la procedura da seguire per eseguire la migrazione di una rete virtuale, un gateway e le distribuzioni associate nella stessa sottoscrizione del circuito ExpressRoute. Questa migrazione non comporta tempi di inattività. È possibile continuare a usare tutte le risorse durante il processo di migrazione. Il piano di gestione è bloccato durante la migrazione. 

1. Assicurarsi che il circuito ExpressRoute sia stato spostato dalla distribuzione classica all'ambiente Resource Manager.
2. Assicurarsi che la rete virtuale sia stata preparata in modo appropriato per la migrazione.
3. Registrare la sottoscrizione per la migrazione delle risorse. Per registrare la sottoscrizione per la migrazione delle risorse, usare il frammento di codice di PowerShell seguente: 
    ```
    Select-AzureRmSubscription -SubscriptionName <Your Subscription Name>
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    ```
4. Convalidare, preparare ed eseguire la migrazione. Per spostare la rete virtuale, usare il frammento di codice di PowerShell seguente:
    ```
    Move-AzureVirtualNetwork -Prepare $vnetName  
    Move-AzureVirtualNetwork -Commit $vnetName
    ```
    È anche possibile interrompere la migrazione eseguendo il cmdlet di PowerShell seguente:
    ```
    Move-AzureVirtualNetwork -Abort $vnetName
    ``` 
## <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-a-different-subscription-from-that-of-the-expressroute-circuit"></a>Eseguire la migrazione di reti virtuali, gateway e distribuzioni associate in una sottoscrizione diversa da quella del circuito ExpressRoute

1. Assicurarsi che il circuito ExpressRoute sia stato spostato dalla distribuzione classica all'ambiente Resource Manager.
2. Assicurarsi che la rete virtuale sia stata preparata in modo appropriato per la migrazione.
3. Assicurarsi che il circuito ExpressRoute possa essere usato nella distribuzione classica e nell'ambiente Resource Manager. Per consentire l'uso del circuito nella distribuzione classica e nell'ambiente Resource Manager, usare questo script di PowerShell: 
    ```
    Login-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName <My subscription>
    $circuit = Get-AzureRmExpressRouteCircuit -Name <CircuitName> -ResourceGroupName <ResourceGroup Name> 
    $circuit.AllowClassicOperations = $true
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
    ```
4. Creare autorizzazioni nell'ambiente Resource Manager. Per informazioni su come creare le autorizzazioni, vedere [Come collegare reti virtuali a circuiti ExpressRoute](expressroute-howto-linkvnet-arm.md). Per creare un'autorizzazione, usare il frammento di codice di PowerShell seguente:
    ```
    circuit = Get-AzureRmExpressRouteCircuit -Name <CircuitName> -ResourceGroupName <ResourceGroup Name> 
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "AuthorizationForMigration"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
    $circuit = Get-AzureRmExpressRouteCircuit -Name MigrateCircuit -ResourceGroupName MigrateRGWest

    $id = $circuit.id 
    $auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "AuthorizationForMigration"

    $key=$auth1.AuthorizationKey 
    ```
    Annotare l'ID del circuito e la chiave di autorizzazione. Questi elementi vengono usati per connettere il circuito alla rete virtuale al termine della migrazione.
  
5. Eliminare il collegamento al circuito dedicato associato alla rete virtuale. Per rimuovere il collegamento al circuito nella distribuzione classica, usare il cmdlet seguente: 
    ```
    $skey = Get-AzureDedicatedCircuit | select ServiceKey
    Remove-AzureDedicatedCircuitLink -ServiceKey $skey -VNetName $vnetName
    ```  

6. Registrare la sottoscrizione per la migrazione delle risorse. Per registrare la sottoscrizione per la migrazione delle risorse, usare il frammento di codice di PowerShell seguente: 
    ```
    Select-AzureRmSubscription -SubscriptionName <Your Subscription Name>
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    ```
7. Convalidare, preparare ed eseguire la migrazione. Per spostare la rete virtuale, usare il frammento di codice di PowerShell seguente:
    ```
    Move-AzureVirtualNetwork -Prepare $vnetName  
    Move-AzureVirtualNetwork -Commit $vnetName
    ```
    È anche possibile interrompere la migrazione eseguendo il cmdlet di PowerShell seguente:
    ```
    Move-AzureVirtualNetwork -Abort $vnetName
    ```
8. Connettere di nuovo la rete virtuale al circuito ExpressRoute. Il frammento di codice di PowerShell seguente viene eseguito nel contesto della sottoscrizione in cui viene creata la rete virtuale. È necessario non eseguire questo frammento di codice nella sottoscrizione in cui viene creato il circuito. Usare l'ID del circuito come valore PeerID e la chiave di autorizzazione annotata nel Passaggio 4.
    ```
    Select-AzureRMSubscription –SubscriptionName <customer subscription>  
    $gw = Get-AzureRmVirtualNetworkGateway -Name $vnetName-Default-Gateway -ResourceGroupName ($vnetName + "-Migrated")
    $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroup  ($vnetName + "-Migrated")  

    New-AzureRmVirtualNetworkGatewayConnection -Name  ($vnetName + "-GwConn") -ResourceGroupName ($vnetName + "-Migrated")  -Location $vnet.Location -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey $key
    ```
## <a name="next-steps"></a>Passaggi successivi
* [Migrazione supportata dalla piattaforma di risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [FAQs: Platform-supported migration of IaaS resources from classic to Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md) (Domande frequenti: Migrazione supportata dalla piattaforma per risorse IaaS dalla distribuzione classica ad Azure Resource Manager)
* [Errori comuni durante la migrazione dalla distribuzione classica ad Azure Resource Manager](../virtual-machines/virtual-machines-migration-errors.md)

