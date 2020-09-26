---
title: Test per DevOps per le app LUIS
description: Come testare l'app Language Understanding (LUIS) in un ambiente DevOps.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/3/2020
ms.openlocfilehash: c41e9fe1f197334bce27241ab9f28309c92f7e0a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316546"
---
# <a name="testing-for-luis-devops"></a>Test per LUIS DevOps

I tecnici software che stanno sviluppando un'app Language Understanding (LUIS) possono applicare procedure DevOps per il [controllo del codice sorgente](luis-concept-devops-sourcecontrol.md), le [compilazioni automatizzate](luis-concept-devops-automation.md), i [test](luis-concept-devops-testing.md)e la [gestione delle versioni](luis-concept-devops-automation.md#release-management) attenendosi alle linee guida.

Nelle metodologie Agile Software Development il test svolge un ruolo integrale nella creazione di software di qualità. Ogni modifica significativa a un'app LUIS deve essere accompagnata da test progettati per testare le nuove funzionalità che lo sviluppatore sta creando nell'app. Questi test vengono archiviati nel repository del codice sorgente insieme all' `.lu` origine dell'app Luis. L'implementazione della modifica è terminata quando l'app soddisfa i test.

I test sono una parte essenziale dei [flussi di lavoro ci/CD](luis-concept-devops-automation.md). Quando le modifiche apportate a un'app LUIS vengono proposte in una richiesta pull (PR) o dopo l'Unione delle modifiche nel ramo master, i flussi di lavoro CI devono eseguire i test per verificare che gli aggiornamenti non abbiano causato alcuna regressione.

## <a name="how-to-do-unit-testing-and-batch-testing"></a>Come eseguire unit test e test batch

Esistono due tipi diversi di test per un'app LUIS che è necessario eseguire nei flussi di lavoro di integrazione continua:

- **Unit test** : test relativamente semplici che verificano le funzionalità principali dell'app Luis. Una unit test viene passata quando viene restituita la finalità prevista e le entità previste per una determinata espressione di test. Per completare correttamente l'esecuzione dei test, è necessario che tutti gli unit test vengano superati.  
Questo tipo di test è simile ai [test interattivi](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test) che è possibile eseguire nel [portale Luis](https://www.luis.ai/).

- **Test batch** : il test batch è un test completo sul modello attualmente sottoposto a training per misurarne le prestazioni. Diversamente dagli unit test, i test batch non vengono superati | test non superati. La previsione con test batch non prevede che ogni test restituisca la finalità prevista e le entità previste. Al contrario, un test di batch consente di visualizzare l'accuratezza di ogni finalità ed entità nell'app e consente di confrontare nel tempo quando si apportano miglioramenti.  
Questo tipo di test è identico a quello dei [test batch](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test) che è possibile eseguire in modo interattivo nel portale Luis.

È possibile usare il testing unità dall'inizio del progetto. I test batch sono solo di valore dopo aver sviluppato lo schema dell'app LUIS e si sta lavorando per migliorarne l'accuratezza.

Per gli unit test e i test batch, assicurarsi che le espressioni di test siano mantenute separate dalle espressioni di training. Se si esegue il test sugli stessi dati su cui si esegue il training, si otterrà la falsa impressione che l'applicazione sta funzionando correttamente quando si tratta semplicemente dell'overfitting dei dati di test. I test devono essere rilevati dal modello per verificarne la generalizzazione.

### <a name="writing-tests"></a>Scrittura di test

Quando si scrive un set di test, per ogni test è necessario definire:

* Espressione di test
* Finalità prevista
* Entità previste.

Usare la [sintassi del file batch](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test#batch-syntax-template-for-intents-with-entities) Luis per definire un gruppo di test in un file in formato JSON. Ad esempio:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities":
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

Alcuni strumenti di test, ad esempio [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) supporta anche i file di test in formato LUDown.

#### <a name="designing-unit-tests"></a>Progettazione di unit test

Gli unit test devono essere progettati per testare le funzionalità principali dell'app LUIS. In ogni iterazione o sprint dello sviluppo di app è necessario scrivere un numero sufficiente di test per verificare che la funzionalità chiave implementata in tale iterazione funzioni correttamente.

In ogni unit test, per una determinata espressione di test, è possibile:

* Verificare che venga restituito lo scopo corretto
* Verificare che vengano restituite le entità' Key ', quelle cruciali per la soluzione.
* Verificare che il [Punteggio di stima](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-prediction-score) per finalità ed entità superi una soglia definita dall'utente. Ad esempio, è possibile decidere di considerare solo che un test è stato superato se il Punteggio di stima per l'Intent e per le entità chiave supera 0,75.

Negli unit test, è consigliabile verificare che le entità chiave siano state restituite nella risposta di stima, ma ignorare i falsi positivi. I *falsi positivi* sono entità che si trovano nella risposta di stima ma che non sono definite nei risultati previsti per il test. Ignorando i falsi positivi, diventa meno oneroso creare unit test consentendo allo stesso tempo di concentrarsi sul testing che i dati chiave per la soluzione vengono restituiti in una risposta di stima.

> [!TIP]
> [NLU. ](https://github.com/microsoft/NLU.DevOps)Lo strumento DevOps supporta tutte le esigenze di test di Luis. Il `compare` comando utilizzato in [modalità unit test](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode) asserirà che tutti i test vengano superati e ignorerà i risultati falsi positivi per le entità che non sono etichettate nei risultati previsti.

#### <a name="designing-batch-tests"></a>Progettazione di test batch

I set di test batch devono contenere un numero elevato di test case, progettati per eseguire test su tutti gli Intent e tutte le entità nell'app LUIS. Per informazioni sulla definizione di un set di test batch, vedere [test di batch nel portale Luis](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test) .

### <a name="running-tests"></a>Esecuzione di test

Il portale LUIS offre funzionalità che consentono di eseguire test interattivi:

* Il [**testing interattivo**](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test) consente di inviare un enunciato di esempio e di ottenere una risposta di Intent ed entità riconosciuti da Luis. Verificare l'esito positivo del test tramite l'ispezione visiva.

* Il [**test batch**](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test) usa un file di test batch come input per convalidare la versione con Training attivo per misurare l'accuratezza della stima. Un test batch consente di visualizzare l'accuratezza di ogni finalità ed entità nella versione attiva, visualizzando i risultati con un grafico.

#### <a name="running-tests-in-an-automated-build-workflow"></a>Esecuzione di test in un flusso di lavoro di compilazione automatizzato

Le funzionalità di test interattive nel portale LUIS sono utili, ma per DevOps i test automatizzati eseguiti in un flusso di lavoro di integrazione continua/recapito continuo implicano alcuni requisiti:

* Gli strumenti di test devono essere eseguiti in un passaggio del flusso di lavoro in un server di compilazione. Questo significa che gli strumenti devono essere in grado di essere eseguiti nella riga di comando.
* Gli strumenti di test devono essere in grado di eseguire un gruppo di test rispetto a un endpoint e verificare automaticamente i risultati previsti in base ai risultati effettivi.
* Se i test hanno esito negativo, gli strumenti di test devono restituire un codice di stato per arrestare il flusso di lavoro e "interrompere la compilazione".

LUIS non offre uno strumento da riga di comando o un'API di alto livello che offre queste funzionalità. Si consiglia di usare [NLU. Strumento DevOps](https://github.com/microsoft/NLU.DevOps) per eseguire i test e verificare i risultati, sia dalla riga di comando che durante i test automatizzati in un flusso di lavoro ci/CD.

Le funzionalità di test disponibili nel portale LUIS non richiedono un endpoint pubblicato e fanno parte delle funzionalità di authoring LUIS. Quando si implementano i test in un flusso di lavoro di compilazione automatizzato, è necessario pubblicare la versione dell'app LUIS da testare in un endpoint in modo che gli strumenti di test come NLU. DevOps può inviare richieste di stima come parte del test.

> [!TIP]
> * Se si sta implementando una soluzione di test personalizzata e si scrive codice per inviare le espressioni di test a un endpoint, tenere presente che se si utilizza la chiave LUIS authoring, la frequenza delle transazioni consentita è limitata a 5TPS. Limitare la velocità di invio o utilizzare invece una chiave di stima.
> * Quando si inviano query di test a un endpoint, ricordarsi di utilizzare `log=false` nella stringa di query della richiesta di stima. In questo modo si garantisce che le espressioni di test non vengano registrate da LUIS e finiscano nell'elenco di revisione di espressioni di endpoint presentato dalla funzionalità di [apprendimento attivo](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances) Luis e, di conseguenza, vengono aggiunti accidentalmente alle espressioni di training dell'app.

#### <a name="running-unit-tests-at-the-command-line-and-in-cicd-workflows"></a>Esecuzione di unit test dalla riga di comando e nei flussi di lavoro CI/CD

È possibile usare [NLU. Pacchetto DevOps](https://github.com/microsoft/NLU.DevOps) per eseguire i test dalla riga di comando:

* Usare NLU. [Comando di test](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md) DevOps per inviare i test da un file di test a un endpoint e acquisire i risultati di stima effettivi in un file.
* Usare NLU. [Comando DevOps compare](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md) per confrontare i risultati effettivi con i risultati previsti definiti nel file di test di input. Il `compare` comando genera l'output del test NUnit e, quando viene usato in [modalità unit test](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode) usando il `--unit-test` flag, asserirà che tutti i test vengano superati.

### <a name="running-batch-tests-at-the-command-line-and-in-cicd-workflows"></a>Esecuzione di test batch dalla riga di comando e nei flussi di lavoro CI/CD

È anche possibile usare NLU. Pacchetto DevOps per l'esecuzione di test batch dalla riga di comando.

* Usare NLU. [Comando di test](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md) di DevOps per inviare i test da un file di test a un endpoint e acquisire i risultati di stima effettivi in un file, come con gli unit test.
* Usare NLU. [Comando DevOps compare](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md) in [modalità di test delle prestazioni](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#performance-test-mode) per misurare le prestazioni dell'app. è anche possibile confrontare le prestazioni dell'app con un benchmark delle prestazioni di base, ad esempio i risultati dell'ultimo commit al master o la versione corrente. In modalità di test delle prestazioni, il `compare` comando genera l'output del test NUnit e [i risultati del test batch](https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#batch-test) in formato JSON.

## <a name="luis-non-deterministic-training-and-the-effect-on-testing"></a>Formazione non deterministica LUIS e effetto sui test

Quando LUIS esegue il training di un modello, ad esempio un Intent, richiede entrambi i dati positivi, ovvero le espressioni di training con etichetta fornite per eseguire il training dell'app per il modello e i dati negativi, che *non* sono esempi validi dell'utilizzo di tale modello. Durante il training, LUIS compila i dati negativi di un modello da tutti i dati positivi forniti per gli altri modelli, ma in alcuni casi può produrre uno squilibrio dei dati. Per evitare questo squilibrio, LUIS campiona un subset dei dati negativi in modo non deterministico per ottimizzare un set di training più equilibrato, migliorare le prestazioni del modello e tempi di training più veloci.

Il risultato di questa formazione non deterministica è che è possibile ottenere una [risposta di stima leggermente diversa tra diverse sessioni di training](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-prediction-score), in genere per gli Intent e/o le entità in cui il Punteggio di [stima](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-prediction-score) non è elevato.

Se si vuole disabilitare il training non deterministico per le versioni di app LUIS che si sta creando ai fini del test, usare l' [API delle impostazioni della versione](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) con l' `UseAllTrainingData` impostazione impostata su `true` .

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sull' [implementazione dei flussi di lavoro ci/CD](luis-concept-devops-automation.md)
* Informazioni su come [implementare DevOps per Luis con GitHub](luis-how-to-devops-with-github.md)