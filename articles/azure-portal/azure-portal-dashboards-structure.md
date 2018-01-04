---
title: Struttura dei dashboard di Azure | Microsoft Docs
description: Questo articolo descrive la struttura JSON di un dashboard di Azure
services: azure-portal
documentationcenter: 
author: adamab
manager: timlt
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/01/2017
ms.author: adamab
ms.openlocfilehash: f71ff9383f20a1a75fd2c1cf4dc3aaf049d970cf
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="the-structure-of-azure-dashboards"></a>Struttura dei dashboard di Azure
Questo documento descrive dettagliatamente la struttura di un dashboard di Azure usando il dashboard seguente come esempio:

![Dashboard di esempio](./media/azure-portal-dashboards-structure/sample-dashboard.png)

Poiché i [dashboard di Azure condivisi sono risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), questo dashboard può essere rappresentato come JSON.  Il codice JSON seguente rappresenta il dashboard visualizzato sopra.

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
        "metadata": {
            "model": {
                "timeRange": {
                    "value": {
                        "relative": {
                            "duration": 24,
                            "timeUnit": 1
                        }
                    },
                    "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
                }
            }
        }
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

## <a name="common-resource-properties"></a>Proprietà delle risorse comuni

Scomponendo le sezioni rilevanti del codice JSON,  le proprietà di primo livello __id__, __name__, __type__, __location__ e __tags__ vengono condivise tra tutti i tipi di risorsa di Azure. Di conseguenza, non riguardano molto il contenuto del dashboard.

### <a name="the-id-property"></a>Proprietà id

