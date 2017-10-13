---
title: Creare dashboard di Azure a livello di codice | Microsoft Docs
description: Questo articolo descrive come creare dashboard di Azure a livello di codice.
services: azure-portal
documentationcenter: 
author: adamab
manager: timlt
editor: tysonn
ms.service: multiple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/01/2017
ms.author: adamab
ms.openlocfilehash: 6c0d76207233a04bdec604d95f1779c62f6e2d8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="programmatically-create-azure-dashboards"></a>Creare dashboard di Azure a livello di codice

Questo documento descrive in modo dettagliato il processo di creazione e pubblicazione di dashboard di Azure a livello di codice. Il dashboard mostrato di seguito viene usato come riferimento in tutto il documento.

![Dashboard di esempio](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Panoramica

I dashboard condivisi in Azure sono [risorse](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) molto simili alle macchine virtuali e agli account di archiviazione.  Di conseguenza, possono essere gestiti a livello di codice tramite le [API REST di Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-rest-api), l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/en-us/cli/azure/overview) e i [comandi di Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/get-started-azureps?view=azurermps-4.2.0). Inoltre, molte funzionalità del [portale di Azure](https://portal.azure.com) sono basate su queste API per semplificare la gestione delle risorse.  

Ognuno degli strumenti e delle API permette di creare, elencare, recuperare, modificare ed eliminare risorse in modi diversi.  Poiché i dashboard sono risorse, è possibile selezionare l'API o lo strumento preferito da usare.

Indipendentemente dallo strumento usato, è necessario creare una rappresentazione JSON dell'oggetto dashboard prima di poter chiamare qualsiasi API di creazione delle risorse. Questo oggetto contiene informazioni sulle parti del dashboard, chiamate anche riquadri. Include anche dimensioni, posizioni, risorse cui sono associati questi elementi e tutte le personalizzazioni dell'utente.

Il modo più pratico per creare il documento JSON consiste nell'usare il [portale](https://portal.azure.com/) per aggiungere e posizionare i riquadri in modo interattivo. È quindi necessario esportare il documento JSON. Infine, creare un modello dal risultato, da usare successivamente in script, programmi e strumenti di distribuzione.

## <a name="create-a-dashboard"></a>Creare un dashboard

Per creare un nuovo dashboard, usare il comando Nuovo dashboard nella schermata principale del portale.

![Comando Nuovo dashboard](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

È quindi possibile usare la raccolta di riquadri per trovare e aggiungere riquadri. Per aggiungere i riquadri, trascinarli e rilasciarli. Alcuni riquadri supportano il ridimensionamento tramite un quadratino di trascinamento, mentre altri hanno dimensioni fisse, visualizzabili nel menu di scelta rapida corrispondente.

### <a name="drag-handle"></a>Quadratino di trascinamento
![Quadratino di trascinamento](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

### <a name="fixed-sizes-via-context-menu"></a>Dimensioni fisse nel menu di scelta rapida
![Menu di scelta rapida delle dimensioni](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Condividere il dashboard

Dopo aver configurato il dashboard in base alle preferenze, il passaggio successivo consiste nel pubblicarlo (con il comando Condividi) e quindi usare Esplora risorse per recuperare la rappresentazione JSON.

![Comando Condividi](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Facendo clic sul comando Condividi, viene visualizzata una finestra di dialogo che chiede in quale sottoscrizione e gruppo di risorse eseguire la pubblicazione. Tenere presente che [è necessario avere accesso in scrittura](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-configure) alla sottoscrizione e al gruppo di risorse scelti.

![Condivisione e accesso](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Recuperare la rappresentazione JSON del dashboard

La pubblicazione richiede solo pochi secondi.  Al termine, il passaggio successivo consiste nell'accedere a [Esplora risorse](https://portal.azure.com/#blade/HubsExtension/ArmExplorerBlade) per recuperare la rappresentazione JSON.

![Spostamento in Esplora risorse](./media/azure-portal-dashboards-create-programmatically/browse-resource-explorer.png)

Da Esplora risorse passare alla sottoscrizione e al gruppo di risorse scelti. Quindi, fare clic sulla risorsa dashboard appena pubblicata per visualizzare la rappresentazione JSON.

![JSON in Esplora risorse](./media/azure-portal-dashboards-create-programmatically/resource-explorer-json.png)

## <a name="create-a-template-from-the-json"></a>Creare un modello dalla rappresentazione JSON

Il passaggio successivo consiste nel creare un modello dalla rappresentazione JSON per poterlo riutilizzare a livello di codice con le API di gestione delle risorse e gli strumenti della riga di comando appropriati oppure nel portale.

Per creare un modello, non è necessaria una comprensione approfondita della struttura JSON del dashboard. Nella maggior parte dei casi, è bene preservare la struttura e la configurazione di ogni riquadro e quindi impostare i parametri per il set di risorse di Azure a cui puntano. Esaminare il dashboard JSON esportato e individuare tutte le occorrenze degli ID risorsa di Azure. Il dashboard di esempio contiene più riquadri che puntano tutti a una singola macchina virtuale di Azure. Questo è perché il dashboard considera una sola risorsa. Se nel codice JSON di esempio (incluso alla fine del documento) si cerca "/subscriptions", si troveranno diverse occorrenze di questo ID.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Per pubblicare il dashboard per qualsiasi macchina virtuale in futuro, è necessario impostare i parametri per ogni occorrenza di questa stringa all'interno del codice JSON. 

Esistono due tipi di API che creano risorse in Azure. [API imperative](https://docs.microsoft.com/en-us/rest/api/resources/resources), che creano una risorsa per volta, e un sistema di [distribuzione basato su modello](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-deploy), che può orchestrare la creazione di più risorse dipendenti con un'unica chiamata API. Poiché il secondo tipo supporta in modo nativo la parametrizzazione e la creazione di modelli, è quello usato per questo esempio.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Creare un dashboard a livello di codice dal modello usando una distribuzione di modelli

Azure offre la possibilità di orchestrare la distribuzione di più risorse. È necessario creare un modello di distribuzione che indica il set di risorse da distribuire, nonché le rispettive relazioni.  Il formato JSON di ogni risorsa è lo stesso di quando le risorse vengono create una per una. La differenza è che il [linguaggio del modello](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authoring-templates) aggiunge alcuni concetti come variabili, parametri, funzioni di base e altro ancora. Questa sintassi estesa è supportata solo nel contesto di una distribuzione di modelli e non funziona se usata con le API imperative descritte in precedenza.

Se si intende seguire questo approccio, la parametrizzazione deve essere eseguita usando la sintassi dei parametri del modello.  Sostituire tutte le istanze dell'ID risorsa trovate prima, come mostrato qui.

### <a name="example-json-property-with-hard-coded-resource-id"></a>Proprietà JSON di esempio con ID risorsa hardcoded
`id: “/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1”`

### <a name="example-json-property-converted-to-a-parameterized-version-based-on-template-parameters"></a>Proprietà JSON di esempio convertita in versione parametrizzata basata sui parametri del modello

`id: "[resourceId(parameters('virtualMachineResourceGroup'), ‘Microsoft.Compute/virtualMachines’, parameters('virtualMachineName'))]"`

È anche necessario dichiarare alcuni metadati del modello obbligatori e i parametri nella parte superiore del modello JSON, in questo modo:

```json

{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

__Il modello completo e funzionante viene mostrato alla fine di questo documento.__

Dopo aver creato il modello, è possibile distribuirlo usando le [API REST](https://docs.microsoft.com/en-us/rest/api/resources/deployments), [PowerShell](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-deploy), l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/en-us/cli/azure/group/deployment#az_group_deployment_create) oppure la [pagina per la distribuzione di modelli del portale](https://portal.azure.com/#create/Microsoft.Template).

Ecco due versioni della rappresentazione JSON del dashboard di esempio. La prima è la versione esportata dal portale e già associata a una risorsa. La seconda è la versione modello che può essere associata a livello di codice a qualsiasi macchina virtuale e distribuita con Azure Resource Manager.

## <a name="json-representation-of-our-example-dashboard-before-templating"></a>Rappresentazione JSON del dashboard di esempio (prima della creazione del modello)

Ecco che cosa verrà probabilmente visualizzato se si seguono le istruzioni precedenti per recuperare la rappresentazione JSON di un dashboard che è già stato distribuito. Notare gli identificatori di risorsa hardcoded, che mostrano che il dashboard punta a una macchina virtuale di Azure specifica.

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

La versione modello del dashboard ha definito tre parametri chiamati __virtualMachineName__, __virtualMachineResourceGroup__ e __dashboardName__.  I parametri permettono di fare in modo che il dashboard punti a una macchina virtuale di Azure diversa a ogni distribuzione. Gli ID con parametri sono evidenziati per indicare che questo dashboard può essere configurato e distribuito a livello di codice in modo da puntare a qualsiasi macchina virtuale di Azure. Il modo più semplice per testare questa funzionalità consiste nel copiare il modello seguente e incollarlo nella [pagina di distribuzione dei modelli del portale di Azure](https://portal.azure.com/#create/Microsoft.Template). 

Questo esempio distribuisce solo un dashboard, ma il linguaggio del modello permette di distribuire più risorse e di generare un bundle di uno o più dashboard insieme. 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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