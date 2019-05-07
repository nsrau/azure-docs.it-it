---
title: Creare una voce personalizzata - servizi di riconoscimento vocale
titlesuffix: Azure Cognitive Services
description: Quando si è pronti per caricare i dati, passare al portale vocale personalizzato. Creare o selezionare un progetto vocale personalizzato. Il progetto deve condividere la destra/impostazioni locali della lingua e le proprietà relativa al sesso come i dati che si intende utilizzare per il training vocali.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: fad69c4108d747c44eccf37b81adf2c7c615cb58
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65156847"
---
# <a name="create-a-custom-voice"></a>Creare una voce personalizzata

Nelle [preparano i dati vocali personalizzati](how-to-custom-voice-prepare-data.md), sono descritti i tipi di dati diversi è possibile usare per eseguire il training di una voce personalizzata e i requisiti di formato diverso. Dopo aver preparato i dati, è possibile iniziare a li caricano le [portale vocale personalizzato](http://aka.ms/custom-voice-portal), o tramite l'API training vocale personalizzato. Di seguito vengono descritti i passaggi del training di una voce personalizzata tramite il portale.

> [!NOTE]
> Questa pagina si presuppone di aver letto [Introduzione a Custom vocali](how-to-custom-voice.md) e [preparano i dati vocali personalizzati](how-to-custom-voice-prepare-data.md)e aver creato un progetto vocale personalizzato.

Selezionare le lingue supportate per le funzionalità vocali personalizzati: [linguaggio per la personalizzazione](language-support.md#customization).

## <a name="upload-your-datasets"></a>Caricare i set di dati

Quando si è pronti per caricare i dati, passare al [portale personalizzato vocale](http://aka.ms/custom-voice-portal). Creare o selezionare un progetto vocale personalizzato. Il progetto deve condividere la destra/impostazioni locali della lingua e le proprietà relativa al sesso come i dati che si intende utilizzare per il training vocali. Ad esempio, selezionare `en-GB` se le registrazioni audio hai avviene in lingua inglese con una distinzione tra caratteri accentati Regno Unito.

Andare alla **Data** scheda e fare clic su **caricare i dati**. Nella procedura guidata, selezionare il tipo di dati corretti che corrisponde a ciò che è stato preparato.

Ogni set di dati caricati deve soddisfare i requisiti per il tipo di dati scelto. È importante formattare correttamente i dati prima del caricamento. In questo modo che verranno elaborati in modo accurato i dati dal servizio vocali personalizzati. Passare a [preparano i dati vocali personalizzati](how-to-custom-voice-prepare-data.md) e assicurarsi che i dati sono stati formattati giusto.

> [!NOTE]
> Gli utenti di sottoscrizione gratuita (F0) possono caricare simultaneamente i due set di dati. Utenti della sottoscrizione standard (S0) di cinque set di dati è possono caricare contemporaneamente. Se si raggiunge il limite, attendere che l'importazione di almeno un set di dati venga completata. Quindi riprovare.

> [!NOTE]
> Il numero massimo di set di dati possono essere importati per ogni sottoscrizione è 10 ZIP file gratuitamente utenti della sottoscrizione (F0) e 500 per gli utenti sottoscrizione standard (S0).

I set di dati vengono convalidati automaticamente dopo aver premuto il pulsante di caricamento. Convalida dei dati include una serie di controlli sui file audio per verificare il formato di file, dimensioni e frequenza di campionamento. Correggere gli errori, se presente e inviare di nuovo. Quando la richiesta di importazione di dati viene avviata correttamente, si verrà visualizzata una voce nella tabella di dati che corrisponde al set di dati che appena caricati.

Nella tabella seguente sono indicati gli stati di elaborazione per i set di dati importati:

| Stato | Significato |
| ----- | ------- |
| Elaborazione in corso | Il set di dati è stato ricevuto ed elaborato. |
| Succeeded | Il set di dati è stato convalidato e può ora essere usato per compilare un modello vocali. |
| Failed | Il set di dati è stato eseguito durante l'elaborazione a causa di vari motivi, ad esempio gli errori del file, i problemi dei dati o problemi di rete. |

Dopo la convalida è completa, è possibile visualizzare il numero totale di espressioni corrispondenti per ogni set di dati nel **Utterances** colonna. Se il tipo di dati che è stata selezionata richiede prolungata-audio segmentazione, questa colonna si rifletta solo le espressioni che è stata segmentata automaticamente in base le trascrizioni o tramite il servizio di trascrizione vocale. Inoltre, è possibile scaricare il set di dati convalidati per visualizzare i risultati di dettaglio delle espressioni è state importate e le trascrizioni di mapping. Hint: segmentazione prolungata-audio può richiedere più di un'ora per completare l'elaborazione dei dati.

Per i set di dati en-US e zh-CN, è possibile scaricare ulteriormente un report per controllare i punteggi di pronuncia e il livello di disturbo per ognuna delle registrazioni. Gli intervalli del punteggio di pronuncia sono compresi tra 0 e 100. Un punteggio della pronuncia inferiore a 70 generalmente indica un errore di riconoscimento vocale o una mancata corrispondenza dello script. Un forte accento può ridurre il punteggio della pronuncia e influire sulla voce digitale generata.

Un rapporto segnale/rumore superiore indica un livello di rumore inferiore nell'audio. Generalmente è possibile raggiungere un rapporto segnale/rumore superiore a 50 eseguendo la registrazione in studi professionali. L'audio con un rapporto segnale/rumore inferiore a 20 può determinare la presenza di rumore nella voce generata.

Valutare se ripetere la registrazione di qualsiasi espressione con punteggi di pronuncia o rapporti segnale/rumore particolarmente bassi. Se non è possibile ripetere la registrazione, tali espressioni possono essere escluse dal set di dati.

## <a name="build-your-custom-voice-model"></a>Compilare il modello vocali personalizzati

Dopo che il set di dati è stato convalidato, è possibile usarlo per compilare il modello vocali personalizzati.

1.  Passare a **sintesi vocale > vocali personalizzati > formazione**.

2.  Fare clic su **Train model**.

3.  Immettere quindi un' **Name** e **descrizione** per facilitare l'identificazione di questo modello.

    Scegliere un nome con attenzione. Il nome immesso in questo campo è il nome usato per specificare la voce nella richiesta di sintesi vocale come parte dell'input SSML. Solo lettere, numeri e alcuni caratteri di punteggiatura, ad esempio - \_e (',') sono consentiti. Usare nomi diversi per i modelli diversi vocali.

    Il campo **Descrizione** generalmente viene usato per registrare i nomi dei set di dati usati per creare il modello.

4.  Dal **selezionare i dati di training** pagina, scegliere uno o più set di dati che si desidera usare per il training. Controllare il numero di espressioni prima di inviare loro. È possibile iniziare con un numero qualsiasi di espressioni per i modelli vocale en-US e zh-CN. Per altre impostazioni locali, è necessario selezionare più di 2.000 espressioni a essere in grado di eseguire il training di una voce.

    > [!NOTE]
    > Nomi audio duplicati verranno rimossi dai corsi di formazione. Assicurarsi che i set di dati selezionati non contengono gli stessi nomi di audio in più file con estensione zip.

    > [!TIP]
    > Usando il set di dati di chi parla stesso è necessaria per la qualità dei risultati. Quando i set di dati che è stato inviato per il training contengono un numero totale di minore di 6.000 utterances distinct, si eseguirà il training modello tramite la tecnica statistica parametriche sintesi vocale. Nel caso in cui i dati di training superano un numero totale di 6.000 utterances distinti, verrà avviato un processo di training con la tecnica di sintesi di concatenazione. In genere la tecnologia di concatenazione può generare risultati più naturale e maggiore fedeltà vocali. [Contattare il team personalizzato vocali](mailto:speechsupport@microsoft.com) se si vuole eseguire il training di un modello con le tecnologie più recenti di sintesi vocale neurale che possono produrre una voce digitale equivalente a disponibile pubblicamente [neurale voices](language-support.md#neural-voices).

5.  Fare clic su **Train** per iniziare a creare il modello vocali.

La tabella di formazione consente di visualizzare una nuova voce che corrisponde a questo nuovo modello. La tabella viene anche visualizzato lo stato: L'elaborazione, completato, non è riuscita.

Lo stato visualizzato riflette il processo di conversione del set di dati a un modello vocale, come illustrato di seguito.

| Stato | Significato |
| ----- | ------- |
| Elaborazione in corso | Viene creato il modello vocali. |
| Succeeded | Il modello vocale è stato creato e può essere distribuito. |
| Failed | Il modello vocale è stato eseguito nel training dovuto a vari motivi, problemi di rete o problemi di dati, ad esempio non visti. |

Il tempo necessario per il training varia a seconda del volume dei dati audio elaborati. In genere, i tempi vanno da 30 minuti per alcune centinaia di espressioni a 40 ore per 20.000 espressioni. Il training del modello è stato completato correttamente, è possibile iniziare a testarla.

> [!NOTE]
> Utenti sottoscrizione gratuita (F0) possono eseguire il training di un carattere voce contemporaneamente. Sottoscrizione standard (S0) gli utenti possono eseguire il training tre voci contemporaneamente. Se si raggiunge il limite, attendere che venga completato il training di almeno un carattere voce, quindi riprovare.

> [!NOTE]
> Il numero massimo di modelli vocali possono essere sottoposti a training per ogni sottoscrizione è 10 modelli per gli utenti di sottoscrizione gratuita (F0) e 100 per utenti della sottoscrizione standard (S0).

## <a name="test-your-voice-model"></a>Testare il modello vocale

Dopo aver creato correttamente il carattere voce, è possibile testarlo prima di distribuirlo per l'uso.

1.  Passare a **sintesi vocale > vocali personalizzati > test**.

2.  Fare clic su **Aggiungi test**.

3.  Selezionare uno o più modelli che si desidera testare.

4.  Fornire il testo desiderato voice(s) da leggere a voce. Se si è scelto di testare più modelli in una sola volta, lo stesso testo da utilizzare per il test di modelli diversi.

    > [!NOTE]
    > La lingua del testo deve corrispondere a quella del carattere voce. È possibile testare solo i modelli è stato sottoposto a training. In questo passaggio è supportato solo testo normale.

5.  Fare clic su **Create**(Crea).

Dopo avere inviato la richiesta di test, si tornerà alla pagina di test. La tabella ora include una voce che corrisponde alla nuova richiesta e alla colonna di stato. La sintesi vocale può richiedere alcuni minuti. Quando la colonna Stato afferma **Succeeded**, è possibile riprodurre l'audio oppure scaricare l'input di testo (file con estensione txt) e audio di output (file con estensione wav) e un'ulteriore risultato su quest'ultimo per la qualità.

È anche possibile trovare i risultati del test nella pagina di dettaglio di ogni modelli è stata selezionata per il test. Andare alla **Training** scheda e fare clic sul nome del modello per immettere la pagina dei dettagli del modello.

## <a name="create-and-use-a-custom-voice-endpoint"></a>Creare e usare un endpoint vocali personalizzati

Dopo aver creato e testato il modello vocale, distribuirlo in un endpoint personalizzato per la sintesi vocale. È quindi possibile usare questo endpoint al posto dell'endpoint normale quando si effettuano richieste di sintesi vocale tramite l'API REST. L'endpoint personalizzato può essere chiamato solo per la sottoscrizione che è utilizzati per distribuire il tipo di carattere.

Per creare un nuovo endpoint vocali personalizzati, visitare **sintesi vocale > vocali personalizzati > distribuzione**. Selezionare **Aggiungi endpoint** e immettere una **Name** e **descrizione** per l'endpoint personalizzato. Selezionare il modello vocali personalizzati che si desidera associare a questo endpoint.

Dopo aver scelto il **Add** pulsante, nella tabella dell'endpoint, si verrà visualizzata una voce per il nuovo endpoint. La creazione di un'istanza per un nuovo endpoint potrebbe richiedere alcuni minuti. Quando lo stato della distribuzione è **Succeeded** (Completato), l'endpoint è pronto per l'uso.

> [!NOTE]
> Utenti sottoscrizione gratuita (F0) possono avere solo un modello distribuito. Utenti sottoscrizione standard (S0) possono creare fino a 50 endpoint, ognuno con un proprio vocali personalizzati.

> [!NOTE]
> Per usare le vocali personalizzati, è necessario specificare il nome del modello vocali, usare l'URI personalizzato direttamente in una richiesta HTTP e usare la stessa sottoscrizione di passare attraverso l'autenticazione del servizio di sintesi vocale.

Dopo aver distribuito l'endpoint, il nome dell'endpoint viene visualizzato come collegamento. Fare clic sul collegamento per visualizzare informazioni specifiche per l'endpoint, ad esempio la chiave dell'endpoint, URL dell'endpoint e codice di esempio.

Il test online dell'endpoint è anche disponibile tramite il portale per la voce personalizzata. Per testare l'endpoint, scegliere **controllare endpoint** dal **detail Endpoint** pagina. Viene visualizzata la pagina di test degli endpoint. Immettere il testo da pronunciare (in testo normale oppure [formato SSML](speech-synthesis-markup.md) nella casella di testo. Selezionare **Riproduci** per ascoltare il testo pronunciato con il carattere voce personalizzato. Questa funzionalità di testing viene addebitata rispetto all'utilizzo di sintesi vocale personalizzata.

Dal punto di vista funzionale, l'endpoint personalizzato è identico all'endpoint standard usato per le richieste di sintesi vocale. Per altre informazioni, vedere [API REST](rest-text-to-speech.md).

## <a name="next-steps"></a>Passaggi successivi

* [Guida: Esempi di voce di record](record-custom-voice-samples.md)
* [Riferimento all'API sintesi vocale](rest-text-to-speech.md)
