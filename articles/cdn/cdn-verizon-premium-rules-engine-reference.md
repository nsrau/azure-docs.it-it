---
title: Riferimento del motore regole della rete CDN di Azure | Documentazione Microsoft
description: Documentazione di riferimento per le funzionalità e condizioni di corrispondenza del motore regole della rete CDN di Azure.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: 75633521474ec3bcbc35cea49ea7a2da6a271e01
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83872521"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Informazioni di riferimento del motore regole della rete CDN di Azure da Verizon Premium

Questo articolo fornisce descrizioni dettagliate delle funzionalità e dalle condizioni di corrispondenza disponibili per il [motore regole](cdn-verizon-premium-rules-engine.md) della rete per la distribuzione di contenuti (rete CDN) di Azure.

Il motore regole è progettato per essere l'autorità finale sul modo in cui i tipi di richieste specifici vengono elaborati dalla rete CDN.

**Usi comuni**:

- Eseguire l'override o definire un criterio della cache personalizzato.
- Proteggere o negare le richieste di contenuti sensibili.
- Reindirizzare le richieste.
- Archiviare i dati di log personalizzati.
## <a name="key-concepts"></a>Concetti chiave
Di seguito sono descritti i concetti chiave per la configurazione del motore regole.
### <a name="draft"></a>Draft
La bozza di un criterio è costituita da una o più regole destinate all'identificazione delle richieste e il set di azioni da applicare. Una bozza è un lavoro in corso che consente aggiornamenti frequenti della configurazione senza influire sul traffico del sito. Quando una bozza è pronta per essere finalizzata, deve essere convertita in un criterio di sola lettura.

### <a name="rule"></a>Regola
Una regola identifica uno o più tipi di richieste e il set di azioni da applicare.

È costituita da: 

- Un set di espressioni condizionali che definiscono la logica con la quale vengono identificate le richieste.
- Un set di condizioni di corrispondenza che definiscono i criteri usati per identificare le richieste.
- Un set di funzionalità che definiscono la modalità di gestione delle richieste suddette da parte della rete CDN.
Questi elementi sono identificati nella figura seguente.

![Flusso di lavoro per la distribuzione dei criteri](./media/cdn-verizon-premium-rules-engine-reference/verizon-rules-engine-reference.png)

### <a name="policy"></a>Policy
Un criterio, costituito da un set di regole di sola lettura, offre i mezzi per:

- Creare, archiviare e gestire più varianti delle regole.
- Eseguire il rollback a una versione distribuita in precedenza.
- Preparare in anticipo regole specifiche di un evento (ad esempio, una regola che reindirizza il traffico a seguito di un intervento di manutenzione dell'origine di un cliente).

> [!NOTE]
> Sebbene sia consentito un solo criterio per ambiente, i criteri possono essere distribuiti come necessario.

### <a name="deploy-request"></a>Richiesta di distribuzione
Una richiesta di distribuzione rappresenta una procedura semplice e lineare attraverso la quale è possibile applicare rapidamente criteri all'ambiente di staging o di produzione. È disponibile la cronologia delle richieste di distribuzione per facilitare il rilevamento delle modifiche applicate a tali ambienti.

> [!NOTE]
> Solo le richieste che non superano la convalida automatica e il sistema di rilevamento degli errori richiedono la revisione e l'approvazione manuali.

### <a name="rule-precedence"></a>Precedenza delle regole
Le regole contenute in un criterio vengono in genere elaborate nell'ordine in cui sono elencate (ad esempio, dall'alto verso il basso). Se la richiesta soddisfa regole in conflitto, l'ultima regola da elaborare avrà la precedenza.

### <a name="policy-deployment-workflow"></a>Flusso di lavoro per la distribuzione dei criteri
Di seguito è illustrato il flusso di lavoro tramite il quale un criterio viene applicato all'ambiente di produzione o di staging.

![Flusso di lavoro per la distribuzione dei criteri](./media/cdn-verizon-premium-rules-engine-reference/policy-deployment-workflow.png)

