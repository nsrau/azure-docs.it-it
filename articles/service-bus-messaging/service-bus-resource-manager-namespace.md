---
title: Creare uno spazio dei nomi della messaggistica del bus di servizio tramite un modello di Azure Resource Manager | Microsoft Docs
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
ms.openlocfilehash: 4162775153a48dc8ea28e06f7c99f9927b9c602a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444762"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Creare uno spazio dei nomi del Bus di servizio usando un modello Azure Resource Manager

Informazioni su come distribuire un modello di Azure Resource Manager per creare uno spazio dei nomi del Bus di servizio. È possibile usare questo modello per le proprie distribuzioni o personalizzarlo in base alle esigenze. Per altre informazioni sulla creazione di modelli, vedere [documentazione di Azure Resource Manager](/azure/azure-resource-manager/).

Sono disponibili per la creazione di spazi dei nomi del Bus di servizio anche i modelli seguenti:

* [Creare uno spazio dei nomi del bus di servizio con coda](./service-bus-resource-manager-namespace-queue.md)
* [Creare uno spazio dei nomi del bus di servizio con argomento e sottoscrizione](./service-bus-resource-manager-namespace-topic.md)
* [Creare uno spazio dei nomi del bus di servizio con coda e regola di autorizzazione](./service-bus-resource-manager-namespace-auth-rule.md)
* [Creare uno spazio dei nomi del bus di servizio con argomento, sottoscrizione e regola](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-a-service-bus-namespace"></a>Creare uno spazio dei nomi del bus di servizio

In questa Guida introduttiva Usa un' [modello di Resource Manager esistenti](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) dalla [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/):

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

Per trovare ulteriori esempi di modelli, vedere [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular).

Per creare uno spazio dei nomi del bus di servizio distribuendo un modello:

1. Selezionare **Provalo** dal blocco di codice seguente e quindi seguire le istruzioni per accedere ad Azure Cloud shell.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    Il nome del gruppo di risorse è il nome dello spazio dei nomi del bus di servizio con **rg** aggiunto.

2. Selezionare **Copia** per copiare lo script di PowerShell.
3. La console di shell e quindi scegliere **Incolla**.

Sono necessari alcuni minuti per creare un hub eventi.

## <a name="verify-the-deployment"></a>Verificare la distribuzione

Per visualizzare lo spazio dei nomi del bus di servizio distribuito, è possibile aprire il gruppo di risorse dal portale di Azure oppure usare il seguente script PowerShell di Azure. Se Cloud shell è ancora aperta, non devi copia/eseguire prima e seconda riga dello script seguente.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

Azure PowerShell viene usato per distribuire il modello in questa esercitazione. Per altri metodi di distribuzione del modello, vedere:

* [Tramite il portale di Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).
* [Usando Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md).
* [Tramite l'API REST](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse. Se Cloud shell è ancora aperta, non devi copia/eseguire prima e seconda riga dello script seguente.

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
