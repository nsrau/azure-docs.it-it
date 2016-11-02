<properties 
	pageTitle="Creare definizioni di app per la logica | Microsoft Azure" 
	description="Informazioni su come scrivere la definizione JSON per le app per la logica" 
	authors="jeffhollan" 
	manager="erikre" 
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
	
# Creare definizioni di app per la logica
Questo argomento illustra come usare le definizioni di [app per la logica di Azure](app-service-logic-what-are-logic-apps.md), che costituiscono un semplice linguaggio JSON dichiarativo. Se ancora non è stato fatto, leggere prima di tutto l'articolo su [come creare una nuova app per la logica](app-service-logic-create-a-logic-app.md). È anche possibile leggere il [materiale di riferimento completo del linguaggio di definizione su MSDN](http://aka.ms/logicappsdocs).

## Più passaggi che si ripetono in un elenco

È possibile sfruttare il [tipo foreach](app-service-logic-loops-and-scopes.md) per ripetere una matrice di un massimo di 10.000 elementi ed eseguire un'azione per ogni elemento.

## Passaggio di gestione degli errori in caso di problemi

In genere, è opportuno saper scrivere una *procedura di correzione*, cioè la logica che viene eseguita se, **e solo se**, una o più chiamate hanno esito negativo. In questo esempio, i dati vengono recuperati da diverse posizioni, ma se la chiamata ha esito negativo, è opportuno INSERIRE un messaggio in un punto qualsiasi, in modo da poter ricostruire l'errore in un secondo momento.

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"readData": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "http://myurl"
			}
		},
		"postToErrorMessageQueue": {
			"type": "ApiConnection",
			"inputs": "...",
			"runAfter": {
				"readData": ["Failed"]
			}
		}
	},
	"outputs": {}
}
```

È possibile usare la proprietà `runAfter` per specificare che `postToErrorMessageQueue` deve essere eseguito solo quando `readData` è **Failed**. Può anche trattarsi di un elenco di possibili valori, quindi `runAfter` può essere `["Succeeded", "Failed"]`.

Infine, poiché l'errore è stato gestito, l'esecuzione non viene più contrassegnata come **Non riuscita**. Come si può vedere, questa esecuzione risulta **Riuscita** anche se un passaggio non è riuscito, perché è stato scritto un passaggio per gestire l'errore.

## Due o più passaggi eseguiti in parallelo

Per eseguire più azioni in parallelo, la proprietà `runAfter` deve essere equivalente in fase di esecuzione.

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"readData": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "http://myurl"
			}
		},
		"branch1": {
			"type": "Http",
			"inputs": "...",
			"runAfter": {
				"readData": ["Succeeded"]
			}
		},
		"branch2": {
			"type": "Http",
			"inputs": "...",
			"runAfter": {
				"readData": ["Succeeded"]
			}
		}
	},
	"outputs": {}
}
```

Come si può osservare nell'esempio precedente, sia `branch1` che `branch2` vengono impostati per essere eseguiti dopo `readData`. Di conseguenza, entrambi i rami (branch) saranno eseguiti in parallelo:

![Parallelo](./media/app-service-logic-author-definitions/parallel.png)

Come si può vedere, il timestamp è identico per entrambi i rami.

## Creare un join di due rami paralleli

È possibile creare un join di due azioni impostate per l'esecuzione in parallelo aggiungendo elementi alla proprietà `runAfter` come è stato fatto sopra.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
    "actions": {
        "readData": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        },
        "branch1": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "branch2": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "join": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "branch1": [
                    "Succeeded"
                ],
                "branch2": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {},
    "parameters": {},
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {}
            },
            "kind": "Http",
            "type": "Request"
        }
    }
}
```

![Parallelo](./media/app-service-logic-author-definitions/join.png)

## Mapping degli elementi di un elenco a una configurazione diversa

A questo punto, si vuole ottenere un contenuto completamente diverso in base al valore di una proprietà. È possibile creare una mappa di valori alle relative destinazioni come parametro.

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"specialCategories": {
			"defaultValue": ["science", "google", "microsoft", "robots", "NSA"],
			"type": "Array"
		},
		"destinationMap": {
			"defaultValue": {
				"science": "http://www.nasa.gov",
				"microsoft": "https://www.microsoft.com/it-IT/default.aspx",
				"google": "https://www.google.com",
				"robots": "https://en.wikipedia.org/wiki/Robot",
				"NSA": "https://www.nsa.gov/"
			},
			"type": "Object"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"getArticles": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
			},
			"conditions": []
		},
		"getSpecialPage": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
			},
			"conditions": [{
				"expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)"
			}],
			"forEach": "@body('getArticles').responseData.feed.entries"
		}
	}
}
```

