<properties 
	pageTitle="Creare definizioni di app per la logica" 
	description="Informazioni su come scrivere la definizione JSON per le app per la logica." 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/08/2015"
	ms.author="stepsic"/>
	
#Creare definizioni di app per la logica
Questo argomento illustra come usare le definizioni di [app per la logica dei servizi app](app-service-logic-what-are-logic-apps.md), che costituiscono un semplice linguaggio JSON dichiarativo. Se ancora non è stato fatto, leggere prima di tutto l'articolo su [come creare una nuova app per la logica](../app-service-create-a-logic-app.md). È anche possibile leggere il [materiale di riferimento completo del linguaggio di definizione su MSDN](https://msdn.microsoft.com/library/azure/dn948512.aspx).

## Più passaggi che si ripetono in un elenco

Un modello comune consiste nell'usare un solo passaggio per ottenere un elenco di elementi e quindi avere una serie di due o più azioni da eseguire nell'elenco.

![Ripetere in elenchi](./media/app-service-logic-author-definitions/repeatoverlists.png)

In questo esempio vengono eseguite 3 azioni:

1. Un'azione ottiene un elenco di articoli. Restituisce un oggetto che contiene una matrice.

2. Un'azione passa alla proprietà del collegamento di ogni articolo e restituisce la posizione effettiva dell'articolo.

3. Un'azione scorre tutti i risultati della seconda azione per scaricare gli articoli effettivi.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {},
    "actions": {
        "getArticles": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
            }
        },
        "readLinks": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatItem().link"
            },
            "repeat": "@body('getArticles').responseData.feed.entries"
        },
        "downloadLinks": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatOutputs().headers.location"
            },
            "conditions": [
                {
                    "expression": "@not(equals(actions('readLinks').status, 'Skipped'))"
                }
            ],
            "repeat": "@actions('readLinks').outputs.repeatItems"
        }
    },
    "outputs": {}
}
```

Come descritto nell'articolo [Usare le funzionalità delle app per la logica](app-service-logic-use-logic-app-features.md), scorrere il primo elenco usando la proprietà `repeat:` nella seconda azione. Per la terza azione è tuttavia necessario selezionare la proprietà `@actions('readLinks').outputs.repeatItems`, perché la seconda viene eseguita per ogni articolo.

All'interno dell'azione è possibile usare la funzione [`repeatItem()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatItem), [`repeatOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatOutputs) o [`repeatBody()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatBody). In questo esempio si vuole ottenere l'intestazione `location`, quindi si userà la funzione [`repeatOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatOutputs) per ottenere gli output dell'esecuzione della seconda azione che si sta scorrendo.

## Mapping degli elementi di un elenco a una configurazione diversa

A questo punto, si vuole ottenere un contenuto completamente diverso in base al valore di una proprietà. È possibile creare una mappa di valori a destinazioni come parametro.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "specialCategories": {
            "defaultValue": [
                "science",
                "google",
                "microsoft",
                "robots",
                "NSA"
            ],
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
    "triggers": {},
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
            "repeat": "@body('getArticles').responseData.feed.entries",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(repeatItem().categories, parameters('specialCategories')))]"
            },
            "conditions": [
                {
                    "expression": "@greater(length(intersection(repeatItem().categories, parameters('specialCategories'))), 0)"
                }
            ]
        }
    }
}
```

In questo caso, si ottiene prima di tutto un elenco di articoli e poi nel secondo passaggio si cerca in una mappa l'URL dal quale ottenere il contenuto, in base alla categoria definita come parametro.

Qui occorre prestare attenzione a due elementi: la funzione [`intersection()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#intersection) viene usata per verificare se la categoria corrisponde a una delle categorie note definite. Secondo, dopo avere ottenuto la categoria è possibile estrarre l'elemento dalla mappa usando parentesi quadre: `parameters[...]`.

## Concatenare/annidare le app per la logica durante la ripetizione in un elenco

Spesso è più facile gestire le app per la logica se sono più discrete. A questo scopo, è possibile effettuare il factoring della logica in più definizioni e chiamarle dalla stessa definizione padre. In questo esempio, si userà un'app per la logica padre che riceve gli ordini e un'app per la logica figlio che esegue alcuni passaggi per ogni ordine.

