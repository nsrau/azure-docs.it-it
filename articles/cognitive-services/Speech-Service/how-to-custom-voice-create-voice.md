---
title: Creare una voce personalizzata - Servizio di riconoscimento vocaleCreate a Custom Voice - Speech service
titleSuffix: Azure Cognitive Services
description: Quando si è pronti a caricare i dati, passare al portale Voce personalizzata. Creare o selezionare un progetto Custom Voice. Il progetto deve condividere la lingua/impostazioni locali e le proprietà di genere corrette come dati che si intende utilizzare per il training vocale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: bbe1d651a7d2d2cac1b1aa78b815b2797ad185c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717321"
---
# <a name="create-a-custom-voice"></a>Creare una voce personalizzata

In [Preparare i dati per la voce personalizzata](how-to-custom-voice-prepare-data.md)vengono descritti i diversi tipi di dati che è possibile utilizzare per eseguire il training di una voce personalizzata e i requisiti di formato diversi. Dopo aver preparato i dati, è possibile iniziare a caricarli nel [portale Voce personalizzata](https://aka.ms/custom-voice-portal)o tramite l'API di training Custom Voice. Qui descriviamo i passaggi per la formazione di una voce personalizzata attraverso il portale.

> [!NOTE]
> In questa pagina si presuppone che sia stata letta [Introduzione a Custom Voice](how-to-custom-voice.md) e Preparazione dei dati per la voce [personalizzata](how-to-custom-voice-prepare-data.md)e che sia stato creato un progetto Custom Voice.

Controllare le lingue supportate per la voce personalizzata: lingua per la [personalizzazione](language-support.md#customization).

## <a name="upload-your-datasets"></a>Caricare i set di dati

Quando si è pronti per caricare i dati, passare al [portale Voce personalizzata](https://aka.ms/custom-voice-portal). Creare o selezionare un progetto Custom Voice. Il progetto deve condividere la lingua/impostazioni locali e le proprietà di genere corrette come dati che si intende utilizzare per il training vocale. Ad esempio, `en-GB` selezionare se le registrazioni audio in tuo hai è fatto in inglese con un accento del Regno Unito.

Vai alla scheda **Dati** e fai clic su **Carica dati**. Nella procedura guidata selezionare il tipo di dati corretto corrispondente a quello preparato.

Ogni set di dati caricato deve soddisfare i requisiti per il tipo di dati scelto. È importante formattare correttamente i dati prima che vengano caricati. In questo modo i dati verranno elaborati accuratamente dal servizio Custom Voice. Vai a [Preparare i dati per la voce personalizzata](how-to-custom-voice-prepare-data.md) e assicurati che i tuoi dati siano stati formattati correttamente.

> [!NOTE]
> Gli utenti con abbonamento gratuito (F0) possono caricare due set di dati contemporaneamente. Gli utenti della sottoscrizione standard (S0) possono caricare cinque set di dati contemporaneamente. Se si raggiunge il limite, attendere che l'importazione di almeno un set di dati venga completata. Quindi riprovare.

> [!NOTE]
> Il numero massimo di set di dati che possono essere importati per ogni sottoscrizione è 10 file .zip per gli utenti di sottoscrizione gratuita (F0) e 500 per gli utenti di sottoscrizione standard (S0).

I set di dati vengono convalidati automaticamente quando si preme il pulsante di caricamento. La convalida dei dati include una serie di controlli sui file audio per verificarne il formato, le dimensioni e la frequenza di campionamento. Correggere gli eventuali errori e inviarli di nuovo. Quando la richiesta di importazione dei dati viene avviata correttamente, verrà visualizzata una voce nella tabella dati che corrisponde al set di dati appena caricato.

Nella tabella seguente sono indicati gli stati di elaborazione per i set di dati importati:

| State | Significato |
| ----- | ------- |
| Elaborazione in corso | Il set di dati è stato ricevuto ed è in fase di elaborazione. |
| Operazione completata | Il set di dati è stato convalidato e può ora essere usato per creare un modello vocale. |
| Operazione non riuscita | Il set di dati non è riuscito durante l'elaborazione a causa di molti motivi, ad esempio errori di file, problemi di dati o problemi di rete. |

Al termine della convalida, è possibile visualizzare il numero totale di espressioni corrispondenti per ogni set di dati nella colonna **Utterances.** Se il tipo di dati selezionato richiede la segmentazione audio lunga, questa colonna riflette solo le espressioni che abbiamo segmentato per te in base alle trascrizioni o tramite il servizio di trascrizione vocale. È possibile scaricare ulteriormente il set di dati convalidato per visualizzare i risultati dettagliati delle espressioni importate correttamente e le relative trascrizioni di mapping. Suggerimento: la segmentazione audio lunga può richiedere più di un'ora per completare l'elaborazione dei dati.

Per i set di dati en-US e zh-CN, è possibile scaricare ulteriormente un report per controllare i punteggi di pronuncia e il livello di rumore per ciascuna delle registrazioni. Gli intervalli del punteggio di pronuncia sono compresi tra 0 e 100. Un punteggio della pronuncia inferiore a 70 generalmente indica un errore di riconoscimento vocale o una mancata corrispondenza dello script. Un forte accento può ridurre il punteggio della pronuncia e influire sulla voce digitale generata.

Un rapporto segnale/rumore superiore indica un livello di rumore inferiore nell'audio. Generalmente è possibile raggiungere un rapporto segnale/rumore superiore a 50 eseguendo la registrazione in studi professionali. L'audio con un rapporto segnale/rumore inferiore a 20 può determinare la presenza di rumore nella voce generata.

Valutare se ripetere la registrazione di qualsiasi espressione con punteggi di pronuncia o rapporti segnale/rumore particolarmente bassi. Se non è possibile ripetere la registrazione, tali espressioni possono essere escluse dal set di dati.

## <a name="build-your-custom-voice-model"></a>Crea il tuo modello vocale personalizzato

Dopo la convalida del set di dati, è possibile usarlo per creare il modello vocale personalizzato.

1.  Passare a **Text-to-Speech > Custom Voice > Training**.

2.  Fare clic su **Formato modello**.

3.  Immettere quindi un **Nome** e una **Descrizione** per identificare il modello.

    Scegliere un nome con attenzione. Il nome immesso in questo campo è il nome usato per specificare la voce nella richiesta di sintesi vocale come parte dell'input SSML. Sono consentiti solo lettere, numeri e alcuni caratteri \_di punteggiatura, ad esempio -, , e (', '). Utilizzare nomi diversi per modelli vocali diversi.

    Un utilizzo comune del campo **Descrizione** consiste nel registrare i nomi dei set di dati utilizzati per creare il modello.

4.  Nella pagina **Seleziona dati di training** scegliere uno o più set di dati da usare per il training. Controllare il numero di espressioni prima di inviarle. È possibile iniziare con un numero qualsiasi di espressioni per i modelli vocali en-US e zh-CN. Per altre impostazioni locali, è necessario selezionare più di 2.000 espressioni per poter eseguire il training di una voce.

    > [!NOTE]
    > I nomi audio duplicati verranno rimossi dall'allenamento. Assicurarsi che i dataset selezionati non contengano gli stessi nomi audio in più file .zip.

    > [!TIP]
    > L'utilizzo dei set di dati dello stesso altoparlante è necessario per ottenere risultati di qualità. Quando i set di dati inviati per il training contengono un numero totale di espressioni distinte inferiore a 6.000, verrà eseguito il training del modello vocale tramite la tecnica di sintesi parametrica statistica. Nel caso in cui i dati di training superino un numero totale di 6.000 espressioni distinte, verrà avviato un processo di training con la tecnica di sintesi della concatenazione. Normalmente la tecnologia di concatenazione può portare a risultati vocali più naturali e ad alta fedeltà. [Contattare il team Di Voice personalizzato](https://go.microsoft.com/fwlink/?linkid=2108737) se si desidera eseguire il training di un modello con la più recente tecnologia di ttS neurale in grado di produrre una voce digitale equivalente alle voci [neurali](language-support.md#neural-voices)disponibili pubblicamente.

5.  Fare clic su **Train** per iniziare a creare il modello vocale.

Nella tabella Training viene visualizzata una nuova voce che corrisponde al modello appena creato. Nella tabella viene inoltre visualizzato lo stato: Elaborazione, Operazione riuscita, Operazione non riuscita.

Lo stato visualizzato riflette il processo di conversione del set di dati in un modello vocale, come illustrato di seguito.

| State | Significato |
| ----- | ------- |
| Elaborazione in corso | È in corso la creazione del modello vocale. |
| Operazione completata | Il modello vocale è stato creato e può essere distribuito. |
| Operazione non riuscita | Il modello vocale non è stato eseguito durante il training a causa di molti motivi, ad esempio problemi di dati invisibili o problemi di rete. |

Il tempo necessario per il training varia a seconda del volume dei dati audio elaborati. In genere, i tempi vanno da 30 minuti per alcune centinaia di espressioni a 40 ore per 20.000 espressioni. Una volta che il training del modello ha esito positivo, è possibile iniziare a testarlo.

> [!NOTE]
> Gli utenti con abbonamento gratuito (F0) possono addestrare un carattere vocale contemporaneamente. Gli utenti con abbonamento standard (S0) possono formare tre voci contemporaneamente. Se si raggiunge il limite, attendere che venga completato il training di almeno un carattere voce, quindi riprovare.

> [!NOTE]
> Il numero massimo di modelli vocali di cui è consentito il training per sottoscrizione è 10 modelli per gli utenti con sottoscrizione gratuita (F0) e 100 per gli utenti di sottoscrizione standard (S0).

Se si utilizza la funzionalità di training vocale neurale, è possibile scegliere di eseguire il training di un modello ottimizzato per scenari di streaming in tempo reale o di un modello neurale HD ottimizzato per la [sintesi audio lunga](long-audio-api.md)asincrona.  

## <a name="test-your-voice-model"></a>Testare il modello vocale

Dopo aver creato correttamente il carattere voce, è possibile testarlo prima di distribuirlo per l'uso.

1.  Passare a **Text-to-Speech > Custom Voice > Testing**.

2.  Fare clic su **Aggiungi test**.

3.  Selezionare uno o più modelli che si desidera testare.

4.  Fornire il testo che si desidera che le voci di parlare. Se si è scelto di testare più modelli contemporaneamente, lo stesso testo verrà utilizzato per il test per modelli diversi.

    > [!NOTE]
    > La lingua del testo deve corrispondere a quella del carattere voce. Solo i modelli sottoposti a training con successo possono essere testati. In questo passaggio è supportato solo testo normale.

5.  Fare clic su **Crea**.

Una volta inviata la richiesta di test, si tornerà alla pagina di test. La tabella ora include una voce che corrisponde alla nuova richiesta e alla colonna di stato. La sintesi vocale può richiedere alcuni minuti. Quando la colonna di stato indica **Operazione riuscita**, è possibile riprodurre l'audio o scaricare l'input di testo (un file .txt) e l'output audio (un file wav) e un'ulteriore audizione per la qualità.

È inoltre possibile trovare i risultati del test nella pagina dei dettagli di ogni modello selezionato per il test. Passare alla scheda **Formazione** e fare clic sul nome del modello per immettere la pagina dei dettagli del modello.

## <a name="create-and-use-a-custom-voice-endpoint"></a>Creare e usare un endpoint vocale personalizzatoCreate and use a custom voice endpoint

Dopo aver creato e testato il modello vocale, distribuirlo in un endpoint personalizzato per la sintesi vocale. È quindi possibile usare questo endpoint al posto dell'endpoint normale quando si effettuano richieste di sintesi vocale tramite l'API REST. L'endpoint personalizzato può essere chiamato solo dalla sottoscrizione usata per distribuire il tipo di carattere.

Per creare un nuovo endpoint vocale personalizzato, passare a **Sintesi vocale > Distribuzione**di Voice > personalizzati . Selezionare **Aggiungi endpoint** e immettere un **Nome** e una **Descrizione** per l'endpoint personalizzato. Selezionare quindi il modello vocale personalizzato che si desidera associare all'endpoint.

Dopo aver fatto clic sul pulsante **Aggiungi,** nella tabella degli endpoint verrà visualizzata una voce per il nuovo endpoint. La creazione di un'istanza per un nuovo endpoint potrebbe richiedere alcuni minuti. Quando lo stato della distribuzione è **Succeeded**, l'endpoint è pronto per l'uso.

> [!NOTE]
> Gli utenti con abbonamento gratuito (F0) possono avere un solo modello distribuito. Gli utenti della sottoscrizione standard (S0) possono creare fino a 50 endpoint, ognuno con la propria voce personalizzata.

> [!NOTE]
> Per usare la voce personalizzata, è necessario specificare il nome del modello vocale, usare l'URI personalizzato direttamente in una richiesta HTTP e usare la stessa sottoscrizione per passare attraverso l'autenticazione del servizio di servizi di dominio Microsoft .

Dopo la distribuzione dell'endpoint, il nome dell'endpoint viene visualizzato come collegamento. Fare clic sul collegamento per visualizzare informazioni specifiche per l'endpoint, ad esempio la chiave dell'endpoint, l'URL dell'endpoint e il codice di esempio.

Il test online dell'endpoint è anche disponibile tramite il portale per la voce personalizzata. Per testare l'endpoint, scegliere **Controlla endpoint** dalla pagina **Dettagli endpoint.** Viene visualizzata la pagina di test degli endpoint. Immettere il testo da pronunciare (in formato testo normale o [SSML](speech-synthesis-markup.md) nella casella di testo. Selezionare **Riproduci** per ascoltare il testo pronunciato con il carattere voce personalizzato. Questa funzionalità di test verrà addebitata sull'utilizzo personalizzato della sintesi vocale.

Dal punto di vista funzionale, l'endpoint personalizzato è identico all'endpoint standard usato per le richieste di sintesi vocale. Per altre informazioni, vedere [API REST](rest-text-to-speech.md).

## <a name="next-steps"></a>Passaggi successivi

* [Guida: Registrare i campioni vocali](record-custom-voice-samples.md)
* [Informazioni di riferimento sull'API di sintesi vocale](rest-text-to-speech.md)
* [API audio lunga](long-audio-api.md)