ID risorsa di Azure, soggetto alle [convenzioni di denominazione delle risorse di Azure](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Quando il portale crea un dashboard, in genere sceglie un ID sotto forma di GUID, ma è possibile scegliere qualsiasi nome valido quando si usa la creazione a livello di codice. 

### <a name="the-name-property"></a>Proprietà name
Il nome è il segmento dell'ID risorsa che non include le informazioni su sottoscrizione, tipo di risorsa o gruppo di risorse. Essenzialmente, si tratta dell'ultimo segmento dell'ID risorsa.

### <a name="the-type-property"></a>Proprietà type
Tutti i dashboard sono di tipo __Microsoft.Portal/dashboards__.

### <a name="the-location-property"></a>Proprietà location
Diversamente da altre risorse, i dashboard non devono essere un componente di runtime.  Per i dashboard, la posizione indica l'area geografica principale in cui è archiviata la rappresentazione JSON del dashboard. Il valore deve essere uno dei codici località che può essere recuperato con l'[API apposita nella risorsa delle sottoscrizioni](https://docs.microsoft.com/rest/api/resources/subscriptions).

### <a name="the-tags-property"></a>Proprietà tags
I tag sono una caratteristica comune delle risorse di Azure e permettono di organizzare la risorsa in base a coppie arbitrarie di nome-valore. Per i dashboard, è disponibile un tag speciale chiamato __hidden-title__. Se il dashboard contiene questa proprietà, la proprietà viene usata come nome visualizzato per il dashboard nel portale. Gli ID risorsa di Azure non possono essere rinominati, mentre i tag sì. Questo tag permette di includere un nome visualizzato modificabile per il dashboard.

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>Oggetto delle proprietà
L'oggetto delle proprietà contiene due proprietà, __lenses__ e __metadata__. La proprietà __lenses__ contiene informazioni sui riquadri del dashboard, chiamati anche parti.  La proprietà __metadata__ è disponibile per possibili funzionalità future.

### <a name="the-lenses-property"></a>Proprietà lenses
La proprietà __lenses__ contiene il dashboard. Notare che l'oggetto lenses in questo esempio contiene un'unica proprietà chiamata "0". Le sezioni sono un concetto di raggruppamento attualmente non implementato nei dashboard. Al momento, tutti i dashboard includono questa unica proprietà nell'oggetto delle sezioni, chiamata appunto "0".

### <a name="the-lens-object"></a>Oggetto lenses
L'oggetto sotto la proprietà "0" contiene due proprietà, __order__ e __parts__.  Nella versione corrente dei dashboard __order__ è sempre 0. La proprietà __parts__ contiene un oggetto che definisce le singole parti del dashboard, chiamate anche riquadri.

L'oggetto __parts__ contiene una proprietà per ogni parte, in cui il nome della proprietà è un numero. Questo numero non è significativo. 

### <a name="the-part-object"></a>Oggetto parts
Ogni singolo oggetto delle parti contiene una proprietà __position__ e una proprietà __metadata__.

### <a name="the-position-object"></a>Oggetto position
La proprietà __position__ contiene le informazioni su dimensioni e posizione per la parte espressa come __x__, __y__, __rowSpan__ e __colSpan__. I valori sono espressi in termini di unità griglia. Queste unità griglia sono visibili quando il dashboard è nella modalità di personalizzazione mostrata qui. Se un riquadro deve avere una larghezza di due unità griglia, un'altezza di un'unità griglia e una posizione nell'angolo in alto a sinistra del dashboard, l'oggetto position avrà questo aspetto:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![Unità griglia](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>Oggetto metadata
Ogni parte contiene una proprietà metadata, mentre un oggetto ha solo una proprietà obbligatoria chiamata __type__. Questa stringa indica al portale quale riquadro visualizzare. Il dashboard di esempio usa questi tipi di riquadri:


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart`: usato per visualizzare le metriche di monitoraggio
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart`: da visualizzare con testo o immagini con formattazione di base per elenchi, collegamenti e così via
1. `Extension[azure]/HubsExtension/PartType/VideoPart`: usato per visualizzare video da YouTube, Channel9 e qualsiasi altro tipo di video che supporta un tag video html.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart`: usato per visualizzare il nome e lo stato di una macchina virtuale di Azure.

Ogni tipo di parte ha una configurazione propria. Le proprietà di configurazione possibili si chiamano __inputs__, __settings__ e __asset__. 

### <a name="the-inputs-object"></a>Oggetto inputs
L'oggetto inputs contiene in genere informazioni che associano un riquadro a un'istanza della risorsa.  La parte della macchina virtuale nel dashboard di esempio contiene un singolo input che usa l'ID risorsa di Azure per esprimere l'associazione.  Il formato dell'ID risorsa è uniforme in tutte le risorse di Azure.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
La parte del grafico delle metriche include un singolo input che esprime la risorsa a cui eseguire l'associazione, nonché informazioni sulle metriche visualizzate. Ecco l'input per il riquadro che mostra le metriche Rete in ingresso e Rete in uscita.

```json
“inputs”:
[
    {
        "name": "queryInputs",
        "value": 
        {
            "timespan": 
            {
                "duration": "PT1H",
                "start": null,
                "end": null
           },
            "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
            "chartType": 0,
            "metrics": 
            [
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
]

```

### <a name="the-settings-object"></a>Oggetto settings
L'oggetto settings contiene gli elementi configurabili di una parte.  Nel dashboard di esempio la parte Markdown usa l'oggetto settings per archiviare il contenuto del markdown personalizzato e un titolo e un sottotitolo configurabili.

```json
"settings": 
{
    "content": 
    {
        "settings": 
        {
            "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
            "title": "Test VM Dashboard",
            "subtitle": "Contoso"
        }
    }
}

```

Analogamente, il riquadro del video ha impostazioni proprie che contengono un puntatore al video da riprodurre, un'impostazione di riproduzione automatica e informazioni sul titolo facoltative.

```json
"settings": 
{
   "content": 
    {
        "settings": 
        {
            "title": "",
            "subtitle": "",
            "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
            "autoplay": false
        }
    }
}

```

### <a name="the-asset-object"></a>Oggetto asset
Per i riquadri associati a oggetti del portale gestibili di prima classe, chiamati asset, la relazione è espressa tramite l'oggetto asset.  Nel dashboard di esempio il riquadro della macchina virtuale contiene la descrizione di questo asset.  La proprietà __idInputName__ indica al portale che l'ID immesso contiene l'identificatore univoco per l'asset, in questo caso l'ID risorsa. Il portale include asset definiti per la maggior parte dei tipi di risorsa di Azure.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`