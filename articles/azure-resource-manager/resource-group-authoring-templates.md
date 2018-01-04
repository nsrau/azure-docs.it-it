---
title: Struttura e sintassi del modello di Azure Resource Manger | Documentazione Microsoft
description: "Descrive la struttura e le proprietà dei modelli di Azure Resource Manager con la sintassi dichiarativa JSON."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2017
ms.author: tomfitz
ms.openlocfilehash: b0bc5abd768be0fa5876aaef108cd71a15d94510
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2017
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Comprendere la struttura e la sintassi dei modelli di Azure Resource Manger
Questo articolo descrive la struttura di un modello di Azure Resource Manager. Presenta le diverse sezioni di un modello e le proprietà disponibili in queste sezioni. Il modello è composto da JSON ed espressioni che è possibile usare per creare valori per la distribuzione. Per un'esercitazione dettagliata sulla creazione di un modello, vedere [Creare il primo modello di Azure Resource Manager](resource-manager-create-first-template.md).

## <a name="template-format"></a>Formato del modello
La struttura più semplice di un modello è costituita dagli elementi seguenti:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  },
    "variables": {  },
    "resources": [  ],
    "outputs": {  }
}
```

| Nome dell'elemento | Obbligatoria | DESCRIZIONE |
|:--- |:--- |:--- |
| $schema |Sì |Percorso del file di schema JSON che descrive la versione del linguaggio del modello. Usare l'URL riportato nell'esempio precedente. |
| contentVersion |Sì |Versione del modello (ad esempio 1.0.0.0). Questo elemento accetta tutti i valori. Quando si distribuiscono risorse tramite il modello, è possibile usare questo valore per assicurarsi che venga usato il modello corretto. |
| Parametri |No  |Valori forniti durante la distribuzione per personalizzare la distribuzione di risorse. |
| variables |No  |Valori usati come frammenti JSON nel modello per semplificare le espressioni di linguaggio del modello. |
| resources |Sì |Tipi di risorse che vengono distribuite o aggiornate in un gruppo di risorse. |
| outputs |No  |Valori restituiti dopo la distribuzione. |

Ogni elemento contiene proprietà che è possibile impostare. L'esempio seguente contiene la sintassi completa per un modello:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  
        "<parameter-name>" : {
            "type" : "<type-of-parameter-value>",
            "defaultValue": "<default-value-of-parameter>",
            "allowedValues": [ "<array-of-allowed-values>" ],
            "minValue": <minimum-value-for-int>,
            "maxValue": <maximum-value-for-int>,
            "minLength": <minimum-length-for-string-or-array>,
            "maxLength": <maximum-length-for-string-or-array-parameters>,
            "metadata": {
                "description": "<description-of-the parameter>" 
            }
        }
    },
    "variables": {
        "<variable-name>": "<variable-value>",
        "<variable-object-name>": {
            <variable-complex-type-value>
        },
        "<variable-object-name>": {
            "copy": [
                {
                    "name": "<name-of-array-property>",
                    "count": <number-of-iterations>,
                    "input": {
                        <properties-to-repeat>
                    }
                }
            ]
        },
        "copy": [
            {
                "name": "<variable-array-name>",
                "count": <number-of-iterations>,
                "input": {
                    <properties-to-repeat>
                }
            }
        ]
    },
    "resources": [
      {
          "condition": "<boolean-value-whether-to-deploy>",
          "apiVersion": "<api-version-of-resource>",
          "type": "<resource-provider-namespace/resource-type-name>",
          "name": "<name-of-the-resource>",
          "location": "<location-of-resource>",
          "tags": {
              "<tag-name1>": "<tag-value1>",
              "<tag-name2>": "<tag-value2>"
          },
          "comments": "<your-reference-notes>",
          "copy": {
              "name": "<name-of-copy-loop>",
              "count": "<number-of-iterations>",
              "mode": "<serial-or-parallel>",
              "batchSize": "<number-to-deploy-serially>"
          },
          "dependsOn": [
              "<array-of-related-resource-names>"
          ],
          "properties": {
              "<settings-for-the-resource>",
              "copy": [
                  {
                      "name": ,
                      "count": ,
                      "input": {}
                  }
              ]
          },
          "resources": [
              "<array-of-child-resources>"
          ]
      }
    ],
    "outputs": {
        "<outputName>" : {
            "type" : "<type-of-output-value>",
            "value": "<output-value-expression>"
        }
    }
}
```

In questo articolo le sezioni del modello vengono esaminate in modo dettagliato.

## <a name="syntax"></a>Sintassi
La sintassi di base del modello è JSON. Le espressioni e le funzioni estendono ad ogni modo i valori JSON disponibili all'interno del modello.  Le espressioni vengono scritte all'interno di valori letterali stringa JSON il cui primo e ultimo carattere sono le parentesi quadre: rispettivamente`[` e `]`. Il valore dell'espressione viene valutato quando viene distribuito il modello. Sebbene sia scritto come valore letterale stringa, il risultato della valutazione dell'espressione può essere di un tipo JSON diverso, ad esempio una matrice o un numero intero, a seconda dell'espressione effettiva.  Per avere una stringa letterale che inizi con una parentesi quadra `[`, ma che non venga interpretata come espressione, è necessario aggiungere un'altra parentesi in modo che la stringa inizi con `[[`.

