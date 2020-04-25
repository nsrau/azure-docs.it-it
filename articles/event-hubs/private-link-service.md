---
title: Integrare Hub eventi di Azure con il servizio di collegamento privato di Azure
description: Informazioni su come integrare Hub eventi di Azure con il servizio di collegamento privato di Azure
services: event-hubs
author: spelluru
ms.author: spelluru
ms.date: 03/12/2020
ms.service: event-hubs
ms.topic: article
ms.openlocfilehash: fb8fc93174345d0bdb09e4308a4206a65ed2270a
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148209"
---
# <a name="integrate-azure-event-hubs-with-azure-private-link-preview"></a>Integrare Hub eventi di Azure con collegamento privato di Azure (anteprima)
Il servizio di collegamento privato di Azure consente di accedere ai servizi di Azure (ad esempio, Hub eventi di Azure, archiviazione di Azure e Azure Cosmos DB) e ai servizi cliente/partner ospitati in Azure tramite un **endpoint privato** nella rete virtuale.

Un endpoint privato è un'interfaccia di rete che si connette privatamente e in modo sicuro a un servizio basato sul collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della rete virtuale, introducendo efficacemente il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite l'endpoint privato, quindi non sono necessari gateway, dispositivi NAT, ExpressRoute o connessioni VPN oppure indirizzi IP pubblici. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. È possibile connettersi a un'istanza di una risorsa di Azure, garantendo il massimo livello di granularità nel controllo di accesso.

