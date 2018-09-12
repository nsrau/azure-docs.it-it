---
title: Che cos'è una voce personalizzata? - Servizi cognitivi di Azure
description: Questo articolo offre una panoramica della personalizzazione della voce del servizio di sintesi vocale Microsoft, che consente di creare una voce distintiva e di tipo unico per il proprio marchio.
services: cognitive-services
author: noellelacharite
ms.service: cognitive-services
ms.topic: article
ms.date: 05/07/2018
ms.author: nolach
ms.openlocfilehash: 35572f046b3702deba56e86819b8ad0cd7ae6e9b
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2018
ms.locfileid: "43842471"
---
# <a name="creating-custom-voice-fonts"></a>Creazione di caratteri voce personalizzati

La personalizzazione della voce del servizio di sintesi vocale Microsoft consente di creare una voce distintiva e di tipo unico per il proprio marchio: un *carattere voce.* 

Per creare un carattere voce, effettuare una registrazione in studio e caricare gli script associati come dati di training. Il servizio crea quindi un modello vocale univoco ottimizzato per la registrazione. È quindi possibile usare questo carattere voce per la sintesi vocale. 

È possibile iniziare con una piccola quantità di dati per un modello di verifica. Tuttavia, più dati si forniscono, più naturale e professionale risulterà la voce.


## <a name="prerequisites"></a>Prerequisiti

La funzionalità di personalizzazione della voce per la sintesi vocale è attualmente in versione di anteprima privata. [Compilare il modulo di richiesta](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0N8Vcdi8MZBllkZb70o6KdURjRaUzhBVkhUNklCUEMxU0tQMEFPMjVHVi4u) per essere presi in considerazione per l'accesso.

È necessario disporre anche di un account di Azure e di una sottoscrizione al servizio Voce. [Crearne una](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started) se non è già stato fatto. Collegare la sottoscrizione al portale per la voce personalizzata come indicato di seguito.

