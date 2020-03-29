---
title: Distribuzione su host dedicato
description: Usare un host dedicato per ottenere un vero isolamento a livello di host per i carichi di lavoro delle istanze del contenitore di AzureUse a dedicated host to achieve true host-level isolation for your Azure Container Instances workloads
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: adad0ddfc78530b3a3a7c139d9a95ec4790c8053
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934138"
---
# <a name="deploy-on-dedicated-hosts"></a>Eseguire la distribuzione in host dedicati

"Dedicato" è uno sku per le istanze del contenitore di Azure (ACI) che fornisce un ambiente di elaborazione isolato e dedicato per l'esecuzione sicura dei contenitori. L'uso dello sku dedicato comporta che ogni gruppo di contenitori abbia un server fisico dedicato in un data center di Azure, garantendo l'isolamento completo del carico di lavoro per soddisfare i requisiti di sicurezza e conformità dell'organizzazione. 

Lo sku dedicato è appropriato per i carichi di lavoro dei contenitori che richiedono l'isolamento del carico di lavoro dal punto di vista del server fisico.

## <a name="prerequisites"></a>Prerequisiti

* Il limite predefinito per ogni sottoscrizione per l'utilizzo di sku dedicato è 0.The default limit for any subscription to use the dedicated sku is 0. Se si vuole usare questo sku per le distribuzioni del contenitore di produzione, creare una richiesta di supporto di [Azure][azure-support] per aumentare il limite.

## <a name="use-the-dedicated-sku"></a>Usare lo sku dedicato

> [!IMPORTANT]
> L'uso dello sku dedicato è disponibile solo nell'ultima versione dell'API (2019-12-01) attualmente in fase di implementazione. Specificare questa versione dell'API nel modello di distribuzione.
>

A partire dalla versione API 2019-12-01, è presente una `sku` proprietà nella sezione delle proprietà del gruppo di contenitori di un modello di distribuzione, necessaria per una distribuzione ACI. Attualmente, è possibile usare questa proprietà come parte di un modello di distribuzione di Azure Resource Manager per ACI. Per altre informazioni sulla distribuzione di risorse ACI con un modello, vedere [Esercitazione: Distribuire un gruppo multicontenitore usando un modello](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group)di Resource Manager. 

La `sku` proprietà può avere uno dei seguenti valori:
* `Standard`- la scelta di implementazione ACI standard, che garantisce ancora la sicurezza a livello di hypervisor 
* `Dedicated`- utilizzato per l'isolamento a livello di carico di lavoro con host fisici dedicati per il gruppo di contenitori- used for workload level isolation with dedicated physical hosts for the container group

## <a name="modify-your-json-deployment-template"></a>Modificare il modello di distribuzione JSONModify your JSON deployment template

Nel modello di distribuzione modificare o aggiungere le proprietà seguenti:In your deployment template, modify or add the following properties:
* In `resources`, `apiVersion` `2012-12-01`impostare su .
* Nelle proprietà del gruppo `sku` di contenitori aggiungere una proprietà con valore `Dedicated`.

Ecco un frammento di codice di esempio per la sezione delle risorse di un modello di distribuzione di gruppi di contenitori che usa lo sku dedicato:Here is an example snippet for the resources section of a container group deployment template that uses the dedicated sku:

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

Di seguito è riportato un modello completo che distribuisce un gruppo di contenitori di esempio che esegue una singola istanza del contenitore:Following is a complete template that deploys a sample container group running a single container instance:

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

## <a name="deploy-your-container-group"></a>Distribuire il gruppo di contenitoriDeploy your container group

Se il file del modello di distribuzione è stato creato e modificato sul desktop, è possibile caricarlo nella directory di Cloud Shell trascinandoilo. 

Creare un gruppo di risorse con il comando [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Distribuire il modello con il comando [az group deployment create][az-group-deployment-create].

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

Entro pochi secondi si dovrebbe ricevere una risposta iniziale da Azure. Una distribuzione corretta avviene in un host dedicato.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
