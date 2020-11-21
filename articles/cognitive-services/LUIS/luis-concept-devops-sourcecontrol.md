---
title: Rami di controllo del codice sorgente e sviluppo-LUIS
description: Come gestire l'app Language Understanding (LUIS) nel controllo del codice sorgente. Come applicare gli aggiornamenti a un'app LUIS mentre si lavora in un ramo di sviluppo.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: 9e322ac89d8ecad93c2002aa302c155f895911f4
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95019195"
---
# <a name="devops-practices-for-luis"></a>Procedure della metodologia DevOps per LUIS

I tecnici software che stanno sviluppando un'app Language Understanding (LUIS) possono applicare procedure DevOps per il [controllo del codice sorgente](luis-concept-devops-sourcecontrol.md), le [compilazioni automatizzate](luis-concept-devops-automation.md), i [test](luis-concept-devops-testing.md)e la [gestione delle versioni](luis-concept-devops-automation.md#release-management) attenendosi alle linee guida.

## <a name="source-control-and-branch-strategies-for-luis"></a>Strategie di controllo del codice sorgente e gestione rami per LUIS

Uno dei fattori chiave che dipende dal successo di DevOps è il controllo del [codice sorgente](/azure/devops/user-guide/source-control?view=azure-devops). Un sistema di controllo del codice sorgente consente agli sviluppatori di collaborare al codice e di tenere traccia delle modifiche. L'uso dei rami consente agli sviluppatori di passare da una versione all'altra della codebase e di lavorare in modo indipendente da altri membri del team. Quando gli sviluppatori generano una [richiesta pull](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) (PR) per proporre gli aggiornamenti da un ramo a un altro o quando le modifiche vengono unite, possono essere il trigger per le [compilazioni automatiche](luis-concept-devops-automation.md) per compilare e testare continuamente il codice.

Usando i concetti e le linee guida descritte in questo documento, è possibile sviluppare un'app LUIS durante il rilevamento delle modifiche in un sistema di controllo del codice sorgente e seguire le procedure consigliate per la progettazione del software:

- **Controllo del codice sorgente**
  - Il codice sorgente per l'app LUIS è in un formato leggibile.
  - Il modello può essere compilato dall'origine in modo ripetibile.
  - Il codice sorgente può essere gestito da un repository di codice sorgente.
  - Le credenziali e i segreti, ad esempio le chiavi di creazione e sottoscrizione, non vengono mai archiviati nel codice sorgente.

- **Creazione di rami e merge**
  - Gli sviluppatori possono lavorare da rami indipendenti.
  - Gli sviluppatori possono lavorare simultaneamente in più rami.
  - È possibile integrare le modifiche apportate a un'app LUIS da un ramo a un altro tramite la riassegnazione o il merge.
  - Gli sviluppatori possono unire una richiesta pull al ramo padre.

- **Controllo delle versioni**
  - Per ogni componente di un'applicazione di grandi dimensioni è necessario eseguire il controllo delle versioni in modo indipendente, consentendo agli sviluppatori di rilevare modifiche o aggiornamenti di rilievo semplicemente esaminando il numero di versione.

- **Revisioni del codice**
  - Le modifiche nella richiesta pull vengono presentate come codice sorgente leggibile che può essere esaminato prima di accettare la richiesta pull.

## <a name="source-control"></a>Controllo del codice sorgente

