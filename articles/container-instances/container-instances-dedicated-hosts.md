---
title: Distribuisci in host dedicati
description: Usare gli host dedicati per ottenere un vero isolamento a livello di host per i carichi di lavoro
ms.topic: article
ms.date: 01/10/2020
ms.author: danlep
ms.openlocfilehash: 619a39f4d08a4308cb0f566bc50860e9562bf9e4
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903756"
---
# <a name="deploy-on-dedicated-hosts"></a>Distribuisci in host dedicati

"Dedicated" è uno SKU di istanze di contenitore di Azure che fornisce un ambiente di calcolo isolato e dedicato per i contenitori in esecuzione sicura. L'uso dello SKU dedicato consente a ogni gruppo di contenitori di avere un server fisico dedicato in un Data Center di Azure, garantendo un isolamento completo dei carichi di lavoro per soddisfare i requisiti di sicurezza e conformità dell'organizzazione. 

Lo SKU dedicato è adatto per i carichi di lavoro del contenitore che richiedono l'isolamento del carico di lavoro dal punto di vista del server fisico.

## <a name="using-the-dedicated-sku"></a>Uso dello SKU dedicato

> [!IMPORTANT]
> L'uso dello SKU dedicato è disponibile solo nella versione più recente dell'API (2019-12-01) attualmente in fase di implementazione. Specificare questa versione dell'API nel modello di distribuzione. Inoltre, il limite predefinito per qualsiasi sottoscrizione per l'uso dello SKU dedicato è 0. Se si vuole usare questo SKU per le distribuzioni di contenitori di produzione, creare un [richiesta di supporto di Azure][azure-support]

A partire dall'API versione 2019-12-01, nella sezione Proprietà gruppo di contenitori di un modello di distribuzione è presente una proprietà "SKU", necessaria per una distribuzione di ACI. Attualmente, è possibile usare questa proprietà come parte di un modello di distribuzione Azure Resource Manager per ACI. Per altre informazioni sulla distribuzione di risorse ACI con un modello, vedere l' [esercitazione: distribuire un gruppo multicontenitore usando un modello di gestione risorse](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 

La proprietà SKU può avere uno dei valori seguenti:
* Standard: la scelta di distribuzione standard di ACI, che garantisce comunque la sicurezza a livello di hypervisor 
* Dedicato: usato per l'isolamento a livello di carico di lavoro con host fisici dedicati per il gruppo di contenitori

## <a name="modify-your-json-deployment-template"></a>Modificare il modello di distribuzione JSON

Nel modello di distribuzione, in cui viene specificata la risorsa del gruppo di contenitori, verificare che il `"apiVersion": "2019-12-01",`. Nella sezione Properties della risorsa del gruppo di contenitori impostare `"sku": "Dedicated",`.

Ecco un frammento di esempio per la sezione Resources di un modello di distribuzione del gruppo di contenitori che usa lo SKU dedicato:

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
                "osType": "Linux",
            },
            "location": "eastus2euap",
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

Distribuire il modello con il comando [az group deployment create][az-group-deployment-create].

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

Entro pochi secondi si dovrebbe ricevere una risposta iniziale da Azure. Al termine della distribuzione, tutti i dati relativi a tale servizio verranno crittografati con la chiave specificata.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
