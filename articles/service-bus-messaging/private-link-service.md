---
title: Integrare il bus di servizio di Azure con il servizio di collegamento privato di AzureIntegrate Azure Service Bus with Azure Private Link Service
description: Informazioni su come integrare il bus di servizio di Azure con il servizio di collegamento privato di AzureLearn how to integrate Azure Service Bus with Azure Private Link Service
services: service-bus-messaging
author: spelluru
ms.author: spelluru
ms.date: 03/13/2020
ms.service: service-bus-messaging
ms.topic: article
ms.openlocfilehash: b8c4248b7275ac96acce96f890f6ff0148116f48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478006"
---
# <a name="integrate-azure-service-bus-with-azure-private-link-preview"></a>Integrare il bus di servizio di Azure con Azure Private Link (anteprima)Integrate Azure Service Bus with Azure Private Link (Preview)

Il servizio di collegamento privato di Azure consente di accedere ai servizi di Azure(ad esempio, Bus di servizio di Azure, Archiviazione di Azure e database cosmodi di Azure) e i servizi cliente/partner ospitati di Azure su un **endpoint privato** nella rete virtuale.

A private endpoint is a network interface that connects you privately and securely to a service powered by Azure Private Link. L'endpoint privato usa un indirizzo IP privato della rete virtuale, introducendo efficacemente il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite l'endpoint privato, quindi non sono necessari gateway, dispositivi NAT, ExpressRoute o connessioni VPN oppure indirizzi IP pubblici. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. È possibile connettersi a un'istanza di una risorsa di Azure, garantendo il massimo livello di granularità nel controllo di accesso.

