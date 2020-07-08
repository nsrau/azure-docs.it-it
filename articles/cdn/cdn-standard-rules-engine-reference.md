---
title: Guida di riferimento al motore regole standard per la rete CDN di Azure | Microsoft Docs
description: Documentazione di riferimento per le condizioni di corrispondenza e le azioni nel motore regole standard per la rete per la distribuzione di contenuti di Azure (rete CDN di Azure).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 06/22/2020
ms.author: allensu
ms.openlocfilehash: 6260a4b78197329e020bebaa3bc08db5ad792086
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559310"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Informazioni di riferimento sul motore regole standard per la rete CDN di Azure

Nel [motore delle regole standard](cdn-standard-rules-engine.md) per la rete per la distribuzione di contenuti di Azure (rete CDN di Azure), una regola è costituita da una o più condizioni di corrispondenza e da un'azione. Questo articolo fornisce descrizioni dettagliate delle condizioni di corrispondenza e delle funzionalità disponibili nel motore delle regole standard per la rete CDN di Azure.

Il motore regole è progettato per essere l'autorità finale sul modo in cui i tipi di richieste specifici vengono elaborati dalla rete CDN standard di Azure.

**Usi comuni per le regole**:

- Eseguire l'override o definire un criterio della cache personalizzato.
- Reindirizzare le richieste.
- Modificare le intestazioni di richiesta e risposta HTTP.

## <a name="terminology"></a>Terminologia

Per definire una regola nel motore regole, impostare [le condizioni](cdn-standard-rules-engine-match-conditions.md) e le [azioni](cdn-standard-rules-engine-actions.md)di corrispondenza:

 ![Struttura delle regole della rete CDN di Azure](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Ogni regola può avere fino a dieci condizioni di corrispondenza e cinque azioni. Ogni endpoint della rete CDN di Azure può avere fino a 25 regole. 

Incluso in questo limite è una *regola globale*predefinita. La regola globale non presenta condizioni di corrispondenza; le azioni definite in una regola globale vengono sempre attivate.

## <a name="limits-and-pricing"></a>Limiti e prezzi 

Ogni endpoint della rete CDN di Azure può avere fino a 25 regole. Ogni regola può avere fino a dieci condizioni di corrispondenza e cinque azioni. I prezzi per il motore regole seguono le dimensioni seguenti: 
- Regole: $1 per regola al mese 
- Richieste elaborate: $0,60 per milione di requets
- Le prime 5 regole rimarranno gratuite

## <a name="syntax"></a>Sintassi

Il modo in cui i caratteri speciali vengono trattati in una regola varia a seconda del modo in cui le diverse condizioni di corrispondenza e azioni gestiscono valori di testo Una condizione di corrispondenza o un'azione può interpretare il testo in uno dei modi seguenti:

- [Valori letterali](#literal-values)
- [Valori caratteri jolly](#wildcard-values)


### <a name="literal-values"></a>Valori letterali

Il testo interpretato come valore letterale considera tutti i caratteri speciali, *ad eccezione del simbolo%* come parte del valore di cui è necessario trovare una corrispondenza in una regola. Ad esempio, una condizione di corrispondenza letterale impostata su `'*'` viene soddisfatta solo quando `'*'` viene trovato il valore esatto.

Viene usato un segno di percentuale per indicare la codifica URL (ad esempio, `%20` ).

### <a name="wildcard-values"></a>Valori caratteri jolly

Il testo interpretato come un valore di carattere jolly assegna un significato aggiuntivo ai caratteri speciali. Nella tabella seguente vengono descritte le modalità di interpretazione dei caratteri speciali specifici nel motore regole standard:

Carattere | Descrizione
----------|------------
\ | Una barra rovesciata viene utilizzata per eseguire l'escape di ogni carattere specificato in questa tabella. Una barra rovesciata deve essere specificata direttamente prima del carattere speciale che deve essere sottoposto a escape. La sintassi seguente, ad esempio, ignora un asterisco: `\*`
% | Viene usato un segno di percentuale per indicare la codifica URL (ad esempio, `%20` ).
\* | L'asterisco è un carattere jolly che rappresenta uno o più caratteri.
space | Un carattere di spazio indica che una condizione di corrispondenza può essere soddisfatta da uno dei valori o modelli specificati.
virgolette singole | Una virgoletta singola non ha un significato speciale. Tuttavia, un set di virgolette singole indica che un valore deve essere trattato come valore letterale. È possibile utilizzare le virgolette singole nei modi seguenti:<ul><li>Per consentire la soddisfazione di una condizione di corrispondenza ogni volta che il valore specificato corrisponde a una parte del valore di confronto.  Ad esempio, `'ma'` corrisponde a una qualsiasi delle seguenti stringhe: <ul><li>/business/**ma**rathon/asset.htm</li><li>**ma**p.gif</li><li>/business/template.**ma**p</li></ul><li>Per consentire a un carattere speciale di essere specificato come carattere letterale. Ad esempio, è possibile specificare un carattere spazio letterale racchiudendo uno spazio in un set di virgolette singole ( `' '` o `'<sample value>'` ).</li><li>Per consentire la specifica di un valore vuoto. Specificare un valore vuoto specificando un set di virgolette singole (**''**).</li></ul>**Importante**:<br /><ul><li>Se il valore specificato non contiene un carattere jolly, il valore viene automaticamente considerato un valore letterale. Non è necessario specificare un set di virgolette singole per un valore letterale.</li><li>Se una barra rovesciata non viene utilizzata per eseguire l'escape di un altro carattere in questa tabella, la barra rovesciata viene ignorata quando viene specificata in un set di virgolette singole.</li><li>Un altro modo per specificare un carattere speciale come carattere letterale consiste nell'eseguire l'escape usando una barra rovesciata ( `\` ).</li></ul>

## <a name="next-steps"></a>Passaggi successivi

- [Condizioni di corrispondenza del motore regole standard](cdn-standard-rules-engine-match-conditions.md)
- [Azioni del motore regole standard](cdn-standard-rules-engine-actions.md)
- [Applicare HTTPS usando il motore regole standard](cdn-standard-rules-engine.md)
- [Panoramica della rete CDN di Azure](cdn-overview.md)
