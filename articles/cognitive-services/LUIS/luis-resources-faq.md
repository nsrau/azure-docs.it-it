---
title: Domande frequenti su LUIS (Language Understanding) in Azure | Microsoft Docs
description: Risposte alle domande frequenti su LUIS
author: diberry
manager: cjgronlund
services: cognitive-services
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: 8e0d834b94ff902eb0c1e0ada2fb32d374cee12b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239118"
---
# <a name="language-understanding-faq"></a>Domande frequenti su LUIS

Questo articolo contiene risposte alle domande frequenti su LUIS.

## <a name="luis-authoring"></a>Creazione di LUIS

### <a name="what-are-the-luis-best-practices"></a>Quali sono le procedure consigliate per LUIS? 
Iniziare con il [ciclo di creazione](luis-concept-app-iteration.md), quindi leggere le [procedure consigliate](luis-concept-best-practices.md). 

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Qual è il modo migliore per iniziare a creare un'app in LUIS?

Il modo migliore per creare un'app consiste nel seguire un [processo incrementale](luis-concept-app-iteration.md). 

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Qual è una buona norma da seguire per modellare le finalità in un'app? È consigliabile creare finalità più specifiche o più generiche?

Scegliere finalità sufficientemente specifiche da evitare sovrapposizioni, ma non eccessivamente poiché LUIS potrebbe avere difficoltà a distinguere tra finalità simili. La creazione di finalità discriminative specifiche è una delle procedure consigliate per la modellazione in LUIS.

### <a name="is-it-important-to-train-the-none-intent"></a>È importante eseguire il training della finalità None?

Sì, è consigliabile eseguire il training della finalità **None** con più espressioni man mano che si aggiungono etichette ad altre finalità. Un rapporto valido è di 1 o 2 etichette aggiunte a **None** per ogni 10 etichette aggiunte a una finalità. Questo rapporto migliora l'efficienza discriminativa di LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Come è possibile correggere gli errori di ortografia nelle espressioni?

