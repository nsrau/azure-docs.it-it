---
title: Creare uno spazio dei nomi del bus di servizio di Azure usando il modello
description: Usare il modello di Azure Resource Manager per creare uno spazio dei nomi della messaggistica del bus di servizio
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/21/2019
ms.author: spelluru
ms.openlocfilehash: 5febdd63ab6f854ca3244f8449f6f715a75e735f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76264476"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Creare uno spazio dei nomi del bus di servizio usando un modello di Azure Resource Manager

Informazioni su come distribuire un modello di Azure Resource Manager per creare uno spazio dei nomi del bus di servizio. È possibile usare questo modello per le proprie distribuzioni o personalizzarlo in base alle esigenze. Per ulteriori informazioni sulla creazione di modelli, vedere [Azure Resource Manager documentazione](/azure/azure-resource-manager/).

Per la creazione di spazi dei nomi del bus di servizio sono disponibili anche i modelli seguenti:

* [Creare uno spazio dei nomi del bus di servizio con coda](./service-bus-resource-manager-namespace-queue.md)
* [Creare uno spazio dei nomi del bus di servizio con argomento e sottoscrizione](./service-bus-resource-manager-namespace-topic.md)
* [Creare uno spazio dei nomi del bus di servizio con coda e regola di autorizzazione](./service-bus-resource-manager-namespace-auth-rule.md)
* [Creare uno spazio dei nomi del bus di servizio con argomento, sottoscrizione e regola](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-a-service-bus-namespace"></a>Creare uno spazio dei nomi del bus di servizio

In questa Guida introduttiva si userà un [modello di gestione risorse esistente](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/):

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

Per altri esempi di modello, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular).

Per creare uno spazio dei nomi del bus di servizio distribuendo un modello:

1. Selezionare **Prova** nel blocco di codice seguente e quindi seguire le istruzioni per accedere ad Azure Cloud Shell.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    Il nome del gruppo di risorse è il nome dello spazio dei nomi del bus di servizio con l'aggiunta di **RG** .

2. Selezionare **Copia** per copiare lo script di PowerShell.
3. Fare clic con il pulsante destro del mouse sulla console della shell e quindi scegliere **Incolla**.

La creazione di un hub eventi richiede pochi minuti.

## <a name="verify-the-deployment"></a>Verificare la distribuzione

Per visualizzare lo spazio dei nomi del bus di servizio distribuito, è possibile aprire il gruppo di risorse dalla portale di Azure o usare lo script di Azure PowerShell seguente. Se cloud Shell è ancora aperto, non è necessario copiare/eseguire la prima e la seconda riga dello script seguente.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

Azure PowerShell viene usato per distribuire il modello in questa esercitazione. Per altri metodi di distribuzione dei modelli, vedere:

* [Utilizzando il portale di Azure](../azure-resource-manager/templates/deploy-portal.md).
* [Usando l'interfaccia della riga di comando di Azure](../azure-resource-manager/templates/deploy-cli.md).
* [Usando l'API REST](../azure-resource-manager/templates/deploy-rest.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse. Se cloud Shell è ancora aperto, non è necessario copiare/eseguire la prima e la seconda riga dello script seguente.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato creato uno spazio dei nomi del bus di servizio. Vedere le altre guide introduttive per informazioni su come creare e usare code, argomenti/sottoscrizioni:

* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Introduzione agli argomenti del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)
