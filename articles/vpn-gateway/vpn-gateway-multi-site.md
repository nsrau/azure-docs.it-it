---
title: "Connettere una rete virtuale a più siti con gateway VPN e PowerShell: modello classico | Documentazione Microsoft"
description: "Questo articolo descrive la procedura per connettere più siti locali a una rete virtuale mediante un gateway VPN per il modello di distribuzione classica."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-service-management
ms.assetid: b043df6e-f1e8-4a4d-8467-c06079e2c093
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/20/2017
ms.author: yushwang
ms.openlocfilehash: 434f84dc6244eddce9b172a617722b218360ffc2
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Aggiungere una connessione da sito a sito a una rete virtuale con una connessione gateway VPN esistente (modello classico)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (classic)](vpn-gateway-multi-site.md) (PowerShell (classico))
>
>

Questo articolo illustra come usare PowerShell per aggiungere connessioni da sito a sito (S2S) a un gateway VPN con una connessione esistente. Questo tipo di connessione è spesso definito configurazione "multisito". I passaggi riportati in questo articolo si applicano alle reti virtuali create usando il modello di distribuzione classica (noto anche come Gestione dei servizi). Questi passaggi non si applicano alle configurazioni con connessioni coesistenti ExpressRoute/da sito a sito.

### <a name="deployment-models-and-methods"></a>Metodi e modelli di distribuzione

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Questa tabella viene aggiornata man mano che per questa configurazione diventano disponibili nuovi articoli e altri strumenti. Quando un articolo è disponibile, nella tabella sarà presente un collegamento diretto.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>Informazioni sulla connessione

È possibile connettere più siti locali a una singola rete virtuale. Ciò è particolarmente interessante per la creazione di soluzioni per cloud ibride. La creazione di una connessione multisito al gateway della rete virtuale di Azure è simile alla creazione di altre connessioni da sito a sito. È infatti possibile usare un gateway VPN di Azure esistente, purché il gateway sia dinamico (basato su route).

Se è già presente un gateway statico connesso alla rete virtuale, è possibile modificare il tipo di gateway in dinamico senza dover ricreare la rete virtuale per adattarla a più siti. Prima di modificare il tipo di routing, assicurarsi che il gateway VPN locale supporti le configurazioni VPN basate su route.

![diagramma multisito](./media/vpn-gateway-multi-site/multisite.png "multisito")

## <a name="points-to-consider"></a>Elementi da considerare:

**Non sarà possibile usare il portale per apportare modifiche a questa rete virtuale.** È necessario apportare modifiche al file di configurazione di rete invece di usare il portale. Se si apportano modifiche nel portale, queste sovrascriveranno le impostazioni di riferimento multisito per questa rete virtuale.

Dovrebbe risultare semplice usando il file di configurazione di rete nel momento in cui è stata completata la procedura multisito. Tuttavia, se si ci sono più persone che lavorano alla configurazione di rete, è necessario verificare che tutti siano a conoscenza di questa limitazione. Ciò non significa che non sia possibile usare il portale. È possibile usarlo per qualsiasi operazione, ad eccezione delle modifiche di configurazione a questa particolare rete virtuale.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la configurazione, verificare ci siano le condizioni seguenti:

* Hardware VPN compatibile per ogni sede locale. Controllare le [Informazioni sui dispositivi VPN per le connessioni di rete virtuale da sito a sito](vpn-gateway-about-vpn-devices.md) per verificare se il dispositivo che si intende usare è ritenuto compatibile.
* Un indirizzo IP IPv4 pubblico esterno per ogni dispositivo VPN. L’indirizzo IP non può trovarsi dietro un NAT. Questo è un requisito.
* È necessario installare l'ultima versione dei cmdlet di Azure PowerShell. Assicurarsi di installare la versione Service Management (SM) oltre alla versione Resource Manager (RM). Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview) .
* Una persona esperta nella configurazione di hardware VPN. È necessario avere una conoscenza approfondita di come configurare il proprio dispositivo VPN oppure lavorare insieme una persona esperta in questo campo.
* Gli intervalli di indirizzi IP da usare per la propria rete virtuale (se non ne hai già creata una).
* Gli intervalli di indirizzi IP per ogni sito della rete locale a cui si effettuerà la connessione. È necessario verificare che gli intervalli di indirizzi IP per ogni sito della rete locale a cui ci si vuole connettere non si sovrappongano. In caso contrario, il portale o l'API REST rifiuteranno il caricamento della configurazione.<br>Se, ad esempio, ci sono due siti della rete locale che entrambi contengono l'intervallo di indirizzo IP 10.2.3.0/24 e si ha a disposizione un pacchetto con un indirizzo di destinazione 10.2.3.3, Azure non saprà a quale sito si vuole inviare il pacchetto perché gli intervalli degli indirizzi si sovrappongono. Per evitare problemi di routing, Azure non consente di caricare un file di configurazione con intervalli che si sovrappongono.