|Passaggio |Descrizione |
|---------|---------|
|[Creazione della bozza](https://docs.vdms.com/cdn/index.html#HRE/AdministeringDraftsandRules.htm#Create)    |    Una bozza è costituita da un set di regole che definiscono la modalità di gestione delle richieste di contenuto da parte della rete CDN.     |
|Blocco della bozza   |     Una volta finalizzata, la bozza deve essere bloccata e convertita in un criterio di sola lettura.    |
|[Invio richiesta di distribuzione](https://docs.vdms.com/cdn/index.html#HRE/DeployRequest.htm)   |   <br> Una richiesta di distribuzione consente di applicare un criterio a traffico di test o di produzione.</br> <br>Inviare una richiesta di distribuzione all'ambiente di staging o di produzione.</br>     |
|Revisione richiesta di distribuzione   |    <br>Una richiesta di distribuzione viene sottoposta alla convalida automatica e al rilevamento di errori in modo automatizzato.</br><br>Sebbene la maggior parte delle richieste di distribuzione venga approvata automaticamente, la revisione manuale è necessaria per i criteri più complessi.</br>   |
|Distribuzione del criterio ([staging](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Staging))   |  <br> Al momento dell'approvazione di una richiesta di distribuzione nell'ambiente di staging, il criterio viene applicato a questo ambiente, che consente di testare un criterio a fronte di traffico del sito fittizio.</br><br>Quando il criterio è pronto per essere applicato al traffico del sito in tempo reale, è necessario inviare una nuova richiesta di distribuzione per l'ambiente di produzione.</br>      |
|Distribuzione del criterio ([produzione](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Producti))   |  Al momento dell'approvazione di una richiesta di distribuzione nell'ambiente di produzione, il criterio viene applicato a questo ambiente, che consente a un criterio di fungere da autorità finale per determinare la modalità di gestione del traffico in tempo reale da parte della rete CDN.     |
## <a name="syntax"></a>Sintassi

Il modo in cui verranno trattati i caratteri speciali varia a seconda del modo in cui una condizione di corrispondenza o funzionalità gestisce i valori di testo. Una condizione di corrispondenza o funzionalità potrebbe interpretare il testo in uno dei modi seguenti:

- [**Valori letterali**](#literal-values)
- [**Valori caratteri jolly**](#wildcard-values)
- [**Espressioni regolari**](#regular-expressions)

### <a name="literal-values"></a>Valori letterali

Il testo che viene interpretato come valore letterale tratta tutti i caratteri speciali, fatta eccezione per il simbolo %, come parte del valore di cui trovare la corrispondenza. In altre parole, una condizione di corrispondenza letterale impostata su `\'*'\` viene soddisfatta solo se viene trovato tale valore (ad esempio, `\'*'\`).

Per indicare la codifica URL viene usato un simbolo di percentuale (ad esempio, `%20`).

### <a name="wildcard-values"></a>Valori caratteri jolly

Il testo che viene interpretato come un valore carattere jolly assegna un significato aggiuntivo ai caratteri speciali. La tabella di seguito descrive come viene interpretato il set di caratteri seguente:

Carattere | Descrizione
----------|------------
\ | Una barra rovesciata viene utilizzata per eseguire l'escape di ogni carattere specificato in questa tabella. Una barra rovesciata deve essere specificata direttamente prima del carattere speciale che deve essere sottoposto a escape.<br/>La sintassi seguente, ad esempio, ignora un asterisco: `\*`
% | Per indicare la codifica URL viene usato un simbolo di percentuale (ad esempio, `%20`).
\* | L'asterisco è un carattere jolly che rappresenta uno o più caratteri.
Space | Un carattere di spazio indica che una condizione di corrispondenza può essere soddisfatta da uno dei valori o modelli specificati.
"value" | Una virgoletta singola non ha un significato speciale. Tuttavia, un set di virgolette singole viene utilizzato per indicare che un valore deve essere considerato come valore letterale. Può essere usato nei modi seguenti:<br><br/>- Consente una condizione di corrispondenza da soddisfare ogni volta che il valore specificato corrisponde a una parte qualsiasi del valore di confronto.  Ad esempio, `'ma'` corrisponde a una qualsiasi delle seguenti stringhe: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- Consente a un carattere speciale di essere specificato come carattere letterale. Ad esempio, è possibile specificare un carattere spazio letterale racchiudendo uno spazio all'interno di un set di virgolette singole (ad esempio, `' '` o `'sample value'`).<br/>- Consente di specificare un valore vuoto. Specificare un valore vuoto indicando un set di virgolette singole (ovvero '').<br /><br/>**Importante:**<br/>-Se il valore specificato non contiene un carattere jolly, viene automaticamente considerato un valore letterale. Questo significa che non è necessario specificare un set di virgolette singole.<br/>- Se una barra rovesciata non effettua l'escape di un altro carattere in questa tabella, questo viene ignorato se specificato all'interno di un set di virgolette singole.<br/>- Un altro modo per specificare un carattere speciale come un carattere letterale è quello di effettuare l'escape con una barra rovesciata (ovvero `\`).

### <a name="regular-expressions"></a>Espressioni regolari

Le espressioni regolari definiscono un modello che viene cercato all'interno di un valore di testo. La notazione delle espressioni regolari definisce un significato specifico per una varietà di simboli. La tabella seguente indica come vengono trattati i caratteri speciali dalle funzionalità e dalle condizioni di corrispondenza che supportano le espressioni regolari.

Carattere speciale | Descrizione
------------------|------------
\ | Una barra rovesciata esegue l'escape del carattere che la segue. In questo modo il carattere viene trattato come un valore letterale anziché assumere il significato di espressione regolare. La sintassi seguente, ad esempio, ignora un asterisco: `\*`
% | Il significato di un simbolo di percentuale dipende dal relativo utilizzo.<br/><br/> `%{HTTPVariable}`: questa sintassi identifica una variabile HTTP.<br/>`%{HTTPVariable%Pattern}`: questa sintassi usa un simbolo di percentuale per identificare una variabile HTTP e come delimitatore.<br />`\%`: l'escape di un simbolo di percentuale consente di usare tale simbolo come valore letterale o per indicare la codifica URL (ad esempio, `\%20`).
\* | Un asterisco consente al carattere precedente di essere messo in corrispondenza zero o più volte.
Space | Un carattere di spazio in genere è considerato come un carattere letterale.
"value" | Le virgolette singole vengono trattate come caratteri letterali. Un set di virgolette singole non ha un significato speciale.

Le condizioni di corrispondenza e le funzionalità che supportano espressioni regolari accettano modelli definiti dalla libreria PCRE (Perl Compatible Regular Expressions).



## <a name="next-steps"></a>Passaggi successivi

- [Condizioni di corrispondenza del motore regole](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Espressioni condizionali del motore regole](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funzionalità del motore regole](cdn-verizon-premium-rules-engine-reference-features.md)
- [Override del comportamento HTTP predefinito mediante il motore regole](cdn-verizon-premium-rules-engine.md)
- [Panoramica della rete CDN di Azure](cdn-overview.md)
