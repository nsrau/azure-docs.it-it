---
title: Come testare una knowledge base - QnA Maker
description: Testare la Knowledge Base di QnA Maker è una parte importante di un processo iterativo per migliorare l'accuratezza delle risposte restituite. È possibile testare la Knowledge Base attraverso un'interfaccia di chat avanzata che consente anche di apportare modifiche.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 273548ec095ce04772438a2d732b914d80d976cc
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353154"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Testare la Knowledge base in QnA Maker

Testare la Knowledge Base di QnA Maker è una parte importante di un processo iterativo per migliorare l'accuratezza delle risposte restituite. È possibile testare la Knowledge Base attraverso un'interfaccia di chat avanzata che consente anche di apportare modifiche.

## <a name="interactively-test-in-qna-maker-portal"></a>Eseguire test in modo interattivo nel portale QnA Maker

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/v1)

1. Accedere alla Knowledge Base selezionandone il nome nella pagina **My knowledge bases** (Knowledge Base personali).
1. Per accedere al pannello a scorrimento Test, selezionare **Test** nel pannello superiore dell'applicazione.
1. Immettere una query nella casella di testo e premere Invio.
1. Viene fornita la risposta della Knowledge Base con la migliore corrispondenza.

### <a name="clear-test-panel"></a>Cancellare il pannello Test

Per cancellare tutte le query di test inserite e i relativi risultati dalla console di test, selezionare **Ricomincia** nell'angolo in alto a sinistra del pannello Test.

### <a name="close-test-panel"></a>Chiudere il pannello Test

Per chiudere il pannello Test, selezionare nuovamente il pulsante **Test**. Quando il pannello Test è aperto, non è possibile modificare i contenuti della Knowledge Base.

### <a name="inspect-score"></a>Ispezionare il punteggio

È possibile ispezionare i dettagli dei risultati del test nel pannello Inspect (Ispeziona).

1.  Con il pannello a scorrimento Test aperto, selezionare **Inspect** (Ispeziona) per visualizzare altri dettagli sulla risposta.

    ![Ispezionare le risposte](../media/qnamaker-how-to-test-knowledge-bases/inspect.png)

2.  Verrà visualizzato il pannello Inspection (Ispeziona). Il pannello include la finalità con il punteggio più alto ed eventuali entità identificate. Il pannello mostra il risultato dell'espressione selezionata.

### <a name="correct-the-top-scoring-answer"></a>Correggere la risposta con il punteggio più alto

Se la risposta con il punteggio più alto non è corretta, selezionare la risposta corretta dall'elenco e selezionare **Save and Train** (Salva ed esegui training).

![Correggere la risposta con il punteggio più alto](../media/qnamaker-how-to-test-knowledge-bases/choose-answer.png)

### <a name="add-alternate-questions"></a>Aggiungere domande alternative

È possibile aggiungere forme alternative di una domanda a una determinata risposta. Digitare le risposte alternative nella casella di testo e premere INVIO per aggiungerle. Selezionare **Save and Train** (Salva ed esegui training) per archiviare gli aggiornamenti.

![Aggiungere domande alternative](../media/qnamaker-how-to-test-knowledge-bases/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Aggiungere una nuova risposta

È possibile aggiungere una nuova risposta se una delle risposte esistenti individuate non è corretta o se la risposta non esiste nella Knowledge Base (non è stata trovata una buona corrispondenza nella Knowledge Base).

Nella parte inferiore dell'elenco risposte utilizzare la casella di testo per immettere una nuova risposta e premere INVIO per aggiungerla.

Selezionare **Save and train** (Salva ed esegui training) per salvare questa risposta in modo permanente. Una nuova coppia di domanda/risposta è stata così aggiunta alla Knowledge Base.

> [!NOTE]
> Tutte le modifiche alla Knowledge Base vengono salvate solo quando si fa clic sul pulsante **Save and train** (Salva ed esegui training).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/v2)

