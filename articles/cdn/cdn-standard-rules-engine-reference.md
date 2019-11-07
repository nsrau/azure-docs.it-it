---
title: Riferimento al motore regole standard della rete CDN di Azure | Microsoft Docs
description: Documentazione di riferimento per le condizioni e le azioni del motore regole di rete CDN standard di Azure.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 6fb7e704f3d33cff8c29386b8aba9d8289037cbb
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615936"
---
# <a name="azure-cdn-from-microsoft-rules-engine-reference"></a>Riferimento al motore della rete CDN di Azure di Microsoft Rules

Questo articolo elenca le descrizioni dettagliate delle condizioni di corrispondenza e delle funzionalità disponibili per il [motore delle regole standard](cdn-standard-rules-engine.md)della rete per la distribuzione di contenuti (CDN) di Azure.

Il motore regole è progettato per essere l'autorità finale sul modo in cui i tipi di richieste specifici vengono elaborati dalla rete CDN.

**Usi comuni**:

- Eseguire l'override o definire un criterio della cache personalizzato.
- Reindirizzare le richieste.
- Modificare le intestazioni di richiesta e risposta HTTP

## <a name="terminology"></a>Terminologia

Una regola viene definita tramite l'utilizzo delle [**condizioni di corrispondenza**](cdn-standard-rules-engine-match-conditions.md)e delle [**azioni**](cdn-standard-rules-engine-actions.md). Questi elementi vengono evidenziati nella figura seguente:

 ![Struttura delle regole della rete CDN](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Ogni regola può avere un massimo di 4 condizioni di corrispondenza e 3 azioni. Per ogni endpoint della rete CDN è previsto un massimo di 5 regole. Inoltre, per impostazione predefinita è presente una regola denominata **regola globale**. Si tratta di una regola senza condizioni di corrispondenza, in cui le azioni definite all'interno di vengono sempre attivate. Questa regola è inclusa nel limite corrente di 5 regole.

## <a name="syntax"></a>Sintassi

Il modo in cui vengono trattati i caratteri speciali varia a seconda del modo in cui una condizione di corrispondenza o actopm gestisce i valori di testo. Una condizione di corrispondenza o funzionalità potrebbe interpretare il testo in uno dei modi seguenti:

1. [**Valori letterali**](#literal-values)
2. [**Valori caratteri jolly**](#wildcard-values)


### <a name="literal-values"></a>Valori letterali

Il testo che viene interpretato come valore letterale tratta tutti i caratteri speciali, fatta eccezione per il simbolo %, come parte del valore di cui trovare la corrispondenza. In altre parole, una condizione di corrispondenza letterale impostata su `\'*'\` viene soddisfatta solo se viene trovato tale valore (ad esempio, `\'*'\`).

Viene usato un segno di percentuale per indicare la codifica URL, ad esempio `%20`.

### <a name="wildcard-values"></a>Valori caratteri jolly

Il testo che viene interpretato come un valore carattere jolly assegna un significato aggiuntivo ai caratteri speciali. La tabella di seguito descrive come viene interpretato il set di caratteri seguente:

Character | Descrizione
----------|------------
\ | Una barra rovesciata viene utilizzata per eseguire l'escape di ogni carattere specificato in questa tabella. Una barra rovesciata deve essere specificata direttamente prima del carattere speciale che deve essere sottoposto a escape.<br/>La sintassi seguente, ad esempio, ignora un asterisco: `\*`
% | Viene usato un segno di percentuale per indicare la codifica URL, ad esempio `%20`.
\* | L'asterisco è un carattere jolly che rappresenta uno o più caratteri.
Spazio | Un carattere di spazio indica che una condizione di corrispondenza può essere soddisfatta da uno dei valori o modelli specificati.
"value" | Una virgoletta singola non ha un significato speciale. Tuttavia, un set di virgolette singole viene utilizzato per indicare che un valore deve essere considerato come valore letterale. Può essere usato nei modi seguenti:<br><br/>- Consente una condizione di corrispondenza da soddisfare ogni volta che il valore specificato corrisponde a una parte qualsiasi del valore di confronto.  Ad esempio, `'ma'` corrisponde a una qualsiasi delle seguenti stringhe: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- Consente a un carattere speciale di essere specificato come carattere letterale. Ad esempio, è possibile specificare un carattere spazio letterale racchiudendo uno spazio all'interno di un set di virgolette singole (ad esempio, `' '` o `'sample value'`).<br/>- Consente di specificare un valore vuoto. Specificare un valore vuoto indicando un set di virgolette singole (ovvero '').<br /><br/>**Importante:**<br/>-Se il valore specificato non contiene un carattere jolly, viene automaticamente considerato un valore letterale. Questo significa che non è necessario specificare un set di virgolette singole.<br/>- Se una barra rovesciata non effettua l'escape di un altro carattere in questa tabella, questo viene ignorato se specificato all'interno di un set di virgolette singole.<br/>- Un altro modo per specificare un carattere speciale come un carattere letterale è quello di effettuare l'escape con una barra rovesciata (ovvero `\`).

## <a name="next-steps"></a>Passaggi successivi

- [Condizioni di corrispondenza del motore regole standard](cdn-standard-rules-engine-match-conditions.md)
- [Azioni del motore regole standard](cdn-standard-rules-engine-actions.md)
- [Applicare HTTPS usando il motore regole standard](cdn-standard-rules-engine.md)
- [Panoramica della rete CDN di Azure](cdn-overview.md)
