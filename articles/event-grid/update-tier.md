---
title: Aggiornare il piano tariffario di un argomento o di un dominio di Griglia di eventi di AzureUpdate pricing tier of an Azure Event Grid topic or domain
description: Questo articolo descrive come aggiornare il piano tariffario di un argomento o di un dominio di Griglia di eventi di Azure (da usare da premium a premium, da premium a base di base) usando il portale di Azure, l'interfaccia della riga di comando di Azure e Azure PowerShell.This article describes how to update pricing tier of an Azure Event Grid topic or domain (basic to premium, premium to basic) using Azure portal, Azure CLI, and Azure PowerShell.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 1e92a9f0b9ba007b289c634995b04f4f1575310a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79300141"
---
# <a name="update-pricing-tier"></a>Aggiornare il piano tariffario 
Questo articolo illustra come aggiornare il piano tariffario di un argomento o di un dominio di Griglia di eventi di Azure usando il portale di Azure, l'interfaccia della riga di comando di Azure e Azure PowerShell.This article shows you how to update pricing tier of an Azure Event Grid topic or domain by using Azure portal, Azure CLI, and Azure PowerShell. 

## <a name="use-azure-portal"></a>Usare il portale di Azure
Questa sezione illustra come modificare il piano tariffario di un argomento o di un dominio nel portale di Azure.This section shows how to change the pricing tier of a topic or a domain in the Azure portal. 

### <a name="overview-page"></a>Pagina di panoramica
È possibile modificare il piano tariffario di un argomento o di un dominio nella pagina **Panoramica.You** can change the pricing tier of a topic or a domain on the Overview page. L'esempio seguente mostra come aggiornare un argomento dal livello di base al livello Premium.The following example shows how to upgrade a topic from the basic tier to premium tier. I passaggi per eseguire il downgrade dal livello Premium al livello di base sono simili.

1. Nel [portale di Azure](https://portal.azure.com)passare alla pagina dell'argomento o del dominio. 
2. Nella pagina **Panoramica** selezionare il piano tariffario corrente (nell'esempio seguente, è **di base).**
    
    ![Selezionare il piano tariffario corrente](./media/update-tier/select-tier.png)
3. Nella pagina **Livello prezzi** modificare il livello e selezionare **OK**. 

    ![Aggiornare il piano tariffario](./media/update-tier/change-tier.png)
4. Controllare lo stato dell'operazione nella pagina **Notifiche.**

    ![Stato aggiornamento](./media/update-tier/status.png)    
5. Verificare che il livello aggiornato venga visualizzato nella pagina **Panoramica.** 

    ![Stato aggiornamento](./media/update-tier/changed-tier.png)

### <a name="networking-page"></a>Pagina di funzionalità di rete
È possibile **eseguire l'aggiornamento** dal livello di base al livello Premium nella pagina **Rete.You** can upgrade from the basic tier to the premium tier on the Networking page. Non è tuttavia possibile eseguire il downgrade dal livello Premium al livello di base in questa pagina. 

1. Nel [portale di Azure](https://portal.azure.com)passare alla pagina dell'argomento o del dominio. 
2. Nella pagina **Rete** passare alla scheda **Connessioni endpoint privati (anteprima).** 
3. Se il piano tariffario corrente è **di base,** viene visualizzato il messaggio seguente. Selezionarlo. 

    ![Pagina Controllo livello di aggiornamento nelle connessioni endpoint privati](./media/update-tier/private-endpoint-connections-page.png)
4. Nella pagina **Aggiorna al piano tariffario premium** selezionare **Sì**. 
    
    ![Confermare l'aggiornamento](./media/update-tier/message-private-endpoint-connection.png)
5. Controllare lo stato dell'operazione nella pagina **Notifiche.**

    ![Stato aggiornamento](./media/update-tier/status.png)



## <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
Questa sezione illustra come usare i comandi dell'interfaccia della riga di comando di Azure per modificare il piano tariffario di un argomento o di un dominio. 

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Aggiornare un argomento da base a premiumUpgrade a topic from basic to premium

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Eseguire il downgrade di un argomento dal premio al

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Aggiornare un dominio da base a premiumUpgrade a domain from basic to premium

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Eseguire il downgrade di un dominio da premium a basic

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```



## <a name="use-azure-powershell"></a>Usare Azure PowerShell
In questa sezione viene illustrato come usare i comandi di PowerShell per modificare il piano tariffario di un argomento o di un dominio. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Preparare token e intestazioni per le chiamate all'API RESTPrepare token and headers for REST API calls 
Eseguire i comandi dei prerequisiti seguenti per ottenere un token di autenticazione da usare con le chiamate all'API REST, l'autorizzazione e altre informazioni di intestazione. 

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

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Aggiornare un argomento da base a premiumUpgrade a topic from basic to premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Eseguire il downgrade di un argomento dal premio al

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Aggiornare un dominio da base a premiumUpgrade a domain from basic to premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Eseguire il downgrade di un dominio da premium a basic

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

## <a name="next-steps"></a>Passaggi successivi
Per gli argomenti e i domini di livello Premium, è possibile configurare gli endpoint privati per limitare l'accesso solo dalle reti virtuali selezionate. Per istruzioni dettagliate, vedere [Configure private endpoints](configure-private-endpoints.md).