Solitamente, si usano espressioni con funzioni per eseguire operazioni per la configurazione della distribuzione. Proprio come in JavaScript, le chiamate di funzione sono formattate come `functionName(arg1,arg2,arg3)`. Per i riferimenti alle proprietà si usano il punto e gli operatori [index].

Nell'esempio seguente viene illustrato come utilizzare diverse funzioni durante la costruzione di un valore:

```json
"variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
}
```

Per l’elenco completo delle funzioni del modello, vedere [Funzioni del modello di Gestione risorse di Azure](resource-group-template-functions.md). 

## <a name="parameters"></a>Parametri
Nella sezione parameters del modello si possono specificare i valori che è possibile immettere durante la distribuzione delle risorse. I valori dei parametri consentono di personalizzare la distribuzione fornendo valori specifici per un determinato ambiente, ad esempio sviluppo, test e produzione. Non è obbligatorio specificare i parametri nel modello, ma senza di essi il modello distribuisce sempre le stesse risorse con lo stesso nome, località e proprietà.

Nell'esempio seguente viene illustrata una definizione di parametro semplici:

```json
"parameters": {
  "siteNamePrefix": {
    "type": "string",
    "metadata": {
      "description": "The name prefix of the web app that you wish to create."
    }
  },
},
```

Per informazioni sulla definizione dei parametri, vedere [sezione parametri di modelli di Azure Resource Manager](resource-manager-templates-parameters.md).

## <a name="variables"></a>variables
Nella sezione variables è possibile costruire valori da usare in tutto il modello. Non è obbligatorio definire le variabili, che però permettono spesso di semplificare il modello riducendo le espressioni complesse.

Nell'esempio seguente viene illustrata una definizione di variabile semplice:

```json
"variables": {
  "webSiteName": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
},
```

Per informazioni sulla definizione delle variabili, vedere [sezione variabili dei modelli di Azure Resource Manager](resource-manager-templates-variables.md).

## <a name="resources"></a>Risorse
Nella sezione risorse è possibile definire le risorse da distribuire o aggiornare. Questa sezione può risultare complicata perché per specificare i valori corretti è necessario conoscere i tipi da distribuire.

```json
"resources": [
  {
    "apiVersion": "2016-08-01",
    "name": "[variables('webSiteName')]",
    "type": "Microsoft.Web/sites",
    "location": "[resourceGroup().location]",
    "properties": {
      "serverFarmId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Web/serverFarms/<plan-name>"
    }
  }
],
```

Per ulteriori informazioni, vedere [sezione delle risorse dei modelli di Azure Resource Manager](resource-manager-templates-resources.md).

## <a name="outputs"></a>Output
Nella sezione dell'output è possibile specificare i valori restituiti dalla distribuzione. Ad esempio, è possibile restituire l'URI per accedere a una risorsa distribuita.

```json
"outputs": {
  "newHostName": {
    "type": "string",
    "value": "[reference(variables('webSiteName')).defaultHostName]"
  }
}
```

Per ulteriori informazioni, vedere [genera una sezione dei modelli di Azure Resource Manager](resource-manager-templates-outputs.md).

## <a name="template-limits"></a>Limiti del modello

Limitare le dimensioni del modello a 1 MB e ogni file di parametri a 64 KB. Il limite di 1 MB si applica allo stato finale del modello dopo che è stato espanso con le definizioni delle risorse iterative e i valori di variabili e parametri. 

Si è anche limitati a:

* 256 parametri
* 256 variabili
* 800 risorse (incluso il conteggio copie)
* 64 valori di output
* 24.576 caratteri in un'espressione di modello

È possibile superare alcuni limiti del modello usando un modello annidato. Per altre informazioni, vedere [Uso di modelli collegati nella distribuzione di risorse di Azure](resource-group-linked-templates.md). Per ridurre il numero di parametri, variabili o output, è possibile combinare più valori in un oggetto. Per altre informazioni, vedere [Oggetti come parametri](resource-manager-objects-as-parameters.md).

## <a name="next-steps"></a>Passaggi successivi
* Per visualizzare modelli completi per molti tipi diversi di soluzioni, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/).
* Per informazioni dettagliate sulle funzioni che è possibile usare in un modello, vedere [Funzioni del modello di Azure Resource Manager](resource-group-template-functions.md).
* Per unire più modelli durante la distribuzione, vedere [Uso di modelli collegati con Azure Resource Manager](resource-group-linked-templates.md).
* Potrebbe essere necessario usare le risorse esistenti all'interno di un gruppo di risorse diverso. Questo scenario è comune quando si usano account di archiviazione o reti virtuali condivisi tra più gruppi di risorse. Per altre informazioni, vedere la [funzione resourceId](resource-group-template-functions-resource.md#resourceid).