1. Accedere al [portale per la voce personalizzata](https://customvoice.ai) tramite lo stesso account Microsoft usato per richiedere l'accesso.

2. Passare a "All Subscriptions" (Tutte le sottoscrizioni) sotto il nome dell'account in alto a destra.

    ![Sottoscrizioni](media/custom-voice/subscriptions.png)

3. Nella pagina "Sottoscrizioni" scegli "Connect existing subscription" (Connetti a una sottoscrizione esistente).

4. Incollare la chiave di sottoscrizione nella tabella, come illustrato di seguito. Ogni sottoscrizione dispone di due chiavi che è possibile usare nell'applicazione.

     ![Aggiungere la sottoscrizione](media/custom-voice/add-subscription.png)

Ora si è pronti per iniziare.

> [!IMPORTANT]
> Durante la fase di anteprima privata, le sottoscrizioni devono essere incluse nell'elenco elementi consentiti per usare la funzionalità voce personalizzata. Seguire i passaggi nella pagina per includere le sottoscrizioni nell'elenco elementi.

## <a name="prepare-recordings-and-transcripts"></a>Preparare le registrazioni e le trascrizioni

Un set di dati di training vocale è costituito da un set di file audio, insieme a un file di testo contenente le trascrizioni di tutti i file audio.

È possibile preparare questi file in due modi: scrivere uno script e farlo leggere da uno speaker oppure usare audio disponibile pubblicamente e trascriverne il testo. Nel secondo caso, modificare le disfluenze dai file audio, ad esempio "um" e altri suoni riempitivi, balbettii, parole mormorate o errori di pronuncia.

Per produrre un carattere voce ottimale, è importante che le registrazioni vengano eseguite in un ambiente silenzioso con un microfono di alta qualità. Volume uniforme, velocità, tono e manierismi espressivi sono elementi essenziali per la creazione di una voce digitale di buona qualità. Per creare una voce per l'uso in produzione, è consigliabile avvalersi di uno studio di registrazione e uno speaker professionali. Per altri dettagli, vedere [Come registrare esempi vocali per una voce personalizzata](record-custom-voice-samples.md).

### <a name="audio-files"></a>File audio

Ciascun file audio deve contenere una singola espressione, ad esempio un'unica frase o una sola battuta di un dialogo. Tutti i file devono essere nella stessa lingua (le voci personalizzate multilingue non sono supportate). I file audio devono inoltre avere un nome numerico univoco con l'estensione `.wav`.

I file audio devono essere preparati come segue. Gli altri formati non sono supportati e verranno rifiutati.

| **Proprietà** | **Valore** |
| ------------ | --------- |
| Formato file  | RIFF (WAV)|
| Frequenza di campionamento| Almeno 16.000 Hz |
| Formato del campione| PCM, 16 bit |
| File Name    | Numerico, con estensione `.wav` |
| Formato di archiviazione| Zip      |
| Dimensione massima del file|200 MB|

> [!NOTE]
> I file Wave con una frequenza di campionamento inferiore a 16.000 Hz vengono rifiutati. Se un file con estensione zip contiene file wave con frequenze di campionamento diverse, vengono importati solo i file con una frequenza uguale o superiore a 16.000 Hz.
> Il portale attualmente consente di importare archivi ZIP fino a 200 MB. È tuttavia possibile caricare più archivi. Il numero massimo di set di dati consentito è di 10 file ZIP per gli utenti con una sottoscrizione gratuita e di 50 per gli utenti con una sottoscrizione standard.

### <a name="transcripts"></a>Trascrizioni

Il file di trascrizione è un file di testo normale (ANSI/UTF-8/UTF-8-BOM/UTF-16-LE/UTF-16-BE). Ogni riga del file di trascrizione deve contenere il nome di un file audio, seguito da un carattere di tabulazione (punto di codice 9) e infine la trascrizione. Non sono consentite righe vuote.

Ad esempio: 

```
0000000001  This is the waistline, and it's falling.
0000000002  We have trouble scoring.
0000000003  It was Janet Maslin.
```

Il sistema per la voce personalizzata normalizza le trascrizioni convertendo il testo in lettere minuscole e rimuovendo la punteggiatura estranea. È importante che le trascrizioni riflettano in modo completamente accurato le registrazioni audio corrispondenti.

> [!TIP]
> Durante la creazione di voci per la sintesi vocale da usare in produzione, selezionare le espressioni (o scrivere gli script) considerando sia la copertura fonetica che l'efficienza. Problemi nell'ottenere i risultati desiderati? [Contattare il team di voce personalizzata](mailto:tts@microsoft.com) per altre informazioni su come ottenere assistenza.

## <a name="upload-your-datasets"></a>Caricare i set di dati

Dopo aver preparato l'archivio dei file audio e le trascrizioni, caricarli tramite il [portale del servizio per la voce personalizzata](https://customvoice.ai).

> [!NOTE]
> I set di dati non possono essere modificati dopo essere stati caricati. Se ad esempio si dimentica di includere le trascrizioni di alcuni dei file audio o si sceglie accidentalmente il sesso errato, è necessario caricare nuovamente l'intero set di dati. Controllare attentamente il set di dati e le impostazioni prima di iniziare il caricamento.

1. Accedere al portale.

2. Scegliere **Data** (Dati) sotto Custom Voice (Voce personalizzata) nella pagina principale. 

    Verrà visualizzata la tabella My Voice Data (Dati vocali personali). Se la tabella è vuota, non è stato ancora caricato alcun set di dati vocali.

3. Fare clic su **Import data** (Importa dati) per aprire la pagina per il caricamento di un nuovo set di dati.

    ![Importare i dati vocali](media/custom-voice/import-voice-data.png)

4. Immettere il nome e la descrizione negli appositi campi. 

5. Selezionare le impostazioni locali per i caratteri voce. Verificare che le informazioni sulle impostazioni locali corrispondano alla lingua dei dati delle registrazioni e degli script. 

6. Selezionare il sesso dello speaker di cui è stata usata la voce.

7. Scegliere gli script e i file audio da caricare. 

8. Fare clic su **Import** (Importa) per caricare i dati. Per i set di dati di grandi dimensioni, l'importazione potrebbe richiedere alcuni minuti.

> [!NOTE]
> Gli utenti con una sottoscrizione gratuita possono caricare due set di dati alla volta. Gli utenti con una sottoscrizione standard possono caricare cinque set di dati contemporaneamente. Se si raggiunge il limite, attendere che venga completata l'importazione di almeno un set di dati, quindi riprovare.

Una volta completato il caricamento, verrà visualizzata nuovamente la tabella My Voice Data (Dati vocali personali). Sarà presente una voce che corrisponde al set di dati appena caricato.

I set di dati vengono convalidati automaticamente dopo il caricamento. La convalida dei dati include una serie di controlli sui file audio per verificare il formato dei file, le dimensioni e la frequenza di campionamento. I controlli sui file di trascrizione verificano il formato dei file ed eseguono alcune operazioni di normalizzazione del testo. Le espressioni sono trascritte usando il riconoscimento vocale e il testo risultante viene confrontato con la trascrizione specificata.

![Dati vocali personali](media/custom-voice/my-voice-data.png)

Nella tabella seguente sono indicati gli stati di elaborazione per i set di dati importati. 

| Stato | Significato
| ----- | -------
| `NotStarted` | Il set di dati è stato ricevuto ed è in coda per l'elaborazione
| `Running` | La convalida del set di dati è in corso
| `Succeeded` | Il set di dati è stato convalidato e ora può essere usato per creare un carattere voce

Al termine della convalida, è possibile visualizzare il numero totale di espressioni corrispondenti per ogni set di dati nella colonna Utterance (Espressione).

È possibile scaricare un report per controllare i punteggi della pronuncia e il livello di rumore per ognuna delle registrazioni. Il punteggio della pronuncia è compreso tra 0 e 100. Un punteggio inferiore a 70 in genere indica un errore del riconoscimento vocale o una mancata corrispondenza dello script. Un forte accento può ridurre il punteggio della pronuncia e influire sulla voce digitale generata.

Un rapporto segnale/rumore superiore indica un livello di rumore inferiore nell'audio. Generalmente è possibile raggiungere un rapporto segnale/rumore superiore a 50 eseguendo la registrazione tramite studi professionali. L'audio con un rapporto segnale/rumore inferiore a 20 può determinare la presenza di rumore nella voce generata.

Valutare se ripetere la registrazione di qualsiasi espressione con punteggi di pronuncia o rapporti segnale/rumore particolarmente bassi. Se non è possibile ripetere la registrazione, tali espressioni possono essere escluse dal set di dati.

## <a name="build-your-voice-font"></a>Creare il carattere voce

Al termine della convalida del set di dati, è possibile usarlo per creare il carattere voce personalizzato. 

1. Scegliere **Models** (Modelli) dal menu a discesa "Custom Voice" (Voce personalizzata). 
 
    Verrà visualizzata la tabella My Voice Fonts (Caratteri voce personali), con tutti i caratteri voce personalizzati che sono già stati creati.

1. Fare clic su **Create voices** (Crea voci) sotto il titolo della tabella. 

    Verrà visualizzata la pagina per la creazione di un carattere voce. Le impostazioni locali correnti sono visualizzate nella prima riga della tabella. Modificare le impostazioni locali per creare una voce in un'altra lingua. Le impostazioni locali devono corrispondere ai set di dati usati per creare la voce.

1. Come durante il caricamento del set di dati, immettere un nome e una descrizione per identificare il modello. 

    Scegliere con attenzione, perché il nome immesso in questo campo sarà il nome usato per specificare la voce nella richiesta di sintesi vocale nell'ambito dell'input SSML. Sono consentiti solo lettere, numeri e alcuni caratteri di punteggiatura, come "-", "_", "(" e ")".

    Il campo della descrizione generalmente viene usato per registrare i nomi dei set di dati usati per creare il modello.

1. Scegliere il sesso del carattere voce. Deve corrispondere al sesso del set di dati.

1. Selezionare uno o più set di dati da usare per eseguire il training del carattere voce. Tutti i set di dati usati devono essere dello stesso speaker.

1. Fare clic su **Crea** per iniziare a creare il carattere voce.

    ![Creare il modello](media/custom-voice/create-model.png)

Il nuovo modello verrà visualizzato nella tabella My Voice Fonts (Caratteri voce personali). 

![Caratteri voce personali](media/custom-voice/my-voice-fonts.png)

Lo stato visualizzato riflette il processo di conversione del set di dati in un carattere voce, come illustrato di seguito.

| Stato | Significato
| ----- | -------
| `NotStarted` | La richiesta per la creazione del carattere voce è in coda per l'elaborazione
| `Running` | La creazione del carattere voce è in corso
| `Succeeded` | Il carattere voce è stato creato e può essere distribuito

Il tempo necessario per il training varia a seconda del volume dei dati audio elaborati. In genere, i tempi vanno da 30 minuti per alcune centinaia di espressioni a 40 ore per 20.000 espressioni.

> [!NOTE]
> Gli utenti con una sottoscrizione gratuita possono eseguire il training di due caratteri voce alla volta. Gli utenti con una sottoscrizione standard possono eseguire il training di tre voci contemporaneamente. Se si raggiunge il limite, attendere che venga completata l'importazione di almeno un carattere voce, quindi riprovare.

## <a name="test-your-voice-font"></a>Testare il carattere voce

Dopo aver creato correttamente il carattere voce, è possibile testarlo prima di distribuirlo per l'uso. Fare clic su **Test** nella colonna Operazioni della tabella My Voice Fonts (Caratteri voce personali). Verrà visualizzata la pagina di test per il carattere voce selezionato. La tabella è vuota se non sono ancora state inviate richieste di test per la voce.

Fare clic sul pulsante **Test with text** (Test con testo) sotto il titolo della tabella per visualizzare un menu di scelta rapida per l'invio di richieste di test. È possibile inviare la richiesta di test in testo normale o in formato SSML. La dimensione massima di input è 1.024 caratteri, inclusi tutti i tag per la richiesta SSML. La lingua del testo deve corrispondere a quella del carattere voce.

Dopo aver compilato la casella di testo e confermato la modalità di input, fare clic su **Sì** per inviare la richiesta di test e tornare alla pagina di prova. La tabella ora include una voce corrispondente alla nuova richiesta e la colonna dello stato. La sintesi vocale può richiedere alcuni minuti. Quando nella colonna dello stato è indicato Succeeded (Completato), è possibile scaricare l'input di testo (un file `.txt`) e l'output audio (un file `.wav`) e controllare la qualità di quest'ultimo.

## <a name="create-and-use-a-custom-endpoint"></a>Creare e usare un endpoint personalizzato

Dopo aver creato e testato il modello vocale, distribuirlo in un endpoint personalizzato per la sintesi vocale. Sarà quindi possibile usare questo endpoint al posto dell'endpoint normale quando si effettuano richieste di sintesi vocale tramite l'API REST. L'endpoint personalizzato può essere richiamato solo dalla sottoscrizione usata per distribuire il carattere.

Per creare un nuovo endpoint personalizzato, scegliere **Endpoints** (Endpoint) dal menu Custom Voice (Voce personalizzata) nella parte superiore della pagina. Verrà visualizzata la pagina My Deployed Voices (Voci personali distribuite), con la relativa tabella di endpoint vocali personalizzati correnti, se presenti. Le impostazioni locali correnti vengono visualizzate nella prima riga della tabella. Per creare una distribuzione per una lingua diversa, cambiare le impostazioni locali visualizzate. Le impostazioni locali devono corrispondere alla voce da distribuire.

Fare clic sul pulsante **Deploy voices** (Distribuisci voci) per creare un nuovo endpoint. Inserire il nome e la descrizione dell'endpoint personalizzato.

Nel menu Subscription (Sottoscrizione) scegliere la sottoscrizione da usare. Gli utenti con una sottoscrizione gratuita possono avere solo un modello distribuito alla volta. Gli utenti con una sottoscrizione standard possono creare fino a 20 endpoint, ognuno con una voce personalizzata.

![Creare un endpoint](media/custom-voice/create-endpoint.png)

Dopo aver selezionato il modello da distribuire, fare clic su **Create** (Crea). Verrà visualizzata nuovamente la pagina My Deployed Voices (Voci personali distribuite), ora con una voce per il nuovo endpoint. La creazione di un'istanza per un nuovo endpoint potrebbe richiedere alcuni minuti. Quando lo stato della distribuzione cambia in Succeeded (Completato), l'endpoint è pronto per l'uso.

![Voci personali distribuite](media/custom-voice/my-deployed-voices.png)

Quando lo stato della distribuzione è Succeeded (Completato), l'endpoint del carattere voce distribuito viene visualizzato nella tabella My Deployed Voices (Voci personali distribuite). È possibile usare questo URI direttamente in una richiesta HTTP.

Il test online dell'endpoint è anche disponibile tramite il portale per la voce personalizzata. Per testare l'endpoint, scegliere **Endpoints testing** (Test endpoint) dal menu a discesa Custom Voice (Voce personalizzata). Viene visualizzata la pagina di test degli endpoint. Scegliere una voce personalizzata distribuita, quindi immettere il testo da leggere (come testo normale o in formato SSML) nella casella di testo.

> [!NOTE] 
> Quando si usa il formato SSML, il tag `<voice>` deve specificare il nome assegnato alla voce personalizzata al momento della creazione. Se si invia testo normale, viene usata sempre la voce personalizzata.

Fare clic su **Play** (Riproduci) per ascoltare il testo pronunciato con il carattere voce personalizzato.

![Test degli endpoint](media/custom-voice/endpoint-testing.png)

Dal punto di vista funzionale, l'endpoint personalizzato è identico all'endpoint standard usato per le richieste di sintesi vocale. Per altre informazioni, vedere [API REST](rest-apis.md).

## <a name="language-support"></a>Supporto per le lingue

La personalizzazione della voce è disponibile per l'inglese (Stati Uniti) (en-US), il cinese (zh-CN) e l'italiano (it-IT).

> [!NOTE]
> Il training per la voce in italiano inizia con un set di dati di oltre 2000 espressioni. Anche i modelli bilingui in lingua cinese-inglese sono supportati con un set di dati di oltre 2000 espressioni.

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
- [Riconoscimento vocale in C#](quickstart-csharp-dotnet-windows.md)
