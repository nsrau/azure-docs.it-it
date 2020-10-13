---
title: Distribuisci su host dedicato
description: Usare un host dedicato per ottenere un vero isolamento a livello di host per i carichi di lavoro di istanze di contenitore di Azure
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 967d2da61ffdfa9d1723bcab589deb2277d4041e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91825610"
---
# <a name="deploy-on-dedicated-hosts"></a>Eseguire la distribuzione in host dedicati

"Dedicated" è uno SKU di istanze di contenitore di Azure che fornisce un ambiente di calcolo isolato e dedicato per i contenitori in esecuzione sicura. L'uso dello SKU dedicato consente a ogni gruppo di contenitori di avere un server fisico dedicato in un Data Center di Azure, garantendo un isolamento completo dei carichi di lavoro per soddisfare i requisiti di sicurezza e conformità dell'organizzazione. 

Lo SKU dedicato è adatto per i carichi di lavoro del contenitore che richiedono l'isolamento del carico di lavoro dal punto di vista del server fisico.

## <a name="prerequisites"></a>Prerequisiti

* Il limite predefinito per qualsiasi sottoscrizione per l'uso dello SKU dedicato è 0. Se si vuole usare questo SKU per le distribuzioni di contenitori di produzione, creare un [richiesta di supporto di Azure][azure-support] per aumentare il limite.

## <a name="use-the-dedicated-sku"></a>Usare lo SKU dedicato

> [!IMPORTANT]
> L'uso dello SKU dedicato è disponibile solo nella versione più recente dell'API (2019-12-01) attualmente in fase di implementazione. Specificare questa versione dell'API nel modello di distribuzione.
>

A partire dall'API versione 2019-12-01, è presente una `sku` proprietà nella sezione delle proprietà del gruppo di contenitori di un modello di distribuzione, che è necessario per una distribuzione di ACI. Attualmente, è possibile usare questa proprietà come parte di un modello di distribuzione Azure Resource Manager per ACI. Per altre informazioni sulla distribuzione di risorse ACI con un modello [, vedere l'esercitazione: distribuire un gruppo multicontenitore usando un modello di gestione risorse](./container-instances-multi-container-group.md). 

La `sku` proprietà può avere uno dei valori seguenti:
* `Standard` -la scelta di distribuzione ACI standard, che garantisce comunque la sicurezza a livello di hypervisor 
* `Dedicated` -usato per l'isolamento a livello di carico di lavoro con host fisici dedicati per il gruppo di contenitori

## <a name="modify-your-json-deployment-template"></a>Modificare il modello di distribuzione JSON

Nel modello di distribuzione modificare o aggiungere le proprietà seguenti:
* In `resources` , impostare `apiVersion` su `2019-12-01` .
* In Proprietà gruppo di contenitori aggiungere una `sku` proprietà con valore `Dedicated` .

Ecco un frammento di esempio per la sezione Resources di un modello di distribuzione del gruppo di contenitori che usa lo SKU dedicato:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "sku": "Dedicated",
            "containers": {
                [...]
            }
        }
    }
]
```

Di seguito è riportato un modello completo che distribuisce un gruppo di contenitori di esempio che esegue una singola istanza del contenitore:

```json
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
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Dedicated",
                "containers": [
                    {
                        "name": "container1",
                        "properties": {
                            "image": "nginx",
                            "command": [
                                "/bin/sh",
                                "-c",
                                "while true; do echo `date`; sleep 1000000; done"
                            ],
                            "ports": [
                                {
                                    "protocol": "TCP",
                                    "port": 80
                                }
                            ],
                            "environmentVariables": [],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "Always",
                "ipAddress": {
                    "ports": [
                        {
                            "protocol": "TCP",
                            "port": 80
                        }
                    ],
                    "type": "Public"
                },
                "osType": "Linux"
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>Distribuire il gruppo di contenitori

Se il file del modello di distribuzione è stato creato e modificato sul desktop, è possibile caricarlo nella directory Cloud Shell trascinandolo. 

Creare un gruppo di risorse con il comando [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Distribuire il modello con il comando [AZ Deployment Group create][az-deployment-group-create] .

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

Entro pochi secondi si dovrebbe ricevere una risposta iniziale da Azure. Una distribuzione corretta viene eseguita in un host dedicato.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
