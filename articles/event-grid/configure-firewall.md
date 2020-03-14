---
title: Configurare il firewall IP per gli argomenti o i domini di griglia di eventi di Azure (anteprima)
description: Questo articolo descrive come configurare le impostazioni del firewall per gli argomenti o i domini della griglia di eventi.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: b195872ca1002970fa96ae133d5eb47a9267796d
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299868"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains-preview"></a>Configurare il firewall IP per gli argomenti o i domini di griglia di eventi di Azure (anteprima)
Per impostazione predefinita, l'argomento e il dominio sono accessibili da Internet, purché la richiesta venga fornita con autenticazione e autorizzazione valide. Con il firewall IP, è possibile limitarlo ulteriormente a un set di indirizzi IPv4 o a intervalli di indirizzi IPv4 in notazione [CIDR (instradamento tra domini senza classi)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . Gli autori che hanno origine da altri indirizzi IP verranno rifiutati e riceveranno una risposta 403 (accesso negato). Per ulteriori informazioni sulle funzionalità di sicurezza di rete supportate da griglia di eventi, vedere [sicurezza di rete per griglia di eventi](network-security.md).

Questo articolo descrive come configurare le impostazioni del firewall IP per gli argomenti o i domini di griglia di eventi di Azure.

## <a name="use-azure-portal"></a>Usare il portale di Azure
In questa sezione viene illustrato come utilizzare il portale di Azure per creare regole del firewall IP in ingresso. I passaggi illustrati in questa sezione sono disponibili per gli argomenti. È possibile utilizzare passaggi simili per creare regole IP in ingresso per i **domini**. 

1. Nella [portale di Azure](https://portal.azure.com)passare all'argomento o al dominio di griglia di eventi e passare alla scheda **rete** .
2. Selezionare **reti pubbliche** per consentire a tutti i network, incluso Internet, di accedere alla risorsa. 

    È possibile limitare il traffico usando le regole del firewall basate su IP. Specificare un singolo indirizzo IPv4 o un intervallo di indirizzi IP nella notazione CIDR (Inter-Domain Routing) con classe. 

    ![Pagina reti pubbliche](./media/configure-firewall/public-networks-page.png)
3. Selezionare **solo endpoint privati** per consentire solo le connessioni all'endpoint privato per accedere a questa risorsa. Utilizzare la scheda **connessioni endpoint privato** in questa pagina per gestire le connessioni. 

    ![Pagina reti pubbliche](./media/configure-firewall/private-endpoints-page.png)
4. Sulla barra degli strumenti selezionare **Salva**. 



## <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
Questa sezione illustra come usare i comandi dell'interfaccia della riga di comando di Azure per creare argomenti con regole IP in ingresso. I passaggi illustrati in questa sezione sono disponibili per gli argomenti. È possibile utilizzare passaggi simili per creare regole IP in ingresso per i **domini**. 


### <a name="enable-public-network-access-for-an-existing-topic"></a>Abilitare l'accesso alla rete pubblica per un argomento esistente
Per impostazione predefinita, l'accesso alla rete pubblica è abilitato per argomenti e domini. È possibile limitare il traffico configurando le regole del firewall per gli indirizzi IP in ingresso. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Enabled\""}}"
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Disabilitare l'accesso alla rete pubblica per un argomento esistente
Quando l'accesso alla rete pubblica è disabilitato per un argomento o un dominio, il traffico su Internet pubblico non è consentito. Solo le connessioni a endpoint privati saranno autorizzate ad accedere a queste risorse. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Disabled\""}}"
```

### <a name="create-topic-with-inbound-ip-rules"></a>Crea argomento con regole IP in ingresso
Il comando dell'interfaccia della riga di comando di esempio seguente crea un argomento di griglia di eventi con regole IP in ingresso in un unico passaggio. 

```azurecli-interactive
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"",\""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}
```

### <a name="create-topic-first-and-then-add-inbound-ip-rules"></a>Creare prima l'argomento e quindi aggiungere regole IP in ingresso
Questo esempio crea prima un argomento di griglia di eventi e quindi aggiunge le regole IP in ingresso per l'argomento in un comando separato. Aggiorna inoltre le regole IP in ingresso impostate nel secondo comando. 

```azurecli-interactive

# create the event grid topic first
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
    --body {\""location\"":\""<LOCATION>\""}

# add inbound IP rules
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" 
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"", \""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}

# later, update topic with additional ip rules or remove them. 
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" 
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"", \""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""}, {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}
```


## <a name="use-powershell"></a>Usare PowerShell
Questa sezione illustra come usare i comandi di Azure PowerShell per creare argomenti di griglia di eventi di Azure con regole del firewall IP in ingresso. I passaggi illustrati in questa sezione sono disponibili per gli argomenti. È possibile utilizzare passaggi simili per creare regole IP in ingresso per i **domini**. 

### <a name="prerequisite"></a>Prerequisito
Seguire le istruzioni riportate in [procedura: usare il portale per creare un'applicazione Azure ad e un'entità servizio che possano accedere alle risorse](../active-directory/develop/howto-create-service-principal-portal.md) per creare un'applicazione Azure Active Directory e annotare i valori seguenti:

- ID directory (tenant)
- ID applicazione (client)
- Segreto applicazione (client)

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

### <a name="enable-public-network-access-for-an-existing-topic"></a>Abilitare l'accesso alla rete pubblica per un argomento esistente
Per impostazione predefinita, l'accesso alla rete pubblica è abilitato per argomenti e domini. È possibile limitare il traffico configurando le regole del firewall per gli indirizzi IP in ingresso. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Disabilitare l'accesso alla rete pubblica per un argomento esistente
Quando l'accesso alla rete pubblica è disabilitato per un argomento o un dominio, il traffico su Internet pubblico non è consentito. Solo le connessioni a endpoint privati saranno autorizzate ad accedere a queste risorse. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>Creare un argomento di griglia di eventi con regole in ingresso in un unico passaggio

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that inbound IP rules are included. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# create the event grid topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5
```


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>Crea prima l'argomento della griglia di eventi e quindi Aggiungi regole IP in ingresso

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that no inbound IP rules are specified. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled";}} | ConvertTo-Json -Depth 5

# create the Event Grid topic
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body`

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

# prepare the body for REST PUT method. Notice that it includes inbound IP rules now. This feature available in both basic and premium tiers.
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"}, @{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# update the topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was updated
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AzURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sul monitoraggio dei recapiti degli eventi, vedere [Monitorare il recapito dei messaggi di Griglia di eventi di Azure](monitor-event-delivery.md).
* Per altre informazioni sulla chiave di autenticazione, vedere [Sicurezza e autenticazione di Griglia di eventi](security-authentication.md).
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
