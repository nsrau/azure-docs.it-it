---
title: Integrare il bus di servizio di Azure con il servizio Collegamento privato di Azure
description: Informazioni su come integrare il bus di servizio di Azure con il servizio Collegamento privato di Azure
services: service-bus-messaging
author: spelluru
ms.author: spelluru
ms.date: 03/13/2020
ms.service: service-bus-messaging
ms.topic: article
ms.openlocfilehash: a78375a3acf5c56d9a59c0f4b6113a063f8c431a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650959"
---
# <a name="integrate-azure-service-bus-with-azure-private-link"></a>Integrare il bus di servizio di Azure con Collegamento privato di Azure

Il servizio Collegamento privato di Azure consente di accedere ai servizi di Azure, ad esempio al bus di servizio di Azure, ad Archiviazione di Azure e ad Azure Cosmos DB, e ai servizi di clienti/partner ospitati in Azure tramite un **endpoint privato** nella rete virtuale.

Un endpoint privato è un'interfaccia di rete che connette privatamente e in modo sicuro a un servizio basato su Collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della rete virtuale, introducendo efficacemente il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite l'endpoint privato, quindi non sono necessari gateway, dispositivi NAT, ExpressRoute o connessioni VPN oppure indirizzi IP pubblici. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. È possibile connettersi a un'istanza di una risorsa di Azure, garantendo il massimo livello di granularità nel controllo di accesso.

