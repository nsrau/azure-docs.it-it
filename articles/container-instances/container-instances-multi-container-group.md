---
title: "Distribuire gruppi di più contenitori in Istanze di contenitore di Azure"
description: "Informazioni su come distribuire un gruppo di contenitori con più contenitori in Istanze di contenitore di Azure."
services: container-instances
author: neilpeterson
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 12/19/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 5de818b0b47ee3345ddbc41455f5e953c5b96aa4
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="deploy-a-container-group"></a>Distribuire un gruppo di contenitori

Istanze di contenitore di Azure supporta la distribuzione di più contenitori in un singolo host usando un *gruppo contenitore*. Ciò è utile quando si compila un contenitore collaterale dell'applicazione per la registrazione, il monitoraggio o qualsiasi altra configurazione in cui un servizio necessita di un secondo processo associato.

Questo documento viene illustrata l'esecuzione di una configurazione semplice multi-contenitore Car tramite la distribuzione di un modello di gestione risorse di Azure.

## <a name="configure-the-template"></a>Configurare il modello

Creare un file denominato `azuredeploy.json` e copiarvi il codice JSON seguente.

In questo esempio viene definito un gruppo di contenitori con due contenitori e un indirizzo IP pubblico. Il primo contenitore di gruppo viene eseguita un'applicazione con connessione internet. Il secondo contenitore, ovvero il contenitore collaterale, invia una richiesta HTTP all'applicazione Web principale tramite la rete locale del gruppo.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "microsoft/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "microsoft/aci-tutorial-sidecar"
  },
    "resources": [
      {
        "name": "myContainerGroup",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2017-08-01-preview",
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
              }
            ]
          }
        }
      }
    ],
    "outputs": {
      "containerIPv4Address": {
        "type": "string",
        "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', 'myContainerGroup')).ipAddress.ip]"
      }
    }
  }
```

Per utilizzare un registro di immagini contenitore privato, aggiungere un oggetto per il documento JSON con il formato seguente.

```json
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

Distribuire il modello con il [distribuzione gruppo az creare] [ az-group-deployment-create] comando.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --name myContainerGroup --template-file azuredeploy.json
```

Entro pochi secondi, si dovrebbe ricevere una risposta iniziale da Azure.

## <a name="view-deployment-state"></a>Visualizzare lo stato della distribuzione

Per visualizzare lo stato della distribuzione, utilizzare il [Mostra contenitore az] [ az-container-show] comando. Restituisce l'indirizzo IP pubblico provisioning mediante il quale è possibile accedere all'applicazione.

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Output:

```bash
Name              ResourceGroup    ProvisioningState    Image                                                             IP:ports           CPU/Memory    OsType    Location
----------------  ---------------  -------------------  ----------------------------------------------------------------  -----------------  ------------  --------  ----------
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-tutorial-sidecar,microsoft/aci-tutorial-app:v1      40.118.253.154:80  1.0 core/1.5 gb   Linux     westus
```

## <a name="view-logs"></a>Visualizzare i log

Visualizzare l'output del log di un contenitore usando il [az contenitore registri] [ az-container-logs] comando. L'argomento `--container-name` specifica il contenitore da cui effettuare il pull dei log. In questo esempio viene specificato il primo contenitore.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Output:

```bash
listening on port 80
::1 - - [18/Dec/2017:21:31:08 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [18/Dec/2017:21:31:11 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [18/Dec/2017:21:31:15 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Per visualizzare i log per il contenitore collaterale, eseguire lo stesso comando specificando il nome del secondo contenitore.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Output:

```bash
Every 3s: curl -I http://localhost                          2017-12-18 23:19:34

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
Date: Mon, 18 Dec 2017 23:19:34 GMT
Connection: keep-alive
```

Come si può notare, il contenitore collaterale invia periodicamente una richiesta HTTP all'applicazione Web principale tramite la rete locale del gruppo per verificare che l'applicazione sia in esecuzione. Questo esempio di contenitore collaterale può essere esteso per attivare un avviso se si riceve un codice di risposta HTTP diverso da 200 OK.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo illustrati i passaggi necessari per la distribuzione di un'istanza di Azure multi-contenitore. Per un'esperienza di istanze di contenitori Azure end-to-end, vedere l'esercitazione di istanze di contenitori di Azure.

> [!div class="nextstepaction"]
> [Esercitazione per istanze di contenitori di Azure][aci-tutorial]

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-group-deployment-create]: /cli/azure/group/deployment#az_group_deployment_create
