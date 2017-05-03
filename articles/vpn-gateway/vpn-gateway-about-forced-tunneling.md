---
title: 'Configurare il tunneling forzato per connessioni di Azure da sito a sito: versione classica | Microsoft Docs'
description: Come reindirizzare o forzare tutto il traffico associato a Internet verso il percorso locale.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 5c0177f1-540c-4474-9b80-f541fa44240b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0d9afb1554158a4d88b7f161c62fa51c1bf61a7d
ms.openlocfilehash: 326d09de70e00463df554f27d754ef6999da21d1
ms.lasthandoff: 04/12/2017


---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Configurare il tunneling forzato con il modello di distribuzione classico

Il tunneling forzato consente di reindirizzare o "forzare" tutto il traffico associato a Internet verso la posizione locale tramite un tunnel VPN da sito a sito per l'ispezione e il controllo. Si tratta di un requisito di sicurezza critico per la maggior parte dei criteri IT aziendali. Senza il tunneling forzato, il traffico associato a Internet dalle macchine virtuali in Azure raggiungerà direttamente Internet attraversando sempre l'infrastruttura di rete di Azure, senza l'opzione che consente di ispezionare o controllare il traffico. L'accesso a Internet non autorizzato potrebbe provocare la divulgazione di informazioni o altri tipi di violazioni della sicurezza

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Questo articolo illustra la configurazione del tunneling forzato per le reti virtuali create usando il modello di distribuzione classico. Il tunneling forzato può essere configurato tramite PowerShell e non tramite il portale. Se si intende configurare il tunneling forzato per il modello di distribuzione di Resource Manager, selezionare l'articolo relativo alla distribuzione classica nell'elenco a discesa seguente:

> [!div class="op_single_selector"]
> * [PowerShell - Classico](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell - Gestione risorse](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="requirements-and-considerations"></a>Problemi e considerazioni
Il tunneling forzato in Azure viene configurato tramite route di rete virtuale definite dall'utente. Il reindirizzamento del traffico a un sito locale viene espresso come route predefinita al gateway VPN di Azure. Nella sezione seguente viene elencata la limitazione attuale della tabella di routing e delle route per una rete virtuale di Azure:

* Ciascuna subnet della rete virtuale dispone di una tabella di routing di sistema integrata. La tabella di routing di sistema include i tre gruppi di route seguenti:
  
  * **Route della rete virtuale locale:** direttamente alla destinazione di macchine virtuali nella stessa rete virtuale
  * **Route locale:** al gateway VPN di Azure
  * **Route predefinita:** direttamente a Internet. I pacchetti destinati agli indirizzi IP privati non rientranti nelle due route precedenti verranno eliminati.
* Con il rilascio di route definite dall'utente, è possibile creare una tabella di routing per aggiungere una route predefinita, quindi associare la tabella di routing alle subnet della rete virtuale per abilitare il tunneling forzato su tali subnet.
* È necessario impostare un "sito predefinito" tra i siti locali cross-premise connessi alla rete virtuale.
* Il tunneling forzato deve essere associato a una rete virtuale che disponga di un gateway VPN (non un gateway statico).
* Il tunneling forzato ExpressRoute non viene configurato mediante questo meccanismo, ma è abilitato annunciando una route predefinita tramite le sessioni di peering BGP ExpressRoute. Per altre informazioni, vedere la [documentazione di ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) .

## <a name="configuration-overview"></a>Panoramica della configurazione
Nell'esempio seguente il tunneling della subnet front-end non viene forzato. I carichi di lavoro nella subnet front-end possono continuare ad accettare e a rispondere alle richieste dei clienti direttamente da Internet. Il tunneling delle subnet di livello intermedio e back-end viene forzato. Tutte le connessioni in uscita da queste due subnet a Internet verranno forzate o reindirizzate verso un sito locale tramite uno dei tunnel VPN S2S.

Ciò consente di limitare e ispezionare l'accesso a Internet dalle macchine virtuali o dai servizi cloud in Azure, pur continuando ad abilitare l'architettura dei servizi multilivello richiesta. È anche possibile applicare il tunneling forzato a tutte le reti virtuali se non sono presenti carichi di lavoro con connessione Internet nelle reti virtuali.

![Tunneling forzato](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="before-you-begin"></a>Prima di iniziare
Prima di iniziare la configurazione, verificare che ci siano le condizioni seguenti:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
* Una rete virtuale configurata. 
* La versione più recente dei cmdlets di Azure PowerShell. Per altre informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs) .

## <a name="configure-forced-tunneling"></a>È possibile configurare il tunneling forzato?
La procedura seguente consentirà di specificare il tunneling forzato per una rete virtuale. I passaggi di configurazione corrispondono al file di configurazione della rete virtuale.

```
<VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>
```

In questo esempio la rete virtuale "MultiTier-VNet" include tre subnet (*Frontend*, *Midtier* e *Backend*) con quattro connessioni di più sedi locali (*DefaultSiteHQ*) e tre *Branch*. 

La procedura consente di impostare *DefaultSiteHQ* come connessione predefinita del sito per il tunneling forzato e di configurare le subnet Midtier e Backend per l'uso del tunneling forzato.

1. Creare una tabella di routing. Utilizzare il cmdlet seguente per creare la tabella route.

  ```powershell
  New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
  ```
2. Aggiungere una route predefinita alla tabella di routing. 
   
    L'esempio seguente aggiunge una route predefinita alla tabella di routing creata nel passaggio 1. Notare che l'unica route supportata è il prefisso di destinazione di "0.0.0.0/0" su NextHop "VPNGateway".

  ```powershell
  Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
  ```
3. Associare la tabella di routing alle subnet. 
   
    Dopo aver creato una tabella di routing e aggiunto una route, usare l'esempio seguente per aggiungere o associare la tabella di route a una subnet della rete virtuale. L'esempio aggiunge la tabella di route "MyRouteTable" alle subnet Midtier e Backend della rete virtuale MultiTier-VNet.

  ```powershell
  Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
  Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
  ```
4. Assegnare un sito predefinito per il tunneling forzato. 
   
    Nel passaggio precedente, gli script di cmdlet di esempio hanno consentito di creare la tabella route e di associarla a due delle subnet della rete virtuale. L'ultimo passaggio consiste nel selezionare un sito locale tra le connessioni multisito della rete virtuale come sito predefinito o tunnel.

  ```powershell
  $DefaultSite = @("DefaultSiteHQ")
  Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
  ```

## <a name="additional-powershell-cmdlets"></a>Ulteriori cmdlet di PowerShell
### <a name="to-delete-a-route-table"></a>Per eliminare una tabella route

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>Per elencare una tabella route

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>Per eliminare una route da una tabella route

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>Per rimuovere una route da una subnet

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Per elencare la tabella route associata a una subnet

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Per rimuovere un sito predefinito da un gateway VPN della rete virtuale

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```







