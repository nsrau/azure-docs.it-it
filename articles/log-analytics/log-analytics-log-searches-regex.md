---
title: Espressioni regolari nelle ricerche log di Azure Log Analytics | Microsoft Docs
description: "È possibile usare la parola chiave RegEx nelle ricerche di log in Log Analytics per filtrare i risultati in base a un'espressione regolare.  In questo articolo viene descritta la sintassi per queste espressioni con diversi esempi."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: bwren
ms.openlocfilehash: 8915e0e35951871ff10fd84453d55bd5102e97df
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2018
---
# <a name="using-regular-expressions-to-filter-log-searches-in-log-analytics"></a>Uso di espressioni regolari per filtrare ricerche log in Log Analytics

>[!NOTE]
> Questo articolo descrive le espressioni regolari usando il linguaggio di query legacy di Log Analytics.  Se l'area di lavoro è stata aggiornata al [nuovo linguaggio di query di Log Analytics](log-analytics-log-search-upgrade.md), è consigliabile fare riferimento a [Regular expressions](https://docs.loganalytics.io/docs/Language-Reference/References/Regular-Expressions-syntax) (Espressioni regolari) nella documentazione del linguaggio.


Le [ricerche log](log-analytics-log-searches.md) consentono di estrarre informazioni dall'area di lavoro di Log Analytics.  Le [espressioni di filtro](log-analytics-search-reference.md#filter-expressions) consentono di filtrare i risultati della ricerca in base a criteri specifici.  La parola chiave **RegEx** consente di specificare un'espressione regolare per il filtro.  

Questo articolo contiene informazioni dettagliate sulla sintassi di espressioni regolari usate da Log Analytics.

> [!NOTE]
> È possibile usare solo RegEx con i campi ricercabili.  Per altre informazioni sui campi ricercabili, vedere **Tipi di campo** in [Trovare dati con ricerche nei log in Log Analytics](log-analytics-log-searches.md#use-additional-filters).


## <a name="regex-keyword"></a>Parola chiave RegEx

Servirsi della sintassi seguente per usare la parola chiave **RegEx** in una ricerca log.  È possibile usare le altre sezioni in questo articolo per determinare la sintassi dell'espressione regolare stessa.

    field:Regex("Regular Expression")
    field=Regex("Regular Expression")

Ad esempio, per usare un'espressione regolare per restituire i record degli avvisi con un tipo di *avviso* o *errore*, usare la ricerca log seguente.

    Type=Alert AlertSeverity=RegEx("Warning|Error")

## <a name="partial-matches"></a>Corrispondenze parziali
Si noti che l'espressione regolare deve corrispondere all'intero testo della proprietà.  Le corrispondenze parziali non restituiranno alcun record.  Ad esempio, se si sta tentando di restituire i record da un computer denominato srv01.contoso.com, la ricerca log seguente **non** restituirà alcun record.

    Computer=RegEx("srv..")

Questo perché solo la prima parte del nome corrisponde all'espressione regolare.  Le due ricerche log seguenti restituirebbero dei record da questo computer perché corrispondono all'intero nome.

    Computer=RegEx("srv..@")
    Computer=RegEx("srv...contoso.com")

## <a name="characters"></a>Caratteri
Specificare caratteri diversi.

| Character | DESCRIZIONE | Esempio | Corrispondenze di esempio |
|:--|:--|:--|:--|
| a | Una occorrenza del carattere. | Computer=RegEx("srv01.contoso.com") | srv01.contoso.com |
| . | Qualsiasi carattere singolo. | Computer=RegEx("srv...contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| a? | Nessuna o una occorrenza del carattere. | Computer=RegEx("srv01?.contoso.com") | srv0.contoso.com<br>srv01.contoso.com |
| a* | Nessuna o più occorrenze del carattere. | Computer=RegEx("srv01*.contoso.com") | srv0.contoso.com<br>srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| a+ | Una o più occorrenze del carattere. | Computer=RegEx("srv01+.contoso.com") | srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| [*abc*] | Corrispondenza di qualsiasi carattere singolo tra parentesi | Computer=RegEx("srv0[123].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| [*a*-*z*] | Corrispondenza di un carattere singolo nell'intervallo.  Può includere più intervalli. | Computer=RegEx("srv0[1-3].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| [^*abc*] | Nessuno dei caratteri tra parentesi | Computer=RegEx("srv0[^123].contoso.com") | srv05.contoso.com<br>srv06.contoso.com<br>srv07.contoso.com |
| [^*a*-*z*] | Nessuno dei caratteri nell'intervallo. | Computer=RegEx("srv0[^1-3].contoso.com") | srv05.contoso.com<br>srv06.contoso.com<br>srv07.contoso.com |
| [*n*-*m*] | Corrispondenza di un intervallo di caratteri numerici. | Computer=RegEx("srv[01-03].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| @ | Qualsiasi stringa di caratteri. | Computer=RegEx("srv@.contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |


## <a name="multiple-occurences-of-character"></a>Più occorrenze del carattere
Specificare più occorrenze di un carattere specifico.

| Character | DESCRIZIONE | Esempio | Corrispondenze di esempio |
|:--|:--|:--|:--|
| a{n} |  *n* occorrenze del carattere. | Computer=RegEx("bw-win-sc01{3}.bwren.lab") | bw-win-sc0111.bwren.lab |
| a{n,} |  *n* o più occorrenze del carattere. | Computer=RegEx("bw-win-sc01{3,}.bwren.lab") | bw-win-sc0111.bwren.lab<br>bw-win-sc01111.bwren.lab<br>bw-win-sc011111.bwren.lab<br>bw-win-sc0111111.bwren.lab |
| a{n,m} |  *n* a *m* occorrenze del carattere. | Computer=RegEx("bw-win-sc01{3,5}.bwren.lab") | bw-win-sc0111.bwren.lab<br>bw-win-sc01111.bwren.lab<br>bw-win-sc011111.bwren.lab |


## <a name="logical-expressions"></a>Espressioni logiche
Selezionare più valori.

| Character | DESCRIZIONE | Esempio | Corrispondenze di esempio |
|:--|:--|:--|:--|
| &#124; | OR logico.  Restituisce un risultato se corrisponde a una delle espressioni. | Type=Alert AlertSeverity=RegEx("Warning&#124;Error") | Avviso<br>Tipi di errore |
| & | AND logico.  Restituisce un risultato se corrisponde a entrambe le espressioni | EventData=regex("(Security.\*&.\*success.\*)") | Controllo della sicurezza eseguito correttamente |


## <a name="literals"></a>Valori letterali
Convertire i caratteri speciali in caratteri letterali.  Inclusi i caratteri che danno funzionalità alle espressioni regolari, ad esempio?-\*^\[\]{}\(\)+\|.&.

| Character | DESCRIZIONE | Esempio | Corrispondenze di esempio |
|:--|:--|:--|:--|
| \\ | Converte un carattere speciale in un valore letterale. | Status_CF=\\[Error\\]@<br>Status_CF=Error\\-@ | [Error]File non trovato.<br>Errore-File non trovato. |


## <a name="next-steps"></a>Passaggi successivi

* Acquisire familiarità con le [ricerche log](log-analytics-log-searches.md) per visualizzare e analizzare i dati nell'area di lavoro di Log Analytics.
