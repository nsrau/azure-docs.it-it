---
title: 'Esercitazione: Definizione e specifica di funzioni - Servizio decisionale personalizzato'
titlesuffix: Azure Cognitive Services
description: Esercitazione per la definizione e la specifica di funzioni di Machine Learning nel Servizio decisionale personalizzato.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 409dcf7dc224eaf2a3f51325010507b9182fadf2
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707051"
---
# <a name="tutorial-featurization-and-feature-specification"></a>Esercitazione: Definizione e specifica di funzioni

Questa esercitazione illustra la funzionalità avanzata di apprendimento automatico del Servizio decisionale personalizzato. L'esercitazione è costituita da due parti: [definizione delle funzioni](#featurization-concepts-and-implementation) e [specifica delle funzioni](#feature-specification-format-and-apis). La definizione delle funzioni è la rappresentazione dei dati come "funzioni" per l'apprendimento automatico. La specifica delle funzioni riguarda il formato JSON e le API ausiliarie per specificare le funzioni.

Per impostazione predefinita, l'apprendimento automatico nel Servizio decisionale personalizzato è trasparente al cliente. Le funzioni vengono estratte automaticamente dal contenuto e viene usato un algoritmo di apprendimento per rinforzo standard. L'estrazione delle funzioni sfrutta molti altri Servizi cognitivi di Azure: [Entity Linking](../entitylinking/home.md), [Analisi del testo](../text-analytics/overview.md), [Emozioni](../emotion/home.md) e [Visione artificiale](../computer-vision/home.md). È possibile saltare questa esercitazione se si usa solo la funzionalità predefinita.

## <a name="featurization-concepts-and-implementation"></a>Definizione delle funzioni: concetti e implementazione

Il Servizio decisionale personalizzato prende le decisioni una alla volta. Ogni decisione implica la scelta tra varie alternative, anche note come azioni. A seconda dell'applicazione, la decisione può scegliere una singola azione o un breve elenco di azioni con classificazione.

Un esempio è personalizzare la selezione degli articoli nella pagina principale di un sito Web. In questo caso le azioni corrispondono agli articoli e ogni decisione determina quali articoli mostrare a un utente specifico.

Ogni azione è rappresentata da un vettore di proprietà, indicate da ora in avanti come *funzioni*. È possibile specificare nuove funzioni, oltre alle funzioni estratte automaticamente. Si può anche istruire il Servizio decisionale personalizzato a registrare alcune funzioni ma a ignorarle per l'apprendimento automatico.

### <a name="native-vs-internal-features"></a>Funzioni native e interne

È possibile specificare le funzioni in un formato più semplice per l'applicazione, sia esso un numero, una stringa o una matrice. Queste funzioni sono chiamate "funzioni native". Il Servizio decisionale personalizzato converte ogni funzione nativa in una o più funzioni numeriche, denominate "funzioni interne".

La conversione in funzioni interne avviene nel modo seguente:

- le funzioni numeriche rimangono invariate.
- una matrice numerica viene convertita in più funzioni numeriche, una per ogni elemento della matrice.
- una funzione con valori stringa `"Name":"Value"` è convertita per impostazione predefinita in una funzione con il nome `"NameValue"` e il valore 1.
- Facoltativamente, una stringa può essere rappresentata come [elenco di parole](https://en.wikipedia.org/wiki/Bag-of-words_model). Viene quindi creata una funzione interna per ogni parola della stringa il cui valore è il numero di occorrenze della parola.
- Le funzioni interne con valore zero vengono omesse.

### <a name="shared-vs-action-dependent-features"></a>Funzioni condivise e dipendenti dall'azione

Alcune funzioni fanno riferimento all'intera decisione e sono le stesse per tutte le azioni. Sono chiamate *funzioni condivise*. Altre funzioni sono specifiche di una determinata azione. Sono chiamate *funzioni dipendenti dall'azione*.

Nell'esempio in esecuzione le funzioni condivise possono descrivere l'utente e/o lo stato del mondo. Caratteristiche come la georilevazione, l'età e il sesso dell'utente e gli eventi principali che si stanno verificando ora. Le funzioni dipendenti dall'azione possono descrivere le proprietà di un determinato articolo, come gli argomenti trattati in questo articolo.

### <a name="interacting-features"></a>Interazione delle funzioni

Le funzioni spesso "interagiscono": l'effetto di una di esse dipende da altre. Ad esempio la funzione X indica se l'utente è interessato allo sport. La funzione Y indica se un determinato articolo riguarda lo sport. Quindi l'effetto della funzione Y è fortemente dipendente dalla funzione X.

Per tenere conto dell'interazione tra le funzioni X e Y, si crea una funzione *quadratica* il cui valore è X\*Y. Si dice anche "intersezione" di X e Y. È possibile scegliere quali coppie di funzioni si intersecano.

> [!TIP]
> Una funzione condivisa deve essere intersecata con funzioni dipendenti dall'azione per influenzare la classificazione. Una funzione dipendente dall'azione deve essere intersecata con funzioni condivise per contribuire alla personalizzazione.

In altre parole, una funzione condivisa non intersecata con funzioni dipendenti dall'azione influenza ogni azione nello stesso modo. Una funzione dipendente dall'azione non intersecata con funzioni condivise influenza troppo ogni decisione. Questi tipi di funzioni possono ridurre la varianza delle stime di premio.

### <a name="implementation-via-namespaces"></a>Implementazione tramite spazi dei nomi

È possibile implementare funzioni intersecate, e altri concetti di definizione delle funzioni, tramite la "riga di comando di VW" nel portale. La sintassi è basata sulla riga di comando di [Vowpal Wabbit](http://hunch.net/~vw/).

L'implementazione si basa sul concetto di *spazio dei nomi*: un sottoinsieme di funzioni denominato. Ogni funzione appartiene a un solo spazio dei nomi. È possibile specificare lo spazio dei nomi in modo esplicito quando il valore della funzione è fornito al Servizio decisionale personalizzato. È l'unico modo per fare riferimento a una funzione nella riga di comando di VW.

Uno spazio dei nomi è "condiviso" o "dipendente dall'azione": include solo le funzioni condivise o è costituito solo da funzioni dipendenti dall'azione per la stessa azione.

> [!TIP]
> È consigliabile eseguire il wrapping delle funzioni in spazi dei nomi specificati in modo esplicito. Raggruppare le funzioni correlate nello stesso spazio dei nomi.

Se lo spazio dei nomi non viene fornito, la funzione è assegnata automaticamente allo spazio dei nomi predefinito.

> [!IMPORTANT]
> Le funzioni e gli spazi dei nomi non devono necessariamente essere coerenti fra le azioni. In particolare, uno spazio dei nomi può avere funzioni diverse per azioni diverse. Inoltre è possibile definire un determinato spazio dei nomi per alcune azioni e non per altre.

Più funzioni interne che provengono dalla stessa funzione nativa con valori stringa sono raggruppate nello stesso spazio dei nomi. Due funzioni native che si trovano in spazi dei nomi diversi vengono trattate come distinte, anche se hanno lo stesso nome di funzione.

> [!IMPORTANT]
> Anche se gli ID degli spazi dei nomi lunghi e molto descrittivi sono comuni, la riga di comando di VW non distingue tra più spazi dei nomi i cui ID iniziano con la stessa lettera. Di seguito gli ID degli spazi dei nomi sono singole lettere, ad esempio `x` e `y`.

I dettagli di implementazione sono i seguenti:

- Per intersecare gli spazi dei nomi `x` e `y`, si scrive `-q xy` o `--quadratic xy`. Quindi ogni funzione in `x` interseca ogni funzione in `y`. Usare `-q x:` per intersecare `x` con ogni spazio dei nomi e `-q ::` per intersecare tutte le coppie di spazi dei nomi.

- Per ignorare tutte le funzioni nello spazio dei nomi `x`, scrivere `--ignore x`.

Questi comandi sono applicati a ogni azione separatamente, ogni volta che vengono definiti gli spazi dei nomi.

### <a name="estimated-average-as-a-feature"></a>Media stimata come funzione

Come esperimento concettuale, quale sarebbe il premio medio di una determinata azione se fosse scelta per tutte le decisioni? Il premio medio potrebbe essere usato come misura della "qualità complessiva" di questa azione. Non è possibile sapere esattamente quando sono state scelte altre azioni in alcune decisioni, ma è possibile stimarlo tramite tecniche di apprendimento per rinforzo. La qualità di questa stima migliora in genere nel tempo.

È possibile scegliere di includere questo "premio medio stimato" come funzione per una determinata azione. Quindi il Servizio decisionale personalizzato aggiorna automaticamente la stima quando arrivano nuovi dati. Questa caratteristica è chiamata *funzione marginale* di questa azione. Le funzioni marginali possono essere usate per l'apprendimento automatico e per il controllo.

Per aggiungere funzioni marginali, scrivere `--marginal <namespace>` nella riga di comando di VW. Definire `<namespace>` in JSON come indicato di seguito:

```json
{<namespace>: {"mf_name":1 "action_id":1}
```

Inserire questo spazio dei nomi insieme ad altre funzioni dipendenti dall'azione di una determinata azione. Fornire questa definizione per ogni decisione usando lo stesso `mf_name` e `action_id` per tutte le decisioni.

La funzione marginale viene aggiunta per ogni azione con `<namespace>`. `action_id` può essere qualsiasi nome di funzione che identifica in modo univoco l'azione. Il nome della funzione è impostato su `mf_name`. In particolare, le funzioni marginali con `mf_name` diverso sono considerate come funzioni diverse, ovvero viene appreso un peso diverso per ogni `mf_name`.

L'utilizzo predefinito implica che `mf_name` sia uguale per tutte le azioni. Quindi viene appreso un solo peso per tutte le funzioni marginali.

Si può anche specificare più funzioni marginali per la stessa azione, con nomi diversi ma gli stessi valori.

```json
{<namespace>: {"mf_name1":1 "action_id":1 "mf_name2":1 "action_id":1}}
```

### <a name="1-hot-encoding"></a>Codifica 1-hot

È possibile scegliere di rappresentare alcune funzioni come vettori di bit, in cui ogni bit corrisponde a un intervallo di valori possibili. Questo bit viene impostato su 1 se e solo se la funzione si trova in questo intervallo. Di conseguenza, c'è un solo bit "hot" che è impostato su 1 e i restanti valori sono impostati su 0. Questa rappresentazione è comunemente nota come *codifica 1-hot*.

La codifica 1-hot è tipica di funzioni categoriche come la "regione geografica" che non hanno una rappresentazione numerica intrinsecamente significativa. È inoltre consigliabile per le funzioni numeriche la cui influenza sul premio è probabilmente non lineare. Ad esempio, un determinato articolo potrebbe essere pertinente per una fascia di età specifica e non esserlo per tutti gli utenti più vecchi o più giovani.

Qualsiasi funzione con valori stringa assume la codifica 1-hot per impostazione predefinita: viene creata una funzione interna distinta per ogni possibile valore. La codifica 1-hot automatica per le funzioni numeriche e/o con intervalli personalizzati non è attualmente fornita.

> [!TIP]
> Gli algoritmi di apprendimento automatico considerano tutti i valori possibili di una determinata funzione interna in modo uniforme: tramite un "peso" comune. La codifica 1-hot consente di applicare un "peso" separato per ogni intervallo di valori. Riducendo gli intervalli si ottengono premi migliori dopo che sono stati raccolti dati sufficienti, ma potrebbe aumentare la quantità di dati necessaria per ottenere la convergenza su premi migliori.

## <a name="feature-specification-format-and-apis"></a>Specifica delle funzioni: formato e API

È possibile specificare le funzioni tramite diverse API ausiliarie. Tutte le API usano un formato JSON comune. Di seguito sono indicate le API e il formato a livello concettuale. La specifica è integrata da uno schema di Swagger.

Il modello di JSON di base per la specifica delle funzioni è il seguente:

```json
{
"<name>":<value>, "<name>":<value>, ... ,
"namespace1": {"<name>":<value>, ... },
"namespace2": {"<name>":<value>, ... },
...
}
```

Qui `<name>` e `<value>` indicano rispettivamente il nome e il valore della funzione. `<value>` può essere un valore stringa, integer, float, boolean o matrice. Se non si esegue il wrapping di una funzione in uno spazio dei nomi, la funzione è assegnata automaticamente allo spazio dei nomi predefinito.

Per rappresentare una stringa come elenco di parole, usare la sintassi speciale `"_text":"string"` invece di `"<name>":<value>`. Viene creata una diversa funzione interna per ogni parola nella stringa. Il valore è il numero di occorrenze della parola.

Se `<name>` inizia con "_", e non è `"_text"`, la funzione viene ignorata.

> [!TIP]
> In alcuni casi si uniscono funzioni da più origini JSON. Per praticità è possibile rappresentarle come indicato di seguito:
>
> ```json
> {
> "source1":<features>,
> "source2":<features>,
> ...
> }
> ```

Qui `<features>` fa riferimento alla specifica di funzione di base definita in precedenza. Sono consentiti anche livelli di "annidamento" più profondi. Il Servizio decisionale personalizzato individua automaticamente gli oggetti JSON "più bassi" che possono essere interpretati come `<features>`.

#### <a name="feature-set-api"></a>API del set di funzioni

L'API del set di funzioni restituisce un elenco delle funzioni nel formato JSON descritto in precedenza. È possibile usare diversi endpoint dell'API del set di funzioni. Ogni endpoint è identificato dall'ID del set di funzioni e un URL. Il mapping tra gli ID dei set di funzioni e gli URL è impostato nel portale.

Chiamare l'API del set di funzioni inserendo il corrispondente ID del set di funzioni nella sezione appropriata in JSON. Per le funzioni dipendenti dall'azione, la chiamata è automaticamente parametrizzata con l'ID azione. È possibile specificare diversi ID di set di funzioni per la stessa azione.

#### <a name="action-set-api-json-version"></a>API del set di azioni (versione JSON)

L'API del set di azioni ha una versione in cui le azioni e le funzioni vengono specificate in JSON. È possibile specificare le funzioni in modo esplicito e/o tramite le API del set di funzioni. Le funzioni condivise possono essere specificate una volta per tutte le azioni.

#### <a name="ranking-api-http-post-call"></a>API di classificazione (chiamata HTTP POST)

L'API di classificazione ha una versione che usa la chiamata HTTP POST. Il corpo di questa chiamata specifica le azioni e funzioni tramite una sintassi JSON flessibile.

È possibile specificare le azioni in modo esplicito e/o tramite ID di set di azioni. Ogni volta che viene rilevato un ID di set di azione, viene eseguita una chiamata all'endpoint dell'API del set di azioni corrispondente.

Come per l'API del set di azioni, è possibile specificare le funzioni in modo esplicito e/o tramite le API del set di funzioni. Le funzioni condivise possono essere specificate una volta per tutte le azioni.
