---
title: Configurare gli endpoint privati per gli argomenti o i domini di Griglia di eventi di AzureConfigure private endpoints for Azure Event Grid topics or domains
description: Questo articolo descrive come configurare gli endpoint privati per gli argomenti o il dominio di Griglia di eventi di Azure.This article describes how to configure private endpoints for Azure Event Grid topics or domain.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: d08afe00c13a3f96b9526c3cb29804cfad688ddc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299907"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains-preview"></a>Configurare gli endpoint privati per gli argomenti o i domini di Griglia di eventi di Azure (anteprima)Configure private endpoints for Azure Event Grid topics or domains (Preview)
È possibile usare [gli endpoint privati](../private-link/private-endpoint-overview.md) per consentire l'ingresso degli eventi direttamente dalla rete virtuale agli argomenti e ai domini in modo sicuro tramite un collegamento [privato](../private-link/private-link-overview.md) senza passare attraverso la rete Internet pubblica. L'endpoint privato usa un indirizzo IP dello spazio di indirizzi della rete virtuale per l'argomento o il dominio. Per ulteriori informazioni concettuali, vedere [Sicurezza di rete](network-security.md).

In questo articolo viene descritto come configurare gli endpoint privati per argomenti o domini.

> [!IMPORTANT]
> La funzionalità degli endpoint privati è disponibile per gli argomenti e i domini solo nel livello Premium.Private endpoints feature is available for topics and domains only in premium tier. Per eseguire l'aggiornamento dal livello di base al livello Premium, vedere l'articolo Aggiornare il [piano tariffario.](update-tier.md) 

## <a name="use-azure-portal"></a>Usare il portale di Azure 
Questa sezione illustra come usare il portale di Azure per creare un endpoint privato per un argomento o un dominio.

> [!NOTE]
> I passaggi illustrati in questa sezione riguardano principalmente argomenti. È possibile utilizzare passaggi simili per creare endpoint privati per i **domini.** 

