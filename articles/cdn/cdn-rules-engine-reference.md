---
title: Riferimento del motore regole della rete CDN di Azure | Documentazione Microsoft
description: "Documentazione di riferimento per le funzionalità e condizioni di corrispondenza del motore regole della rete CDN di Azure."
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
translationtype: Human Translation
ms.sourcegitcommit: dccb945e170bd3e3f23283359db25e574a2d4296
ms.openlocfilehash: c10145661a8c575381493c9aaa901c3ef92c2e81


---
# <a name="azure-cdn-rules-engine"></a>Motore regole della rete CDN di Azure
In questo argomento sono riportate descrizioni dettagliate delle condizioni di corrispondenza e delle funzionalità disponibili per il [motore regole](cdn-rules-engine.md)della rete per la distribuzione di contenuti (CDN) di Azure.

Il motore regole HTTP è progettato per essere l'autorità finale sul modo in cui i tipi di richieste specifici vengono elaborati dalla rete CDN.

**Usi comuni**:

- Eseguire l'override o definire un criterio della cache personalizzato.
- Proteggere o negare le richieste di contenuti sensibili.
- Reindirizzare le richieste.
- Archiviare i dati di log personalizzati.

## <a name="terminology"></a>Terminologia
Una regola è definita con l'utilizzo di [ **espressioni condizionali**](cdn-rules-engine-reference-conditional-expressions.md), [ **corrispondenze**](cdn-rules-engine-reference-match-conditions.md) e [ **funzionalità**](cdn-rules-engine-reference-features.md). Questi elementi vengono evidenziati nella figura seguente.

 ![Condizione di corrispondenza della rete CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Sintassi

Il modo in cui verranno trattati i caratteri speciali varia a seconda del modo in cui una condizione di corrispondenza o funzionalità gestisce i valori di testo. Una condizione di corrispondenza o funzionalità potrebbe interpretare il testo in uno dei modi seguenti:

1. [**Valori letterali**](#literal-values) 
2. [**Valori caratteri jolly**](#wildcard-values)
3. [**Espressioni regolari**](#regular-expressions)

### <a name="literal-values"></a>Valori letterali
Il testo che viene interpretato come valore letterale tratterà tutti i caratteri speciali, fatta eccezione per il simbolo %, come parte del valore da mettere in corrispondenza. In altre parole, una condizione di corrispondenza letterale impostata su `\'*'\` verrà soddisfatta solo laddove si troverà tale valore (ad esempio, `\'*'\`).
 
Un simbolo di percentuale viene utilizzato per indicare la codifica URL (ad esempio `%20`).

### <a name="wildcard-values"></a>Valori caratteri jolly
Il testo che viene interpretato come un valore carattere jolly assegnerà un significato aggiuntivo ai caratteri speciali. Nella tabella seguente viene descritto come verrà interpretato il seguente set di caratteri.

Character | Descrizione
----------|------------
\ | Una barra rovesciata viene utilizzata per eseguire l'escape di ogni carattere specificato in questa tabella. Una barra rovesciata deve essere specificata direttamente prima del carattere speciale che deve essere sottoposto a escape.<br/>La sintassi seguente, ad esempio, ignora un asterisco: `\*`
% | Un simbolo di percentuale viene utilizzato per indicare la codifica URL (ad esempio, `%20`).
* | L'asterisco è un carattere jolly che rappresenta uno o più caratteri.
Spazio | Un carattere di spazio indica che una condizione di corrispondenza può essere soddisfatta da uno dei valori o modelli specificati.
"value" | Una virgoletta singola non ha un significato speciale. Tuttavia, un set di virgolette singole viene utilizzato per indicare che un valore deve essere considerato come valore letterale. Può essere usato nei modi seguenti:<br><br/>- Consente una condizione di corrispondenza da soddisfare ogni volta che il valore specificato corrisponde a una parte qualsiasi del valore di confronto.  Ad esempio, `'ma'` corrisponde a una qualsiasi delle seguenti stringhe: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- Consente a un carattere speciale di essere specificato come carattere letterale. Ad esempio, è possibile specificare un carattere di spazio letterale racchiudendo un carattere di spazio all'interno di un set di virgolette singole (ad esempio, `' '` o `'sample value'`).<br/>- Consente di specificare un valore vuoto. Specificare un valore vuoto indicando un set di virgolette singole (ad esempio '').<br /><br/>**Importante:**<br/>- Se il valore specificato non contiene un carattere jolly, verrà automaticamente considerato un valore letterale. Ciò significa che non è necessario specificare un set di virgolette singole.<br/>- Se una barra rovesciata non effettua l'escape di un altro carattere in questa tabella, questo verrà ignorato se specificato all'interno di un set di virgolette singole.<br/>- Un altro modo per specificare un carattere speciale come un carattere letterale è quello di effettuare l'escape con una barra rovesciata (ad esempio, `\`).

### <a name="regular-expressions"></a>Espressioni regolari

Le espressioni regolari definiscono un modello che verrà cercato all'interno di un valore di testo. La notazione delle espressioni regolari definisce un significato specifico per una varietà di simboli. La tabella seguente indica come vengono trattati i caratteri speciali dalle funzionalità e dalle condizioni di corrispondenza che supportano le espressioni regolari.

Carattere speciale | Descrizione
------------------|------------
\ | Una barra rovesciata esegue l'escape del carattere che la segue. In questo modo il carattere deve essere trattato come un valore letterale anziché adottare il significato di espressione regolare. La sintassi seguente, ad esempio, ignora un asterisco: `\*`
% | Il significato di un simbolo di percentuale dipende dal relativo utilizzo.<br/><br/> `%{HTTPVariable}`: questa sintassi identifica una variabile HTTP.<br/>`%{HTTPVariable%Pattern}`: questa sintassi usa un simbolo di percentuale per identificare una variabile HTTP e come delimitatore.<br />`\%`: l'escape di un simbolo di percentuale consente di utilizzare tale simbolo come valore letterale o per indicare la codifica URL (ad esempio, `\%20`).
* | Un asterisco consente al carattere precedente di essere messo in corrispondenza zero o più volte. 
Spazio | Un carattere di spazio in genere è considerato come un carattere letterale. 
"value" | Le virgolette singole vengono trattate come caratteri letterali. Un set di virgolette singole non ha un significato speciale.


## <a name="next-steps"></a>Passaggi successivi
* [Condizioni di corrispondenza del motore regole](cdn-rules-engine-reference-match-conditions.md)
* [Espressioni condizionali del motore regole](cdn-rules-engine-reference-conditional-expressions.md)
* [Funzionalità del motore regole](cdn-rules-engine-reference-features.md)
* [Override del comportamento HTTP predefinito mediante il motore di regole](cdn-rules-engine.md)
* [Panoramica della rete CDN di Azure](cdn-overview.md)



<!--HONumber=Jan17_HO4-->