## <a name="1-create-a-site-to-site-vpn"></a>1. Creare una VPN da sito a sito
Se già si dispone di una VPN da sito a sito con un gateway di routing dinamico, eseguire l'operazione seguente. È possibile procedere e iniziare a [Esportare le impostazioni di configurazione della rete virtuale](#export). Se non ne avete una, eseguire le operazioni seguenti:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Se già si ha a disposizione una rete virtuale da sito a sito, ma con un gateway di routing statico basato su criteri:
1. Modificare il tipo di gateway in routing dinamico. Una VPN multisito richiede un gateway di routing dinamico, ovvero basato su route. Per modificare il tipo di gateway, è necessario prima eliminare quello esistente e poi crearne uno nuovo.
2. Configurare il nuovo gateway e creare il proprio tunnel VPN. Per istruzioni vedere [Specificare il tipo di SKU e VPN](vpn-gateway-howto-site-to-site-classic-portal.md#sku). Assicurarsi di specificare il tipo di routing come "dinamico".

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Se non si ha a disposizione una rete virtuale da sito a sito:
1. Creare una rete virtuale da sito a sito usando le istruzioni seguenti: [Creare una rete virtuale con una connessione VPN da sito a sito](vpn-gateway-site-to-site-create.md).  
2. Configurare un gateway di routing dinamico usando le istruzioni seguenti: [Configurare un gateway VPN](vpn-gateway-configure-vpn-gateway-mp.md). Assicurarsi di selezionare **routing dinamico** per il tipo di gateway.

## <a name="export"></a>2. Esportare il file di configurazione di rete
Esportare il file di configurazione di rete di Azure eseguendo il comando seguente. Se necessario è possibile modificare il percorso del file da esportare.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. Aprire il file di configurazione di rete
Aprire il file di configurazione di rete scaricato nell'ultimo passaggio. Usare qualsiasi editor xml desiderato. Il codice dovrebbe essere simile a quello riportato di seguito:

        <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <LocalNetworkSites>
              <LocalNetworkSite name="Site1">
                <AddressSpace>
                  <AddressPrefix>10.0.0.0/16</AddressPrefix>
                  <AddressPrefix>10.1.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
              </LocalNetworkSite>
              <LocalNetworkSite name="Site2">
                <AddressSpace>
                  <AddressPrefix>10.2.0.0/16</AddressPrefix>
                  <AddressPrefix>10.3.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
              </LocalNetworkSite>
            </LocalNetworkSites>
            <VirtualNetworkSites>
              <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
                <AddressSpace>
                  <AddressPrefix>10.20.0.0/16</AddressPrefix>
                  <AddressPrefix>10.21.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="FE">
                    <AddressPrefix>10.20.0.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="BE">
                    <AddressPrefix>10.20.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="GatewaySubnet">
                    <AddressPrefix>10.20.2.0/29</AddressPrefix>
                  </Subnet>
                </Subnets>
                <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="Site1">
                      <Connection type="IPsec" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

## <a name="4-add-multiple-site-references"></a>4. Aggiungere riferimenti a più siti
Quando si aggiungono o rimuovono le informazioni di riferimento ai siti, si apporteranno modifiche alla configurazione in ConnectionsToLocalNetwork/LocalNetworkSiteRef. L'aggiunta di un nuovo riferimento a siti locali induce Azure a creare un nuovo tunnel. Nell'esempio seguente, la configurazione di rete è per una connessione a un singolo sito. Dopo aver apportato le modifiche, salvare il file.

```
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

Per aggiungere riferimenti ad altri siti (creare una configurazione multisito), è sufficiente aggiungere altre righe "LocalNetworkSiteRef", come illustrato nell'esempio riportato di seguito:

```
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. Importare il file di configurazione di rete
Importare il file di configurazione di rete. Quando si importa questo file con le modifiche, verranno aggiunti i nuovi tunnel. I tunnel useranno il gateway dinamico creato in precedenza. È possibile usare PowerShell per importare il file.

## <a name="6-download-keys"></a>6. Chiavi di download
Una volta che sono stati aggiunti i nuovi tunnel, usare i cmdlet di PowerShell "Get-AzureVNetGatewayKey" per ottenere le chiavi precondivise IPsec/IKE per ogni tunnel.

Ad esempio: 

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Se si preferisce, è possibile usare anche l'API REST *Ottenere la chiave condivisa del gateway di rete virtuale* per ottenere le chiavi precondivise.

## <a name="7-verify-your-connections"></a>7. Verificare le connessioni
Verificare lo stato del tunnel multisito. Dopo aver scaricato le chiavi per ogni tunnel, è opportuno verificare le connessioni. Usare "Get-AzureVnetConnection" per ottenere un elenco di tunnel di reti virtuali, come illustrato nell'esempio seguente. VNet1 è il nome della rete virtuale.

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

Esempio restituito:

```
    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui gateway VPN, vedere [Informazioni sui gateway VPN](vpn-gateway-about-vpngateways.md).
