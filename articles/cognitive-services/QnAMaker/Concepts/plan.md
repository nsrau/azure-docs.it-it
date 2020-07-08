---
title: Pianificare l'app-QnA Maker
description: Per pianificare l'app QnA Maker è necessario comprendere il funzionamento di QnA Maker e interagire con altri servizi di Azure, oltre ad alcuni concetti della Knowledge base.
ms.topic: conceptual
ms.date: 07/2/2020
ms.openlocfilehash: d19ec51aec7e71b6f040a03543f72af3aed09556
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85875709"
---
# <a name="plan-your-qna-maker-app"></a>Pianificare l'app QnA Maker

Per pianificare l'app QnA Maker è necessario comprendere il funzionamento di QnA Maker e interagire con altri servizi di Azure, oltre ad alcuni concetti della Knowledge base.

## <a name="azure-resources"></a>Risorse di Azure

Ogni [risorsa di Azure](azure-resources.md#resource-purposes) creata con QnA Maker ha uno scopo specifico. Poiché ogni risorsa ha i propri scopi, limiti e piano [tariffario](azure-resources.md#pricing-tier-considerations), è importante comprendere le operazioni eseguite da queste risorse come parte del processo di pianificazione.

|Risorsa|Scopo|
|--|--|
| Risorsa [QnA Maker](azure-resources.md#qna-maker-resource)|Creazione e stima di query|
| Risorsa [ricerca cognitiva](azure-resources.md#cognitive-search-resource)|Archiviazione e ricerca di dati|
| Risorsa del servizio [app e risorsa del servizio del piano app](azure-resources.md#app-service-and-app-service-plan)|Eseguire query sull'endpoint di stima|
| Risorsa [Application Insights](azure-resources.md#application-insights)|Telemetria stima query|

### <a name="resource-planning"></a>Pianificazione delle risorse

Durante l'apprendimento della creazione e della stima delle query, usando il livello gratuito, `F0` , di ogni risorsa funziona e fornirà l'esperienza di creazione e di stima delle query. Quando ci si sposta in uno scenario di produzione, in tempo reale, rivalutare la selezione della risorsa.

#### <a name="qna-maker-resource"></a>Risorsa QnA Maker

Una singola risorsa QnA Maker può ospitare più di una Knowledge base. Il numero di Knowledge base è determinato dalla quantità di indici supportati del piano tariffario ricerca cognitiva. Ulteriori informazioni sulla [relazione tra gli indici e le Knowledge base](azure-resources.md#index-usage).

#### <a name="knowledge-base-size-and-throughput"></a>Dimensioni e velocità effettiva della Knowledge base

Quando si prevede di compilare un'app reale, pianificare le risorse per le dimensioni della Knowledge base e le richieste di stima delle query che si prevede.

Una dimensione della Knowledge base viene controllata da:
* Limiti del piano tariffario per le [risorse ricerca cognitiva](../../../search/search-limits-quotas-capacity.md)
* [Limiti di QnA Maker](../limits.md)

La richiesta di stima della query della Knowledge base è controllata dal piano dell'app Web e dall'app Web. Vedere [le impostazioni consigliate](azure-resources.md#recommended-settings) per pianificare il piano tariffario.

### <a name="resource-sharing"></a>Condivisione delle risorse

Se si dispone già di alcune di queste risorse in uso, è possibile valutare la possibilità di condividere le risorse. Sebbene sia [possibile condividere](azure-resources.md#share-services-with-qna-maker)alcune risorse, si tratta di uno scenario avanzato.

Tutte le Knowledge base create nella stessa risorsa QnA Maker condividono lo stesso endpoint di stima della query di **test** .

### <a name="understanding-impact-of-resource-selection"></a>Informazioni sull'effetto della selezione delle risorse

La selezione corretta delle risorse significa che la Knowledge base risponde correttamente alle stime delle query.

Se la Knowledge base non funziona correttamente, il problema è in genere la gestione delle risorse non corretta.

La selezione di risorse non corretta richiede l'analisi per determinare quale [risorsa deve essere modificata](azure-resources.md#when-to-change-a-pricing-tier).

## <a name="knowledge-bases"></a>Knowledge base

Una Knowledge base è collegata direttamente alla risorsa QnA Maker e include le coppie Question e Answer (QnA) utilizzate per rispondere alle richieste di stima delle query.

### <a name="language-considerations"></a>Considerazioni sul linguaggio

La prima Knowledge base creata nella risorsa QnA Maker imposta la lingua per la risorsa. È possibile avere una sola lingua per una risorsa QnA Maker.

Strutturare le risorse QnA Maker in base alla lingua o utilizzare [Translator](../../translator/translator-info-overview.md) per modificare una query da un'altra lingua nella lingua della Knowledge base prima di inviare la query all'endpoint di stima della query.

### <a name="ingesting-data-sources"></a>Inserimento di origini dati

Le [origini dati](knowledge-base.md)inserite, utilizzate per creare una Knowledge base, possono essere una delle seguenti:

* URL pubblico
* URL di SharePoint privato
* File

Il processo di inserimento converte i [tipi di contenuto supportati](content-types.md) in Markdown. Tutte le altre modifiche della *risposta* vengono eseguite con Markdown. Dopo aver creato la Knowledge base, è possibile modificare le [coppie QnA](question-answer-set.md) nel portale di QnA Maker con la funzionalità di [creazione di testo avanzata](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer).

### <a name="data-format-considerations"></a>Considerazioni sul formato dei dati

Poiché il formato finale di una coppia di QnA è Markdown, è importante comprendere il [supporto di Markdown](../reference-markdown-format.md) .

Le immagini collegate devono essere disponibili da un URL pubblico per poter essere visualizzate nel riquadro di test del portale QnA Maker e in qualsiasi applicazione client perché QnA Maker non fornisce l'autenticazione per il contenuto, incluse le immagini.

### <a name="bot-personality"></a>Personalità bot

Aggiungere una personalità bot alla Knowledge base con [Chit-Chat](../how-to/chit-chat-knowledge-base.md). Questa personalità si presenta con le risposte fornite in un certo tono colloquiale, ad esempio *Professional* e *friendly*. Questo Chit-Chat viene fornito come set di conversazioni, che è disponibile per il controllo totale da aggiungere, modificare e rimuovere.

Una personalità bot è consigliata se il bot si connette alla Knowledge base. Se ci si connette a diversi servizi, uno dei quali è la Knowledge base, è possibile scegliere di continuare a usare Chit-Chat nella Knowledge base, ma è necessario rivedere il modo in cui il servizio bot interagisce per sapere se si tratta della progettazione dell'architettura corretta per l'utilizzo.

### <a name="conversation-flow-with-a-knowledge-base"></a>Flusso di conversazioni con una Knowledge base

Il flusso di conversazione inizia in genere con una formula di saluto da un utente, ad esempio `Hi` o `Hello` . La Knowledge base può rispondere con una risposta generale, ad esempio `Hi, how can I help you` , e può anche fornire una selezione di richieste di completamento per continuare la conversazione.

È necessario progettare il flusso di conversazione tenendo presente un ciclo in modo che un utente sappia come usare il bot e non venga abbandonato dal bot nella conversazione. Le [richieste di completamento](../how-to/multiturn-conversation.md) forniscono il collegamento tra coppie QnA, che consentono il flusso di conversazione.

### <a name="authoring-with-collaborators"></a>Creazione con collaboratori

I collaboratori possono essere altri sviluppatori che condividono lo stack di sviluppo completo dell'applicazione Knowledge base oppure possono essere limitati alla semplice creazione della Knowledge base.

La creazione della Knowledge base supporta diverse [autorizzazioni di accesso in base al ruolo](../how-to/collaborate-knowledge-base.md) che si applicano al portale di Azure per limitare l'ambito delle capacità di un collaboratore.

## <a name="integration-with-client-applications"></a>Integrazione con le applicazioni client

L'integrazione con [le applicazioni client](integration-with-other-applications.md) significa inviare una query all'endpoint del runtime di stima. Una query viene inviata alla Knowledge base specifica con un SDK o una richiesta basata su REST all'endpoint dell'app Web del QnA Maker.

Per autenticare correttamente una richiesta client, è necessario che l'applicazione client invii le credenziali corrette e l'ID Knowledge base. Se si usa un servizio Azure bot, configurare l'impostazione come parte della configurazione di bot nell'portale di Azure.

### <a name="conversation-flow-in-a-client-application"></a>Flusso di conversazione in un'applicazione client

Il flusso di conversazione in un' [applicazione client](integration-with-other-applications.md), ad esempio un bot di Azure, potrebbe richiedere funzionalità prima e dopo l'interazione con la Knowledge base.

Se l'applicazione client supporta il flusso di conversazioni, fornendo metodi alternativi per gestire i prompt di completamento o Chit-Chit, progettarli in anticipo e assicurarsi che la query che usa nell'applicazione client venga gestita correttamente, da un altro servizio o inviata alla Knowledge base.

### <a name="dispatching-between-qna-maker-and-language-understanding-luis"></a>Invio tra QnA Maker e Language Understanding (LUIS)

Un'applicazione client può fornire diverse funzionalità, una delle quali risponde a una Knowledge base. Altre funzionalità dovrebbero comunque comprendere il testo colloquiale ed estrarre il suo significato.

Un'architettura di applicazione client comune prevede l'uso combinato di QnA Maker e [Language Understanding (Luis)](../../LUIS/what-is-luis.md) . LUIS fornisce la classificazione e l'estrazione del testo per qualsiasi query, incluso ad altri servizi, mentre QnA Maker fornisce risposte dalla Knowledge base.

In un' [architettura condivisa](../choose-natural-language-processing-service.md), l'invio tra i due servizi viene eseguito con lo strumento di [invio](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) di bot Framework.

### <a name="active-learning-from-a-client-application"></a>Apprendimento attivo da un'applicazione client

QnA Maker utilizza l' _apprendimento attivo_ per migliorare la Knowledge base suggerendo domande alternative a una risposta. L'applicazione client è responsabile di una parte di questo [apprendimento attivo](active-learning-suggestions.md). L'applicazione client, tramite richieste di conversazione, è in grado di determinare che la risposta restituita dalla Knowledge base non è la risposta che l'utente stava cercando e determinare la risposta migliore. L'applicazione client deve restituire [tali informazioni alla Knowledge base](active-learning-suggestions.md#how-you-give-explicit-feedback-with-the-train-api) per migliorare la qualità della stima.

### <a name="providing-a-default-answer"></a>Fornire una risposta predefinita

Se la Knowledge base non trova una risposta, viene restituita la _risposta predefinita_. Questa risposta è configurabile dal portale di QnA Maker, nella pagina **Impostazioni** o nelle [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body).

Questa risposta predefinita è diversa da quella predefinita di Azure bot. La risposta predefinita di Azure bot è configurata nel portale di Azure, per il bot, come parte delle impostazioni di configurazione e viene restituita quando la soglia del punteggio non viene soddisfatta.

## <a name="prediction"></a>Previsione

La stima è la risposta della Knowledge base che include più informazioni rispetto alla semplice risposta. Per ottenere una risposta di stima della query, usare l' [API GeneateAnswer](query-knowledge-base.md).

### <a name="prediction-score-fluctuations"></a>Fluttuazioni del Punteggio di stima

Un punteggio può variare in base a diversi fattori:

* Numero di risposte richieste in risposta a [GenerateAnswer](query-knowledge-base.md) con la `top` Proprietà
* Varie domande alternative disponibili
* Filtro per i metadati
* Query inviata a `test` o `production` Knowledge base

È previsto un [rango di risposta in due fasi](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer):
* Ricerca cognitiva: primo rango: affinché una risposta venga restituita da ricerca cognitiva, il numero di _risposte consentite_ deve essere sufficientemente elevato da garantire che le risposte migliori vengano restituite da ricerca cognitiva in modo che possano passare al ranker QnA Maker
* QnA Maker-Second Rank: applicare conteggi e machine learning per determinare la risposta migliore.

### <a name="service-updates"></a>Aggiornamenti del servizio

Gli aggiornamenti dei servizi vengono gestiti automaticamente applicando gli [aggiornamenti più recenti del runtime](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

### <a name="scaling-throughput-and-resiliency"></a>Scalabilità, velocità effettiva e resilienza

La scalabilità, la velocità effettiva e la resilienza sono determinate dalle [risorse di Azure](../how-to/set-up-qnamaker-service-azure.md), dai rispettivi piani tariffari e da qualsiasi architettura circostante, ad esempio [Gestione traffico](../how-to/set-up-qnamaker-service-azure.md#business-continuity-with-traffic-manager).

### <a name="analytics-with-application-insights"></a>Analisi con Application Insights

Tutte le query alla Knowledge base vengono archiviate in Application Insights. Usare le [query principali](../how-to/get-analytics-knowledge-base.md) per comprendere la metrica.

## <a name="development-lifecycle"></a>Ciclo di vita di sviluppo

Il [ciclo](development-lifecycle-knowledge-base.md) di vita di sviluppo di una Knowledge base è in corso: modifica, test e pubblicazione della Knowledge base.

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>Sviluppo di coppie di QnA Maker della Knowledge base

Le [coppie di QnA](question-answer-set.md) devono essere progettate e sviluppate in base all'utilizzo dell'applicazione client.

Ogni coppia può contenere:
* Filtraggio dei metadati durante l'esecuzione di query. Questo consente di contrassegnare le coppie QnA con informazioni aggiuntive sull'origine, il contenuto, il formato e lo scopo dei dati.
* Richieste di completamento: determinare un percorso attraverso la Knowledge base in modo che l'utente arrivi alla risposta corretta.
* Domande alternative: le domande alternative sono importanti per consentire alla ricerca di trovare una corrispondenza con la risposta da diverse forme della domanda. I suggerimenti per l' [apprendimento attivo](active-learning-suggestions.md) diventano domande alternative.

### <a name="devops-development"></a>Sviluppo DevOps

Per lo sviluppo di una Knowledge base da inserire in una pipeline DevOps è necessario che la Knowledge base sia isolata durante i [test batch](../quickstarts/batch-testing.md).

Una Knowledge base condivide l'indice ricerca cognitiva con tutte le altre Knowledge base nella risorsa QnA Maker. Mentre la Knowledge base è isolata dalla partizione, la condivisione dell'indice può causare una differenza nel punteggio rispetto alla Knowledge base pubblicata.

Per ottenere lo _stesso punteggio_ sulle `test` `production` Knowledge base e, isolare una risorsa QnA Maker a una singola Knowledge base. In questa architettura, la risorsa deve essere attiva solo fino alla lunghezza del test batch isolato.

## <a name="next-step"></a>Passaggio successivo

* [Risorse di Azure](../how-to/set-up-qnamaker-service-azure.md)
* [Coppie di domande e risposte](question-answer-set.md)