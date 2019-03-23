---
title: Domande frequenti
titleSuffix: Azure Cognitive Services
description: Questo articolo contiene risposte alle domande frequenti su LUIS.
author: diberry
manager: nitinme
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/21/2019
ms.author: diberry
ms.openlocfilehash: aed7d4fedd4781eac8c127744e5fe93fb054b99d
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369718"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Risposte alle domande frequenti per Language Understanding (LUIS)

Questo articolo contiene risposte alle domande frequenti su LUIS.

<a name="luis-authoring"></a>

## <a name="authoring"></a>Creazione

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

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Come si trasferisce la proprietà di un'app LUIS?
Per trasferire un'app LUIS in un'altra sottoscrizione di Azure, esportare l'app LUIS e importarla usando un nuovo account. Aggiornare l'ID dell'app LUIS nell'applicazione client che la chiama. La nuova app può restituire punteggi LUIS leggermente diversi rispetto all'app originale.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>Un'entità predefinita verrà contrassegnata in un utterance esempio invece l'entità personalizzata. Come si risolve il problema? 

Visualizzare [risoluzione dei problemi relativi a entità predefinite](luis-concept-entity-types.md#troubleshooting-prebuilt-entities).

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Si è tentato di importare un file app o di versione, ma si verifica un errore, cosa è successo? 

Altre informazioni, vedere [errori di importazione versione](luis-how-to-manage-versions.md#import-errors) e [errori di importazione app](luis-how-to-start-new-app.md#import-errors).

<a name="luis-collaborating"></a>

## <a name="collaborating"></a>Collaborazione

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-role-based-access-control-rbac"></a>Come si concede ai collaboratori l'accesso a LUIS con Azure Active Directory (Azure AD) o il controllo degli accessi in base al ruolo (RBAC)?

Per informazioni su come concedere l'accesso ai collaboratori, vedere [Risorse di Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-resources) e [Utente del tenant di Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-tenant-user). 

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Endpoint

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>La query dell'endpoint ha restituito risultati imprevisti. Cosa devo fare?

I risultati imprevisti della query dipendono dallo stato del modello pubblicato. Per correggere il modello, può essere necessario modificare il modello, eseguire il training e ripetere la pubblicazione. 

La correzione del modello inizia con l'[apprendimento attivo](luis-how-to-review-endoint-utt.md).

È possibile rimuovere il training non deterministico aggiornando l'[API delle impostazioni della versione dell'applicazione](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) in modo da usare tutti i dati di training.

Per altri suggerimenti, rivedere le [procedure consigliate](luis-concept-best-practices.md). 

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Perché LUIS aggiunge spazi alla query attorno o in mezzo alle parole?
LUIS [suddivide in token](luis-glossary.md#token) l'espressione in base alle [impostazioni cultura](luis-language-support.md#tokenization). Il valore originale e il valore in formato token sono entrambi disponibili per l'[estrazione dei dati](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Come è possibile creare e assegnare una chiave di endpoint di LUIS?
[Creare la chiave di endpoint](luis-how-to-azure-subscription.md) in Azure per il livello di [servizio](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/). [Assegnare la chiave](luis-how-to-azure-subscription.md) nella pagina **[Keys and endpoints](luis-how-to-azure-subscription.md)** (Chiavi ed endpoint). Non vi è alcuna API corrispondente per questa azione. È necessario quindi modificare la richiesta HTTP all'endpoint per fare in modo che [usi la nuova chiave dell'endpoint](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-interpret-luis-scores"></a>Come si interpretano i punteggi di LUIS?
Il sistema deve usare la finalità di punteggio più elevata, indipendentemente dal relativo valore. Ad esempio, un punteggio al di sotto di 0,5 (inferiore al 50%) non significa necessariamente che LUIS disponga di una confidenza bassa. Fornendo più dati di training sarà possibile aumentare il [punteggio](luis-concept-prediction-score.md) della finalità più probabile.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Perché non vengono visualizzate le occorrenze degli endpoint nella dashboard dell'app?
Le occorrenze totali dell'endpoint nel dashboard dell'app vengono aggiornate periodicamente, ma le metriche associate alla chiave endpoint a LUIS nel portale di Azure vengono aggiornate più di frequente.

Se non sono presenti occorrenze sull'endpoint aggiornato nel dashboard, accedere al portale di Azure e trovare la risorsa associata alla chiave endpoint LUIS, quindi aprire **Metriche** per selezionare la metrica **Chiamate totali**. Se la chiave endpoint viene usata per più di un'app LUIS, la metrica nel portale di Azure mostra il numero aggregato di chiamate da tutte le app LUIS che la usano.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>È disponibile un comando di PowerShell per ottenere la quota di endpoint?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

È possibile usare un comando di PowerShell per visualizzare la quota di endpoint:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
``` 

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

Vedere [Differenze di stima tra copie della stessa app](luis-concept-prediction-score.md#review-intents-with-similar-scores).

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Alcune espressioni ottengono la finalità errata dopo aver apportato modifiche all'applicazione. Il problema sembra scomparire in modo casuale. Risoluzione 

Vedere [Eseguire il training con tutti i dati](luis-how-to-train.md#train-with-all-data).

## <a name="app-publishing"></a>Pubblicazione dell'app

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Che cos'è l'ID tenant nella finestra "Aggiungi una chiave all'app"?
In Azure, un tenant rappresenta il client o l'organizzazione associati a un servizio. È possibile trovare l'ID del tenant nel portale di Azure nella casella **ID directory** selezionando **Azure Active Directory** > **Gestisci**  >  **Proprietà**.

![ID tenant nel portale di Azure](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Perché sono presenti più chiavi endpoint di quelle che sono state assegnate all'app?
Ogni app LUIS dispone della chiave di creazione/autorizzazione nell'elenco degli endpoint per maggiore praticità. Questa chiave consente di effettuare solo alcuni accessi agli endpoint in modo che sia possibile provare LUIS.  

Se l'app esisteva prima che LUIS fosse disponibile a livello generale (GA), le chiavi di endpoint LUIS vengono assegnate automaticamente nella sottoscrizione. Tale operazione è stata pensata per facilitare la migrazione GA. Eventuali nuove chiavi di endpoint LUIS nel portale di Azure _non_ vengono assegnate automaticamente a LUIS.

## <a name="key-management"></a>Gestione della chiave

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Come si fa a sapere quale chiave è necessaria, dove si trova e cosa farne? 

Per altre informazioni sulle differenze tra la [chiave di creazione](luis-how-to-account-settings.md) e la [chiave di endpoint per la stima](luis-how-to-azure-subscription.md), vedere [Chiavi di creazione e di endpoint per query di stima in LUIS](luis-concept-keys.md). 

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>È stato restituito un errore per segnalare il superamento della quota. Risoluzione 

Per altre informazioni, vedere [Come correggere gli errori di superamento della quota quando la chiave supera il limite di utilizzo del piano tariffario](luis-how-to-azure-subscription.md##how-to-fix-out-of-quota-errors-when-the-key-exceeds-pricing-tier-usage).

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>È necessario gestire più query di endpoint. Come procedere? 

Per altre informazioni, vedere [Come correggere gli errori di superamento della quota quando la chiave supera il limite di utilizzo del piano tariffario](luis-how-to-azure-subscription.md##how-to-fix-out-of-quota-errors-when-the-key-exceeds-pricing-tier-usage).



## <a name="app-management"></a>Gestione app

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Come si scarica un log delle espressioni degli utenti?
Per impostazione predefinita, l'app LUIS registra le espressioni degli utenti. Per scaricare un log delle espressioni che gli utenti inviano all'app LUIS, visitare **Le mie app** e selezionare l'app. Sulla barra degli strumenti contestuale selezionare **Export Endpoint Logs** (Esporta log degli endpoint). Il log viene formattato come un file con valori delimitati da virgole (CSV).

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

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>In che modo Microsoft gestisce i dati inviati a LUIS?

In [Trust Center](https://www.microsoft.com/trustcenter) sono illustrati i principi cardine adottati da Microsoft e le opzioni disponibili per gli utenti in relazione alla gestione dei dati e all'accesso in Servizi di Azure.

## <a name="language-and-translation-support"></a>Supporto linguistico e di traduzione

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Si dispone di un'app in una lingua e si desidera creare un'app parallela in un'altra lingua. Qual è il modo più semplice per eseguire questa operazione?
1. Esportare l'app.
2. Tradurre nella lingua di destinazione le espressioni con etichetta nel file JSON dell'app esportata.
3. Potrebbe essere necessario modificare i nomi di finalità ed entità oppure lasciarli invariati.
4. Infine, importare l'app per disporre di un'app LUIS nella lingua di destinazione.

## <a name="app-notification"></a>Notifica dell'app

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Perché si riceve un messaggio di posta elettronica che indica che la quota è quasi terminata?
La chiave di creazione/avvio consente solo 1000 query di endpoint al mese. Creare una chiave endpoint LUIS (gratuita o a pagamento) e usare tale chiave quando si eseguono le query di endpoint. Se si eseguono query di endpoint da un bot o un'altra applicazione client, è necessario modificare lì la chiave di endpoint LUIS.

## <a name="bots"></a>Bot

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>Il bot LUIS non funziona. Cosa devo fare?

La prima cosa da fare è determinare se il problema è correlato a LUIS o avviene al difuori del middleware LUIS. 

#### <a name="resolve-issue-in-luis"></a>Risolvere il problema LUIS
Passare la stessa espressione a LUIS dall'[endpoint LUIS](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance). Se si riceve un errore, risolvere il problema in LUIS finché l'errore non viene più restituito. Di seguito sono riportati gli errori più comuni.

* `Out of call volume quota. Quota will be replenished in <time>.` - Questo problema indica che occorre passare da una chiave di creazione a una [chiave endpoint](luis-how-to-azure-subscription.md) oppure è necessario modificare i [livelli di servizio](luis-how-to-azure-subscription.md#change-pricing-tier). 

#### <a name="resolve-issue-in-azure-bot-service"></a>Risolvere il problema nel servizio Azure Bot

Se si usa il servizio Azure Bot e il problema è che il **Test in Web Chat** (Testa in Web Chat) restituisce `Sorry, my bot code is having an issue`, controllare i log:

1. Nel portale di Azure, per il bot, selezionare **Build** (Compila) dalla sezione **Bot Management** (Gestione bot).
1. Aprire l'editor di codice online. 
1. Nella barra di spostamento superiore azzurra selezionare il nome del bot (il secondo elemento a destra).
1. Nell'elenco a discesa visualizzato selezionare **Open Kudu Console** (Apri console Kudu).
1. Selezionare **LogFiles**, quindi selezionare **Application** (Applicazione). Esaminare tutti i file di log. Se l'errore non compare nella cartella dell'applicazione, esaminare tutti i file di log in **LogFiles**. 
1. Ricordarsi di ricompilare il progetto se si usa un linguaggio compilato, ad esempio C#.

> [!Tip] 
> La console può anche installare pacchetti. 

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Risolvere il problema durante il debug nel computer locale con Bot Framework. 

Per altre informazioni sul debug locale di un bot, vedere [Eseguire il debug di un bot](https://docs.microsoft.com/azure/bot-service/bot-service-debug-bot?view=azure-bot-service-4.0).

## <a name="integrating-luis"></a>Integrazione di LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Dove si trova l'app LUIS creata durante il processo di iscrizione di un bot all'app Web di Azure?
Se si seleziona un modello LUIS e quindi il pulsante **Seleziona** nel riquadro del modello, il riquadro a sinistra cambia per includere il tipo di modello e chiede in quale area geografica creare il modello LUIS. Il processo bot dell'app Web, tuttavia, non crea una sottoscrizione a LUIS.

![Area geografica del bot Web per il modello LUIS](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Quali aree geografiche LUIS supportano il priming del riconoscimento vocale di Bot Framework?
Il [priming del riconoscimento vocale](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) è supportato solo per le app LUIS nell'istanza Stati Uniti centrali.

## <a name="api-programming-strategies"></a>Strategie di programmazione di API

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Come si ottiene l'area di LUIS di una risorsa a livello di codice? 

Usare il sample di LUIS per [trovare l'area](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) a livello di codice usando C# o Node. Js. 

## <a name="luis-service"></a>Servizio LUIS

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Language Understanding (LUIS) è disponibile in locale o nel cloud privato?

Sì, è possibile usare il [contenitore](luis-container-howto.md) LUIS per questi scenari se si dispone della connettività necessaria per misurare l'utilizzo. 

### <a name="at-the-build-2018-conference-i-heard-about-a-language-understanding-feature-or-demo-but-i-dont-remember-what-it-was-called"></a>In occasione della conferenza Build 2018, è stata menzionata una funzionalità o una dimostrazione di LUIS. Di quale caratteristica si trattava?

In occasione della conferenza Build 2018 sono state rilasciate le seguenti funzionalità:

|NOME|Content|
|--|--|
|Miglioramenti|Entità [Espressione regolare](luis-concept-data-extraction.md##regular-expression-entity-data) ed entità [Frase chiave](luis-concept-data-extraction.md#key-phrase-extraction-entity-data)
|Modelli|Modelli [concept](luis-concept-patterns.md), [tutorial](luis-tutorial-pattern.md), [how-to](luis-how-to-model-intent-pattern.md)<br>Concetto dell'entità [Patterns.Any](luis-concept-entity-types.md), incluso un [elenco esplicito](luis-concept-patterns.md#explicit-lists) per le eccezioni<br>Concetto [Ruoli](luis-concept-roles.md)|
|Integrazioni|Integrazione dell'[analisi di testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) per l'[analisi del sentiment](luis-how-to-publish-app.md#enable-sentiment-analysis)<br>Integrazione [vocale](https://docs.microsoft.com/azure/cognitive-services/speech) del priming del riconoscimento vocale in combinazione con [Speech SDK](https://aka.ms/SpeechSDK)|
|Strumento Dispatch|Parte di [BotBuilder-tools](https://github.com/Microsoft/botbuilder-tools), lo [strumento](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps) a riga di comando Dispatch permette di combinare più app LUIS e QnA Maker in una singola app LUIS per migliorare il riconoscimento delle finalità in un bot

Sono state incluse altre [route API](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/authoring-routes.md) di creazione.

Video:
* [Azure Friday all'evento Build 2018: Servizi cognitivi - Linguaggio (LUIS)](https://channel9.msdn.com/Shows/Azure-Friday/At-Build-2018-Cognitive-Services-Language-LUIS/player)
* [Build 2018 AI Show - Novità del servizio Language Understanding](https://channel9.msdn.com/Shows/AI-Show/Whats-New-with-Language-Understanding-Service-LUIS/player)
* [Sessione di Build 2018: bot intelligenti, funzionalità vocali e procedure consigliate per la comprensione del linguaggio naturale](https://channel9.msdn.com/events/Build/2018/BRK3208)
* [Build 2018 - Aggiornamenti su Language Understanding (LUIS)](https://channel9.msdn.com/events/Build/2018/THR3118/player)

Progetti:
* Demo del [bot Contoso Cafe](https://github.com/botbuilderbuild2018/build2018demo) - Codice sorgente su GitHub

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su LUIS, vedere le risorse seguenti:
* [Domande su Stack Overflow con tag LUIS](https://stackoverflow.com/questions/tagged/luis)
* [Forum MSDN su LUIS (Language Understanding Intelligent Service)](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS)
