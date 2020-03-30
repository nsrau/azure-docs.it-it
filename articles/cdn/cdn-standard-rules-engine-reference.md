---
title: Informazioni di riferimento sul motore di regole standard per la rete CDN di Azure Documenti Microsoft
description: Documentazione di riferimento per le condizioni di corrispondenza e le azioni nel motore delle regole standard per la rete per la distribuzione di contenuti di Azure .COM.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: aa401150ee7a0f02e809ad702b8247e18081c8a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74171570"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Standard rules engine reference for Azure CDN

Nel [motore regole standard](cdn-standard-rules-engine.md) per la rete per la distribuzione di contenuti di Azure una regola è costituita da una o più condizioni di corrispondenza e da un'azione. Questo articolo fornisce descrizioni dettagliate delle condizioni di corrispondenza e delle funzionalità disponibili nel motore regole Standard per la rete CDN di Azure.This article provides detailed descriptions of the match conditions and features that are available in the Standard rules engine for Azure CDN.

The rules engine is designed to be the final authority on how specific types of requests are processed by Standard Azure CDN.

**Usi comuni per le regole**:

- Eseguire l'override o definire un criterio della cache personalizzato.
- Reindirizzare le richieste.
- Modificare le intestazioni di richiesta e risposta HTTP.

## <a name="terminology"></a>Terminologia

Per definire una regola nel motore regole, impostare [le condizioni](cdn-standard-rules-engine-match-conditions.md) di corrispondenza e le [azioni:](cdn-standard-rules-engine-actions.md)

 ![Struttura delle regole della rete CDN di AzureAzure CDN rules structure](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Ogni regola può avere fino a quattro condizioni di corrispondenza e tre azioni. Ogni endpoint della rete CDN di Azure può avere fino a cinque regole. 

Incluso nel limite di cinque regole corrente per un endpoint della rete CDN di Azure è una *regola globale*predefinita. La regola globale non ha condizioni di corrispondenza e le azioni definite in una regola globale vengono sempre attivate.

## <a name="syntax"></a>Sintassi

Il modo in cui i caratteri speciali vengono trattati in una regola varia in base al modo in cui le diverse condizioni di corrispondenza e le azioni gestiscono i valori di testo. Una condizione o un'azione di corrispondenza può interpretare il testo in uno dei modi seguenti:A match condition or action can interpret text in one of the following ways:

- [Valori letterali](#literal-values)
- [Valori con caratteri jolly](#wildcard-values)


### <a name="literal-values"></a>Valori letterali

Il testo interpretato come valore letterale considera tutti i caratteri speciali *ad eccezione del simbolo %* come parte del valore che deve essere trovato in una regola. Ad esempio, una condizione `'*'` di corrispondenza letterale `'*'` impostata su viene soddisfatta solo quando viene trovato il valore esatto.

Un segno di percentuale viene utilizzato `%20`per indicare la codifica URL, ad esempio ).

### <a name="wildcard-values"></a>Valori caratteri jolly

Il testo interpretato come valore jolly assegna un significato aggiuntivo ai caratteri speciali. Nella tabella seguente viene descritto come vengono interpretati caratteri speciali specifici nel motore delle regole Standard:The following table describes how specific special characters are interpreted in the Standard rules engine:

Carattere | Descrizione
----------|------------
\ | Una barra rovesciata viene utilizzata per eseguire l'escape di ogni carattere specificato in questa tabella. Una barra rovesciata deve essere specificata direttamente prima del carattere speciale che deve essere sottoposto a escape. La sintassi seguente, ad esempio, ignora un asterisco: `\*`
% | Un segno di percentuale viene utilizzato `%20`per indicare la codifica URL, ad esempio ).
\* | L'asterisco è un carattere jolly che rappresenta uno o più caratteri.
space | Un carattere di spazio indica che una condizione di corrispondenza può essere soddisfatta da uno dei valori o dei modelli specificati.
virgolette singole | Una virgoletta singola non ha un significato speciale. Tuttavia, un set di virgolette singole indica che un valore deve essere considerato come un valore letterale. Le virgolette singole possono essere utilizzate nei seguenti modi:<ul><li>Per consentire la condizione di corrispondenza da soddisfare ogni volta che il valore specificato corrisponde a qualsiasi parte del valore di confronto.  Ad esempio, `'ma'` corrisponde a una qualsiasi delle seguenti stringhe: <ul><li>/business/**ma**rathon/asset.htm</li><li>**ma**p.gif</li><li>/business/template.**ma**p</li></ul><li>Per consentire a un carattere speciale di essere specificato come carattere letterale. Ad esempio, è possibile specificare un carattere di spazio letterale`' '` racchiudendo un carattere di spazio in un set di virgolette singole ( o `'<sample value>'`).</li><li>Per consentire l'specificare un valore vuoto. Specificare un valore vuoto specificando un set di virgolette singole (**''**).</li></ul>**Importante**:<br /><ul><li>Se il valore specificato non contiene un carattere jolly, il valore viene automaticamente considerato un valore letterale. Non è necessario specificare un set di virgolette singole per un valore letterale.</li><li>Se una barra rovesciata non viene utilizzata per eseguire l'escape di un altro carattere in questa tabella, la barra rovesciata viene ignorata quando viene specificata in un set di virgolette singole.</li><li>Un altro modo per specificare un carattere speciale come carattere letterale consiste nell'eseguirne l'escape utilizzando una barra rovesciata ( ).`\`</li></ul>

## <a name="next-steps"></a>Passaggi successivi

- [Condizioni di corrispondenza nel motore regole StandardMatch conditions in the Standard rules engine](cdn-standard-rules-engine-match-conditions.md)
- [Azioni nel motore regole StandardActions in the Standard rules engine](cdn-standard-rules-engine-actions.md)
- [Applicare HTTPS usando il motore regole standard](cdn-standard-rules-engine.md)
- [Panoramica della rete CDN di Azure](cdn-overview.md)
