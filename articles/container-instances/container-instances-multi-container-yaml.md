---
title: Distribuire gruppi di più contenitori in Istanze di contenitore di Azure con l'interfaccia della riga di comando di Azure e YAML
description: Informazioni su come distribuire un gruppo di contenitori con più contenitori in Istanze di contenitore di Azure con l'interfaccia della riga di comando di Azure e un file YAML.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 07/17/2018
ms.author: danlep
ms.openlocfilehash: ffc9cf24e686924878a752b5d9df31160328ef0a
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854712"
---
# <a name="deploy-a-multi-container-container-group-with-yaml"></a>Distribuire un gruppo di contenitori con più contenitori con YAML

Istanze di contenitore di Azure supporta la distribuzione di più contenitori in un singolo host usando un [gruppo di contenitori](container-instances-container-groups.md). I gruppi di contenitori con più contenitori sono utili quando si compila un contenitore collaterale dell'applicazione per la registrazione, il monitoraggio o qualsiasi altra configurazione in cui un servizio necessita di un secondo processo associato.

Esistono due metodi per la distribuzione di gruppi di contenitori con più contenitori usando l'interfaccia della riga di comando di Azure:

* Distribuzione con un file YAML (questo articolo)
* [Distribuzione del modello di Resource Manager](container-instances-multi-container-group.md)

A causa della natura più concisa del formato YAML, la distribuzione con un file YAML è consigliata quando la distribuzione include *solo* istanze di contenitore. Se è necessario distribuire ulteriori risorse del servizio di Azure (ad esempio, una condivisione di File di Azure) al momento della distribuzione delle istanze di contenitore, è consigliabile la distribuzione del modello di Resource Manager.

> [!NOTE]
> I gruppi multicontenitore sono attualmente limitati ai contenitori Linux. Microsoft si impegna per rendere disponibili tutte le funzionalità anche per i contenitori Windows, ma nel frattempo è possibile trovare le differenze correnti tra le piattaforme in [Quotas and region availability for Azure Container Instances](container-instances-quotas.md) (Quote e aree disponibili per Istanze di contenitore di Azure).

## <a name="configure-the-yaml-file"></a>Configurare il file YAML

Per distribuire un gruppo di contenitori con più contenitori con il comando [az container create][az-container-create] nell'interfaccia della riga di comando di Azure, è necessario specificare la configurazione del gruppo di contenitori in un file YAML, quindi passare il file YAML come parametro per il comando.

Per iniziare, copiare il file YAML seguente in un nuovo file denominato **deploy-aci.yaml**.

Questo file YAML definisce un gruppo di contenitori chiamato "myContainerGroup" con due contenitori, un indirizzo IP pubblico e due porte esposte. Il primo contenitore nel gruppo esegue un'applicazione Web con connessione Internet. Il secondo contenitore, quello collaterale, esegue periodicamente le richieste HTTP per l'applicazione Web in esecuzione nel primo contenitore tramite la rete locale del gruppo di contenitori.

```YAML
apiVersion: 2018-06-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: microsoft/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: microsoft/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
    - protocol: tcp
      port: '8080'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

## <a name="deploy-the-container-group"></a>Distribuire il gruppo di contenitori

Creare un gruppo di risorse con il comando [az group create][az-group-create]:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Distribuire il gruppo di contenitori con il comando [az container create][az-container-create], passando il file YAML come argomento:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

Entro pochi secondi si dovrebbe ricevere una risposta iniziale da Azure.

## <a name="view-deployment-state"></a>Visualizzare lo stato della distribuzione

Per visualizzare lo stato della distribuzione, usare il comando [az container show][az-container-show] seguente:

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Se si desidera visualizzare l'applicazione in esecuzione, passare al relativo indirizzo IP nel browser. Ad esempio, l'indirizzo IP è `52.168.26.124` in questo output di esempio:

```bash
Name              ResourceGroup    ProvisioningState    Image                                                           IP:ports               CPU/Memory       OsType    Location
----------------  ---------------  -------------------  --------------------------------------------------------------  ---------------------  ---------------  --------  ----------
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-helloworld:latest,microsoft/aci-tutorial-sidecar  52.168.26.124:80,8080  1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-logs"></a>Visualizzare i log

Visualizzare l'output del log di un contenitore con il comando [az container logs][az-container-logs]. L'argomento `--container-name` specifica il contenitore da cui effettuare il pull dei log. In questo esempio viene specificato il primo contenitore.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Output:

```console
listening on port 80
::1 - - [09/Jan/2018:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Per visualizzare i log per il contenitore collaterale, eseguire lo stesso comando specificando il nome del secondo contenitore.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Output:

```console
Every 3s: curl -I http://localhost                          2018-01-09 23:25:11

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Tue, 09 Jan 2018 23:25:11 GMT
Connection: keep-alive
```

Come si può notare, il contenitore collaterale invia periodicamente una richiesta HTTP all'applicazione Web principale tramite la rete locale del gruppo per verificare che l'applicazione sia in esecuzione. Questo esempio di contenitore collaterale può essere esteso per attivare un avviso se si riceve un codice di risposta HTTP diverso da 200 OK.

## <a name="deploy-from-private-registry"></a>Eseguire la distribuzione da un registro privato

Per usare un registro privato di immagini di contenitori, includere il file YAML seguente con i valori modificati per l'ambiente specifico:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

Ad esempio, il file YAML seguente consente di distribuire un gruppo di contenitori con un singolo contenitore, la cui l'immagine viene recuperata tramite pull da un Registro contenitori di Azure privato denominato "myregistry":

```YAML
apiVersion: 2018-06-01
location: eastus
name: myContainerGroup2
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: myregistry.azurecr.io/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
  imageRegistryCredentials:
  - server: myregistry.azurecr.io
    username: myregistry
    password: REGISTRY_PASSWORD
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

## <a name="export-container-group-to-yaml"></a>Esportare un gruppo di contenitori in un file YAML

È possibile esportare la configurazione di un gruppo di contenitori esistente in un file YAML usando il comando dell'interfaccia della riga di comando di Azure [az container export][az-container-export].

Utile per mantenere la configurazione di un gruppo di contenitori, l'esportazione consente di archiviare le configurazioni dei gruppi di contenitori nel controllo della versione per la "configurazione come codice". In alternativa, usare il file esportato come punto di partenza per lo sviluppo di una nuova configurazione in YAML.

Esportare la configurazione per il gruppo di contenitori creato in precedenza eseguendo il comando [az container export][az-container-export] seguente:

```azurecli-interactive
az container export --resource-group myResourceGroup --name myContainerGroup --file deployed-aci.yaml
```

Se il comando ha esito positivo non genera output, ma è possibile visualizzare il contenuto del file per visualizzare il risultato. Ad esempio, le prime righe con `head`:

```console
$ head deployed-aci.yaml
additional_properties: {}
apiVersion: '2018-06-01'
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: microsoft/aci-helloworld:latest
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato i passaggi necessari per la distribuzione di un'istanza multicontenitore di Azure. Per un'esperienza completa di Istanze di contenitore di Azure, incluse informazioni sull'uso del registro contenitori di Azure privato, vedere l'esercitazione su Istanze di contenitore di Azure.

> [!div class="nextstepaction"]
> [Esercitazione su Istanze di contenitore di Azure][aci-tutorial]

<!-- LINKS - External -->
[cli-issue-6525]: https://github.com/Azure/azure-cli/issues/6525

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-export]: /cli/azure/container#az-container-export
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
