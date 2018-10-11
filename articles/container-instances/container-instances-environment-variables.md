---
title: Impostare le variabili di ambiente in Istanze di contenitore di Azure
description: Informazioni su come impostare le variabili di ambiente nei contenitori eseguiti in Istanze di contenitore di Azure
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 07/19/2018
ms.author: danlep
ms.openlocfilehash: 9fd23ce8236688f1456bcddb2b16b539d5e18482
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2018
ms.locfileid: "48855630"
---
# <a name="set-environment-variables"></a>Impostare le variabili di ambiente

L'impostazione delle variabili di ambiente nelle istanze di contenitore consente di offrire la configurazione dinamica dell'applicazione o dello script eseguiti dal contenitore. Per impostare le variabili di ambiente in un contenitore, specificarle quando si crea un'istanza del contenitore. È possibile impostare le variabili di ambiente quando si avvia un contenitore con l'[interfaccia della riga di comando di Azure](#azure-cli-example), [Azure PowerShell](#azure-powershell-example) e il [portale di Azure](#azure-portal-example).

Se ad esempio si esegue l'immagine del contenitore [microsoft/aci-wordcount][aci-wordcount], è possibile modificarne il comportamento specificando le variabili di ambiente seguenti:

*NumWords*: il numero di parole inviate a STDOUT.

*MinLength*: il numero minimo di caratteri in una parola perché venga contata. Un numero più alto ignora le parole comuni, ad esempio "di" e "il".

Se è necessario passare segreti come variabili di ambiente, Istanze di contenitore di Azure supporta [valori sicuri](#secure-values) per i contenitori sia Windows che Linux.

## <a name="azure-cli-example"></a>Esempio di interfaccia della riga di comando di Azure

Per visualizzare l'output predefinito del contenitore [microsoft/aci-wordcount][aci-wordcount], eseguirlo prima con questo comando [az container create][az-container-create] (nessuna variabile di ambiente specificata):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Per modificare l'output, avviare un secondo contenitore con l'argomento `--environment-variables` aggiunto, specificando i valori per le variabili *NumWords* e *MinLength*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Dopo che lo stato di entrambi i contenitori risulta *terminato* (usare [az container show][az-container-show] per controllare lo stato), visualizzarne i log con [az container logs][az-container-logs] per verificare l'output.

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

Avviare prima il contenitore [microsoft/aci-wordcount][aci-wordcount] nella configurazione predefinita con questo comando [New-AzureRmContainerGroup][new-azurermcontainergroup]:

```azurepowershell-interactive
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image microsoft/aci-wordcount:latest
```

Eseguire ora il comando [New-AzureRmContainerGroup][new-azurermcontainergroup] seguente, che specifica le variabili di ambiente *NumWords* e *MinLength* dopo avere popolato un variabile di matrice, `envVars`:

```azurepowershell-interactive
$envVars = @{NumWords=5;MinLength=8}
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image microsoft/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Dopo che lo stato di entrambi i contenitori risulta *terminato* (usare [Get-AzureRmContainerInstanceLog][azure-instance-log] per controllare lo stato), eseguire il pull dei log con il comando [Get-AzureRmContainerInstanceLog][azure-instance-log].

```azurepowershell-interactive
Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

L'output di ogni contenitore mostra come si è modificato lo script eseguito dal contenitore impostando le variabili di ambiente.

```console
PS Azure:\> Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
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
PS Azure:\> Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Esempio del portale di Azure

Per impostare le variabili di ambiente quando si avvia un contenitore nel portale di Azure, specificarle nella pagina **Configurazione** quando si crea il contenitore.

Quando si esegue la distribuzione con il portale, il limite corrente è di tre variabili ed è necessario immetterle in questo formato: `"variableName":"value"`

Per visualizzare un esempio, avviare il contenitore [microsoft/aci-wordcount][aci-wordcount] con le variabili *NumWords* e *MinLength*.

1. In **Configurazione** impostare **Criteri di riavvio** su *In caso di errore*
2. Immettere `"NumWords":"5"` come prima variabile, selezionare **Sì** in **Add additional environment variables** (Aggiungi altre variabili di ambiente) e immettere `"MinLength":"8"` come seconda variabile. Selezionare **OK** per verificare e quindi distribuire il contenitore.

![Pagina del portale che mostra il pulsante di abilitazione e le caselle di testo delle variabili di ambiente][portal-env-vars-01]

Per visualizzare i log del contenitore, in **IMPOSTAZIONI** selezionare **Contenitori** e quindi **Log**. Analogamente all'output illustrato nelle sezioni precedenti sull'interfaccia della riga di comando e PowerShell, è possibile visualizzare come il comportamento dello script è stato modificato dalle variabili di ambiente. Vengono visualizzate solo cinque parole, ognuna con una lunghezza minima di otto caratteri.

![Portale che visualizza l'output del log del contenitore][portal-env-vars-02]

## <a name="secure-values"></a>Valori sicuri

Gli oggetti con valori sicuri sono progettati per contenere informazioni riservate, ad esempio le password o le chiavi per le applicazioni. L'uso di valori sicuri per le variabili di ambiente è sia più sicuro che più flessibile rispetto all'inclusione nell'immagine del contenitore. Un'altra opzione consiste nell'usare volumi segreti, come descritto in [Montare un volume segreto in Istanze di contenitore di Azure](container-instances-volume-secret.md).

Le variabili di ambiente con valori sicuri non sono visibili nelle proprietà del contenitore: i relativi valori sono accessibili solo dall'interno del contenitore. Ad esempio, le proprietà del contenitore visualizzate nel portale di Azure o nell'interfaccia della riga di comando di Azure mostrano solo il nome della variabile sicura e non il suo valore.

Impostare una variabile di ambiente sicura, specificando la proprietà `secureValue` anziché il normale `value` per il tipo di variabile. Le due variabili definite nel file YAML seguente illustrano i due tipi di variabili.

### <a name="yaml-deployment"></a>Distribuzione con file YAML

Creare un file `secure-env.yaml` con il frammento seguente.

```yaml
apiVersion: 2018-06-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - "name": "NOTSECRET"
          "value": "my-exposed-value"
        - "name": "SECRET"
          "secureValue": "my-secret-value"
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

Eseguire il comando [az container show][az-container-show] per eseguire query sulle variabili di ambiente del contenitore:

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

Con il comando [az container exec][az-container-exec], che abilita l'esecuzione di un comando in un contenitore in esecuzione, è possibile verificare che la variabile di ambiente sicura è stata impostata. Eseguire il comando seguente per avviare una sessione bash interattiva nel contenitore:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

Dopo aver aperto una shell interattiva all'interno del contenitore, è possibile accedere al valore della variabile `SECRET`:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>Passaggi successivi

Gli scenari basati su attività, ad esempio l'elaborazione batch di un set di dati di grandi dimensioni con diversi contenitori, possono trarre vantaggio dalle variabili di ambiente personalizzate in fase di esecuzione. Per altre informazioni sull'esecuzione di contenitori basati su attività, vedere [Eseguire attività in contenitori in Istanze di contenitore di Azure](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/azurerm.containerinstance/get-azurermcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-azurerm-ps
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
