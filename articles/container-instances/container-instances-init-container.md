---
title: Esegui i contenitori init
description: Eseguire i contenitori init in istanze di contenitore di Azure per eseguire le attività di installazione in un gruppo di contenitori prima dell'esecuzione dei contenitori dell'applicazione.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 5a729263ee632eb9227694ec8684eb6889c6324b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85954282"
---
# <a name="run-an-init-container-for-setup-tasks-in-a-container-group"></a>Eseguire un contenitore init per le attività di installazione in un gruppo di contenitori

Istanze di contenitore di Azure supporta i *contenitori init* in un gruppo di contenitori. I contenitori init vengono eseguiti fino al completamento prima dell'avvio del contenitore o dei contenitori dell'applicazione. Analogamente ai [contenitori Kubernetes init](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/), usare uno o più contenitori init per eseguire la logica di inizializzazione per i contenitori di app, ad esempio l'impostazione di account, l'esecuzione di script di installazione o la configurazione di database.

Questo articolo illustra come usare un modello di Azure Resource Manager per configurare un gruppo di contenitori con un contenitore init.

## <a name="things-to-know"></a>Informazioni importanti

* **Versione API** : è necessaria almeno l'API istanze di contenitore di Azure versione 2019-12-01 per distribuire i contenitori init. Eseguire la distribuzione usando una `initContainers` Proprietà in un [file YAML](container-instances-multi-container-yaml.md) o un [modello di gestione risorse](container-instances-multi-container-group.md).
* **Ordine di esecuzione** : i contenitori init vengono eseguiti nell'ordine specificato nel modello e prima di altri contenitori. Per impostazione predefinita, è possibile specificare un massimo di 59 contenitori init per gruppo di contenitori. È necessario che nel gruppo sia presente almeno un contenitore non init.
* **Ambiente host** : i contenitori init vengono eseguiti sullo stesso hardware del resto dei contenitori del gruppo.
* **Risorse** : non si specificano le risorse per i contenitori init. Vengono concesse le risorse totali, ad esempio CPU e memoria disponibili per il gruppo di contenitori. Mentre viene eseguito un contenitore init, nessun altro contenitore viene eseguito nel gruppo.
* **Proprietà supportate** : i contenitori init possono usare proprietà del gruppo, ad esempio volumi, segreti e identità gestite. Tuttavia, non possono usare porte o un indirizzo IP se configurato per il gruppo di contenitori. 
* **Criteri di riavvio** : ogni contenitore init deve uscire correttamente prima dell'avvio del successivo contenitore del gruppo. Se un contenitore init non viene chiuso correttamente, l'azione di riavvio dipende dal [criterio di riavvio](container-instances-restart-policy.md) configurato per il gruppo:

    |Criteri nel gruppo  |Criteri in init  |
    |---------|---------|
    |Sempre     |OnFailure         |
    |OnFailure     |OnFailure         |
    |Mai     |Mai         |
* **Addebiti** : il gruppo di contenitori viene addebitato in base alla prima distribuzione di un contenitore init.

## <a name="resource-manager-template-example"></a>Esempio di modello di Resource Manager

Per iniziare, copiare il codice JSON seguente in un nuovo file denominato `azuredeploy.json`. Il modello configura un gruppo di contenitori con un contenitore init e due contenitori di applicazioni:

* Il contenitore *init1* esegue l'immagine [busybox](https://hub.docker.com/_/busybox) dall'hub docker. Dorme per 60 secondi e quindi scrive una stringa della riga di comando in un file in un [volume emptyDir](container-instances-volume-emptydir.md).
* Entrambi i contenitori di applicazioni eseguono l' `aci-wordcount` immagine del contenitore Microsoft:
    * Il contenitore *Hamlet* esegue l'app WordCount con la relativa configurazione predefinita, contando le frequenze delle parole in Shakespeare ' s Play *Hamlet*.
    * Il contenitore dell'app *Juliet* legge la stringa della riga di comando dal volume emptDir per eseguire l'app WordCount in *Romeo e Giulietta*di Shakespeare.

Per altre informazioni ed esempi sull'uso dell' `aci-wordcount` immagine, vedere [impostare le variabili di ambiente nelle istanze di contenitore](container-instances-environment-variables.md).

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
                "sku": "Standard",
                "initContainers": [
                {
                    "name": "init1",
                    "properties": {
                        "image": "busybox",
                        "environmentVariables": [],
                        "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                         "command": [
                            "/bin/sh",
                            "-c",
                            "sleep 60; echo python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html > /mnt/emptydir/command_line.txt"
                        ]
                    }
                }
            ], 
                "containers": [
                    {
                        "name": "hamlet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    },
                    {
                        "name": "juliet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                            ],
                            "command": [
                                "/bin/sh",
                                "-c",
                                "$(cat /mnt/emptydir/command_line.txt)"
                            ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "OnFailure",
                "osType": "Linux",
                "volumes": [
                    {
                        "name": "emptydir1",
                        "emptyDir": {}
                    }
                ]           
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-the-template"></a>Distribuire il modello

Creare un gruppo di risorse con il comando [az group create][az-group-create].

```azurecli
az group create --name myResourceGroup --location eastus
```

Distribuire il modello con il comando [AZ Deployment Group create][az-deployment-group-create] .

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

In un gruppo con un contenitore init, il tempo di distribuzione viene aumentato a causa del tempo necessario per il completamento del contenitore o dei contenitori init.


## <a name="view-container-logs"></a>Visualizzare i log dei contenitori

Per verificare la corretta esecuzione del contenitore init, visualizzare l'output del log dei contenitori di app usando il comando [AZ container logs][az-container-logs] . L'argomento `--container-name` specifica il contenitore da cui effettuare il pull dei log. In questo esempio, effettuare il pull dei log per i contenitori *Hamlet* e *Juliet* , che mostrano un output del comando diverso:

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name hamlet
```

Output:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name juliet
```

Output:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Passaggi successivi

I contenitori init consentono di eseguire attività di installazione e inizializzazione per i contenitori di applicazioni. Per altre informazioni sull'esecuzione di contenitori basati su attività, vedere [eseguire attività in contenitori con i criteri di riavvio](container-instances-restart-policy.md).

Istanze di contenitore di Azure offre altre opzioni per modificare il comportamento dei contenitori di applicazioni. Tra gli esempi sono inclusi:

* [Impostare le variabili di ambiente nelle istanze di contenitore](container-instances-environment-variables.md)
* [Impostare la riga di comando in un'istanza di contenitore per sostituire l'operazione della riga di comando predefinita](container-instances-start-command.md)


[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-container-logs]: /cli/azure/container#az-container-logs
