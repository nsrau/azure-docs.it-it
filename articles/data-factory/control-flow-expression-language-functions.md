---
title: Espressioni e funzioni in Azure Data Factory | Microsoft Docs
description: "Questo articolo fornisce informazioni sulle espressioni e funzioni che è possibile usare per la creazione di entità di data factory."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 78f21576bb7d839e5b5c4d8c2b721e381d663406
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Espressioni e funzioni in Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-functions-variables.md)
> * [Versione 2 - Anteprima](control-flow-expression-language-functions.md)

Questo articolo fornisce informazioni dettagliate sulle espressioni e funzioni supportate da Azure Data Factory (versione 2). 

## <a name="introduction"></a>Introduzione
I valori JSON nella definizione possono essere letterali o espressioni che vengono valutate in fase di esecuzione. Ad esempio:   
  
```json
"name": "value"
```

 (oppure)  
  
```json
"name": "@pipeline().parameters.password"
```


> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Funzioni e variabili in Data Factory V1](v1/data-factory-functions-variables.md).


## <a name="expressions"></a>Espressioni  
Le espressioni possono trovarsi in qualsiasi punto in un valore stringa JSON e restituiscono sempre un altro valore JSON. Se un valore JSON è un'espressione, il corpo dell'espressione viene estratto rimuovendo il simbolo di chiocciola (@). Se è necessaria una stringa letterale che inizia con @, occorre che la stringa sia preceduta da un carattere di escape @@. L'esempio seguente illustra la modalità di valutazione delle espressioni.  
  
|Valore JSON|Risultato|  
|----------------|------------|  
|"parameters"|Vengono restituiti i caratteri di tipo 'parameters'.|  
|"parameters[1]"|Vengono restituiti i caratteri di tipo 'parameters[1]'.|  
|"@@"|Viene restituita una stringa da 1 carattere che contiene '@'.|  
|" @"|Viene restituita una stringa da 2 caratteri che contiene ' @'.|  
  
 Tramite una funzionalità denominata *interpolazione delle stringhe*, è possibile inserire le espressioni anche all'interno delle stringhe in cui viene eseguito il wrapping delle espressioni in `@{ ... }`. Ad esempio: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Usando l'interpolazione delle stringhe, il risultato è sempre una stringa. Si supponga di aver definito `myNumber` come `42` e `myString` come `foo`:  
  
|Valore JSON|Risultato|  
|----------------|------------|  
|"@pipeline().parameters.myString"| Restituisce `foo` come stringa.|  
|"@{pipeline().parameters.myString}"| Restituisce `foo` come stringa.|  
|"@pipeline().parameters.myNumber"| Restituisce `42` come *numero*.|  
|"@{pipeline().parameters.myNumber}"| Restituisce `42` come *stringa*.|  
|"Answer is: @{pipeline().parameters.myNumber}"| Restituisce la stringa `Answer is: 42`.|  
|"@concat('Answer is: ', string(pipeline().parameters.myNumber))"| Restituisce la stringa `Answer is: 42`.|  
|"Answer is: @@{pipeline().parameters.myNumber}"| Restituisce la stringa `Answer is: @{pipeline().parameters.myNumber}`.|  
  
### <a name="examples"></a>Esempi

#### <a name="a-dataset-with-a-parameter"></a>Un set di dati con un parametro
Nell'esempio seguente BlobDataset accetta un parametro denominato **path**. Questo valore viene usato per impostare un valore per la proprietà **folderPath** usando l'espressione seguente: `@{dataset().path}`. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

#### <a name="a-pipeline-with-a-parameter"></a>Una pipeline con un parametro
Nell'esempio seguente la pipeline accetta i parametri **inputPath** e **outputPath**. Il parametro **path** per il set di dati del BLOB con parametri viene impostato usando i valori di questi parametri. La sintassi usata è: `pipeline().parameters.parametername`. 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```
  
## <a name="functions"></a>Funzioni  
 È possibile chiamare le funzioni all'interno delle espressioni. Le sezioni seguenti forniscono informazioni sulle funzioni che è possibile usare in un'espressione.  

## <a name="string-functions"></a>Funzioni stringa  
 Le funzioni seguenti sono applicabili solo alle stringhe. È anche possibile usare numerose funzioni di raccolta sulle stringhe.  
  