In questo caso, si ottiene prima di tutto un elenco di articoli e poi nel secondo passaggio si cerca in una mappa l'URL dal quale ottenere il contenuto, in base alla categoria definita come parametro.

Qui occorre prestare attenzione a due elementi: la funzione [`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) viene usata per verificare se la categoria corrisponde a una delle categorie note definite. Secondo, dopo avere ottenuto la categoria è possibile estrarre l'elemento dalla mappa usando parentesi quadre: `parameters[...]`.

## Uso delle stringhe

Sono disponibili molte funzioni che è possibile usare per modificare le stringhe. Se consideri ad esempio di voler passare una stringa a un sistema, ma di non essere certi che la codifica dei caratteri venga gestita correttamente. Per questa stringa è possibile usare, ad esempio, la codifica Base64. Tuttavia, per evitare di eseguire l'escape in un URL, verranno sostituiti alcuni caratteri.

Si vuole anche ottenere una sottostringa del nome dell'ordine, perché i primi 5 caratteri non vengono usati.

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"order": {
			"defaultValue": {
				"quantity": 10,
				"id": "myorder1",
				"orderer": "NAME=Stèphén__Šīçiłianö"
			},
			"type": "Object"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"order": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
			}
		}
	},
	"outputs": {}
}
```

Descrizione della stringa di codice:

1. Ottiene l'elemento [`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length) del nome dell'autore dell'ordine e restituisce il numero totale di caratteri.

2. Sottrae 5 (si vuole ottenere una stringa più breve).

3. Accetta in effetti l'elemento [`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring). Inizia dall'indice `5` e procede alla parte rimanente della stringa.

4. Converte la sottostringa in una stringa [`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64).

5. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) sostituisce tutti i caratteri `+` con `-`

6. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) sostituisce tutti i caratteri `/` con `_`

## Utilizzo di date e ore

Date e ore possono risultare utili, specialmente quando si tenta di eseguire il pull dei dati da un'origine dati che non supporta **trigger** in modo naturale. È anche possibile usare date e ore per prevedere la durata di diversi passaggi.

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"order": {
			"defaultValue": {
				"quantity": 10,
				"id": "myorder1"
			},
			"type": "Object"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"order": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "http://www.example.com/?id=@{parameters('order').id}"
			}
		},
		"timingWarning": {
			"actions" {
				"type": "Http",
				"inputs": {
					"method": "GET",
					"uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
				},
				"runAfter": {}
			}
			"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))"
		}
	},
	"outputs": {}
}
```

In questo esempio viene estratto l'elemento `startTime` del passaggio precedente. Si otterrà quindi l'ora corrente sottraendo un secondo:[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) (è anche possibile usare altre unità di tempo come `minutes` o `hours`). Infine si potranno confrontare questi due valori. Se il primo è minore del secondo, significa che è trascorso più di un secondo dal momento in cui è stato inserito l'ordine.

Notare che è possibile usare formattatori di stringa per formattare le date: nella stringa di query viene usato [`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow) per ottenere RFC1123. Tutta la formattazione delle date [è documentata in MSDN](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow).

## Uso di parametri al momento della distribuzione per diversi ambienti

In genere, un ciclo di vita di distribuzione prevede un ambiente di sviluppo, un ambiente di gestione temporanea e quindi un ambiente di produzione. In tutti questi ambienti è possibile avere la stessa definizione, ma usare, ad esempio, diversi database. In modo analogo, è possibile usare la stessa definizione in molte aree diverse ai fini della disponibilità elevata, ma fare in modo che ogni istanza dell'app per la logica comunichi con il database di quell'area.

Questo approccio è diverso dall'accettare diversi parametri in *fase di esecuzione*. In quel caso è necessario usare la funzione `trigger()` descritta sopra.

È possibile iniziare con una definizione molto semplicistica, come questa:

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"uri": {
			"type": "string"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"readData": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "@parameters('uri')"
			}
		}
	},
	"outputs": {}
}
```

Quindi, nella richiesta `PUT` effettiva per l'app per la logica è possibile fornire il parametro `uri`. Poiché non è più disponibile un valore predefinito, questo parametro è obbligatorio nel payload dell'app per la logica:

```
{
    "properties": {},
        "definition": {
          // Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

In ogni ambiente è quindi possibile fornire un valore diverso per il parametro `connection`.

Per tutte le opzioni disponibili per la creazione e la gestione di app per la logica, vedere [documentazione dell'API REST](https://msdn.microsoft.com/library/azure/mt643787.aspx).

<!---HONumber=AcomDC_0803_2016-->