Per altre informazioni, vedere [Che cos'è Azure Private Link?](../private-link/private-link-overview.md)

> [!NOTE]
> Questa funzionalità è supportata con il livello Premium del bus di servizio di Azure.This feature is supported with the **premium** tier of Azure Service Bus. Per altre informazioni sul livello Premium, vedere l'articolo Livelli di messaggistica Service Bus Premium e Standard.For more information about the premium tier, see the [Service Bus Premium and Standard messaging tiers](service-bus-premium-messaging.md) article.
>
> Questa funzione è attualmente in **anteprima**. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Aggiungere un endpoint privato usando il portale di AzureAdd a private endpoint using Azure portal

### <a name="prerequisites"></a>Prerequisiti

Per integrare uno spazio dei nomi del bus di servizio con Azure Private Link, sono necessarie le entità o le autorizzazioni seguenti:To integrate a Service Bus namespace with Azure Private Link, you'll need the following entities or permissions:

- Uno spazio dei nomi del bus di servizio.
- Una rete virtuale di Azure.
- Una subnet nella rete virtuale.
- Autorizzazioni di proprietario o collaboratore sia per lo spazio dei nomi del bus di servizio che per la rete virtuale.

L'endpoint privato e la rete virtuale devono trovarsi nella stessa area. Quando si seleziona un'area per l'endpoint privato tramite il portale, verranno automaticamente filtrate solo le reti virtuali presenti in tale area. Lo spazio dei nomi del bus di servizio può trovarsi in un'area diversa. L'endpoint privato usa un indirizzo IP privato nella rete virtuale.

### <a name="steps"></a>steps

Se si dispone già di uno spazio dei nomi esistente, è possibile creare un endpoint privato attenendosi alla procedura seguente:If you already have an existing namespace, you can create a private endpoint by following these steps:

1. Accedere al [portale](https://portal.azure.com)di Azure . 
2. Nella barra di ricerca digitare **Bus di servizio**.
3. Selezionare **lo spazio dei nomi** dall'elenco a cui si desidera aggiungere un endpoint privato.
4. Selezionare la scheda **Rete** in **Impostazioni**.
5. Selezionare la scheda **Connessioni endpoint privati (anteprima)** nella parte superiore della pagina
6. Selezionare il pulsante **: Endpoint privato** nella parte superiore della pagina.

    ![Pulsante Aggiungi endpoint privato](./media/private-link-service/private-link-service-3.png)
7. Nella pagina **Nozioni di base,** attenersi alla seguente procedura: 
    1. Selezionare la sottoscrizione di **Azure** in cui si vuole creare l'endpoint privato. 
    2. Selezionare il **gruppo di risorse** per la risorsa endpoint privato.
    3. Immettere un **nome** per l'endpoint privato. 
    5. Selezionare **un'area** per l'endpoint privato. L'endpoint privato deve trovarsi nella stessa area della rete virtuale, ma può trovarsi in un'area diversa dalla risorsa collegamento privato a cui ci si connette. 
    6. Selezionare **Successivo: >risorsa** nella parte inferiore della pagina.

        ![Pagina Crea endpoint privato - Nozioni di base](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Nella pagina Risorsa eseguire la procedura seguente:On the **Resource** page, follow these steps:
    1. Per il metodo di connessione, se si seleziona Connetti a una **risorsa di Azure nella directory**personale, attenersi alla seguente procedura:   
        1. Selezionare la sottoscrizione di **Azure** in cui è presente **lo spazio dei nomi del bus** di servizio. 
        2. Per **Tipo di risorsa**, selezionare **Microsoft.ServiceBus/namespaces** per **Tipo di risorsa**.
        3. Per **Risorsa**, selezionare uno spazio dei nomi del bus di servizio dall'elenco a discesa. 
        4. Verificare che la **sottorisorsa Target** sia impostata sullo **spazio dei nomi**.
        5. Selezionare **Successivo: pulsante >della configurazione** nella parte inferiore della pagina. 
        
            ![Pagina Crea endpoint privato - Risorsa](./media/private-link-service/create-private-endpoint-resource-page.png)
    2. Se si seleziona Connetti a una **risorsa di Azure in base all'ID o all'alias**della risorsa, attenersi alla seguente procedura:
        1. Immettere l'ID o **l'alias**della **risorsa.** Può essere l'ID risorsa o l'alias che alcuni hanno condiviso con te.
        2. Per **la sottorisorsa Target**immettere **namespace**. È il tipo di sottorisorsa a cui può accedere l'endpoint privato. 
        3. (opzionale) Immettere un **messaggio di richiesta**. Il proprietario della risorsa vede questo messaggio durante la gestione della connessione all'endpoint privato. 
        4. Quindi, selezionare **Avanti: Configurazione >** pulsante nella parte inferiore della pagina. 

            ![Creare un endpoint privato - connettersi usando l'ID risorsaCreate Private Endpoint - connect using resource ID](./media/private-link-service/connect-resource-id.png)
9. Nella pagina **Configurazione** selezionare la subnet in una rete virtuale in cui si vuole distribuire l'endpoint privato. 
    1. Selezionare una **rete virtuale**. Nell'elenco a discesa sono elencate solo le reti virtuali nella sottoscrizione e nel percorso attualmente selezionate. 
    2. Selezionare una **subnet** nella rete virtuale selezionata. 
    3. Selezionare **Successivo: Tag >** pulsante nella parte inferiore della pagina. 

        ![Crea endpoint privato - Pagina Configurazione](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Nella pagina **Tag** creare i tag (nomi e valori) che si desidera associare alla risorsa endpoint privata. Quindi, seleziona il pulsante **Revisione e crea** nella parte inferiore della pagina. 
11. Nella scheda **Revisione e creazione**rivedere tutte le impostazioni e selezionare **Crea** per creare l'endpoint privato.
    
    ![Crea endpoint privato - Pagina Revisione e creazione](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Verificare che l'endpoint privato sia stato creato. Se si è il proprietario della risorsa e si è selezionato **l'opzione Connetti a una risorsa di Azure nella directory** per il metodo **Connection**, la connessione all'endpoint deve essere **approvata automaticamente.** Se si è nello stato **in sospeso,** vedere la sezione Gestire gli endpoint privati tramite il portale di Azure.If it's in the pending state, see the [Manage private endpoints using Azure portal section.](#manage-private-endpoints-using-azure-portal)

    ![Endpoint privato creato](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Aggiungere un endpoint privato tramite PowerShellAdd a private endpoint using PowerShell
L'esempio seguente illustra come usare Azure PowerShell per creare una connessione endpoint privata a uno spazio dei nomi del bus di servizio.

L'endpoint privato e la rete virtuale devono trovarsi nella stessa area. Lo spazio dei nomi del bus di servizio può trovarsi in un'area diversa. L'endpoint privato usa un indirizzo IP privato nella rete virtuale.

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
1. Nella barra di ricerca digitare **Bus di servizio**.
1. Selezionare lo **spazio dei nomi** che si desidera gestire.
1. Selezionare la scheda **Rete.Select** the Networking tab.
5. Passare alla sezione appropriata di seguito in base all'operazione desiderata: approvare, rifiutare o rimuovere. 

### <a name="approve-a-private-endpoint-connection"></a>Approvare una connessione endpoint privata

1. Se sono presenti connessioni in sospeso, verrà visualizzata una connessione elencata con **In sospeso** nello stato di provisioning. 
2. Selezionare **l'endpoint privato** che si desidera approvare
3. Selezionare il pulsante **Approva.**

    ![Approvare l'endpoint privato](./media/private-link-service/private-endpoint-approve.png)
4. Nella pagina **Approva connessione** immettere un **commento**facoltativo e selezionare **Sì**. Se si seleziona **No**, non accade nulla. 

    ![Approva pagina di connessione](./media/private-link-service/approve-connection-page.png)
5. Lo stato della connessione nell'elenco dovrebbe essere modificato in **Approvato**. 

    ![Stato della connessione - approvato](./media/private-link-service/connection-status-approved.png)

### <a name="reject-a-private-endpoint-connection"></a>Rifiutare una connessione endpoint privataReject a private endpoint connection

1. Se sono presenti connessioni endpoint private che si desidera rifiutare, indipendentemente dal fatto che si tratti di una richiesta in sospeso o di una connessione esistente che è stata approvata in precedenza, selezionare la connessione endpoint e fare clic sul pulsante **Rifiuta.**

    ![Pulsante Rifiuta](./media/private-link-service/private-endpoint-reject.png)
2. Nella pagina **Rifiuta connessione** immettere un commento facoltativo e selezionare **Sì**. Se si seleziona **No**, non accade nulla. 

    ![Pagina Di rifiuto connessione](./media/private-link-service/reject-connection-page.png)
3. Lo stato della connessione dovrebbe essere modificato **nell'elenco Rifiutato**. 

    ![Endpoint rifiutato](./media/private-link-service/endpoint-rejected.png)


### <a name="remove-a-private-endpoint-connection"></a>Rimuovere una connessione endpoint privataRemove a private endpoint connection

1. Per rimuovere una connessione all'endpoint privato, selezionarla nell'elenco e selezionare **Rimuovi** sulla barra degli strumenti. 

    ![Pulsante Rimuovi](./media/private-link-service/remove-endpoint.png)
2. Nella pagina **Elimina connessione** selezionare **Sì** per confermare l'eliminazione dell'endpoint privato. Se si seleziona **No**, non accade nulla. 

    ![Elimina pagina di connessione](./media/private-link-service/delete-connection-page.png)
3. Lo stato dovrebbe essere modificato in **Disconnesso**. Quindi, si vedrà l'endpoint scomparire dall'elenco. 

## <a name="validate-that-the-private-link-connection-works"></a>Verificare il funzionamento della connessione di collegamento privato

È necessario verificare che le risorse all'interno della stessa subnet della risorsa dell'endpoint privato si connettono allo spazio dei nomi del bus di servizio tramite un indirizzo IP privato e che dispongano dell'integrazione della zona DNS privata corretta.

Creare prima di tutto una macchina virtuale seguendo la procedura descritta nell'articolo [Creare una macchina virtuale di Windows nel portale di Azure](../virtual-machines/windows/quick-create-portal.md).

Nella scheda **Rete:**

1. Specificare **Rete virtuale** e **Subnet**. È possibile creare una nuova macchina virtuale o selezionarne una esistente. Se si seleziona una macchina virtuale esistente, assicurarsi che l'area corrisponda.
1. Specificare una risorsa **IP pubblica.**
1. Per gruppo di sicurezza di **rete NIC**, **selezionare Nessuno**.
1. Per **Bilanciamento del carico**, selezionare **No**.

Aprire la riga di comando ed eseguire il comando seguente:

```console
nslookup <your-service-bus-namespace-name>.servicebus.windows.net
```

Se si esegue il comando di ricerca ns per risolvere l'indirizzo IP di uno spazio dei nomi del bus di servizio su un endpoint pubblico, verrà visualizzato un risultato simile al seguente:

```console
c:\ >nslookup <your-service-bus-namespace-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-service-bus-namespace-name>.servicebus.windows.net
```

Se si esegue il comando di ricerca ns per risolvere l'indirizzo IP di uno spazio dei nomi del bus di servizio su un endpoint privato, verrà visualizzato un risultato simile al seguente:

```console
c:\ >nslookup your_service-bus-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-service-bus-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Limitazioni e considerazioni di progettazione

**Prezzi**: Per informazioni sui prezzi, vedere Prezzi di [Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/).

**Limitazioni:** l'endpoint privato per il bus di servizio di Azure è in anteprima pubblica. Questa funzionalità è disponibile in tutte le aree pubbliche di Azure.

**Numero massimo di endpoint privati per spazio dei nomi del bus**di servizio: 120.Maximum number of private endpoints per Service Bus namespace : 120.

Per altre informazioni, vedere [Servizio Di Azure Private Link: LimitazioniFor](../private-link/private-link-service-overview.md#limitations) more, see Azure Private Link service: Limitations

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Collegamento privato di Azure](../private-link/private-link-service-overview.md)
- Altre informazioni sul bus di servizio di [AzureLearn](service-bus-messaging-overview.md) more about Azure Service Bus
