---
title: Informazioni sul Servizio di riconoscimento vocale (anteprima)
description: 'Il servizio Voce, parte dei Servizi Cognitivi Microsoft, unisce diversi servizi vocali di Azure precedentemente disponibili in modo separato: Riconoscimento vocale Bing (che comprende il riconoscimento vocale e la sintesi vocale), Riconoscimento vocale personalizzato e Traduzione vocale.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 60ff2f71766a14af17ebb1cb9d20825976471296
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/17/2018
ms.locfileid: "41929859"
---
# <a name="what-is-the-speech-service-preview"></a>Informazioni sul Servizio di riconoscimento vocale (anteprima)

Con una sola sottoscrizione, il servizio Voce offre agli sviluppatori un modo semplice per aggiungere alle loro applicazioni potenti funzioni abilitate per la sintesi vocale. Le applicazioni possono ora offrire comandi vocali, trascrizione, dettatura, sintesi vocale e traduzione vocale.

Il servizio Voce si basa sulle tecnologie utilizzate in altri prodotti Microsoft, tra cui Cortana e Microsoft Office.

> [!NOTE]
> Il servizio Voce è attualmente disponibile in anteprima pubblica. Si consiglia di consultare regolarmente questa pagina per aggiornamenti della documentazione, esempi di codici aggiuntivi e altro.

## <a name="speech-service-features"></a>Funzionalità del servizio Voce

|Funzione|DESCRIZIONE|
|-|-|
|[Riconoscimento vocale](speech-to-text.md)| Trascrive i flussi audio in testo che l'applicazione può accettare come input. Si integra anche con il servizio [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) per captare la finalità dell'utente dalle parole.|
|[Sintesi vocale](text-to-speech.md)| Converte il testo normale in parlato dal suono naturale che viene trasmesso all'applicazione in un file audio. Sono disponibili più voci, che variano per genere o accento, per molte lingue supportate. |
|[Traduzione vocale](speech-translation.md)| Può essere usata per traslare i flussi audio in tempo quasi reale o elaborare la voce registrata. |
|Riconoscimento vocale personalizzato|È possibile personalizzare il riconoscimento vocale creando i propri modelli [acustici](how-to-customize-acoustic-models.md) e di [lingua](how-to-customize-language-model.md) e specificando regole di [pronuncia](how-to-customize-pronunciation.md) personalizzate. |
|[Sintesi vocale personalizzata](how-to-customize-voice-font.md)|È possibile creare le proprie voci per la sintesi vocale.|
|[Speech Devices SDK](speech-devices-sdk.md)| Con l'introduzione del Servizio di riconoscimento vocale unificato, Microsoft e i suoi partner offrono una piattaforma hardware/software integrata ottimizzata per lo sviluppo di dispositivi con funzioni vocali |

## <a name="access-to-the-speech-service"></a>Accesso al Servizio di riconoscimento vocale

Il servizio di riconoscimento vocale viene reso disponibile in due modi. L’[SDK](speech-sdk.md) estrae i protocolli di rete per facilitare lo sviluppo sulle piattaforme supportate.
 L’[API REST](rest-apis.md) funziona con qualsiasi linguaggio di programmazione, ma non offre tutte le funzioni offerte dall’SDK.

|<br>Metodo|Sintesi vocale<br>Riconoscimento vocale|Sintesi vocale<br>Sintesi vocale|Sintesi vocale<br>Traduzione|<br>DESCRIZIONE|
|-|-|-|-|-|
|[SDK](speech-sdk.md)|Yes|No |Yes|Librerie per determinati linguaggi di programmazione che usano un protocollo basato su Websocket che semplifica lo sviluppo.|
|[REST](rest-apis.md)|Yes|Yes|No |Un'API semplice basato su HTTP che rende più facile aggiungere comandi vocali all'applicazione.|

## <a name="speech-scenarios"></a>Scenari di riconoscimento vocale

Alcuni usi comuni della tecnologia di riconoscimento vocale vengono illustrati brevemente di seguito. [Speech SDK](speech-sdk.md) è fondamentale per la maggior parte di questi scenari.

