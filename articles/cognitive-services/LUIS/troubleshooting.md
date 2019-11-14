---
title: Domande frequenti-LUIS
titleSuffix: Azure Cognitive Services
description: Questo articolo contiene risposte alle domande frequenti su LUIS.
author: diberry
manager: nitinme
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: c7e34b816f33c5286557dfc5d58711128ff68437
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075873"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Risposte alle domande frequenti per Language Understanding (LUIS)

Questo articolo contiene risposte alle domande frequenti su LUIS.

## <a name="whats-new"></a>Novità

[Altre](whats-new.md) informazioni sulle novità di Language Understanding (Luis).

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
Per modificare l'app LUIS a livello di codice, usare l'[API di creazione](https://go.microsoft.com/fwlink/?linkid=2092087). Vedere [Chiamare l'API di creazione LUIS](./luis-quickstart-node-add-utterance.md) e [Compilare un'app LUIS a livello di codice con Node.js](./luis-tutorial-node-import-utterances-csv.md) per esempi su come chiamare l'API di creazione. L'API di creazione richiede l'uso di una [chiave di creazione](luis-concept-keys.md#azure-resources-for-luis) e non di una chiave di endpoint. La creazione a livello di codice consente fino a 1.000.000 di chiamate al mese e cinque transazioni al secondo. Per altre informazioni sulle chiavi usate con LUIS, vedere [Gestire le chiavi](./luis-concept-keys.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Dove si trova la funzionalità Criterio che offre l'associazione di espressioni regolari?
La **funzionalità Criterio** precedente è attualmente deprecata ed è sostituita da **[Modelli](luis-concept-patterns.md)** .

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Come si può usare un'entità per estrarre i dati corretti?
Vedere [entità](luis-concept-entity-types.md) ed [estrazione dei dati](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Le variazioni di un'espressione di esempio devono includere la punteggiatura?
Aggiungere le variazioni come espressioni di esempio alla finalità o aggiungere il criterio dell'espressione di esempio con la [sintassi per ignorare](luis-concept-patterns.md#pattern-syntax) la punteggiatura.

### <a name="does-luis-currently-support-cortana"></a>LUIS supporta attualmente Cortana?

Le app Cortana predefinite sono state deprecate nel 2017. Non sono più supportate.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Come si trasferisce la proprietà di un'app LUIS?
Per trasferire un'app LUIS in un'altra sottoscrizione di Azure, esportare l'app LUIS e importarla usando un nuovo account. Aggiornare l'ID dell'app LUIS nell'applicazione client che la chiama. La nuova app può restituire punteggi LUIS leggermente diversi rispetto all'app originale.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>Un'entità predefinita è contrassegnata con un enunciato di esempio invece che con l'entità personalizzata. Ricerca per categorie risolvere questo problema? 

Nel portale LUIS è possibile etichettare il testo per l'entità esatta a cui si è interessati per l'estrazione. Se il portale LUIS non Visualizza la stima dell'entità corretta, potrebbe essere necessario aggiungere più espressioni ed etichettare l'entità all'interno del testo o aggiungere un descrittore (ad esempio una funzionalità). 

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Si è tentato di importare un'app o un file di versione, ma si è verificato un errore? 

Altre informazioni sugli [errori di importazione della versione](luis-how-to-manage-versions.md#import-errors).

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>Collaborazione e contributo

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-role-based-access-control-rbac"></a>Come si concede ai collaboratori l'accesso a LUIS con Azure Active Directory (Azure AD) o il controllo degli accessi in base al ruolo (RBAC)?

Per informazioni su come concedere l'accesso ai collaboratori, vedere [Risorse di Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-resources) e [Utente del tenant di Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-tenant-user). 

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Endpoint

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>È stato ricevuto un codice di stato di errore HTTP 403. Risoluzione

Si ottengono i codici di stato di errore 403 e 429 quando si superano le transazioni al secondo o le transazioni al mese per il piano tariffario. Aumentare il piano tariffario o usare Language Understanding [contenitori](luis-container-howto.md).

Quando si usano tutte le query di endpoint 1000 gratuite o si supera la quota di transazioni mensili del piano tariffario, si riceve un codice di stato di errore HTTP 403. 

Per correggere l'errore, è necessario [modificare il piano tariffario](luis-how-to-azure-subscription.md#change-pricing-tier) a un livello superiore o [creare una nuova risorsa](get-started-portal-deploy-app.md#create-the-endpoint-resource) e [assegnarla all'app](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

Le soluzioni per questo errore includono:

* Nel [portale di Azure](https://portal.azure.com), sulla risorsa Language Understanding, nel piano **tariffario di gestione delle risorse->** modificare il piano tariffario in un livello di TPS superiore. Non è necessario eseguire alcuna operazione nel portale di Language Understanding se la risorsa è già stata assegnata all'app Language Understanding.
*  Se l'utilizzo supera il piano tariffario più elevato, aggiungere altre Language Understanding risorse con un servizio di bilanciamento del carico. Il [contenitore Language Understanding](luis-container-howto.md) con Kubernetes o Docker compose può essere utile per questa operazione.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>È stato ricevuto un codice di stato di errore HTTP 429. Risoluzione

Si ottengono i codici di stato di errore 403 e 429 quando si superano le transazioni al secondo o le transazioni al mese per il piano tariffario. Aumentare il piano tariffario o usare Language Understanding [contenitori](luis-container-howto.md).

Questo codice di stato viene restituito quando le transazioni al secondo superano il piano tariffario.  

Le soluzioni includono:

* Se non si è al livello più elevato, è possibile [aumentare il piano tariffario](luis-how-to-azure-subscription.md#change-pricing-tier).
* Se l'utilizzo supera il piano tariffario più elevato, aggiungere altre Language Understanding risorse con un servizio di bilanciamento del carico. Il [contenitore Language Understanding](luis-container-howto.md) con Kubernetes o Docker compose può essere utile per questa operazione.
* È possibile controllare le richieste dell'applicazione client con i [criteri di ripetizione](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) implementati quando si ottiene questo codice di stato. 

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>La query dell'endpoint ha restituito risultati imprevisti. Cosa devo fare?

I risultati imprevisti della query dipendono dallo stato del modello pubblicato. Per correggere il modello, può essere necessario modificare il modello, eseguire il training e ripetere la pubblicazione. 

La correzione del modello inizia con l'[apprendimento attivo](luis-how-to-review-endpoint-utterances.md).

È possibile rimuovere il training non deterministico aggiornando l'[API delle impostazioni della versione dell'applicazione](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) in modo da usare tutti i dati di training.

Per altri suggerimenti, rivedere le [procedure consigliate](luis-concept-best-practices.md). 

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Perché LUIS aggiunge spazi alla query attorno o in mezzo alle parole?
LUIS [suddivide in token](luis-glossary.md#token) l'espressione in base alle [impostazioni cultura](luis-language-support.md#tokenization). Il valore originale e il valore in formato token sono entrambi disponibili per l'[estrazione dei dati](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Come è possibile creare e assegnare una chiave di endpoint di LUIS?
[Creare la chiave di endpoint](luis-how-to-azure-subscription.md) in Azure per il livello di [servizio](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/). [Assegnare la chiave](luis-how-to-azure-subscription.md) nella pagina **[delle risorse di Azure](luis-how-to-azure-subscription.md)** . Non vi è alcuna API corrispondente per questa azione. È necessario quindi modificare la richiesta HTTP all'endpoint per fare in modo che [usi la nuova chiave dell'endpoint](luis-concept-keys.md).

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
Seguire queste [istruzioni](#how-do-i-create-and-assign-a-luis-endpoint-key) per creare una chiave dell'endpoint Luis e assegnarla all'app. Quindi è necessario modificare la richiesta HTTP dell'applicazione client all'endpoint per [usare la nuova chiave dell'endpoint](luis-concept-keys.md). Se è stata creata una nuova risorsa in un'area diversa, modificare anche l'area della richiesta del client HTTP.

### <a name="how-do-i-secure-my-luis-endpoint"></a>Come si protegge l'endpoint LUIS?
Vedere [Protezione dell'endpoint](luis-concept-keys.md#securing-the-endpoint).

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

Per informazioni sulle differenze tra la chiave di creazione e la chiave del runtime di stima, vedere [creazione e modifica delle chiavi degli endpoint di stima in Luis](luis-concept-keys.md) . 

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>È stato restituito un errore per segnalare il superamento della quota. Risoluzione 

Per altre informazioni, vedere correggere il codice di stato HTTP [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) e [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) .

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>È necessario gestire più query di endpoint. Come procedere? 

Per altre informazioni, vedere correggere il codice di stato HTTP [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) e [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) .

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>È stata creata una chiave di creazione, ma non viene visualizzata nel portale LUIS. Che cosa è successo?

Le chiavi di creazione sono disponibili nel portale LUIS dopo [la migrazione all'esperienza di creazione della chiave](luis-migration-authoring.md).  

## <a name="app-management"></a>Gestione app

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Come si scarica un log delle espressioni degli utenti?
Per impostazione predefinita, l'app LUIS registra le espressioni degli utenti. Per scaricare un log delle espressioni che gli utenti inviano all'app LUIS, visitare **Le mie app** e selezionare l'app. Sulla barra degli strumenti contestuale selezionare **Export Endpoint Logs** (Esporta log degli endpoint). Il log viene formattato come un file con valori delimitati da virgole (CSV).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Come si disabilita la registrazione delle espressioni?
È possibile disattivare la registrazione delle espressioni degli utenti impostando `log=false` nell'URL dell'endpoint che l'applicazione client usa per inviare query a LUIS. Tuttavia, la disattivazione della registrazione impedisce all'app LUIS di suggerire espressioni o migliorare le prestazioni basate sull'[apprendimento attivo](luis-concept-review-endpoint-utterances.md#what-is-active-learning). Se si imposta `log=false` a causa di problemi di privacy dei dati, non sarà possibile scaricare un record delle espressioni degli utenti da LUIS o usarle per migliorare l'app.

La registrazione è l'unico spazio di archiviazione delle espressioni.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Perché un utente potrebbe non desiderare la registrazione di tutte le espressioni degli endpoint?
Se si usa il log per l'analisi delle stime, non acquisire le espressioni di test nel log.

## <a name="data-management"></a>Gestione dati

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

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>Il bot LUIS non funziona. che cosa occorre fare?

La prima cosa da fare è determinare se il problema è correlato a LUIS o avviene al difuori del middleware LUIS. 

#### <a name="resolve-issue-in-luis"></a>Risolvere il problema LUIS
Passare la stessa espressione a LUIS dall'[endpoint LUIS](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint). Se si riceve un errore, risolvere il problema in LUIS finché l'errore non viene più restituito. Di seguito sono riportati gli errori più comuni.

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

## <a name="migrating-to-the-next-version"></a>Migrazione alla versione successiva

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Ricerca per categorie eseguire la migrazione all'API anteprima V3? 

Vedere [la guida alla migrazione da API v2 a V3 per le app Luis](luis-migration-api-v3.md)

## <a name="build-2019-conference-announcements"></a>Build 2019 annunci di conferenza

Le funzionalità seguenti sono state rilasciate alla conferenza Build 2019:

* [Anteprima della Guida alla migrazione dell'API V3](luis-migration-api-v3.md)
* [Dashboard di analisi migliorato](luis-how-to-use-dashboard.md)
* [Domini predefiniti migliorati](luis-reference-prebuilt-domains.md) 
* [Entità elenco dinamico](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [Entità esterne](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

Video:

* [Come usare l'intelligenza artificiale per la conversazione di Azure per la scalabilità dell'azienda per la prossima generazione](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su LUIS, vedere le risorse seguenti:
* [Domande su Stack Overflow con tag LUIS](https://stackoverflow.com/questions/tagged/luis)
* [Forum MSDN su LUIS (Language Understanding Intelligent Service)](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS)
