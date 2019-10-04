---
title: Formato dello schema - API Knowledge Exploration Service
titlesuffix: Azure Cognitive Services
description: Informazioni sul formato dello schema nell'API Knowledge Exploration Service (KES).
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 51a812762659bcc67762b82e9c120772065aab53
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814407"
---
# <a name="schema-format"></a>Formato dello schema

Lo schema è specificato in un file JSON che descrive la struttura di attributi degli oggetti nel file di dati usato per creare l'indice.  Per ogni attributo, lo schema specifica il nome, il tipo di dati, le operazioni facoltative e l'elenco facoltativo di sinonimi.  Un oggetto può avere 0 o più valori di ogni attributo.  Di seguito è disponibile un esempio semplificato, tratto da un dominio di pubblicazioni accademiche:

``` json
{
  "attributes":[
    {"name":"Title", "type":"String"},
    {"name":"Year", "type":"Int32"},
    {"name":"Author", "type":"Composite"},
    {"name":"Author.Id", "type":"Int64", "operations":["equals"]},
    {"name":"Author.Name", "type":"String"},
    {"name":"Author.Affiliation", "type":"String"},
    {"name":"Keyword", "type":"String", "synonyms":"Keyword.syn"}
  ]
}
```

I nomi degli attributi sono identificatori che rilevano la differenza tra maiuscole e minuscole, che iniziano con una lettera e contengono solo lettere (A-Z), numeri (0-9) e caratteri di sottolineatura (\_).  L'attributo riservato "logprob" viene usato per specificare le probabilità relative basate su logaritmi naturali tra gli oggetti.

## <a name="attribute-type"></a>Tipo di attributo

Di seguito è riportato un elenco dei tipi di dati degli attributi supportati:

| Type | Descrizione | Operazioni | Esempio |
|------|-------------|------------|---------|
| `String` | Stringa (da 1 a 1024 caratteri) | equals, starts_with | "hello world" |
| `Int32` | Valore intero a 32 bit con segno | equals, starts_with, is_between | 2016 |
| `Int64` | Valore intero a 64 bit con segno | equals, starts_with, is_between | 9876543210 |
| `Double` | Valore a virgola mobile a precisione doppia | equals, starts_with, is_between | 1,602e-19 |
| `Date` | Data (da 1400-01-01 a 9999-12-31) | equals, is_between | '2016-03-14' |
| `Guid` | Identificatore univoco globale | equals | "602DD052-CC47-4B23-A16A-26B52D30C05B" |
| `Blob` | Dati non indicizzati compressi internamente | *None* | "Empower every person and every organization on the planet to achieve more" |
| `Composite` | Composizione di più attributi secondari| *N/D* | { "Name":"harry shum", "Affiliation":"microsoft" } |

Gli attributi di tipo stringa vengono usati per rappresentare i valori di stringa che possono essere visualizzati come parte della query utente.  Supportano l'operazione *equals* con corrispondenza esatta, oltre all'operazione *starts_with* per gli scenari di completamento delle query, ad esempio la corrispondenza di "micros" con "microsoft".  La corrispondenza fuzzy e senza distinzione tra maiuscole e minuscole per gestire gli errori ortografici sarà supportata in una versione futura.

Gli attributi Int32/Int64/Double vengono usati per rappresentare valori numerici.  L'operazione *is_between* abilita il supporto della disuguaglianza (lt, le, gt, ge) in fase di esecuzione.  L'operazione *starts_with* supporta gli scenari di completamento delle query, ad esempio la corrispondenza di "20" con "2016" o di"3," con "3,14".

Gli attributi di tipo data vengono usati per codificare in modo efficiente i valori delle date.  L'operazione *is_between* abilita il supporto della disuguaglianza (lt, le, gt, ge) in fase di esecuzione.
  
Gli attributi di tipo GUID vengono usati per rappresentare in modo efficiente i valori dei GUID con il supporto predefinito per l'operazione *equals*.

Gli attributi di tipo BLOB vengono usati per codificare in modo efficiente i BLOB di dati potenzialmente di grandi dimensioni per la ricerca runtime dall'oggetto corrispondente, senza il supporto per le operazione di indicizzazione in base al contenuto dei valori BLOB.

### <a name="composite-attributes"></a>Attributi compositi

Gli attributi composti vengono usati per rappresentare un raggruppamento di valori di attributi.  Il nome di ogni attributo secondario inizia con il nome dell'attributo composito seguito da ".".  I valori per gli attributi compositi vengono specificati come oggetto JSON contenente i valori degli attributi annidati.  Gli attributi compositi possono avere più valori di oggetto.  Gli attributi compositi, tuttavia, potrebbero non avere attributi secondari che sono a loro volta attributi compositi.

Nell'esempio di pubblicazione accademica precedente, ciò consente al servizio di cercare i documenti di "harry shum" mentre lavora presso "microsoft".  Senza attributi compositi, il servizio può cercare solo i documenti in cui uno degli autori è "harry shum" e uno degli autori è presso "microsoft".  Per altre informazioni, vedere [Query composite](SemanticInterpretation.md#composite-function).

## <a name="attribute-operations"></a>Operazioni degli attributi

Per impostazione predefinita, ogni attributo viene indicizzato per supportare tutte le operazioni disponibili per il tipo di dati dell'attributo.  Se una determinata operazione non è obbligatoria, il set di operazioni indicizzate può essere specificato in modo esplicito per ridurre le dimensioni dell'indice.  Nel frammento seguente, tratto dallo schema di esempio precedente, l'attributo Author.Id viene indicizzato per supportare solo l'operazione *equals*, ma non le operazioni *starts_with* e *is_between* aggiuntive per gli attributi Int32.
```json
{"name":"Author.Id", "type":"Int32", "operations":["equals"]}
```

Quando si fa riferimento a un attributo all'interno di una grammatica, l'operazione *starts_with* deve essere specificata nello schema in modo che il servizio possa generare i completamenti dalle query parziali.  

## <a name="attribute-synonyms"></a>Sinonimi degli attributi

È spesso consigliabile fare riferimento a un particolare valore di attributo di tipo stringa con un sinonimo.  Gli utenti, ad esempio, possono fare riferimento a "Microsoft" come "MSFT" o "MS".  In questi casi, la definizione dell'attributo può specificare il nome di un file di schema che si trova nella stessa directory del file di schema.  Ogni riga del file di sinonimo rappresenta una voce sinonimo nel formato JSON seguente: `["<canonical>", "<synonym>"]`.  Nello schema di esempio "AuthorName.syn" è un file JSON contenente i valori sinonimo per l'attributo Author.Name.

`{"name":"Author.Name", "type":"String", "synonyms":"AuthorName.syn"}`


Un valore canonico può avere più sinonimi.  Più valori canonici possono condividere un sinonimo comune.  In questi casi, il servizio risolverà l'ambiguità tramite più interpretazioni.  Di seguito è riportato un file di sinonimi AuthorName.syn di esempio corrispondente allo schema precedente:
```json
["harry shum","heung-yeung shum"]
["harry shum","h shum"]
["henry shum","h shum"]
...
```