> [!div class="checklist"]
> * Creare app con attivazione vocale
> * Trascrivere registrazioni dei call center
> * Implementare i bot vocali

### <a name="voice-triggered-apps"></a>App con attivazione vocale

L’input vocale è un ottimo modo per rendere l'app flessibile, da utilizzare a mani libere e in modo rapido. In un'app con il riconoscimento vocale, gli utenti possono semplicemente richiedere le informazioni desiderate, senza bisogno di navigare facendo clic o toccando il dispositivo.

Se l'app è destinata al pubblico generale, è possibile usare il modello di riconoscimento vocale di base fornito dal servizio di riconoscimento vocale. Il riconoscimento vocale riconosce un'ampia gamma di parlanti in ambienti tipici.

Se l'app è destinata a un dominio specifico (ad esempio, medicina o IT), è possibile creare un [modello di lingua](how-to-customize-language-model.md) per insegnare al servizio di riconoscimento vocale la terminologia speciale usata dall'app.

Se l'app è destinata a un ambiente rumoroso, come una fabbrica, è possibile creare un [modello acustico](how-to-customize-acoustic-models.md) personalizzato per consentire al servizio di riconoscimento vocale di distinguere meglio la voce dal rumore.

Iniziare è facile, basta scaricare [Speech SDK](speech-sdk.md) e seguire il relativo articolo della [guida introduttiva](quickstart-csharp-dotnet-windows.md).

### <a name="transcribe-call-center-recordings"></a>Trascrivere registrazioni dei call center

Spesso, le registrazioni del call center vengono consultate solo se si verifica un problema con una chiamata. Con il servizio di riconoscimento vocale, è facile trascrivere ogni registrazione in testo. Una volta trascritte in testo, è possibile indicizzarle facilmente per la [ricerca full-text](https://docs.microsoft.com/azure/search/search-what-is-azure-search) oppure applicare l’[Analisi del testo](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/) per rilevare sentimenti, lingua e frasi chiave.

Se le registrazioni del call center contengono terminologia specializzata (ad esempio i nomi di prodotto o gergo IT), è possibile creare un [modello di lingua](how-to-customize-language-model.md) per insegnare al servizio di riconoscimento vocale quel vocabolario. Un [modello acustico](how-to-customize-acoustic-models.md) personalizzato aiuta il servizio di riconoscimento vocale a comprendere le connessioni del telefono non ottimali.

Per altre informazioni su questo scenario, vedere [trascrizione batch](batch-transcription.md) con il servizio riconoscimento vocale.

### <a name="voice-bots"></a>Bot vocali

I [bot](https://dev.botframework.com/) sono un modo sempre più diffuso per connettere gli utenti con le informazioni desiderate e i clienti con le aziende che preferiscono. L'aggiunta di un'interfaccia utente discorsiva per il sito Web o per l’app rende le funzionalità più semplici da trovare e l’accesso più rapido. Con il servizio di riconoscimento vocale, la conversazione assume una nuova dimensione di fluidità rispondendo effettivamente alle query pronunciate con la sintesi vocale.

Per aggiungere una personalità univoca al tuo bot di riconoscimento vocale (e potenziare il tuo marchio), è possibile assegnargli una voce propria. La creazione di una voce personalizzata è un processo in due passaggi. Prima di tutto, [registrare](record-custom-voice-samples.md) la voce da usare. Quindi, [inviare le registrazioni](how-to-customize-voice-font.md) (insieme a una trascrizione di testo) al [portale personalizzazione vocale](https://cris.ai/Home/CustomVoice) del servizio di riconoscimento vocale, che fa il resto. Dopo aver creato la voce personalizzata, è semplice utilizzarla nell'app.

## <a name="next-steps"></a>Passaggi successivi

Ottieni una chiave di sottoscrizione per il servizio di riconoscimento vocale.

> [!div class="nextstepaction"]
> [Provare gratuitamente il Servizio di riconoscimento vocale](get-started.md)
