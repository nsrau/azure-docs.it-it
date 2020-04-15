---
title: Integrare hub eventi di Azure con il servizio di collegamento privato di AzureIntegrate Azure Event Hubs with Azure Private Link Service
description: Informazioni su come integrare gli hub eventi di Azure con il servizio di collegamento privato di AzureLearn how to integrate Azure Event Hubs with Azure Private Link Service
services: event-hubs
author: spelluru
ms.author: spelluru
ms.date: 03/12/2020
ms.service: event-hubs
ms.topic: article
ms.openlocfilehash: bcc360bbe4dd58200993b9377317ccb608b3529d
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383638"
---
# <a name="integrate-azure-event-hubs-with-azure-private-link-preview"></a>Integrare hub eventi di Azure con Azure Private Link (anteprima)Integrate Azure Event Hubs with Azure Private Link (Preview)
Il servizio di collegamento privato di Azure consente di accedere ai servizi di Azure (ad esempio, Hub eventi di Azure, Archiviazione di Azure e database Cosmos di Azure) e ai servizi cliente/partner ospitati di Azure su un **endpoint privato** nella rete virtuale.

A private endpoint is a network interface that connects you privately and securely to a service powered by Azure Private Link. L'endpoint privato usa un indirizzo IP privato della rete virtuale, introducendo efficacemente il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite l'endpoint privato, quindi non sono necessari gateway, dispositivi NAT, ExpressRoute o connessioni VPN oppure indirizzi IP pubblici. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. È possibile connettersi a un'istanza di una risorsa di Azure, garantendo il massimo livello di granularità nel controllo di accesso.

