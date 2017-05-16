---
title: Modelli di Azure Resource Manager - Oggetti come parametri| Documentazione Microsoft
description: "Viene descritto come estendere la funzionalità dei modelli di Azure Resource Manager per usare oggetti come parametri"
services: azure-resource-manager, guidance
documentationcenter: na
author: petertay
manager: christb
editor: 
ms.service: guidance
ms.topic: article
ms.date: 05/01/2017
ms.author: mspnp
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 0ab00cc3455d4bff7bfe1dfb62bafa550d65dea8
ms.contentlocale: it-it
ms.lasthandoff: 05/03/2017


---

# <a name="patterns-for-extending-the-functionality-of-azure-resource-manager-templates---objects-as-parameters"></a>Schemi per estendere le funzionalità dei modelli di Azure Resource Manager - oggetti come parametri

I modelli di Azure Resource Manager supportano dei parametri per specificare i valori per personalizzare una distribuzione di risorse. Benché questa funzionalità sia utile e consenta di creare distribuzioni complesse, un singolo modello è limitato a 255 parametri. Se si usa un parametro per ogni proprietà in una risorsa e si dispone di una distribuzione di grandi dimensioni, si potrebbero esaurire i parametri.

## <a name="create-object-as-parameter"></a>Creare un oggetto come parametro

Un modo per risolvere questo problema consiste nell'usare un oggetto come parametro. Il modello consiste nello specificare un parametro come oggetto nel modello, quindi fornire l'oggetto come un valore o una matrice di valori. Si fa riferimento alle sottoproprietà usando la funzione `parameter()` e l'operatore punto. ad esempio:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "VNetSettings":{"type":"object"}
  },
  "variables": {},
  "resources": [
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[parameters('VNetSettings').name]",
      "location":"[resourceGroup().location]",
      "properties": {
          "addressSpace":{
              "addressPrefixes": [
                  "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
              ]
          },
          "subnets":[
              {
                  "name":"[parameters('VNetSettings').subnets[0].name]",
                  "properties": {
                      "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
                  }
              },
              {
                  "name":"[parameters('VNetSettings').subnets[1].name]",
                  "properties": {
                      "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
                  }
              }
          ]
        }
    }
  ],          
  "outputs": {}
}

```

Il file di parametro corrispondente è simile a:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{ 
      "VNetSettings":{
          "value":{
              "name":"VNet1",
              "addressPrefixes": [
                  { 
                      "name": "firstPrefix",
                      "addressPrefix": "10.0.0.0/22"
                  }
              ],
              "subnets":[
                  {
                      "name": "firstSubnet",
                      "addressPrefix": "10.0.0.0/24"
                  },
                  {
                      "name":"secondSubnet",
                      "addressPrefix":"10.0.1.0/24"
                  }
              ]
          }
      }
  }
}
```

In questo esempio tutti i valori specificati per la rete virtuale provengono da un singolo parametro, `VNetSettings`. Questo modello è utile per la gestione dei valori della proprietà perché è possibile mantenere tutti i valori per una determinata risorsa in un singolo oggetto. Mentre in questo esempio viene usato un oggetto come parametro, è possibile anche usare una matrice di oggetti come parametro. Si fa riferimento agli oggetti usando un indice nella matrice.

## <a name="use-object-instead-of-multiple-arrays"></a>Usare l'oggetto anziché più array

È stato usato un modello simile per creare più istanze di una risorsa creando più array di valori di proprietà e scorrendo ogni matrice per selezionare il valore. Questo modello funziona bene quando si creano più risorse dello stesso tipo, ma può essere problematico quando usato per creare delle risorse figlio. 

Esistono due ragioni per questo problema. In primo luogo, Resource Manager tenta di distribuire le risorse figlio in parallelo, ma la distribuzione presenta un errore quando due risorse aggiornano contemporaneamente l'elemento padre. 

In secondo luogo, tutte le matrici di valori di proprietà vengono iterate in parallelo e se tutte le matrici non hanno la stessa forma si verifica un errore. Ad esempio, si considerino le seguenti matrici di proprietà:

```json
"variables": {
    "firstProperty": [
        {
            "name": "A",
            "type": "typeA"
        },
        {
            "name": "B",
            "type": "typeB"
        },
        {
            "name": "C",
            "type": "typeC"
        }
    ],
    "secondProperty": [
        "one","two"
    ]
}
```

