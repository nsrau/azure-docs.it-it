---
title: Impostare le variabili di ambiente in Istanze di contenitore di Azure
description: Informazioni su come impostare le variabili di ambiente in Istanze di contenitore di Azure
services: container-instances
author: david-stanford
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 03/13/2018
ms.author: dastanfo
ms.openlocfilehash: f845e96a3e05be3f9109446d0d9e88934c4794cc
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="set-environment-variables"></a>Impostare le variabili di ambiente

L'impostazione delle variabili di ambiente nelle istanze di contenitore consente di offrire la configurazione dinamica dell'applicazione o dello script eseguiti dal contenitore.

È attualmente possibile impostare le variabili di ambiente dall'interfaccia della riga di comando e da PowerShell. In entrambi i casi, si userà un flag nei comandi per impostare le variabili di ambiente. L'impostazione delle variabili di ambiente in Istanze di contenitore di Azure è simile all'argomento della riga di comando `--env` per `docker run`. Se ad esempio si usa l'immagine del contenitore microsoft/aci-wordcount, è possibile modificare il comportamento specificando le variabili di ambiente seguenti:

*NumWords*: il numero di parole inviate a STDOUT.

*MinLength*: il numero minimo di caratteri in una parola perché venga contata. Un numero più alto ignora le parole comuni, ad esempio "di" e "il".

## <a name="azure-cli-example"></a>Esempio di interfaccia della riga di comando di Azure

Per visualizzare l'output predefinito del contenitore, eseguire il comando seguente:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Specificando `NumWords=5` e `MinLength=8` per le variabili di ambiente del contenitore, i log contenitori devono mostrare un output diverso.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Quando lo stato del contenitore viene mostrato come *Terminated* (usare [az container show][az-container-show] per controllarne lo stato), vengono visualizzati i log per verificare l'output.  Per visualizzare l'output del contenitore senza variabili di ambiente, impostare --name come mycontainer1 invece che come mycontainer2.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

## <a name="azure-powershell-example"></a>Esempio di Azure PowerShell

Per visualizzare l'output predefinito del contenitore, eseguire il comando seguente:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Specificando `NumWords=5` e `MinLength=8` per le variabili di ambiente del contenitore, i log contenitori devono mostrare un output diverso.

```azurepowershell-interactive
$envVars = @{NumWord=5;MinLength=8}
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image microsoft/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Quando lo stato del contenitore è *Terminated* (usare [Get-AzureRmContainerInstanceLog][azure-instance-log] per controllarne lo stato), vengono visualizzati i log per verificare l'output. Per visualizzare i log del contenitore senza variabili di ambiente, impostare ContainerGroupName come mycontainer1 invece che come mycontainer2.

```azurepowershell-interactive
Get-AzureRmContainerInstanceLog `
    -ResourceGroupName myResourceGroup `
    -ContainerGroupName mycontainer2
```

## <a name="example-output-without-environment-variables"></a>Output di esempio senza variabili di ambiente

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

## <a name="example-output-with-environment-variables"></a>Output di esempio con variabili di ambiente

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come personalizzare l'input per il contenitore, è possibile salvare in modo permanente l'output dei contenitori eseguiti fino al completamento.
> [!div class="nextstepaction"]
> [Montaggio di una condivisione file di Azure con Istanze di contenitore di Azure](container-instances-mounting-azure-files-volume.md)

<!-- LINKS Internal -->
[azure-cloud-shell]: ../cloud-shell/overview.md
[azure-cli-install]: /cli/azure/
[azure-powershell-install]: /powershell/azure/install-azurerm-ps
[azure-instance-log]: /powershell/module/azurerm.containerinstance/get-azurermcontainerinstancelog
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show