Nell'app per la logica padre:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "orders": {
            "defaultValue": [
                {
                    "quantity": 10,
                    "id": "myorder1"
                },
                {
                    "quantity": 200,
                    "id": "specialOrder"
                },
                {
                    "quantity": 5,
                    "id": "myOtherOrder"
                }
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "iterateOverOrders": {
            "repeat": "@parameters('orders')",
            "type": "Workflow",
            "inputs": {
                "uri": "https://westus.logic.azure.com/subscriptions/xxxxxx-xxxxx-xxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Logic/workflows/xxxxxxx",
                "apiVersion": "2015-02-01-preview",
                "trigger": {
                    "name": "submitOrder",
                    "outputs": {
                        "body": "@repeatItem()"
                    }
                },
                "authentication": {
                    "type": "Basic",
                    "username": "default",
                    "password": "xxxxxxxxxxxxxx"
                }
            }
        },
        "sendInvoices": {
            "repeat": "@outputs('iterateOverOrders').repeatItems",
            "type": "Http",
            "inputs": {
                "uri": "http://www.example.com/?invoiceID=@{repeatOutputs().run.outputs.deliverTime.value}",
                "method": "GET"
            }
        }
    },
    "outputs": {}
}
```

Nell'app per la logica figlio si userà quindi la funzione [`triggerBody()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#triggerBody) per ottenere i valori passati nel flusso di lavoro figlio. Si popoleranno quindi gli output con i dati da restituire al flusso padre.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {},
    "actions": {
        "calulatePrice": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?action=calcPrice&id=@{triggerBody().id}&qty=@{triggerBody().quantity}"
            }
        },
        "calculateDeliveryTime": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?action=calcTime&id=@{triggerBody().id}&qty=@{triggerBody().quantity}"
            }
        }
    },
    "outputs": {
        "deliverTime": {
            "type": "String",
            "value": "@outputs('calculateDeliveryTime').headers.etag"
        }
    }
}
```

Altre informazioni sono disponibili nell'articolo relativo all'[azione di tipo app per la logica su MSDN](https://msdn.microsoft.com/it-IT/library/azure/dn948511.aspx).

>[AZURE.NOTE]La finestra di progettazione di app per la logica non supporta le azioni di tipo app per la logica, quindi sarà necessario modificare la definizione manualmente.


## Passaggio di gestione degli errori in caso di problemi

In genere, è opportuno poter scrivere una *procedura di correzione*, cioè la logica che viene eseguita se, **e solo se**, una o più chiamate non riescono. In questo esempio, i dati vengono recuperati da diverse posizioni, ma se la chiamata non riesce, è opportuno INSERIRE un messaggio in qualche punto, in modo da poter ricostruire l'errore in seguito.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "dataFeeds": {
            "defaultValue": [
                "https://www.microsoft.com/it-IT/default.aspx",
                "https://gibberish.gibberish/"
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "readData": {
            "repeat": "@parameters('dataFeeds')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatItem()"
            }
        },
        "postToErrorMessageQueue": {
            "repeat": "@actions('readData').outputs.repeatItems",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?noteAnErrorFor=@{repeatItem().inputs.uri}"
            },
            "conditions": [
                {
                    "expression": "@equals(actions('readData').status, 'Failed')"
                },
                {
                    "expression": "@equals(repeatItem().status, 'Failed')"
                }
            ]
        }
    },
    "outputs": {}
}
```

In questo caso si usano due condizioni, perché nel primo passaggio l'azione viene ripetuta in un elenco. Se fosse disponibile una singola azione, sarebbe necessaria una sola condizione (la prima). Tenere presente che nella procedura di correzione è anche possibile usare gli *input* dell'azione non riuscita. In questo caso, l'URL non riuscito viene passato al secondo passaggio.

![Correzione](./media/app-service-logic-author-definitions/remediation.png)

Infine, poiché l'errore è stato gestito, l'esecuzione non viene più contrassegnata come **Non riuscita**. Come si può vedere, questa esecuzione risulta **Riuscita** anche se un passaggio non è riuscito, perché è stato scritto un passaggio per gestire l'errore.

## Due o più passaggi eseguiti in parallelo

Per eseguire più azioni in parallelo, invece che in sequenza, è necessario rimuovere la condizione `dependsOn` che collega le azioni. Dopo la rimozione della dipendenza, le azioni verranno eseguite automaticamente in parallelo, a meno che i dati di un'azione non siano necessari alle altre.

