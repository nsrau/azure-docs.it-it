---
title: Impostare le variabili di ambiente nell'istanza del contenitore
description: Informazioni su come impostare le variabili di ambiente nei contenitori eseguiti nelle istanze di contenitore di Azure
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: c3c76ba0c6131a8ab3de68c13c9dfddaf7e8749a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78379141"
---
# <a name="set-environment-variables-in-container-instances"></a>Impostare le variabili di ambiente nelle istanze di contenitore

L'impostazione delle variabili di ambiente nelle istanze di contenitore consente di fornire la configurazione dinamica dell'applicazione o dello script eseguito dal contenitore. È simile all'argomento della riga di comando `--env` per `docker run`. 

Per impostare le variabili di ambiente in un contenitore, specificarle quando si crea un'istanza di contenitore. Questo articolo illustra esempi di impostazione delle variabili di ambiente quando si avvia un contenitore con l'interfaccia della riga di comando di [Azure](#azure-cli-example), [Azure PowerShell](#azure-powershell-example)e il [portale di Azure](#azure-portal-example). 

Se ad esempio si esegue l'immagine del contenitore Microsoft [ACI-WordCount][aci-wordcount] , è possibile modificarne il comportamento specificando le seguenti variabili di ambiente:

*NumWords*: numero di parole inviate a stdout.

*MinLength*: numero minimo di caratteri in una parola affinché venga conteggiata. Un numero più elevato ignora le parole comuni come "of" e "The".

Se è necessario passare i segreti come variabili di ambiente, istanze di contenitore di Azure supporta [i valori sicuri per i](#secure-values) contenitori Windows e Linux.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Esempio di interfaccia della riga di comando Azure

Per visualizzare l'output predefinito del contenitore [ACI-WordCount][aci-wordcount] , eseguirlo prima con il comando [AZ container create][az-container-create] (nessuna variabile di ambiente specificata):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Per modificare l'output, avviare un secondo contenitore con l'argomento `--environment-variables` aggiunto, specificando i valori per le variabili *NumWords* e *minLength* . In questo esempio si presuppone che l'interfaccia della riga di comando sia in esecuzione in una shell bash o in Azure Cloud Shell. Se si usa il prompt dei comandi di Windows, specificare le variabili con virgolette doppie, ad esempio `--environment-variables "NumWords"="5" "MinLength"="8"`.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

Quando lo stato di entrambi i contenitori viene visualizzato come *terminato* (usare [AZ container Show][az-container-show] per verificare lo stato), visualizzare i log con [AZ container logs][az-container-logs] per visualizzare l'output.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

L'output dei contenitori Mostra come è stato modificato il comportamento dello script del secondo contenitore impostando le variabili di ambiente.

**mycontainer1**
```output
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

**che come mycontainer2**
```output
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Esempio di Azure PowerShell

L'impostazione delle variabili di ambiente in PowerShell è simile all'interfaccia della riga di comando, ma usa l'`-EnvironmentVariable` argomento della riga di comando.

Per prima cosa, avviare il contenitore [ACI-WordCount][aci-wordcount] nella configurazione predefinita con il comando [New-AzContainerGroup][new-Azcontainergroup] :

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Eseguire quindi il comando [New-AzContainerGroup][new-Azcontainergroup] seguente. Questo valore specifica le variabili di ambiente *NumWords* e *minLength* dopo aver popolato una variabile di matrice, `envVars`:

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Quando lo stato di entrambi i contenitori viene *terminato* (usare [Get-AzContainerInstanceLog][azure-instance-log] per controllare lo stato), eseguire il pull dei log con il comando [Get-AzContainerInstanceLog][azure-instance-log] .

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

L'output per ogni contenitore Mostra come è stato modificato lo script eseguito dal contenitore impostando le variabili di ambiente.

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

## <a name="azure-portal-example"></a>Esempio di portale di Azure

Per impostare le variabili di ambiente quando si avvia un contenitore nella portale di Azure, specificarle nella pagina **Avanzate** quando si crea il contenitore.

1. Nella pagina **Avanzate** impostare il criterio di **riavvio** *su in* caso di errore
2. In **variabili di ambiente**immettere `NumWords` con un valore `5` per la prima variabile e immettere `MinLength` con il valore `8` per la seconda variabile. 
1. Selezionare **Verifica + crea** per verificare e quindi distribuire il contenitore.

![Pagina del portale che mostra le caselle di testo e il pulsante Abilita variabile di ambiente][portal-env-vars-01]

Per visualizzare i log del contenitore, in **Impostazioni** selezionare **contenitori**, quindi **log**. Analogamente all'output illustrato nelle sezioni precedenti dell'interfaccia della riga di comando e di PowerShell, è possibile vedere come il comportamento dello script è stato modificato dalle variabili di ambiente. Vengono visualizzate solo cinque parole, ciascuna con una lunghezza minima di otto caratteri.

![Portale che mostra l'output del log del contenitore][portal-env-vars-02]

## <a name="secure-values"></a>Valori sicuri

Gli oggetti con valori sicuri hanno lo scopo di conservare informazioni riservate, ad esempio password o chiavi per l'applicazione. L'uso di valori sicuri per le variabili di ambiente è più sicuro e più flessibile che includerlo nell'immagine del contenitore. Un'altra opzione consiste nell'usare i volumi segreti, descritti in [montare un volume segreto in istanze di contenitore di Azure](container-instances-volume-secret.md).

Le variabili di ambiente con valori sicuri non sono visibili nelle proprietà del contenitore. è possibile accedere ai relativi valori solo dall'interno del contenitore. Ad esempio, le proprietà del contenitore visualizzate nel portale di Azure o nell'interfaccia della riga di comando di Azure visualizzano solo il nome di una variabile sicura, non il relativo valore.

Impostare una variabile di ambiente protetta specificando la proprietà `secureValue` anziché l'`value` normale per il tipo della variabile. Le due variabili definite nell'YAML seguente illustrano i due tipi di variabili.

### <a name="yaml-deployment"></a>Distribuzione YAML

Creare un file di `secure-env.yaml` con il frammento di codice seguente.

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

Eseguire il comando seguente per distribuire il gruppo di contenitori con YAML (modificare il nome del gruppo di risorse secondo necessità):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Verificare le variabili di ambiente

Eseguire il comando [AZ container Show][az-container-show] per eseguire una query sulle variabili di ambiente del contenitore:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

La risposta JSON Mostra sia la chiave che il valore della variabile di ambiente non protetta, ma solo il nome della variabile di ambiente protetta:

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

Con il comando [AZ container Exec][az-container-exec] , che consente l'esecuzione di un comando in un contenitore in esecuzione, è possibile verificare che sia stata impostata la variabile di ambiente Secure. Eseguire il comando seguente per avviare una sessione bash interattiva nel contenitore:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

Una volta aperta una shell interattiva all'interno del contenitore, è possibile accedere al valore della variabile `SECRET`:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>Passaggi successivi

Gli scenari basati su attività, ad esempio l'elaborazione batch di un set di dati di grandi dimensioni con diversi contenitori, possono trarre vantaggio dalle variabili di ambiente personalizzate in fase di esecuzione. Per altre informazioni sull'esecuzione di contenitori basati su attività, vedere [eseguire attività in contenitori con i criteri di riavvio](container-instances-restart-policy.md).

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