Il modello tipico per assegnare questi valori alle proprietà all'interno di un ciclo copia consiste nell'accedere alla proprietà usando la funzione `variables()` usando `copyIndex()` come indice nella matrice. ad esempio:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    ...
    "copy": {
        "name": "copyLoop1",
        "count": "[length(variables('firstProperty'))]"
    },
    ...
    "properties": {
        "name": { "value": "[variables('firstProperty')[copyIndex()].name]" },
        "type": { "value": "[variables('firstProperty')[copyIndex()].type]" },
        "number": { "value": "[variables('secondProperty')[copyIndex()]]" }
    }
}
```
Si noti che `count` del ciclo di copia si basa sul numero di proprietà nella matrice `firstProperty`. Tuttavia, non c'è lo stesso numero di proprietà nella matrice `secondProperty`. La convalida non riesce per questo modello perché la matrice `secondProperty` non ha la stessa lunghezza.

Tuttavia, se si includono tutte le proprietà di un singolo oggetto, è molto più semplice visualizzare quando non è presente un valore. ad esempio:

```json
"variables": {
    "propertyObject": [
        {
            "name": "A",
            "type": "typeA",
            "number": "one"
        },
        {
            "name": "B",
            "type": "typeB",
            "number": "two"
        },
        {
            "name": "C",
            "type": "typeC"
        }
    ]
}
```

## <a name="use-with-sequential-copy"></a>Usare con una copia sequenziale

Questo modello risulta ancora più utile in combinazione con il [modello di copia sequenziale](resource-manager-sequential-loop.md), in particolare per la distribuzione di risorse figlio. Il seguente modello di esempio distribuisce un gruppo di sicurezza di rete (NSG) con due regole di sicurezza. La prima risorsa denominata `NSG1` distribuisce il gruppo di sicurezza di rete. Il secondo gruppo di risorse denominato `loop-0` esegue due funzioni: prima di tutto, è `dependsOn` il NSG e pertanto la sua distribuzione non viene avviata fino a che `NSG1` non viene completato, ed è la prima iterazione del ciclo di sequenza. La terza risorsa è un modello annidato che distribuisce le regole di sicurezza usando un oggetto per i valori del parametro come illustrato nell'ultimo esempio.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "networkSecurityGroupsSettings": {"type":"object"}
  },
  "variables": {},
  "resources": [
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "NSG1",
      "location":"[resourceGroup().location]",
      "properties": {
          "securityRules":[]
      }
    },
    {
        "apiVersion": "2015-01-01",
        "type": "Microsoft.Resources/deployments",
        "name": "loop-0",
        "dependsOn": [
            "NSG1"
        ],
        "properties": {
            "mode":"Incremental",
            "parameters":{},
            "template": {
                "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {},
                "variables": {},
                "resources": [],
                "outputs": {}
            }
        }       
    },
    {
        "apiVersion": "2015-01-01",
        "type": "Microsoft.Resources/deployments",
        "name": "[concat('loop-', copyIndex(1))]",
        "dependsOn": [
          "[concat('loop-', copyIndex())]"
        ],
        "copy": {
          "name": "iterator",
          "count": "[length(parameters('networkSecurityGroupsSettings').securityRules)]"
        },
        "properties": {
          "mode": "Incremental",
          "template": {
            "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
           "parameters": {},
            "variables": {},
            "resources": [
                {
                    "name": "[concat('NSG1/' , parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].name)]",
                    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
                    "apiVersion": "2016-09-01",
                    "location":"[resourceGroup().location]",
                    "properties":{
                        "description": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].description]",
                        "priority":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].priority]",
                        "protocol":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].protocol]",
                        "sourcePortRange": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].sourcePortRange]",
                        "destinationPortRange": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].destinationPortRange]",
                        "sourceAddressPrefix": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].sourceAddressPrefix]",
                        "destinationAddressPrefix": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].destinationAddressPrefix]",
                        "access":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].access]",
                        "direction":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].direction]"
                        }
                  }
            ],
            "outputs": {}
          }
        }
    }
  ],          
  "outputs": {}
}

```

Il file di parametro corrispondente è simile a:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{ 
      "networkSecurityGroupsSettings": {
      "value": {
          "securityRules": [
            {
              "name": "RDPAllow",
              "description": "allow RDP connections",
              "direction": "Inbound",
              "priority": 100,
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "10.0.0.0/24",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "access": "Allow",
              "protocol": "Tcp"
            },
            {
              "name": "HTTPAllow",
              "description": "allow HTTP connections",
              "direction": "Inbound",
              "priority": 200,
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "10.0.1.0/24",
              "sourcePortRange": "*",
              "destinationPortRange": "80",
              "access": "Allow",
              "protocol": "Tcp"
            }
          ]
        }
      }
    }
  }
```

## <a name="try-the-template"></a>Provare il modello

Se si vuole sperimentare con questi modelli, seguire questa procedura:

1.    Accedere al portale di Azure, selezionare l'icona "+" e cercare il tipo di risorsa "distribuzione del modello". Dopo averla individuata nei risultati della ricerca, selezionarla.
2.    Quando si arriva alla pagina "distribuzione del modello", selezionare il pulsante **crea**. Questo pulsante apre il pannello "distribuzione personalizzata".
3.    Selezionare il pulsante **Modifica modello**.
4.    Eliminare il modello vuoto nel riquadro destro.
5.    Copiare e incollare il modello di esempio nel riquadro destro.
6.    Fare clic sul pulsante **Salva**.
7.    Quando si torna al riquadro "distribuzione personalizzata", selezionare il pulsante **Modifica parametri**.
8.  Nel pannello "modifica parametri" eliminare il modello esistente.
9.  Copiare e incollare il modello di parametro dall'esempio precedente.
10. Selezionare il pulsante **Salva**, che rimanda al pannello "distribuzione personalizzata".
11. Nel pannello "distribuzione personalizzata" selezionare la propria sottoscrizione, creare un nuovo gruppo di risorse o usarne uno esistente e selezionare una posizione. Esaminare i termini e condizioni e selezionare la casella di controllo "Accetto".
12.    Fare clic sul pulsante **Acquista**.

## <a name="next-steps"></a>Passaggi successivi

Se sono necessari più del numero massimo di 255 parametri consentiti per la distribuzione, usare questo modello per specificare un minor numero di parametri nel modello in uso. È anche possibile usare questo modello per gestire più facilmente le proprietà per le risorse, quindi distribuirle senza conflitti usando il modello di ciclo sequenziale.

* Per informazioni introduttive sulla funzione `parameter()` e sull'uso delle matrici, vedere [Funzioni del modello di Azure Resource Manager](resource-group-template-functions.md).
* Questo schema è implementato anche nel [progetto dei blocchi predefiniti del modello](https://github.com/mspnp/template-building-blocks) e nelle [architetture di riferimento di Azure](/azure/architecture/reference-architectures/).
