---
title: Un ciclo sequenziale nei modelli di Azure | Documentazione Microsoft
description: "Descrive come estendere la funzionalità dei modelli di Azure Resource Manager per implementare cicli sequenziali durante la distribuzione di più istanze di un tipo di risorsa."
services: guidance, azure-resource-manager
documentationcenter: na
author: petertaylor9999
manager: christb
editor: 
ms.service: guidance
ms.topic: article
ms.date: 04/18/2017
ms.author: petertay
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 44d59488600e05d6ae80a169b5d682e8fa94ccc5
ms.lasthandoff: 04/18/2017


---

# <a name="patterns-for-extending-the-functionality-of-azure-resource-manager-templates---sequential-looping"></a>Schemi per estendere le funzionalità dei modelli di Azure Resource Manager - Ciclo sequenziale

I modelli di Azure Resource Manager supportano la distribuzione di un gruppo di risorse simili tramite un ciclo di copia. Un ciclo di copia in un oggetto risorsa può essere usato per eseguire un'iterazione su una matrice di stringhe che vengono quindi utilizzate per generare nomi univoci per le risorse. Un ciclo di copia dell'oggetto risorsa può essere usato anche per eseguire un'iterazione su una matrice di variabili che descrivono la risorsa.

Questi schemi funzionano tutti bene, ma solo quando non esistono dipendenze tra i membri del gruppo. Durante un ciclo di iterazione, Resource Manager tenta di distribuire risorse in parallelo. Se una prima risorsa dipende da una seconda, la distribuzione potrebbe non riuscire se Resource Manager distribuisce la seconda risorsa prima della prima risorsa.

Il vero problema è che Resource Manager non supporta attualmente `dependsOn` all'interno di un ciclo di iterazione. Questa funzionalità tuttavia può essere implementata mediante funzionalità esistenti di Resource Manager e un po' di creatività nella denominazione delle risorse. 

## <a name="sequential-looping-pattern"></a>Schema di ciclo sequenziale

Lo schema è il seguente: una prima risorsa viene denominata usando una concatenazione del prefisso del nome e di `0` o del primo indice del ciclo. Una seconda risorsa include un ciclo di copia e nel ciclo di copia il nome di risorsa successivo è una concatenazione del prefisso del nome e del risultato della funzione `copyIndex(1)`, che aggiunge 1 al `copyIndex()` corrente. La seconda risorsa include anche un elemento `dependsOn` che fa riferimento alla concatenazione del prefisso del nome e del risultato della funzione `copyIndex()`. Questo approccio consente di creare una relazione `dependsOn` dalla risorsa successiva alla risorsa precedente. Resource Manager aspetta a distribuire la risorsa successiva fino a quando non è stata distribuita la risorsa precedente.

Il modello seguente illustra questo schema. Il tipo di risorsa Microsoft.Resources/deployments è solo un modello annidato che in realtà non distribuisce alcun elemento.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberToDeploy": {
      "type": "int",
      "minValue": 2,
      "defaultValue": 5
    }
  },
  "variables": {
    "count": "[sub(parameters('numberToDeploy'), 1)]"
  },
  "resources": [
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "loop-0",
      "properties": {
        "mode": "Incremental",
        "parameters": {},
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [],
          "outputs": {
            "collection": {
              "type": "string",
              "value": "loop-0 "
            }
          }
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
        "count": "[variables('count')]"
      },
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [],
          "outputs": {
            "collection": {
              "type": "string",
              "value": "[concat(reference(concat('loop-',copyIndex())).outputs.collection.value,'loop-',copyIndex(1), ' ')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "result": {
      "type": "string",
      "value": "[reference(concat('loop-',variables('count'))).outputs.collection.value]"
    }
  }
}

```
In questo modello il primo oggetto risorsa è denominato `loop-0`. Quindi, nel secondo oggetto risorsa, il nome di risorsa successivo è una concatenazione della parola `loop-` e del risultato della funzione `copyIndex(1)`: `loop-1`. L'elemento `dependsOn` fa riferimento alla risorsa precedente perché è una concatenazione della parola `loop-` e del risultato della funzione `copyIndex()`: `loop-0`. Lo schema nel secondo oggetto risorsa si ripete fino a raggiungere `count` e termina con una risorsa denominata `loop-4` che `dependsOn` `loop-3`. Si noti che `count` è una variabile che sottrae `1` dal parametro `numberToDeploy` per mantenere il corretto conteggio in base zero.

## <a name="try-the-template"></a>Provare il modello

Se si vuole sperimentare con questo modello, seguire questi passaggi:

1.    Accedere al portale di Azure, selezionare l'icona "+" e cercare il tipo di risorsa "distribuzione del modello". Dopo averla individuata nei risultati della ricerca, selezionarla.
2.    Quando si arriva alla pagina "distribuzione del modello", selezionare il pulsante "Crea". Si passa al pannello "distribuzione personalizzata" dove si nota che il modello non ha risorse.
3.    Selezionare l'icona "Modifica".
4.    Eliminare il modello vuoto nel riquadro destro.
5.    Copiare e incollare il modello di esempio precedente nel riquadro destro.
6.    Fare clic sul pulsante Salva.
7.    Si torna al riquadro "distribuzione personalizzata", ma questa volta sono presenti alcune caselle di riepilogo a discesa. Selezionare la propria sottoscrizione, creare un nuovo gruppo di risorse o usarne uno esistente e selezionare un percorso. Esaminare i termini e condizioni e fare clic sul pulsante "Accetto".
8.    Fare clic sul pulsante "Acquista".

Per verificare che le risorse vengano distribuite in sequenza, selezionare "gruppi di risorse", quindi selezionare il gruppo di risorse selezionato in precedenza. Selezionare il pulsante "distribuzioni" nel pannello del gruppo di risorse per vedere le risorse distribuite in ordine sequenziale con un timestamp corrispondente.

![Distribuzione del modello di ciclo sequenziale in Azure Resource Manager](./media/resource-manager-sequential-loop/deployments.png)

## <a name="next-steps"></a>Passaggi successivi

Usare questo schema nei modelli aggiungendo le risorse al modello nidificato. È possibile crearle sia direttamente nell'elemento del modello della risorsa `Microsoft.Resources/deployments` sia collegarle tramite l'elemento `templateLink`. Il tipo di risorsa nell'esempio è un modello nidificato, ma è possibile distribuire qualsiasi tipo di risorsa. L'unica eccezione è che non è possibile fare riferimento alle risorse figlio dall'interno di un ciclo di iterazione.

* Per un'introduzione alla creazione di più istanze di una risorsa, vedere [Distribuire più istanze delle risorse nei modelli di Azure Resource Manager](resource-group-create-multiple.md).
* Questo schema è implementato anche nel [progetto dei blocchi predefiniti del modello](https://github.com/mspnp/template-building-blocks) e nelle [architetture di riferimento di Azure](/azure/architecture/reference-architectures/).