Per altre informazioni, vedere [Che cos'è Collegamento privato di Azure?](../private-link/private-link-overview.md).

>[!WARNING]
> L'implementazione di endpoint privati può impedire ad altri servizi di Azure di interagire con il bus di servizio.
>
> I servizi Microsoft considerati attendibili non sono supportati quando si usano reti virtuali.
>
> Scenari comuni di Azure che non supportano le reti virtuali (l'elenco **NON** è esaustivo) -
> - Integrazione con Griglia di eventi di Azure
> - Route dell'hub IoT di Azure
> - Azure IoT Device Explorer
>
> I servizi Microsoft seguenti devono essere in una rete virtuale
> - Servizio app di Azure
> - Funzioni di Azure

> [!IMPORTANT]
> Questa funzionalità è supportata con il livello **Premium** del bus di servizio di Azure. Per altre informazioni sul livello Premium, vedere l'articolo [Livelli di messaggistica Premium e Standard del bus di servizio](service-bus-premium-messaging.md).


## <a name="add-a-private-endpoint-using-azure-portal"></a>Aggiungere un endpoint privato con il portale di Azure

### <a name="prerequisites"></a>Prerequisiti

Per integrare uno spazio dei nomi del bus di servizio con Collegamento privato di Azure, sono necessarie le entità o le autorizzazioni seguenti:

- Uno spazio dei nomi del bus di servizio.
- Una rete virtuale di Azure.
- Una subnet nella rete virtuale.
- Autorizzazioni di proprietario o collaboratore per lo spazio dei nomi del bus di servizio e la rete virtuale.

L'endpoint privato e la rete virtuale devono trovarsi nella stessa area. Quando si seleziona un'area per l'endpoint privato tramite il portale, verranno automaticamente filtrate solo le reti virtuali presenti in tale area. Lo spazio dei nomi del bus di servizio può trovarsi in un'area diversa. E l'endpoint privato usa un indirizzo IP privato nella rete virtuale.

### <a name="steps"></a>steps

Se si ha già uno spazio dei nomi esistente, è possibile creare un endpoint privato seguendo questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Nella barra di ricerca digitare **Bus di servizio**.
3. Selezionare nell'elenco lo **spazio dei nomi** in cui si vuole aggiungere un endpoint privato.
4. Selezionare la scheda **Rete** in **Impostazioni**.
5. Selezionare la scheda **Connessioni endpoint privato** nella parte superiore della pagina
6. Selezionare il pulsante **+ Endpoint privato** nella parte superiore della pagina.

    ![Pulsante Aggiungi endpoint privato](./media/private-link-service/private-link-service-3.png)
7. Nella pagina **Informazioni di base** seguire questa procedura: 
    1. Selezionare la **sottoscrizione di Azure** in cui creare l'endpoint privato. 
    2. Selezionare il **gruppo di risorse** per la risorsa endpoint privato.
    3. Immettere un **nome** per l'endpoint privato. 
    5. Selezionare un'**area** per l'endpoint privato. L'endpoint privato deve trovarsi nella stessa area della rete virtuale, ma può trovarsi in un'area diversa rispetto alla risorsa collegamento privato a cui si sta effettuando la connessione. 
    6. Selezionare **Avanti: Risorsa >** nella parte inferiore della pagina.

        ![Creare l'endpoint privato - Pagina Informazioni di base](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Nella pagina **Risorsa** seguire questa procedura:
    1. Per il metodo di connessione, se si è selezionato **Connettersi a una risorsa di Azure nella directory**, seguire questa procedura:   
        1. Selezionare la **sottoscrizione di Azure** in cui si trova lo **spazio dei nomi del bus di servizio**. 
        2. Per **Tipo di risorsa** selezionare **Microsoft.ServiceBus/namespaces**.
        3. Per **Risorsa** selezionare uno spazio dei nomi del bus di servizio nell'elenco a discesa. 
        4. Verificare che **Sottorisorsa di destinazione** sia impostato su **namespace**.
        5. Selezionare **Avanti: Configurazione >** nella parte inferiore della pagina. 
        
            ![Creare l'endpoint privato - Pagina Risorsa](./media/private-link-service/create-private-endpoint-resource-page.png)
    2. Se si seleziona **Connettersi a una risorsa di Azure in base all'alias o all'ID risorsa**, seguire questa procedura:
        1. Immettere l'**ID risorsa** o l'**alias**. Può trattarsi dell'ID risorsa o dell'alias condiviso da un altro utente. Il modo più semplice per ottenere l'ID risorsa è passare allo spazio dei nomi del bus di servizio nel portale di Azure e copiare la parte dell'URI a partire da `/subscriptions/`. Per un esempio, vedere l'immagine seguente. 
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
12. Verificare che l'endpoint privato sia stato creato. Se l'utente è il proprietario della risorsa e aveva selezionato l'opzione **Connettersi a una risorsa di Azure nella directory** come **Metodo di connessione**, la connessione all'endpoint deve essere impostata su **Approvazione automatica**. Se lo stato della connessione è **In sospeso**, vedere la sezione [Gestire gli endpoint privati con il portale di Azure](#manage-private-endpoints-using-azure-portal).

    ![Endpoint privato creato](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Aggiungere un endpoint privato con PowerShell
L'esempio seguente illustra come usare Azure PowerShell per creare una connessione endpoint privato a uno spazio dei nomi del bus di servizio.

L'endpoint privato e la rete virtuale devono trovarsi nella stessa area. Lo spazio dei nomi del bus di servizio può trovarsi in un'area diversa. E l'endpoint privato usa un indirizzo IP privato nella rete virtuale.

```azurepowershell-interactive

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VNET LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create resource group
az group create -l $vnetLocation -n $rgName

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

# create premium service bus namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Sku @{name = "Premium"; capacity = 1} -Properties @{} -ResourceType "Microsoft.ServiceBus/namespaces" -

# create a private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use in the next step                                
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# now, create private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


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
1. Nella barra di ricerca digitare **Bus di servizio**.
1. Selezionare lo **spazio dei nomi** che si vuole gestire.
1. Selezionare la scheda **Rete**.
5. Passare a una delle sezioni seguenti in base all'operazione che si vuole eseguire: approvare, rifiutare o rimuovere. 

### <a name="approve-a-private-endpoint-connection"></a>Approvare una connessione endpoint privato

1. Se sono presenti connessioni in sospeso, verrà visualizzata una connessione elencata con **In sospeso** nello stato di provisioning. 
2. Selezionare l'**endpoint privato** che si vuole approvare
3. Selezionare il pulsante **Approva**.

    ![Approvare un endpoint privato](./media/private-link-service/private-endpoint-approve.png)
4. Nella pagina **Approva la connessione** immettere un **commento** facoltativo e selezionare **Sì**. Se si seleziona **No**, non accade nulla. 

    ![Pagina Approva la connessione](./media/private-link-service/approve-connection-page.png)
5. Si noterà che lo stato della connessione nell'elenco è diventato **Approvata**. 

    ![Stato della connessione - Approvata](./media/private-link-service/connection-status-approved.png)

### <a name="reject-a-private-endpoint-connection"></a>Rifiutare una connessione endpoint privato

1. Se sono presenti connessioni endpoint privato da rifiutare, sia che si tratti di una richiesta in sospeso o di una connessione esistente approvata in precedenza, selezionare la connessione all'endpoint e fare clic sul pulsante **Rifiuta**.

    ![Pulsante Rifiuta](./media/private-link-service/private-endpoint-reject.png)
2. Nella pagina **Rifiuta la connessione** immettere un commento facoltativo e selezionare **Sì**. Se si seleziona **No**, non accade nulla. 

    ![Pagina Rifiuta la connessione](./media/private-link-service/reject-connection-page.png)
3. Si noterà che lo stato della connessione nell'elenco è diventato **Rifiutata**. 

    ![Endpoint rifiutato](./media/private-link-service/endpoint-rejected.png)


### <a name="remove-a-private-endpoint-connection"></a>Rimuovere una connessione endpoint privato

1. Per rimuovere una connessione endpoint privato, selezionarla nell'elenco e selezionare **Rimuovi** sulla barra degli strumenti. 

    ![Pulsante Rimuovi](./media/private-link-service/remove-endpoint.png)
2. Nella pagina **Elimina connessione** selezionare **Sì** per confermare l'eliminazione dell'endpoint privato. Se si seleziona **No**, non accade nulla. 

    ![Pagina Elimina connessione](./media/private-link-service/delete-connection-page.png)
3. Si noterà che lo stato è diventato **Disconnessa** e che l'endpoint non è più presente nell'elenco. 

## <a name="validate-that-the-private-link-connection-works"></a>Verificare il funzionamento della connessione di collegamento privato

È necessario verificare che le risorse all'interno della stessa subnet della risorsa endpoint privato si connettano allo spazio dei nomi del bus di servizio tramite un indirizzo IP privato e che abbiano l'integrazione con la zona DNS privato corretta.

Creare prima di tutto una macchina virtuale seguendo la procedura descritta nell'articolo [Creare una macchina virtuale di Windows nel portale di Azure](../virtual-machines/windows/quick-create-portal.md).

Nella scheda **Rete**: 

1. Specificare **Rete virtuale** e **Subnet**. È necessario selezionare la rete virtuale in cui è stato distribuito l'endpoint privato.
2. Specificare una risorsa **IP pubblico**.
3. Per **Gruppo di sicurezza di rete della scheda di interfaccia di rete** selezionare **Nessuno**.
4. Per **Bilanciamento del carico** selezionare **No**.

Connettersi alla macchina virtuale, aprire la riga di comando ed eseguire il comando seguente:

```console
nslookup <service-bus-namespace-name>.servicebus.windows.net
```

Verrà visualizzato un risultato simile al seguente. 

```console
Non-authoritative answer:
Name:    <service-bus-namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <service-bus-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Limitazioni e considerazioni di progettazione

**Pricing** (Prezzi): per informazioni sui prezzi, vedere [Prezzi di Collegamento privato di Azure](https://azure.microsoft.com/pricing/details/private-link/).

**Limitazioni**:  Questa funzionalità è disponibile in tutte le aree pubbliche di Azure.

**Numero massimo di endpoint privati per lo spazio dei nomi del bus di servizio**: 120.

Per altre informazioni, vedere [Servizio Collegamento privato di Azure: Limitazioni](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Collegamento privato di Azure](../private-link/private-link-service-overview.md)
- Altre informazioni sul [bus di servizio di Azure](service-bus-messaging-overview.md)
