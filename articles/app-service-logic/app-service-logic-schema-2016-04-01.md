---
title: Nuova versione dello schema 2016-06-01 | Documentazione Microsoft
description: Informazioni su come scrivere la definizione JSON per l&quot;ultima versione di App per la logica
author: jeffhollan
manager: dwrede
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eec9705688f88cedddedee32c562bdf714e45743


---
# <a name="new-schema-version-2016-06-01"></a>Nuova versione dello schema 2016-06-01
La nuova versione dello schema e dell'API per App per la logica include una serie di miglioramenti relativi in particolare all'affidabilità e alla semplicità d'uso di App per la logica. Le differenze principali sono tre:

1. Sono stati aggiunti ambiti che sono azioni contenenti una raccolta di azioni.
2. Le condizioni e i cicli sono azioni di prima classe
3. L'ordinamento di esecuzione è più dettagliato tramite la proprietà `runAfter` (che sostituisce `dependsOn`)

Per informazioni sull'aggiornamento delle app per la logica dallo schema 2015-08-01-preview allo schema 2016-06-01, [vedere la sezione relativa all'aggiornamento riportata di seguito](#upgrading-to-2016-06-01-schema)

## <a name="1-scopes"></a>1. Ambiti
L'aggiunta di ambiti e la possibilità di annidare le azioni in altre azioni è una delle modifiche principali di questo schema.  Questa modifica è utile quando si raggruppa un set di azioni o quando è necessario annidare azioni in altre azioni, ad esempio una condizione può contenere un'altra condizione.  Altre informazioni sulla sintassi dell'ambito sono disponibili [qui](app-service-logic-loops-and-scopes.md), ma un esempio semplice è illustrato di seguito:

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

## <a name="2-conditions-and-loops-changes"></a>2. Modifiche di condizioni e cicli
Nelle versioni precedenti dello schema le condizioni e i cicli sono parametri associati a una singola azione.  Questa limitazione è stata eliminata in questo schema e attualmente le condizioni e i cicli vengono visualizzati come tipo di azione.  Altre informazioni sono reperibili [in questo articolo](app-service-logic-loops-and-scopes.md)e un esempio semplice di una condizione di azione è illustrato di seguito:

```
{
    "If_trigger_is_foo": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'foo')",
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
            "if_trigger_is_bar": "..."
        }      
    }
}
```

## <a name="3-runafter-property"></a>3. Proprietà RunAfter
La nuova proprietà `runAfter` sostituisce `dependsOn` per consentire maggiore precisione nell'ordinamento dell'esecuzione.  `dependsOn` indica che l'azione è stata eseguita con esito positivo; spesso è tuttavia necessario eseguire un'azione se l'azione precedente ha avuto esito positivo, non è riuscita o è stata ignorata.  `runAfter` consente questo livello di flessibilità.  Questo oggetto specifica i nomi di tutte le azioni che verranno eseguite dopo e definisce una matrice di stati accettabili da cui eseguire il trigger.  Per definire l'esecuzione dopo che il passaggio A è stato completato o che il passaggio B è stato completato o non è riuscito, è possibile usare la proprietà `runAfter` seguente:

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrading-to-2016-06-01-schema"></a>Aggiornamento allo schema 2016-06-01
L'aggiornamento al nuovo schema 2016-06-01 richiede pochi passaggi.  Sono disponibili informazioni dettagliate sulle modifiche dello schema [in questo articolo](app-service-logic-schema-2016-04-01.md).  Il processo di aggiornamento include l'esecuzione di script di aggiornamento, il salvataggio come nuova app per la logica e potenzialmente la sovrascrittura della app per la logica precedente, se necessario.

1. Aprire la app per la logica corrente.
2. Fare clic sul pulsante **Aggiorna schema** sulla barra degli strumenti
   
    ![][1]
   
    Verrà restituita la definizione aggiornata.  È possibile copiare e incollare questa definizione in una definizione di risorsa, se necessario, ma **è consigliabile** usare il pulsante **Salva con nome** per assicurarsi che tutti i riferimenti alla connessione siano validi nell'app per la logica aggiornata.
3. Fare clic su di **Salva con nome** pulsante sulla barra degli strumenti del Pannello di aggiornamento.
4. Immettere il nome e lo stato dell'app per la logica e fare clic su **Crea** per distribuire l'app per la logica di aggiornamento.
5. Verificare che l'app per la logica aggiornata stia funzionando nel modo previsto.
   
   > [!NOTE]
   > Se si usa un trigger manuale o di richiesta, l'URL di callback sarà cambiato nella nuova app per la logica.  Usare il nuovo URL per verificare il funzionamento end-to-end ed è possibile clonare l'app per la logica esistente per mantenere gli URL precedenti.
   > 
   > 
6. *Facoltativo* Usare il pulsante **Clona** sulla barra degli strumenti (accanto all'icona **Aggiorna schema** nell'immagine precedente) per sovrascrivere l'app per la logica precedente con la nuova versione dello schema.  Ciò è necessario solo se si desidera mantenere lo stesso ID di risorsa o URL del trigger di richiesta dell'app per la logica.

### <a name="upgrade-tool-notes"></a>Note sullo strumento di aggiornamento
#### <a name="condition-mapping"></a>Mapping delle condizioni
Lo strumento migliora il raggruppamento di azioni del ramo true e false in un ambito nella definizione aggiornata.  In particolare, il modello di progettazione di `@equals(actions('a').status, 'Skipped')` verrà visualizzato come un'azione `else`.  Tuttavia se lo strumento rileva modelli non riconosce che potenzialmente creerà le condizioni separate sia per il ramo true sia per il ramo false.  Le azioni possono essere rimappate nella fase successiva all'aggiornamento, se necessario.

#### <a name="foreach-with-condition"></a>ForEach con condizione
Il modello precedente di un ciclo foreach con una condizione per ogni elemento può essere replicato nel nuovo schema con l'azione di filtro.  Questo dovrebbe avvenire automaticamente in fase di aggiornamento.  La condizione diventa un'azione di filtro prima del ciclo foreach (per restituire solo un array di elementi che soddisfano la condizione) e l'array viene passato nell'azione foreach.  È possibile visualizzare un esempio [in questo articolo](app-service-logic-loops-and-scopes.md)

#### <a name="resource-tags"></a>Tag delle risorse
I tag delle risorse verranno rimossi in seguito all'aggiornamento e sarà necessario impostarli di nuovo per il flusso di lavoro aggiornato.

## <a name="other-changes"></a>Altre modifiche
### <a name="manual-trigger-renamed-to-request-trigger"></a>Trigger manuale rinominato in trigger di richiesta
Il tipo `manual` è deprecato ed è stato rinominato in `request` con la tipologia `http`.  È più coerente con il tipo di modello usato dal trigger per la creazione.

### <a name="new-filter-action"></a>Nuova azione "filtro"
Se si usa un array di grandi dimensioni ed è necessario usare un filtro per un insieme di elementi più piccolo, è possibile usare il nuovo tipo di "filtro".  Accetta un array e una condizione e valuterà la condizione per ogni elemento e restituirà un array di elementi che soddisfano la condizione.

### <a name="foreach-and-until-action-restrictions"></a>Restrizioni delle azioni ForEach e until
I cicli foreach e until sono limitati a una singola azione.

### <a name="trackedproperties-on-actions"></a>TrackedProperties sulle azioni
Le azioni possono ora includere una proprietà aggiuntiva (di pari livello rispetto a `runAfter` e `type`) denominata `trackedProperties`.  È un oggetto che consente di specificare alcuni input o output di azione da includere nei dati di telemetria diagnostica di Azure generati come parte di un flusso di lavoro.  ad esempio:

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
* [Usare la definizione del flusso della app per la logica](app-service-logic-author-definitions.md)
* [Creare un modello di distribuzione di app per la logica](app-service-logic-create-deploy-template.md)

<!-- Image references -->
[1]: ./media/app-service-logic-schema-2016-04-01/upgradeButton.png



<!--HONumber=Nov16_HO3-->