|Nome della funzione|DESCRIZIONE|  
|-------------------|-----------------|  
|concat|Combina un numero qualsiasi di stringhe. Se ad esempio parameter1 è `foo,` l'espressione seguente restituisce `somevalue-foo-somevalue`: `concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **Numero parametro**: 1 ... *n*<br /><br /> **Nome**: String *n*<br /><br /> **Descrizione**: obbligatoria. Stringhe da combinare in una singola stringa.|  
|substring|Restituisce un sottoinsieme di caratteri da una stringa. Ad esempio, l'espressione seguente:<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> restituisce:<br /><br /> `foo`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrizione**: obbligatoria. Stringa da cui viene ottenuta la sottostringa.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: StartIndex<br /><br /> **Descrizione**: obbligatoria. Indice da cui inizia la sottostringa nel parametro 1.<br /><br /> **Numero di parametro**: 3<br /><br /> **Nome**: Length<br /><br /> **Descrizione**: obbligatoria. Lunghezza della sottostringa.|  
|replace|Sostituisce una stringa con una stringa specifica. Ad esempio, l'espressione:<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> restituisce:<br /><br /> `the new string`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: string<br /><br /> **Descrizione**: obbligatoria.  Se il parametro 2 viene trovato nel parametro 1, è la stringa in cui viene eseguita la ricerca del parametro 2 e che viene aggiornata con il parametro 3.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: OldString<br /><br /> **Descrizione**: obbligatoria. Stringa da sostituire con il parametro 3 quando viene trovata una corrispondenza nel parametro 1<br /><br /> **Numero di parametro**: 3<br /><br /> **Nome**: NewString<br /><br /> **Descrizione**: obbligatoria. Stringa usata per sostituire la stringa nel parametro 2 quando viene trovata una corrispondenza nel parametro 1.|  
|GUID| Genera una stringa univoca globale (Guid). Ad esempio, potrebbe essere generato l'output seguente `c2ecc88d-88c8-4096-912c-d6f2e2b138ce`:<br /><br /> `guid()`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Format<br /><br /> **Descrizione**: facoltativa. Identificatore di formato singolo che indica [come formattare il valore di questo GUID](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). Il parametro format può essere "N", "D", "B", "P" o "X". Se il valore format non viene specificato, viene usato "D".|  
|toLower|Converte una stringa in lettere minuscole. Ad esempio, il codice seguente restituisce `two by two is four`: `toLower('Two by Two is Four')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrizione**: obbligatoria. Stringa da convertire in lettere minuscole. Se un carattere nella stringa non ha un equivalente minuscolo, viene incluso senza modifiche nella stringa restituita.|  
|toUpper|Converte una stringa in lettere maiuscole. Ad esempio, l'espressione seguente restituisce `TWO BY TWO IS FOUR`: `toUpper('Two by Two is Four')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrizione**: obbligatoria. Stringa da convertire in lettere maiuscole. Se un carattere nella stringa non ha un equivalente maiuscolo, viene incluso senza modifiche nella stringa restituita.|  
|indexof|Consente di trovare l'indice di un valore in una stringa senza distinzione tra maiuscole e minuscole. Ad esempio, l'espressione seguente restituisce `7`: `indexof('hello, world.', 'world')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrizione**: obbligatoria. Stringa che potrebbe contenere il valore.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: String<br /><br /> **Descrizione**: obbligatoria. Valore nel cui indice si vuole eseguire la ricerca.|  
|lastindexof|Consente di trovare l'ultimo indice di un valore in una stringa senza distinzione tra maiuscole e minuscole. Ad esempio, l'espressione seguente restituisce `3`: `lastindexof('foofoo', 'foo')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrizione**: obbligatoria. Stringa che potrebbe contenere il valore.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: String<br /><br /> **Descrizione**: obbligatoria. Valore nel cui indice si vuole eseguire la ricerca.|  
|startswith|Verifica se la stringa inizia con un valore senza distinzione tra maiuscole e minuscole. Ad esempio, l'espressione seguente restituisce `true`: `lastindexof('hello, world', 'hello')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrizione**: obbligatoria. Stringa che potrebbe contenere il valore.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: String<br /><br /> **Descrizione**: obbligatoria. Valore con cui può iniziare la stringa.|  
|endswith|Verifica se la stringa finisce con un valore senza distinzione tra maiuscole e minuscole. Ad esempio, l'espressione seguente restituisce `true`: `lastindexof('hello, world', 'world')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrizione**: obbligatoria. Stringa che potrebbe contenere il valore.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: String<br /><br /> **Descrizione**: obbligatoria. Valore con cui può finire la stringa.|  
|split|Suddivide la stringa usando un separatore. Ad esempio, l'espressione seguente restituisce `["a", "b", "c"]`: `split('a;b;c',';')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrizione**: obbligatoria. Stringa suddivisa.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: String<br /><br /> **Descrizione**: obbligatoria. Separatore.|  
  
  
## <a name="collection-functions"></a>Funzioni di raccolta  
 Queste funzioni operano sulle raccolte come le matrici, le stringhe e, in alcuni casi, i dizionari.  
  
