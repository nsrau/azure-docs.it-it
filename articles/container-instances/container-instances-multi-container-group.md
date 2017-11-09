---
title: Istanze di contenitore di Azure - Gruppo multicontenitore | Azure Docs
description: Istanze di contenitore di Azure - Gruppo multicontenitore
services: container-instances
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 140f58582645ea32f77e901eb13364ed145bbecf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-container-group"></a>Distribuire un gruppo di contenitori

Istanze di contenitore di Azure supporta la distribuzione di più contenitori in un singolo host usando un *gruppo di contenitori*. Ciò è utile quando si compila un contenitore collaterale dell'applicazione per la registrazione, il monitoraggio o qualsiasi altra configurazione in cui un servizio necessita di un secondo processo associato. 

Questo documento descrive come eseguire una configurazione multicontenitore con contenitore collaterale tramite un modello di Azure Resource Manager.

## <a name="configure-the-template"></a>Configurare il modello

Creare un file denominato `azuredeploy.json` e copiare il codice JSON seguente al suo interno. 

In questo esempio viene definito un gruppo di contenitori con due contenitori e un indirizzo IP pubblico. Il primo contenitore del gruppo esegue un'applicazione con connessione Internet. Il secondo contenitore, ovvero il contenitore collaterale, invia una richiesta HTTP all'applicazione Web principale tramite la rete locale del gruppo. 

Questo esempio di contenitore collaterale può essere esteso per attivare un avviso se si riceve un codice di risposta HTTP diverso da 200 OK. 

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

Per usare un registro di immagini del contenitore privato, aggiungere un oggetto al documento JSON con il formato seguente.

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

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#create).

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Distribuire il modello con il comando [az group deployment create](/cli/azure/group/deployment#create).

```azurecli-interactive
az group deployment create --name myContainerGroup --resource-group myResourceGroup --template-file azuredeploy.json
```

Entro pochi secondi si riceverà una risposta iniziale da Azure. 

## <a name="view-deployment-state"></a>Visualizzare lo stato della distribuzione

Per visualizzare lo stato della distribuzione, usare il comando `az container show`. Il comando restituisce l'indirizzo IP pubblico con provisioning eseguito su cui è possibile accedere all'applicazione.

```azurecli-interactive
az container show --name myContainerGroup --resource-group myResourceGroup -o table
```

Output:

```azurecli
Name              ResourceGroup    ProvisioningState    Image                                                             IP:ports           CPU/Memory    OsType    Location
----------------  ---------------  -------------------  ----------------------------------------------------------------  -----------------  ------------  --------  ----------
myContainerGroup  myResourceGrou2  Succeeded            microsoft/aci-tutorial-sidecar,microsoft/aci-tutorial-app:v1      40.118.253.154:80  1.0 core/1.5 gb   Linux     westus
```

## <a name="view-logs"></a>Visualizzare i log   

Visualizzare l'output del log di un contenitore con il comando `az container logs`. L'argomento `--container-name` specifica il contenitore da cui effettuare il pull dei log. In questo esempio viene specificato il primo contenitore. 

```azurecli-interactive
az container logs --name myContainerGroup --container-name aci-tutorial-app --resource-group myResourceGroup
```

Output:

```bash
istening on port 80
::1 - - [27/Jul/2017:17:35:29 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [27/Jul/2017:17:35:32 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [27/Jul/2017:17:35:35 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [27/Jul/2017:17:35:38 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Per visualizzare i log per il contenitore collaterale, eseguire lo stesso comando specificando il nome del secondo contenitore.

```azurecli-interactive
az container logs --name myContainerGroup --container-name aci-tutorial-sidecar --resource-group myResourceGroup
```

Output:

```bash
Every 3.0s: curl -I http://localhost                                                                                                                       Mon Jul 17 11:27:36 2017

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
Accept-Ranges: bytes
Content-Length: 1663
Content-Type: text/html; charset=utf-8
Last-Modified: Sun, 16 Jul 2017 02:08:22 GMT
Date: Mon, 17 Jul 2017 18:27:36 GMT
```

Come si può notare, il contenitore collaterale invia periodicamente una richiesta HTTP all'applicazione Web principale tramite la rete locale del gruppo per verificare che l'applicazione sia in esecuzione.

## <a name="next-steps"></a>Passaggi successivi

In questo documento sono stati illustrati i passaggi necessari per la distribuzione di un'istanza multicontenitore di Azure. Per un'esperienza end-to-end di Istanze di contenitore di Azure, vedere l'apposita esercitazione.

> [!div class="nextstepaction"]
> [Esercitazione su Istanze di contenitore di Azure]: ./container-instances-tutorial-prepare-app.md
