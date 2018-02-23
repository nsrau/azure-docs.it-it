---
title: "Aggiornamenti dello schema del 1° giugno 2016 - App per la logica di Azure | Microsoft Docs"
description: Creare definizioni JSON per App per la logica di Azure con la versione dello schema 2016-06-01
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/25/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 525df7ddb8cd569bfd361da10d14ae08c1a721e0
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Aggiornamenti dello schema per App per la logica di Azure: 1° giugno 2016

Lo [schema aggiornato](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) e la versione dell'API per App per la logica di Azure includono importanti miglioramenti che rendono le app per la logica più affidabili e più facili da usare:

* Gli [ambiti](#scopes) consentono di raggruppare o annidare le azioni come una raccolta di azioni.
* [Condizioni e cicli](#conditions-loops) sono ora azioni di prima classe.
* La proprietà `runAfter`, che sostituisce `dependsOn`, consente di ordinare con maggiore precisione l'esecuzione delle azioni.

Per aggiornare le app per la logica dallo schema di anteprima del 1° agosto 2015 allo schema del 1° giugno 2016, [vedere la sezione relativa all'aggiornamento](#upgrade-your-schema).

<a name="scopes"></a>

## <a name="scopes"></a>Ambiti

Questo schema include gli ambiti, che consentono di raggruppare le azioni o annidarle all'interno di altre. Una condizione, ad esempio, può contenere un'altra condizione. Vedere altre informazioni sulla [sintassi degli ambiti](../logic-apps/logic-apps-loops-and-scopes.md) oppure esaminare questo esempio di ambito di base:

```
{
    "actions": {
        "My_Scope": {
            "type": "scope",
            "actions": {                
                "Http": {
                    "inputs": {
                        "method": "GET",
                        "uri": "http://www.bing.com"
                    },
                    "runAfter": {},
                    "type": "Http"
                }
            }
        }
    }
}
```

<a name="conditions-loops"></a>

## <a name="conditions-and-loops-changes"></a>Modifiche di condizioni e cicli

Nelle versioni precedenti dello schema, le condizioni e i cicli sono parametri associati a una singola azione. In questo schema è stata rimossa questa limitazione e le condizioni e i cicli sono disponibili come tipi di azione. Vedere altre informazioni su [cicli e ambiti](../logic-apps/logic-apps-loops-and-scopes.md) oppure esaminare questo esempio di base di un'azione condizione:

```
{
    "If_trigger_is_some-trigger": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'some-trigger')",
        "runAfter": { },
        "actions": {
            "Http_2": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.bing.com"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "else": 
        {
            "if_trigger_is_another-trigger": "..."
        }      
    }
}
```

<a name="run-after"></a>

## <a name="runafter-property"></a>Proprietà "runAfter"

La proprietà `runAfter` sostituisce `dependsOn`, offrendo maggiore precisione quando si specifica l'ordine di esecuzione delle azioni in base allo stato di azioni precedenti.

La proprietà `dependsOn` indica l'esecuzione riuscita dell'azione, indipendentemente dal numero di volte che si vuole eseguire un'azione a seconda che la precedente sia riuscita, abbia avuto esito negativo o sia stata ignorata. La proprietà `runAfter` offre tale flessibilità in quanto oggetto che specifica tutti nomi delle azioni dopo cui viene eseguito. Questa proprietà definisce anche una matrice degli stati accettabili come trigger. Per impostare l'esecuzione dopo l'esito positivo del passaggio A e dopo l'esito positivo o negativo del passaggio B, ad esempio, si costruisce questa proprietà `runAfter`:

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>Aggiornare lo schema

Per eseguire l'aggiornamento allo [schema più recente](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json), sono necessari solo pochi passaggi. Il processo di aggiornamento include l'esecuzione dello script di aggiornamento, il salvataggio come nuova app per la logica ed eventualmente, se necessario, la sovrascrittura dell'app per la logica precedente.

1. Nel portale di Azure aprire l'app per la logica.

2. Passare a **Panoramica**. Sulla barra degli strumenti dell'app per la logica scegliere **Aggiorna schema**.
   
    ![Scegliere Aggiorna schema][1]
   
    Verrà restituita la definizione aggiornata, che è possibile copiare e incollare in una definizione di risorsa, se necessario. 
    È tuttavia **altamente consigliabile** scegliere **Salva con nome** per assicurarsi che tutti i riferimenti alla connessione siano validi nell'app per la logica aggiornata.

3. Sulla barra degli strumenti del pannello per l'aggiornamento scegliere **Salva con nome**.

4. Immettere il nome logico e lo stato. Per distribuire l'app per la logica aggiornata, scegliere **Crea**.

5. Verificare che l'app per la logica aggiornata funzioni come previsto.
   
   > [!NOTE]
   > Se si usa un trigger manuale o di richiesta, l'URL di callback viene modificato nella nuova app per la logica. Testare il nuovo URL per verificare il funzionamento dell'esperienza end-to-end. Per mantenere gli URL precedenti, è possibile clonare l'app per la logica esistente.

6. *Facoltativo* Per sovrascrivere l'app per la logica precedente con la nuova versione dello schema, sulla barra degli strumenti scegliere **Clona** accanto ad **Aggiorna schema**. Questo passaggio è necessario solo se si vuole mantenere lo stesso ID risorsa o lo stesso URL del trigger di richiesta dell'app per la logica.

### <a name="upgrade-tool-notes"></a>Note sullo strumento di aggiornamento

#### <a name="mapping-conditions"></a>Mapping delle condizioni

Nella definizione aggiornata, lo strumento tenta di raggruppare le azioni dei rami true e false in un ambito. In particolare, il modello di progettazione `@equals(actions('a').status, 'Skipped')` verrà visualizzato come un'azione `else`. Se lo strumento rileva modelli non riconoscibili, tuttavia, potrebbe creare condizioni separate per i rami true e false. Se necessario, è possibile modificare il mapping delle azioni dopo l'aggiornamento.

#### <a name="foreach-loop-with-condition"></a>Ciclo "foreach" con condizione

Nel nuovo schema, è possibile usare l'azione di filtro per replicare il modello di un ciclo `foreach` con una condizione per elemento, ma questa modifica dovrebbe essere eseguita automaticamente durante l'aggiornamento. La condizione diventa un'azione di filtro prima del ciclo foreach per restituire solo una matrice di elementi che soddisfano la condizione e tale matrice viene passata nell'azione foreach. Per un esempio, vedere l'articolo relativo a [cicli e ambiti](../logic-apps/logic-apps-loops-and-scopes.md).

#### <a name="resource-tags"></a>Tag delle risorse

Dopo l'aggiornamento, i tag delle risorse vengono rimossi. È quindi necessario reimpostarli per il flusso di lavoro aggiornato.

## <a name="other-changes"></a>Altre modifiche

### <a name="renamed-manual-trigger-to-request-trigger"></a>Ridenominazione del trigger "manual" in "request"

Il tipo di trigger `manual` è deprecato ed è stato rinominato `request` con tipo `http`. Questa modifica offre maggiore coerenza per la tipologia di modello creata con il trigger.

### <a name="new-filter-action"></a>Nuova azione "filtro"

Per filtrare una matrice di grandi dimensioni in modo da ottenere un set ridotto di elementi, il nuovo tipo `filter` accetta una matrice e una condizione, valuta la condizione per ogni elemento e restituisce una matrice con gli elementi che soddisfano la condizione.

### <a name="restrictions-for-foreach-and-until-actions"></a>Restrizioni per le azioni "foreach" e "until"

I cicli `foreach` e `until` sono limitati a una singola azione.

### <a name="new-trackedproperties-for-actions"></a>Nuova proprietà "trackedProperties" per le azioni

Le azioni possono ora includere una proprietà aggiuntiva denominata `trackedProperties`, di pari livello rispetto alle proprietà `runAfter` e `type`. Questo oggetto specifica determinati input o output delle azioni da includere nei dati di telemetria di Diagnostica di Azure generati come parte di un flusso di lavoro, Ad esempio: 

```
{                
    "Http": {
        "inputs": {
            "method": "GET",
            "uri": "http://www.bing.com"
        },
        "runAfter": {},
        "type": "Http",
        "trackedProperties": {
            "responseCode": "@action().outputs.statusCode",
            "uri": "@action().inputs.uri"
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
* [Creare definizioni dei flussi di lavoro per le app per la logica](../logic-apps/logic-apps-author-definitions.md)
* [Creare modelli di distribuzione per le app per la logica](../logic-apps/logic-apps-create-deploy-template.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
