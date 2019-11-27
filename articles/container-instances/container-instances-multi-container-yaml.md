---
title: Esercitazione-distribuire un gruppo di più contenitori-YAML
description: Questa esercitazione illustra come distribuire un gruppo di contenitori con più contenitori in istanze di contenitore di Azure usando un file YAML con l'interfaccia della riga di comando di Azure.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: cce98ec56ee1d84c087150ba486b9482515b46f0
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533598"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>Esercitazione: distribuire un gruppo multicontenitore usando un file YAML

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Gestione risorse](container-instances-multi-container-group.md)
>

Istanze di Azure Container supporta la distribuzione di più contenitori in un singolo host usando un [gruppo di contenitori](container-instances-container-groups.md). Un gruppo di contenitori è utile quando si compila un'applicazione sidecar per la registrazione, il monitoraggio o qualsiasi altra configurazione in cui un servizio necessita di un secondo processo collegato.

In questa esercitazione si seguono i passaggi per eseguire una semplice configurazione sidecar a due contenitori distribuendo un [file YAML](container-instances-reference-yaml.md) usando l'interfaccia della riga di comando di Azure. Un file YAML fornisce un formato conciso per specificare le impostazioni dell'istanza. Si apprenderà come:

> [!div class="checklist"]
> * Configurare un file YAML
> * Distribuire il gruppo di contenitori
> * Visualizzare i log dei contenitori

> [!NOTE]
> I gruppi multicontenitore sono attualmente limitati ai contenitori Linux.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-yaml-file"></a>Configurare un file YAML

Per distribuire un gruppo multicontenitore con il comando [AZ container create][az-container-create] nell'interfaccia della riga di comando di Azure, è necessario specificare la configurazione del gruppo di contenitori in un file YAML. Passare quindi il file YAML come parametro al comando.

Per iniziare, copiare il file YAML seguente in un nuovo file denominato **deploy-aci.yaml**. In Azure Cloud Shell, è possibile usare Visual Studio Code per creare il file nella directory di lavoro:

```
code deploy-aci.yaml
```

Questo file YAML definisce un gruppo di contenitori chiamato "myContainerGroup" con due contenitori, un indirizzo IP pubblico e due porte esposte. I contenitori vengono distribuiti dalle immagini Microsoft pubbliche. Il primo contenitore nel gruppo esegue un'applicazione Web con connessione Internet. Il secondo contenitore, quello collaterale, esegue periodicamente le richieste HTTP per l'applicazione Web in esecuzione nel primo contenitore tramite la rete locale del gruppo di contenitori.

```YAML
apiVersion: 2018-10-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: mcr.microsoft.com/azuredocs/aci-tutorial-sidecar
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

Per usare un registro di immagini del contenitore privato, aggiungere la proprietà `imageRegistryCredentials` al gruppo di contenitori, con i valori modificati per l'ambiente:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>Distribuire il gruppo di contenitori

Creare un gruppo di risorse con il comando [AZ Group create][az-group-create] :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Distribuire il gruppo di contenitori con il comando [AZ container create][az-container-create] , passando il file YAML come argomento:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

Entro pochi secondi si dovrebbe ricevere una risposta iniziale da Azure.

## <a name="view-deployment-state"></a>Visualizzare lo stato della distribuzione

Per visualizzare lo stato della distribuzione, usare il comando [AZ container Show][az-container-show] seguente:

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Se si desidera visualizzare l'applicazione in esecuzione, passare al relativo indirizzo IP nel browser. Ad esempio, l'indirizzo IP è `52.168.26.124` in questo output di esempio:

```bash
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Visualizzare i log dei contenitori

Visualizzare l'output del log di un contenitore usando il comando [AZ container logs][az-container-logs] . L'argomento `--container-name` specifica il contenitore da cui effettuare il pull dei log. In questo esempio viene specificato il contenitore `aci-tutorial-app`.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Output:

```console
listening on port 80
::1 - - [21/Mar/2019:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Per visualizzare i log per il contenitore sidecar, eseguire un comando simile che specifichi il contenitore `aci-tutorial-sidecar`.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Output:

```console
Every 3s: curl -I http://localhost                          2019-03-21 20:36:41

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
Date: Thu, 21 Mar 2019 20:36:41 GMT
Connection: keep-alive
```

Come si può notare, il contenitore collaterale invia periodicamente una richiesta HTTP all'applicazione Web principale tramite la rete locale del gruppo per verificare che l'applicazione sia in esecuzione. Questo esempio di sidecar potrebbe essere espanso per attivare un avviso se è stato ricevuto un codice di risposta HTTP diverso da `200 OK`.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato usato un file YAML per distribuire un gruppo di più contenitori in istanze di contenitore di Azure. Si è appreso come:

> [!div class="checklist"]
> * Configurare un file YAML per un gruppo di più contenitori
> * Distribuire il gruppo di contenitori
> * Visualizzare i log dei contenitori

È anche possibile specificare un gruppo di più contenitori usando un [modello di gestione risorse](container-instances-multi-container-group.md). Un modello di Gestione risorse può essere facilmente adattato per gli scenari in cui è necessario distribuire altre risorse dei servizi di Azure con il gruppo di contenitori.

<!-- LINKS - External -->


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
