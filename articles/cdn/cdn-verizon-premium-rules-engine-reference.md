---
title: Riferimento del motore regole della rete CDN di Azure | Documentazione Microsoft
description: Documentazione di riferimento per le funzionalità e condizioni di corrispondenza del motore regole della rete CDN di Azure.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 5fc611af75a7f733576f9343a4375fb56cacc030
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "67593157"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Riferimento al motore delle regole della rete CDN di Azure di Verizon Premium

Questo articolo fornisce descrizioni dettagliate delle funzionalità e dalle condizioni di corrispondenza disponibili per il [motore regole](cdn-verizon-premium-rules-engine.md) della rete per la distribuzione di contenuti (rete CDN) di Azure.

Il motore regole è progettato per essere l'autorità finale sul modo in cui i tipi di richieste specifici vengono elaborati dalla rete CDN.

**Usi comuni**:

- Eseguire l'override o definire un criterio della cache personalizzato.
- Proteggere o negare le richieste di contenuti sensibili.
- Reindirizzare le richieste.
- Archiviare i dati di log personalizzati.

## <a name="terminology"></a>Terminologia

Una regola è definita con l'uso di [**espressioni condizionali**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [ **corrispondenze**](cdn-verizon-premium-rules-engine-reference-match-conditions.md) e [**funzionalità**](cdn-verizon-premium-rules-engine-reference-features.md). Questi elementi vengono evidenziati nella figura seguente:

 ![Condizione di corrispondenza della rete CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Sintassi

Il modo in cui verranno trattati i caratteri speciali varia a seconda del modo in cui una condizione di corrispondenza o funzionalità gestisce i valori di testo. Una condizione di corrispondenza o funzionalità potrebbe interpretare il testo in uno dei modi seguenti:

1. [**Valori letterali**](#literal-values)
2. [**Valori caratteri jolly**](#wildcard-values)
3. [**Espressioni regolari**](#regular-expressions)

### <a name="literal-values"></a>Valori letterali

Il testo che viene interpretato come valore letterale tratta tutti i caratteri speciali, fatta eccezione per il simbolo %, come parte del valore di cui trovare la corrispondenza. In altre parole, una condizione di corrispondenza letterale impostata su `\'*'\` viene soddisfatta solo se viene trovato tale valore (ad esempio, `\'*'\`).

Per indicare la codifica URL viene usato un simbolo di percentuale (ad esempio, `%20`).

### <a name="wildcard-values"></a>Valori caratteri jolly

Il testo che viene interpretato come un valore carattere jolly assegna un significato aggiuntivo ai caratteri speciali. La tabella di seguito descrive come viene interpretato il set di caratteri seguente:

Character | Descrizione
----------|------------
\ | Una barra rovesciata viene utilizzata per eseguire l'escape di ogni carattere specificato in questa tabella. Una barra rovesciata deve essere specificata direttamente prima del carattere speciale che deve essere sottoposto a escape.<br/>La sintassi seguente, ad esempio, ignora un asterisco: `\*`
% | Per indicare la codifica URL viene usato un simbolo di percentuale (ad esempio, `%20`).
\* | L'asterisco è un carattere jolly che rappresenta uno o più caratteri.
Spazio | Un carattere di spazio indica che una condizione di corrispondenza può essere soddisfatta da uno dei valori o modelli specificati.
"value" | Una virgoletta singola non ha un significato speciale. Tuttavia, un set di virgolette singole viene utilizzato per indicare che un valore deve essere considerato come valore letterale. Può essere usato nei modi seguenti:<br><br/>- Consente una condizione di corrispondenza da soddisfare ogni volta che il valore specificato corrisponde a una parte qualsiasi del valore di confronto.  Ad esempio, `'ma'` corrisponde a una qualsiasi delle seguenti stringhe: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- Consente a un carattere speciale di essere specificato come carattere letterale. Ad esempio, è possibile specificare un carattere spazio letterale racchiudendo uno spazio all'interno di un set di virgolette singole (ad esempio, `' '` o `'sample value'`).<br/>- Consente di specificare un valore vuoto. Specificare un valore vuoto indicando un set di virgolette singole (ovvero '').<br /><br/>**Importante:**<br/>-Se il valore specificato non contiene un carattere jolly, viene automaticamente considerato un valore letterale. Questo significa che non è necessario specificare un set di virgolette singole.<br/>- Se una barra rovesciata non effettua l'escape di un altro carattere in questa tabella, questo viene ignorato se specificato all'interno di un set di virgolette singole.<br/>- Un altro modo per specificare un carattere speciale come un carattere letterale è quello di effettuare l'escape con una barra rovesciata (ovvero `\`).

### <a name="regular-expressions"></a>Espressioni regolari

Le espressioni regolari definiscono un modello che viene cercato all'interno di un valore di testo. La notazione delle espressioni regolari definisce un significato specifico per una varietà di simboli. La tabella seguente indica come vengono trattati i caratteri speciali dalle funzionalità e dalle condizioni di corrispondenza che supportano le espressioni regolari.

Carattere speciale | Descrizione
------------------|------------
\ | Una barra rovesciata esegue l'escape del carattere che la segue. In questo modo il carattere viene trattato come un valore letterale anziché assumere il significato di espressione regolare. La sintassi seguente, ad esempio, ignora un asterisco: `\*`
% | Il significato di un simbolo di percentuale dipende dal relativo utilizzo.<br/><br/> `%{HTTPVariable}`: Questa sintassi identifica una variabile HTTP.<br/>`%{HTTPVariable%Pattern}`: Questa sintassi usa un simbolo di percentuale per identificare una variabile HTTP e come delimitatore.<br />`\%`: L'escape di un simbolo di percentuale ne consente l'utilizzo come valore letterale o per indicare la codifica URL (ad `\%20`esempio,).
\* | Un asterisco consente al carattere precedente di essere messo in corrispondenza zero o più volte.
Spazio | Un carattere di spazio in genere è considerato come un carattere letterale.
"value" | Le virgolette singole vengono trattate come caratteri letterali. Un set di virgolette singole non ha un significato speciale.

## <a name="next-steps"></a>Passaggi successivi

- [Condizioni di corrispondenza del motore regole](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Espressioni condizionali del motore regole](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funzionalità del motore regole](cdn-verizon-premium-rules-engine-reference-features.md)
- [Override del comportamento HTTP predefinito mediante il motore regole](cdn-verizon-premium-rules-engine.md)
- [Panoramica della rete CDN di Azure](cdn-overview.md)