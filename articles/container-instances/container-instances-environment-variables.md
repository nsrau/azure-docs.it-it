---
title: Set environment variables in container instance
description: Informazioni su come impostare le variabili di ambiente nei contenitori eseguiti in Istanze di Azure Container
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: d12d3204740f2971216636f9f5dd6403b17ecbff
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483191"
---
# <a name="set-environment-variables-in-container-instances"></a>Set environment variables in container instances

L'impostazione delle variabili di ambiente nelle istanze di contenitore consente di offrire la configurazione dinamica dell'applicazione o dello script eseguiti dal contenitore. È simile all'argomento della riga di comando `--env` per `docker run`. 

Per impostare le variabili di ambiente in un contenitore, specificarle quando si crea un'istanza del contenitore. This article shows examples of setting environment variables when you start a container with the [Azure CLI](#azure-cli-example), [Azure PowerShell](#azure-powershell-example), and the [Azure portal](#azure-portal-example). 

For example, if you run the Microsoft [aci-wordcount][aci-wordcount] container image, you can modify its behavior by specifying the following environment variables:

*NumWords*: il numero di parole inviate a STDOUT.

*MinLength*: il numero minimo di caratteri in una parola perché venga contata. Un numero più alto ignora le parole comuni, ad esempio "di" e "il".

Se è necessario passare segreti come variabili di ambiente, Istanze di Azure Container supporta [valori sicuri](#secure-values) per i contenitori sia Windows che Linux.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Esempio di interfaccia della riga di comando di Azure

To see the default output of the [aci-wordcount][aci-wordcount] container, run it first with this [az container create][az-container-create] command (no environment variables specified):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Per modificare l'output, avviare un secondo contenitore con l'argomento `--environment-variables` aggiunto, specificando i valori per le variabili *NumWords* e *MinLength*. (In questo esempio si presuppone che l'interfaccia della riga di comando sia eseguita in una shell di Bash o in Azure Cloud Shell. Se si usa il prompt dei comandi di Windows, specificare le variabili con le virgolette doppie, ad esempio `--environment-variables "NumWords"="5" "MinLength"="8"`.)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

Once both containers' state shows as *Terminated* (use [az container show][az-container-show] to check state), display their logs with [az container logs][az-container-logs] to see the output.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

L'output dei contenitori mostra come si è modificato il comportamento di script del secondo contenitore impostando le variabili di ambiente.

```console
azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer1
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

azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Esempio di Azure PowerShell

L'impostazione delle variabili di ambiente in PowerShell è simile all'interfaccia della riga di comando, ma usa l'argomento della riga di comando `-EnvironmentVariable`.

First, launch the [aci-wordcount][aci-wordcount] container in its default configuration with this [New-AzContainerGroup][new-Azcontainergroup] command:

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Now run the following [New-AzContainerGroup][new-Azcontainergroup] command. che specifica le variabili di ambiente *NumWords* e *MinLength* dopo avere popolato un variabile di matrice, `envVars`:

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Once both containers' state is *Terminated* (use [Get-AzContainerInstanceLog][azure-instance-log] to check state), pull their logs with the [Get-AzContainerInstanceLog][azure-instance-log] command.

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

L'output di ogni contenitore mostra come si è modificato lo script eseguito dal contenitore impostando le variabili di ambiente.

```console
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
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

Azure:\
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Esempio del portale di Azure

To set environment variables when you start a container in the Azure portal, specify them in the **Advanced** page when you create the container.

1. On the **Advanced** page, set the **Restart policy** to *On failure*
2. Under **Environment variables**, enter `NumWords` with a value of `5` for the first variable, and enter `MinLength` with a value of `8` for the second variable. 
1. Select **Review + create** to verify and then deploy the container.

![Pagina del portale che mostra il pulsante di abilitazione e le caselle di testo delle variabili di ambiente][portal-env-vars-01]

To view the container's logs, under **Settings** select **Containers**, then **Logs**. Analogamente all'output illustrato nelle sezioni precedenti sull'interfaccia della riga di comando e PowerShell, è possibile visualizzare come il comportamento dello script è stato modificato dalle variabili di ambiente. Vengono visualizzate solo cinque parole, ognuna con una lunghezza minima di otto caratteri.

![Portale che visualizza l'output del log del contenitore][portal-env-vars-02]

## <a name="secure-values"></a>Valori sicuri

Gli oggetti con valori sicuri sono progettati per contenere informazioni riservate, ad esempio le password o le chiavi per le applicazioni. L'uso di valori sicuri per le variabili di ambiente è sia più sicuro che più flessibile rispetto all'inclusione nell'immagine del contenitore. Un'altra opzione consiste nell'usare volumi segreti, come descritto in [Montare un volume segreto in Istanze di Azure Container](container-instances-volume-secret.md).

Le variabili di ambiente con valori sicuri non sono visibili nelle proprietà del contenitore: i relativi valori sono accessibili solo dall'interno del contenitore. Ad esempio, le proprietà del contenitore visualizzate nel portale di Azure o nell'interfaccia della riga di comando di Azure mostrano solo il nome della variabile sicura e non il suo valore.

Impostare una variabile di ambiente sicura, specificando la proprietà `secureValue` anziché il normale `value` per il tipo di variabile. Le due variabili definite nel file YAML seguente illustrano i due tipi di variabili.

### <a name="yaml-deployment"></a>Distribuzione con file YAML

Creare un file `secure-env.yaml` con il frammento seguente.

```yaml
apiVersion: 2018-10-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - name: 'NOTSECRET'
          value: 'my-exposed-value'
        - name: 'SECRET'
          secureValue: 'my-secret-value'
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Eseguire il comando seguente per distribuire il gruppo di contenitori con YAML (modificare il nome del gruppo di risorse in base alle esigenze):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Verificare le variabili di ambiente

Run the [az container show][az-container-show] command to query your container's environment variables:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

La risposta JSON mostra sia la chiave che il valore della variabile di ambiente non sicura, ma solo il nome della variabile di ambiente sicura:

```json
[
  [
    {
      "name": "NOTSECRET",
      "secureValue": null,
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET",
      "secureValue": null,
      "value": null
    }
  ]
]
```

With the [az container exec][az-container-exec] command, which enables executing a command in a running container, you can verify that the secure environment variable has been set. Eseguire il comando seguente per avviare una sessione bash interattiva nel contenitore:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

Dopo aver aperto una shell interattiva all'interno del contenitore, è possibile accedere al valore della variabile `SECRET`:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>Passaggi successivi

Gli scenari basati su attività, ad esempio l'elaborazione batch di un set di dati di grandi dimensioni con diversi contenitori, possono trarre vantaggio dalle variabili di ambiente personalizzate in fase di esecuzione. For more information about running task-based containers, see [Run containerized tasks with restart policies](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-Az-ps
[new-Azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[portal]: https://portal.azure.com
