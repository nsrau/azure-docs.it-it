---
title: 'Connettere le reti virtuali classiche alle reti virtuali di Azure Resource Manager: PowerShell | Microsoft Docs'
description: Informazioni su come creare una connessione VPN tra le reti virtuali classiche e le reti virtuali di Resource Manager usando il gateway VPN e PowerShell
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: f17c3bf0-5cc9-4629-9928-1b72d0c9340b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: cherylmc
ms.openlocfilehash: da5bddba3a1fad74b2ee08fd2f34d1b01c7345c8
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Connettere reti virtuali da diversi modelli di distribuzione usando PowerShell



Questo articolo descrive come connettere reti virtuali classiche a reti virtuali di Resource Manager per consentire alle risorse presenti nei modelli di distribuzione separati di comunicare tra di loro. La procedura descritta in questo articolo usa PowerShell, tuttavia è inoltre possibile creare questa configurazione tramite il portale di Azure selezionando l'articolo da questo elenco.

> [!div class="op_single_selector"]
> * [Portale](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

La connessione di una rete virtuale classica a una rete virtuale di Resource Manager è simile alla connessione di una rete virtuale a una posizione del sito locale. Entrambi i tipi di connettività utilizzano un gateway VPN per fornire un tunnel sicuro tramite IPsec/IKE. È possibile creare una connessione tra reti virtuali in sottoscrizioni diverse e in aree diverse. È anche possibile connettere reti virtuali che già dispongono di connessioni alle reti locali, purché il gateway con cui sono state configurate sia dinamico o basato su route. Per altre informazioni sulle connessioni da rete virtuale a rete virtuale, vedere la sezione [Domande frequenti relative alla connessione da rete virtuale a rete virtuale](#faq) alla fine di questo articolo. 

Se le reti virtuali si trovano nella stessa area, è consigliabile considerare invece la possibilità di connetterle tramite peering reti virtuali. Peering reti virtuali non usa un gateway VPN. Per altre informazioni, vedere [Peering reti virtuali](../virtual-network/virtual-network-peering-overview.md). 

## <a name="before"></a>Prima di iniziare

I passaggi seguenti illustrano le impostazioni necessarie per configurare un gateway dinamico o basato su route per ogni rete virtuale e creare una connessione VPN tra i gateway. Questa configurazione non supporta i gateway con routing statico o basati su criteri.

### <a name="pre"></a>Prerequisiti

* Entrambe le reti virtuali sono già state create.
* Gli intervalli di indirizzi per le reti virtuali non si sovrappongono l'uno con l'altro o non si sovrappongano con gli intervalli delle eventuali altre connessioni a cui i gateway potrebbero essere collegati.
* Sono stati installati i cmdlet di PowerShell più recenti. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview) . Assicurarsi di installare sia i cmdlet di Gestione dei servizi (SM) che i cmdlet di Resource Manager (RM). 

### <a name="exampleref"></a>Impostazioni di esempio

È possibile usare questi valori per creare un ambiente di test o farvi riferimento per comprendere meglio gli esempi di questo articolo.

**Impostazioni della rete virtuale classica**

Nome rete virtuale = ClassicVNet  <br>
Località = Stati Uniti occidentali <br>
Spazi degli indirizzi della rete virtuale = 10.0.0.0/24 <br>
Subnet-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Nome della rete locale = RMVNetLocal <br>
GatewayType = DynamicRouting

**Impostazioni della rete virtuale di Resource Manager**

Nome della rete virtuale = RMVNet  <br>
Gruppo di risorse= RG1 <br>
Spazi degli indirizzi della rete virtuale = 192.168.0.0/16 <br>
Subnet-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Località = Stati Uniti orientali <br>
Nome IP pubblico del gateway = gwpip <br>
Gateway di rete locale = ClassicVNetLocal <br>
Nome gateway di rete virtuale = RMGateway <br>
Configurazione di indirizzamento IP del gateway = gwipconfig

## <a name="createsmgw"></a>Sezione 1: Configurare una rete virtuale classica
### <a name="1-download-your-network-configuration-file"></a>1. Eseguire il download del file di configurazione di rete
1. Accedere all'account Azure nella console di PowerShell con diritti elevati. Il cmdlet seguente richiede le credenziali di accesso per l'account Azure. Dopo l'accesso, vengono scaricate le impostazioni dell'account in modo che siano disponibili per Azure PowerShell. Per completare questa parte della configurazione, si usano i cmdlet PowerShell di Gestione dei servizi.

  ```powershell
  Add-AzureAccount
  ```
2. Esportare il file di configurazione di rete di Azure eseguendo il comando seguente. Se necessario è possibile modificare il percorso del file da esportare.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
3. Aprire il file XML scaricato per modificarlo. Per un esempio del file di configurazione di rete, vedere lo [Schema di configurazione di rete](https://msdn.microsoft.com/library/jj157100.aspx).

### <a name="2-verify-the-gateway-subnet"></a>2. Verificare la subnet del gateway
Nell'elemento **VirtualNetworkSites** aggiungere una subnet del gateway alla rete virtuale se non ne è ancora stato creato uno. Quando si lavora con il file di configurazione di rete, la subnet del gateway DEVE essere denominata "GatewaySubnet", altrimenti Azure non potrà riconoscerla e utilizzarla come una subnet del gateway.

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Esempio:**

    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>

### <a name="3-add-the-local-network-site"></a>3. Aggiungere il sito di rete locale
Il sito di rete locale aggiunto rappresenta la rete virtuale di Resource Manager a cui si desidera connettersi. Aggiungere un elemento **LocalNetworkSites** al file, se non ne esiste già uno. A questo punto nella configurazione, il parametro VPNGatewayAddress può essere qualsiasi indirizzo IP pubblico valido poiché non è stato ancora creato il gateway per la rete virtuale di Resource Manager. Dopo aver creato il gateway, l'indirizzo IP segnaposto verrà sostituito con l'indirizzo IP pubblico corretto assegnato al gateway di Resource Manager.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. Associare la rete locale al sito della rete locale
Questa sezione descrive come specificare il sito di rete locale a cui si desidera connettere la rete virtuale. In questo caso, è la rete virtuale di Resource Manager a cui si è fatto riferimento in precedenza. Assicurarsi che i nomi corrispondano. Questo passaggio non crea un gateway. Specifica la rete locale a cui il gateway si collegherà.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="5-save-the-file-and-upload"></a>5. Salvare il file e caricarlo
Salvare il file, quindi importarlo in Azure eseguendo il comando seguente. Assicurarsi di modificare il percorso del file in base al proprio ambiente.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Viene visualizzato un risultato simile che mostra che l'importazione è stata completata.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="6-create-the-gateway"></a>6. Creare il gateway

Prima di eseguire questo esempio, vedere i nomi esatti previsti da Azure nel file di configurazione di rete scaricato. Il file di configurazione di rete contiene i valori per le reti virtuali classiche. In alcuni casi, i nomi di rete virtuale classica vengono modificati nel file di configurazione di rete durante la creazione di impostazioni di rete virtuale classica nel portale di Azure, a causa delle differenze nei modelli di distribuzione. Ad esempio, se è stato usato il portale di Azure per creare una rete virtuale classica denominata "Classic VNet"' in un gruppo di risorse denominato "ClassicRG", il nome contenuto nel file di configurazione di rete viene convertito in "Group ClassicRG Classic VNet". Quando si specifica il nome di una rete virtuale che contiene spazi, racchiudere il valore tra virgolette.


Usare l'esempio seguente per creare un gateway con routing dinamico:

```powershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

È possibile controllare lo stato del gateway tramite il cmdlet **Get-AzureVNetGateway**.

## <a name="creatermgw"></a>Sezione 2: Configurazione del gateway della rete virtuale di Resource Manager
Per creare un gateway VPN per la rete virtuale di Resource Manager, seguire le istruzioni seguenti. Non eseguire i passaggi fino a quando non si è recuperato l'indirizzo IP pubblico del gateway della rete virtuale classica. 

1. Accedere all'account Azure nella console di PowerShell. Il cmdlet seguente richiede le credenziali di accesso per l'account Azure. Dopo l'accesso, vengono scaricate le impostazioni dell'account in modo che siano disponibili per Azure PowerShell.

  ```powershell
  Login-AzureRmAccount
  ``` 
   
  Ottenere un elenco delle sottoscrizioni di Azure se si dispone di più di una sottoscrizione.

  ```powershell
  Get-AzureRmSubscription
  ```
   
  Specificare la sottoscrizione da usare.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
2. Creare un gateway di rete locale. In una rete virtuale il gateway di rete locale in genere fa riferimento al percorso locale. In questo caso, il gateway di rete locale fa riferimento alla rete virtuale classica. Assegnargli un nome che Azure possa usare come riferimento e specificare il prefisso dello spazio indirizzi. Azure usa il prefisso di indirizzo IP che viene specificato per identificare il traffico da inviare al percorso locale. Se si desidera modificare le informazioni prima di creare il gateway, è possibile modificare i valori ed eseguire nuovamente l'esempio.
   
   **-Name** è il nome da assegnare per fare riferimento al gateway di rete locale.<br>
   **AddressPrefix -** è lo spazio degli indirizzi per una rete virtuale classica.<br>
   **-GatewayIpAddress** è l'indirizzo IP pubblico del gateway della rete virtuale classica. Assicurarsi di modificare l'esempio seguente in modo da riflettere l'indirizzo IP corretto.<br>

  ```powershell
  New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
  -Location "West US" -AddressPrefix "10.0.0.0/24" `
  -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
  ```
3. Richiedere un indirizzo IP pubblico da allocare al gateway di rete virtuale per la rete virtuale di Resource Manager. Non è possibile specificare l'indirizzo IP che si vuole usare. L'indirizzo IP viene allocato in modo dinamico al gateway di rete virtuale. Ciò non significa però che l'indirizzo IP verrà modificato. L'indirizzo IP del gateway di rete virtuale viene modificato solamente quando il gateway viene eliminato e ricreato. Non viene modificato in caso di ridimensionamento, reimpostazione o altre manutenzioni/aggiornamenti del gateway.

  In questo passaggio viene anche impostata una variabile che verrà usata in un passaggio successivo.

  ```powershell
  $ipaddress = New-AzureRmPublicIpAddress -Name gwpip `
  -ResourceGroupName RG1 -Location 'EastUS' `
  -AllocationMethod Dynamic
  ```

4. Verificare che la rete virtuale disponga di una subnet del gateway. Se non esiste alcuna subnet del gateway, aggiungerne una. Assicurarsi che la subnet del gateway sia denominata *GatewaySubnet*.
5. Recuperare la subnet usata per il gateway tramite il comando seguente. In questo passaggio, è anche possibile impostare una variabile da usare nel passaggio successivo.
   
   **-Name** è il nome della rete virtuale di Resource Manager.<br>
   **-ResourceGroupName** è il gruppo di risorse a cui è associata la rete virtuale. Per poter funzionare correttamente, la subnet del gateway deve esistere già per questa rete virtuale e deve essere denominata *GatewaySubnet* .<br>

  ```powershell
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
  -VirtualNetwork (Get-AzureRmVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
  ``` 

6. Creare la configurazione di indirizzamento IP del gateway. La configurazione del gateway definisce la subnet e l'indirizzo IP pubblico da utilizzare. Per creare la configurazione del gateway, usare l'esempio seguente.

  In questo passaggio i parametri **-SubnetId** e **-PublicIpAddressId** devono essere passati alla proprietà id dalla subnet e dagli oggetti dell'indirizzo IP rispettivamente. Non è possibile usare una semplice stringa. Queste variabili vengono impostate nel passaggio per richiedere un indirizzo IP pubblico e nel passaggio per recuperare la subnet.

  ```powershell
  $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
  -Name gwipconfig -SubnetId $subnet.id `
  -PublicIpAddressId $ipaddress.id
  ```
7. Creare il gateway di rete virtuale di Resource Manager tramite il comando seguente. `-VpnType` deve essere *RouteBased*. La creazione del gateway può richiedere oltre 45 minuti.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
  -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
  -IpConfigurations $gwipconfig `
  -EnableBgp $false -VpnType RouteBased
  ```
8. Dopo che il gateway della rete privata virtuale è stato creato, copiare l'indirizzo IP pubblico. Verrà usato al momento della configurazione delle impostazioni di rete locale per la rete virtuale classica. È possibile usare il cmdlet seguente per recuperare l'indirizzo IP pubblico. L'indirizzo IP pubblico viene elencato nel valore restituito come *IpAddress*.

  ```powershell
  Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1
  ```

## <a name="localsite"></a>Sezione 3: Modificare le impostazioni del sito locale della rete virtuale classica

In questa sezione viene usata la rete virtuale classica. Viene sostituito l'indirizzo IP segnaposto usato nella specifica delle impostazioni del sito locale, che verrà usato per connettersi al gateway di rete virtuale di Resource Manager. 

1. Esportare il file di configurazione della rete.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
2. In un editor di testo modificare il valore di VPNGatewayAddress. Sostituire l'indirizzo IP segnaposto con l'indirizzo IP pubblico del gateway di Resource Manager e salvare le modifiche.

  ```
  <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
  ```
3. Importare il file di configurazione di rete modificato in Azure.

  ```powershell
  Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
  ```

## <a name="connect"></a>Sezione 4: Creazione di una connessione tra i gateway
La creazione di una connessione tra i gateway richiede PowerShell. Potrebbe essere necessario aggiungere l'account Azure per usare la versione classica dei cmdlet di PowerShell. A tale scopo, usare **Add-AzureAccount**.

1. Nella console di PowerShell impostare la chiave condivisa. Prima di eseguire i cmdlet, vedere i nomi esatti previsti da Azure nel file di configurazione di rete scaricato. Quando si specifica il nome di una rete virtuale che contiene spazi, racchiudere il valore tra virgolette singole.<br><br>Nell'esempio seguente **-VNetName** è il nome della rete virtuale classica, mentre **-LocalNetworkSiteName** è il nome specificato per il sito della rete locale. **-SharedKey** è un valore che è possibile generare e specificare. Per questo esempio è stato usato "abc123", ma è possibile generare e usare un elemento più complesso. È importante che il valore specificato qui sia lo stesso valore specificato nel passaggio successivo quando si crea la connessione. Il valore restituito deve mostrare lo **stato: Operazione completata**.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName ClassicVNet `
  -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
  ```
2. Creare la connessione VPN eseguendo i comandi seguenti:
   
  Impostare le variabili.

  ```powershell
  $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
  $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
  ```
   
  Creare la connessione. Si noti che **-ConnectionType** è IPsec, non Vnet2Vnet.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
  -Location "East US" -VirtualNetworkGateway1 `
  $vnet02gateway -LocalNetworkGateway2 `
  $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```

## <a name="verify"></a>Sezione 5: Verificare le connessioni

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Per verificare la connessione dalla rete virtuale classica alla rete virtuale di Resource Manager

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Portale di Azure

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Per verificare la connessione dalla rete virtuale di Resource Manager alla rete virtuale classica

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Portale di Azure

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>Domande frequenti sulle connessioni da rete virtuale a rete virtuale

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