Per altre informazioni, vedere [Che cos'è Collegamento privato di Azure?](../private-link/private-link-overview.md).

> [!NOTE]
> Questa funzionalità è supportata solo con il livello **dedicato.** Per ulteriori informazioni sul livello dedicato, vedere [Panoramica degli hub eventi dedicati](event-hubs-dedicated-overview.md). 
>
> Questa funzione è attualmente in **anteprima**. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Aggiungere un endpoint privato usando il portale di AzureAdd a private endpoint using Azure portal

### <a name="prerequisites"></a>Prerequisiti

Per integrare uno spazio dei nomi Hub eventi con Azure Private Link, sono necessarie le entità o le autorizzazioni seguenti:To integrate an Event Hubs namespace with Azure Private Link, you'll need the following entities or permissions:

- Uno spazio dei nomi di Hub eventi.
- Una rete virtuale di Azure.
- Una subnet nella rete virtuale.
- Autorizzazioni di proprietario o collaboratore sia per lo spazio dei nomi che per la rete virtuale.

L'endpoint privato e la rete virtuale devono trovarsi nella stessa area. Quando si seleziona un'area per l'endpoint privato tramite il portale, verranno automaticamente filtrate solo le reti virtuali presenti in tale area. Lo spazio dei nomi può trovarsi in un'area diversa.

L'endpoint privato usa un indirizzo IP privato nella rete virtuale.

### <a name="steps"></a>Passaggi
Se si dispone già di uno spazio dei nomi Hub eventi, è possibile creare una connessione di collegamento privato attenendosi alla seguente procedura:

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Nella barra di ricerca digitare hub **eventi**.
3. Selezionare **lo spazio dei nomi** dall'elenco a cui si desidera aggiungere un endpoint privato.
4. Selezionare la scheda **Rete** in **Impostazioni**.
5. Selezionare la scheda **Connessioni endpoint privati (anteprima)** nella parte superiore della pagina. Se non si utilizza un livello dedicato di hub eventi, viene visualizzato un messaggio: Le connessioni endpoint private negli **hub eventi sono supportate solo dagli spazi dei nomi creati in un cluster dedicato**.
6. Selezionare il pulsante **: Endpoint privato** nella parte superiore della pagina.

    ![Immagine](./media/private-link-service/private-link-service-3.png)
7. Nella pagina **Nozioni di base,** attenersi alla seguente procedura: 
    1. Selezionare la sottoscrizione di **Azure** in cui si vuole creare l'endpoint privato. 
    2. Selezionare il **gruppo di risorse** per la risorsa endpoint privato.
    3. Immettere un **nome** per l'endpoint privato. 
    5. Selezionare **un'area** per l'endpoint privato. L'endpoint privato deve trovarsi nella stessa area della rete virtuale, ma può trovarsi in un'area diversa dalla risorsa collegamento privato a cui ci si connette. 
    6. Selezionare **Successivo: >risorsa** nella parte inferiore della pagina.

        ![Pagina Crea endpoint privato - Nozioni di base](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Nella pagina Risorsa eseguire la procedura seguente:On the **Resource** page, follow these steps:
    1. Per il metodo di connessione, se si seleziona Connetti a una **risorsa di Azure nella directory**personale, attenersi alla seguente procedura: 
        1. Selezionare la **sottoscrizione di Azure** in cui è presente lo spazio dei nomi Hub **eventi.** 
        2. Per **Tipo di risorsa**, selezionare **Microsoft.EventHub/namespaces** per **Tipo di risorsa**.
        3. Per **Risorsa**, selezionare uno spazio dei nomi Hub eventi dall'elenco a discesa. 
        4. Verificare che la **sottorisorsa Target** sia impostata sullo **spazio dei nomi**.
        5. Selezionare **Successivo: pulsante >della configurazione** nella parte inferiore della pagina. 
        
            ![Pagina Crea endpoint privato - Risorsa](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Se si seleziona Connetti a una **risorsa di Azure in base all'ID o all'alias**della risorsa, attenersi alla seguente procedura:
        1. Immettere l'ID o **l'alias**della **risorsa.** Può essere l'ID risorsa o l'alias che alcuni hanno condiviso con te.
        2. Per **la sottorisorsa Target**immettere **namespace**. È il tipo di sottorisorsa a cui può accedere l'endpoint privato.
        3. (opzionale) Immettere un **messaggio di richiesta**. Il proprietario della risorsa vede questo messaggio durante la gestione della connessione all'endpoint privato.
        4. Quindi, selezionare **Avanti: Configurazione >** pulsante nella parte inferiore della pagina.

            ![Creare un endpoint privato - Connettersi usando l'ID risorsaCreate Private Endpoint - Connect using resource ID](./media/private-link-service/connect-resource-id.png)
9. Nella pagina **Configurazione** selezionare la subnet in una rete virtuale in cui si vuole distribuire l'endpoint privato. 
    1. Selezionare una **rete virtuale**. Nell'elenco a discesa sono elencate solo le reti virtuali nella sottoscrizione e nel percorso attualmente selezionate. 
    2. Selezionare una **subnet** nella rete virtuale selezionata. 
    3. Selezionare **Successivo: Tag >** pulsante nella parte inferiore della pagina. 

        ![Crea endpoint privato - Pagina Configurazione](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Nella pagina **Tag** creare i tag (nomi e valori) che si desidera associare alla risorsa endpoint privata. Quindi, seleziona il pulsante **Revisione e crea** nella parte inferiore della pagina. 
11. Nella scheda **Revisione e creazione**rivedere tutte le impostazioni e selezionare **Crea** per creare l'endpoint privato.
    
    ![Crea endpoint privato - Pagina Revisione e creazione](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Verificare che la connessione all'endpoint privato creata venga visualizzata nell'elenco degli endpoint. In questo esempio, l'endpoint privato viene approvato automaticamente perché si è connessi a una risorsa di Azure nella directory e si dispone di autorizzazioni sufficienti. 

    ![Endpoint privato creato](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Aggiungere un endpoint privato tramite PowerShellAdd a private endpoint using PowerShell
L'esempio seguente mostra come usare Azure PowerShell per creare una connessione endpoint privata. Non crea un cluster dedicato per l'utente. Seguire i passaggi descritti in [questo articolo](event-hubs-dedicated-cluster-create-portal.md) per creare un cluster hub eventi dedicato. 

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
Creare una zona DNS privata per il dominio Hub eventi e creare un collegamento di associazione con la rete virtuale:Create a private DNS zone for Event Hubs domain and create an association link with the virtual network:

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

## <a name="manage-private-endpoints-using-azure-portal"></a>Gestire gli endpoint privati tramite il portale di AzureManage private endpoints using Azure portal

Quando si crea un endpoint privato, la connessione deve essere approvata. Se la risorsa per la quale si sta creando un endpoint privato si trova nella directory, è possibile approvare la richiesta di connessione purché si disponga di autorizzazioni sufficienti. Se ci si connette a una risorsa di Azure in un'altra directory, è necessario attendere che il proprietario della risorsa approvi la richiesta di connessione.

Sono disponibili quattro stati di provisioning:

| Azione di servizio | Stato dell'endpoint privato del consumer del servizio | Descrizione |
|--|--|--|
| nessuno | In sospeso | La connessione viene creata manualmente ed è in attesa di approvazione dal proprietario della risorsa di collegamento privato. |
| Approvazione | Approved | La connessione è stata approvata automaticamente o manualmente ed è pronta per essere usata. |
| Rifiuto | Rifiutato | La connessione è stata rifiutata dal proprietario della risorsa di collegamento privato. |
| Rimuovere | Disconnesso | La connessione è stata rimossa dal proprietario della risorsa di collegamento privato, l'endpoint privato diventa informativo e deve essere eliminato per la pulizia. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Approvare, rifiutare o rimuovere una connessione endpoint privato

1. Accedere al portale di Azure.
2. Nella barra di ricerca digitare hub **eventi**.
3. Selezionare lo **spazio dei nomi** che si desidera gestire.
4. Selezionare la scheda **Rete.Select** the Networking tab.
5. Passare alla sezione appropriata di seguito in base all'operazione desiderata: approvare, rifiutare o rimuovere.

### <a name="approve-a-private-endpoint-connection"></a>Approvare una connessione endpoint privata
1. Se sono presenti connessioni in sospeso, verrà visualizzata una connessione elencata con **In sospeso** nello stato di provisioning. 
2. Selezionare **l'endpoint privato** che si desidera approvare
3. Selezionare il pulsante **Approva.**

    ![Immagine](./media/private-link-service/approve-private-endpoint.png)
4. Nella pagina **Approva connessione** aggiungere un commento (facoltativo) e selezionare **Sì**. Se si seleziona **No**, non accade nulla. 
5. Lo stato della connessione all'endpoint privato nell'elenco dovrebbe essere modificato in **Approvato**. 

### <a name="reject-a-private-endpoint-connection"></a>Rifiutare una connessione endpoint privataReject a private endpoint connection

1. Se sono presenti connessioni endpoint private che si desidera rifiutare, indipendentemente dal fatto che si tratti di una richiesta in sospeso o di una connessione esistente, selezionare la connessione e fare clic sul pulsante **Rifiuta.**

    ![Immagine](./media/private-link-service/private-endpoint-reject-button.png)
2. Nella pagina **Rifiuta connessione** immettere un commento (facoltativo) e selezionare **Sì**. Se si seleziona **No**, non accade nulla. 
3. Lo stato della connessione all'endpoint privato dovrebbe essere modificato in **Rifiutato**. 

### <a name="remove-a-private-endpoint-connection"></a>Rimuovere una connessione endpoint privataRemove a private endpoint connection

1. Per rimuovere una connessione all'endpoint privato, selezionarla nell'elenco e selezionare **Rimuovi** sulla barra degli strumenti.
2. Nella pagina **Elimina connessione** selezionare **Sì** per confermare l'eliminazione dell'endpoint privato. Se si seleziona **No**, non accade nulla.
3. Lo stato dovrebbe essere modificato in **Disconnesso**. Quindi, si vedrà l'endpoint scomparire dall'elenco.

## <a name="validate-that-the-private-link-connection-works"></a>Verificare il funzionamento della connessione di collegamento privato

È necessario verificare che le risorse all'interno della stessa subnet della risorsa dell'endpoint privato si connettono allo spazio dei nomi Hub eventi tramite un indirizzo IP privato e che dispongano dell'integrazione della zona DNS privata corretta.

Creare prima di tutto una macchina virtuale seguendo la procedura descritta nell'articolo [Creare una macchina virtuale di Windows nel portale di Azure](../virtual-machines/windows/quick-create-portal.md).

Nella scheda **Rete:**

1. Specificare **la rete virtuale** e la **subnet**. È possibile creare una nuova macchina virtuale o selezionarne una esistente. Se si seleziona una macchina virtuale esistente, assicurarsi che l'area corrisponda.
1. Specificare una risorsa **IP pubblica.**
1. Nel **gruppo**di sicurezza di rete NIC **selezionare Nessuno**.
1. Nella pagina **Bilanciamento del carico**selezionare **No**.

Aprire la riga di comando ed eseguire il comando seguente:

```console
nslookup <your-event-hubs-namespace-name>.servicebus.windows.net
```

Se si esegue il comando di ricerca ns per risolvere l'indirizzo IP di uno spazio dei nomi Hub eventi su un endpoint pubblico, verrà visualizzato un risultato simile al seguente:

```console
c:\ >nslookup <your-event-hubs-namespae-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-event-hubs-namespace-name>.servicebus.windows.net
```

Se si esegue il comando di ricerca ns per risolvere l'indirizzo IP di uno spazio dei nomi Hub eventi su un endpoint privato, verrà visualizzato un risultato simile al seguente:

```console
c:\ >nslookup your_event-hubs-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-event-hub-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Limitazioni e considerazioni di progettazione

**Pricing** (Prezzi): per informazioni sui prezzi, vedere [Prezzi di Collegamento privato di Azure](https://azure.microsoft.com/pricing/details/private-link/).

**Limitazioni:** l'endpoint privato per gli hub eventi di Azure è in anteprima pubblica. Questa funzionalità è disponibile in tutte le aree pubbliche di Azure.

**Numero massimo di endpoint privati per spazio dei nomi Hub eventi: 120.Maximum number of private endpoints per Event Hubs namespace**: 120.

Per altre informazioni, vedere [Servizio Collegamento privato di Azure: Limitazioni](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Collegamento privato di Azure](../private-link/private-link-service-overview.md)
- Altre informazioni sugli hub eventi di AzureLearn more about [Azure Event Hubs](event-hubs-about.md)
