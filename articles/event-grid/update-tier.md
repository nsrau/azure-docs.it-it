---
title: Aggiornare il piano tariffario di un argomento o di un dominio di griglia di eventi di Azure
description: Questo articolo descrive come aggiornare il piano tariffario di un argomento o un dominio di griglia di eventi di Azure (da Basic a Premium, da Premium a Basic) usando portale di Azure, l'interfaccia della riga di comando di Azure e Azure PowerShell.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: spelluru
ms.openlocfilehash: caea8d515964510fce432eb0497e3af19ecc1369
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82101638"
---
# <a name="update-pricing-tier"></a>Aggiornare il piano tariffario 
Questo articolo illustra come aggiornare il piano tariffario di un argomento o dominio di griglia di eventi di Azure usando portale di Azure, l'interfaccia della riga di comando di Azure e Azure PowerShell. 

## <a name="use-azure-portal"></a>Usare il portale di Azure
In questa sezione viene illustrato come modificare il piano tariffario di un argomento o di un dominio nel portale di Azure. 

### <a name="overview-page"></a>Pagina di panoramica
È possibile modificare il piano tariffario di un argomento o di un dominio nella pagina **Panoramica** . Nell'esempio seguente viene illustrato come aggiornare un argomento dal livello Basic al livello Premium. La procedura per effettuare il downgrade dal livello Premium al livello Basic è simile.

1. Nella [portale di Azure](https://portal.azure.com)passare alla pagina argomento o dominio. 
2. Nella pagina **Overview (panoramica** ) selezionare il piano tariffario corrente (nell'esempio seguente si tratta di **base**).
    
    ![Selezionare il piano tariffario corrente](./media/update-tier/select-tier.png)
3. Nella pagina piano **tariffario** modificare il livello e selezionare **OK**. 

    ![Aggiornare il piano tariffario](./media/update-tier/change-tier.png)
4. Controllare lo stato dell'operazione nella pagina **notifiche** .

    ![Stato aggiornamento](./media/update-tier/status.png)    
5. Verificare che il livello aggiornato sia visualizzato nella pagina **Panoramica** . 

    ![Stato aggiornamento](./media/update-tier/changed-tier.png)

### <a name="networking-page"></a>Pagina di funzionalità di rete
È possibile **eseguire l'aggiornamento** dal livello Basic al livello Premium nella pagina **rete** . Tuttavia, non è possibile effettuare il downgrade dal livello Premium al livello Basic in questa pagina. 

1. Nella [portale di Azure](https://portal.azure.com)passare alla pagina argomento o dominio. 
2. Nella pagina **rete** passare alla scheda **connessioni endpoint privato (anteprima)** . 
3. Se il piano tariffario corrente è **Basic**, viene visualizzato il messaggio seguente. Selezionarlo. 

    ![Pagina di aggiornamento del livello nella pagina connessioni endpoint privato](./media/update-tier/private-endpoint-connections-page.png)
4. Nella pagina **aggiornamento a piano tariffario Premium** selezionare **Sì**. 
    
    ![Conferma aggiornamento](./media/update-tier/message-private-endpoint-connection.png)
5. Controllare lo stato dell'operazione nella pagina **notifiche** .

    ![Stato aggiornamento](./media/update-tier/status.png)



## <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
Questa sezione illustra come usare i comandi dell'interfaccia della riga di comando di Azure per modificare il piano tariffario di un argomento. Per aggiornare il piano tariffario di un dominio, `az eventgrid domain update` usare il comando in modo analogo.

### <a name="prerequisites"></a>Prerequisiti
Aggiornare l'estensione di griglia di eventi di Azure per l'interfaccia della riga di comando eseguendo il comando seguente: 

```azurecli-interactive
az extension update -n eventgrid
```

Se l'estensione non è installata, eseguire il comando seguente per installarla: 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Aggiornare un argomento da Basic a Premium

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --sku "Premium" 
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Eseguire il downgrade di un argomento da Premium a Basic

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --sku "Basic" 
```


## <a name="use-azure-powershell"></a>Usare Azure PowerShell
Questa sezione illustra come usare i comandi di PowerShell per modificare il piano tariffario di un argomento o di un dominio. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Preparare il token e le intestazioni per le chiamate API REST 
Eseguire i comandi dei prerequisiti seguenti per ottenere un token di autenticazione da usare con le chiamate API REST, le autorizzazioni e altre informazioni di intestazione. 

```azurepowershell-interactive
# replace <CLIENT ID> and <CLIENT SECRET>
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get the authentication token. Replace <TENANT ID>
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token `
    -Body $body `
    -ContentType 'application/x-www-form-urlencoded'

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
$Headers.Add("Content-Type","application/json")
```

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Aggiornare un argomento da Basic a Premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Eseguire il downgrade di un argomento da Premium a Basic

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Aggiornare un dominio da Basic a Premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Effettuare il downgrade di un dominio da Premium a Basic

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

## <a name="next-steps"></a>Passaggi successivi
Per gli argomenti e i domini del livello Premium è possibile configurare endpoint privati per limitare l'accesso solo da reti virtuali selezionate. Per istruzioni dettagliate, vedere [configurare endpoint privati](configure-private-endpoints.md).
