---
title: 'Avvio rapido: Configurare la disponibilità elevata con Frontdoor di Azure - Azure PowerShell'
description: Questo argomento di avvio rapido illustra come usare Frontdoor di Azure per creare un'applicazione Web globale a disponibilità elevata e prestazioni elevate con Azure PowerShell.
services: front-door
documentationcenter: na
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/21/2020
ms.author: duau
ms.openlocfilehash: 8ccc50dfc4c53f1ee207a04d7d7775775f13cb65
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91348043"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-powershell"></a>Avvio rapido: Creare un'istanza di Frontdoor per un'applicazione Web globale a disponibilità elevata con Azure PowerShell

Iniziare a usare Frontdoor di Azure con Azure PowerShell per creare un'applicazione Web globale a disponibilità elevata e prestazioni elevate.

Frontdoor indirizza il traffico Web a risorse specifiche in un pool back-end. Definire il dominio front-end, aggiungere le risorse a un pool back-end e creare una regola di gestione. Questo articolo usa una semplice configurazione di un unico pool back-end con due risorse dell'app Web e un'unica regola di gestione che usa il percorso predefinito corrispondente a "/*".

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installazione di Azure PowerShell in locale o Azure Cloud Shell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>Creare un gruppo di risorse

In Azure, si allocano le risorse correlate a un gruppo di risorse. È possibile usare un gruppo di risorse esistente o crearne uno nuovo.

Creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupFD -Location centralus
```

## <a name="create-two-instances-of-a-web-app"></a>Creare due istanze di un'app Web

Per questo argomento di avvio rapido, è necessario creare due istanze di un'applicazione Web eseguite in aree di Azure diverse. Entrambe le istanze dell'applicazione Web vengono eseguite in modalità attiva/attiva, in modo che una delle due possa gestire il traffico. Questa configurazione è diversa da una configurazione attiva/standby, in cui un'istanza agisce da failover.

Se non si dispone già di un'app Web, usare lo script seguente per configurare due app Web di esempio.

```azurepowershell-interactive
# Create first web app in Central US region.
$webapp1 = New-AzWebApp `
-Name "WebAppContoso-$(Get-Random)" `
-Location centralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanCentralUS

# Create second web app in South Central US region.
$webapp2 = New-AzWebApp `
-Name "WebAppContoso-$(Get-Random)" `
-Location southcentralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanSouthCentralUS
```

## <a name="create-a-front-door"></a>Creare una frontdoor

Questa sezione descrive dettagliatamente come creare e configurare i componenti seguenti dell'istanza di Frontdoor:
    
* Un oggetto front-end contiene il dominio predefinito di Frontdoor.
* Un pool back-end è un insieme di back-end equivalenti tra cui Frontdoor bilancia il carico delle richieste dei client.
* Una regola di gestione esegue il mapping dell'host frontend e di un modello di percorso URL corrispondente a un pool back-end specifico.

### <a name="create-a-frontend-object"></a>Creare un oggetto front-end

L'oggetto front-end configura il nome host per l'istanza di Frontdoor. Per impostazione predefinita, il nome host avrà il suffisso * *.azurefd.net*.

```azurepowershell-interactive
# Create a unique name
$fdname = "contoso-frontend-$(Get-Random)"

#Create the frontend object
$FrontendEndObject = New-AzFrontDoorFrontendEndpointObject `
-Name "frontendEndpoint1" `
-HostName $fdname".azurefd.net"
```

### <a name="create-the-backend-pool"></a>Creare il pool back-end

Il pool back-end è costituito dalle due app Web create all'inizio di questo argomento di avvio rapido. Il probe di integrità e le impostazioni di bilanciamento del carico definite in questo passaggio usano i valori predefiniti.

```azurepowershell-interactive
# Create backend objects that points to the hostname of the web apps
$backendObject1 = New-AzFrontDoorBackendObject `
-Address $webapp1.DefaultHostName
$backendObject2 = New-AzFrontDoorBackendObject `
-Address $webapp2.DefaultHostName

# Create a health probe object
$HealthProbeObject = New-AzFrontDoorHealthProbeSettingObject `
-Name "HealthProbeSetting"

# Create the load balancing setting object
$LoadBalancingSettingObject = New-AzFrontDoorLoadBalancingSettingObject `
-Name "Loadbalancingsetting" `
-SampleSize "4" `
-SuccessfulSamplesRequired "2" `
-AdditionalLatencyInMilliseconds "0"

# Create a backend pool using the backend objects, health probe, and load balancing settings
$BackendPoolObject = New-AzFrontDoorBackendPoolObject `
-Name "myBackendPool" `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-Backend $backendObject1,$backendObject2 `
-HealthProbeSettingsName "HealthProbeSetting" `
-LoadBalancingSettingsName "Loadbalancingsetting"
```

### <a name="create-a-routing-rule"></a>Creare una regola di routing

La regola di gestione esegue il mapping del pool back-end al dominio front-end e imposta il valore predefinito del percorso corrispondente su "/*".

```azurepowershell-interactive
# Create a routing rule mapping the frontend host to the backend pool
$RoutingRuleObject = New-AzFrontDoorRoutingRuleObject `
-Name LocationRule `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-FrontendEndpointName "frontendEndpoint1" `
-BackendPoolName "myBackendPool" `
-PatternToMatch "/*"
```
### <a name="create-the-front-door"></a>Creare l'istanza di Frontdoor

Dopo aver creato gli oggetti necessari, creare l'istanza di Frontdoor:

```azurepowershell-interactive
# Creates the Front Door
New-AzFrontDoor `
-Name $fdname `
-ResourceGroupName myResourceGroupFD `
-RoutingRule $RoutingRuleObject `
-BackendPool $BackendPoolObject `
-FrontendEndpoint $FrontendEndObject `
-LoadBalancingSetting $LoadBalancingSettingObject `
-HealthProbeSetting $HealthProbeObject
```

Una volta completata la distribuzione, è possibile testarla attenendosi alla procedura descritta nella sezione successiva.

## <a name="test-the-front-door"></a>Testare l'istanza di Frontdoor

Eseguire i comandi seguenti per ottenere il nome host dell'istanza di Frontdoor.

```azurepowershell-interactive
# Gets Front Door in resource group and output the hostname of the frontend domain.
$fd = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD
$fd.FrontendEndpoints[0].Hostname
```

Aprire un Web browser e immettere il nome host ottenuto dai comandi. L'istanza di Frontdoor indirizza la richiesta a una delle risorse back-end. 

:::image type="content" source="./media/quickstart-create-front-door-powershell/front-door-test-page.png" alt-text="Pagina di test dell'istanza di Frontdoor":::

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create con l'istanza di Frontdoor non sono più necessarie, eliminare il gruppo di risorse. L'eliminazione del gruppo di risorse comporta anche l'eliminazione dell'istanza di Frontdoor e di tutte le risorse correlate. 

Per eliminare il gruppo di risorse, chiamare il cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupFD
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido sono stati creati questi componenti:
* Frontdoor
* Due app Web

Per informazioni su come aggiungere un dominio personalizzato all'istanza di Frontdoor, proseguire con le esercitazioni su Frontdoor.

> [!div class="nextstepaction"]
> [Aggiungere un dominio personalizzato](front-door-custom-domain.md)
