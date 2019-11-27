---
title: Progettazione di app iterative-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS apprende meglio in un ciclo iterativo di modifiche ai modelli, esempi di espressioni, pubblicazione e raccolta di dati da query endpoint.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: c1c1b2df301634a435b610c395a1a58aa5573da3
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422598"
---
# <a name="iterative-app-design-for-luis"></a>Progettazione di app iterative per LUIS

Un'app Language Understanding (LUIS) apprende ed esegue in modo più efficiente l'iterazione. Di seguito è riportato un tipico ciclo di iterazione:

* Crea nuova versione
* Modificare lo schema dell'app LUIS. Sono inclusi:
    * Intent con espressioni di esempio
    * Entità
    * Funzionalità
* Esegui il training, il test e la pubblicazione
    * Eseguire il test nell'endpoint di stima per l'apprendimento attivo
* raccolta dati da query endpoint

![Ciclo di creazione](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>Creazione di uno schema LUIS

Lo schema di un'app definisce gli elementi richiesti dall'utente ( _intenzione_ o _finalità_ ) e le parti dello scopo che forniscono i dettagli (detti _entità_) usati per determinare la risposta. 

Lo schema dell'app deve essere specifico per i domini dell'app per determinare parole e frasi rilevanti, nonché per determinare l'ordinamento di parole tipico. 

Le espressioni di esempio rappresentano gli input utente, ad esempio il riconoscimento vocale o il testo riconosciuto, previsto dall'app in fase di esecuzione. 

Lo schema richiede Intent e _deve avere_ entità. 

### <a name="example-schema-of-intents"></a>Schema di esempio di Intent

Lo schema più comune è uno schema preventivo organizzato con finalità. Questo tipo di schema utilizza LUIS per determinare l'intenzione di un utente. 

Il tipo di schema Intent può avere entità se aiuta LUIS a determinare l'intenzione dell'utente. Ad esempio, un'entità shipping, come descrittore a scopo, aiuta LUIS a determinare un'intenzione di spedizione. 

### <a name="example-schema-of-entities"></a>Schema di esempio delle entità

Uno schema di entità è incentrato sulle entità, ovvero i dati estratti da espressioni utente. Se, ad esempio, un utente diceva "Vorrei ordinare tre pizze". Sono presenti due entità che verrebbero estratte: _tre_ e _pizze_. Queste informazioni vengono usate per soddisfare l'intenzione, ovvero per eseguire un ordine. 

Per uno schema di entità, l'intenzione dell'espressione è meno importante per l'applicazione client. 

Un metodo comune per organizzare uno schema di entità consiste nell'aggiungere tutte le espressioni di esempio alla finalità **None** . 

### <a name="example-of-a-mixed-schema"></a>Esempio di schema misto

Lo schema più potente e maturo è uno schema preventivo con una gamma completa di entità e funzionalità. Questo schema può iniziare come uno schema di entità o Intent e crescere per includere i concetti di entrambi, perché l'applicazione client necessita di tali informazioni. 

## <a name="add-example-utterances-to-intents"></a>Aggiungere espressioni di esempio ad Intent

LUIS necessita di alcune espressioni di esempio in ogni **finalità**. Le espressioni di esempio richiedono una variazione sufficiente di scelta di parole e l'ordine delle parole per poter determinare a quale finalità è destinata l'espressione. 

> [!CAUTION]
> Non aggiungere espressioni di esempio in blocco. Iniziare da 15 a 30 esempi specifici e variabili. 

Ogni espressione di esempio deve avere **dati necessari per estrarre i dati** progettati ed etichettati con le **entità**. 

|Elemento Key|Scopo|
|--|--|
|Finalità|**Classificare** le espressioni utente in un'unica intenzione o in un'azione. Gli esempi includono `BookFlight` e `GetWeather`.|
|Entità|**Estrarre** i dati da un enunciato necessario per completare l'intenzione. Gli esempi includono la data e l'ora di viaggio e località.|

Un'app LUIS può essere progettata per ignorare le espressioni che non sono rilevanti per il dominio di un'app assegnando l'espressione a **Nessuna** finalità.

## <a name="test-and-train-your-app"></a>Eseguire test e training dell'app

Dopo avere da 15 a 30 espressioni di esempio diverse per ogni finalità, con le entità obbligatorie etichettate, è necessario eseguire il test e il [Training](luis-how-to-train.md) dell'app Luis. 

## <a name="publish-to-a-prediction-endpoint"></a>Pubblicare in un endpoint di stima

L'app LUIS deve essere pubblicata in modo che sia disponibile nelle aree dell'endpoint di [stima](luis-reference-regions.md)degli elenchi.

## <a name="test-your-published-app"></a>Testare l'app pubblicata

È possibile testare l'app LUIS pubblicata dall'endpoint di stima HTTPS. Il test dall'endpoint di stima consente a LUIS di scegliere eventuali espressioni con una bassa confidenza per la [Revisione](luis-how-to-review-endpoint-utterances.md).  

## <a name="create-a-new-version-for-each-cycle"></a>Crea una nuova versione per ogni ciclo

Ogni versione è uno snapshot nel tempo dell'app LUIS. Prima di apportare modifiche all'app, creare una nuova versione. È più facile tornare a una versione precedente rispetto a provare a rimuovere gli Intent e le espressioni in uno stato precedente.

L'ID versione è composto da caratteri, cifre o '.' e non può superare i 10 caratteri.

La versione iniziale (0.1) è la versione attiva predefinita. 

### <a name="begin-by-cloning-an-existing-version"></a>Per iniziare, clonare una versione esistente

Clonare una versione esistente da utilizzare come punto di partenza per ogni nuova versione. Dopo aver clonato una versione, la nuova versione diventa la versione **attiva** . 

### <a name="publishing-slots"></a>Pubblicazione di slot

È possibile pubblicare negli slot di produzione o di gestione temporanea. Ogni slot può presentare una versione diversa o la stessa versione. Questa operazione è utile per verificare le modifiche prima della pubblicazione in produzione, disponibile per bot o altre app di chiamata LUIS. 

Le versioni con Training non sono disponibili automaticamente nell' [endpoint](luis-glossary.md#endpoint)dell'app Luis. È necessario [pubblicare](luis-how-to-publish-app.md) o ripubblicare una versione affinché sia disponibile nell'endpoint dell'app Luis. È possibile pubblicare in **gestione temporanea** e **produzione**, fornendo due versioni dell'app disponibili nell'endpoint. Se è necessario che in un endpoint siano disponibili più versioni dell'app, è necessario esportare la versione e reimportarla in una nuova app. La nuova app presenta un ID app diverso.

### <a name="import-and-export-a-version"></a>Importare ed esportare una versione

È possibile importare una versione a livello di app. Tale versione diventa la versione attiva e usa l'ID versione nella proprietà `versionId` del file dell'app. È anche possibile importare in un'app esistente a livello di versione. La nuova versione diventa quella attiva. 

Una versione può essere esportata anche a livello di app o di versione. L'unica differenza è che la versione esportata a livello di app è la versione attualmente attiva, mentre a livello di versione è possibile scegliere qualsiasi versione da esportare nella pagina **[Settings](luis-how-to-manage-versions.md)** (Impostazioni). 

Il file esportato **non** contiene:

* Informazioni apprese dal computer, perché l'app viene sottoposta a training dopo l'importazione
* informazioni sul collaboratore

Per eseguire il backup dello schema dell'app LUIS, esportare una versione dal [portale Luis](https://www.luis.ai/applications).

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>Gestire le modifiche ai collaboratori con versioni e collaboratori

LUIS USA il concetto di collaboratori per un'app, fornendo le autorizzazioni a livello di risorsa di Azure. Combinare questo concetto con il controllo delle versioni per fornire la collaborazione di destinazione. 

Usare le tecniche seguenti per gestire le modifiche del collaboratore nell'app.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Gestione di più versioni all'interno della stessa app

Iniziare con la [clonazione](luis-how-to-manage-versions.md#clone-a-version) da una versione di base per ogni autore. 

Ogni autore apporta modifiche alla propria versione dell'app. Quando l'autore è soddisfatto del modello, esportare le nuove versioni in file JSON.  

Le app esportate, i file con estensione JSON o LU possono essere confrontate per le modifiche. Combinare i file per creare un singolo file della nuova versione. Modificare la proprietà `versionId` per indicare la nuova versione unita. Importare la versione nell'app originale. 

Questo metodo consente di disporre di una versione attiva, di una versione di staging e di una versione pubblicata. È possibile confrontare i risultati della versione attiva con una versione pubblicata (fase o produzione) nel [riquadro test interattivo](luis-interactive-test.md).

### <a name="manage-multiple-versions-as-apps"></a>Gestione di più versioni come app

[Esportare](luis-how-to-manage-versions.md#export-version) la versione base. Ogni autore importa la versione. L'utente che importa l'app è il proprietario della versione. Dopo aver modificato l'app, esportare la versione. 

Le app esportate sono file in formato JSON confrontabili con l'esportazione base a livello di modifiche. Combinare i file per creare un singolo file JSON della nuova versione. Modificare la proprietà **versionId** nel file JSON affinché indichi la nuova versione unita. Importare la versione nell'app originale.

Altre informazioni sulla creazione di contributi da [collaboratori](luis-how-to-collaborate.md).

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>Esaminare le espressioni endpoint per iniziare il nuovo ciclo iterativo

Al termine di un ciclo di iterazione, è possibile ripetere il processo. Iniziare a esaminare [gli enunciati dell'endpoint di stima](luis-how-to-review-endpoint-utterances.md) Luis contrassegnati con una bassa confidenza. Controllare questi enunciati sia per la finalità stimata corretta sia per l'entità corretta e completa estratta. Dopo aver esaminato e accettato le modifiche, l'elenco di revisione deve essere vuoto.  

## <a name="next-steps"></a>Passaggi successivi

Concetti di [collaborazione](luis-concept-keys.md).
