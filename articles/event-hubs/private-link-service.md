---
title: Integrare Hub eventi di Azure con il servizio Collegamento privato di Azure
description: Informazioni su come integrare Hub eventi di Azure con il servizio Collegamento privato di Azure
ms.date: 06/23/2020
ms.topic: article
ms.openlocfilehash: bfed3f8e4c19463e10b721006d742726cf916900
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512257"
---
# <a name="integrate-azure-event-hubs-with-azure-private-link"></a>Integrare Hub eventi di Azure con Collegamento privato di Azure
Il servizio Collegamento privato di Azure consente di accedere ai servizi di Azure, ad esempio Hub eventi di Azure, Archiviazione di Azure e Azure Cosmos DB, e ai servizi di clienti/partner ospitati in Azure tramite un **endpoint privato** nella rete virtuale.

Un endpoint privato è un'interfaccia di rete che connette privatamente e in modo sicuro a un servizio basato su Collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della rete virtuale, portando il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite l'endpoint privato, quindi non sono necessari gateway, dispositivi NAT, ExpressRoute o connessioni VPN oppure indirizzi IP pubblici. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. È possibile connettersi a un'istanza di una risorsa di Azure, garantendo il massimo livello di granularità nel controllo di accesso.

Per altre informazioni, vedere [Che cos'è Collegamento privato di Azure?](../private-link/private-link-overview.md).

> [!IMPORTANT]
> Questa funzionalità è supportata sia per i livelli **standard** che per quelli **dedicati** . 

>[!WARNING]
> L'abilitazione di endpoint privati può impedire ad altri servizi di Azure di interagire con Hub eventi.
>
> I servizi Microsoft considerati attendibili non sono supportati quando si usano reti virtuali.
>
> Scenari comuni di Azure che non supportano le reti virtuali (l'elenco **NON** è esaustivo) -
> - Monitoraggio di Azure (impostazioni di diagnostica)
> - Analisi di flusso di Azure
> - Integrazione con Griglia di eventi di Azure
> - Route dell'hub IoT di Azure
> - Azure IoT Device Explorer
>
> I servizi Microsoft seguenti devono essere in una rete virtuale
> - App Web di Azure
> - Funzioni di Azure

## <a name="add-a-private-endpoint-using-azure-portal"></a>Aggiungere un endpoint privato con il portale di Azure

### <a name="prerequisites"></a>Prerequisiti

Per integrare uno spazio dei nomi di Hub eventi con Collegamento privato di Azure, sono necessarie le entità o autorizzazioni seguenti:

- Uno spazio dei nomi di Hub eventi.
- Una rete virtuale di Azure.
- Una subnet nella rete virtuale.
- Autorizzazioni di proprietario o collaboratore per lo spazio dei nomi e la rete virtuale.

L'endpoint privato e la rete virtuale devono trovarsi nella stessa area. Quando si seleziona un'area per l'endpoint privato tramite il portale, verranno automaticamente filtrate solo le reti virtuali presenti in tale area. Lo spazio dei nomi può trovarsi in un'area diversa.

L'endpoint privato usa un indirizzo IP privato nella rete virtuale.

### <a name="steps"></a>Passaggi
Se si ha già uno spazio dei nomi di Hub eventi, è possibile creare una connessione di collegamento privato seguendo questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Nella barra di ricerca digitare **Hub eventi**.
3. Selezionare nell'elenco lo **spazio dei nomi** in cui si vuole aggiungere un endpoint privato.
4. Selezionare la scheda **Rete** in **Impostazioni**.

    > [!NOTE]
    > Viene visualizzata la scheda **rete** solo per gli spazi dei nomi **standard** o **dedicati** . 
1. Selezionare la scheda **Connessioni endpoint privato** nella parte superiore della pagina. 
1. Selezionare il pulsante **+ Endpoint privato** nella parte superiore della pagina.

    :::image type="content" source="./media/private-link-service/private-link-service-3.png" alt-text="Pagina rete-scheda connessioni endpoint privato-Aggiungi collegamento endpoint privato":::
7. Nella pagina **Informazioni di base** seguire questa procedura: 
    1. Selezionare la **sottoscrizione di Azure** in cui creare l'endpoint privato. 
    2. Selezionare il **gruppo di risorse** per la risorsa endpoint privato.
    3. Immettere un **nome** per l'endpoint privato. 
    5. Selezionare un'**area** per l'endpoint privato. L'endpoint privato deve trovarsi nella stessa area della rete virtuale, ma può trovarsi in un'area diversa rispetto alla risorsa collegamento privato a cui si sta effettuando la connessione. 
    6. Selezionare **Avanti: Risorsa >** nella parte inferiore della pagina.

        ![Creare l'endpoint privato - Pagina Informazioni di base](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Nella pagina **Risorsa** seguire questa procedura:
    1. Per il metodo di connessione, se si è selezionato **Connettersi a una risorsa di Azure nella directory**, seguire questa procedura: 
        1. Selezionare la **sottoscrizione di Azure** in cui si trova lo **spazio dei nomi di Hub eventi**. 
        2. Per **Tipo di risorsa** selezionare **Microsoft.EventHub/namespaces**.
        3. Per **Risorsa** selezionare uno spazio dei nomi di Hub eventi nell'elenco a discesa. 
        4. Verificare che **Sottorisorsa di destinazione** sia impostato su **namespace**.
        5. Selezionare **Avanti: Configurazione >** nella parte inferiore della pagina. 
        
            ![Creare l'endpoint privato - Pagina Risorsa](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Se si seleziona **Connettersi a una risorsa di Azure in base all'alias o all'ID risorsa**, seguire questa procedura:
        1. Immettere l'**ID risorsa** o l'**alias**. Può trattarsi dell'ID risorsa o dell'alias condiviso da un altro utente. Il modo più semplice per ottenere l'ID risorsa è passare allo spazio dei nomi di Hub eventi nel portale di Azure e copiare la parte dell'URI a partire da `/subscriptions/`. Per un esempio, vedere l'immagine seguente. 
        2. Per **Sottorisorsa di destinazione** immettere **namespace**. È il tipo della sottorisorsa a cui l'endpoint privato può accedere.
        3. (facoltativo) Immettere un **messaggio di richiesta**. Il proprietario della risorsa visualizza questo messaggio mentre gestisce la connessione endpoint privato.
        4. Selezionare quindi **Avanti: Configurazione >** nella parte inferiore della pagina.

            ![Creare l'endpoint privato - Connettersi usando l'ID risorsa](./media/private-link-service/connect-resource-id.png)
9. Nella pagina **Configurazione** si seleziona la subnet in una rete virtuale in cui si vuole distribuire l'endpoint privato. 
    1. Selezionare una **rete virtuale**. Nell'elenco a discesa sono elencate solo le reti virtuali nella sottoscrizione e nella località attualmente selezionate. 
    2. Selezionare una **subnet** nella rete virtuale selezionata. 
    3. Selezionare **Avanti: Tag >** nella parte inferiore della pagina. 

        ![Creare l'endpoint privato - Pagina Configurazione](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Nella pagina **Tag** creare i tag (nomi e valori) da associare alla risorsa endpoint privato. Selezionare quindi il pulsante **Rivedi e crea** nella parte inferiore della pagina. 
11. In **Rivedi e crea** rivedere tutte le impostazioni e selezionare **Crea** per creare l'endpoint privato.
    
    ![Creare l'endpoint privato - Pagina Rivedi e crea](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Verificare che nell'elenco degli endpoint venga visualizzata la connessione endpoint privato creata. In questo esempio l'endpoint privato viene approvato automaticamente perché si è connessi a una risorsa di Azure nella directory e si hanno autorizzazioni sufficienti. 

    ![Endpoint privato creato](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Aggiungere un endpoint privato con PowerShell
L'esempio seguente illustra come usare Azure PowerShell per creare una connessione endpoint privato. Non crea un cluster dedicato. Per creare un cluster di Hub eventi dedicato, seguire la procedura descritta in [questo articolo](event-hubs-dedicated-cluster-create-portal.md). 

```azurepowershell-interactive
# create resource group

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VIRTUAL NETWORK LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName $rgName `
                    -Location $vnetlocation `
                    -Name $vnetName `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name $subnetName `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork

# create an event hubs namespace in a dedicated cluster
$namespaceResource = New-AzResource -Location $namespaceLocation `
                                    -ResourceName $namespaceName `
                                    -ResourceGroupName $rgName `
                                    -Sku @{name = "Standard"; capacity = 1} `
                                    -Properties @{clusterArmId = "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/clusters/<EVENT HUBS CLUSTER NAME>"} `
                                    -ResourceType "Microsoft.EventHub/namespaces" -ApiVersion "2018-01-01-preview"

# create private endpoint connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use later
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# create a private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

### <a name="configure-the-private-dns-zone"></a>Configurare la zona DNS privato
Creare una zona DNS privato per il dominio di Hub eventi e creare un collegamento di associazione con la rete virtuale:

```azurepowershell-interactive
$zone = New-AzPrivateDnsZone -ResourceGroupName $rgName `
                            -Name "privatelink.servicebus.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $rgName `
                                            -ZoneName "privatelink.servicebus.windows.net" `
                                            -Name "mylink" `
                                            -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
        Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
        $recordName = $fqdn.split('.',2)[0] 
        $dnsZone = $fqdn.split('.',2)[1] 
        New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.servicebus.windows.net"  `
                                -ResourceGroupName $rgName -Ttl 600 `
                                -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
    } 
}
```

## <a name="manage-private-endpoints-using-azure-portal"></a>Gestire gli endpoint privati con il portale di Azure

Quando si crea un endpoint privato, la connessione deve essere approvata. Se la risorsa per cui si sta creando un endpoint privato si trova nella propria directory, è possibile approvare la richiesta di connessione purché si abbiano autorizzazioni sufficienti. Se ci si sta connettendo a una risorsa di Azure in un'altra directory, è necessario attendere che il proprietario della risorsa approvi la richiesta di connessione.

Sono disponibili quattro stati di provisioning:

| Azione del servizio | Stato dell'endpoint privato del consumer del servizio | Descrizione |
|--|--|--|
| nessuno | In sospeso | La connessione viene creata manualmente ed è in attesa di approvazione dal proprietario della risorsa di collegamento privato. |
| Approvazione | Approved | La connessione è stata approvata automaticamente o manualmente ed è pronta per essere usata. |
| Rifiuto | Rifiutato | La connessione è stata rifiutata dal proprietario della risorsa di collegamento privato. |
| Rimuovere | Disconnesso | La connessione è stata rimossa dal proprietario della risorsa di collegamento privato, l'endpoint privato diventa informativo e deve essere eliminato per la pulizia. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Approvare, rifiutare o rimuovere una connessione endpoint privato

1. Accedere al portale di Azure.
2. Nella barra di ricerca digitare **Hub eventi**.
3. Selezionare lo **spazio dei nomi** che si vuole gestire.
4. Selezionare la scheda **Rete**.
5. Passare a una delle sezioni seguenti in base all'operazione che si vuole eseguire: approvare, rifiutare o rimuovere.

### <a name="approve-a-private-endpoint-connection"></a>Approvare una connessione endpoint privato
1. Se sono presenti connessioni in sospeso, verrà visualizzata una connessione elencata con **In sospeso** nello stato di provisioning. 
2. Selezionare l'**endpoint privato** che si vuole approvare
3. Selezionare il pulsante **Approva**.

    ![Immagine](./media/private-link-service/approve-private-endpoint.png)
4. Nella pagina **Approva la connessione** aggiungere un commento (facoltativo) e selezionare **Sì**. Se si seleziona **No**, non accade nulla. 
5. Si noterà che lo stato della connessione endpoint privato nell'elenco è diventato **Approvata**. 

### <a name="reject-a-private-endpoint-connection"></a>Rifiutare una connessione endpoint privato

1. Se sono presenti connessioni endpoint privato da rifiutare, sia che si tratti di una richiesta in sospeso o di una connessione esistente, selezionare la connessione e fare clic sul pulsante **Rifiuta**.

    ![Immagine](./media/private-link-service/private-endpoint-reject-button.png)
2. Nella pagina **Rifiuta la connessione** immettere un commento (facoltativo) e selezionare **Sì**. Se si seleziona **No**, non accade nulla. 
3. Si noterà che lo stato della connessione endpoint privato nell'elenco è diventato **Rifiutata**. 

### <a name="remove-a-private-endpoint-connection"></a>Rimuovere una connessione endpoint privato

1. Per rimuovere una connessione endpoint privato, selezionarla nell'elenco e selezionare **Rimuovi** sulla barra degli strumenti.
2. Nella pagina **Elimina connessione** selezionare **Sì** per confermare l'eliminazione dell'endpoint privato. Se si seleziona **No**, non accade nulla.
3. Si noterà che lo stato è diventato **Disconnessa** e che l'endpoint non è più presente nell'elenco.

## <a name="validate-that-the-private-link-connection-works"></a>Verificare il funzionamento della connessione di collegamento privato

È necessario verificare che le risorse all'interno della stessa subnet della risorsa endpoint privato si connettano allo spazio dei nomi di Hub eventi tramite un indirizzo IP privato e che abbiano l'integrazione con la zona DNS privato corretta.

Creare prima di tutto una macchina virtuale seguendo la procedura descritta nell'articolo [Creare una macchina virtuale di Windows nel portale di Azure](../virtual-machines/windows/quick-create-portal.md).

Nella scheda **Rete**: 

1. Specificare **Rete virtuale** e **Subnet**. È necessario selezionare la rete virtuale in cui è stato distribuito l'endpoint privato.
2. Specificare una risorsa **IP pubblico**.
3. Per **Gruppo di sicurezza di rete della scheda di interfaccia di rete** selezionare **Nessuno**.
4. Per **Bilanciamento del carico** selezionare **No**.

Connettersi alla macchina virtuale, aprire la riga di comando ed eseguire il comando seguente:

```console
nslookup <event-hubs-namespace-name>.servicebus.windows.net
```

Verrà visualizzato un risultato simile al seguente. 

```console
Non-authoritative answer:
Name:    <event-hubs-namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <event-hubs-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Limitazioni e considerazioni di progettazione

**Pricing** (Prezzi): per informazioni sui prezzi, vedere [Prezzi di Collegamento privato di Azure](https://azure.microsoft.com/pricing/details/private-link/).

**Limitazioni**:  Questa funzionalità è disponibile in tutte le aree pubbliche di Azure.

**Numero massimo di endpoint privati per lo spazio dei nomi di Hub eventi**: 120.

Per altre informazioni, vedere [Servizio Collegamento privato di Azure: Limitazioni](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Collegamento privato di Azure](../private-link/private-link-service-overview.md)
- Altre informazioni su [Hub eventi di Azure](event-hubs-about.md)