1. Accedere alla Knowledge Base selezionandone il nome nella pagina **My knowledge bases** (Knowledge Base personali).
1. Per accedere al pannello a scorrimento Test, selezionare **Test** nel pannello superiore dell'applicazione. 
1. Verrà visualizzata una casella di controllo nella parte superiore della **risposta breve visualizzata**, selezionata per impostazione predefinita. Questa opzione viene usata per abilitare il rilevamento dell'intervallo di risposte basato su MRC nel pannello di test. 
1. Immettere una query nella casella di testo e premere Invio. 
1. Per ogni query, se è presente una risposta precisa/risposta breve nel passaggio di risposta, insieme al passaggio di risposta con la corrispondenza più appropriata, presente nella Knowledge base, sarà disponibile anche una breve risposta per la query.
    ![Riquadro del test abilitato per la modalità gestita](../media/qnamaker-how-to-test-knowledge-bases/test-pane-with-managed-detail.png)
1. Se si deseleziona la **visualizzazione della risposta breve**, viene restituito come risposta solo il passaggio della risposta con la corrispondenza più appropriata della Knowledge base.

### <a name="clear-test-panel"></a>Cancellare il pannello Test

Per cancellare tutte le query di test inserite e i relativi risultati dalla console di test, selezionare **Ricomincia** nell'angolo in alto a sinistra del pannello Test.

### <a name="close-test-panel"></a>Chiudere il pannello Test

Per chiudere il pannello Test, selezionare nuovamente il pulsante **Test**. Quando il pannello Test è aperto, non è possibile modificare i contenuti della Knowledge Base.

### <a name="inspect-score"></a>Ispezionare il punteggio

È possibile ispezionare i dettagli dei risultati del test nel pannello Inspect (Ispeziona).