![Rami](./media/app-service-logic-author-definitions/branches.png)

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "dataFeeds": {
            "defaultValue": [
                "https://www.microsoft.com/it-IT/default.aspx",
                "https://office.live.com/start/default.aspx"
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "readData": {
            "repeat": "@parameters('dataFeeds')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatItem()"
            }
        },
        "branch1": {
            "repeat": "@actions('readData').outputs.repeatItems",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?branch1Logic=@{repeatItem().inputs.uri}"
            }
        },
        "branch2": {
            "repeat": "@actions('readData').outputs.repeatItems",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?branch2Logic=@{repeatItem().inputs.uri}"
            }
        }
    },
    "outputs": {}
}
```

Come si può vedere nell'esempio precedente, branch1 e branch2 dipendono semplicemente dal contenuto di readData. Di conseguenza, entrambi i rami (branch) saranno eseguiti in parallelo:

![Parallelo](./media/app-service-logic-author-definitions/parallel.png)

Come si può vedere, il timestamp è identico per entrambi i rami.

## Unire due diramazioni condizionali di logica

È possibile combinare due flussi condizionali di logica (che siano stati eseguiti o meno) mediante una singola azione che accetta i dati da entrambi i rami.

La strategia per questa operazione varia a seconda che venga gestito un elemento o una raccolta di elementi. Nel caso di un singolo elemento, si userà la funzione [`coalesce()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#coalesce):

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
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
    "triggers": {},
    "actions": {
        "handleNormalOrders": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderNormally=@{parameters('order').id}"
            },
            "conditions": [
                {
                    "expression": "@lessOrEquals(parameters('order').quantity, 100)"
                }
            ]
        },
        "handleSpecialOrders": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderSpecially=@{parameters('order').id}"
            },
            "conditions": [
                {
                    "expression": "@greater(parameters('order').quantity, 100)"
                }
            ]
        },
        "submitInvoice": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?invoice=@{coalesce(outputs('handleNormalOrders')?.headers?.etag,outputs('handleSpecialOrders')?.headers?.etag )}"
            },
            "conditions": [
                {
                    "expression": "@or(equals(actions('handleNormalOrders').status, 'Succeeded'), equals(actions('handleSpecialOrders').status, 'Succeeded'))"
                }
            ]
        }
    },
    "outputs": {}
}
```
 
In alternativa se i primi due rami agiscono, ad esempio, in un elenco di ordini, si userà la funzione [`union()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#union) per combinare i dati di entrambi i rami.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "orders": {
            "defaultValue": [
                {
                    "quantity": 10,
                    "id": "myorder1"
                },
                {
                    "quantity": 200,
                    "id": "specialOrder"
                },
                {
                    "quantity": 5,
                    "id": "myOtherOrder"
                }
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "handleNormalOrders": {
            "repeat": "@parameters('orders')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderNormally=@{repeatItem().id}"
            },
            "conditions": [
                {
                    "expression": "@lessOrEquals(repeatItem().quantity, 100)"
                }
            ]
        },
        "handleSpecialOrders": {
            "repeat": "@parameters('orders')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderSpecially=@{repeatItem().id}"
            },
            "conditions": [
                {
                    "expression": "@greater(repeatItem().quantity, 100)"
                }
            ]
        },
        "submitInvoice": {
            "repeat": "@union(actions('handleNormalOrders').outputs.repeatItems, actions('handleSpecialOrders').outputs.repeatItems)",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?invoice=@{repeatOutputs().headers.etag}"
            },
            "conditions": [
                {
                    "expression": "@equals(repeatItem().status, 'Succeeded')"
                }
            ]
        }
    },
    "outputs": {}
}
```
## Uso delle stringhe

Sono disponibili molte funzioni che è possibile usare per modificare le stringhe. Se consideri ad esempio di voler passare una stringa a un sistema, ma di non essere certi che la codifica dei caratteri venga gestita correttamente. Per questa stringa è possibile usare, ad esempio, la codifica Base64. Tuttavia, per evitare di eseguire l'escape in un URL, verranno sostituiti alcuni caratteri.

Si vuole anche ottenere una sottostringa del nome dell'ordine, perché i primi 5 caratteri non vengono usati.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
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
    "triggers": {},
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

1. Ottiene l'elemento [`length()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#length) del nome dell'autore dell'ordine e restituisce il numero totale di caratteri.

2. Sottrae 5 (si vuole ottenere una stringa più breve).

3. Accetta in effetti l'elemento [`substring()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#substring). Inizia dall'indice `5` e procede alla parte rimanente della stringa.

