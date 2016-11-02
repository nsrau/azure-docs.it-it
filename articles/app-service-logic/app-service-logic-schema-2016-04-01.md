<properties 
	pageTitle="Nuova versione dello schema 2016-06-01 | Microsoft Azure" 
	description="Informazioni su come scrivere la definizione JSON per l'ultima versione di App per la logica" 
	authors="jeffhollan" 
	manager="dwrede" 
	editor="" 
	services="logic-apps" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="jehollan"/>
	
# Nuova versione dello schema 2016-06-01

La nuova versione dello schema e dell'API per App per la logica include una serie di miglioramenti relativi in particolare all'affidabilità e alla semplicità d'uso di App per la logica. Le differenze principali sono tre:

1. Sono stati aggiunti ambiti che sono azioni contenenti una raccolta di azioni.
1. Le condizioni e i cicli sono azioni di prima classe
1. L'ordinamento di esecuzione è più dettagliato tramite la proprietà `runAfter` (che sostituisce `dependsOn`)

Per informazioni sull'aggiornamento delle app per la logica dallo schema 2015-08-01-preview allo schema 2016-06-01, [vedere la sezione relativa all'aggiornamento riportata di seguito](#upgrading-to-2016-06-01-schema).


## 1\. Ambiti

L'aggiunta di ambiti e la possibilità di annidare le azioni in altre azioni è una delle modifiche principali di questo schema. Questa modifica è utile quando si raggruppa un set di azioni o quando è necessario annidare azioni in altre azioni, ad esempio una condizione può contenere un'altra condizione. Altre informazioni sulla sintassi dell'ambito sono disponibili [qui](app-service-logic-loops-and-scopes.md), ma un esempio semplice è illustrato di seguito:


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

## 2\. Modifiche di condizioni e cicli

Nelle versioni precedenti dello schema le condizioni e i cicli sono parametri associati a una singola azione. Questa limitazione è stata eliminata in questo schema e attualmente le condizioni e i cicli vengono visualizzati come tipo di azione. Altre informazioni sono reperibili [in questo articolo](app-service-logic-loops-and-scopes.md) e un esempio semplice di una condizione di azione è illustrato di seguito:

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

## 3\. Proprietà RunAfter

La nuova proprietà `runAfter` sostituisce `dependsOn` e consente maggiore precisione in fase di ordinamento. `dependsOn` indica che l'azione è stata eseguita con esito positivo; spesso è tuttavia necessario eseguire un'azione se l'azione precedente ha avuto esito positivo, non è riuscita o è stata ignorata. `runAfter` consente questo livello di flessibilità. Questo oggetto specifica i nomi di tutte le azioni che verranno eseguite dopo e definisce una matrice di stati accettabili da cui eseguire il trigger. Per definire l'esecuzione dopo che il passaggio A è stato completato o che il passaggio B è stato completato o non è riuscito, è possibile usare la proprietà `runAfter` seguente:

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## Aggiornamento allo schema 2016-06-01

L'aggiornamento al nuovo schema 2016-06-01 richiede pochi passaggi. Sono disponibili informazioni dettagliate sulle modifiche dello schema [in questo articolo](app-service-logic-schema-2016-04-01.md). Il processo di aggiornamento include l'esecuzione di script di aggiornamento, il salvataggio come nuova app per la logica e potenzialmente la sovrascrittura della app per la logica precedente, se necessario.

1. Aprire la app per la logica corrente.
1. Fare clic sul pulsante **Aggiorna schema** sulla barra degli strumenti
   
    ![][1]
   
    Verrà restituita la definizione aggiornata. È possibile copiare e incollare questa definizione in una definizione di risorsa, se necessario, ma si **consiglia vivamente di **usare il pulsante **Salva con nome** per assicurarsi che tutti i riferimenti di connessione siano validi nella app per la logica aggiornata.
1. Fare clic su di **Salva con nome** pulsante sulla barra degli strumenti del Pannello di aggiornamento.
1. Immettere il nome e lo stato dell'app per la logica e fare clic su **Crea** per distribuire l'app per la logica di aggiornamento.
1. Verificare che l'app per la logica aggiornata stia funzionando nel modo previsto.

    >[AZURE.NOTE] Se si usa un trigger manuale o di richiesta, l'URL di callback sarà cambiato nella nuova app per la logica. Usare il nuovo URL per verificare il funzionamento end-to-end ed è possibile clonare l'app per la logica esistente per mantenere gli URL precedenti.

1. *Facoltativo* Usare il pulsante **Clone** sulla barra degli strumenti (accanto all'icona **Aggiorna schema** nell'immagine precedente) per sovrascrivere l'app per la logica precedente con la nuova versione dello schema. Ciò è necessario solo se si desidera mantenere lo stesso ID di risorsa o URL del trigger di richiesta dell'app per la logica.

### Note sullo strumento di aggiornamento

#### Mapping delle condizioni

Lo strumento migliora il raggruppamento di azioni del ramo true e false in un ambito nella definizione aggiornata. In particolare il modello di progettazione di `@equals(actions('a').status, 'Skipped')` da visualizzare come un'azione `else`. Tuttavia se lo strumento rileva modelli non riconosce che potenzialmente creerà le condizioni separate sia per il ramo true sia per il ramo false. Le azioni possono essere rimappate nella fase successiva all'aggiornamento, se necessario.

#### ForEach con condizione
  
Il modello precedente di un ciclo foreach con una condizione per ogni elemento può essere replicato nel nuovo schema con l'azione di filtro. Questo dovrebbe avvenire automaticamente in fase di aggiornamento. La condizione diventa un'azione di filtro prima del ciclo foreach (per restituire solo un array di elementi che soddisfano la condizione) e l'array viene passato nell'azione foreach. È possibile visualizzare un esempio [in questo articolo](app-service-logic-loops-and-scopes.md)

#### Tag delle risorse

I tag delle risorse verranno rimossi in seguito all'aggiornamento e sarà necessario impostarli di nuovo per il flusso di lavoro aggiornato.

## Altre modifiche

### Trigger manuale rinominato in trigger di richiesta

Il tipo `manual` è stato deprecato e rinominato in `request` con il tipo `http`. È più coerente con il tipo di modello usato dal trigger per la creazione.

### Nuova azione "filtro"

Se si usa un array di grandi dimensioni ed è necessario usare un filtro per un insieme di elementi più piccolo, è possibile usare il nuovo tipo di "filtro". Accetta un array e una condizione e valuterà la condizione per ogni elemento e restituirà un array di elementi che soddisfano la condizione.

### Restrizioni delle azioni ForEach e until

I cicli foreach e until sono limitati a una singola azione.

### TrackedProperties sulle azioni

Le azioni possono ora includere una proprietà aggiuntiva (elemento di pari livello `runAfter` e `type`) denominata `trackedProperties`. È un oggetto che consente di specificare alcuni input o output di azione da includere nei dati di telemetria diagnostica di Azure generati come parte di un flusso di lavoro. ad esempio:

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

## Passaggi successivi
- [Usare la definizione del flusso della app per la logica](app-service-logic-author-definitions.md)
- [Creare un modello di distribuzione di app per la logica](app-service-logic-create-deploy-template.md)


<!-- Image references -->
[1]: ./media/app-service-logic-schema-2016-04-01/upgradeButton.png

<!---HONumber=AcomDC_0803_2016-->