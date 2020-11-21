---
title: Creazione di contenuto audio-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: La creazione di contenuti audio è uno strumento online che consente di personalizzare e ottimizzare l'output di sintesi vocale di Microsoft per le app e i prodotti.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 7d4d1acac591bfe6ce40efcf6166357d43718363
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024960"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>Migliorare la sintesi con lo strumento di creazione del contenuto audio

La [creazione di contenuti audio](https://aka.ms/audiocontentcreation) è uno strumento facile da usare e potente che ti permette di creare contenuti audio estremamente naturali per diversi scenari, ad esempio audiolibri, trasmissioni di notizie, video narrativi e bot di chat. Con la creazione di contenuti audio è possibile ottimizzare le voci di sintesi vocale e progettare esperienze audio personalizzate in modo efficiente e a basso costo.

Lo strumento è basato sul [linguaggio di markup sintesi vocale (SSML)](speech-synthesis-markup.md). Consente di modificare gli attributi di output da testo a riconoscimento vocale in tempo reale o in sintesi batch, ad esempio caratteri vocali, stili vocali, velocità di pronuncia, pronuncia e prosodia.

È possibile accedere facilmente a più di 150 voci precompilate in quasi 50 lingue diverse, incluse le voci di TTS neurali all'avanguardia e la voce personalizzata se ne è stata compilata una. 

Vedere l' [esercitazione video](https://www.youtube.com/watch?v=O1wIJ7mts_w) per la creazione di contenuto audio.

## <a name="how-to-get-started"></a>Come iniziare?

La creazione di contenuti audio è uno strumento gratuito, ma si paga per il servizio di riconoscimento vocale di Azure che si utilizza. Per usare lo strumento, è necessario accedere con un account di Azure e creare una risorsa di riconoscimento vocale. Per ogni account Azure sono disponibili quote di sintesi vocale gratuite mensili che includono 500.000 caratteri per le voci neurali (al mese), 5 milioni caratteri per le voci standard e personalizzate (al mese) e 1 servizio di hosting di endpoint Voice personalizzato (al mese). La quantità mensile assegnata è in genere sufficiente per un piccolo team di contenuti di circa 3-5 persone. Ecco i passaggi per creare un account Azure e ottenere una risorsa di riconoscimento vocale. 

### <a name="step-1---create-an-azure-account"></a>Passaggio 1: creare un account Azure

Per usare la creazione di contenuto audio, è necessario disporre di un [account Microsoft](https://account.microsoft.com/account) e di un [account Azure](https://azure.microsoft.com/free/ai/). Seguire queste istruzioni per [configurare l'account](./overview.md#try-the-speech-service-for-free). 

[Portale di Azure](https://portal.azure.com/) è la posizione centralizzata per la gestione dell'account Azure. È possibile creare la risorsa di sintesi vocale, gestire l'accesso al prodotto e monitorare tutto da semplici app Web a distribuzioni cloud complesse. 

### <a name="step-2---create-a-speech-resource"></a>Passaggio 2: creare una risorsa vocale

Dopo aver effettuato l'iscrizione per l'account Azure, è necessario creare una risorsa vocale nell'account Azure per accedere ai servizi di riconoscimento vocale. Visualizzare le istruzioni per la [creazione di una risorsa di sintesi vocale](./overview.md#create-the-azure-resource). 

È necessario qualche secondo per distribuire la nuova risorsa del servizio Voce. Al termine della distribuzione, è possibile avviare il percorso di creazione del contenuto audio. 

 >[!NOTE]
   > Se si prevede di usare le voci neurali, assicurarsi di creare la risorsa in [un'area che supporta le voci neurali](regions.md#standard-and-neural-voices).
 
### <a name="step-3---log-into-the-audio-content-creation-with-your-azure-account-and-speech-resource"></a>Passaggio 3: accedere alla creazione di contenuti audio con l'account Azure e la risorsa vocale

1. Dopo avere ottenuto l'account Azure e la risorsa vocale, è possibile accedere alla [creazione del contenuto audio](https://aka.ms/audiocontentcreation) facendo clic su **Introduzione**.
2. Verrà visualizzata la pagina **delle risorse vocali** . Selezionare la risorsa vocale che si vuole usare. Fare clic su **Vai a Studio** per avviare la creazione dell'audio. È anche possibile creare una nuova risorsa vocale facendo clic su **Crea nuovo**. Quando si accede allo strumento di creazione del contenuto audio per la volta successiva, si collegherà direttamente ai file di lavoro audio nella risorsa di sintesi vocale corrente. 
3. È possibile modificare la risorsa di riconoscimento vocale in qualsiasi momento con l'opzione **Impostazioni** , situata nella parte superiore del NAV.

## <a name="how-to-use-the-tool"></a>Come usare lo strumento

Questo diagramma illustra i passaggi necessari per ottimizzare gli output di sintesi vocale. Usare i collegamenti seguenti per ulteriori informazioni su ogni passaggio.

:::image source="media/audio-content-creation/audio-content-creation-diagram.jpg" alt-text="Diagramma dei passaggi necessari per ottimizzare gli output di sintesi vocale":::


1. Scegliere la risorsa vocale che si vuole usare.
2. [Creare un file di ottimizzazione audio](#create-an-audio-tuning-file) usando gli script SSML o testo normale. Digitare o caricare il contenuto nella creazione di contenuto audio.
3. Scegliere la voce e la lingua per il contenuto dello script. La creazione di contenuto audio include tutte le voci di sintesi [vocale di Microsoft](language-support.md#text-to-speech). Puoi usare la tua voce standard, neurale o personalizzata.
   >[!NOTE]
   > L'accesso controllato è disponibile per le voci neurali personalizzate, che consentono di creare voci ad alta definizione simili alla voce naturale. Per ulteriori informazioni, vedere [processo](./text-to-speech.md)di controllo.

4. Fare clic sull'icona di **riproduzione** (un triangolo) per visualizzare l'anteprima dell'output di sintesi predefinito. Migliorare quindi l'output regolando la pronuncia, le interruzioni, il pitch, la velocità, l'intonazione, lo stile vocale e altro ancora. Per un elenco completo delle opzioni, vedere [linguaggio di markup sintesi vocale](speech-synthesis-markup.md). Ecco un [video](https://www.youtube.com/watch?v=O1wIJ7mts_w) che illustra come ottimizzare l'output vocale con la creazione di contenuto audio. 
5. Salvare ed [esportare l'audio ottimizzato](#export-tuned-audio). Quando si salva la traccia di ottimizzazione nel sistema, è possibile continuare a lavorare ed eseguire l'iterazione dell'output. Quando si è soddisfatti dell'output, è possibile creare un'attività di creazione audio con la funzionalità Esporta. È possibile osservare lo stato dell'attività di esportazione e scaricare l'output da usare con le app e i prodotti.

## <a name="create-an-audio-tuning-file"></a>Creare un file di ottimizzazione audio

Esistono due modi per ottenere il contenuto nello strumento di creazione del contenuto audio.

**Opzione 1:**

1. Fare clic sull'icona **nuovo file** in alto a destra per creare un nuovo file di ottimizzazione audio.
2. Digitare o incollare il contenuto nella finestra di modifica. I caratteri per ogni file sono fino a 20.000. Se lo script è più lungo di 20.000 caratteri, è possibile usare l'opzione 2 per suddividere automaticamente il contenuto in più file. 
3. Non dimenticare di salvarlo.

**Opzione 2:**

1. Fare clic su **carica** per importare uno o più file di testo. Sono supportati sia testo normale che SSML. Se il file di script è più di 20.000 caratteri, suddividere il file in base ai paragrafi, al carattere o alle espressioni regolari. 
3. Quando si caricano i file di testo, verificare che il file soddisfi questi requisiti.

   | Proprietà | Valore/note |
   |----------|---------------|
   | Formato file | Testo normale (con estensione txt)<br/> Testo SSML (. txt)<br/> I file zip non sono supportati |
   | Formato di codifica. | UTF-8 |
   | Nome file | Ogni file deve avere un nome univoco. I duplicati non sono supportati. |
   | Lunghezza del testo | Il limite di caratteri del file di testo è 20.000. Se i file superano la limitazione, suddividere i file con le istruzioni nello strumento. |
   | Restrizioni SSML | Ogni file SSML può contenere solo un singolo elemento di SSML. |

**Esempio di testo normale**

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```
**Esempio di testo SSML**

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Esporta audio ottimizzato

Dopo aver esaminato l'output audio e aver soddisfatto l'ottimizzazione e la regolazione, è possibile esportare l'audio.

1. Fare clic su **Esporta** per creare un'attività di creazione audio. L' **esportazione nella libreria audio** è consigliata perché supporta l'output audio lungo e l'intera esperienza di output audio. È anche possibile scaricare direttamente il file audio nel disco locale, ma sono disponibili solo i primi 10 minuti.
2. Scegliere il formato di output per l'audio ottimizzato. Di seguito è riportato un elenco dei formati supportati e delle frequenze di campionamento.
3. È possibile visualizzare lo stato dell'attività nella scheda **Esporta attività** . Se l'attività non riesce, vedere la pagina informazioni dettagliate per un report completo.
4. Al termine dell'attività, l'audio sarà disponibile per il download nella scheda **libreria audio** .
5. Fare clic su **Download**. A questo punto si è pronti per usare l'audio ottimizzato personalizzato nelle app o nei prodotti.

**Formati audio supportati**

| Formato | frequenza di campionamento di 16 kHz | frequenza di campionamento di 24 kHz |
|--------|--------------------|--------------------|
| wav | riff-16kHz-16 bit-mono-PCM | riff-24kHz-16 bit-mono-PCM |
| mp3 | audio-16kHz-128kbitrate-mono-MP3 | audio-24kHz-160kbitrate-mono-MP3 |

## <a name="how-to-addremove-audio-content-creation-users"></a>Come aggiungere o rimuovere gli utenti per la creazione di contenuti audio?

Se più utenti vogliono usare la creazione di contenuto audio, è possibile condividere l'account e la password di Azure con l'utente oppure concedere all'utente l'accesso alla sottoscrizione di Azure e alla risorsa di riconoscimento vocale. Se si aggiunge un utente a una sottoscrizione di Azure, l'utente può accedere a tutte le risorse nella sottoscrizione di Azure. Tuttavia, se si aggiunge un utente solo a una risorsa vocale, l'utente potrà accedere solo alla risorsa vocale e non potrà accedere ad altre risorse in questa sottoscrizione di Azure. Un utente con accesso alla risorsa di sintesi vocale può usare la creazione di contenuto audio.

### <a name="add-users-to-a-speech-resource"></a>Aggiungere utenti a una risorsa vocale

Seguire questi passaggi per aggiungere un utente a una risorsa vocale in modo da poter usare la creazione di contenuto audio.

1. Cercare **Servizi cognitivi** nella [portale di Azure](https://portal.azure.com/)Selezionare la risorsa vocale a cui si vogliono aggiungere utenti.
2. Fare clic su **Controllo di accesso (IAM)** . Fare clic sulla scheda **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo in questa sottoscrizione.
    :::image source="media/audio-content-creation/access-control-roles.png" alt-text="Scheda assegnazione ruolo":::
1. Fare clic su **Aggiungi** > **Aggiungi assegnazione di ruolo** per aprire il riquadro Aggiungi assegnazione di ruolo. Nell'elenco a discesa ruolo selezionare il ruolo **utente servizio cognitivo** . Se si vuole assegnare la proprietà dell'utente a questa risorsa vocale, è possibile selezionare il ruolo **proprietario** .
1. Selezionare un utente dall'elenco. Se l'utente non è visualizzato nell'elenco, è possibile digitare nella casella Seleziona per cercare i nomi visualizzati e gli indirizzi di posta elettronica nella directory. Se l'utente non è presente in questa directory, è possibile immettere l' [account Microsoft](https://account.microsoft.com/account) dell'utente (ritenuto attendibile da Azure Active Directory).
1. Fare clic su **Salva** per assegnare un ruolo. Dopo alcuni istanti, all'utente viene assegnato il ruolo utente servizio cognitivo nell'ambito della risorsa vocale.

    :::image source="media/audio-content-creation/add-role-first.png" alt-text="Finestra di dialogo Aggiungi ruolo":::

1. Gli utenti aggiunti riceveranno un messaggio di posta elettronica di invito. Dopo aver fatto clic su **Accept invito**  >  **Accept per partecipare ad Azure**, è possibile usare la [creazione di contenuto audio](https://aka.ms/audiocontentcreation).

Gli utenti che si trovano nella stessa risorsa di sintesi vocale vedranno il lavoro di tutti gli altri in audio Content Creation Studio. Se si vuole che ogni singolo utente disponga di un'area di lavoro univoca e privata nella creazione di contenuto audio, [creare una nuova risorsa vocale](#step-2---create-a-speech-resource) per ogni utente e concedere a ogni utente l'accesso univoco alla risorsa di sintesi vocale. 

### <a name="remove-users-from-a-speech-resource"></a>Rimuovere gli utenti da una risorsa vocale
1. Cercare **Servizi cognitivi** nella portale di Azure selezionare la risorsa vocale da cui si vogliono rimuovere gli utenti.
2. Fare clic su **Controllo di accesso (IAM)** . Fare clic sulla scheda **assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo per questa risorsa vocale.
3. Selezionare gli utenti che si desidera rimuovere e fare clic su **Rimuovi**  >  **OK**.
    :::image source="media/audio-content-creation/remove-user.png" alt-text="Pulsante Rimuovi":::

### <a name="enable-users-to-grant-access"></a>Consentire agli utenti di concedere l'accesso
Per consentire a uno degli utenti di concedere l'accesso ad altri utenti, è necessario assegnare all'utente il ruolo di proprietario per la risorsa vocale e impostare l'utente come lettore di directory di Azure. 
1. Aggiungere l'utente come proprietario della risorsa vocale. Vedere [come aggiungere utenti a una risorsa di sintesi vocale](#add-users-to-a-speech-resource).
    :::image source="media/audio-content-creation/add-role.png" alt-text="Campo proprietario ruolo":::
1. Selezionare il menu compresso in alto a sinistra. Fare clic su **Azure Active Directory** e quindi su **utenti**.
1. Cercare l'account Microsoft dell'utente e passare alla pagina dei dettagli dell'utente. Fare clic su **ruoli assegnati**.
1. Fare clic su **Add Assignment**  ->  **directory Readers**.

## <a name="see-also"></a>Vedi anche

* [API Long audio](./long-audio-api.md)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