Per altre informazioni, vedere [Che cos'è Collegamento privato di Azure?](../private-link/private-link-overview.md).

> [!IMPORTANT]
> Questa funzionalità è supportata solo con il livello **dedicato** . Per ulteriori informazioni sul livello dedicato, vedere [Panoramica di hub eventi dedicato](event-hubs-dedicated-overview.md). 
>
> Questa funzionalità è attualmente disponibile in **Anteprima**. 

>[!WARNING]
> L'abilitazione di endpoint privati può impedire ad altri servizi di Azure di interagire con hub eventi.
>
> I servizi Microsoft attendibili non sono supportati quando si usano reti virtuali.
>
> Scenari comuni di Azure che non supportano le reti virtuali (l'elenco **NON** è esaustivo) -
> - Monitoraggio di Azure (impostazione di diagnostica)
> - Analisi di flusso di Azure
> - Integrazione con Griglia di eventi di Azure
> - Route dell'hub IoT di Azure
> - Azure IoT Device Explorer
>
> I servizi Microsoft seguenti devono trovarsi in una rete virtuale
> - App Web di Azure
> - Funzioni di Azure

## <a name="add-a-private-endpoint-using-azure-portal"></a>Aggiungere un endpoint privato utilizzando portale di Azure

### <a name="prerequisites"></a>Prerequisiti

Per integrare uno spazio dei nomi di hub eventi con collegamento privato di Azure, sono necessarie le entità o le autorizzazioni seguenti:

- Uno spazio dei nomi di Hub eventi.
- Una rete virtuale di Azure.
- Una subnet nella rete virtuale.
- Autorizzazioni di proprietario o collaboratore sia per lo spazio dei nomi che per la rete virtuale.

L'endpoint privato e la rete virtuale devono trovarsi nella stessa area. Quando si seleziona un'area per l'endpoint privato tramite il portale, verranno automaticamente filtrate solo le reti virtuali presenti in tale area. Lo spazio dei nomi può trovarsi in un'area diversa.

L'endpoint privato usa un indirizzo IP privato nella rete virtuale.

### <a name="steps"></a>Passaggi
Se è già presente uno spazio dei nomi di hub eventi, è possibile creare una connessione di collegamento privato attenendosi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Nella barra di ricerca digitare **Hub eventi**.
3. Selezionare lo **spazio dei nomi** dall'elenco a cui si desidera aggiungere un endpoint privato.
4. Selezionare la scheda **rete** in **Impostazioni**.
5. Selezionare la scheda **connessioni endpoint privato (anteprima)** nella parte superiore della pagina. Se non si usa un livello dedicato di hub eventi, viene visualizzato un messaggio: le **connessioni degli endpoint privati negli hub eventi sono supportate solo dagli spazi dei nomi creati in un cluster dedicato**.
6. Selezionare il pulsante **+ endpoint privato** nella parte superiore della pagina.

    ![Immagine](./media/private-link-service/private-link-service-3.png)
7. Nella pagina **nozioni di base** seguire questa procedura: 
    1. Selezionare la **sottoscrizione di Azure** in cui si vuole creare l'endpoint privato. 
    2. Selezionare il **gruppo di risorse** per la risorsa endpoint privato.
    3. Immettere un **nome** per l'endpoint privato. 
    5. Selezionare un' **area** per l'endpoint privato. L'endpoint privato deve trovarsi nella stessa area della rete virtuale, ma può trovarsi in un'area diversa dalla risorsa di collegamento privato a cui ci si connette. 
    6. Fare clic su **Next: Resource >** Button nella parte inferiore della pagina.

        ![Crea endpoint privato-pagina Nozioni di base](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Nella pagina **delle risorse** , attenersi alla seguente procedura:
    1. Per metodo di connessione, se si seleziona **Connetti a una risorsa di Azure nella directory**, seguire questa procedura: 
        1. Selezionare la **sottoscrizione di Azure** in cui è presente lo **spazio dei nomi di hub eventi** . 
        2. Per **tipo di risorsa**selezionare **Microsoft. EventHub/Namespaces** per il **tipo di risorsa**.
        3. Per **Resource**selezionare uno spazio dei nomi di hub eventi dall'elenco a discesa. 
        4. Verificare che la **sottorisorsa di destinazione** sia impostata su **spazio dei nomi**.
        5. Fare clic su **Next: Configuration >** Button nella parte inferiore della pagina. 
        
            ![Crea endpoint privato-pagina delle risorse](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Se si seleziona **Connetti a una risorsa di Azure in base all'ID risorsa o alias**, seguire questa procedura:
        1. Immettere l' **ID risorsa** o l' **alias**. Può trattarsi dell'ID risorsa o dell'alias che qualcuno ha condiviso con l'utente. Il modo più semplice per ottenere l'ID risorsa è passare allo spazio dei nomi di hub eventi nel portale di Azure e copiare la parte dell'URI a `/subscriptions/`partire da. Per un esempio, vedere la figura seguente. 
        2. Per **risorsa secondaria di destinazione**immettere **spazio dei nomi**. È il tipo della sottorisorsa a cui l'endpoint privato può accedere.
        3. opzionale Immettere un **messaggio di richiesta**. Il proprietario della risorsa Visualizza questo messaggio durante la gestione della connessione all'endpoint privato.
        4. Quindi, fare clic su **Next: Configuration >** pulsante nella parte inferiore della pagina.

            ![Crea endpoint privato-Connetti usando l'ID risorsa](./media/private-link-service/connect-resource-id.png)
9. Nella pagina **configurazione** selezionare la subnet in una rete virtuale in cui si vuole distribuire l'endpoint privato. 
    1. Selezionare una **rete virtuale**. Nell'elenco a discesa sono elencate solo le reti virtuali nella sottoscrizione e nella località attualmente selezionate. 
    2. Selezionare una **subnet** nella rete virtuale selezionata. 
    3. Selezionare **Avanti: tag >** pulsante nella parte inferiore della pagina. 

        ![Crea endpoint privato-pagina Configurazione](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Nella pagina **tag** creare eventuali tag (nomi e valori) che si desidera associare alla risorsa dell'endpoint privato. Selezionare quindi il pulsante **Rivedi e crea** nella parte inferiore della pagina. 
11. In **Verifica e crea**esaminare tutte le impostazioni e selezionare **Crea** per creare l'endpoint privato.
    
    ![Crea endpoint privato-verifica e Crea pagina](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Verificare che venga visualizzata la connessione all'endpoint privato creata nell'elenco degli endpoint. In questo esempio, l'endpoint privato viene approvato automaticamente perché si è connessi a una risorsa di Azure nella directory e si dispone di autorizzazioni sufficienti. 

    ![Endpoint privato creato](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Aggiungere un endpoint privato usando PowerShell
Nell'esempio seguente viene illustrato come utilizzare Azure PowerShell per creare una connessione all'endpoint privato. Non crea un cluster dedicato. Seguire i passaggi in [questo articolo](event-hubs-dedicated-cluster-create-portal.md) per creare un cluster di hub eventi dedicato. 

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

### <a name="configure-the-private-dns-zone"></a>Configurare la zona DNS privata
Creare una zona DNS privata per il dominio di hub eventi e creare un collegamento di associazione con la rete virtuale:

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

## <a name="manage-private-endpoints-using-azure-portal"></a>Gestire endpoint privati con portale di Azure

Quando si crea un endpoint privato, la connessione deve essere approvata. Se la risorsa per cui si sta creando un endpoint privato si trova nella directory, è possibile approvare la richiesta di connessione purché si disponga di autorizzazioni sufficienti. Se ci si connette a una risorsa di Azure in un'altra directory, è necessario attendere che il proprietario della risorsa approvi la richiesta di connessione.

Sono disponibili quattro stati di provisioning:

| Azione del servizio | Stato dell'endpoint privato del consumer del servizio | Descrizione |
|--|--|--|
| nessuno | In sospeso | La connessione viene creata manualmente ed è in attesa di approvazione dal proprietario della risorsa di collegamento privato. |
| Approvazione | Approved | La connessione è stata approvata automaticamente o manualmente ed è pronta per essere usata. |
| Rifiuto | Rifiutato | La connessione è stata rifiutata dal proprietario della risorsa di collegamento privato. |
| Rimuovere | Disconnesso | La connessione è stata rimossa dal proprietario della risorsa di collegamento privato, l'endpoint privato diventa informativo e deve essere eliminato per la pulizia. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Approva, rifiuta o Rimuovi una connessione all'endpoint privato

1. Accedere al portale di Azure.
2. Nella barra di ricerca digitare **Hub eventi**.
3. Selezionare lo **spazio dei nomi** che si desidera gestire.
4. Selezionare la scheda **rete** .
5. Passare alla sezione appropriata riportata di seguito in base all'operazione desiderata: approva, rifiuta o Rimuovi.

### <a name="approve-a-private-endpoint-connection"></a>Approva una connessione all'endpoint privato
1. Se sono presenti connessioni in sospeso, verrà visualizzata una connessione elencata con in **sospeso** nello stato di provisioning. 
2. Selezionare l' **endpoint privato** che si vuole approvare
3. Selezionare il pulsante **approva** .

    ![Immagine](./media/private-link-service/approve-private-endpoint.png)
4. Nella pagina **approva connessione** aggiungere un commento (facoltativo) e selezionare **Sì**. Se si seleziona **No**, non viene eseguita alcuna operazione. 
5. Si noterà che lo stato della connessione all'endpoint privato nell'elenco è stato modificato in **approvato**. 

### <a name="reject-a-private-endpoint-connection"></a>Rifiutare una connessione all'endpoint privato

1. Se sono presenti connessioni di endpoint privato che si desidera rifiutare, sia che si tratti di una richiesta in sospeso o di una connessione esistente, selezionare la connessione e fare clic sul pulsante **rifiuta** .

    ![Immagine](./media/private-link-service/private-endpoint-reject-button.png)
2. Nella pagina **rifiuta connessione** immettere un commento (facoltativo) e selezionare **Sì**. Se si seleziona **No**, non viene eseguita alcuna operazione. 
3. Si noterà che lo stato della connessione all'endpoint privato nell'elenco è stato modificato in **rifiutato**. 

### <a name="remove-a-private-endpoint-connection"></a>Rimuovere una connessione all'endpoint privato

1. Per rimuovere una connessione all'endpoint privato, selezionarla nell'elenco e selezionare **Rimuovi** sulla barra degli strumenti.
2. Nella pagina **Elimina connessione** selezionare **Sì** per confermare l'eliminazione dell'endpoint privato. Se si seleziona **No**, non viene eseguita alcuna operazione.
3. Si noterà che lo stato è cambiato in **disconnected**. Si noterà quindi che l'endpoint scompare dall'elenco.

## <a name="validate-that-the-private-link-connection-works"></a>Verificare il funzionamento della connessione di collegamento privato

È necessario verificare che le risorse all'interno della stessa subnet della risorsa endpoint privato si connettano allo spazio dei nomi di hub eventi tramite un indirizzo IP privato e che dispongano dell'integrazione della zona DNS privata corretta.

Creare prima di tutto una macchina virtuale seguendo la procedura descritta nell'articolo [Creare una macchina virtuale di Windows nel portale di Azure](../virtual-machines/windows/quick-create-portal.md).

Nella scheda **rete** :

1. Specificare la **rete virtuale** e la **Subnet**. È possibile creare una nuova macchina virtuale o selezionarne una esistente. Se si seleziona una macchina virtuale esistente, assicurarsi che l'area corrisponda.
1. Specificare una risorsa **IP pubblica** .
1. Nel **gruppo di sicurezza di rete NIC**selezionare **nessuno**.
1. In **bilanciamento del carico**selezionare **No**.

Aprire la riga di comando ed eseguire il comando seguente:

```console
nslookup <your-event-hubs-namespace-name>.servicebus.windows.net
```

Se si esegue il comando di ricerca NS per risolvere l'indirizzo IP di uno spazio dei nomi di hub eventi su un endpoint pubblico, verrà visualizzato un risultato simile al seguente:

```console
c:\ >nslookup <your-event-hubs-namespae-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-event-hubs-namespace-name>.servicebus.windows.net
```

Se si esegue il comando di ricerca NS per risolvere l'indirizzo IP di uno spazio dei nomi di hub eventi su un endpoint privato, verrà visualizzato un risultato simile al seguente:

```console
c:\ >nslookup your_event-hubs-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-event-hub-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Limitazioni e considerazioni di progettazione

**Pricing** (Prezzi): per informazioni sui prezzi, vedere [Prezzi di Collegamento privato di Azure](https://azure.microsoft.com/pricing/details/private-link/).

**Limitazioni**: l'endpoint privato per hub eventi di Azure è in versione di anteprima pubblica. Questa funzionalità è disponibile in tutte le aree pubbliche di Azure.

**Numero massimo di endpoint privati per spazio dei nomi di hub eventi**: 120.

Per altre informazioni, vedere [Servizio Collegamento privato di Azure: Limitazioni](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Collegamento privato di Azure](../private-link/private-link-service-overview.md)
- Altre informazioni su [Hub eventi di Azure](event-hubs-about.md)