Vedere l'esercitazione [API Controllo ortografico Bing V7](luis-tutorial-bing-spellcheck.md). LUIS applica limite imposti dall'API Controllo ortografico Bing V7. 

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Come è possibile modificare a livello di codice l'app LUIS?
Per modificare l'app LUIS a livello di codice, usare l'[API di creazione](https://aka.ms/luis-authoring-apis). Vedere [Chiamare l'API di creazione LUIS](./luis-quickstart-node-add-utterance.md) e [Compilare un'app LUIS a livello di codice con Node.js](./luis-tutorial-node-import-utterances-csv.md) per esempi su come chiamare l'API di creazione. L'API di creazione richiede l'uso di una [chiave di creazione](luis-concept-keys.md#authoring-key) e non di una chiave di endpoint. La creazione a livello di codice consente fino a 1.000.000 di chiamate al mese e cinque transazioni al secondo. Per altre informazioni sulle chiavi usate con LUIS, vedere [Gestire le chiavi](./luis-concept-keys.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Dove si trova la funzionalità Criterio che offre l'associazione di espressioni regolari?
La **funzionalità Criterio** precedente è attualmente deprecata ed è sostituita da **[Modelli](luis-concept-patterns.md)**. 

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Come si può usare un'entità per estrarre i dati corretti? 
Vedere [entità](luis-concept-entity-types.md) ed [estrazione dei dati](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Le variazioni di un'espressione di esempio devono includere la punteggiatura? 
Aggiungere le variazioni come espressioni di esempio alla finalità o aggiungere il criterio dell'espressione di esempio con la [sintassi per ignorare](luis-concept-patterns.md#pattern-syntax) la punteggiatura. 

### <a name="does-luis-currently-support-cortana"></a>LUIS supporta attualmente Cortana?

Le app Cortana predefinite sono state deprecate nel 2017. Non sono più supportate. 

## <a name="luis-endpoint"></a>Endpoint LUIS

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Perché LUIS aggiunge spazi alla query attorno o in mezzo alle parole?
LUIS [suddivide in token](luis-glossary.md#token) l'espressione in base alle [impostazioni cultura](luis-supported-languages.md#tokenization). Il valore originale e il valore in formato token sono entrambi disponibili per l'[estrazione dei dati](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Come è possibile creare e assegnare una chiave di endpoint di LUIS?
[Creare la chiave di endpoint](luis-how-to-azure-subscription.md#create-luis-endpoint-key) in Azure per il livello di [servizio](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/). [Assegnare la chiave](luis-how-to-manage-keys.md#assign-endpoint-key) nella pagina **[Pubblica](luis-how-to-publish-app.md)**. Non vi è alcuna API corrispondente per questa azione. È necessario quindi modificare la richiesta HTTP all'endpoint per fare in modo che [usi la nuova chiave dell'endpoint](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-interpret-luis-scores"></a>Come si interpretano i punteggi di LUIS? 
Il sistema deve usare la finalità di punteggio più elevata, indipendentemente dal relativo valore. Ad esempio, un punteggio al di sotto di 0,5 (inferiore al 50%) non significa necessariamente che LUIS disponga di una confidenza bassa. Fornendo più dati di training sarà possibile aumentare il punteggio della finalità più probabile.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Perché non vengono visualizzate le occorrenze degli endpoint nella dashboard dell'app?
Le occorrenze totali dell'endpoint nel dashboard dell'app vengono aggiornate periodicamente, ma le metriche associate alla chiave endpoint a LUIS nel portale di Azure vengono aggiornate più di frequente. 

Se non sono presenti occorrenze sull'endpoint aggiornato nel dashboard, accedere al portale di Azure e trovare la risorsa associata alla chiave endpoint a LUIS, quindi aprire **Metriche** per selezionare la metrica **Chiamate totali**. Se la chiave endpoint viene usata per più di un'app LUIS, la metrica nel portale di Azure mostra il numero aggregato di chiamate da tutte le app LUIS che la usano.

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Ieri l'app LUIS funzionava, mentre oggi si ricevono errori 403. L'app non è stata modificata. Risoluzione 
Seguire le [istruzioni](#how-do-i-create-and-assign-a-luis-endpoint-key) nella prossima domanda per creare una chiave di endpoint LUIS e assegnarla all'app. È necessario quindi modificare la richiesta HTTP all'endpoint per fare in modo che [usi la nuova chiave dell'endpoint](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-secure-my-luis-endpoint"></a>Come si protegge l'endpoint LUIS? 
Vedere [Protezione dell'endpoint](luis-concept-security.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Uso entro i limiti LUIS

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Qual è il numero massimo di finalità ed entità supportate da un'app LUIS?
Vedere il riferimento sui [limiti](luis-boundaries.md).

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Si desidera creare un'app LUIS con un numero di finalità maggiore del numero massimo. Cosa devo fare?

Consultare le [Procedure consigliate per le finalità](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Si desidera creare un'app in LUIS con un numero di entità maggiore del numero massimo. Cosa devo fare?

Consultare le [Procedure consigliate per le entità](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities).

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Quali sono i limiti per il numero e le dimensioni degli elenchi di frasi?
Per la lunghezza massima di un [elenco di frasi](./luis-concept-feature.md), vedere il riferimento sui [limiti](luis-boundaries.md).

### <a name="what-are-the-limits-on-example-utterances"></a>Quali sono i limiti per le espressioni di esempio?
Vedere il riferimento sui [limiti](luis-boundaries.md).

## <a name="testing-and-training"></a>Test e training

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Sono presenti alcuni errori nel riquadro di test in batch per alcuni dei modelli nell'app. Come è possibile risolvere il problema?

Gli errori indicano la presenza di discrepanze tra le etichette e le stime dai modelli. Per risolvere il problema, effettuare una o entrambe le attività seguenti:
* Per migliorare la discriminazione delle finalità di LUIS, aggiungere altre etichette.
* Per velocizzare l'apprendimento di LUIS, aggiungere caratteristiche di elenchi di frasi che introducano lemmi specifici per un dominio.

Vedere l'esercitazione sul [test in batch](luis-tutorial-batch-testing.md).

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Quando un'app viene esportata e reimportata in una nuova app (con un nuovo ID), i punteggi di stima di LUIS cambiano. Perché si verifica questa situazione? 

Vedere [Differenze di stima tra copie della stessa app](luis-concept-prediction-score.md#differences-with-predictions).

## <a name="app-publishing"></a>Pubblicazione dell'app

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Che cos'è l'ID tenant nella finestra "Aggiungi una chiave all'app"?
In Azure un tenant rappresenta il client o l'organizzazione associati a un servizio. È possibile trovare l'ID del tenant nel portale di Azure nella casella **ID directory** selezionando **Azure Active Directory** > **Gestisci**  >  **Proprietà**.

![ID tenant nel portale di Azure](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
### <a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>Perché sono presenti più chiavi endpoint di quelle assegnate all'app nella pagina della pubblicazione? 
Ogni app LUIS dispone di una chiave di creazione/avvio. Le chiavi endpoint LUIS create durante l'intervallo di tempo GA sono visibili nella pagina di pubblicazione, indipendentemente dal fatto che siano state aggiunte all'app. Tale operazione è stata pensata per facilitare la migrazione GA. Le nuove chiavi endpoint LUIS non vengono visualizzate nella pagina di pubblicazione. 

## <a name="app-management"></a>Gestione app

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Come si trasferisce la proprietà di un'app LUIS?
Per trasferire un'app LUIS in un'altra sottoscrizione di Azure, esportare l'app LUIS e importarla usando un nuovo account. Aggiornare l'ID dell'app LUIS nell'applicazione client che la chiama. La nuova app può restituire punteggi LUIS leggermente diversi rispetto all'app originale. 

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Come si scarica un log delle espressioni degli utenti?
Per impostazione predefinita, l'app LUIS registra le espressioni degli utenti. Per scaricare un log delle espressioni che gli utenti inviano all'app LUIS, visitare **Le mie app**e fare clic sui puntini di sospensione (***...*** ) nell'elenco dell'app. Quindi fare clic su **Esporta i registri endpoint**. Il log viene formattato come un file con valori delimitati da virgole (CSV).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Come si disabilita la registrazione delle espressioni?
È possibile disattivare la registrazione delle espressioni degli utenti impostando `log=false` nell'URL dell'endpoint che l'applicazione client usa per inviare query a LUIS. Tuttavia, la disattivazione della registrazione impedisce all'app LUIS di suggerire espressioni o migliorare le prestazioni basate sull'[apprendimento attivo](luis-concept-review-endpoint-utterances.md#what-is-active-learning). Se si imposta `log=false` a causa di problemi di privacy dei dati, non sarà possibile scaricare un record delle espressioni degli utenti da LUIS o usarle per migliorare l'app.

La registrazione è l'unico spazio di archiviazione delle espressioni. 

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Perché un utente potrebbe non desiderare la registrazione di tutte le espressioni degli endpoint?
Se si usa il log per l'analisi delle stime, non acquisire le espressioni di test nel log.

## <a name="data-management"></a>Gestione dei dati

### <a name="can-i-delete-data-from-luis"></a>È possibile eliminare dati da LUIS? 

* È sempre possibile eliminare le espressioni di esempio usate per il training di LUIS. Le espressioni di esempio eliminate dall'app LUIS vengono rimosse dal servizio Web LUIS e non sono più disponibili per l'esportazione.
* È possibile eliminare le espressioni dall'elenco di espressioni utente suggerite da LUIS nella pagina **Review endpoint utterances** (Esamina espressioni endpoint). Le espressioni eliminate da questo elenco non vengono più suggerite, ma non vengono eliminate dai log.
* Se si elimina un account, vengono eliminate tutte le app insieme ai relativi log ed espressioni di esempio. I dati vengono mantenuti nei server per 60 giorni prima di essere eliminati in modo permanente.

## <a name="language-and-translation-support"></a>Supporto linguistico e di traduzione 

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Si dispone di un'app in una lingua e si desidera creare un'app parallela in un'altra lingua. Qual è il modo più semplice per eseguire questa operazione?
1. Esportare l'app.
2. Tradurre nella lingua di destinazione le espressioni con etichetta nel file JSON dell'app esportata.
3. Potrebbe essere necessario modificare i nomi di finalità ed entità oppure lasciarli invariati.
4. Infine, importare l'app per disporre di un'app LUIS nella lingua di destinazione.

## <a name="app-notification"></a>Notifica dell'app

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Perché si riceve un messaggio di posta elettronica che indica che la quota è quasi terminata?
La chiave di creazione/avvio consente solo 1000 query di endpoint al mese. Creare una chiave endpoint LUIS (gratuita o a pagamento) e usare tale chiave quando si eseguono le query di endpoint. Se si eseguono query di endpoint da un bot o un'altra applicazione client, è necessario modificare lì la chiave di endpoint LUIS. 

## <a name="integrating-luis"></a>Integrazione di LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Dove si trova l'app LUIS creata durante il processo di iscrizione di un bot all'app Web di Azure?
Se si seleziona un modello LUIS e quindi il pulsante **Seleziona** nel riquadro del modello, il riquadro a sinistra cambia per includere il tipo di modello e chiede in quale area geografica creare il modello LUIS. Il processo bot dell'app Web, tuttavia, non crea una sottoscrizione a LUIS.

![Area geografica del bot Web per il modello LUIS](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Quali aree geografiche LUIS supportano il priming del riconoscimento vocale di Bot Framework?
Il [priming del riconoscimento vocale](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) è supportato solo per le app LUIS nell'istanza Stati Uniti centrali. 

## <a name="luis-service"></a>Servizio LUIS 

### <a name="is-luis-available-on-premises-or-in-private-cloud"></a>LUIS è disponibile in locale o in un cloud privato?
No. 

## <a name="changes-to-the-docs"></a>Modifiche alla documentazione

### <a name="where-did-the-tutorials-go"></a>Dove sono state spostate le esercitazioni? 
Gli articoli che precedentemente si trovavano nella sezione Esercitazioni si trovano ora nella sezione Procedure dei documenti. 

|Esercitazione|
|--|
|Integrare LUIS con un bot usando [C#](luis-csharp-tutorial-build-bot-framework-sample.md) e [Node.js](luis-nodejs-tutorial-build-bot-framework-sample.md)|
|Aggiungere Application Insights a un bot con [C#](luis-tutorial-bot-csharp-appinsights.md) e [Node.js](luis-tutorial-function-appinsights.md)|
|Compilare un'app LUIS a livello di codice con [Node.js](luis-tutorial-node-import-utterances-csv.md)|
|Usare un'[entità composita](luis-tutorial-composite-entity.md) per estrarre i dati raggruppati|
|Aggiungere un'[entità elenco](luis-tutorial-list-entity.md) per un maggiore rilevamento di entità con Node.js|
|Migliorare l'accuratezza delle stime con un [elenco di frasi](luis-quickstart-primary-and-secondary-data.md), [modelli](luis-tutorial-pattern.md), e [test in batch](luis-tutorial-batch-testing.md)|
|[Correggere l'ortografia](luis-tutorial-batch-testing.md) con l'API Controllo ortografico Bing v7

### <a name="at-the-build-2018-conference-i-heard-about-a-language-understanding-feature-or-demo-but-i-dont-remember-what-it-was-called"></a>In occasione della conferenza Build 2018, è stata menzionata una funzionalità o una dimostrazione di LUIS. Di quale caratteristica si trattava? 

In occasione della conferenza Build 2018 sono state rilasciate le seguenti funzionalità:

|NOME|Content|
|--|--|
|Miglioramenti|Entità [Espressione regolare](luis-concept-data-extraction.md##regular-expression-entity-data) ed entità [Frase chiave](luis-concept-data-extraction.md#key-phrase-extraction-entity-data)
|Modelli|Modelli [concept](luis-concept-patterns.md), [tutorial](luis-tutorial-pattern.md), [how-to](luis-how-to-model-intent-pattern.md)<br>Concetto dell'entità [Patterns.Any](luis-concept-entity-types.md), incluso un [elenco esplicito](luis-concept-patterns.md#explicit-lists) per le eccezioni<br>Concetto [Ruoli](luis-concept-roles.md)|
|Integrazioni|Integrazione dell'[analisi di testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) per l'[analisi del sentiment](luis-how-to-publish-app.md#enable-sentiment-analysis)<br>Integrazione [vocale](https://docs.microsoft.com/azure/cognitive-services/speech) del [priming del riconoscimento vocale](luis-how-to-publish-app.md#enable-speech-priming) in combinazione con [Speech SDK](https://aka.ms/SpeechSDK)|
|Strumento Dispatch|Parte di [BotBuilder-tools](https://github.com/Microsoft/botbuilder-tools), lo [strumento](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps) a riga di comando Dispatch permette di combinare più app LUIS e QnA Maker in una singola app LUIS per migliorare il riconoscimento delle finalità in un bot

Sono state incluse altre [route API](https://github.com/Microsoft/LUIS-Samples/blob/master/authoring-routes.md) di creazione. 

Video: 
* [Azure Friday a Build 2018 - Servizi cognitivi - Lingua (Language Understanding)](https://channel9.msdn.com/Shows/Azure-Friday/At-Build-2018-Cognitive-Services-Language-LUIS/player)
* [Build 2018 AI Show - Novità del servizio Language Understanding](https://channel9.msdn.com/Shows/AI-Show/Whats-New-with-Language-Understanding-Service-LUIS/player)
* [Sessione di Build 2018: bot intelligenti, funzionalità vocali e procedure consigliate per la comprensione del linguaggio naturale](https://channel9.msdn.com/events/Build/2018/BRK3208)
* [Build 2018 - Aggiornamenti su Language Understanding (LUIS)](https://channel9.msdn.com/events/Build/2018/THR3118/player)

Progetti: 
* Dimostrazione del [bot Contoso Cafe](https://github.com/botbuilderbuild2018/build2018demo) - codice sorgente su GitHub

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su LUIS, vedere le risorse seguenti:
* [Domande su Stack Overflow con tag LUIS](https://stackoverflow.com/questions/tagged/luis)
* [Forum MSDN su LUIS (Language Understanding Intelligent Service)](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS) 