4. Converte la sottostringa in una stringa [`base64()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#base64).

5. [`replace()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#replace) sostituisce tutti i caratteri `+` con `-`

6. [`replace()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#replace) sostituisce tutti i caratteri `/` con `_`

## Utilizzo di date e ore

Date e ore possono risultare utili, specialmente quando si tenta di eseguire il pull dei dati da un'origine dati che non supporta **trigger** in modo naturale. È anche possibile usare date e ore per prevedere la durata di diversi passaggi.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
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
    "triggers": {},
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{parameters('order').id}"
            }
        },
        "timingWarning": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
        	},
            "conditions": [
                {
                    "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))"
                }
            ]
        }
    },
    "outputs": {}
}
```

In questo esempio viene estratto l'elemento `startTime` del passaggio precedente. Si otterrà quindi l'ora corrente sottraendo un secondo:[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/dn948512.aspx#addseconds) (è anche possibile usare altre unità di tempo come `minutes` o `hours`). Infine si potranno confrontare questi due valori. Se il primo è minore del secondo, significa che è trascorso più di un secondo dal momento in cui è stato inserito l'ordine.

Notare che è possibile usare formattatori di stringa per formattare le date: nella stringa di query viene usato [`utcnow('r')`](https://msdn.microsoft.com/library/azure/dn948512.aspx#utcnow) per ottenere RFC1123. Tutta la formattazione delle date [è documentata in MSDN](https://msdn.microsoft.com/library/azure/dn948512.aspx#utcnow).

## Passaggio di valori in fase di esecuzione per modificare il comportamento

Si supponga di avere diversi comportamenti che si prevede di eseguire in base ai valori usati per l'avvio dell'app per la logica. Per ottenere questi valori da quelli passati, è possibile usare la funzione [`triggerOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#triggerOutputs):

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "triggers": {},
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@triggerOutputs().uriToGet"
            }
        },
        "extraStep": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/extraStep"
            },
            "conditions": [
                {
                    "expression": "@triggerOutputs().doMoreLogic"
                }
            ]
        },  
    },
    "outputs": {}
}
```

Per eseguire questa operazione, quando si avvia l'esecuzione è necessario passare le proprietà necessarie (nell'esempio `uriToGet` e `doMoreLogic`). Di seguito è riportata la chiamata per cui è possibile [usare l'autenticazione di base](https://msdn.microsoft.com/library/azure/dn948513.aspx#basicAuth):

```
POST https://<<Logic app endpoint from the Essentials>>/run?api-version=2015-02-01-preview
Authorization: Basic <<Based 64 encoded username (default) : password (from the Settings blade)>>
Content-type: application/json
```

Il payload è il seguente. Si noti che sono stati forniti all'app per la logica i valori da usare a questo punto:

```
{
    "outputs": {
        "uriToGet" : "http://my.uri.I.want/",
        "doMoreLogic" : true
    }
}
``` 

Quando l'app per la logica viene eseguita, chiamerà l'URI passato ed eseguirà il passaggio aggiuntivo, perché nella chiamata è stato passato `true`. Per modificare solo i parametri al momento della distribuzione (non per *ogni esecuzione*), usare `parameters` come indicato di seguito.

## Uso di parametri al momento della distribuzione per diversi ambienti

In genere, un ciclo di vita di distribuzione prevede un ambiente di sviluppo, un ambiente di gestione temporanea e quindi un ambiente di produzione. In tutti questi ambienti è possibile avere la stessa definizione, ma usare, ad esempio, diversi database. In modo analogo, è possibile usare la stessa definizione in molte aree diverse ai fini della disponibilità elevata, ma fare in modo che ogni istanza dell'app per la logica comunichi con il database di quell'area.

Questo approccio è diverso dall'accettare diversi parametri in *fase di esecuzione*. In quel caso è necessario usare la funzione `trigger()` descritta sopra.

È possibile iniziare con una definizione molto semplicistica, come questa:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "connection": {
            "type": "string"
        }
    },
    "triggers": {},
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('connection')"
            }
        }        
    },
    "outputs": {}
}
```

Quindi, nella richiesta `PUT` effettiva per l'app per la logica è possibile fornire il parametro `connection`. Poiché non è più disponibile un valore predefinito, questo parametro è obbligatorio nel payload dell'app per la logica:

```
{
    "properties": {
        "sku": {
            "name": "Premium",
            "plan": {
                "id": "/subscriptions/xxxxx/resourceGroups/xxxxxx/providers/Microsoft.Web/serverFarms/xxxxxx"
            }
        },
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

## Esecuzione di un passaggio fino a quando non viene soddisfatta una condizione

È un'API che si sta chiamando e si desidera attendere una risposta determinata prima di procedere. Si supponga, ad esempio, che si desideri attendere che un utente carichi un file in una directory prima di elaborare il file. È possibile farlo con *si-fino a quando non*:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {},
    "actions": {
        "http0": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://mydomain/listfiles"
            },
            "until": {
                "limit": {
                    "timeout": "PT10M"
                },
                "conditions": [
                    {
                        "expression": "@greater(length(action().outputs.body),0)"
                    }
                ]
            }
        }
    },
    "outputs": {}
}
```

Per tutte le opzioni disponibili per la creazione e la gestione di app per la logica, vedere [documentazione dell'API REST](https://msdn.microsoft.com/library/azure/dn948513.aspx).

<!---HONumber=Sept15_HO2-->