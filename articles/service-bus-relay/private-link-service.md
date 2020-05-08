---
title: Integrare l'inoltro di Azure con il servizio di collegamento privato di Azure
description: Informazioni su come integrare il servizio di inoltro di Azure con il servizio di collegamento privato di Azure
services: service-bus-relay
author: spelluru
ms.author: spelluru
ms.date: 05/07/2020
ms.service: service-bus-relay
ms.topic: article
ms.openlocfilehash: ad57ea4ddeab2fb2af0da68d199445d9737a67cd
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984460"
---
# <a name="integrate-azure-relay-with-azure-private-link-preview"></a>Integrare il relè di Azure con collegamento privato di Azure (anteprima)
Il **servizio di collegamento privato** di Azure consente di accedere ai servizi di Azure (ad esempio inoltro di Azure, bus di servizio di Azure, Hub eventi di Azure, archiviazione di azure e Azure Cosmos DB) e ai servizi cliente/partner ospitati di Azure tramite un endpoint privato nella rete virtuale. Per altre informazioni, vedere [Che cos'è Collegamento privato di Azure? (Anteprima)](../private-link/private-link-overview.md).

Un **endpoint privato** è un'interfaccia di rete che consente ai carichi di lavoro in esecuzione in una rete virtuale di connettersi privatamente e in modo sicuro a un servizio che dispone di una **risorsa di collegamento privato** (ad esempio, uno spazio dei nomi di inoltro). L'endpoint privato usa un indirizzo IP privato della rete virtuale, introducendo efficacemente il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite l'endpoint privato, quindi non sono necessari gateway, dispositivi NAT, ExpressRoute, connessioni VPN o indirizzi IP pubblici. Il traffico tra la rete virtuale e il servizio attraversa la rete dorsale Microsoft eliminando l'esposizione dalla rete Internet pubblica. È possibile fornire un livello di granularità nel controllo di accesso consentendo le connessioni a spazi dei nomi di inoltro di Azure specifici. 


> [!IMPORTANT]
> Questa funzionalità è attualmente disponibile in **Anteprima**. 
>
> Attualmente sono supportate le connessioni di collegamento privato nei client mittente. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Aggiungere un endpoint privato utilizzando portale di Azure

### <a name="prerequisites"></a>Prerequisiti
Per integrare uno spazio dei nomi di inoltro di Azure con collegamento privato di Azure (anteprima), sono necessarie le entità o le autorizzazioni seguenti:

- Uno spazio dei nomi di inoltro di Azure.
- Una rete virtuale di Azure.
- Una subnet nella rete virtuale.
- Autorizzazioni di proprietario o collaboratore per la rete virtuale.

L'endpoint privato e la rete virtuale devono trovarsi nella stessa area. Quando si seleziona un'area per l'endpoint privato tramite il portale, verranno automaticamente filtrate solo le reti virtuali presenti in tale area. Lo spazio dei nomi può trovarsi in un'area diversa.

L'endpoint privato usa un indirizzo IP privato nella rete virtuale.

### <a name="steps"></a>Passaggi
Per istruzioni dettagliate sulla creazione di un nuovo spazio dei nomi e delle entità di inoltro di Azure, vedere [creare uno spazio dei nomi di inoltro di Azure usando il portale di Azure](relay-create-namespace-portal.md).

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Nella barra di ricerca digitare **inoltri**.
3. Selezionare lo **spazio dei nomi** dall'elenco a cui si desidera aggiungere un endpoint privato.
4. Selezionare la scheda **rete** in **Impostazioni**.
5. Selezionare la scheda **connessioni endpoint privato (anteprima)** nella parte superiore della pagina
6. Selezionare il pulsante **+ endpoint privato** nella parte superiore della pagina.

    ![Pulsante Aggiungi endpoint privato](./media/private-link-service/add-private-endpoint-button.png)
7. Nella pagina **nozioni di base** seguire questa procedura: 
    1. Selezionare la **sottoscrizione di Azure** in cui si vuole creare l'endpoint privato. 
    2. Selezionare il **gruppo di risorse** per la risorsa endpoint privato.
    3. Immettere un **nome** per l'endpoint privato. 
    5. Selezionare un' **area** per l'endpoint privato. L'endpoint privato deve trovarsi nella stessa area della rete virtuale, ma può trovarsi in un'area diversa dallo spazio dei nomi di inoltro di Azure a cui ci si connette. 
    6. Fare clic su **Next: Resource >** Button nella parte inferiore della pagina.

        ![Crea endpoint privato-pagina Nozioni di base](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Nella pagina **delle risorse** , attenersi alla seguente procedura:
    1. Per metodo di connessione, se si seleziona **Connetti a una risorsa di Azure nella directory**, si ha accesso proprietario o collaboratore allo spazio dei nomi e tale spazio dei nomi si trova nella stessa directory dell'endpoint privato, attenersi alla procedura seguente: 
        1. Selezionare la **sottoscrizione di Azure** in cui è presente lo **spazio dei nomi di inoltro di Azure** . 
        2. Per **tipo di risorsa**selezionare **Microsoft. Relay/Namespaces** per il **tipo di risorsa**.
        3. Per **risorsa**selezionare uno spazio dei nomi di inoltro dall'elenco a discesa. 
        4. Verificare che la **sottorisorsa di destinazione** sia impostata su **spazio dei nomi**.
        5. Fare clic su **Next: Configuration >** Button nella parte inferiore della pagina. 
        
            ![Crea endpoint privato-pagina delle risorse](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Se si seleziona **Connetti a una risorsa di Azure in base all'ID risorsa o alias** perché lo spazio dei nomi non si trova nella stessa directory dell'endpoint privato, attenersi alla procedura seguente:
        1. Immettere l' **ID risorsa** o l' **alias**. Può trattarsi dell'ID risorsa o dell'alias che qualcuno ha condiviso con l'utente. Il modo più semplice per ottenere l'ID risorsa è passare allo spazio dei nomi di inoltro di Azure nel portale di Azure e copiare la parte dell'URI `/subscriptions/`a partire da. Di seguito è riportato un esempio:`/subscriptions/000000000-0000-0000-0000-000000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Relay/namespaces/myrelaynamespace.` 
        2. Per **risorsa secondaria di destinazione**immettere **spazio dei nomi**. È il tipo della sottorisorsa a cui l'endpoint privato può accedere.
        3. opzionale Immettere un **messaggio di richiesta**. Il proprietario della risorsa Visualizza questo messaggio durante la gestione della connessione all'endpoint privato.
        4. Quindi, fare clic su **Next: Configuration >** pulsante nella parte inferiore della pagina.

            ![Crea endpoint privato-Connetti usando l'ID risorsa](./media/private-link-service/connect-resource-id.png)
9. Nella pagina **configurazione** selezionare la subnet in una rete virtuale in cui si vuole distribuire l'endpoint privato. 
    1. Selezionare una **rete virtuale**. Nell'elenco a discesa sono elencate solo le reti virtuali nella sottoscrizione e nella località attualmente selezionate. 
    2. Selezionare una **subnet** nella rete virtuale selezionata. 
    3. Abilitare l' **integrazione con la zona DNS privata** se si vuole integrare l'endpoint privato con una zona DNS privata. 
    
        Per connettersi privatamente all'endpoint privato, è necessario un record DNS. Si consiglia di integrare l'endpoint privato con una **zona DNS privata**. È anche possibile usare i propri server DNS o creare record DNS usando i file host delle macchine virtuali. Per altre informazioni, vedere [configurazione DNS dell'endpoint privato di Azure](../private-link/private-endpoint-dns.md). In questo esempio, l'opzione **integra con la zona DNS privata** è selezionata e viene creata automaticamente una zona DNS privata. 
    3. Selezionare **Avanti: tag >** pulsante nella parte inferiore della pagina. 

        ![Crea endpoint privato-pagina Configurazione](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Nella pagina **tag** creare eventuali tag (nomi e valori) che si desidera associare all'endpoint privato e alla zona DNS privata, se è stata abilitata l'opzione. Selezionare quindi il pulsante **Rivedi e crea** nella parte inferiore della pagina. 
11. In **Verifica e crea**esaminare tutte le impostazioni e selezionare **Crea** per creare l'endpoint privato.
    
    ![Crea endpoint privato-verifica e Crea pagina](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Nella pagina **endpoint privato** è possibile visualizzare lo stato della connessione all'endpoint privato. Se si è il proprietario dello spazio dei nomi di inoltro o si ha la possibilità di gestire l'accesso ed è stata selezionata l'opzione **Connetti a una risorsa di Azure nella directory** per il **metodo di connessione**, la connessione all'endpoint deve essere **approvata automaticamente**. Se è nello stato in **sospeso** , vedere la sezione [gestire endpoint privati con portale di Azure](#manage-private-endpoints-using-azure-portal) .

    ![Pagina Endpoint privato](./media/private-link-service/private-endpoint-page.png)
13. Tornare alla pagina **rete** dello **spazio dei nomi**e passare alla scheda **connessioni endpoint privato (anteprima)** . Verrà visualizzato l'endpoint privato creato. 

    ![Endpoint privato creato](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Aggiungere un endpoint privato usando PowerShell
L'esempio seguente illustra come usare Azure PowerShell per creare una connessione di un endpoint privato a uno spazio dei nomi di inoltro di Azure.

L'endpoint privato e la rete virtuale devono trovarsi nella stessa area. Lo spazio dei nomi di inoltro di Azure può trovarsi in un'area diversa. L'endpoint privato usa un indirizzo IP privato nella rete virtuale.

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

# create a relay namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Properties @{} -ResourceType "Microsoft.Relay/namespaces" 

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

## <a name="manage-private-endpoints-using-azure-portal"></a>Gestire endpoint privati con portale di Azure

Quando si crea un endpoint privato, la connessione deve essere approvata. Se la risorsa (spazio dei nomi di inoltro) per cui si sta creando un endpoint privato si trova nella directory, è possibile approvare la richiesta di connessione purché si disponga di privilegi di gestione sullo spazio dei nomi di inoltro. Se ci si connette a uno spazio dei nomi di inoltro per il quale non si dispone dell'accesso di gestione, è necessario attendere che il proprietario della risorsa approvi la richiesta di connessione.

Sono disponibili quattro stati di provisioning:

| Azione del servizio | Stato dell'endpoint privato del consumer del servizio | Descrizione |
|--|--|--|
| nessuno | In sospeso | La connessione viene creata manualmente ed è in attesa di approvazione dal proprietario dello spazio dei nomi di inoltro di Azure. |
| Approvazione | Approved | La connessione è stata approvata automaticamente o manualmente ed è pronta per essere usata. |
| Rifiuto | Rifiutato | La connessione è stata rifiutata dal proprietario dello spazio dei nomi di inoltro di Azure. |
| Rimuovere | Disconnesso | La connessione è stata rimossa dal proprietario dello spazio dei nomi di inoltro di Azure, l'endpoint privato diventa informativo e deve essere eliminato per la pulizia. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Approva, rifiuta o Rimuovi una connessione all'endpoint privato

1. Accedere al portale di Azure.
1. Nella barra di ricerca digitare **Relay**.
1. Selezionare lo **spazio dei nomi** che si desidera gestire.
1. Selezionare la scheda **rete** .
5. Passare alla sezione appropriata riportata di seguito in base all'operazione desiderata: approva, rifiuta o Rimuovi. 

### <a name="approve-a-private-endpoint-connection"></a>Approva una connessione all'endpoint privato

1. Se sono presenti connessioni in sospeso, verrà visualizzata una connessione elencata con in **sospeso** nello stato di provisioning. 
2. Selezionare l' **endpoint privato** che si vuole approvare
3. Selezionare il pulsante **approva** .

    ![Approva endpoint privato](./media/private-link-service/private-endpoint-approve.png)
4. Nella pagina **approva connessione** immettere un **Commento**facoltativo e selezionare **Sì**. Se si seleziona **No**, non viene eseguita alcuna operazione. 

    ![Pagina approva connessione](./media/private-link-service/approve-connection-page.png)
5. Si noterà che lo stato della connessione nell'elenco è stato modificato in **approvato**.

### <a name="reject-a-private-endpoint-connection"></a>Rifiutare una connessione all'endpoint privato

1. Se sono presenti connessioni di endpoint privato che si desidera rifiutare, sia che si tratti di una richiesta in sospeso o di una connessione esistente approvata in precedenza, selezionare la connessione all'endpoint e fare clic sul pulsante **rifiuta** .

    ![Pulsante rifiuta](./media/private-link-service/private-endpoint-reject.png)
2. Nella pagina **rifiuta connessione** immettere un commento facoltativo e selezionare **Sì**. Se si seleziona **No**, non viene eseguita alcuna operazione. 

    ![Pagina rifiuta connessione](./media/private-link-service/reject-connection-page.png)
3. Verrà visualizzato lo stato della connessione nell'elenco modificato **rifiutato**.


### <a name="remove-a-private-endpoint-connection"></a>Rimuovere una connessione all'endpoint privato

1. Per rimuovere una connessione all'endpoint privato, selezionarla nell'elenco e selezionare **Rimuovi** sulla barra degli strumenti. 

    ![Pulsante Rimuovi](./media/private-link-service/remove-endpoint.png)
2. Nella pagina **Elimina connessione** selezionare **Sì** per confermare l'eliminazione dell'endpoint privato. Se si seleziona **No**, non viene eseguita alcuna operazione. 

    ![Pagina Elimina connessione](./media/private-link-service/delete-connection-page.png)
3. Si noterà che lo stato è cambiato in **disconnected**. Si noterà quindi che l'endpoint scompare dall'elenco. 

## <a name="validate-that-the-private-link-connection-works"></a>Verificare il funzionamento della connessione di collegamento privato
È necessario verificare che le risorse all'interno della stessa subnet dell'endpoint privato si connettano allo spazio dei nomi di inoltro di Azure tramite l'indirizzo IP privato.

Per questo test, creare una macchina virtuale seguendo i passaggi descritti in [creare una macchina virtuale Windows nel portale di Azure](../virtual-machines/windows/quick-create-portal.md)

Nella scheda **rete** : 

1. Specificare la **rete virtuale** e la **Subnet**. È necessario selezionare la rete virtuale in cui è stato distribuito l'endpoint privato.
2. Specificare una risorsa **IP pubblica** .
3. Per **gruppo di sicurezza di rete NIC**selezionare **nessuno**.
4. Per il **bilanciamento del carico**, selezionare **No**.

Connettersi alla macchina virtuale e aprire la riga di comando ed eseguire il comando seguente:

```console
nslookup <your-relay-namespace-name>.servicebus.windows.net
```

Verrà visualizzato un risultato simile al seguente. 

```console
Non-authoritative answer:
Name:    <namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Limitazioni e considerazioni di progettazione

### <a name="design-considerations"></a>Considerazioni sulla progettazione
- L'endpoint privato per il relay di Azure è in versione di **anteprima pubblica**. 
- per informazioni sui prezzi, vedere [Prezzi di Collegamento privato di Azure (Anteprima)](https://azure.microsoft.com/pricing/details/private-link/).

### <a name="limitations"></a>Limitazioni 
- Numero massimo di endpoint privati per ogni spazio dei nomi di inoltro di Azure: 64.
- Numero massimo di spazi dei nomi di inoltro di Azure con endpoint privati per sottoscrizione: 64.
- Le regole del gruppo di sicurezza di rete (NSG) e le route definite dall'utente non si applicano all'endpoint privato. Per altre informazioni, vedere [servizio di collegamento privato di Azure: limitazioni](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Collegamento privato di Azure (anteprima)](../private-link/private-link-service-overview.md)
- Altre informazioni sul [relè di Azure](relay-what-is-it.md)
