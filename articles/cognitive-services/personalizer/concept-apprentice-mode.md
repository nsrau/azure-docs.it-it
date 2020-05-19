---
title: Modalità apprendista-personalizzatore
description: ''
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 2697ab4b32edbd4841f2b11725fda46e90e7ae7e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599403"
---
# <a name="use-apprentice-mode-to-train-personalizer-without-affecting-your-existing-application"></a>Usare la modalità apprendista per eseguire il training di personalizzazione senza influire sull'applicazione esistente

A causa della natura dell'apprendimento del rinforzo **reale** , è possibile eseguire il training di un modello di personalizzazione solo in un ambiente di produzione. Quando si distribuisce un nuovo caso d'uso, il modello di personalizzazione non viene eseguito in modo efficiente perché richiede tempo sufficiente per il training del modello.  La **modalità Apprentice** è un comportamento di apprendimento che semplifica questa situazione e consente di ottenere confidenza nel modello, senza che lo sviluppatore modifichi il codice.

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="what-is-apprentice-mode"></a>Che cos'è la modalità apprendista?

Analogamente a come un apprendista apprende da un master e con esperienza può migliorare; La modalità Apprentice è un _comportamento_ che consente a personalizzazione di apprendere osservando i risultati ottenuti dalla logica dell'applicazione esistente.

I treni di personalizzazione simulano lo stesso output dell'applicazione. Con l'aumentare del flusso di eventi, il Personalizzatore può _recuperare_ l'applicazione esistente senza influire sulla logica e sui risultati esistenti. Le metriche, disponibili dal portale di Azure e dall'API, consentono di comprendere le prestazioni apprese dal modello.

Dopo aver appreso e raggiunto un certo livello di comprensione, lo sviluppatore può modificare il comportamento dalla modalità apprendista alla modalità online. In quel momento, la personalizzazione inizia a influenzare le azioni nell'API di rango.

## <a name="purpose-of-apprentice-mode"></a>Scopo della modalità apprendista

La modalità apprendista offre una relazione di trust con il servizio di personalizzazione e le funzionalità di apprendimento automatico e garantisce che il servizio sia in grado di inviare informazioni che possono essere apprese da, senza rischiare il traffico in linea.

I due motivi principali per usare la modalità apprendista sono:

* Mitigazione degli **avvii a freddo**: la modalità apprendista consente di gestire e valutare il costo di un nuovo tempo di apprendimento del modello, quando non viene restituita l'azione migliore e non è stato possibile ottenere un livello di efficacia soddisfacente pari a circa il 75-85%.
* **Convalida delle funzionalità di azione e contesto**: le funzionalità inviate in azioni e contesto potrebbero non essere adeguate o non accurate, troppo piccole, troppo, non corrette o troppo specifiche per il training di Personalizzatore per ottenere la velocità di efficacia ideale. Usare le [valutazioni delle funzionalità](concept-feature-evaluation.md) per individuare e risolvere i problemi relativi alle funzionalità.

## <a name="when-should-you-use-apprentice-mode"></a>Quando è consigliabile usare la modalità Apprentice?

Usare la modalità apprendista per eseguire il training di personalizzazione per migliorarne l'efficacia tramite gli scenari seguenti lasciando inalterata l'esperienza degli utenti:

* Si sta implementando il Personalizzatore in un nuovo caso di utilizzo.
* Sono state modificate in modo significativo le funzionalità inviate in un contesto o in azioni.
* Si è apportato un cambiamento significativo quando e come si calcolano i vantaggi.

La modalità Apprentice non è un modo efficace per misurare la personalizzazione dell'effetto sui punteggi di ricompensa. Per misurare il modo in cui il Personalizzatore efficace è scegliere la migliore azione possibile per ogni chiamata di rango, usare le [valutazioni offline](concepts-offline-evaluation.md).

## <a name="who-should-use-apprentice-mode"></a>Chi deve usare la modalità Apprentice?

La modalità Apprentice è utile per sviluppatori, data scientist e decision maker aziendali:

* **Gli sviluppatori** possono usare la modalità Apprentice per assicurarsi che le API Rank e Reward vengano usate correttamente nell'applicazione e che le funzionalità inviate al personalizzatore dall'applicazione non contengano bug o funzionalità non rilevanti, ad esempio un elemento timestamp o userid.

* I **Data Scientist** possono usare la modalità apprendista per verificare che le funzionalità siano efficaci per il training dei modelli di personalizzazione, che i tempi di attesa del premio non siano troppo lunghi o brevi.

* I **decisori aziendali** possono utilizzare la modalità apprendista per valutare le potenzialità del Personalizzatore per migliorare i risultati (vale a dire i premi) rispetto alla logica di business esistente. Questo consente loro di prendere una decisione informata che influisca negativamente sull'esperienza degli utenti, in cui sono presenti ricavi reali e soddisfazione degli utenti.

## <a name="comparing-behaviors---apprentice-mode-and-online-mode"></a>Confronto tra comportamenti-modalità Apprentice e modalità online

L'apprendimento in modalità apprendista differisce dalla modalità online nei modi seguenti.