Per mantenere la [definizione dello schema dell'app](./app-schema-definition.md) di un'app Luis in un sistema di gestione del codice sorgente, usare la rappresentazione [LUDown Format ( `.lu` )](/azure/bot-service/file-format/bot-builder-lu-file-format?view=azure-bot-service-4.0)  dell'app. `.lu` è preferibile formattare `.json` il formato perché è leggibile, che rende più semplice apportare ed esaminare le modifiche nelle richieste pull.

### <a name="save-a-luis-app-using-the-ludown-format"></a>Salvare un'app LUIS usando il formato LUDown

Per salvare un'app LUIS nel `.lu` formato e inserirla nel controllo del codice sorgente:

- Eseguire entrambe [le operazioni: esportare la versione dell'app](./luis-how-to-manage-versions.md#other-actions) `.lu` dal [portale Luis](https://www.luis.ai/) e aggiungerla al repository del controllo del codice sorgente

- In alternativa, usare un editor di testo per creare un `.lu` file per un'app Luis e aggiungerlo al repository del controllo del codice sorgente

> [!TIP]
> Se si lavora con l'esportazione JSON di un'app LUIS, è possibile [convertirla in LUDown](https://github.com/microsoft/botframework-cli/tree/master/packages/luis#bf-luisconvert) usando l'interfaccia della riga di comando di [BOTBUILDER-Tools Luis](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS). Usare l' `--sort` opzione per assicurarsi che gli Intent e le espressioni siano ordinati alfabeticamente.  
> Si noti che **.** La funzionalità di esportazione Lu incorporata nel portale Luis Ordina già l'output.

### <a name="build-the-luis-app-from-source"></a>Compilare l'app LUIS dall'origine

Per un'app LUIS, per *compilare da un'origine* significa [creare una nuova versione dell'app Luis importando l' `.lu` origine](./luis-how-to-manage-versions.md#import-version) , per eseguire [il training della versione](./luis-how-to-train.md) e per [pubblicarla](./luis-how-to-publish-app.md). Questa operazione può essere eseguita nel portale LUIS o nella riga di comando:

- Usare il portale LUIS per [importare la `.lu` versione](./luis-how-to-manage-versions.md#import-version) dell'app dal controllo del codice sorgente e per eseguire il [Training](./luis-how-to-train.md) e la [pubblicazione](./luis-how-to-publish-app.md) dell'app.

- Usare l' [interfaccia della riga di comando di bot Framework per Luis](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) dalla riga di comando o in un flusso di lavoro di integrazione continua/distribuzione continua per [importare](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisversionimport) la `.lu` versione dell'app dal controllo del codice sorgente in un'applicazione Luis, quindi eseguire il [Training](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luistrainrun) e [pubblicare](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish) l'app.

### <a name="files-to-maintain-under-source-control"></a>File da gestire nel controllo del codice sorgente

I seguenti tipi di file per l'applicazione LUIS devono essere conservati nel controllo del codice sorgente:

- `.lu` file per l'applicazione LUIS

- [File di definizione unit test](luis-concept-devops-testing.md#writing-tests) (espressioni e risultati previsti)

- [File di test batch](./luis-concept-batch-test.md#batch-file-format) (espressioni e risultati previsti) usati per il test delle prestazioni

### <a name="credentials-and-keys-are-not-checked-in"></a>Le credenziali e le chiavi non vengono archiviate

Non includere chiavi di sottoscrizione o valori riservati simili nei file che si archiviano nel repository in cui potrebbero essere visibili a personale non autorizzato. Le chiavi e altri valori da impedire dall'archiviazione includono:

- Chiavi di stima e creazione di LUIS
- Endpoint per la creazione e la stima di LUIS
- Chiavi della sottoscrizione di Azure
- Token di accesso, ad esempio il token per un' [entità servizio](/cli/azure/ad/sp?view=azure-cli-latest) di Azure usata per l'autenticazione di automazione

#### <a name="strategies-for-securely-managing-secrets"></a>Strategie per la gestione sicura dei segreti

Le strategie per la gestione sicura dei segreti includono:

- Se si usa il controllo della versione di Git, è possibile archiviare i segreti di runtime in un file locale e impedire l'archiviazione del file aggiungendo un modello in modo che corrisponda al nome del file con estensione [gitignore](https://git-scm.com/docs/gitignore)
- In un flusso di lavoro di automazione è possibile archiviare i segreti in modo sicuro nella configurazione dei parametri offerta da tale tecnologia di automazione. Ad esempio, se si usano le [azioni di GitHub](https://github.com/features/actions), è possibile archiviare i segreti in modo sicuro nei [segreti di GitHub](https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets).

## <a name="branching-and-merging"></a>Creazione di rami e merge

I sistemi di controllo della versione distribuiti come Git offrono flessibilità nel modo in cui i membri del team pubblicano, condividono, esaminano ed eseguono l'iterazione delle modifiche del codice tramite rami di sviluppo condivisi con altri. Adottare una [strategia di diramazione git](/azure/devops/repos/git/git-branching-guidance) appropriata per il team.

Indipendentemente dalla strategia di diramazione adottata, un principio chiave di tutti questi elementi è che i membri del team possono lavorare sulla soluzione in un *Branch di funzionalità* indipendentemente dal lavoro in corso in altri rami.

Per supportare l'uso indipendente dei rami con un progetto LUIS:

- **Il ramo master ha una propria app LUIS.** Questa app rappresenta lo stato corrente della soluzione per il progetto e la versione attiva corrente deve sempre essere mappata all' `.lu` origine che si trova nel ramo master. Tutti gli aggiornamenti all' `.lu` origine per questa app devono essere esaminati e testati in modo che l'app possa essere distribuita in ambienti di compilazione come la produzione in qualsiasi momento. Quando gli aggiornamenti per `.lu` vengono uniti in un master da un branch di funzionalità, è necessario creare una nuova versione nell'app Luis e [urtare il numero di versione](#versioning).

- **Ogni ramo della funzionalità deve usare la propria istanza di un'app Luis**. Gli sviluppatori lavorano con questa app in un branch di funzionalità senza rischiare di influire sugli sviluppatori che lavorano in altri rami. Questa app dev Branch è una copia di lavoro che deve essere eliminata quando viene eliminato il ramo della funzionalità.

![Ramo funzionalità git](./media/luis-concept-devops-sourcecontrol/feature-branch.png)

### <a name="developers-can-work-from-independent-branches"></a>Gli sviluppatori possono lavorare da rami indipendenti

Gli sviluppatori possono lavorare sugli aggiornamenti in un'app LUIS indipendentemente da altri rami:

1. Creazione di un branch di funzionalità dal ramo principale (a seconda della strategia di Branch, in genere master o develop).

1. [Creare una nuova app Luis nel portale Luis](./luis-how-to-start-new-app.md) ("*dev Branch app*") esclusivamente per supportare il lavoro nel ramo Feature.

   * Se l' `.lu` origine per la soluzione esiste già nel Branch, perché è stata salvata dopo il lavoro eseguito in un altro ramo precedente nel progetto, creare l'app Luis branch Dev importando il `.lu` file.

   * Se si inizia a lavorare in un nuovo progetto, non è ancora presente l' `.lu` origine per l'app Master Luis nel repository. Il file verrà creato `.lu` esportando l'app dev Branch dal portale dopo aver completato il lavoro del ramo funzionalità e averlo inviato come parte della richiesta pull.

1. Lavorare sulla versione attiva dell'app dev Branch per implementare le modifiche necessarie. È consigliabile lavorare solo in una singola versione dell'app dev Branch per tutte le attività di Branch di funzionalità. Se si crea più di una versione nell'app dev Branch, prestare attenzione a tenere traccia della versione che contiene le modifiche da archiviare quando si genera la richiesta pull.

1. Testare gli aggiornamenti: vedere [test per Luis DevOps](luis-concept-devops-testing.md) per informazioni dettagliate sul test dell'app dev Branch.

1. Esportare la versione attiva dell'app dev Branch come `.lu` dall' [elenco versioni](./luis-how-to-manage-versions.md).

1. Archiviare gli aggiornamenti e invitare la revisione peer degli aggiornamenti. Se si usa GitHub, verrà generata una [richiesta pull](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests).

1. Quando le modifiche vengono approvate, unire gli aggiornamenti al ramo master. A questo punto, si creerà una nuova [versione](./luis-how-to-manage-versions.md) dell'app *Master* Luis, usando il aggiornato `.lu` nel master. Per considerazioni sull'impostazione del nome della versione, vedere [controllo delle versioni](#versioning) .

1. Quando viene eliminato il ramo funzionalità, è consigliabile eliminare l'app LUIS Branch di sviluppo creata per il funzionamento del ramo della funzionalità.

### <a name="developers-can-work-in-multiple-branches-concurrently"></a>Gli sviluppatori possono lavorare simultaneamente in più rami

Se si segue il modello descritto in precedenza in [gli sviluppatori possono lavorare da rami indipendenti](#developers-can-work-from-independent-branches), si userà un'applicazione Luis univoca in ogni ramo della funzionalità. Un singolo sviluppatore può lavorare su più rami contemporaneamente, a condizione che passino all'app LUIS branch Dev corretta per il ramo su cui stanno attualmente lavorando.

Si consiglia di usare lo stesso nome sia per il ramo funzionalità che per l'app LUIS Branch di sviluppo creata per il funzionamento del ramo funzionalità, per rendere meno probabile che si lavori accidentalmente sull'app non corretta.

Come indicato in precedenza, per semplicità è possibile usare una singola versione in ogni app dev Branch. Se si usano più versioni, prestare attenzione ad attivare la versione corretta quando si passa da app Branch di sviluppo a un'altra.

### <a name="multiple-developers-can-work-on-the-same-branch-concurrently"></a>Più sviluppatori possono lavorare contemporaneamente sullo stesso ramo

È possibile supportare più sviluppatori che lavorano nello stesso ramo di funzionalità allo stesso tempo:

- Gli sviluppatori estraggono lo stesso ramo della funzionalità e le modifiche push e pull inviate da se stessi e da altri sviluppatori mentre il lavoro continua, come di consueto.

- Se si segue il modello descritto in precedenza in [sviluppatori che possono lavorare da rami indipendenti](#developers-can-work-from-independent-branches), questo ramo userà un'applicazione Luis univoca per supportare lo sviluppo. L'app LUIS ' dev Branch ' verrà creata dal primo membro del team di sviluppo che inizia a lavorare nel ramo Feature.

- [Aggiungere i membri del team come collaboratori](./luis-how-to-collaborate.md) per l'app Luis branch Dev.

- Al termine del ramo della funzionalità, esportare la versione attiva dell'app dev Branch LUIS come `.lu` dall' [elenco versioni](./luis-how-to-manage-versions.md), salvare il `.lu` file aggiornato nel repository e archiviare le modifiche ed eseguire il pull.

### <a name="incorporating-changes-from-one-branch-to-another-with-rebase-or-merge"></a>Incorporamento delle modifiche da un ramo a un altro con riassegnazione o Unione

Alcuni altri sviluppatori del team che lavorano in un altro ramo possono avere effettuato aggiornamenti all' `.lu` origine e Uniti al ramo master dopo aver creato il ramo della funzionalità. È possibile incorporare le modifiche nella versione di lavoro prima di continuare a apportare le proprie modifiche all'interno del ramo della funzionalità. Questa operazione può essere eseguita riassegnando o eseguendo il [merge al Master](https://git-scm.com/book/en/v2/Git-Branching-Rebasing) nello stesso modo di qualsiasi altro asset di codice. Poiché l'app LUIS in formato LUDown è leggibile, supporta l'Unione usando gli strumenti di merge standard.

Se si sta ribasando l'app LUIS in un ramo funzionalità, seguire questi suggerimenti:

- Prima di riassegnare o unire, verificare che la copia locale dell' `.lu` origine per l'app includa tutte le modifiche più recenti applicate tramite il portale Luis, riesportando prima l'app dal portale. In questo modo, è possibile assicurarsi che tutte le modifiche apportate nel portale e non ancora esportate non vadano perse.

- Durante l'Unione, utilizzare gli strumenti standard per risolvere eventuali conflitti di Unione.

- Non dimenticare dopo il completamento della riassegnazione o del merge per reimportare nuovamente l'app nel portale, in modo da usare l'app aggiornata mentre si continua ad applicare le proprie modifiche.

### <a name="merge-prs"></a>Unisci a pull

Una volta approvata la richiesta pull, è possibile unire le modifiche al ramo master. Nessuna considerazione speciale è valida per l'origine LUDown per un'app LUIS: è leggibile e pertanto supporta l'Unione mediante gli strumenti di merge standard. Eventuali conflitti di merge possono essere risolti nello stesso modo di altri file di origine.

Dopo che la richiesta pull è stata unita, è consigliabile eseguire la pulizia:

- Eliminare il ramo nel repository

- Eliminare l'app LUIS "dev Branch" creata per il funzionamento del ramo funzionalità.

Allo stesso modo degli asset del codice dell'applicazione, è necessario scrivere unit test per accompagnare gli aggiornamenti delle app LUIS. Per eseguire il test, è necessario usare flussi di lavoro di integrazione continua:

- Aggiornamenti in una richiesta pull prima del merge della richiesta pull
- L'app LUIS branch master dopo una richiesta pull è stata approvata e le modifiche sono state unite nel database master.

Per altre informazioni sui test per LUIS DevOps, vedere [test di DevOps per Luis](luis-concept-devops-testing.md). Per altri dettagli sull'implementazione dei flussi di lavoro, vedere [flussi di lavoro di automazione per Luis DevOps](luis-concept-devops-automation.md).

## <a name="code-reviews"></a>Revisioni del codice

Un'app LUIS in formato LUDown è leggibile, che supporta la comunicazione delle modifiche in una richiesta pull adatta per la revisione. Anche i file di unit test vengono scritti in formato LUDown e possono essere facilmente rivisualizzabili in una richiesta pull.

## <a name="versioning"></a>Controllo delle versioni

Un'applicazione è costituita da più componenti che possono includere elementi come un bot in esecuzione nel [servizio Azure bot](/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0), [QnA Maker](https://www.qnamaker.ai/), [servizio di riconoscimento vocale di Azure](../speech-service/overview.md)e altro ancora. Per raggiungere l'obiettivo delle applicazioni a regime di controllo libero, usare il [controllo della versione](/azure/devops/learn/git/what-is-version-control) in modo che ogni componente di un'applicazione venga sottoporre a controllo delle versioni in modo indipendente, consentendo agli sviluppatori di rilevare modifiche o aggiornamenti di rilievo semplicemente esaminando il numero di versione. È più facile eseguire la versione dell'app LUIS indipendentemente da altri componenti se la si mantiene nel proprio repository.

Per l'app LUIS per il ramo master deve essere applicato uno schema di controllo delle versioni. Quando si uniscono gli aggiornamenti al `.lu` per un'app Luis nel database master, si importerà quindi l'origine aggiornata in una nuova versione nell'app Luis per il ramo master.

Si consiglia di usare uno schema di controllo delle versioni numerico per la versione dell'app Master LUIS, ad esempio:

`major.minor[.build[.revision]]`

Ogni aggiornamento il numero di versione viene incrementato in corrispondenza dell'ultima cifra.

La versione principale/secondaria può essere usata per indicare l'ambito delle modifiche alla funzionalità dell'app LUIS:

* Versione principale: modifica significativa, ad esempio il supporto per una nuova [finalità](./luis-concept-intent.md) o [entità](./luis-concept-entity-types.md)
* Versione secondaria: modifica secondaria compatibile con le versioni precedenti, ad esempio dopo un nuovo training significativo
* Compilazione: nessuna modifica delle funzionalità, ma solo una compilazione diversa.

Dopo aver determinato il numero di versione per la revisione più recente dell'app Master LUIS, è necessario compilare e testare la nuova versione dell'app e pubblicarla in un endpoint in cui può essere usata in diversi ambienti di compilazione, ad esempio la garanzia di qualità o la produzione. È consigliabile automatizzare tutti questi passaggi in un flusso di lavoro di integrazione continua (CI).

Vedere:
- [Flussi di lavoro di automazione](luis-concept-devops-automation.md) per informazioni dettagliate su come implementare un flusso di lavoro ci per testare e rilasciare un'app Luis.
- Per informazioni su come distribuire l'app LUIS, [Release Management](luis-concept-devops-automation.md#release-management) .

### <a name="versioning-the-feature-branch-luis-app"></a>Controllo delle versioni dell'app LUIS ' feature Branch '

Quando si usa un'app LUIS "dev Branch" creata per supportare il lavoro in un branch di funzionalità, si esporterà l'app al termine dell'operazione e si includerà l'aggiornamento nella richiesta pull `'lu` . Il ramo nel repository e l'app LUIS "dev Branch" devono essere eliminati dopo che la richiesta pull è stata unita nel database master. Poiché questa app esiste esclusivamente per supportare il lavoro nel ramo funzionalità, non è necessario applicare uno schema di controllo delle versioni specifico all'interno dell'app.

Quando le modifiche apportate alla richiesta pull vengono unite nel database master, ovvero quando è necessario applicare il controllo delle versioni, in modo che tutti gli aggiornamenti al database master vengano sottoposti a controllo delle versioni in modo indipendente.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [test per Luis DevOps](luis-concept-devops-testing.md)
* Informazioni su come [implementare DevOps per Luis con GitHub](luis-how-to-devops-with-github.md)