1. Accedere al [portale](https://portal.azure.com) di Azure e passare all'argomento o al dominio.
2. Passare alla scheda **Rete** della pagina dell'argomento. Sulla barra degli strumenti, **selezionare -Endpoint privato.**

    ![Aggiungere un endpoint privato](./media/configure-private-endpoints/add-button.png)
2. Una pagina **Nozioni di base,** attenersi alla seguente procedura: 
    1. Selezionare una sottoscrizione di **Azure** in cui si vuole creare l'endpoint privato. 
    2. Selezionare un gruppo di risorse di **Azure** per l'endpoint privato. 
    3. Immettere un **nome** per l'endpoint. 
    4. Selezionare **l'area** per l'endpoint. L'endpoint privato deve trovarsi nella stessa area della rete virtuale, ma può trovarsi in un'area diversa dalla risorsa a collegamento privato (in questo esempio, un argomento della griglia di eventi). 
    5. Quindi, selezionare **Avanti: risorsa >** pulsante nella parte inferiore della pagina. 

      ![Endpoint privato - pagina delle nozioni di basePrivate endpoint - basics page](./media/configure-private-endpoints/basics-page.png)
3. Nella pagina Risorsa eseguire la procedura seguente:On the **Resource** page, follow these steps: 
    1. Per il metodo di connessione, se si seleziona **Connetti a una risorsa di Azure nella directory**personale, attenersi alla seguente procedura. Questo esempio mostra come connettersi a una risorsa di Azure nella directory. 
        1. Selezionare la sottoscrizione di **Azure** in cui è presente **l'argomento o** il dominio. 
        1. Per **Tipo di risorsa**, selezionare **Microsoft.EventGrid/topics** o **Microsoft.EventGrid/domains** per tipo **di risorsa**.
        2. Per **Risorsa**, selezionare un argomento o un dominio dall'elenco a discesa. 
        3. Verificare che la **sottorisorsa Destinazione** sia impostata su **argomento** o **dominio** (in base al tipo di risorsa selezionato).    
        4. Selezionare **Successivo: pulsante >della configurazione** nella parte inferiore della pagina. 

            ![Endpoint privato - pagina delle risorse](./media/configure-private-endpoints/resource-page.png)
    2. Se si seleziona **Connetti a una risorsa utilizzando un ID risorsa o un alias,** attenersi alla seguente procedura:
        1. Immettere l'ID della risorsa. Ad esempio `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`.  
        2. Per **Risorsa**immettere **argomento** o **dominio**. 
        3. (opzionale) Aggiungere un messaggio di richiesta. 
        4. Selezionare **Successivo: pulsante >della configurazione** nella parte inferiore della pagina. 

            ![Endpoint privato - pagina delle risorse](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. Nella pagina **Configurazione** selezionare la subnet in una rete virtuale in cui si vuole distribuire l'endpoint privato. 
    1. Selezionare una **rete virtuale**. Nell'elenco a discesa sono elencate solo le reti virtuali nella sottoscrizione e nel percorso attualmente selezionate. 
    2. Selezionare una **subnet** nella rete virtuale selezionata. 
    3. Selezionare **Successivo: Tag >** pulsante nella parte inferiore della pagina. 

    ![Endpoint privato - pagina di configurazione](./media/configure-private-endpoints/configuration-page.png)
5. Nella pagina **Tag** creare i tag (nomi e valori) che si desidera associare alla risorsa endpoint privata. Quindi, seleziona il pulsante **Revisione e crea** nella parte inferiore della pagina. 
6. Nella scheda **Revisione e creazione**rivedere tutte le impostazioni e selezionare **Crea** per creare l'endpoint privato. 

    ![Endpoint privato - esaminare & pagina di creazione](./media/configure-private-endpoints/review-create-page.png)
    

## <a name="manage-private-link-connection"></a>Gestire una connessione di collegamento privato

Quando si crea un endpoint privato, la connessione deve essere approvata. Se la risorsa per la quale si sta creando un endpoint privato si trova nella directory, è possibile approvare la richiesta di connessione purché si disponga di autorizzazioni sufficienti. Se ci si connette a una risorsa di Azure in un'altra directory, è necessario attendere che il proprietario della risorsa approvi la richiesta di connessione.

Sono disponibili quattro stati di provisioning:

| Azione di servizio | Stato dell'endpoint privato del consumer del servizio | Descrizione |
|--|--|--|
| nessuno | In sospeso | La connessione viene creata manualmente ed è in attesa di approvazione da parte del proprietario della risorsa Link privata. |
| Approvazione | Approved | La connessione è stata approvata automaticamente o manualmente ed è pronta per essere usata. |
| Rifiuto | Rifiutato | La connessione è stata rifiutata dal proprietario della risorsa di collegamento privato. |
| Rimuovere | Disconnesso | La connessione è stata rimossa dal proprietario della risorsa di collegamento privato, l'endpoint privato diventa informativo e deve essere eliminato per la pulizia. |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>Come gestire una connessione all'endpoint privatoHow to manage a private endpoint connection
Nelle sezioni seguenti viene illustrato come approvare o rifiutare una connessione all'endpoint privato. 

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Nella barra di ricerca digitare **Argomenti griglia di eventi** o Domini griglia di **eventi**.
1. Selezionare **l'argomento** o il **dominio** che si desidera gestire.
1. Selezionare la scheda **Rete.Select** the Networking tab.
1. Se sono presenti connessioni in sospeso, verrà visualizzata una connessione elencata con **In sospeso** nello stato di provisioning. 

### <a name="to-approve-a-private-endpoint"></a>Per approvare un endpoint privato
È possibile approvare un endpoint privato nello stato in sospeso. Per approvare, attenersi alla seguente procedura: 

> [!NOTE]
> I passaggi illustrati in questa sezione riguardano principalmente argomenti. È possibile utilizzare passaggi simili per approvare gli endpoint privati per i **domini.** 

1. Selezionare **l'endpoint privato** che si desidera approvare e selezionare **Approva** sulla barra degli strumenti.

    ![Endpoint privato - stato in sospeso](./media/configure-private-endpoints/pending.png)
1. Nella finestra di dialogo **Approva connessione** immettere un commento (facoltativo) e selezionare **Sì**. 

    ![Endpoint privato - approva](./media/configure-private-endpoints/approve.png)
1. Verificare che lo stato dell'endpoint sia **approvato.** 

    ![Endpoint privato - stato approvato](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>Per rifiutare un endpoint privatoTo reject a private endpoint
È possibile rifiutare un endpoint privato che si trovi nello stato in sospeso o approvato. Per rifiutare, attenersi alla seguente procedura: 

> [!NOTE]
> I passaggi illustrati in questa sezione sono relativi agli argomenti. È possibile utilizzare passaggi simili per rifiutare gli endpoint privati per i **domini.** 

1. Selezionare **l'endpoint privato** che si desidera rifiutare e selezionare **Rifiuta** sulla barra degli strumenti.

    ![Endpoint privato - rifiuta](./media/configure-private-endpoints/reject-button.png)
1. Nella finestra di dialogo **Rifiuta connessione** immettere un commento (facoltativo) e selezionare **Sì**. 

    ![Endpoint privato - rifiuta](./media/configure-private-endpoints/reject.png)
1. Verificare che lo stato dell'endpoint sia **Rifiutato.** 

    ![Endpoint privato - stato rifiutato](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > Non è possibile approvare un endpoint privato nel portale di Azure dopo che è stato rifiutato. 


## <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
Per creare un endpoint privato, usare il metodo az network private-endpoint create come illustrato nell'esempio seguente:To create a private endpoint, use the [az network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) method as shown in the following example:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

Per una descrizione dei parametri utilizzati nell'esempio, vedere la documentazione relativa alla [rete az private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create). Alcuni punti da notare in questo esempio sono: 

- Per `private-connection-resource-id`, specificare l'ID risorsa **dell'argomento** o del **dominio**. Nell'esempio precedente viene utilizzato l'argomento type:.
- per `group-ids`, `topic` `domain`specificare o . Nell'esempio precedente, `topic` viene utilizzato. 

Per eliminare un endpoint privato, usare il metodo az network private-endpoint delete come illustrato nell'esempio seguente:To delete a private endpoint, use the [az network private-endpoint delete](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-delete) method as shown in the following example:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> I passaggi illustrati in questa sezione sono relativi agli argomenti. È possibile utilizzare passaggi simili per creare endpoint privati per i **domini.** 

### <a name="create-a-private-endpoint"></a>Creare un endpoint privato
Ecco uno script di esempio che crea le risorse di Azure seguenti:Here's a sample script that creates the following Azure resources:

- Resource group
- Rete virtuale
- Subnet nella rete virtuale
- Argomento Griglia di eventi di Azure (livello Premium)Azure Event Grid topic (premium tier)
- Endpoint privato per l'argomento

> [!NOTE]
> I passaggi illustrati in questa sezione sono relativi agli argomenti. È possibile utilizzare passaggi simili per creare endpoint privati per i domini.

```azurecli-interactive
subscriptionID="<AZURE SUBSCRIPTION ID>"
resourceGroupName="<RESOURCE GROUP NAME>"
location="<LOCATION>"
vNetName="<VIRTUAL NETWORK NAME>"
subNetName="<SUBNET NAME>"
topicName = "<TOPIC NAME>"
connectionName="<ENDPOINT CONNECTION NAME>"
endpointName=<ENDPOINT NAME>

# URI for the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicUri="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>?api-version=2020-04-01-preview"

# resource ID of the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicResourceID="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>"

# select subscription
az account set --subscription $subscriptionID

# create resource group
az group create --name $resourceGroupName --location $location

# create vnet 
az network vnet create \
    --resource-group $resourceGroupName \
    --name $vNetName \
    --address-prefix 10.0.0.0/16

# create subnet
az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --address-prefixes 10.0.0.0/24

# disable private endpoint network policies for the subnet
az network vnet subnet update \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --disable-private-endpoint-network-policies true

# create event grid topic. update <LOCATION>
az rest --method put \
    --uri $topicUri \
    --body "{\""location\"":\""LOCATION\"", \""sku\"": {\""name\"": \""premium\""}, \""properties\"": {\""publicNetworkAccess\"":\""Disabled\""}}"

# verify that the topic was created.
az rest --method get \
    --uri $topicUri

# create private endpoint for the topic you created
az network private-endpoint create 
    --resource-group $resourceGroupName \
    --name $endpointName \
    --vnet-name $vNetName \
    --subnet $subNetName \
    --private-connection-resource-id $topicResourceID \
    --connection-name $connectionName \
    --location $location \
    --group-ids topic

# get topic 
az rest --method get \
    --uri $topicUri

```

### <a name="approve-a-private-endpoint-connection"></a>Approvare una connessione endpoint privata
Il frammento dell'interfaccia della riga di comando di esempio seguente illustra come approvare una connessione all'endpoint privato. 

```azurecli-interactive
az rest --method put --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" --body "{\""properties\"":{\""privateLinkServiceConnectionState\"": {\""status\"":\""approved\"",\""description\"":\""connection approved\"", \""actionsRequired\"": \""none\""}}}"
```


### <a name="reject-a-private-endpoint-connection"></a>Rifiutare una connessione endpoint privataReject a private endpoint connection
Il frammento dell'interfaccia della riga di comando di esempio seguente illustra come rifiutare una connessione all'endpoint privato. 

```azurecli-interactive
az rest --method put --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" --body "{\""properties\"":{\""privateLinkServiceConnectionState\"": {\""status\"":\""rejected\"",\""description\"":\""connection rejected\"", \""actionsRequired\"": \""none\""}}}"
```


## <a name="use-powershell"></a>Usare PowerShell
In questa sezione viene illustrato come creare un endpoint privato per un argomento o un dominio tramite PowerShell.This section shows you how to create a private endpoint for a topic or domain using PowerShell. 

### <a name="prerequisite"></a>Prerequisito
Seguire le istruzioni da [How to: Use the portal to create an Azure AD application and service principal that can access resources](../active-directory/develop/howto-create-service-principal-portal.md) to create an Azure Active Directory application and note down the values for Directory **(tenant) ID**, Application **(Client) ID,** and **Application (client) secret**. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Preparare token e intestazioni per le chiamate all'API RESTPrepare token and headers for REST API calls 
Eseguire i comandi dei prerequisiti seguenti per ottenere un token di autenticazione da usare con le chiamate e l'autorizzazione dell'API REST e altre informazioni di intestazione. 

```azurepowershell-interactive
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get authentication token
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token  `
    -Body $body  `
    -ContentType 'application/x-www-form-urlencoded' 

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
```

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>Creare una subnet con criteri di rete endpoint disabilitatiCreate a subnet with endpoint network policies disabled

```azurepowershell-interactive

# create resource group
New-AzResourceGroup -ResourceGroupName <RESOURCE GROUP NAME>  -Location <LOCATION>

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName <RESOURCE GROUP NAME> `
                    -Location <LOCATION> `
                    -Name <VIRTUAL NETWORK NAME> `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name example-privatelinksubnet `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork
```

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>Creare un argomento della griglia di eventi con un endpoint privatoCreate an event grid topic with a private endpoint

> [!NOTE]
> I passaggi illustrati in questa sezione sono relativi agli argomenti. È possibile utilizzare passaggi simili per creare endpoint privati per i **domini.** 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="premium"}; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview"   `
    -Headers $Headers  

# create private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name "<PRIVATE LINK SERVICE CONNECTION NAME>" `
                                -PrivateLinkServiceId $topic.Id `
                                -GroupId "topic"

# get virtual network info 
$virtualNetwork = Get-AzVirtualNetwork  `
                    -ResourceGroupName  <RESOURCE GROUP NAME> `
                    -Name <VIRTUAL NETWORK NAME>

# get subnet info
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                             | Where-Object  {$_.Name -eq <SUBNET NAME>}  

# now, you are ready to create a private endpoint 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName <RESOURCE GROUP NAME>  `
                                        -Name <PRIVATE ENDPOINT NAME>   `
                                        -Location <LOCATION> `
                                        -Subnet  $subnet   `
                                        -PrivateLinkServiceConnection $privateEndpointConnection

# verify that the endpoint was created
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-04-01-preview"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

Quando si verifica che l'endpoint è stato creato, verrà visualizzato il risultato simile al seguente:When you verify that the endpoint was created, you should see the result similar to the following:

```json

{
  "value": [
    {
      "properties": {
        "privateEndpoint": {
          "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Network/privateEndpoints/<PRIVATE ENDPOINT NAME>"
        },
        "groupIds": [
          "topic"
        ],
        "privateLinkServiceConnectionState": {
          "status": "Approved",
          "description": "Auto-approved",
          "actionsRequired": "None"
        },
        "provisioningState": "Succeeded"
      },
      "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>",
      "name": "myConnection",
      "type": "Microsoft.EventGrid/topics/privateEndpointConnections"
    }
  ]
}
```

### <a name="approve-a-private-endpoint-connection"></a>Approvare una connessione endpoint privata
Il frammento di codice di PowerShell di esempio seguente illustra come approvare un endpoint privato. 

> [!NOTE]
> I passaggi illustrati in questa sezione sono relativi agli argomenti. È possibile utilizzare passaggi simili per approvare gli endpoint privati per i **domini.** 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>Rifiutare una connessione endpoint privataReject a private endpoint connection
Nell'esempio seguente viene illustrato come rifiutare un endpoint privato tramite PowerShell.The following example shows you how to reject a private endpoint using PowerShell. È possibile ottenere il GUID per l'endpoint privato dal risultato del comando GET precedente. 

> [!NOTE]
> I passaggi illustrati in questa sezione sono relativi agli argomenti. È possibile utilizzare passaggi simili per rifiutare gli endpoint privati per i **domini.** 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" ` 
    -Headers $Headers
```

È possibile approvare la connessione anche dopo che è stata rifiutata tramite API. Se si usa il portale di Azure, non è possibile approvare un endpoint rifiutato. 

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come configurare le impostazioni del firewall IP, vedere Configurare il firewall IP per gli argomenti o i domini di Griglia di eventi di [Azure.](configure-firewall.md)