|Area|Modalità Apprentice|ModalitàOnline|
|--|--|--|
|Impatto sull'esperienza utente|È possibile utilizzare il comportamento utente esistente per eseguire il training di personalizzatore, consentendo di osservare (non influire) quale sarà l' **azione predefinita** e il premio ottenuto. Ciò significa che l'esperienza degli utenti e i risultati aziendali non saranno interessati.|Visualizza l'azione principale restituita dalla chiamata di rango per influenzare il comportamento dell'utente.|
|Velocità di apprendimento|Il Personalizzatore apprenderà più lentamente in modalità apprendista che durante l'apprendimento in modalità online. La modalità apprendista può apprendere solo osservando i premi ottenuti dall' **azione predefinita**, che limita la velocità di apprendimento, in quanto non è possibile eseguire l'esplorazione.|Impara più velocemente perché può sfruttare il modello corrente ed esplorare le nuove tendenze.|
|"CEILING" per l'efficacia dell'apprendimento|Il Personalizzatore può approssimarsi, molto raramente e non superare mai le prestazioni della logica di business di base (il totale della ricompensa ottenuto dall' **azione predefinita** di ogni chiamata di rango).|Il Personalizzatore deve superare la linea di base delle applicazioni e nel tempo in cui si blocca è necessario eseguire la valutazione offline e la valutazione delle funzionalità per continuare a ottenere miglioramenti al modello. |
|Valore dell'API Rank per rewardActionId|L'esperienza degli utenti non ha alcun effetto, perché _rewardActionId_ è sempre la prima azione da inviare nella richiesta di rango. In altre parole, l'API Rank non è visibile per l'applicazione durante la modalità Apprentice. Le API Reward nell'applicazione non devono modificare il modo in cui viene usata l'API Reward tra una modalità e un'altra.|L'esperienza degli utenti verrà modificata da _rewardActionId_ che il Personalizzatore sceglie per l'applicazione. |
|Giudizi|Il Personalizzatore mantiene un confronto tra i totali di ricompensa che la logica di business predefinita sta ottenendo e la personalizzazione dei totali del premio viene ottenuta in modalità online. Un confronto è disponibile nel portale di Azure per tale risorsa|Valutare l'efficacia del personalizzatore eseguendo [valutazioni offline](concepts-offline-evaluation.md), che consentono di confrontare il totale dei vantaggi del personalizzatore rispetto ai potenziali vantaggi della linea di base dell'applicazione.|

Nota sull'efficacia della modalità Apprentice:

* L'efficacia del personalizzatore in modalità apprendista raggiunge raramente quasi il 100% della linea di base dell'applicazione; senza mai superarlo.
* Le procedure consigliate non possono tentare di raggiungere il 100% di raggiungimento; Tuttavia, un intervallo compreso tra 75 e 85% deve essere destinato a seconda del caso d'uso.

## <a name="using-apprentice-mode-to-train-with-historical-data"></a>Uso della modalità apprendista per eseguire il training con i dati cronologici

Se si dispone di una quantità significativa di dati cronologici, si desidera utilizzare per il training di Personalizzator, è possibile utilizzare la modalità apprendista per riprodurre i dati tramite la personalizzazione.

Configurare il Personalizzatore in modalità apprendista e creare uno script che chiama Rank con le azioni e le funzionalità di contesto dei dati cronologici. Chiamare l'API Reward in base ai calcoli dei record in questi dati. Per visualizzare alcuni risultati sono necessari circa 50.000 eventi cronologici, ma è consigliabile usare 500.000 per una maggiore confidenza nei risultati.

Quando si esegue il training dai dati cronologici, è consigliabile che i dati inviati (funzionalità per contesto e azioni, il relativo layout nel codice JSON usato per le richieste di rango e il calcolo della ricompensa in questo training set) corrispondano ai dati (funzionalità e calcolo della ricompensa) disponibili dall'applicazione esistente.

I dati offline e post-facto tendono a essere più incompleti e più rumorosi e diversi nel formato. Sebbene sia possibile eseguire il training dei dati cronologici, il risultato di questa operazione può essere senza risultati e non è un buon predittore dell'apprendimento dell'appropriazione della personalizzazione, soprattutto se le funzionalità variano tra i dati precedenti e l'applicazione esistente.

In genere, per la personalizzazione, rispetto alla formazione con i dati cronologici, la modifica del comportamento alla modalità apprendista e l'apprendimento da un'applicazione esistente è un percorso più efficace per avere un modello efficace, con minori attività lavorative, progettazione dei dati e pulizia.

## <a name="using-apprentice-mode-versus-ab-tests"></a>Uso della modalità apprendista rispetto ai test A/B

È utile solo per i test A/B dei trattamenti di personalizzazione dopo che è stata convalidata e sta imparando in modalità online. In modalità apprendista viene utilizzata solo l' **azione predefinita** , il che significa che tutti gli utenti visualizzeranno efficacemente l'esperienza di controllo.

Anche se la personalizzazione è solo il _trattamento_, la stessa sfida è presente quando la convalida dei dati è valida per la personalizzazione del training. In alternativa, è possibile usare la modalità Apprentice con il 100% del traffico e con tutti gli utenti che ricevono l'esperienza di controllo (non interessata).

Una volta creato un caso d'uso usando il Personalizzatore e la formazione online, gli esperimenti A/B consentono di eseguire coorti controllati e confronto scientifico dei risultati che potrebbero essere più complessi rispetto ai segnali usati per le premiazioni. Un test A/B può rispondere A una domanda di esempio:`In a retail website, Personalizer optimizes a layout and gets more users to _check out_ earlier, but does this reduce total revenue per transaction?`

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sugli [eventi attivi e inattivi](concept-active-inactive-events.md)