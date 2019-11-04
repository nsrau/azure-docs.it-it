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
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 12a1f2304e4255eb9abd04ab2e2d0726066dd1e6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487763"
---
# <a name="authoring-cycles-and-versions"></a>Cicli e versioni di creazione

L'app LUIS apprende meglio in un ciclo iterativo di:

* Crea nuova versione
* Modifica schema app
    * Intent con espressioni di esempio
    * entities
    * elastico
* treno
* test
* Pubblica
    * test in endpoint di stima per l'apprendimento attivo
* raccolta dati da query endpoint

![Ciclo di creazione](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>Creazione di uno schema LUIS

Lo scopo dello schema dell'applicazione consiste nel definire l'elemento richiesto dall'utente (intenzione o finalità) e le parti della domanda che forniscono i dettagli (entità) che consentono di determinare la risposta. 

Lo schema dell'app deve essere specifico per i domini dell'app per determinare le parole e le frasi rilevanti, nonché l'ordinamento di parole tipiche. 

Le espressioni di esempio rappresentano l'input dell'utente che l'app dovrebbe ottenere in fase di esecuzione. 

Lo schema richiede Intent e _deve avere_ entità. 

### <a name="example-schema-of-intents"></a>Schema di esempio di Intent

Lo schema più comune è uno schema preventivo organizzato con finalità. Questo tipo di schema dipende da LUIS per determinare l'intenzione di un utente. 

Questo tipo di schema può avere entità se aiuta LUIS a determinare l'intenzione. Ad esempio, un'entità shipping, come descrittore a scopo, aiuta LUIS a determinare un'intenzione di spedizione. 

### <a name="example-schema-of-entities"></a>Schema di esempio delle entità

Uno schema di entità è incentrato sulle entità, ovvero i dati da estrarre dagli enunciati. 

L'intenzione dell'enunciato è inferiore o non importante per l'applicazione client. 

Un metodo comune per organizzare uno schema di entità consiste nell'aggiungere tutte le espressioni di esempio alla finalità None. 

### <a name="example-of-a-mixed-schema"></a>Esempio di schema misto

Lo schema più potente e maturo è uno schema preventivo con una gamma completa di entità e funzionalità. Questo schema può iniziare come uno schema di entità o Intent e crescere per includere i concetti di entrambi, perché l'applicazione client necessita di tali informazioni. 

## <a name="add-example-utterances-to-intents"></a>Aggiungere espressioni di esempio ad Intent

LUIS necessita di alcune espressioni di esempio in ogni **finalità**. Le espressioni di esempio richiedono una variazione sufficiente di scelta di parole e l'ordine delle parole per poter determinare a quale finalità è destinata l'espressione. 

> [!CAUTION]
> Non aggiungere espressioni di esempio in blocco. Iniziare da 15 a 30 esempi specifici e variabili. 

Ogni espressione di esempio deve avere **dati necessari per estrarre i dati** progettati ed etichettati con le **entità**. 

|Elemento Key|Finalità|
|--|--|
|Finalità|**Classificare** le espressioni utente in un'unica intenzione o in un'azione. Gli esempi includono `BookFlight` e `GetWeather`.|
|Persona giuridica|**Estrarre** i dati da un enunciato necessario per completare l'intenzione. Gli esempi includono la data e l'ora di viaggio e località.|

Si progetta l'app LUIS per ignorare le espressioni che non sono rilevanti per il dominio dell'app assegnando l'espressione a **Nessuna** finalità. 

## <a name="test-and-train-your-app"></a>Eseguire test e training dell'app

Quando si hanno da 15 a 30 espressioni di esempio diverse in ogni finalità, con le entità obbligatorie etichettate, è necessario eseguire il test e il [Training](luis-how-to-train.md). 

## <a name="publish-to-a-prediction-endpoint"></a>Pubblicare in un endpoint di stima

Assicurarsi di pubblicare l'app in modo che sia disponibile nelle aree dell' [endpoint di stima](luis-reference-regions.md) necessarie. 

## <a name="test-your-published-app"></a>Testare l'app pubblicata

È possibile testare l'app LUIS pubblicata dall'endpoint di stima HTTPS. Il test dall'endpoint di stima consente a LUIS di scegliere eventuali espressioni con una bassa confidenza per la [Revisione](luis-how-to-review-endpoint-utterances.md).  

## <a name="create-a-new-version-for-each-cycle"></a>Crea una nuova versione per ogni ciclo

Le versioni in LUIS sono simili alle versioni nella programmazione tradizionale. Ogni versione è uno snapshot temporizzato dell'app. Prima di apportare modifiche all'app, creare una nuova versione. È più facile tornare a una versione precedente, quindi provare a rimuovere gli Intent e le espressioni in uno stato precedente.

L'ID versione è composto da caratteri, cifre o '.' e non può superare i 10 caratteri.

La versione iniziale (0.1) è la versione attiva predefinita. 

### <a name="begin-by-cloning-an-existing-version"></a>Per iniziare, clonare una versione esistente

Clonare una versione esistente da utilizzare come punto di partenza per la nuova versione. Dopo aver clonato una versione, la nuova versione diventa la versione **attiva**. 

### <a name="publishing-slots"></a>Pubblicazione di slot
È possibile pubblicare negli slot di produzione e di gestione temporanea. Ogni slot può presentare una versione diversa o la stessa versione. Questa operazione è utile per verificare le modifiche prima della pubblicazione in produzione, disponibile per bot o altre applicazioni LUIS chiamante. 

Le versioni con Training non sono automaticamente disponibili nell' [endpoint](luis-glossary.md#endpoint)dell'app. È necessario [pubblicare](luis-how-to-publish-app.md) o ripubblicare una versione affinché sia disponibile in corrispondenza dell'endpoint dell'app. È possibile pubblicare in **gestione temporanea** e **produzione**, fornendo due versioni dell'app disponibili nell'endpoint. Se è necessario che a un endpoint siano disponibili più versioni dell'app, esportare la versione e reimportarla in una nuova app. La nuova app presenta un ID app diverso.

### <a name="import-and-export-a-version"></a>Importare ed esportare una versione
È possibile importare una versione a livello di app. Tale versione diventa la versione attiva e usa l'ID versione nella proprietà `versionId` del file dell'app. È anche possibile importare in un'app esistente a livello di versione. La nuova versione diventa quella attiva. 

È possibile esportare una versione a livello di app o di versione. L'unica differenza è che la versione esportata a livello di app è la versione attualmente attiva, mentre a livello di versione è possibile scegliere qualsiasi versione da esportare nella pagina **[Settings](luis-how-to-manage-versions.md)** (Impostazioni). 

Il file esportato non contiene:

* informazioni apprese dal computer perché l'app viene sottoposta a training dopo l'importazione
* informazioni sul collaboratore

Per eseguire il backup dello schema dell'app LUIS, esportare una versione dal portale LUIS.

## <a name="manage-contributor-changes-with-versions-and-apps"></a>Gestire le modifiche ai collaboratori con versioni e app

LUIS fornisce il concetto di collaboratori di un'app, fornendo le autorizzazioni a livello di risorsa di Azure. Combinare questo concetto con il controllo delle versioni per fornire la collaborazione di destinazione. 

Usare le tecniche seguenti per gestire le modifiche del collaboratore nell'app.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Gestione di più versioni all'interno della stessa app
Eseguire innanzitutto la [clonazione](luis-how-to-manage-versions.md#clone-a-version) da una versione base per ogni autore. 

Ogni autore apporta modifiche alla propria versione dell'app. Dopo che ogni autore è soddisfatto del proprio modello, esportare le nuove versioni in file JSON.  

Le app esportate, i file con estensione JSON o LU possono essere confrontate per le modifiche. Combinare i file per creare un singolo file della nuova versione. Modificare la proprietà **VersionId** per indicare la nuova versione unita. Importare la versione nell'app originale. 

Questo metodo consente di disporre di una versione attiva, di una versione di staging e di una versione pubblicata. È possibile confrontare i risultati della versione attiva con una versione pubblicata (fase o produzione) nel [riquadro test interattivo](luis-interactive-test.md).

### <a name="manage-multiple-versions-as-apps"></a>Gestione di più versioni come app
[Esportare](luis-how-to-manage-versions.md#export-version) la versione base. Ogni autore importa la versione. L'utente che importa l'app è il proprietario della versione. Dopo aver modificato l'app, esportare la versione. 

Le app esportate sono file in formato JSON confrontabili con l'esportazione base a livello di modifiche. Combinare i file per creare un singolo file JSON della nuova versione. Modificare la proprietà **versionId** nel file JSON affinché indichi la nuova versione unita. Importare la versione nell'app originale.

Altre informazioni sulla creazione di contributi da [collaboratori](luis-how-to-collaborate.md).

## <a name="review-endpoint-utterances-to-begin-the-new-authoring-cycle"></a>Esaminare le espressioni dell'endpoint per iniziare il nuovo ciclo di creazione

Dopo aver completato un ciclo di creazione, è possibile ricominciare. Iniziare a esaminare [gli enunciati dell'endpoint di stima](luis-how-to-review-endpoint-utterances.md) Luis contrassegnati con una bassa confidenza. Controllare questi enunciati sia per la finalità stimata corretta sia per l'entità corretta e completa estratta. Una volta esaminate e accettate le modifiche, l'elenco di revisione deve essere vuoto.  

## <a name="next-steps"></a>Passaggi successivi

Concetti di [collaborazione](luis-concept-keys.md).