1.  Con il pannello a scorrimento Test aperto, selezionare **Inspect** (Ispeziona) per visualizzare altri dettagli sulla risposta.

    ![Controllare l'anteprima delle risposte](../media/qnamaker-how-to-test-knowledge-bases/inspect-with-managed.png)

2.  Verrà visualizzato il pannello Inspection (Ispeziona). Il pannello include la finalità con il punteggio più alto ed eventuali entità identificate. Il pannello mostra il risultato dell'espressione selezionata.
3. Il pannello mostra il Punteggio di confidenza del passaggio di risposta insieme al Punteggio di intervallo di risposta rilevato.

### <a name="correct-the-top-scoring-answer"></a>Correggere la risposta con il punteggio più alto

Se la risposta con il punteggio più alto non è corretta, selezionare la risposta corretta dall'elenco e selezionare **Save and Train** (Salva ed esegui training).

![Correggere l'anteprima della risposta con punteggio superiore](../media/qnamaker-how-to-test-knowledge-bases/choose-answer-managed.png)

### <a name="add-alternate-questions"></a>Aggiungere domande alternative

È possibile aggiungere forme alternative di una domanda a una determinata risposta. Digitare le risposte alternative nella casella di testo e premere INVIO per aggiungerle. Selezionare **Save and Train** (Salva ed esegui training) per archiviare gli aggiornamenti.

![Aggiungi anteprima domande alternative](../media/qnamaker-how-to-test-knowledge-bases/add-alternate-question-with-managed.png)

### <a name="add-a-new-answer"></a>Aggiungere una nuova risposta

È possibile aggiungere una nuova risposta se una delle risposte esistenti individuate non è corretta o se la risposta non esiste nella Knowledge Base (non è stata trovata una buona corrispondenza nella Knowledge Base).

Nella parte inferiore dell'elenco risposte utilizzare la casella di testo per immettere una nuova risposta e premere INVIO per aggiungerla.

Selezionare **Save and train** (Salva ed esegui training) per salvare questa risposta in modo permanente. Una nuova coppia di domanda/risposta è stata così aggiunta alla Knowledge Base.

---

### <a name="test-the-published-knowledge-base"></a>Testare la Knowledge base pubblicata

È possibile testare la versione pubblicata della Knowledge base nel riquadro test. Dopo aver pubblicato la Knowledge base, selezionare la casella **KB pubblicati** e inviare una query per ottenere risultati dalla KB pubblicata.

![Eseguire test su una KB pubblicata](../media/qnamaker-how-to-test-knowledge-bases/test-against-published-knowledge-base.png)

## <a name="batch-test-with-tool"></a>Test batch con strumento

Utilizzare lo strumento di test batch quando si desidera:

* determinare la risposta e il Punteggio principali per un set di domande
* convalidare la risposta prevista per il set di domande

### <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services/)
* [Creare un servizio di QnA Maker](../Quickstarts/create-publish-knowledge-base.md) o usarne uno esistente in lingua inglese.
* Scaricare il file di [esempio multiturno`.docx` ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)
* Scaricare lo [strumento di test batch](https://aka.ms/qnamakerbatchtestingtool) ed estrarre il file eseguibile dal file `.zip`.

### <a name="sign-into-qna-maker-portal"></a>Accedere al portale di QnA Maker

[Accedere](https://www.qnamaker.ai/) al portale di QnA Maker.

### <a name="create-a-new-knowledge-base-from-the-multi-turn-sampledocx-file"></a>Creare una nuova knowledge base dal file sample.docx multiturno

1. Selezionare **Create a knowledge base** (Crea una knowledge base) sulla barra degli strumenti.
1. Ignorare il **passaggio 1**, perché si dovrebbe avere già una risorsa QnA Maker, e procedere con il **passaggio 2** per selezionare le informazioni sulla risorsa esistente:
    * ID di Azure Active Directory
    * Nome della sottoscrizione di Azure
    * Nome del servizio QnA di Azure
    * Lingua: la lingua inglese
1. Immettere `Multi-turn batch test quickstart` come nome della knowledge base.

1. Nel **Passaggio 4** configurare le impostazioni in base alla tabella seguente:

    |Impostazione|valore|
    |--|--|
    |**Enable multi-turn extraction from URLs, .pdf or .docx files** (Abilita estrazione a più turni da URL e file PDF o DOCX)|Selezionato|
    |**Default answer text** (Testo della risposta predefinita)| `Batch test - default answer not found.`|
    |**+ Add File** (+ Aggiungi file)|Selezionare il file `.docx` scaricato come da prerequisiti.|
    |**Chit-chat**|Selezionare **Professional**|

1. Nel **passaggio 5**, selezionare **Create your KB** (Crea la KB).

    Al termine del processo di creazione, nel portale viene visualizzata la knowledge base modificabile.

### <a name="save-train-and-publish-knowledge-base"></a>Salvare, eseguire il training e pubblicare la knowledge base.

1. Selezionare **Save and Train** (Salva ed esegui il training) sulla barra degli strumenti per salvare la knowledge base.
1. Selezionare **Publish** (Pubblica) sulla barra degli strumenti e poi di nuovo **Publish** per pubblicare la knowledge base. La pubblicazione rende disponibile la knowledge base per le query da un endpoint URL pubblico. Al termine della pubblicazione, salvare le informazioni relative all'URL dell'host e alla chiave dell'endpoint visualizzate nella pagina **Publish** (Pubblica).

    |Dati obbligatori| Esempio|
    |--|--|
    |Host pubblicato|`https://YOUR-RESOURCE-NAME.azurewebsites.net`|
    |Chiave pubblicata|`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX` (stringa di caratteri 32 visualizzata dopo `Endpoint`)|
    |ID app|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (stringa di caratteri 36 visualizzata come parte di `POST`) |

### <a name="create-batch-test-file-with-question-ids"></a>Creare un file di test batch con gli ID domanda

Per usare lo strumento di test batch, creare un file denominato `batch-test-data-1.tsv` con un editor di testo. Il file deve essere nel formato UTF-8 e deve contenere le colonne seguenti separate da una tabulazione.

|Campi del file di input TSV|Note|Esempio|
|--|--|--|
|ID della knowledge Base|L'ID della knowledge base disponibile nella pagina di pubblicazione. Testare più knowledge base contemporaneamente nello stesso servizio usando ID knowledge base diversi in un singolo file.|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (stringa di caratteri 36 visualizzata come parte di `POST`) |
|Domanda|Il testo della domanda che verrebbe immesso da un utente. Massimo 1.000 caratteri.|`How do I sign out?`|
|Tag dei metadati|facoltativo|`topic:power` Usa il `key:value` formato|
|Parametro Top|facoltativo|`25`|
|ID della risposta prevista|facoltativo|`13`|

Per questa Knowledge base, aggiungere al file tre righe delle sole due colonne obbligatorie. La prima colonna è l'ID della knowledge base e la seconda deve contenere l'elenco di domande seguente:

|Colonna 2 - domande|
|--|
|`Use Windows Hello to sign in`|
|`Charge your Surface Pro 4`|
|`Get to know Windows 10`|

Queste domande rappresentano l'esatta formulazione della knowledge base e devono restituire 100 come punteggio di attendibilità.

Successivamente, aggiungere alcune domande, simili a queste domande ma non esattamente le stesse su altre tre righe, usando lo stesso ID Knowledge Base:

|Colonna 2 - domande|
|--|
|`What is Windows Hello?`|
|`How do I charge the laptop?`|
|`What features are in Windows 10?`|

> [!CAUTION]
> Verificare che ogni colonna sia delimitata solo da una tabulazione. Gli spazi iniziali o finali vengono aggiunti ai dati della colonna e il programma genera eccezioni se il tipo o le dimensioni non sono corrette.

Il file di test batch, quando viene aperto in Excel, ha un aspetto simile all'immagine seguente. L'ID della knowledge base è stato sostituito con `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` per motivi di sicurezza. Per il test batch, assicurarsi che nella colonna venga visualizzato l'ID della knowledge base.

> [!div class="mx-imgBorder"]
> ![Input della prima versione del file con estensione tvs dal test batch](../media/batch-test/batch-test-1-input.png)

### <a name="test-the-batch-file"></a>Testare il file batch

Eseguire il programma di test batch usando il seguente formato CLI dalla riga di comando.

Sostituire `YOUR-RESOURCE-NAME` e `ENDPOINT-KEY` con i propri valori di nome servizio e chiave dell'endpoint. Questi valori sono disponibili nella pagina **Settings** (Impostazioni) nel portale di QnA Maker.

```console
batchtesting.exe batch-test-data-1.tsv https://YOUR-RESOURCE-NAME.azurewebsites.net ENDPOINT-KEY out.tsv
```
Il test viene completato e genera il file `out.tsv`:

> [!div class="mx-imgBorder"]
> ![Output della prima versione del file con estensione tvs dal test batch](../media/batch-test/batch-test-1-output.png)

L'ID della knowledge base è stato sostituito con `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` per motivi di sicurezza. Per il test batch, nella colonna viene visualizzato l'ID della propria knowledge base.

L'output del test del Punteggio di confidenza, nella quarta colonna, Mostra le prime tre domande che hanno restituito un punteggio di 100 come previsto perché ogni domanda è esattamente identica a quella visualizzata nella Knowledge base. Le ultime tre domande, con la nuova formulazione della domanda, non restituiscono 100 come Punteggio di confidenza. Per aumentare il punteggio per il test e per gli utenti, è necessario aggiungere più domande alternative alla knowledge base.

### <a name="testing-with-the-optional-fields"></a>Test con i campi facoltativi

Una volta capiti il formato e il processo, è possibile generare un file di test da eseguire sulla knowledge base da un'origine dati come i log delle chat.

Poiché l'origine dati e il processo sono automatizzati, il file di test può essere eseguito molte volte con impostazioni diverse per determinare i valori corretti.

Nel caso ad esempio del log di una chat per cui si vuole determinare quale testo si applica a specifici campi di metadati, creare un file di test e impostare i campi dei metadati per ogni riga. Eseguire il test, quindi esaminare le righe che corrispondono ai metadati. In genere, le corrispondenze dovrebbero essere positive, ma è necessario esaminare i risultati per rilevare eventuali falsi positivi. Un falso positivo è una riga che corrisponde ai metadati ma che, in base al testo, non dovrebbe corrispondere.

### <a name="using-optional-fields-in-the-input-batch-test-file"></a>Uso dei campi facoltativi nel file di test batch di input

Usare il grafico seguente per informazioni su come trovare i valori dei campi per i dati facoltativi.

|Numero di colonna|Colonna facoltativa|Posizione dei dati|
|--|--|--|
|3|metadata|Esporta la Knowledge Base esistente per le `key:value` coppie esistenti.|
|4|top|È consigliabile usare il valore predefinito `25`.|
|5|ID del set di domane e risposte|Esporta la knowledge base esistente per i valori di ID. Si noti inoltre che gli ID sono stati restituiti nel file di output.|

### <a name="add-metadata-to-the-knowledge-base"></a>Aggiungere metadati alla knowledge base

1. Nella pagina **Edit** (Modifica) del portale di domande e risposte aggiungere i metadati `topic:power` alle domande seguenti:

    |Domande|
    |--|
    |Ricaricare il Surface Pro 4|
    |Controllare il livello della batteria|

    Per due coppie di domande e risposte sono impostati metadati.

    > [!TIP]
    > Per visualizzare i metadati e gli ID di domanda e risposta di ogni set, esportare la knowledge base. Selezionare la pagina **Settings** (Impostazioni), quindi selezionare **Export** (Esporta) come file `.xls`. Trovare il file scaricato e aprirlo con in Excel per esaminare i metadati e l'ID.

1. Selezionare **Save and train** (Salva ed esegui il training), quindi la pagina **Publish** (Pubblica) e infine il pulsante **Publish**. Queste azioni rendono disponibile la modifica per il test batch. Scaricare la knowledge base dalla pagina **Settings** (Impostazioni).

    Il file scaricato ha il formato corretto per i metadati e l'ID del set di domande e risposte corretto. Usare questi campi nella sezione successiva

    > [!div class="mx-imgBorder"]
    > ![Knowledge base esportata con metadati](../media/batch-test/exported-knowledge-base-with-metadata.png)

### <a name="create-a-second-batch-test"></a>Creare un secondo test batch

Esistono due scenari principali per i test batch:
* **Elaborare i file di log della chat**: determinare la risposta principale per una domanda in precedenza non vista; la situazione più comune è il caso in cui sia necessario elaborare un file di log di query, ad esempio le domande dell'utente di un chatbot. Creare un test di file batch solo con le colonne necessarie. Il test restituisce la risposta principale per ogni domanda. Questo non significa che tale risposta sia quella corretta. Una volta completato questo test, passare al test di convalida.
* **Test di convalida**: convalidare la risposta prevista. Per questo test è necessario che siano state convalidate tutte le domande e le risposte previste corrispondenti nel test batch. Questa operazione potrebbe richiedere un processo manuale.

La procedura seguente presuppone che lo scenario consista nell'elaborare i log della chat

1. Creare un nuovo file di test batch per includere i dati facoltativi `batch-test-data-2.tsv`. Aggiungere le sei righe dal file di input del test batch originale, quindi aggiungere i metadati, la parte superiore e l'ID della coppia QnA per ogni riga.

    Per simulare il processo automatizzato di verifica della presenza di nuovo testo nei log della chat rispetto alla knowledge base, impostare i metadati per ogni colonna sullo stesso valore: `topic:power`.

    > [!div class="mx-imgBorder"]
    > ![Input della seconda versione del file con estensione tvs dal test batch](../media/batch-test/batch-test-2-input.png)

1. Eseguire di nuovo il test, cambiando i nomi dei file di input e output per indicare che si tratta del secondo test.

    > [!div class="mx-imgBorder"]
    > ![Output della seconda versione del file con estensione tvs dal test batch](../media/batch-test/batch-test-2-output.png)

### <a name="test-results-and-an-automated-test-system"></a>Risultati dei test e sistema di test automatizzato

Questo file di output del test può essere analizzato nell'ambito di una pipeline di test continua automatizzata.

L'output specifico di questo test viene interpretato in questo modo: è stata filtrata ogni riga con metadati e per ogni riga che non corrisponde ai metadati della knowledge base viene restituita la risposta predefinita (nessuna corrispondenza trovata nella KB). Delle righe che corrispondono, è stato restituito l'ID QnA e il punteggio.

Tutte le righe restituiscono l'etichetta di valore non corretto perché nessuna riga corrisponde all'ID risposta previsto.

Questi risultati indicano che è possibile usare il testo del log di una chat come query di ogni riga. Senza sapere niente sui dati, i risultati includono molte informazioni che è possibile usare in futuro:

* Metadati
* ID di domande e risposte
* score

L'applicazione di filtri con i metadati si è rivelata una scelta valida per il test? Sì e no. Il sistema di test dovrebbe creare file di test per ogni coppia di metadati, oltre a un test senza coppie di metadati.

### <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a testare la knowledge base, eliminare lo strumento di file batch e i file di test.

Se non si intende continuare a usare questa knowledge base, eliminarla seguendo questa procedura:

1. Nel portale di QnA Maker scegliere **My Knowledge bases** (Knowledge base personali) dal menu in alto.
1. Nell'elenco di knowledge base selezionare l'icona **Elimina** nella riga che corrisponde alla knowledge base usata in questo argomento di avvio rapido.

[La documentazione di riferimento sullo strumento](../reference-tsv-format-batch-testing.md) include:

* esempio di riga di comando dello strumento
* formato per i file di input e di file con estensione TSV

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Pubblicare una knowledge base](../quickstarts/create-publish-knowledge-base.md)