|Nome della funzione|DESCRIZIONE|  
|-------------------|-----------------|  
|contains|Restituisce true se un dizionario contiene una chiave, se un elenco contiene un valore o se una stringa contiene una sottostringa. Ad esempio, l'espressione seguente restituisce `true:``contains('abacaba','aca')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: WithinCollection<br /><br /> **Descrizione**: obbligatoria. Raccolta in cui eseguire la ricerca.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: FindObject<br /><br /> **Descrizione**: obbligatoria. Oggetto da trovare all'interno di **WithinCollection**.|  
|length|Restituisce il numero di elementi in una matrice o in una stringa. Ad esempio, l'espressione seguente restituisce `3`: `length('abc')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Collection<br /><br /> **Descrizione**: obbligatoria. La raccolta di cui si vuole ottenere la lunghezza.|  
|empty|Restituisce true se la matrice, la stringa o l'oggetto è vuoto. Ad esempio, l'espressione seguente restituisce `true`:<br /><br /> `empty('')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Collection<br /><br /> **Descrizione**: obbligatoria. Raccolta da verificare se l'elemento è vuoto.|  
|intersezione|Restituisce una singola matrice o un singolo oggetto con elementi comuni tra le matrici o gli oggetti passati. Ad esempio, questa funzione restituisce `[1, 2]`:<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> I parametri per la funzione possono essere un set di oggetti o un set di matrici, non una combinazione di entrambi. Se sono presenti due oggetti con lo stesso nome, l'ultimo oggetto con tale nome viene visualizzato nell'oggetto finale.<br /><br /> **Numero parametro**: 1 ... *n*<br /><br /> **Nome**: Collection *n*<br /><br /> **Descrizione**: obbligatoria. Raccolte da valutare. Un oggetto deve essere presente in tutte le raccolte passate per essere visualizzato nel risultato.|  
|union|Restituisce una singola matrice o un singolo oggetto con tutti gli elementi che si trovano nella matrice o nell'oggetto passato. Ad esempio, questa funzione restituisce `[1, 2, 3, 10, 101]:`<br /><br /> :  `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> I parametri per la funzione possono essere un set di oggetti o un set di matrici, non una combinazione di entrambi. Se sono presenti due oggetti con lo stesso nome nell'output finale, l'ultimo oggetto con tale nome viene visualizzato nell'oggetto finale.<br /><br /> **Numero parametro**: 1 ... *n*<br /><br /> **Nome**: Collection *n*<br /><br /> **Descrizione**: obbligatoria. Raccolte da valutare. Un oggetto che viene visualizzato in una delle raccolte viene visualizzato nel risultato.|  
|first|Restituisce il primo elemento nella matrice o nella stringa passata. Ad esempio, questa funzione restituisce `0`:<br /><br /> `first([0,2,3])`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Collection<br /><br /> **Descrizione**: obbligatoria. Raccolta da cui ottenere il primo oggetto.|  
|last|Restituisce l'ultimo elemento nella matrice o nella stringa passata. Ad esempio, questa funzione restituisce `3`:<br /><br /> `last('0123')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Collection<br /><br /> **Descrizione**: obbligatoria. Raccolta da cui ottenere l'ultimo oggetto.|  
|take|Restituisce i primi elementi **Count** dalla matrice o dalla stringa passata, ad esempio questa funzione restituisce `[1, 2]`: `take([1, 2, 3, 4], 2)`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Collection<br /><br /> **Descrizione**: obbligatoria. Raccolta di cui accettare i primi oggetti **Count**.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: Count<br /><br /> **Descrizione**: obbligatoria. Numero di oggetti da ottenere da **Collection**. Deve essere un intero positivo.|  
|skip|Restituisce gli elementi nella matrice a partire dall'indice **Count**. Ad esempio, questa funzione restituisce`[3, 4]`:<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Collection<br /><br /> **Descrizione**: obbligatoria. Raccolta di cui ignorare i primi oggetti **Count**.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: Count<br /><br /> **Descrizione**: obbligatoria. Numero di oggetti da rimuovere dalla parte iniziale di **Collection**. Deve essere un intero positivo.|  
  
## <a name="logical-functions"></a>Funzioni logiche  
 Queste funzioni sono utili all'interno delle condizioni e possono essere usate per valutare qualsiasi tipo di logica.  
  
|Nome della funzione|DESCRIZIONE|  
|-------------------|-----------------|  
|equals|Restituisce true se due valori sono uguali. Se ad esempio parameter1 è foo, l'espressione seguente restituisce `true`: `equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Object 1<br /><br /> **Descrizione**: obbligatoria. Oggetto da confrontare a **Object 2**.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: Object 2<br /><br /> **Descrizione**: obbligatoria. Oggetto da confrontare a **Object 1**.|  
|less|Restituisce true se il primo argomento è inferiore al secondo. Si noti che i valori possono essere solo di tipo intero, a virgola mobile o stringa. Ad esempio, l'espressione seguente restituisce `true`: `less(10,100)`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Object 1<br /><br /> **Descrizione**: obbligatoria. Oggetto da controllare per verificare se è inferiore a **Object 2**.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: Object 2<br /><br /> **Descrizione**: obbligatoria. Oggetto da controllare per verificare se è superiore a **Object 1**.|  
|lessOrEquals|Restituisce true se il primo argomento è inferiore o uguale al secondo. Si noti che i valori possono essere solo di tipo intero, a virgola mobile o stringa. Ad esempio, l'espressione seguente restituisce `true`: `lessOrEquals(10,10)`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Object 1<br /><br /> **Descrizione**: obbligatoria. Oggetto da controllare per verificare se è inferiore o uguale a **Object 2**.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: Object 2<br /><br /> **Descrizione**: obbligatoria. Oggetto da controllare per verificare se è superiore o uguale a **Object 1**.|  
|greater|Restituisce true se il primo argomento è superiore al secondo. Si noti che i valori possono essere solo di tipo intero, a virgola mobile o stringa. Ad esempio, l'espressione seguente restituisce `false`: `greater(10,10)`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Object 1<br /><br /> **Descrizione**: obbligatoria. Oggetto da controllare per verificare se è superiore a **Object 2**.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: Object 2<br /><br /> **Descrizione**: obbligatoria. Oggetto da controllare per verificare se è inferiore a **Object 1**.|  
|greaterOrEquals|Restituisce true se il primo argomento è superiore o uguale al secondo. Si noti che i valori possono essere solo di tipo intero, a virgola mobile o stringa. Ad esempio, l'espressione seguente restituisce `false`: `greaterOrEquals(10,100)`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Object 1<br /><br /> **Descrizione**: obbligatoria. Oggetto da controllare per verificare se è superiore o uguale a **Object 2**.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: Object 2<br /><br /> **Descrizione**: obbligatoria. Oggetto da controllare per verificare se è inferiore o uguale a **Object 1**.|  
|e|Restituisce true se entrambi i parametri sono true. Entrambi gli argomenti devono essere valori booleani. L'espressione seguente restituisce `false`: `and(greater(1,10),equals(0,0))`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Boolean 1<br /><br /> **Descrizione**: obbligatoria. Primo argomento, che deve essere `true`.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: Boolean 2<br /><br /> **Descrizione**: obbligatoria. Il secondo argomento deve essere `true`.|  
|oppure|Restituisce true se uno dei parametri è true. Entrambi gli argomenti devono essere valori booleani. L'espressione seguente restituisce `true`: `or(greater(1,10),equals(0,0))`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Boolean 1<br /><br /> **Descrizione**: obbligatoria. Primo argomento, che può essere `true`.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: Boolean 2<br /><br /> **Descrizione**: obbligatoria. Il secondo argomento può essere `true`.|  
|not|Restituisce true se il parametro è `false`. Entrambi gli argomenti devono essere valori booleani. L'espressione seguente restituisce `true`: `not(contains('200 Success','Fail'))`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Boolean<br /><br /> **Descrizione**: restituisce true se il parametro è `false`. Entrambi gli argomenti devono essere valori booleani. L'espressione seguente restituisce `true`: `not(contains('200 Success','Fail'))`|  
|if|Restituisce un valore specificato in base al fatto che l'espressione abbia restituito risultati in `true` o `false`.  Ad esempio, il codice seguente restituisce `"yes"`: `if(equals(1, 1), 'yes', 'no')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Expression<br /><br /> **Descrizione**: obbligatoria. Valore booleano che determina il valore che viene restituito dall'espressione.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: True<br /><br /> **Descrizione**: obbligatoria. Valore da restituire se l'espressione è `true`.<br /><br /> **Numero di parametro**: 3<br /><br /> **Nome**: False<br /><br /> **Descrizione**: obbligatoria. Valore da restituire se l'espressione è `false`.|  
  
## <a name="conversion-functions"></a>Funzioni di conversione  
 Queste funzioni vengono usate per la conversione tra ogni tipo nativo nel linguaggio:  
  
-   stringa  
  
-   numero intero  
  
-   float  
  
-   boolean  
  
-   matrici  
  
-   dizionari  
  
|Nome della funzione|DESCRIZIONE|  
|-------------------|-----------------|  
|int|Converte il parametro in un valore intero. Ad esempio, l'espressione seguente restituisce 100 sotto forma di numero, invece di una stringa: `int('100')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Value<br /><br /> **Descrizione**: obbligatoria. Valore convertito in un valore intero.|  
|stringa|Converte il parametro in una stringa. Ad esempio, l'espressione seguente restituisce `'10'`: `string(10)`. È anche possibile convertire un oggetto in una stringa, ad esempio se il parametro **foo** è un oggetto con una sola proprietà `bar : baz`, l'espressione seguente restituisce `{"bar" : "baz"}`.`string(pipeline().parameters.foo)`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Value<br /><br /> **Descrizione**: obbligatoria. Valore convertito in una stringa.|  
|json|Converte il parametro in un valore di tipo JSON. È il contrario di string(). Ad esempio, l'espressione seguente restituisce `[1,2,3]` come matrice, anziché come stringa:<br /><br /> `parse('[1,2,3]')`<br /><br /> È analogamente possibile convertire una stringa in un oggetto. Ad esempio, `json('{"bar" : "baz"}')` restituisce:<br /><br /> `{ "bar" : "baz" }`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrizione**: obbligatoria. Stringa convertita in un valore di tipo nativo.<br /><br /> La funzione JSON supporta anche l'input XML. Ad esempio, il valore del parametro di:<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> viene convertito nel codice JSON seguente:<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|float|Converte l'argomento del parametro in un numero a virgola mobile. Ad esempio, l'espressione seguente restituisce `10.333`: `float('10.333')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Value<br /><br /> **Descrizione**: obbligatoria. Valore convertito in un numero a virgola mobile.|  
|bool|Converte il parametro in un valore booleano. Ad esempio, l'espressione seguente restituisce `false`: `bool(0)`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Value<br /><br /> **Descrizione**: obbligatoria. Valore convertito in un valore booleano.|  
|coalesce|Restituisce il primo oggetto non Null negli argomenti passati. Nota: una stringa vuota non è Null. Se ad esempio i parametri 1 e 2 non sono definiti, questa funzione restituisce `fallback`: `coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **Numero parametro**: 1 ... *n*<br /><br /> **Nome**: Object*n*<br /><br /> **Descrizione**: obbligatoria. Oggetto da controllare per rilevare valori `null`.|  
|base64|Restituisce la rappresentazione base64 della stringa di input. Ad esempio, l'espressione seguente restituisce `c29tZSBzdHJpbmc=`: `base64('some string')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: String 1<br /><br /> **Descrizione**: obbligatoria. Stringa da codificare nella rappresentazione con codifica Base64.|  
|base64ToBinary|Restituisce una rappresentazione binaria di una stringa con codifica Base64. Ad esempio, l'espressione seguente restituisce la rappresentazione binaria di una stringa: `base64ToBinary('c29tZSBzdHJpbmc=')`.<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrizione**: obbligatoria. Stringa con codifica Base64.|  
|base64ToString|Restituisce una rappresentazione di stringa di una stringa con codifica Based64. Ad esempio, l'espressione seguente restituisce una stringa: `base64ToString('c29tZSBzdHJpbmc=')`.<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrizione**: obbligatoria. Stringa con codifica Base64.|  
|Binary|Restituisce una rappresentazione binaria di un valore.  Ad esempio, l'espressione seguente restituisce la rappresentazione binaria di una stringa: `binary(‘some string’).`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Value<br /><br /> **Descrizione**: obbligatoria. Valore convertito in un valore binario.|  
|dataUriToBinary|Restituisce una rappresentazione binaria di un URI di dati. Ad esempio, l'espressione seguente restituisce la rappresentazione binaria di una stringa: `dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrizione**: obbligatoria. URI di dati da convertire in una rappresentazione binaria.|  
|dataUriToString|Restituisce una rappresentazione di stringa di un URI di dati. Ad esempio, l'espressione seguente restituisce una stringa: `dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: String<br /><br />**Descrizione**: obbligatoria. URI di dati da convertire in una rappresentazione di tipo stringa.|  
|dataUri|Restituisce un URI di dati di un valore. Ad esempio, l'espressione seguente restituisce dati: `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **Numero di parametro**: 1<br /><br />**Nome**: Value<br /><br />**Descrizione**: obbligatoria. Valore da convertire in URI di dati.|  
|decodeBase64|Restituisce una rappresentazione di stringa di una stringa di input con codifica Based64. Ad esempio, l'espressione seguente restituisce `some string`: `decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrizione**: restituisce una rappresentazione di stringa di una stringa di input con codifica Based64.|  
|encodeUriComponent|Aggiunge un URL come escape alla stringa passata. Ad esempio, l'espressione seguente restituisce `You+Are%3ACool%2FAwesome`: `encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrizione**: obbligatoria. Stringa a cui aggiungere caratteri di escape per i caratteri non sicuri per gli URL.|  
|decodeUriComponent|Rimuove i caratteri di escape per l'URL nella stringa passata. Ad esempio, l'espressione seguente restituisce `You Are:Cool/Awesome`: `encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrizione**: obbligatoria. Stringa da cui decodificare i caratteri non sicuri per gli URL.|  
|decodeDataUri|Restituisce una rappresentazione binaria di una stringa di URI di dati di input. Ad esempio, l'espressione seguente restituisce la rappresentazione binaria di `some string`: `decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrizione**: obbligatoria. Valore dataURI da decodificare in una rappresentazione binaria.|  
|uriComponent|Restituisce una rappresentazione con codifica URI di un valore. Ad esempio, l'espressione seguente restituisce `You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')`<br /><br /> Dettagli del parametro: numero: 1, nome: String, descrizione: obbligatorio. Stringa a cui applicare la codifica URI.|  
|uriComponentToBinary|Restituisce una rappresentazione binaria di una stringa con codifica URI. Ad esempio, l'espressione seguente restituisce la rappresentazione binaria di `You Are:Cool/Awesome`: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: String<br /><br />**Descrizione**: obbligatoria. Stringa con codifica URI.|  
|uriComponentToString|Restituisce una rappresentazione di stringa di una stringa con codifica URI. Ad esempio, l'espressione seguente restituisce `You Are:Cool/Awesome`: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Numero di parametro**: 1<br /><br />**Nome**: String<br /><br />**Descrizione**: obbligatoria. Stringa con codifica URI.|  
|xml|Restituisce una rappresentazione XML del valore. Ad esempio, l'espressione seguente restituisce un contenuto XML rappresentato da `'\<name>Alan\</name>'`: `xml('\<name>Alan\</name>')`. La funzione XML supporta anche l'input di tipo oggetto JSON. Ad esempio, il parametro `{ "abc": "xyz" }` viene convertito in contenuto XML `\<abc>xyz\</abc>`<br /><br /> **Numero di parametro**: 1<br /><br />**Nome**: Value<br /><br />**Descrizione**: obbligatoria. Valore da convertire in XML.|  
|xpath|Restituisce una matrice di nodi XML corrispondenti all'espressione xpath di un valore restituito dall'espressione xpath.<br /><br />  **Esempio 1**<br /><br /> Si consideri che il valore del parametro ‘p1’ sia una rappresentazione di stringa di questo codice XML:<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. Questo codice: `xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> restituisce<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> mentre<br /><br /> 2. Questo codice: `xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> restituisce<br /><br /> `13`<br /><br /> <br /><br /> **Esempio 2**<br /><br /> Se è disponibile il contenuto XML seguente:<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1.  Questo codice: `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> oppure<br /><br /> 2. Questo codice: `@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> Restituisce<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> e<br /><br /> 3. Questo codice: `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> Restituisce<br /><br /> ``bar``<br /><br /> **Numero di parametro**: 1<br /><br />**Nome**: Xml<br /><br />**Descrizione**: obbligatoria. Codice XML in base al quale valutare l'espressione XPath.<br /><br /> **Numero di parametro**: 2<br /><br />**Nome**: XPath<br /><br />**Descrizione**: obbligatoria. Espressione XPath da valutare.|  
|array|Converte il parametro in una matrice.  Ad esempio, l'espressione seguente restituisce `["abc"]`: `array('abc')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Value<br /><br /> **Descrizione**: obbligatoria. Valore convertito in una matrice.|
|createArray|Crea una matrice dai parametri.  Ad esempio, l'espressione seguente restituisce `["a", "c"]`: `createArray('a', 'c')`<br /><br /> **Numero di parametro**: 1 ... n<br /><br /> **Nome**: Any n<br /><br /> **Descrizione**: obbligatoria. Valori da combinare in una matrice.|

## <a name="math-functions"></a>Funzioni matematiche  
 Queste funzioni possono essere usate per qualsiasi tipo di numero, ovvero **numeri interi** e **numeri a virgola mobile**.  
  
|Nome della funzione|DESCRIZIONE|  
|-------------------|-----------------|  
|add|Restituisce il risultato della somma dei due numeri. Ad esempio, questa funzione restituisce `20.333`: `add(10,10.333)`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Summand 1<br /><br /> **Descrizione**: obbligatoria. Numero da aggiungere a **Summand 2**.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: Summand 2<br /><br /> **Descrizione**: obbligatoria. Numero da aggiungere a **Summand 1**.|  
|sub|Restituisce il risultato della differenza dei due numeri. Ad esempio, questa funzione restituisce `-0.333`:<br /><br /> `sub(10,10.333)`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Minuend<br /><br /> **Descrizione**: obbligatoria. Numero da cui viene rimosso **Subtrahend**.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: Subtrahend<br /><br /> **Descrizione**: obbligatoria. Numero da rimuovere da **Minuend**.|  
|mul|Restituisce il risultato del prodotto dei due numeri. Ad esempio, il codice seguente restituisce `103.33`:<br /><br /> `mul(10,10.333)`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Multiplicand 1<br /><br /> **Descrizione**: obbligatoria. Numero per cui moltiplicare **Multiplicand 2**.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: Multiplicand 2<br /><br /> **Descrizione**: obbligatoria. Numero per cui moltiplicare **Multiplicand 1**.|  
|div|Restituisce il risultato della divisione dei due numeri. Ad esempio, il codice seguente restituisce `1.0333`:<br /><br /> `div(10.333,10)`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Dividend<br /><br /> **Descrizione**: obbligatoria. Numero da dividere per il **Divisor**.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: Divisor<br /><br /> **Descrizione**: obbligatoria. Numero da dividere per **Dividend**.|  
|mod|Restituisce il risultato del resto dopo la divisione dei due numeri (modulo). Ad esempio, l'espressione seguente restituisce `2`:<br /><br /> `mod(10,4)`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Dividend<br /><br /> **Descrizione**: obbligatoria. Numero da dividere per il **Divisor**.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: Divisor<br /><br /> **Descrizione**: obbligatoria. Numero da dividere per **Dividend**. Dopo la divisione, viene calcolato il resto.|  
|Min|Sono disponibili due modelli diversi per chiamare questa funzione: `min([0,1,2])`. Qui min accetta una matrice. Questa espressione restituisce `0`. In alternativa, questa funzione può accettare un elenco di valori delimitato da virgole: `min(0,1,2)`. Anche questa funzione restituisce 0. Nota: tutti i valori devono essere numeri, quindi se il parametro è una matrice, la matrice deve includere solo numeri.<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Collection o Value<br /><br /> **Descrizione**: obbligatoria. Può essere una matrice di valori di cui trovare il valore minimo oppure il primo valore di un set.<br /><br /> **Numero di parametro**: 2 ... *n*<br /><br /> **Nome**: Value *n*<br /><br /> **Descrizione**: facoltativa. Se il primo parametro è di tipo Value, è possibile passare valori aggiuntivi e viene restituito il valore minimo tra tutti i valori passati.|  
|max|Sono disponibili due modelli diversi per chiamare questa funzione: `max([0,1,2])`<br /><br /> Qui max accetta una matrice. Questa espressione restituisce `2`. In alternativa, questa funzione può accettare un elenco di valori delimitato da virgole: `max(0,1,2)`. Questa funzione restituisce 2. Nota: tutti i valori devono essere numeri, quindi se il parametro è una matrice, la matrice deve includere solo numeri.<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Collection o Value<br /><br /> **Descrizione**: obbligatoria. Può essere una matrice di valori di cui trovare il valore massimo oppure il primo valore di un set.<br /><br /> **Numero di parametro**: 2 ... *n*<br /><br /> **Nome**: Value *n*<br /><br /> **Descrizione**: facoltativa. Se il primo parametro è di tipo Value, è possibile passare valori aggiuntivi e viene restituito il valore massimo tra tutti i valori passati.|  
|range| Genera una matrice di interi a partire da un determinato numero. La lunghezza della matrice restituita viene definita dall'utente. Ad esempio, questa funzione restituisce `[3,4,5,6]`:<br /><br /> `range(3,4)`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: StartIndex<br /><br /> **Descrizione**: obbligatoria. È il primo numero intero nella matrice.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: Count<br /><br /> **Descrizione**: obbligatoria. Numero di valori interi della matrice.|  
|rand| Genera un numero intero casuale entro l'intervallo specificato, estremi compresi. Ad esempio, potrebbe restituire `42`:<br /><br /> `rand(-1000,1000)`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Minimum<br /><br /> **Descrizione**: obbligatoria. Numero intero più basso che può essere restituito.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: Maximum<br /><br /> **Descrizione**: obbligatoria. Numero intero più alto che può essere restituito.|  
  
## <a name="date-functions"></a>Funzioni di data  
  
|Nome della funzione|DESCRIZIONE|  
|-------------------|-----------------|  
|utcnow|Restituisce il timestamp corrente come stringa. Ad esempio, `2015-03-15T13:27:36Z`:<br /><br /> `utcnow()`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Format<br /><br /> **Descrizione**: facoltativa. [Singolo carattere di identificatore formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o [modello di formato personalizzato](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) che indica come formattare il valore di questo timestamp. Se il formato non viene specificato, viene usato il formato ISO 8601 ("o").|  
|addseconds|Aggiunge un numero intero di secondi a un timestamp di stringa passato. Il numero di secondi può essere positivo o negativo. Per impostazione predefinita, il risultato è una stringa in formato ISO 8601 ("o"), a meno che non venga fornito un identificatore di formato. Ad esempio, `2015-03-15T13:27:00Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrizione**: obbligatoria. Stringa contenente l'ora.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: Seconds<br /><br /> **Descrizione**: obbligatoria. Numero di secondi da aggiungere. Può essere negativo per sottrarre secondi.<br /><br /> **Numero di parametro**: 3<br /><br /> **Nome**: Format<br /><br /> **Descrizione**: facoltativa. [Singolo carattere di identificatore formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o [modello di formato personalizzato](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) che indica come formattare il valore di questo timestamp. Se il formato non viene specificato, viene usato il formato ISO 8601 ("o").|  
|addminutes|Aggiunge un numero intero di minuti a un timestamp di stringa passato. Il numero di minuti può essere positivo o negativo. Per impostazione predefinita, il risultato è una stringa in formato ISO 8601 ("o"), a meno che non venga fornito un identificatore di formato. Ad esempio, `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrizione**: obbligatoria. Stringa contenente l'ora.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: Minutes<br /><br /> **Descrizione**: obbligatoria. Numero di minuti da aggiungere. Può essere negativo per sottrarre minuti.<br /><br /> **Numero di parametro**: 3<br /><br /> **Nome**: Format<br /><br /> **Descrizione**: facoltativa. [Singolo carattere di identificatore formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o [modello di formato personalizzato](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) che indica come formattare il valore di questo timestamp. Se il formato non viene specificato, viene usato il formato ISO 8601 ("o").|  
|addhours|Aggiunge un numero intero di ore a un timestamp di stringa passato. Il numero di ore può essere positivo o negativo. Per impostazione predefinita, il risultato è una stringa in formato ISO 8601 ("o"), a meno che non venga fornito un identificatore di formato. Ad esempio, `2015-03-16T01:27:36Z`:<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrizione**: obbligatoria. Stringa contenente l'ora.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: Hours<br /><br /> **Descrizione**: obbligatoria. Numero di ore da aggiungere. Può essere negativo per sottrarre ore.<br /><br /> **Numero di parametro**: 3<br /><br /> **Nome**: Format<br /><br /> **Descrizione**: facoltativa. [Singolo carattere di identificatore formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o [modello di formato personalizzato](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) che indica come formattare il valore di questo timestamp. Se il formato non viene specificato, viene usato il formato ISO 8601 ("o").|  
|adddays|Aggiunge un numero intero di giorni a un timestamp di stringa passato. Il numero di giorni può essere positivo o negativo. Per impostazione predefinita, il risultato è una stringa in formato ISO 8601 ("o"), a meno che non venga fornito un identificatore di formato. Ad esempio, `2015-02-23T13:27:36Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -20)`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrizione**: obbligatoria. Stringa contenente l'ora.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: Days<br /><br /> **Descrizione**: obbligatoria. Numero di giorni da aggiungere. Può essere negativo per sottrarre giorni.<br /><br /> **Numero di parametro**: 3<br /><br /> **Nome**: Format<br /><br /> **Descrizione**: facoltativa. [Singolo carattere di identificatore formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o [modello di formato personalizzato](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) che indica come formattare il valore di questo timestamp. Se il formato non viene specificato, viene usato il formato ISO 8601 ("o").|  
|formatDateTime|Restituisce una stringa con formato data. Per impostazione predefinita, il risultato è una stringa in formato ISO 8601 ("o"), a meno che non venga fornito un identificatore di formato. Ad esempio, `2015-02-23T13:27:36Z`:<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Date<br /><br /> **Descrizione**: obbligatoria. Stringa contenente la data.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: Format<br /><br /> **Descrizione**: facoltativa. [Singolo carattere di identificatore formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o [modello di formato personalizzato](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) che indica come formattare il valore di questo timestamp. Se il formato non viene specificato, viene usato il formato ISO 8601 ("o").|  

## <a name="next-steps"></a>Passaggi successivi
Per un elenco di variabili di sistema che è possibile usare nelle espressioni, vedere [Variabili di sistema](control-flow-system-variables.md).