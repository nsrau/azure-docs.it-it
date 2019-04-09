---
title: 'Esercitazione: distribuire un gruppo multicontenitore in istanze di contenitore di Azure - modello'
description: In questa esercitazione descrive come distribuire un gruppo di contenitori con più contenitori in istanze di contenitore di Azure usando un modello di Azure Resource Manager con la CLI di Azure.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f769beda1654dc9f58ecff733741fb1ab9118031
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006907"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>Esercitazione: Distribuire un gruppo multicontenitore con un modello di Resource Manager

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Gestione risorse](container-instances-multi-container-group.md)

Istanze di Azure Container supporta la distribuzione di più contenitori in un singolo host usando un [gruppo di contenitori](container-instances-container-groups.md). Un gruppo di contenitori è utile quando si compila un contenitore collaterale dell'applicazione per la registrazione, il monitoraggio o qualsiasi altra configurazione in cui un servizio necessita di un secondo processo associato.

In questa esercitazione è seguire questa procedura per eseguire una configurazione multicontenitore con contenitore di due distribuendo un modello di Azure Resource Manager tramite la CLI di Azure. Si apprenderà come:

> [!div class="checklist"]
> * Configurare un modello di gruppo multicontenitore
> * Distribuire il gruppo di contenitori
> * Visualizzare i log dei contenitori

Un modello di Resource Manager può essere facilmente adattato a scenari quando occorre distribuire le risorse dei servizi Azure aggiuntivi (ad esempio, una condivisione file di Azure o una rete virtuale) con il gruppo di contenitori. 

> [!NOTE]
> I gruppi multicontenitore sono attualmente limitati ai contenitori Linux. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-template"></a>Configurare un modello

Per iniziare, copiare il codice JSON seguente in un nuovo file denominato `azuredeploy.json`. In Azure Cloud Shell, è possibile usare Visual Studio Code per creare il file nella directory di lavoro:

```
code azuredeploy.json
```

Il modello di Resource Manager definisce un gruppo di contenitori con due contenitori, un indirizzo IP pubblico e due porte esposte. Il primo contenitore nel gruppo esegue un'applicazione Web con connessione Internet. Il secondo contenitore, ovvero il contenitore collaterale, invia una richiesta HTTP all'applicazione Web principale tramite la rete locale del gruppo.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-10-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

Per usare un registro di immagini del contenitore privato, aggiungere un oggetto al documento JSON con il formato seguente. Per un esempio di implementazione di questa configurazione, vedere la documentazione con le [informazioni di riferimento del modello di Resource Manager in Istanze di contenitore di Azure][template-reference].

```JSON
"imageRegistryCredentials": [
  {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
  }
]
```

## <a name="deploy-the-template"></a>Distribuire il modello

Creare un gruppo di risorse con il comando [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Distribuire il modello con il comando [az group deployment create][az-group-deployment-create].

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file azuredeploy.json
```

Entro pochi secondi si dovrebbe ricevere una risposta iniziale da Azure.

## <a name="view-deployment-state"></a>Visualizzare lo stato della distribuzione

Per visualizzare lo stato della distribuzione, usare il comando [az container show][az-container-show] seguente:

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

Visualizzare l'output del log di un contenitore con il comando [az container logs][az-container-logs]. L'argomento `--container-name` specifica il contenitore da cui effettuare il pull dei log. In questo esempio, il `aci-tutorial-app` contenitore specificato.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Output:

```bash
listening on port 80
::1 - - [21/Mar/2019:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Per visualizzare i log per il contenitore collaterale, eseguire un comando simile che specifica il `aci-tutorial-sidecar` contenitore.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Output:

```bash
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

Come si può notare, il contenitore collaterale invia periodicamente una richiesta HTTP all'applicazione Web principale tramite la rete locale del gruppo per verificare che l'applicazione sia in esecuzione. In questo esempio sidecar può essere ampliato per attivare un avviso se si riceve un codice di risposta HTTP diverso da `200 OK`.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione, è usato un modello Azure Resource Manager per distribuire un gruppo multicontenitore in istanze di contenitore di Azure. Si è appreso come:

> [!div class="checklist"]
> * Configurare un modello di gruppo multicontenitore
> * Distribuire il gruppo di contenitori
> * Visualizzare i log dei contenitori

Per esempi di modelli aggiuntivi, vedere [modelli di Azure Resource Manager per le istanze di contenitore di Azure](container-instances-samples-rm.md).

È inoltre possibile specificare un gruppo multicontenitore con un [file YAML](container-instances-multi-container-yaml.md). Implicazioni più conciso, del formato YAML distribuzione con un file YAML è una scelta ottimale quando la distribuzione include solo le istanze di contenitore.


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
