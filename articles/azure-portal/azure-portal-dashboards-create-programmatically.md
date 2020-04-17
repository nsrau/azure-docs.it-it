---
title: Creare dashboard di Azure a livello di codice
description: Usare un dashboard nel portale di Azure come modello per creare dashboard di Azure a livello di codice. Include il riferimento JSON.
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: mtillman
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: 9ec9a4daad139a4930174ba9e3445e1cda1f8c54
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461311"
---
# <a name="programmatically-create-azure-dashboards"></a>Creare dashboard di Azure a livello di codice

Questo articolo illustra il processo di creazione e pubblicazione di dashboard di Azure a livello di codice. Il dashboard mostrato di seguito viene usato come riferimento in tutto il documento.

![Dashboard di esempio](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Panoramica

I dashboard condivisi nel portale di [Azure](https://portal.azure.com) sono [risorse](../azure-resource-manager/management/overview.md) come le macchine virtuali e gli account di archiviazione. È possibile gestire le risorse a livello di codice usando le API REST di [Azure Resource Manager,](/rest/api/)l'interfaccia [della riga di comando di Azure](/cli/azure)e i comandi di Azure [PowerShell.](/powershell/azure/get-started-azureps)

Molte funzionalità si basano su queste API per semplificare la gestione delle risorse. Ognuno degli strumenti e delle API permette di creare, elencare, recuperare, modificare ed eliminare risorse in modi diversi. Poiché i dashboard sono risorse, è possibile scegliere l'API o lo strumento preferito da usare.

Indipendentemente dagli strumenti utilizzati, per creare un dashboard a livello di codice, si crea una rappresentazione JSON dell'oggetto dashboard. Questo oggetto contiene informazioni sui riquadri nel dashboard. Include dimensioni, posizioni, risorse a cui sono associate ed eventuali personalizzazioni dell'utente.

Il modo più pratico per creare questo documento JSON consiste nell'usare il portale di Azure.The most practical way to build up this JSON document is to use the Azure portal. È possibile aggiungere e posizionare in modo interattivo le piastrelle. Esportare quindi il codice JSON e creare un modello dal risultato per un utilizzo successivo in script, programmi e strumenti di distribuzione.

## <a name="create-a-dashboard"></a>Creare un dashboard

Per creare un dashboard, selezionare **Dashboard** dal menu del portale di [Azure,](https://portal.azure.com) quindi selezionare **Nuovo dashboard.**

![Comando Nuovo dashboard](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Usare la raccolta di riquadri per trovare e aggiungere riquadri. Per aggiungere i riquadri, trascinarli e rilasciarli. Alcuni riquadri supportano il ridimensionamento tramite una maniglia di trascinamento.

![maniglia di trascinamento per modificare le dimensioni](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

Altri hanno dimensioni fisse tra cui scegliere nel loro menu contestuale.

![dimensioni menu contestuale per modificare le dimensioni](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Condividere il dashboard

Dopo aver configurato il dashboard, il passaggio successivo consiste nel pubblicare il dashboard utilizzando il comando **Condividi.**

![condivisione di un dashboard](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Selezionando **Condividi** viene richiesto di scegliere la sottoscrizione e il gruppo di risorse in cui eseguire la pubblicazione. È necessario disporre dell'accesso in scrittura alla sottoscrizione e al gruppo di risorse scelti. Per altre informazioni, vedere Aggiungere o rimuovere assegnazioni di ruolo con Controllo degli accessi in base al ruolo di Azure e il portale di Azure.For more information, see [Add or remove role assignments using Azure RBAC and the Azure portal.](../role-based-access-control/role-assignments-portal.md)

![apportare modifiche alla condivisione e all'accesso](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Recuperare la rappresentazione JSON del dashboard

La pubblicazione richiede solo pochi secondi. Al termine, il passaggio successivo consiste nel recuperare il codice JSON usando il comando **Download.When** it's done, the next step is to fetch the JSON using the Download command.

![scaricare la rappresentazione JSON](./media/azure-portal-dashboards-create-programmatically/download-command.png)

## <a name="create-a-template-from-the-json"></a>Creare un modello dalla rappresentazione JSON

Il passaggio successivo consiste nel creare un modello da questo JSON. Usare tale modello a livello di codice con le API di gestione delle risorse appropriate, gli strumenti della riga di comando o all'interno del portale.

Non è necessario comprendere appieno la struttura JSON del dashboard per creare un modello. Nella maggior parte dei casi, si desidera mantenere la struttura e la configurazione di ogni riquadro. Associare quindi un parametro al set di risorse di Azure a cui puntano i riquadri. Esaminare il dashboard JSON esportato e trovare tutte le occorrenze degli ID risorsa di Azure.Look at your exported JSON dashboard and find all occurrences of Azure resource IDs. Il dashboard di esempio contiene più riquadri che puntano tutti a una singola macchina virtuale di Azure. Questo perché il dashboard esamina solo questa singola risorsa. Se si esegue una ricerca nel codice JSON di esempio, incluso alla fine del documento, per "/subscriptions", sono disponibili diverse occorrenze di questo ID.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Per pubblicare questo dashboard per qualsiasi macchina virtuale in futuro, parametrizzare ogni occorrenza di questa stringa all'interno di JSON.

Esistono due approcci per le API che creano risorse in Azure:There are two approaches for APIs that create resources in Azure:

* Le API imperative creano una risorsa alla volta. Per altre informazioni, vedere [Risorse](/rest/api/resources/resources).
* Un sistema di distribuzione basato su modelli che crea più risorse dipendenti con una singola chiamata API. Per altre informazioni, vedere Distribuire risorse con i modelli di Resource Manager e Azure PowerShell.For more information, see [Deploy resources with Resource Manager templates and Azure PowerShell.](../azure-resource-manager/resource-group-template-deploy.md)

La distribuzione basata su modelli supporta la parametrizzazione e i modelli. Questo approccio viene usato in questo articolo.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Creare un dashboard a livello di codice dal modello usando una distribuzione di modelli

Azure offre la possibilità di orchestrare la distribuzione di più risorse. Si crea un modello di distribuzione che esprime il set di risorse da distribuire e le relazioni tra di esse.  Il formato JSON di ogni risorsa è lo stesso di quando le risorse vengono create una per una. La differenza è che il linguaggio del modello aggiunge alcuni concetti come variabili, parametri, funzioni di base e altro ancora. Questa sintassi estesa è supportata solo nel contesto di una distribuzione di modelli. Non funziona se utilizzato con le API imperative descritte in precedenza. Per altre informazioni, vedere Informazioni sulla struttura e la [sintassi dei modelli di Azure Resource Manager.](../azure-resource-manager/resource-group-authoring-templates.md)

La parametrizzazione deve essere eseguita utilizzando la sintassi dei parametri del modello.  Sostituire tutte le istanze dell'ID di risorsa trovato in precedenza, come illustrato di seguito.

Esempio di proprietà JSON con ID risorsa hardcoded:Example JSON property with hard-coded resource ID:

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

Proprietà JSON di esempio convertita in versione parametrizzata basata sui parametri del modello

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

Dichiarare i metadati del modello richiesto e i parametri nella parte superiore del modello JSON in questo modo:Declare required template metadata and the parameters at the top of the JSON template like this:

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```
Dopo aver configurato il modello, distribuirlo utilizzando uno dei metodi seguenti:

* [API REST](/rest/api/resources/deployments)
* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Interfaccia della riga di comando di Azure](/cli/azure/group/deployment#az-group-deployment-create)
* [Pagina di distribuzione del modello di portale di AzureThe Azure portal template deployment page](https://portal.azure.com/#create/Microsoft.Template)

Successivamente verranno visualizzate due versioni del dashboard di esempio JSON. La prima è la versione esportata dal portale e già associata a una risorsa. The second is the template version that can be programmatically bound to any virtual machine and deployed using Azure Resource Manager.

### <a name="json-representation-of-our-example-dashboard-before-templating"></a>Rappresentazione JSON del dashboard di esempio prima del modello

Questo esempio mostra cosa ci si può aspettare di vedere se avete seguito con questo articolo. Le istruzioni esportano la rappresentazione JSON di un dashboard già distribuito. Gli identificatori di risorsa hardcoded mostrano che questo dashboard punta a una macchina virtuale di Azure specifica.

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": { }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

### <a name="template-representation-of-our-example-dashboard"></a>Rappresentazione basata su modello del dashboard di esempio

La versione del modello del dashboard `virtualMachineName` `virtualMachineResourceGroup`ha `dashboardName`definito tre parametri denominati , e .  I parametri permettono di fare in modo che il dashboard punti a una macchina virtuale di Azure diversa a ogni distribuzione. Questo dashboard può essere configurato e distribuito a livello di codice in modo che punti a qualsiasi macchina virtuale di Azure.This dashboard can be programmatically configured and deployed to point to any Azure virtual machine. Per testare questa funzionalità, copiare il modello seguente e incollarlo nella pagina distribuzione del modello di portale di [Azure.](https://portal.azure.com/#create/Microsoft.Template)

Questo esempio distribuisce solo un dashboard, ma il linguaggio del modello permette di distribuire più risorse e di generare un bundle di uno o più dashboard insieme.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "properties": {
                "lenses": {
                    "0": {
                        "order": 0,
                        "parts": {
                            "0": {
                                "position": {
                                    "x": 0,
                                    "y": 0,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                                "title": "",
                                                "subtitle": ""
                                            }
                                        }
                                    }
                                }
                            },
                            "1": {
                                "position": {
                                    "x": 3,
                                    "y": 0,
                                    "rowSpan": 4,
                                    "colSpan": 8
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                                "title": "Test VM Dashboard",
                                                "subtitle": "Contoso"
                                            }
                                        }
                                    }
                                }
                            },
                            "2": {
                                "position": {
                                    "x": 0,
                                    "y": 2,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "title": "",
                                                "subtitle": "",
                                                "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                                "autoplay": false
                                            }
                                        }
                                    }
                                }
                            },
                            "3": {
                                "position": {
                                    "x": 0,
                                    "y": 4,
                                    "rowSpan": 3,
                                    "colSpan": 11
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "4": {
                                "position": {
                                    "x": 0,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "5": {
                                "position": {
                                    "x": 3,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "6": {
                                "position": {
                                    "x": 6,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "7": {
                                "position": {
                                    "x": 9,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 2
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "id",
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                                    "asset": {
                                        "idInputName": "id",
                                        "type": "VirtualMachine"
                                    },
                                    "defaultMenuItemId": "overview"
                                }
                            }
                        }
                    }
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}
```

Dopo aver visto un esempio di utilizzo di un modello con parametri per distribuire un dashboard, è possibile provare a distribuire il modello usando le API REST di [Azure Resource Manager,](/rest/api/)i comandi [dell'interfaccia della riga di comando](/cli/azure)di Azure o Di Azure [PowerShell.](/powershell/azure/get-started-azureps)
