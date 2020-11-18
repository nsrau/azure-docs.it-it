---
title: Informazioni sul Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Il servizio Voce di Azure combina il riconoscimento vocale, la sintesi vocale e la traduzione vocale in un'unica sottoscrizione di Azure. Aggiungere il riconoscimento vocale ad applicazioni, strumenti e dispositivi con Speech SDK, Speech Devices SDK o API REST.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 08/20/2020
ms.author: trbye
ms.openlocfilehash: 7d31649e18f8cc687a9716c8ecafe556fa250de6
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377888"
---
# <a name="what-is-the-speech-service"></a>Informazioni sul Servizio di riconoscimento vocale

Il servizio Voce di Azure combina il riconoscimento vocale, la sintesi vocale e la traduzione vocale in un'unica sottoscrizione di Azure. Con l'[interfaccia della riga di comando di Voce](spx-overview.md), [Speech SDK](speech-sdk-reference.md), [Speech Devices SDK](https://aka.ms/sdsdk-quickstart), [Speech Studio](https://speech.microsoft.com/) o le [API REST](rest-apis.md), è facile abilitare il riconoscimento vocale per applicazioni, strumenti e dispositivi.

> [!IMPORTANT]
> Il servizio Voce ha sostituito l'API Riconoscimento vocale Bing e Traduzione vocale. Vedere la sezione _Migrazione_ per istruzioni relative alla migrazione.

Le funzionalità seguenti fanno parte del servizio Voce. Per altre informazioni sui casi d'uso comuni per ogni funzionalità, usare i collegamenti in questa tabella o esplorare la documentazione di riferimento delle API.

| Service | Funzionalità | Descrizione | SDK | REST |
|---------|---------|-------------|-----|------|
| [Riconoscimento vocale](speech-to-text.md) | Riconoscimento vocale in tempo reale | Il riconoscimento vocale trascrive o traduce in tempo reale flussi audio o file locali in testo da utilizzare o visualizzare in applicazioni, dispositivi o strumenti. È possibile usare il riconoscimento vocale con [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) per derivare le finalità dell'utente dal parlato trascritto e agire sui comandi vocali. | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Riconoscimento vocale in batch](batch-transcription.md) | Il riconoscimento vocale in batch consente la trascrizione asincrona in testo di una quantità elevata di dati audio vocali memorizzati in Archiviazione BLOB di Azure. Oltre a convertire l'audio vocale in testo, il riconoscimento vocale in batch consente anche di eseguire la diarizzazione e l'analisi del sentiment. | No | [Sì](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) |
| | [Conversazione per più dispositivi](multi-device-conversation.md) | Connessione di più dispositivi o client in una conversazione per l'invio di messaggi vocali o di testo, con il supporto semplificato per la trascrizione e la traduzione| Sì | No |
| | [Trascrizione conversazione](conversation-transcription-service.md) | Abilita il riconoscimento vocale in tempo reale, l'identificazione voce e la diarizzazione. È ideale per la trascrizione di riunioni dal vivo grazie alla capacità di distinguere i parlanti. | Sì | No |
| | [Creare modelli conversione voce/testo personalizzati](#customize-your-speech-experience) | Se si usa il riconoscimento vocale per il riconoscimento e la trascrizione in un ambiente univoco, è possibile creare ed eseguire il training di modelli acustici, linguistici e di pronuncia personalizzati destinati a un ambiente rumoroso o una terminologica specifica del settore. | No | [Sì](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) |
| [Sintesi vocale](text-to-speech.md) | Sintesi vocale | Sintesi vocale converte il testo di input in una voce sintetizzata simile a quella di un essere umano usando [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md). È possibile scegliere tra voci standard e voci neurali (vedere [Supporto per le lingue](language-support.md)). | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Creare voci personalizzate](#customize-your-speech-experience) | Creazione di caratteri voce personalizzati univoci per il proprio marchio o prodotto. | No | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Traduzione vocale](speech-translation.md) | Traduzione vocale | La traduzione vocale consente di attivare la traduzione vocale end-to-end in tempo reale e in più lingue in applicazioni, strumenti e dispositivi. È possibile usare questo servizio per il riconoscimento vocale e la traduzione vocale. | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | No |
| [Assistenti vocali](voice-assistants.md) | Assistenti vocali | Gli assistenti vocali che usano il servizio Voce consentono agli sviluppatori di creare interfacce di conversazione naturali e simili a quelle umane per applicazioni ed esperienze. Il servizio di assistente vocale offre un'interazione rapida e affidabile tra un dispositivo e l'implementazione di un assistente che usa il canale Direct Line Speech di Bot Framework o il servizio integrato Comandi personalizzati (anteprima) per completare le attività. | [Sì](voice-assistants.md) | No |
| [Riconoscimento del parlante](speaker-recognition-overview.md) | Identificazione e verifica voce | Il servizio Riconoscimento del parlante fornisce algoritmi che verificano e identificano i parlanti in base alle relative caratteristiche vocali univoche. Il servizio Riconoscimento del parlante consente di rispondere alla domanda "chi parla?" | Sì | [Sì](https://docs.microsoft.com/rest/api/speakerrecognition/) |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service-for-free"></a>Provare gratuitamente il Servizio di riconoscimento vocale

Per eseguire la procedura seguente sono necessari sia un account Microsoft che un account Azure. Se non si ha un account Microsoft, è possibile iscriversi per ottenerne uno gratuitamente nel [portale per gli account Microsoft](https://account.microsoft.com/account). Selezionare **Accedi con Microsoft** e quindi, quando viene chiesto di accedere, selezionare **Crea un account Microsoft**. Seguire i passaggi per creare e verificare il nuovo account Microsoft.

Una volta che si ha un account Microsoft, passare alla [pagina di iscrizione ad Azure](https://azure.microsoft.com/free/ai/), selezionare **Inizia gratuitamente**, quindi creare un nuovo account Azure usando l'account Microsoft. Ecco un video su [come iscriversi per ricevere un account Azure gratuito](https://www.youtube.com/watch?v=GWT2R1C_uUU).

> [!NOTE]
> Quando ci si iscrive per ricevere un account Azure gratuito, si ottiene un credito del servizio di 200 dollari che è possibile impiegare per una sottoscrizione a pagamento del servizio Voce, valida per un totale di 30 giorni. Quando il credito si esaurisce o scade al termine dei 30 giorni, i servizi di Azure vengono disabilitati. Per continuare a usare i servizi di Azure, è necessario aggiornare l'account. Per altre informazioni, vedere [Come aggiornare l'account Azure gratuito](https://docs.microsoft.com/azure/cost-management-billing/manage/upgrade-azure-subscription). 
>
> Il servizio Voce ha due livelli di servizio, ovvero gratuito (f0) e su sottoscrizione (s0), che presentano limitazioni e vantaggi diversi. Se si usa il livello di servizio gratuito e con volumi ridotti del servizio Voce, è possibile mantenere questa sottoscrizione gratuita anche dopo la scadenza della versione di valutazione gratuita o del credito di servizio. Per altre informazioni, vedere [Prezzi di Servizi cognitivi - Servizi Voce](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="create-the-azure-resource"></a>Creare la risorsa di Azure

Per aggiungere una risorsa del servizio vocale (gratuita o a pagamento) al proprio account Azure:

1. Accedere al [portale di Azure](https://portal.azure.com/) con il proprio account Microsoft.

1. Selezionare **Crea una risorsa** in alto a sinistra sul portale. Se l'opzione **Crea una risorsa** non è visualizzata, è sempre possibile trovarla selezionando il menu compresso nell'angolo superiore sinistro dello schermo.

1. Nella finestra **Nuovo** digitare "voce" nella casella di ricerca e premere INVIO.

1. Nei risultati della ricerca, selezionare **Voce**.

   ![Risultati della ricerca di voce](media/index/speech-search.png)

1. Selezionare **Crea**, quindi:

   - Assegnare un nome univoco alla nuova risorsa. Il nome consente di distinguere tra più sottoscrizioni collegate allo stesso servizio.
   - Scegliere la sottoscrizione di Azure a cui è associata la nuova risorsa per determinare le modalità di fatturazione. Ecco l'introduzione su [come creare una sottoscrizione di Azure](https://docs.microsoft.com/azure/cost-management-billing/manage/create-subscription#create-a-subscription-in-the-azure-portal) nel portale di Azure.
   - Scegliere l'[area](regions.md) in cui verrà usata la risorsa. Azure è una piattaforma cloud globale disponibile a livello generale in molte aree del mondo. Per ottenere prestazioni ottimali, selezionare l'area più vicina a quella in cui si risiede o in cui viene eseguita l'applicazione. La disponibilità del servizio Voce varia a seconda dell'area. Assicurarsi di creare la risorsa in un'area supportata. Vedere [Supporto dei servizi Voce in base all'area](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-to-text-text-to-speech-and-translation).
   - Scegliere un piano tariffario gratuito (F0) o a pagamento (S0). Per informazioni complete sulle quote di utilizzo e sui prezzi per ogni livello, selezionare **Visualizza i dettagli completi sui prezzi** oppure vedere i [prezzi dei servizi Voce](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/). Per informazioni sui limiti delle risorse, vedere [Limiti di Servizi cognitivi di Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cognitive-services-limits).
   - Creare un nuovo gruppo di risorse per questa sottoscrizione di riconoscimento vocale o assegnarla a un gruppo di risorse esistente. I gruppi di risorse consentono di mantenere organizzate le diverse sottoscrizioni di Azure.
   - Selezionare **Crea**. Si passerà alla pagina di panoramica della distribuzione e verranno visualizzati i messaggi sullo stato della distribuzione.  
<!--
> [!NOTE]
> You can create an unlimited number of standard-tier subscriptions in one or multiple regions. However, you can create only one free-tier subscription. Model deployments on the free tier that remain unused for 7 days will be decommissioned automatically.
-->
È necessario qualche secondo per distribuire la nuova risorsa del servizio Voce. 

### <a name="find-keys-and-region"></a>Trovare chiavi e area

Per trovare le chiavi e l'area di una distribuzione completata, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/) con il proprio account Microsoft.

2. Selezionare **Tutte le risorse** e quindi il nome della risorsa dei Servizi cognitivi.

3. Nel riquadro a sinistra, in **GESTIONE RISORSE**, selezionare **Chiavi ed endpoint**.

Ogni sottoscrizione dispone di due chiavi, entrambi utilizzabili nell'applicazione. Per copiare e incollare una chiave nell'editor di codice o in un'altra posizione, selezionare il pulsante Copia accanto a ogni chiave e cambiare finestra per incollare il contenuto degli Appunti nella posizione desiderata.

Inoltre, copiare il valore `LOCATION`, che corrisponde all'ID dell'area (ad esempio, `westus`, `westeurope`) per le chiamate SDK.

> [!IMPORTANT]
> Queste chiavi di sottoscrizione vengono usate per accedere all'API di Servizi cognitivi. Non condividerle. Archiviarle in una posizione sicura, ad esempio usando Azure Key Vault. È inoltre consigliabile rigenerare queste chiavi regolarmente. Per effettuare una chiamata API è necessaria una sola chiave. Quando si rigenera la prima chiave, è possibile usare la seconda chiave per l'accesso continuato al servizio.

## <a name="complete-a-quickstart"></a>Completare una guida di avvio rapido

Sono disponibili guide di avvio rapido nei linguaggi di programmazione più diffusi, ognuna progettata per insegnare gli schemi progettuali di base e consentire all'utente di eseguire il codice in meno di 10 minuti. Per accedere alla guida di avvio rapido per ogni funzionalità, vedere l'elenco seguente.

* [Guida di avvio rapido sul riconoscimento vocale](get-started-speech-to-text.md)
* [Guida di avvio rapido sulla sintesi vocale](get-started-text-to-speech.md)
* [Guida di avvio rapido sulla traduzione vocale](speech-translation-basics.md)
* [Guida di avvio rapido sul riconoscimento delle finalità](quickstarts/intent-recognition.md)
* [Guida di avvio rapido sul riconoscimento del parlante](speaker-recognition-basics.md)

Dopo avere avuto la possibilità di familiarizzare con il servizio Voce, provare le esercitazioni che illustrano come risolvere diversi scenari.

- [Esercitazione: Riconoscere le finalità dai contenuti vocali con Speech SDK e LUIS per C#](how-to-recognize-intents-from-speech-csharp.md)
- [Esercitazione: Abilitare il servizio voce per il bot con Speech SDK, C#](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Esercitazione: Creare un'app Flask per tradurre testo, analizzare sentiment e sintetizzare il testo tradotto in parlato, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Ottenere il codice di esempio

Il codice di esempio per il servizio Voce è disponibile in GitHub. Questi esempi esaminano gli scenari comuni, ad esempio la lettura di audio da un file o streaming, il riconoscimento continuo e singolo e l'uso di modelli personalizzati. Usare i collegamenti seguenti per visualizzare esempi SDK e REST:

- [Esempi di riconoscimento vocale, sintesi vocale e traduzione vocale (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Batch transcription samples (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch) (Esempi di trascrizione batch (REST))
- [Text-to-speech samples (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS) (Esempi di sintesi vocale (REST))
- [Voice assistant samples (SDK)](https://aka.ms/csspeech/samples) (Esempi di assistente vocale - SDK)

## <a name="customize-your-speech-experience"></a>Personalizzare l'esperienza di riconoscimento vocale

Il servizio Voce funziona bene con i modelli predefiniti, tuttavia, è possibile personalizzare e ottimizzare ulteriormente l'esperienza in base al prodotto o all'ambiente in uso. Le opzioni di personalizzazione spaziano dall'ottimizzazione del modello acustico ai caratteri voce univoci per il proprio marchio.

Altri prodotti offrono modelli di conversione voce/testo ottimizzati per scopi specifici, ad esempio per il settore della sanità o delle assicurazioni, ma sono disponibili per tutti ugualmente. La personalizzazione nel servizio Voce di Azure diventa parte integrante del vantaggio competitivo *esclusivo* dell'utente, non disponibile per altri utenti o clienti. In altre parole, i modelli di un cliente sono privati e personalizzati solo per il suo caso d'uso.

| Servizio di riconoscimento vocale | Piattaforma | Descrizione |
| -------------- | -------- | ----------- |
| Riconoscimento vocale | [Riconoscimento vocale personalizzato](https://aka.ms/customspeech) | Personalizzare i modelli di riconoscimento vocale in base alle esigenze e ai dati disponibili. Superare gli ostacoli al riconoscimento vocale, come il modo di parlare, il vocabolario e il rumore di fondo. |
| Sintesi vocale | [Voce personalizzata](https://aka.ms/customvoice) | Creare un'unica voce riconoscibile per le app di sintesi vocale con i dati della lingua disponibili. È possibile ottimizzare ulteriormente gli output vocali regolando un set di parametri vocali. |

## <a name="reference-docs"></a>Documentazione di riferimento

- [Speech SDK](speech-sdk-reference.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [API REST: Riconoscimento vocale](rest-speech-to-text.md)
- [API REST: Sintesi vocale](rest-text-to-speech.md)
- [API REST: Trascrizione e personalizzazione batch](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione al riconoscimento vocale](speech-to-text-basics.md)
> [Introduzione alla sintesi vocale](get-started-text-to-speech.md)
