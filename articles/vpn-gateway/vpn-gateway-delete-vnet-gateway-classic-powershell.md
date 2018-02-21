---
title: 'Eliminare un gateway di rete virtuale: PowerShell: Azure classico | Microsoft Docs'
description: Eliminare un gateway di rete virtuale usando PowerShell nel modello di distribuzione classico.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: cherylmc
ms.openlocfilehash: b1bc18307227a728e2bc8fd95e30fdc1cbdb8c59
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>Eliminare un gateway di rete virtuale usando PowerShell (classico)
> [!div class="op_single_selector"]
> * [Resource Manager - Portale di Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Classica: PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Questo articolo illustra come eliminare un gateway VPN nel modello di distribuzione classica tramite PowerShell. Dopo aver eliminato il gateway di rete virtuale, modificare il file di configurazione di rete per rimuovere gli elementi che non sono più in uso.

##<a name="connect"></a>Passaggio 1: Connettersi ad Azure

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Installare i cmdlet di PowerShell più recenti.

Scaricare e installare la versione più recente dei cmdlet di PowerShell per Gestione del servizio di Azure. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Connettersi all'account di Azure. 

Aprire la console di PowerShell con diritti elevati e connettersi all'account. Per eseguire la connessione, usare gli esempi che seguono:

```powershell
Add-AzureAccount
```

## <a name="export"></a>Passaggio 2: Esportare e visualizzare il file di configurazione di rete

Creare una directory nel computer ed esportarvi il file di configurazione di rete. Usare questo file per visualizzare le informazioni di configurazione correnti e modificare la configurazione di rete.

In questo esempio il file di configurazione di rete viene esportato in C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Aprire il file con un editor di testo e visualizzare il nome di una rete virtuale classica. Quando si crea una rete virtuale nel portale di Azure, il nome completo che usa Azure non è visibile nel portale. Ad esempio, una rete virtuale che nel portale di Azure sembra avere il nome di "ClassicVNet1" potrebbe avere un nome molto più lungo nel file di configurazione di rete. Il nome potrebbe essere simile a: "Gruppo ClassicRG1 ClassicVNet1". I nomi delle reti virtuali sono elencati come **"VirtualNetworkSite name ="**. Usare i nomi nel file di configurazione di rete durante l'esecuzione dei cmdlet di PowerShell.

## <a name="delete"></a>Passaggio 3: Eliminare il gateway di rete virtuale

Quando si elimina un gateway di rete virtuale, vengono interrotte tutte le connessioni alla rete virtuale tramite il gateway. Se ci sono client P2S connessi alla rete virtuale, verranno disconnessi senza alcun avviso.

Questo esempio elimina il gateway di rete virtuale. Usare il nome completo della rete virtuale dal file di configurazione di rete.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

Se ha esito positivo, il risultato mostra quanto segue:

```
Status : Successful
```

## <a name="modify"></a>Passaggio 4: Modificare il file di configurazione di rete

Quando si elimina un gateway di rete virtuale, il cmdlet non modifica il file di configurazione di rete. È necessario modificare il file per rimuovere gli elementi che non sono più in uso. Le sezioni seguenti consentono di modificare il file di configurazione di rete che è stato scaricato.

### <a name="lnsref"></a>Riferimenti al sito di rete locale

Per rimuovere le informazioni di riferimento al sito, apportare modifiche alla configurazione in **ConnectionsToLocalNetwork/LocalNetworkSiteRef**. La rimozione di un riferimento al sito locale induce Azure a eliminare un tunnel. A seconda della configurazione che è stata creata, **LocalNetworkSiteRef** potrebbe non essere elencato.

```
<Gateway>
   <ConnectionsToLocalNetwork>
     <LocalNetworkSiteRef name="D1BFC9CB_Site2">
       <Connection type="IPsec" />
     </LocalNetworkSiteRef>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

Esempio:

```
<Gateway>
   <ConnectionsToLocalNetwork>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

###<a name="lns"></a>Siti di rete locale

Rimuovere tutti i siti locali che non sono più in uso. A seconda della configurazione creata, è possibile che **LocalNetworkSite** non sia elencato.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
  <LocalNetworkSite name="Site3">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>57.179.18.164</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

In questo esempio viene rimosso solo Site3.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

### <a name="clientaddresss"></a>Pool di indirizzi client

Se era presente una connessione P2S sulla rete virtuale, si avrà **VPNClientAddressPool**. Rimuovere i pool di indirizzi client che corrispondono al gateway di rete virtuale che è stato eliminato.

```
<Gateway>
    <VPNClientAddressPool>
      <AddressPrefix>10.1.0.0/24</AddressPrefix>
    </VPNClientAddressPool>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

Esempio:

```
<Gateway>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

### <a name="gwsub"></a>GatewaySubnet

Eliminare il **GatewaySubnet** corrispondente alla rete virtuale.

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
   <Subnet name="GatewaySubnet">
     <AddressPrefix>10.11.1.0/29</AddressPrefix>
   </Subnet>
 </Subnets>
```

Esempio:

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
 </Subnets>
```

## <a name="upload"></a>Passaggio 5: Caricare il file di configurazione di rete

Salvare le modifiche e caricare la configurazione di rete in Azure. Assicurarsi di modificare il percorso del file in base al proprio ambiente.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Se ha esito positivo, il valore restituito è simile a quello mostrato in questo esempio:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded