---
title: 'Creare un servizio di bilanciamento del carico interno: modello di Azure'
titleSuffix: Azure Load Balancer
description: Informazioni su come creare un servizio di bilanciamento del carico interno in Gestione risorse con un modello
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 7e8f34f3d68a957dbd5bb13b69f751184d224e2c
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85961116"
---
# <a name="create-an-internal-load-balancer-using-a-template"></a>Creare un servizio di bilanciamento del carico interno usando un modello

> [!div class="op_single_selector"]
> * [Portale di Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Interfaccia della riga di comando di Azure](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Modello](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Distribuire il modello tramite clic per la distribuzione

Il modello di esempio disponibile nel repository pubblico usa un file di parametro che contiene i valori predefiniti usati per generare lo scenario descritto in precedenza. Distribuire [questo modello](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer)tramite clic per la distribuzione, fare clic su **Distribuisci in Azure**, sostituire i valori del parametro predefinito se necessario e seguire le istruzioni nel portale.

## <a name="deploy-the-template-by-using-powershell"></a>Distribuire il modello tramite PowerShell

Per distribuire il modello scaricato tramite PowerShell, seguire questa procedura.

1. Se è la prima volta che si utilizza Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview) e seguire le istruzioni fino al termine della procedura per accedere ad Azure e selezionare la sottoscrizione desiderata.
2. Scaricare il file dei parametri sul disco locale.
3. Modificare il file e salvarlo.
4. Eseguire il cmdlet **New-AzResourceGroupDeployment** per creare un gruppo di risorse usando il modello.

    ```azurepowershell-interactive
    New-AzResourceGroupDeployment -Name TestRG -Location westus `
        -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
        -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'
    ```

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Distribuire il modello tramite l'interfaccia della riga di comando di Azure

Per distribuire il modello tramite l'interfaccia della riga di comando di Azure, seguire questa procedura.

1. Se non è mai stata usata l'interfaccia della riga di comando di Azure, vedere [installare e configurare l'interfaccia della](../cli-install-nodejs.md) riga di comando di Azure e seguire le istruzioni fino al punto in cui si seleziona l'account e la sottoscrizione di Azure
2. Passare a [https://shell.azure.com](https://shell.azure.com) per aprire Cloud Shell nel browser. Eseguire il comando **azure config mode** per passare alla modalità Gestione risorse, come illustrato di seguito.

    ```console
    azure config mode arm
    ```

    Di seguito è riportato l'output previsto per il comando precedente:

    ```output
    info:    New mode is arm
    ```

3. Aprire il file dei parametri, selezionare i relativi contenuti e salvarlo in un file nel computer. In questo esempio il file dei parametri è stato salvato in *parameters.json*.
4. Eseguire il cmdlet **azure group deployment create** per distribuire il nuovo servizio di bilanciamento del carico usando i file di modello e dei parametri scaricati e modificati in precedenza. Nell'elenco riportato dopo l'output sono indicati i parametri usati.

    ```console
    azure group create --name TestRG --location westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json --parameters-file parameters.json
    ```

## <a name="next-steps"></a>Passaggi successivi

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico utilizzando l’affinità dell’IP di origine](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)

Per la sintassi e le proprietà JSON di un servizio di bilanciamento del carico in un modello, vedere [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers).