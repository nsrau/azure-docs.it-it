---
title: Schema del linguaggio di definizione del flusso di lavoro - App per la logica di Azure | Microsoft Docs
description: Definire i flussi di lavoro in base allo schema di definizione del flusso di lavoro per App per la logica di Azure
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 26c94308-aa0d-4730-97b6-de848bffff91
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/21/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 36eee42b7b10dfb62e569d665f62a94fc94365be
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="workflow-definition-language-schema-for-azure-logic-apps"></a>Schema del linguaggio di definizione del flusso di lavoro per App per la logica di Azure

Una definizione del flusso di lavoro contiene la logica effettiva che viene eseguita come parte dell'app per la logica. Questa definizione include uno o più trigger che avviano l'app per la logica e una o più azioni che devono essere eseguite dall'app per la logica.  
  
## <a name="basic-workflow-definition-structure"></a>Struttura di base della definizione del flusso di lavoro

Ecco la struttura di base di una definizione del flusso di lavoro:  
  
```json
{
    "$schema": "<schema-of the-definition>",
    "contentVersion": "<version-number-of-definition>",
    "parameters": { <parameter-definitions-of-definition> },
    "triggers": [ { <definition-of-flow-triggers> } ],
    "actions": [ { <definition-of-flow-actions> } ],
    "outputs": { <output-of-definition> }
}
```
  
> [!NOTE]
> La documentazione su [Workflow Management REST API](https://docs.microsoft.com/rest/api/logic/workflows) (API REST per la gestione dei flussi di lavoro) include informazioni su come creare e gestire i flussi di lavoro delle app per la logica.
  
|Nome dell'elemento|Obbligatorio|Descrizione|  
|------------------|--------------|-----------------|  
|$schema|No|Specifica il percorso del file dello schema JSON che descrive la versione del linguaggio della definizione. Questo percorso è obbligatorio quando si fa riferimento esternamente a una definizione. Per questo documento il percorso è: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#`|  
|contentVersion|No|Specifica la versione della definizione. Quando si distribuisce un flusso di lavoro tramite la definizione, è possibile usare questo valore per assicurarsi che venga usata la definizione corretta.|  
|parameters|No|Specifica i parametri usati per inserire dati nella definizione. È possibile definire un massimo di 50 parametri.|  
|trigger|No|Specifica le informazioni per i trigger che avviano il flusso di lavoro. È possibile definire un massimo di 10 trigger.|  
|Azioni|No|Specifica le azioni effettuate durante l'esecuzione del flusso. È possibile definire un massimo di 250 azioni.|  
|outputs|No|Specifica le informazioni sulla risorsa distribuita. È possibile definire un massimo di 10 output.|  
  
## <a name="parameters"></a>Parametri

Questa sezione specifica tutti i parametri che vengono usati nella definizione del flusso di lavoro in fase di distribuzione. Tutti i parametri devono essere dichiarati in questa sezione prima di potere essere usati in altre sezioni della definizione.  
  
L'esempio seguente illustra la struttura di una definizione di parametro:  

```json
"parameters": {
    "<parameter-name>" : {
        "type" : "<type-of-parameter-value>",
        "defaultValue": <default-value-of-parameter>,
        "allowedValues": [ <array-of-allowed-values> ],
        "metadata" : { "key": { "name": "value"} }
    }
}
```

|Nome dell'elemento|Obbligatorio|Descrizione|  
|------------------|--------------|-----------------|  
|type|Sì|**Tipo**: string <p> **Dichiarazione**: `"parameters": {"parameter1": {"type": "string"}` <p> **Specifiche**: `"parameters": {"parameter1": {"value": "myparamvalue1"}}` <p> **Tipo**: securestring <p> **Dichiarazione**: `"parameters": {"parameter1": {"type": "securestring"}}` <p> **Specifiche**: `"parameters": {"parameter1": {"value": "myparamvalue1"}}` <p> **Tipo**: int <p> **Dichiarazione**: `"parameters": {"parameter1": {"type": "int"}}` <p> **Specifiche**: `"parameters": {"parameter1": {"value" : 5}}` <p> **Tipo**: bool <p> **Dichiarazione**: `"parameters": {"parameter1": {"type": "bool"}}` <p> **Specifiche**: `"parameters": {"parameter1": { "value": true }}` <p> **Tipo**: array <p> **Dichiarazione**: `"parameters": {"parameter1": {"type": "array"}}` <p> **Specifiche**: `"parameters": {"parameter1": { "value": [ array-of-values ]}}` <p> **Tipo**: object <p> **Dichiarazione**: `"parameters": {"parameter1": {"type": "object"}}` <p> **Specifiche**: `"parameters": {"parameter1": { "value": { JSON-object } }}` <p> **Tipo**: secureobject <p> **Dichiarazione**: `"parameters": {"parameter1": {"type": "object"}}` <p> **Specifiche**: `"parameters": {"parameter1": { "value": { JSON-object } }}` <p> **Nota:** i tipi `securestring` e `secureobject` non vengono restituiti nelle operazioni `GET`. Tutte le password, le chiavi e i segreti devono usare questo tipo.|  
|defaultValue|No|Specifica il valore predefinito per il parametro quando non viene specificato alcun valore durante la creazione della risorsa.|  
|allowedValues|No|Specifica una matrice di valori consentiti per il parametro.|  
|metadata|No|Specifica informazioni aggiuntive sul parametro, ad esempio una descrizione leggibile o dati relativi alla fase di progettazione usati da Visual Studio o da altri strumenti.|  
  
Questo esempio mostra come usare un parametro nella sezione del corpo di un'azione:  
  
```json
"body" :
{
  "property1": "@parameters('parameter1')"
}
```

 I parametri possono essere usati anche negli output.  
  
## <a name="triggers-and-actions"></a>Trigger e azioni  

I trigger e le azioni specificano le chiamate che possono partecipare all'esecuzione del flusso di lavoro. Per informazioni dettagliate su questa sezione, vedere [Workflow Actions and Triggers](logic-apps-workflow-actions-triggers.md) (Azioni e trigger dei flussi di lavoro).
  
## <a name="outputs"></a>outputs  

Gli output specificano le informazioni che possono essere restituite da un'esecuzione del flusso di lavoro. Se ad esempio si vuole tenere traccia di uno stato o di un valore specifico per ogni esecuzione, è possibile includere tali dati negli output dell'esecuzione. I dati vengono visualizzati nell'API REST di gestione per tale esecuzione e nell'interfaccia utente di gestione per l'esecuzione nel portale di Azure. È anche possibile passare questi output ad altri sistemi esterni come PowerBI per la creazione di dashboard. Gli output *non* vengono usati per rispondere a richieste in ingresso nell'API REST del servizio. Per rispondere a una richiesta in ingresso usando il tipo di azione `response`, ecco un esempio:
  
```json
"outputs": {  
  "key1": {  
    "value": "value1",  
    "type" : "<type-of-value>"  
  }  
} 
```

|Nome dell'elemento|Obbligatorio|Descrizione|  
|------------------|--------------|-----------------|  
|key1|Sì|Specifica l'identificatore chiave per l'output. Sostituire **key1** con un nome da usare per identificare l'output.|  
|value|Sì|Specifica il valore dell'output.|  
|type|Sì|Specifica il tipo per il valore specificato. Ecco alcuni tipi possibili di valori: <ul><li>`string`</li><li>`securestring`</li><li>`int`</li><li>`bool`</li><li>`array`</li><li>`object`</li></ul>|
  
## <a name="expressions"></a>Espressioni  

I valori JSON nella definizione possono essere letterali o possono essere espressioni che vengono valutate quando viene usata la definizione. Ad esempio:  
  
```json
"name": "value"
```

 oppure  
  
```json
"name": "@parameters('password') "
```

> [!NOTE]
> Alcune espressioni ottengono i rispettivi valori dalle azioni di runtime che potrebbero non esistere all'inizio dell'esecuzione. È possibile usare le **funzioni** per semplificare il recupero di alcuni di questi valori.  
  
Le espressioni possono trovarsi in qualsiasi punto in un valore stringa JSON e restituiscono sempre un altro valore JSON. Quando un valore JSON è stato identificato come espressione, il corpo dell'espressione viene estratto rimuovendo il simbolo di chiocciola (@). Se è necessaria una stringa letterale che inizia con @, occorre che la stringa sia preceduta da un carattere di escape con @@. L'esempio seguente illustra la modalità di valutazione delle espressioni.  
  
|Valore JSON|Risultato|  
|----------------|------------|  
|"parameters"|Vengono restituiti i caratteri di tipo 'parameters'.|  
|"parameters[1]"|Vengono restituiti i caratteri di tipo 'parameters[1]'.|  
|"@@"|Viene restituita una stringa da 1 carattere che contiene '@'.|  
|" @"|Viene restituita una stringa da 2 caratteri che contiene ' @'.|  
  
Con l'*interpolazione della stringa* è possibile inserire le espressioni anche all'interno delle stringhe in cui viene eseguito il wrapping delle espressioni in `@{ ... }`. Ad esempio: <p>`"name" : "First Name: @{parameters('firstName')} Last Name: @{parameters('lastName')}"`

Il risultato è sempre una stringa. Questa funzionalità è quindi simile alla funzione `concat`. Si supponga di avere definito `myNumber` come `42` e `myString` come `sampleString`:  
  
|Valore JSON|Risultato|  
|----------------|------------|  
|"@parameters('myString')"|Restituisce `sampleString` come stringa.|  
|"@{parameters('myString')}"|Restituisce `sampleString` come stringa.|  
|"@parameters('myNumber')"|Restituisce `42` come *numero*.|  
|"@{parameters('myNumber')}"|Restituisce `42` come *stringa*.|  
|"Answer is: @{parameters('myNumber')}"|Restituisce la stringa `Answer is: 42`.|  
|"@concat('Answer is: ', string(parameters('myNumber')))"|Restituisce la stringa `Answer is: 42`.|  
|"Answer is: @@{parameters('myNumber')}"|Restituisce la stringa `Answer is: @{parameters('myNumber')}`.|  
  
## <a name="operators"></a>Operatori  

Gli operatori sono i caratteri che possono essere usati all'interno delle espressioni o delle funzioni. 
  
|operatore|Descrizione|  
|--------------|-----------------|  
|.|L'operatore punto consente di fare riferimento alle proprietà di un oggetto.|  
|?|L'operatore punto interrogativo consente di fare riferimento alle proprietà Null di un oggetto senza un errore di runtime. È ad esempio possibile usare questa espressione per gestire gli output di trigger Null: <p>`@coalesce(trigger().outputs?.body?.property1, 'my default value')`|  
|'|La virgoletta singola è l'unico modo per eseguire il wrapping dei valori letterali di stringa. Non è possibile usare le virgolette doppie all'interno delle espressioni perché questo segno di punteggiatura è in conflitto con le virgolette JSON che eseguono il wrapping dell'intera espressione.|  
|[]|Le parentesi quadre possono essere usate per ottenere un valore da una matrice con un indice specifico. Se ad esempio è presente un'azione che passa `range(0,10)` alla funzione `forEach`, è possibile usare questa funzione per ottenere gli elementi dalle matrici:  <p>`myArray[item()]`|  
  
## <a name="functions"></a>Funzioni  

È anche possibile chiamare le funzioni all'interno delle espressioni. La tabella seguente mostra le funzioni che possono essere usate in un'espressione.  
  
|Expression|Versione di valutazione|  
|----------------|----------------|  
|"@function('Hello')"|Chiama il membro funzione della definizione con la stringa letterale Hello come primo parametro.|  
|"@function('It''s Cool!')"|Chiama il membro funzione della definizione con la stringa letterale 'It's Cool!' come primo parametro.|  
|"@function().prop1"|Restituisce il valore della proprietà prop1 del membro `myfunction` della definizione.|  
|"@function('Hello').prop1"|Chiama il membro funzione della definizione con la stringa letterale 'Hello' come primo parametro e restituisce la proprietà prop1 dell'oggetto.|  
|"@function(parameters('Hello'))"|Valuta il parametro Hello e passa il valore alla funzione.|  
  
### <a name="referencing-functions"></a>Funzioni di riferimento  

È possibile usare queste funzioni per fare riferimento agli output da altre azioni nell'app per la logica o a valori passati durante la creazione dell'app per la logica. È ad esempio possibile fare riferimento ai dati di un passaggio per usarli in un altro.  
  
|Nome della funzione|Descrizione|  
|-------------------|-----------------|  
|parameters|Restituisce un valore di parametro definito nella definizione. <p>`parameters('password')` <p> **Numero di parametro**: 1 <p> **Nome**: Parameter <p> **Descrizione**: obbligatoria. Nome del parametro di cui si vogliono ottenere i valori.|  
|action|Consente a un'espressione di derivare il rispettivo valore da altre coppie nome/valore JSON o dall'output dell'azione di runtime corrente. La proprietà rappresentata da propertyPath nell'esempio seguente è facoltativa. Se il valore propertyPath non è specificato, il riferimento è relativo all'intero oggetto action. Questa funzione può essere usata solo all'interno delle condizioni do-until di un'azione. <p>`action().outputs.body.propertyPath`|  
|Azioni|Consente a un'espressione di derivare il rispettivo valore da altre coppie nome/valore JSON o dall'output dell'azione di runtime. Queste espressioni dichiarano esplicitamente che un'azione dipende da un'altra azione. La proprietà rappresentata da propertyPath nell'esempio seguente è facoltativa. Se il valore propertyPath non è specificato, il riferimento è relativo all'intero oggetto action. È possibile usare questo elemento o l'elemento conditions per specificare le dipendenze, ma non è necessario usare entrambi per la stessa risorsa dipendente. <p>`actions('myAction').outputs.body.propertyPath` <p> **Numero di parametro**: 1 <p> **Nome**: ActionName <p> **Descrizione**: obbligatoria. Nome dell'azione di cui si vogliono ottenere i valori. <p> Le proprietà disponibili per l'oggetto azione sono: <ul><li>`name`</li><li>`startTime`</li><li>`endTime`</li><li>`inputs`</li><li>`outputs`</li><li>`status`</li><li>`code`</li><li>`trackingId`</li><li>`clientTrackingId`</li></ul> <p>Per informazioni dettagliate su queste proprietà, vedere [Rest API](http://go.microsoft.com/fwlink/p/?LinkID=850646) (API REST).|
|trigger|Consente a un'espressione di derivare il rispettivo valore da altre coppie nome/valore JSON o dall'output del trigger di runtime. La proprietà rappresentata da propertyPath nell'esempio seguente è facoltativa. Se il valore propertyPath non è specificato, il riferimento è relativo all'intero oggetto trigger. <p>`trigger().outputs.body.propertyPath` <p>Quando viene usata all'interno degli input di un trigger, la funzione restituisce gli output dell'esecuzione precedente. Quando viene usata all'interno della condizione di un trigger, tuttavia, la funzione `trigger` restituisce gli output dell'esecuzione corrente. <p> Le proprietà disponibili per l'oggetto trigger sono: <ul><li>`name`</li><li>`scheduledTime`</li><li>`startTime`</li><li>`endTime`</li><li>`inputs`</li><li>`outputs`</li><li>`status`</li><li>`code`</li><li>`trackingId`</li><li>`clientTrackingId`</li></ul> <p>Per informazioni dettagliate su queste proprietà, vedere [Rest API](http://go.microsoft.com/fwlink/p/?LinkID=850644) (API REST).|
|actionOutputs|Questa funzione è la sintassi abbreviata per `actions('actionName').outputs` <p> **Numero di parametro**: 1 <p> **Nome**: ActionName <p> **Descrizione**: obbligatoria. Nome dell'azione di cui si vogliono ottenere i valori.|  
|actionBody e body|Queste funzioni sono la sintassi abbreviata per `actions('actionName').outputs.body` <p> **Numero di parametro**: 1 <p> **Nome**: ActionName <p> **Descrizione**: obbligatoria. Nome dell'azione di cui si vogliono ottenere i valori.|  
|triggerOutputs|Questa funzione è la sintassi abbreviata per `trigger().outputs`|  
|triggerBody|Questa funzione è la sintassi abbreviata per `trigger().outputs.body`|  
|item|Quando viene usata all'interno di un'azione ripetuta, questa funzione restituisce l'elemento che si trova nella matrice per questa iterazione dell'azione. Se ad esempio è presente un'azione che viene eseguita per ogni elemento in una matrice di messaggi, è possibile usare questa sintassi: <p>`"input1" : "@item().subject"`| 
  
### <a name="collection-functions"></a>Funzioni di raccolta  

Queste funzioni operano sulle raccolte e in genere sono applicabili a matrici, stringhe e dizionari.  
  
|Nome della funzione|Descrizione|  
|-------------------|-----------------|  
|contains|Restituisce true se un dizionario contiene una chiave, se un elenco contiene un valore o se una stringa contiene una sottostringa. Ad esempio, questa funzione restituisce `true`: <p>`contains('abacaba','aca')` <p> **Numero di parametro**: 1 <p> **Nome**: WithinCollection <p> **Descrizione**: obbligatoria. Raccolta in cui eseguire la ricerca. <p> **Numero di parametro**: 2 <p> **Nome**: FindObject <p> **Descrizione**: obbligatoria. Oggetto da trovare all'interno di **WithinCollection**.|  
|length|Restituisce il numero di elementi in una matrice o in una stringa. Ad esempio, questa funzione restituisce `3`:  <p>`length('abc')` <p> **Numero di parametro**: 1 <p> **Nome**: Collection <p> **Descrizione**: obbligatoria. Raccolta per cui ottenere la lunghezza.|  
|empty|Restituisce true se la matrice, la stringa o l'oggetto è vuoto. Ad esempio, questa funzione restituisce `true`: <p>`empty('')` <p> **Numero di parametro**: 1 <p> **Nome**: Collection <p> **Descrizione**: obbligatoria. Raccolta da verificare se l'elemento è vuoto.|  
|intersezione|Restituisce una singola matrice o un singolo oggetto con elementi comuni tra le matrici o gli oggetti passati. Ad esempio, questa funzione restituisce `[1, 2]`: <p>`intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])` <p>I parametri per la funzione possono essere un set di oggetti o un set di matrici, non una combinazione di entrambi. Se sono presenti due oggetti con lo stesso nome, l'ultimo oggetto con tale nome viene visualizzato nell'oggetto finale. <p> **Numero parametro**: 1 ... *n* <p> **Nome**: Collection *n* <p> **Descrizione**: obbligatoria. Raccolte da valutare. Un oggetto deve essere presente in tutte le raccolte passate per essere visualizzato nel risultato.|  
|union|Restituisce una singola matrice o un singolo oggetto con tutti gli elementi che si trovano nella matrice o nell'oggetto passato a questa funzione. Ad esempio, questa funzione restituisce `[1, 2, 3, 10, 101]`: <p>`union([1, 2, 3], [101, 2, 1, 10])` <p>I parametri per la funzione possono essere un set di oggetti o un set di matrici, non una combinazione di entrambi. Se sono presenti due oggetti con lo stesso nome nell'output finale, l'ultimo oggetto con tale nome viene visualizzato nell'oggetto finale. <p> **Numero parametro**: 1 ... *n* <p> **Nome**: Collection *n* <p> **Descrizione**: obbligatoria. Raccolte da valutare. Un oggetto che viene visualizzato in una delle raccolte viene visualizzato anche nel risultato.|  
|first|Restituisce il primo elemento nella matrice o nella stringa passata. Ad esempio, questa funzione restituisce `0`: <p>`first([0,2,3])` <p> **Numero di parametro**: 1 <p> **Nome**: Collection <p> **Descrizione**: obbligatoria. Raccolta da cui ottenere il primo oggetto.|  
|last|Restituisce l'ultimo elemento nella matrice o nella stringa passata. Ad esempio, questa funzione restituisce `3`: <p>`last('0123')` <p> **Numero di parametro**: 1 <p> **Nome**: Collection <p> **Descrizione**: obbligatoria. Raccolta da cui ottenere l'ultimo oggetto.|  
|take|Restituisce i primi elementi **Count** dalla matrice o dalla stringa passata. Ad esempio, questa funzione restituisce `[1, 2]`:  <p>`take([1, 2, 3, 4], 2)` <p> **Numero di parametro**: 1 <p> **Nome**: Collection <p> **Descrizione**: obbligatoria. Raccolta da cui ottenere i primi oggetti **Count**. <p> **Numero di parametro**: 2 <p> **Nome**: Count <p> **Descrizione**: obbligatoria. Numero di oggetti da ottenere da **Collection**. Deve essere un intero positivo.|  
|skip|Restituisce gli elementi nella matrice a partire dall'indice **Count**. Ad esempio, questa funzione restituisce `[3, 4]`: <p>`skip([1, 2 ,3 ,4], 2)` <p> **Numero di parametro**: 1 <p> **Nome**: Collection <p> **Descrizione**: obbligatoria. Raccolta di cui ignorare i primi oggetti **Count**. <p> **Numero di parametro**: 2 <p> **Nome**: Count <p> **Descrizione**: obbligatoria. Numero di oggetti da rimuovere dalla parte iniziale di **Collection**. Deve essere un intero positivo.|  
|join|Restituisce una stringa con ogni elemento di una matrice unito da un delimitatore. Ad esempio questo valore restituisce `"1,2,3,4"`:<br /><br /> `join([1, 2, 3, 4], ',')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Collection<br /><br /> **Descrizione**: obbligatoria. Raccolta di cui unire elementi.<br /><br /> **Numero di parametro**: 2<br /><br /> **Nome**: Delimiter<br /><br /> **Descrizione**: obbligatoria. Stringa con cui delimitare gli elementi.|  
  
### <a name="string-functions"></a>Funzioni stringa

Le funzioni seguenti sono applicabili solo alle stringhe. È anche possibile usare alcune funzioni di raccolta sulle stringhe.  
  
|Nome della funzione|Descrizione|  
|-------------------|-----------------|  
|concat|Combina un numero qualsiasi di stringhe. Se ad esempio il parametro 1 è `p1`, questa funzione restituisce `somevalue-p1-somevalue`: <p>`concat('somevalue-',parameters('parameter1'),'-somevalue')` <p> **Numero parametro**: 1 ... *n* <p> **Nome**: String *n* <p> **Descrizione**: obbligatoria. Stringhe da combinare in una singola stringa.|  
|substring|Restituisce un sottoinsieme di caratteri da una stringa. Ad esempio, questa funzione restituisce `abc`: <p>`substring('somevalue-abc-somevalue',10,3)` <p> **Numero di parametro**: 1 <p> **Nome**: String <p> **Descrizione**: obbligatoria. Stringa da cui viene ottenuta la sottostringa. <p> **Numero di parametro**: 2 <p> **Nome**: StartIndex <p> **Descrizione**: obbligatoria. Indice da cui inizia la sottostringa nel parametro 1. <p> **Numero di parametro**: 3 <p> **Nome**: Length <p> **Descrizione**: obbligatoria. Lunghezza della sottostringa.|  
|replace|Sostituisce una stringa con una stringa specifica. Ad esempio, questa funzione restituisce `the new string`: <p>`replace('the old string', 'old', 'new')` <p> **Numero di parametro**: 1 <p> **Nome**: string <p> **Descrizione**: obbligatoria. Stringa in cui viene eseguita la ricerca del parametro 2 e che viene aggiornata con il parametro 3, quando il parametro 2 viene trovato nel parametro 1. <p> **Numero di parametro**: 2 <p> **Nome**: OldString <p> **Descrizione**: obbligatoria. Stringa da sostituire con il parametro 3 quando viene trovata una corrispondenza nel parametro 1. <p> **Numero di parametro**: 3 <p> **Nome**: NewString <p> **Descrizione**: obbligatoria. Stringa usata per sostituire la stringa nel parametro 2 quando viene trovata una corrispondenza nel parametro 1.|  
|guid|Questa funzione genera una stringa univoca globale (GUID). Ad esempio, questa funzione può generare questo GUID: `c2ecc88d-88c8-4096-912c-d6f2e2b138ce` <p>`guid()` <p> **Numero di parametro**: 1 <p> **Nome**: Format <p> **Descrizione**: facoltativa. Identificatore di formato singolo che indica [come formattare il valore di questo GUID](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). Il parametro format può essere "N", "D", "B", "P" o "X". Se il valore format non viene specificato, viene usato "D".|  
|toLower|Converte una stringa in lettere minuscole. Ad esempio, questa funzione restituisce `two by two is four`: <p>`toLower('Two by Two is Four')` <p> **Numero di parametro**: 1 <p> **Nome**: String <p> **Descrizione**: obbligatoria. Stringa da convertire in lettere minuscole. Se un carattere nella stringa non ha un equivalente minuscolo, il carattere viene incluso senza modifiche nella stringa restituita.|  
|toUpper|Converte una stringa in lettere maiuscole. Ad esempio, questa funzione restituisce `TWO BY TWO IS FOUR`: <p>`toUpper('Two by Two is Four')` <p> **Numero di parametro**: 1 <p> **Nome**: String <p> **Descrizione**: obbligatoria. Stringa da convertire in lettere maiuscole. Se un carattere nella stringa non ha un equivalente maiuscolo, il carattere viene incluso senza modifiche nella stringa restituita.|  
|indexof|Consente di trovare l'indice di un valore in una stringa senza distinzione tra maiuscole e minuscole. Ad esempio, questa funzione restituisce `7`: <p>`indexof('hello, world.', 'world')` <p> **Numero di parametro**: 1 <p> **Nome**: String <p> **Descrizione**: obbligatoria. Stringa che potrebbe contenere il valore. <p> **Numero di parametro**: 2 <p> **Nome**: String <p> **Descrizione**: obbligatoria. Valore nel cui indice si vuole eseguire la ricerca.|  
|lastindexof|Consente di trovare l'ultimo indice di un valore in una stringa senza distinzione tra maiuscole e minuscole. Ad esempio, questa funzione restituisce `3`: <p>`lastindexof('foofoo', 'foo')` <p> **Numero di parametro**: 1 <p> **Nome**: String <p> **Descrizione**: obbligatoria. Stringa che potrebbe contenere il valore. <p> **Numero di parametro**: 2 <p> **Nome**: String <p> **Descrizione**: obbligatoria. Valore nel cui indice si vuole eseguire la ricerca.|  
|startswith|Verifica se la stringa inizia con un valore senza distinzione tra maiuscole e minuscole. Ad esempio, questa funzione restituisce `true`: <p>`startswith('hello, world', 'hello')` <p> **Numero di parametro**: 1 <p> **Nome**: String <p> **Descrizione**: obbligatoria. Stringa che potrebbe contenere il valore. <p> **Numero di parametro**: 2 <p> **Nome**: String <p> **Descrizione**: obbligatoria. Valore con cui può iniziare la stringa.|  
|endswith|Verifica se la stringa finisce con un valore senza distinzione tra maiuscole e minuscole. Ad esempio, questa funzione restituisce `true`: <p>`endswith('hello, world', 'world')` <p> **Numero di parametro**: 1 <p> **Nome**: String <p> **Descrizione**: obbligatoria. Stringa che potrebbe contenere il valore. <p> **Numero di parametro**: 2 <p> **Nome**: String <p> **Descrizione**: obbligatoria. Valore con cui può finire la stringa.|  
|split|Suddivide la stringa usando un separatore. Ad esempio, questa funzione restituisce `["a", "b", "c"]`: <p>`split('a;b;c',';')` <p> **Numero di parametro**: 1 <p> **Nome**: String <p> **Descrizione**: obbligatoria. Stringa suddivisa. <p> **Numero di parametro**: 2 <p> **Nome**: String <p> **Descrizione**: obbligatoria. Separatore.|  
  
### <a name="logical-functions"></a>Funzioni logiche  

Queste funzioni sono utili all'interno delle condizioni e possono essere usate per valutare qualsiasi tipo di logica.  
  
|Nome della funzione|Descrizione|  
|-------------------|-----------------|  
|equals|Restituisce true se due valori sono uguali. Se ad esempio parameter1 è someValue, questa funzione restituisce `true`: <p>`equals(parameters('parameter1'), 'someValue')` <p> **Numero di parametro**: 1 <p> **Nome**: Object 1 <p> **Descrizione**: obbligatoria. Oggetto da confrontare a **Object 2**. <p> **Numero di parametro**: 2 <p> **Nome**: Object 2 <p> **Descrizione**: obbligatoria. Oggetto da confrontare a **Object 1**.|  
|less|Restituisce true se il primo argomento è inferiore al secondo. Si noti che i valori possono essere solo di tipo intero, a virgola mobile o stringa. Ad esempio, questa funzione restituisce `true`: <p>`less(10,100)` <p> **Numero di parametro**: 1 <p> **Nome**: Object 1 <p> **Descrizione**: obbligatoria. Oggetto da controllare per verificare se è inferiore a **Object 2**. <p> **Numero di parametro**: 2 <p> **Nome**: Object 2 <p> **Descrizione**: obbligatoria. Oggetto da controllare per verificare se è superiore a **Object 1**.|  
|lessOrEquals|Restituisce true se il primo argomento è inferiore o uguale al secondo. Si noti che i valori possono essere solo di tipo intero, a virgola mobile o stringa. Ad esempio, questa funzione restituisce `true`: <p>`lessOrEquals(10,10)` <p> **Numero di parametro**: 1 <p> **Nome**: Object 1 <p> **Descrizione**: obbligatoria. Oggetto da controllare per verificare se è inferiore o uguale a **Object 2**. <p> **Numero di parametro**: 2 <p> **Nome**: Object 2 <p> **Descrizione**: obbligatoria. Oggetto da controllare per verificare se è superiore o uguale a **Object 1**.|  
|greater|Restituisce true se il primo argomento è superiore al secondo. Si noti che i valori possono essere solo di tipo intero, a virgola mobile o stringa. Ad esempio, questa funzione restituisce `false`:  <p>`greater(10,10)` <p> **Numero di parametro**: 1 <p> **Nome**: Object 1 <p> **Descrizione**: obbligatoria. Oggetto da controllare per verificare se è superiore a **Object 2**. <p> **Numero di parametro**: 2 <p> **Nome**: Object 2 <p> **Descrizione**: obbligatoria. Oggetto da controllare per verificare se è inferiore a **Object 1**.|  
|greaterOrEquals|Restituisce true se il primo argomento è superiore o uguale al secondo. Si noti che i valori possono essere solo di tipo intero, a virgola mobile o stringa. Ad esempio, questa funzione restituisce `false`: <p>`greaterOrEquals(10,100)` <p> **Numero di parametro**: 1 <p> **Nome**: Object 1 <p> **Descrizione**: obbligatoria. Oggetto da controllare per verificare se è superiore o uguale a **Object 2**. <p> **Numero di parametro**: 2 <p> **Nome**: Object 2 <p> **Descrizione**: obbligatoria. Oggetto da controllare per verificare se è inferiore o uguale a **Object 1**.|  
|e|Restituisce true se entrambi i parametri sono true. Entrambi gli argomenti devono essere valori booleani. Ad esempio, questa funzione restituisce `false`: <p>`and(greater(1,10),equals(0,0))` <p> **Numero di parametro**: 1 <p> **Nome**: Boolean 1 <p> **Descrizione**: obbligatoria. Primo argomento, che deve essere `true`. <p> **Numero di parametro**: 2 <p> **Nome**: Boolean 2 <p> **Descrizione**: obbligatoria. Il secondo argomento deve essere `true`.|  
|oppure|Restituisce true se uno dei parametri è true. Entrambi gli argomenti devono essere valori booleani. Ad esempio, questa funzione restituisce `true`: <p>`or(greater(1,10),equals(0,0))` <p> **Numero di parametro**: 1 <p> **Nome**: Boolean 1 <p> **Descrizione**: obbligatoria. Primo argomento, che può essere `true`. <p> **Numero di parametro**: 2 <p> **Nome**: Boolean 2 <p> **Descrizione**: obbligatoria. Il secondo argomento può essere `true`.|  
|not|Restituisce true se i parametri sono `false`. Entrambi gli argomenti devono essere valori booleani. Ad esempio, questa funzione restituisce `true`: <p>`not(contains('200 Success','Fail'))` <p> **Numero di parametro**: 1 <p> **Nome**: Boolean <p> **Descrizione**: restituisce true se i parametri sono `false`. Entrambi gli argomenti devono essere valori booleani. Questa funzione restituisce `true`: `not(contains('200 Success','Fail'))`|  
|if|Restituisce un valore specificato in base al valore restituito dall'espressione, ovvero `true` o `false`.  Ad esempio, questa funzione restituisce `"yes"`: <p>`if(equals(1, 1), 'yes', 'no')` <p> **Numero di parametro**: 1 <p> **Nome**: Expression <p> **Descrizione**: obbligatoria. Valore booleano che determina il valore che deve essere restituito dall'espressione. <p> **Numero di parametro**: 2 <p> **Nome**: True <p> **Descrizione**: obbligatoria. Valore da restituire se l'espressione è `true`. <p> **Numero di parametro**: 3 <p> **Nome**: False <p> **Descrizione**: obbligatoria. Valore da restituire se l'espressione è `false`.|  
  
### <a name="conversion-functions"></a>Funzioni di conversione  

Queste funzioni vengono usate per la conversione tra ogni tipo nativo nel linguaggio:  
  
- string  
  
- numero intero  
  
- float  
  
- boolean  
  
- matrici  
  
- dizionari  

-   form  
  
|Nome della funzione|Descrizione|  
|-------------------|-----------------|  
|int|Converte il parametro in un valore intero. Ad esempio, questa funzione restituisce 100 sotto forma di numero, invece di una stringa: <p>`int('100')` <p> **Numero di parametro**: 1 <p> **Nome**: Value <p> **Descrizione**: obbligatoria. Valore convertito in un valore intero.|  
|string|Converte il parametro in una stringa. Ad esempio, questa funzione restituisce `'10'`: <p>`string(10)` <p>È anche possibile convertire un oggetto in una stringa. Se ad esempio il parametro `myPar` è un oggetto con una proprietà `abc : xyz`, questa funzione restituisce `{"abc" : "xyz"}`: <p>`string(parameters('myPar'))` <p> **Numero di parametro**: 1 <p> **Nome**: Value <p> **Descrizione**: obbligatoria. Valore convertito in una stringa.|  
|json|Converte il parametro in un valore di tipo JSON ed è l'opposto di `string()`. Ad esempio, questa funzione restituisce `[1,2,3]` come matrice, invece di una stringa: <p>`json('[1,2,3]')` <p>È analogamente possibile convertire una stringa in un oggetto. Ad esempio, questa funzione restituisce `{ "abc" : "xyz" }`: <p>`json('{"abc" : "xyz"}')` <p> **Numero di parametro**: 1 <p> **Nome**: String <p> **Descrizione**: obbligatoria. Stringa convertita in un valore di tipo nativo. <p>La funzione `json()` supporta anche l'input XML. Ad esempio, il valore del parametro di: <p>`<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>` <p>Viene convertito in JSON: <p>`{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|float|Converte l'argomento del parametro in un numero a virgola mobile. Ad esempio, questa funzione restituisce `10.333`: <p>`float('10.333')` <p> **Numero di parametro**: 1 <p> **Nome**: Value <p> **Descrizione**: obbligatoria. Valore convertito in un numero a virgola mobile.|  
|bool|Converte il parametro in un valore booleano. Ad esempio, questa funzione restituisce `false`: <p>`bool(0)` <p> **Numero di parametro**: 1 <p> **Nome**: Value <p> **Descrizione**: obbligatoria. Valore convertito in un valore booleano.|  
|base64|Restituisce la rappresentazione base64 della stringa di input. Ad esempio, questa funzione restituisce `c29tZSBzdHJpbmc=`: <p>`base64('some string')` <p> **Numero di parametro**: 1 <p> **Nome**: String 1 <p> **Descrizione**: obbligatoria. Stringa da codificare nella rappresentazione con codifica Base64.|  
|base64ToBinary|Restituisce una rappresentazione binaria di una stringa con codifica Base64. Ad esempio, questa funzione restituisce la rappresentazione binaria di `some string`: <p>`base64ToBinary('c29tZSBzdHJpbmc=')` <p> **Numero di parametro**: 1 <p> **Nome**: String <p> **Descrizione**: obbligatoria. Stringa con codifica Base64.|  
|base64ToString|Restituisce una rappresentazione di stringa di una stringa con codifica Based64. Ad esempio, questa funzione restituisce `some string`: <p>`base64ToString('c29tZSBzdHJpbmc=')` <p> **Numero di parametro**: 1 <p> **Nome**: String <p> **Descrizione**: obbligatoria. Stringa con codifica Base64.|  
|Binary|Restituisce una rappresentazione binaria di un valore.  Ad esempio, questa funzione restituisce una rappresentazione binaria di `some string`: <p>`binary('some string')` <p> **Numero di parametro**: 1 <p> **Nome**: Value <p> **Descrizione**: obbligatoria. Valore convertito in un valore binario.|  
|dataUriToBinary|Restituisce una rappresentazione binaria di un URI di dati. Ad esempio, questa funzione restituisce la rappresentazione binaria di `some string`: <p>`dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')` <p> **Numero di parametro**: 1 <p> **Nome**: String <p> **Descrizione**: obbligatoria. URI di dati da convertire in una rappresentazione binaria.|  
|dataUriToString|Restituisce una rappresentazione di stringa di un URI di dati. Ad esempio, questa funzione restituisce `some string`: <p>`dataUriToString('data:;base64,c29tZSBzdHJpbmc=')` <p> **Numero di parametro**: 1 <p> **Nome**: String<p> **Descrizione**: obbligatoria. URI di dati da convertire in una rappresentazione di tipo stringa.|  
|dataUri|Restituisce un URI di dati di un valore. Ad esempio, questa funzione restituisce questo URI di dati `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=`: <p>`dataUri('some string')` <p> **Numero di parametro**: 1<p> **Nome**: Value<p> **Descrizione**: obbligatoria. Valore da convertire in URI di dati.|  
|decodeBase64|Restituisce una rappresentazione di stringa di una stringa di input con codifica Based64. Ad esempio, questa funzione restituisce `some string`: <p>`decodeBase64('c29tZSBzdHJpbmc=')` <p> **Numero di parametro**: 1 <p> **Nome**: String <p> **Descrizione**: restituisce una rappresentazione di stringa di una stringa di input con codifica Based64.|  
|encodeUriComponent|Aggiunge un URL come escape alla stringa passata. Ad esempio, questa funzione restituisce `You+Are%3ACool%2FAwesome`: <p>`encodeUriComponent('You Are:Cool/Awesome')` <p> **Numero di parametro**: 1 <p> **Nome**: String <p> **Descrizione**: obbligatoria. Stringa a cui aggiungere caratteri di escape per i caratteri non sicuri per gli URL.|  
|decodeUriComponent|Rimuove i caratteri di escape per l'URL nella stringa passata. Ad esempio, questa funzione restituisce `You Are:Cool/Awesome`: <p>`encodeUriComponent('You+Are%3ACool%2FAwesome')` <p> **Numero di parametro**: 1 <p> **Nome**: String <p> **Descrizione**: obbligatoria. Stringa da cui decodificare i caratteri non sicuri per gli URL.|  
|decodeDataUri|Restituisce una rappresentazione binaria di una stringa di URI di dati di input. Ad esempio, questa funzione restituisce la rappresentazione binaria di `some string`: <p>`decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')` <p> **Numero di parametro**: 1 <p> **Nome**: String <p> **Descrizione**: obbligatoria. Valore dataURI da decodificare in una rappresentazione binaria.|  
|uriComponent|Restituisce una rappresentazione con codifica URI di un valore. Ad esempio, questa funzione restituisce `You+Are%3ACool%2FAwesome`: <p>`uriComponent('You Are:Cool/Awesome')` <p> **Numero di parametro**: 1<p> **Nome**: String <p> **Descrizione**: obbligatoria. Stringa a cui applicare la codifica URI.|  
|uriComponentToBinary|Restituisce una rappresentazione binaria di una stringa con codifica URI. Ad esempio, questa funzione restituisce una rappresentazione binaria di `You Are:Cool/Awesome`: <p>`uriComponentToBinary('You+Are%3ACool%2FAwesome')` <p> **Numero di parametro**: 1 <p> **Nome**: String<p> **Descrizione**: obbligatoria. Stringa con codifica URI.|  
|uriComponentToString|Restituisce una rappresentazione di stringa di una stringa con codifica URI. Ad esempio, questa funzione restituisce `You Are:Cool/Awesome`: <p>`uriComponentToBinary('You+Are%3ACool%2FAwesome')` <p> **Numero di parametro**: 1<p> **Nome**: String<p> **Descrizione**: obbligatoria. Stringa con codifica URI.|  
|xml|Restituisce una rappresentazione XML del valore. Ad esempio, questa funzione restituisce contenuto XML rappresentato da `'\<name>Alan\</name>'`: <p>`xml('\<name>Alan\</name>')` <p>La funzione `xml()` supporta anche input di tipo oggetto JSON. Ad esempio, il parametro `{ "abc": "xyz" }` viene convertito in contenuto XML: `\<abc>xyz\</abc>` <p> **Numero di parametro**: 1<p> **Nome**: Value<p> **Descrizione**: obbligatoria. Valore da convertire in XML.|  
|array|Converte il parametro in una matrice. Ad esempio, questa funzione restituisce `["abc"]`: <p>`array('abc')` <p> **Numero di parametro**: 1 <p> **Nome**: Value <p> **Descrizione**: obbligatoria. Valore convertito in una matrice.|
|createArray|Crea una matrice dai parametri. Ad esempio, questa funzione restituisce `["a", "c"]`: <p>`createArray('a', 'c')` <p> **Numero parametro**: 1 ... *n* <p> **Nome**: Any *n* <p> **Descrizione**: obbligatoria. Valori da combinare in una matrice.|
|triggerFormDataValue|Restituisce un singolo valore corrispondente al nome di chiave dall'output del trigger con dati di form o con codifica form.  Se sono presenti più corrispondenze, verrà generato un errore.  Ad esempio, il codice seguente restituirà `bar`: `triggerFormDataValue('foo')`<br /><br />**Numero di parametro**: 1<br /><br />**Nome**: KeyName<br /><br />**Descrizione**: obbligatoria. Nome di chiave del valore di dati del form da restituire.|
|triggerFormDataMultiValues|Restituisce una matrice di valori corrispondenti al nome di chiave dall'output del trigger con dati di form o con codifica form.  Ad esempio, il codice seguente restituirà `["bar"]`: `triggerFormDataValue('foo')`<br /><br />**Numero di parametro**: 1<br /><br />**Nome**: KeyName<br /><br />**Descrizione**: obbligatoria. Nome di chiave dei valori di dati del form da restituire.|
|triggerMultipartBody|Restituisce il corpo per una parte in un output a più parti del trigger.<br /><br />**Numero di parametro**: 1<br /><br />**Nome**: Index<br /><br />**Descrizione**: obbligatoria. Indice della parte da recuperare.|
|formDataValue|Restituisce un singolo valore corrispondente al nome di chiave dall'output dell'azione con dati di form o con codifica form.  Se sono presenti più corrispondenze, verrà generato un errore.  Ad esempio, il codice seguente restituirà `bar`: `formDataValue('someAction', 'foo')`<br /><br />**Numero di parametro**: 1<br /><br />**Nome**: ActionName<br /><br />**Descrizione**: obbligatoria. Nome dell'azione con una risposta di tipo con dati di form o con codifica form.<br /><br />**Numero di parametro**: 2<br /><br />**Nome**: KeyName<br /><br />**Descrizione**: obbligatoria. Nome di chiave del valore di dati del form da restituire.|
|formDataMultiValues|Restituisce una matrice di valori corrispondenti al nome di chiave dall'output dell'azione con dati di form o con codifica form.  Ad esempio, il codice seguente restituirà `["bar"]`: `formDataMultiValues('someAction', 'foo')`<br /><br />**Numero di parametro**: 1<br /><br />**Nome**: ActionName<br /><br />**Descrizione**: obbligatoria. Nome dell'azione con una risposta di tipo con dati di form o con codifica form.<br /><br />**Numero di parametro**: 2<br /><br />**Nome**: KeyName<br /><br />**Descrizione**: obbligatoria. Nome di chiave dei valori di dati del form da restituire.|
|multipartBody|Restituisce il corpo per una parte in un output a più parti di un'azione.<br /><br />**Numero di parametro**: 1<br /><br />**Nome**: ActionName<br /><br />**Descrizione**: obbligatoria. Nome dell'azione con una risposta a più parti.<br /><br />**Numero di parametro**: 2<br /><br />**Nome**: Index<br /><br />**Descrizione**: obbligatoria. Indice della parte da recuperare.|

### <a name="manipulation-functions"></a>Funzioni di manipolazione
 
Queste funzioni si applicano a oggetti e XML.
 
|Nome della funzione|Descrizione|  
|-------------------|-----------------| 
|coalesce|Restituisce il primo oggetto non Null negli argomenti passati. **Nota**: una stringa vuota non è Null. Se ad esempio i parametri 1 e 2 non sono definiti, questa funzione restituisce `fallback`:  <p>`coalesce(parameters('parameter1'), parameters('parameter2') ,'fallback')` <p> **Numero parametro**: 1 ... *n* <p> **Nome**: Object*n* <p> **Descrizione**: obbligatoria. Oggetto da controllare per rilevare valori Null.|
|addProperty|Restituisce un oggetto con una proprietà aggiuntiva. Se la proprietà esiste già in fase di esecuzione verrà generato un errore. Ad esempio, questa funzione restituisce l'oggetto `{ "abc" : "xyz", "def": "uvw" }`: <p>`addProperty(json('{"abc" : "xyz"}'), 'def', 'uvw')` <p> **Numero di parametro**: 1 <p> **Nome**: Object <p> **Descrizione**: obbligatoria. Oggetto a cui aggiungere una nuova proprietà. <p> **Numero di parametro**: 2 <p> **Nome**: Property Name <p> **Descrizione**: obbligatoria. Nome della nuova proprietà. <p> **Numero di parametro**: 3 <p> **Nome**: Value <p> **Descrizione**: obbligatoria. Valore da assegnare alla nuova proprietà.|
|setProperty|Restituisce un oggetto con un'altra proprietà o una proprietà esistente impostata sul valore specificato. Ad esempio, questa funzione restituisce l'oggetto `{ "abc" : "uvw" }`: <p>`setProperty(json('{"abc" : "xyz"}'), 'abc', 'uvw')` <p> **Numero di parametro**: 1 <p> **Nome**: Object <p> **Descrizione**: obbligatoria. Oggetto in cui impostare la proprietà.<p> **Numero di parametro**: 2 <p> **Nome**: Property Name<p> **Descrizione**: obbligatoria. Nome della proprietà nuova o esistente. <p> **Numero di parametro**: 3 <p> **Nome**: Value <p> **Descrizione**: obbligatoria. Valore da assegnare alla proprietà.|
|removeProperty|Restituisce un oggetto con una proprietà rimossa. Se la proprietà da rimuovere non esiste viene restituito l'oggetto originale. Ad esempio, questa funzione restituisce l'oggetto `{ "abc" : "xyz" }`: <p>`removeProperty(json('{"abc" : "xyz", "def": "uvw"}'), 'def')` <p> **Numero di parametro**: 1 <p> **Nome**: Object <p> **Descrizione**: obbligatoria. Oggetto da cui rimuovere la proprietà.<p> **Numero di parametro**: 2 <p> **Nome**: Property Name <p> **Descrizione**: obbligatoria. Nome della proprietà da rimuovere. <p>|
|xpath|Restituisce una matrice di nodi XML corrispondenti all'espressione xpath di un valore restituito dall'espressione xpath. <p> **Esempio 1** <p>Si presupponga che il valore del parametro `p1` sia una rappresentazione di stringa di questo XML: <p>`<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>` <p>Questo codice: `xpath(xml(parameters('p1')), '/lab/robot/name')` <p>Restituisce <p>`[ <name>R1</name>, <name>R2</name> ]` <p>Mentre questo codice: <p>`xpath(xml(parameters('p1')), ' sum(/lab/robot/parts)')` <p>Restituisce <p>`13` <p> <p> **Esempio 2** <p>Se è disponibile il contenuto XML seguente: <p>`<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>` <p>Questo codice: `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')` <p>O questo codice: <p>`@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')` <p>Restituisce <p>`<Location xmlns="http://abc.com">xyz</Location>` <p>E questo codice: `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')` <p>Restituisce <p>``xyz`` <p> **Numero di parametro**: 1 <p> **Nome**: Xml <p> **Descrizione**: obbligatoria. Codice XML in base al quale valutare l'espressione XPath. <p> **Numero di parametro**: 2 <p> **Nome**: XPath <p> **Descrizione**: obbligatoria. Espressione XPath da valutare.|

### <a name="math-functions"></a>Funzioni matematiche  

Queste funzioni possono essere usate per qualsiasi tipo di numero, ovvero **numeri interi** e **numeri a virgola mobile**.  
  
|Nome della funzione|Descrizione|  
|-------------------|-----------------|  
|add|Restituisce il risultato della somma di due numeri. Ad esempio, questa funzione restituisce `20.333`: <p>`add(10,10.333)` <p> **Numero di parametro**: 1 <p> **Nome**: Summand 1 <p> **Descrizione**: obbligatoria. Numero da aggiungere a **Summand 2**. <p> **Numero di parametro**: 2 <p> **Nome**: Summand 2 <p> **Descrizione**: obbligatoria. Numero da aggiungere a **Summand 1**.|  
|sub|Restituisce il risultato della sottrazione di due numeri. Ad esempio, questa funzione restituisce `-0.333`: <p>`sub(10,10.333)` <p> **Numero di parametro**: 1 <p> **Nome**: Minuend <p> **Descrizione**: obbligatoria. Numero da cui viene rimosso **Subtrahend**. <p> **Numero di parametro**: 2 <p> **Nome**: Subtrahend <p> **Descrizione**: obbligatoria. Numero da rimuovere da **Minuend**.|  
|mul|Restituisce il risultato della moltiplicazione di due numeri. Ad esempio, questa funzione restituisce `103.33`: <p>`mul(10,10.333)` <p> **Numero di parametro**: 1 <p> **Nome**: Multiplicand 1 <p> **Descrizione**: obbligatoria. Numero per cui moltiplicare **Multiplicand 2**. <p> **Numero di parametro**: 2 <p> **Nome**: Multiplicand 2 <p> **Descrizione**: obbligatoria. Numero per cui moltiplicare **Multiplicand 1**.|  
|div|Restituisce il risultato della divisione di due numeri. Ad esempio, questa funzione restituisce `1.0333`: <p>`div(10.333,10)` <p> **Numero di parametro**: 1 <p> **Nome**: Dividend <p> **Descrizione**: obbligatoria. Numero da dividere per il **Divisor**. <p> **Numero di parametro**: 2 <p> **Nome**: Divisor <p> **Descrizione**: obbligatoria. Numero da dividere per **Dividend**.|  
|mod|Restituisce il resto dopo la divisione di due numeri (modulo). Ad esempio, questa funzione restituisce `2`: <p>`mod(10,4)` <p> **Numero di parametro**: 1 <p> **Nome**: Dividend <p> **Descrizione**: obbligatoria. Numero da dividere per il **Divisor**. <p> **Numero di parametro**: 2 <p> **Nome**: Divisor <p> **Descrizione**: obbligatoria. Numero da dividere per **Dividend**. Dopo la divisione, viene calcolato il resto.|  
|Min|Sono disponibili due modelli diversi per chiamare questa funzione. <p>Il modello `min` accetta una matrice e la funzione restituisce `0`: <p>`min([0,1,2])` <p>In alternativa, questa funzione può accettare un elenco di valori delimitato da virgole e restituisce `0`: <p>`min(0,1,2)` <p> **Nota**: tutti i valori devono essere numeri, quindi se il parametro è una matrice, la matrice deve includere solo numeri. <p> **Numero di parametro**: 1 <p> **Nome**: Collection o Value <p> **Descrizione**: obbligatoria. Matrice di valori di cui trovare il valore minimo oppure il primo valore di un set. <p> **Numero di parametro**: 2 ... *n* <p> **Nome**: Value *n* <p> **Descrizione**: facoltativa. Se il primo parametro è di tipo Value, è possibile passare valori aggiuntivi e viene restituito il valore minimo tra tutti i valori passati.|  
|max|Sono disponibili due modelli diversi per chiamare questa funzione. <p>Il modello `max` accetta una matrice e la funzione restituisce `2`: <p>`max([0,1,2])` <p>In alternativa, questa funzione può accettare un elenco di valori delimitato da virgole e restituisce `2`: <p>`max(0,1,2)` <p> **Nota**: tutti i valori devono essere numeri, quindi se il parametro è una matrice, la matrice deve includere solo numeri. <p> **Numero di parametro**: 1 <p> **Nome**: Collection o Value <p> **Descrizione**: obbligatoria. Matrice di valori di cui trovare il valore massimo oppure primo valore di un set. <p> **Numero di parametro**: 2 ... *n* <p> **Nome**: Value *n* <p> **Descrizione**: facoltativa. Se il primo parametro è di tipo Value, è possibile passare valori aggiuntivi e viene restituito il valore massimo tra tutti i valori passati.|  
|range|Genera una matrice di numeri interi a partire da un numero specifico. È possibile definire la lunghezza della matrice restituita. <p>Ad esempio, questa funzione restituisce `[3,4,5,6]`: <p>`range(3,4)` <p> **Numero di parametro**: 1 <p> **Nome**: StartIndex <p> **Descrizione**: obbligatoria. Primo numero intero nella matrice. <p> **Numero di parametro**: 2 <p> **Nome**: Count <p> **Descrizione**: obbligatoria. Numero di valori interi presenti nella matrice.|  
|rand|Genera un numero intero casuale entro l'intervallo specificato, compreso solo il primo estremo. Ad esempio, questa funzione può restituire `0` o "1": <p>`rand(0,2)` <p> **Numero di parametro**: 1 <p> **Nome**: Minimum <p> **Descrizione**: obbligatoria. Numero intero più basso che può essere restituito. <p> **Numero di parametro**: 2 <p> **Nome**: Maximum <p> **Descrizione**: obbligatoria. Questo valore è il numero intero successivo al numero intero più alto che può essere restituito.|  
 
### <a name="date-functions"></a>Funzioni di data  

|Nome della funzione|Descrizione|  
|-------------------|-----------------|  
|utcnow|Restituisce il timestamp corrente come stringa, ad esempio: `2017-03-15T13:27:36Z`: <p>`utcnow()` <p> **Numero di parametro**: 1 <p> **Nome**: Format <p> **Descrizione**: facoltativa. [Singolo carattere di identificatore formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o [modello di formato personalizzato](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) che indica come formattare il valore di questo timestamp. Se il formato non viene specificato, viene usato il formato ISO 8601 ("o").|  
|addseconds|Aggiunge un numero intero di secondi a un timestamp di stringa passato. Il numero di secondi può essere positivo o negativo. Per impostazione predefinita, il risultato è una stringa in formato ISO 8601 ("o"), a meno che non venga fornito un identificatore di formato. Ad esempio, `2015-03-15T13:27:00Z`: <p>`addseconds('2015-03-15T13:27:36Z', -36)` <p> **Numero di parametro**: 1 <p> **Nome**: Timestamp <p> **Descrizione**: obbligatoria. Stringa contenente l'ora. <p> **Numero di parametro**: 2 <p> **Nome**: Seconds <p> **Descrizione**: obbligatoria. Numero di secondi da aggiungere. Può essere negativo per sottrarre secondi. <p> **Numero di parametro**: 3 <p> **Nome**: Format <p> **Descrizione**: facoltativa. [Singolo carattere di identificatore formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o [modello di formato personalizzato](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) che indica come formattare il valore di questo timestamp. Se il formato non viene specificato, viene usato il formato ISO 8601 ("o").|  
|addminutes|Aggiunge un numero intero di minuti a un timestamp di stringa passato. Il numero di minuti può essere positivo o negativo. Per impostazione predefinita, il risultato è una stringa in formato ISO 8601 ("o"), a meno che non venga fornito un identificatore di formato. Ad esempio, `2015-03-15T14:00:36Z`: <p>`addminutes('2015-03-15T13:27:36Z', 33)` <p> **Numero di parametro**: 1 <p> **Nome**: Timestamp <p> **Descrizione**: obbligatoria. Stringa contenente l'ora. <p> **Numero di parametro**: 2 <p> **Nome**: Minutes <p> **Descrizione**: obbligatoria. Numero di minuti da aggiungere. Può essere negativo per sottrarre minuti. <p> **Numero di parametro**: 3 <p> **Nome**: Format <p> **Descrizione**: facoltativa. [Singolo carattere di identificatore formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o [modello di formato personalizzato](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) che indica come formattare il valore di questo timestamp. Se il formato non viene specificato, viene usato il formato ISO 8601 ("o").|  
|addhours|Aggiunge un numero intero di ore a un timestamp di stringa passato. Il numero di ore può essere positivo o negativo. Per impostazione predefinita, il risultato è una stringa in formato ISO 8601 ("o"), a meno che non venga fornito un identificatore di formato. Ad esempio, `2015-03-16T01:27:36Z`: <p>`addhours('2015-03-15T13:27:36Z', 12)` <p> **Numero di parametro**: 1 <p> **Nome**: Timestamp <p> **Descrizione**: obbligatoria. Stringa contenente l'ora. <p> **Numero di parametro**: 2 <p> **Nome**: Hours <p> **Descrizione**: obbligatoria. Numero di ore da aggiungere. Può essere negativo per sottrarre ore. <p> **Numero di parametro**: 3 <p> **Nome**: Format <p> **Descrizione**: facoltativa. [Singolo carattere di identificatore formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o [modello di formato personalizzato](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) che indica come formattare il valore di questo timestamp. Se il formato non viene specificato, viene usato il formato ISO 8601 ("o").|  
|adddays|Aggiunge un numero intero di giorni a un timestamp di stringa passato. Il numero di giorni può essere positivo o negativo. Per impostazione predefinita, il risultato è una stringa in formato ISO 8601 ("o"), a meno che non venga fornito un identificatore di formato. Ad esempio, `2015-02-23T13:27:36Z`: <p>`adddays('2015-03-15T13:27:36Z', -2)` <p> **Numero di parametro**: 1 <p> **Nome**: Timestamp <p> **Descrizione**: obbligatoria. Stringa contenente l'ora. <p> **Numero di parametro**: 2 <p> **Nome**: Days <p> **Descrizione**: obbligatoria. Numero di giorni da aggiungere. Può essere negativo per sottrarre giorni. <p> **Numero di parametro**: 3 <p> **Nome**: Format <p> **Descrizione**: facoltativa. [Singolo carattere di identificatore formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o [modello di formato personalizzato](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) che indica come formattare il valore di questo timestamp. Se il formato non viene specificato, viene usato il formato ISO 8601 ("o").|  
|formatDateTime|Restituisce una stringa con formato data. Per impostazione predefinita, il risultato è una stringa in formato ISO 8601 ("o"), a meno che non venga fornito un identificatore di formato. Ad esempio, `2015-02-23T13:27:36Z`: <p>`formatDateTime('2015-03-15T13:27:36Z', 'o')` <p> **Numero di parametro**: 1 <p> **Nome**: Date <p> **Descrizione**: obbligatoria. Stringa contenente la data. <p> **Numero di parametro**: 2 <p> **Nome**: Format <p> **Descrizione**: facoltativa. [Singolo carattere di identificatore formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o [modello di formato personalizzato](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) che indica come formattare il valore di questo timestamp. Se il formato non viene specificato, viene usato il formato ISO 8601 ("o").|  
|startOfHour|Restituisce l'inizio dell'ora a un timestamp di stringa passato. Ad esempio, `2017-03-15T13:00:00Z`:<br /><br /> `startOfHour('2017-03-15T13:27:36Z')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrizione**: obbligatoria. Stringa contenente l'ora.<br /><br />**Numero di parametro**: 2<br /><br /> **Nome**: Format<br /><br /> **Descrizione**: facoltativa. [Singolo carattere di identificatore formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o [modello di formato personalizzato](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) che indica come formattare il valore di questo timestamp. Se il formato non viene specificato, viene usato il formato ISO 8601 ("o").|  
|startOfDay|Restituisce l'inizio della giornata a un timestamp di stringa passato. Ad esempio, `2017-03-15T00:00:00Z`:<br /><br /> `startOfDay('2017-03-15T13:27:36Z')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrizione**: obbligatoria. Stringa contenente l'ora.<br /><br />**Numero di parametro**: 2<br /><br /> **Nome**: Format<br /><br /> **Descrizione**: facoltativa. [Singolo carattere di identificatore formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o [modello di formato personalizzato](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) che indica come formattare il valore di questo timestamp. Se il formato non viene specificato, viene usato il formato ISO 8601 ("o").| 
|startOfMonth|Restituisce l'inizio del mese a un timestamp di stringa passato. Ad esempio, `2017-03-01T00:00:00Z`:<br /><br /> `startOfMonth('2017-03-15T13:27:36Z')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrizione**: obbligatoria. Stringa contenente l'ora.<br /><br />**Numero di parametro**: 2<br /><br /> **Nome**: Format<br /><br /> **Descrizione**: facoltativa. [Singolo carattere di identificatore formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o [modello di formato personalizzato](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) che indica come formattare il valore di questo timestamp. Se il formato non viene specificato, viene usato il formato ISO 8601 ("o").| 
|dayOfWeek|Restituisce il componente giorno della settimana di un timestamp di stringa.  Domenica è 0, lunedì è 1 e così via. Ad esempio, `3`:<br /><br /> `dayOfWeek('2017-03-15T13:27:36Z')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrizione**: obbligatoria. Stringa contenente l'ora.| 
|dayOfMonth|Restituisce il componente giorno del mese di un timestamp di stringa. Ad esempio, `15`:<br /><br /> `dayOfMonth('2017-03-15T13:27:36Z')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrizione**: obbligatoria. Stringa contenente l'ora.| 
|dayOfYear|Restituisce il componente giorno dell'anno di un timestamp di stringa. Ad esempio, `74`:<br /><br /> `dayOfYear('2017-03-15T13:27:36Z')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrizione**: obbligatoria. Stringa contenente l'ora.| 
|ticks|Restituisce la proprietà ticks di un timestamp di stringa. Ad esempio, `1489603019`:<br /><br /> `ticks('2017-03-15T18:36:59Z')`<br /><br /> **Numero di parametro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrizione**: obbligatoria. Stringa contenente l'ora.| 
  
### <a name="workflow-functions"></a>Funzioni del flusso di lavoro  

Queste funzioni consentono di ottenere informazioni sul flusso di lavoro stesso in fase di esecuzione.  
  
|Nome della funzione|Descrizione|  
|-------------------|-----------------|  
|listCallbackUrl|Restituisce una stringa da chiamare per richiamare il trigger o l'azione. <p> **Nota**: questa funzione può essere usata solo in un oggetto **httpWebhook** e **apiConnectionWebhook**, non in un oggetto **manual**, **recurrence**, **http** o **apiConnection**. <p>Ad esempio, la funzione `listCallbackUrl()` restituisce: <p>`https://prod-01.westus.logic.azure.com:443/workflows/1235...ABCD/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxx...xxx` |  
|flusso di lavoro|Questa funzione fornisce tutti i dettagli del flusso di lavoro stesso in fase di esecuzione. <p> Le proprietà disponibili per l'oggetto flusso di lavoro sono: <ul><li>`name`</li><li>`type`</li><li>`id`</li><li>`location`</li><li>`run`</li></ul> <p> Il valore della proprietà `run` è un oggetto con le proprietà seguenti: <ul><li>`name`</li><li>`type`</li><li>`id`</li></ul> <p>Per informazioni dettagliate su queste proprietà, vedere [Rest API](http://go.microsoft.com/fwlink/p/?LinkID=525617) (API REST).<p> Ad esempio, per ottenere il nome dell'esecuzione corrente, usare l'espressione `"@workflow().run.name"`. |

## <a name="next-steps"></a>Passaggi successivi

[Azioni e trigger del flusso di lavoro](logic-apps-workflow-actions-triggers.md)
