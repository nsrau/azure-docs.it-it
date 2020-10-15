---
title: Creare dashboard di Azure a livello di codice
description: Usare un dashboard nel portale di Azure come modello per creare dashboard di Azure a livello di codice. Include il riferimento JSON.
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: mtillman
ms.service: azure-portal
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: 7f52bd94a0286ea50d09ab7c77dce339e8a3ebf3
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089367"
---
# <a name="programmatically-create-azure-dashboards"></a>Creare dashboard di Azure a livello di codice

Questo articolo illustra il processo di creazione e pubblicazione di dashboard di Azure a livello di codice. Il dashboard mostrato di seguito viene usato come riferimento in tutto il documento.

![Dashboard di esempio](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Panoramica

I dashboard condivisi nel [portale di Azure](https://portal.azure.com) sono [risorse come le](../azure-resource-manager/management/overview.md) macchine virtuali e gli account di archiviazione. È possibile gestire le risorse a livello di programmazione usando le [API REST di Azure Resource Manager](/rest/api/), l'interfaccia della riga di comando di [Azure](/cli/azure)e i [comandi di Azure PowerShell](/powershell/azure/get-started-azureps).

Molte funzionalità di si basano su queste API per semplificare la gestione delle risorse. Ognuno degli strumenti e delle API permette di creare, elencare, recuperare, modificare ed eliminare risorse in modi diversi. Poiché i dashboard sono risorse, è possibile scegliere l'API o lo strumento preferito da usare.

Indipendentemente dagli strumenti usati, per creare un dashboard a livello di codice, si costruisce una rappresentazione JSON dell'oggetto dashboard. Questo oggetto contiene informazioni sui riquadri nel dashboard. Include le dimensioni, le posizioni, le risorse a cui sono associati e tutte le personalizzazioni degli utenti.

Il modo più pratico per creare questo documento JSON consiste nell'usare il portale di Azure. È possibile aggiungere e posizionare i riquadri in modo interattivo. Esportare quindi il file JSON e creare un modello dal risultato per un uso successivo in script, programmi e strumenti di distribuzione.

## <a name="create-a-dashboard"></a>Creare un dashboard

Per creare un dashboard, selezionare **Dashboard** dal menu [portale di Azure](https://portal.azure.com) , quindi selezionare **nuovo dashboard**.

![Comando Nuovo dashboard](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Usare la raccolta di riquadri per trovare e aggiungere riquadri. Per aggiungere i riquadri, trascinarli e rilasciarli. Alcuni riquadri supportano il ridimensionamento tramite un handle di trascinamento.

![handle di trascinamento per modificare le dimensioni](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

Altri hanno dimensioni fisse da scegliere nel menu di scelta rapida.

![dimensioni del menu di scelta rapida per modificare le dimensioni](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Condividere il dashboard

Dopo aver configurato il dashboard, il passaggio successivo consiste nel pubblicare il dashboard usando il comando **share** .

![condivisione di un dashboard](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Selezionando **Condividi** viene richiesto di scegliere la sottoscrizione e il gruppo di risorse in cui eseguire la pubblicazione. È necessario avere accesso in scrittura alla sottoscrizione e al gruppo di risorse scelti. Per altre informazioni, vedere [Aggiungere o rimuovere assegnazioni di ruolo di Azure usando il portale di Azure](../role-based-access-control/role-assignments-portal.md).

![modificare la condivisione e l'accesso](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Recuperare la rappresentazione JSON del dashboard

La pubblicazione richiede solo pochi secondi. Al termine, il passaggio successivo consiste nel recuperare il file JSON usando il comando di **download** .

![scaricare la rappresentazione JSON](./media/azure-portal-dashboards-create-programmatically/download-command.png)

## <a name="create-a-template-from-the-json"></a>Creare un modello dalla rappresentazione JSON

Il passaggio successivo consiste nel creare un modello da questo JSON. Usare questo modello a livello di codice con le API di gestione delle risorse appropriate, gli strumenti da riga di comando o all'interno del portale.

Non è necessario comprendere completamente la struttura JSON del dashboard per creare un modello. Nella maggior parte dei casi, si desidera mantenere la struttura e la configurazione di ogni riquadro. Quindi parametrizzare il set di risorse di Azure a cui puntano i riquadri. Esaminare il dashboard JSON esportato e trovare tutte le occorrenze degli ID risorsa di Azure. Il dashboard di esempio contiene più riquadri che puntano tutti a una singola macchina virtuale di Azure. Questo perché il dashboard esamina solo questa singola risorsa. Se si esegue una ricerca nell'esempio JSON, incluso alla fine del documento, per "/Subscriptions" si trovano diverse occorrenze di questo ID.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Per pubblicare questo dashboard per qualsiasi macchina virtuale in futuro, parametrizzare ogni occorrenza di questa stringa all'interno di JSON.

Esistono due approcci per le API che creano risorse in Azure:

* Le API imperative creano una risorsa alla volta. Per altre informazioni, vedere [Risorse](/rest/api/resources/resources).
* Un sistema di distribuzione basato su modello che crea più risorse dipendenti con una singola chiamata API. Per altre informazioni, vedere  [distribuire risorse con modelli e Azure PowerShell di gestione risorse](../azure-resource-manager/templates/deploy-powershell.md).

La distribuzione basata su modello supporta la parametrizzazione e la creazione di modelli. Questo approccio viene usato in questo articolo.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Creare un dashboard a livello di codice dal modello usando una distribuzione di modelli

Azure offre la possibilità di orchestrare la distribuzione di più risorse. Viene creato un modello di distribuzione che esprime il set di risorse da distribuire e le relazioni tra di esse.  Il formato JSON di ogni risorsa è lo stesso di quando le risorse vengono create una per una. La differenza è che il linguaggio del modello aggiunge alcuni concetti come variabili, parametri, funzioni di base e altro ancora. Questa sintassi estesa è supportata solo nel contesto di una distribuzione modello. Non funziona se usato con le API imperative descritte in precedenza. Per altre informazioni, vedere [comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md).

La parametrizzazione deve essere eseguita utilizzando la sintassi dei parametri del modello.  Si sostituiscono tutte le istanze dell'ID risorsa trovato in precedenza, come illustrato di seguito.

Proprietà JSON di esempio con ID risorsa hardcoded:

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

Proprietà JSON di esempio convertita in versione parametrizzata basata sui parametri del modello

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

Dichiarare i metadati del modello obbligatori e i parametri nella parte superiore del modello JSON come segue:

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
Dopo aver configurato il modello, distribuirlo usando uno dei metodi seguenti:

* [API REST](/rest/api/resources/deployments)
* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Interfaccia della riga di comando di Azure](/cli/azure/group/deployment#az-group-deployment-create)
* [Pagina di distribuzione del modello di portale di Azure](https://portal.azure.com/#create/Microsoft.Template)

Verranno quindi visualizzate due versioni del dashboard di esempio JSON. La prima è la versione esportata dal portale e già associata a una risorsa. Il secondo è la versione del modello che può essere associata a livello di codice a qualsiasi macchina virtuale e distribuita con Azure Resource Manager.

### <a name="json-representation-of-our-example-dashboard-before-templating"></a>Rappresentazione JSON del dashboard di esempio prima del modello

Questo esempio mostra cosa si può prevedere se è stato seguito questo articolo. Le istruzioni esportano la rappresentazione JSON di un dashboard già distribuito. Gli identificatori di risorsa hardcoded indicano che questo dashboard punta a una macchina virtuale di Azure specifica.

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

La versione del modello del dashboard ha definito tre parametri `virtualMachineName` , `virtualMachineResourceGroup` , e `dashboardName` .  I parametri permettono di fare in modo che il dashboard punti a una macchina virtuale di Azure diversa a ogni distribuzione. Questo dashboard può essere configurato e distribuito a livello di codice per puntare a qualsiasi macchina virtuale di Azure. Per testare questa funzionalità, copiare il modello seguente e incollarlo nella [pagina di distribuzione del modello portale di Azure](https://portal.azure.com/#create/Microsoft.Template).

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

Ora che è stato illustrato un esempio di uso di un modello con parametri per distribuire un dashboard, è possibile provare a distribuire il modello usando le [API REST di Azure Resource Manager](/rest/api/), l'interfaccia della riga di comando di [Azure](/cli/azure)o i [comandi di Azure PowerShell](/powershell/azure/get-started-azureps).