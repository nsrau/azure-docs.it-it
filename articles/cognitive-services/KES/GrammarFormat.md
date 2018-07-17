---
title: Formato della grammatica nell'API Knowledge Exploration Service | Microsoft Docs
description: Informazioni sul formato della grammatica nell'API Knowledge Exploration Service (KES) in Servizi cognitivi.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 27202379b8c36696a380049336229cac040b0108
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373404"
---
# <a name="grammar-format"></a>Formato della grammatica
La grammatica è un file XML che specifica il set pesato di query in linguaggio naturale che il servizio può interpretare, nonché il modo in cui queste query in linguaggio naturale vengono convertite in espressioni di query semantiche.  La sintassi della grammatica è basata su [SRGS](http://www.w3.org/TR/speech-grammar/), uno standard W3C per le grammatiche di riconoscimento vocale, con estensioni per supportare l'integrazione di indici dei dati e funzioni semantiche.

Di seguito vengono descritti tutti gli elementi sintattici che possono essere usati in una grammatica.  Vedere [questo esempio](#example) per una grammatica completa che dimostra l'uso di questi elementi nel contesto.

### <a name="grammar-element"></a>Elemento grammar 
L'elemento `grammar` è l'elemento di primo livello nel file XML di specifica della grammatica.  L'attributo obbligatorio `root` specifica il nome della regola radice che definisce il punto iniziale della grammatica.

```xml
<grammar root="GetPapers">
```

### <a name="import-element"></a>Elemento import
L'elemento `import` importa una definizione di schema da un file esterno per abilitare i riferimenti agli attributi. L'elemento deve essere un elemento figlio dell'elemento `grammar` di primo livello e viene visualizzato prima di qualsiasi elemento `attrref`. L'attributo `schema` obbligatorio specifica il nome di un file di schema che si trova nella stessa directory del file XML della grammatica. L'elemento `name` obbligatorio specifica l'alias dello schema usato dagli elementi `attrref` successivi per i riferimenti agli attributi definiti all'interno di questo schema.

```xml
  <import schema="academic.schema" name="academic"/>
```

### <a name="rule-element"></a>Elemento rule
L'elemento `rule` definisce una regola della grammatica, ovvero un'unità strutturale che specifica un set di espressioni di query che il sistema può interpretare.  L'elemento deve essere un elemento figlio dell'elemento `grammar` di primo livello.  L'attributo obbligatorio `id` specifica il nome della regola, a cui viene fatto riferimento dagli elementi `grammar` o `ruleref`.

Un elemento `rule` definisce un set di espansioni valide.  Per i token del testo vengono individuate le corrispondenze direttamente in base alla query di input.  Gli elementi `item` specificano ripetizioni e modificano le probabilità di interpretazione.  Gli elementi `one-of` indicano scelte alternative.  Gli elementi `ruleref` consentono la costruzione di espansioni più complesse da quelle più semplici.  Gli elemento `attrref` consentono corrispondenze in base ai valori di attributo dall'indice.  Gli elementi `tag` specificano la semantica dell'interpretazione e possono modificare la probabilità di interpretazione.

```xml
<rule id="GetPapers">...</rule>
```

### <a name="example-element"></a>Elemento example
L'elemento facoltativo `example` specifica frasi di esempio che possono essere accettate dalla definizione di `rule` che lo contiene.  Può essere usato per la documentazione e/o per i test automatizzati.

```xml
<example>papers about machine learning by michael jordan</example>
```

### <a name="item-element"></a>Elemento item
L'elemento `item` raggruppa una sequenza di costrutti della grammatica.  Può essere usato per indicare ripetizioni della sequenza di espansione o per specificare alternative in combinazione con l'elemento `one-of`.

Quando un elemento `item` non è un elemento figlio di un elemento `one-of`, può specificare la ripetizione della sequenza inclusa assegnando l'attributo `repeat` a un valore di conteggio.  Un valore di conteggio "*n*" (dove *n* è un numero intero) indica che la sequenza deve essere ripetuta esattamente *n* volte.  Un valore di conteggio "*m*-*n*" consente la comparsa della sequenza tra *m* e *n* volte, incluse.  Un valore di conteggio "*m*-" specifica che la sequenza deve comparire almeno *m* volte.  L'attributo facoltativo `repeat-logprob` può essere usato per modificare la probabilità di interpretazione per ogni ripetizione aggiuntiva oltre il minimo.

```xml
<item repeat="1-" repeat-logprob="-10">...</item>
```

Quando gli elementi `item` vengono visualizzati come elementi figlio di un elemento `one-of`, definiscono il set di alternative di espansione.  In questo caso, l'attributo facoltativo `logprob` specifica la probabilità logaritmica relativa tra le diverse scelte.  Dato un valore di probabilità *p* compreso tra 0 e 1, la probabilità logaritmica corrispondente può essere calcolata come log(*p*), dove log() è la funzione logaritmo naturale.  Se non specificato, il valore predefinito di `logprob` è 0, che non modifica la probabilità di interpretazione.  Si noti che la probabilità logaritmica è sempre un valore a virgola mobile negativo o 0.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="one-of-element"></a>Elemento one-of
L'elemento `one-of` specifica le espansioni alternative tra uno degli elementi `item` figlio.  Solo gli elementi `item` possono comparire all'interno di un elemento `one-of`.  È possibile specificare probabilità relative tra le diverse opzioni tramite l'attributo `logprob` in ogni elemento figlio `item`.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="ruleref-element"></a>Elemento ruleref
L'elemento `ruleref` specifica le espansioni valide tramite riferimenti a un altro elemento `rule`.  L'uso degli elementi `ruleref` consente di creare espressioni più complesse da regole più semplici.  L'attributo `uri` obbligatorio indica il nome dell'elemento `rule` di riferimento usando la sintassi "#*nomeregola*".  Per acquisire l'output semantico della regola di riferimento, usare l'attributo facoltativo `name` per specificare il nome di una variabile a cui assegnare l'output semantico.
 
```xml
<ruleref uri="#GetPaperYear" name="year"/>
```

### <a name="attrref-element"></a>Elemento attrref
L'elemento `attrref` fa riferimento a un attributo di indice, consentendo l'individuazione di corrispondenze in base ai valori di attributo osservati nell'indice.  L'attributo obbligatorio `uri` specifica il nome dello schema dell'indice e il nome dell'attributo con la sintassi "*nomeSchema*#*nomeAttr*".  Deve essere presente un elemento `import` precedente che importa lo schema denominato *nomeSchema*.  Il nome dell'attributo è il nome di un attributo definito nello schema corrispondente.

Oltre a individuare le corrispondenze per l'input dell'utente, l'elemento `attrref` restituisce anche un oggetto query strutturata come output che seleziona il subset di oggetti nell'indice corrispondente al valore di input.  Usare l'attributo facoltativo `name` per specificare il nome della variabile in cui deve essere archiviato l'output dell'oggetto query.  L'oggetto query può essere composto con altri oggetti query in modo da formare espressioni più complesse.  Vedere [Interpretazione semantica](SemanticInterpretation.md) per altri dettagli.  

```xml
<attrref uri="academic#Keyword" name="keyword"/>
```

#### <a name="query-completion"></a>Completamento della query 
Per supportare i completamenti delle query durante l'interpretazione di query utente parziali, ogni attributo a cui viene fatto riferimento deve includere "starts_with" come operazione nella definizione dello schema.  Dato un prefisso di query utente, `attrref` individuerà come corrispondenze tutti i valori nell'indice che completano il prefisso e restituirà ogni valore completo come interpretazione separata della grammatica.  

Esempi:
* La corrispondenza di `<attrref uri="academic#Keyword" name="keyword"/>` in base al prefisso di query "dat" genera una interpretazione per i documenti sui "database", una interpretazione per i documenti sul "data mining" e così via.
* La corrispondenza di `<attrref uri="academic#Year" name="year"/>` in base al prefisso di query "200" genera una interpretazione per i documenti del "2000", una interpretazione per i documenti del "2001" e così via.

#### <a name="matching-operations"></a>Operazioni di corrispondenza
Oltre alla corrispondenza esatta, alcuni tipi di attributo supportano anche corrispondenze in base a prefisso e disuguaglianza tramite l'attributo facoltativo `op`.  Se nessun oggetto nell'indice ha un valore corrispondente, il percorso della grammatica viene bloccato e il servizio non genererà alcuna interpretazione su questo percorso della grammatica.   Il valore predefinito dell'attributo `op` è "eq".

```xml
in <attrref uri="academic#Year" name="year"/>
before <attrref uri="academic#Year" op="lt" name="year"/
```

La tabella seguente elenca i valori `op` supportati per ogni tipo di attributo.  Per l'uso di questi valori è necessario che l'operazione di indice corrispondente sia inclusa nella definizione dell'attributo dello schema.

| Tipo di attributo | Valore di op | DESCRIZIONE | Operazione sull'indice
|----|----|----|----|
| string | eq | Corrispondenza esatta di stringa | equals |
| string | starts_with | Corrispondenza di prefisso di stringa | starts_with |
| Int32, Int64, Double | eq |  Corrispondenza di uguaglianza numerica | equals |
| Int32, Int64, Double | lt, le, gt, ge | Corrispondenza di disuguaglianza numerica (<, <=, >, >=) | is_between |
| Int32, Int64, Double | starts_with | Corrispondenza di prefisso del valore nella notazione decimale | starts_with |

Esempi:
* `<attrref uri="academic#Year" op="lt" name="year"/>` individua come corrispondenza la stringa di input "2000" e restituisce tutti i documenti pubblicati prima dell'anno 2000, in modo esclusivo.
* `<attrref uri="academic#Year" op="lt" name="year"/>` non individua come corrispondenza la stringa di input "20" poiché non esistono documenti nell'indice pubblicati prima dell'anno 20.
* `<attrref uri="academic#Keyword" op="starts_with" name="keyword"/>` individua come corrispondenza la stringa di input "dat" e restituisce in un'interpretazione singola i su "database", "data mining" e così via.  Questo è un caso d'uso non comune.
* `<attrref uri="academic#Year" op="starts_with" name="year"/>` individua come corrispondenza la stringa di input "20" e restituisce in un'interpretazione singola i documenti pubblicati nel 299 200, 2000-2999 e così via.  Questo è un caso d'uso non comune.

### <a name="tag-element"></a>Elemento tag
L'elemento `tag` specifica come deve essere interpretato un percorso attraverso la grammatica.  Contiene una sequenza di istruzioni che terminano con punto e virgola.  Un'istruzione può essere un'assegnazione di un valore letterale o di una variabile a un'altra variabile.  Può anche assegnare l'output di una funzione con 0 o più parametri a una variabile.  Ogni parametro di funzione può essere specificato usando un valore letterale o una variabile.  Se la funzione non restituisce alcun output, l'assegnazione viene omessa.  L'ambito della variabile è locale per la regola che la contiene.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Ogni `rule` nella grammatica ha una variabile predefinita denominata "out", che rappresenta l'output semantico della regola.  Il valore viene calcolato valutando ogni istruzione semantica attraversata dal percorso tramite la `rule` corrispondente all'input di query dell'utente.  Il valore assegnato alla variabile "out" alla fine della valutazione è l'output semantico della regola.  L'output semantico dell'interpretazione di una query utente in base alla grammatica è l'output semantico della regola radice.

Alcune istruzioni possono modificare la probabilità di un percorso di interpretazione introducendo un offset di probabilità logaritmica additiva.  Alcune istruzioni possono rifiutare del tutto l'interpretazione se non vengono soddisfatte le condizioni specificate.

Per un elenco di funzioni semantiche supportate, vedere [Funzioni semantiche](SemanticInterpretation.md#semantic-functions).

## <a name="interpretation-probability"></a>Probabilità dell'interpretazione
La probabilità di un percorso di interpretazione attraverso la grammatica è la probabilità logaritmica cumulativa di tutti gli elementi `<item>` e delle funzioni semantiche rilevati lungo il percorso.  Descrive la probabilità relativa di individuare una corrispondenza per una particolare sequenza di input.

Dato un valore di probabilità *p* compreso tra 0 e 1, la probabilità logaritmica corrispondente può essere calcolata come log(*p*), dove log() è la funzione logaritmo naturale.  L'uso delle probabilità logaritmiche consente al sistema di accumulare la probabilità congiunta di un percorso di interpretazione tramite aggiunta semplice.  Evita inoltre le condizioni di underflow a virgola mobile comuni a tali calcoli di probabilità congiunta.  Si noti che per impostazione predefinita, la probabilità logaritmica è sempre un valore a virgola mobile negativo o 0, dove i valori superiori indicano maggiore probabilità.

<a name="example"></a>
## <a name="example"></a>Esempio
Di seguito è riportato un file XML di esempio dal dominio delle pubblicazioni accademiche che illustra i vari elementi di una grammatica:

```xml
<grammar root="GetPapers">

  <!-- Import academic data schema-->
  <import schema="academic.schema" name="academic"/>
  
  <!-- Define root rule-->
  <rule id="GetPapers">
    <example>papers about machine learning by michael jordan</example>
    
    papers
    <tag>
      yearOnce = false;
      isBeyondEndOfQuery = false;
      query = All();
    </tag>
  
    <item repeat="1-" repeat-logprob="-10">
      <!-- Do not complete additional attributes beyond end of query -->
      <tag>AssertEquals(isBeyondEndOfQuery, false);</tag>
        
      <one-of>
        <!-- about <keyword> -->
        <item logprob="-0.5">
          about <attrref uri="academic#Keyword" name="keyword"/>
          <tag>query = And(query, keyword);</tag>
        </item>
        
        <!-- by <authorName> [while at <authorAffiliation>] -->
        <item logprob="-1">
          by <attrref uri="academic#Author.Name" name="authorName"/>
          <tag>authorQuery = authorName;</tag>
          <item repeat="0-1" repeat-logprob="-1.5">
            while at <attrref uri="academic#Author.Affiliation" name="authorAffiliation"/>
            <tag>authorQuery = And(authorQuery, authorAffiliation);</tag>
          </item>
          <tag>
            authorQuery = Composite(authorQuery);
            query = And(query, authorQuery);
          </tag>
        </item>
        
        <!-- written (in|before|after) <year> -->
        <item logprob="-1.5">
          <!-- Allow this grammar path to be traversed only once -->
          <tag>
            AssertEquals(yearOnce, false);
            yearOnce = true;
          </tag>
          <ruleref uri="#GetPaperYear" name="year"/>
          <tag>query = And(query, year);</tag>
        </item>
      </one-of>

      <!-- Determine if current parse position is beyond end of query -->
      <tag>isBeyondEndOfQuery = GetVariable("IsBeyondEndOfQuery", "system");</tag>
    </item>
    <tag>out = query;</tag>
  </rule>
  
  <rule id="GetPaperYear">
    <tag>year = All();</tag>
    written
    <one-of>
      <item>
        in <attrref uri="academic#Year" name="year"/>
      </item>
      <item>
        before
        <one-of>
          <item>[year]</item>
          <item><attrref uri="academic#Year" op="lt" name="year"/></item>
        </one-of>
      </item>
      <item>
        after
        <one-of>
          <item>[year]</item>
          <item><attrref uri="academic#Year" op="gt" name="year"/></item>
        </one-of>
      </item>
    </one-of>
    <tag>out = year;</tag>
  </rule>
</grammar>
```
