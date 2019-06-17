---
title: Trascrizione conversazione - servizi di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: La trascrizione di conversazione è una funzionalità avanzata dei servizi di riconoscimento vocale che combina il riconoscimento vocale in tempo reale, identificazione voce e diarization. Trascrizione di conversazione è perfetta per la trascrizione riunioni di persona, con la possibilità di distinguere gli altoparlanti, consente di sapere chi ha detto what e when, che consente ai partecipanti di concentrarsi sulla riunione rapidamente completamento sui passaggi successivi. Questa funzionalità migliora anche l'accessibilità. Con la trascrizione, è possibile coinvolgere attivamente i partecipanti con problemi di udito.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: a31921e229a4bbfccd6fdd871666aad1eeef3232
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66243855"
---
# <a name="what-is-conversation-transcription"></a>Che cos'è la trascrizione di conversazione?

La trascrizione di conversazione è una funzionalità avanzata dei servizi di riconoscimento vocale che combina il riconoscimento vocale in tempo reale, identificazione voce e diarization. Trascrizione di conversazione è perfetta per la trascrizione riunioni di persona, con la possibilità di distinguere gli altoparlanti, consente di sapere chi ha detto what e when, che consente ai partecipanti di concentrarsi sulla riunione rapidamente completamento sui passaggi successivi. Questa funzionalità migliora anche l'accessibilità. Con la trascrizione, è possibile coinvolgere attivamente i partecipanti con problemi di udito.   

Trascrizione conversazione offre riconoscimento accurato con modelli di riconoscimento vocale personalizzabile che è possibile personalizzare per comprendere il settore di fondo e vocabolario specifiche della società. Inoltre, è possibile abbinare la trascrizione di conversazione con il SDK di dispositivi vocale per ottimizzare l'esperienza per i dispositivi più microfono.

>[!NOTE]
> Attualmente, la trascrizione di conversazione è consigliata per piccole riunioni. Se vuoi estendere la trascrizione di conversazione per le riunioni di grandi dimensioni su larga scala, contattaci.

Questo diagramma illustra l'hardware, software e servizi che interagiscono con la trascrizione di conversazione.

![Il diagramma di trascrizione di conversazione di importazione](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> Una matrice di sette microfono circolare con configurazione specifica geometria è obbligatoria. Per informazioni dettagliate specifiche e la progettazione, vedere [microfono SDK di Microsoft Speech dispositivo](https://aka.ms/cts/microphone). Per altre informazioni o si acquista un kit di sviluppo, vedere [ottenere Microsoft Speech Device SDK](https://aka.ms/cts/getsdk).

## <a name="get-started-with-conversation-transcription"></a>Introduzione a conversazione trascrizione

Esistono tre passaggi che è necessario eseguire per iniziare a usare la trascrizione di conversazione.

1. Raccogliere esempi vocali dagli utenti.
2. Generare i profili utente utilizzando gli esempi di utente vocale
3. Usare il SDK di riconoscimento vocale per identificare gli utenti (relatori) e trascrizione vocale

## <a name="collect-user-voice-samples"></a>Raccogliere gli esempi di utente vocale

Il primo passaggio è raccogliere registrazioni audio da ogni utente. Riconoscimento vocale utente deve essere registrati in un ambiente non interattiva senza radiazione di fondo. La lunghezza consigliata per ogni campione audio è compreso tra 30 secondi e due minuti. Campioni audio più lungo comporterà una maggiore precisione quando si identificano gli altoparlanti. Audio deve essere un canale mono con una frequenza di campionamento KHz 16.

Oltre alla Guida menzionati in precedenza come audio verrà registrato e archiviati dipende dall'utente, è consigliabile un database protetto. Nella sezione successiva, verranno esaminate come questo audio viene utilizzato per generare i profili utente che vengono usati con Speech SDK per riconoscere gli altoparlanti.

## <a name="generate-user-profiles"></a>Generare i profili utente mobili

Successivamente, è necessario inviare le registrazioni audio è stato raccolto per il servizio di generazione di firma per convalidare l'audio e generare i profili utente. Il [servizio di generazione firma](https://aka.ms/cts/signaturegenservice) è un set di API REST, che consentono di generare e recuperare i profili utente.

Per creare un profilo utente, è necessario usare il `GenerateVoiceSignature` API. Sono disponibili i dettagli della specifica e codice di esempio:

> [!NOTE]
> La trascrizione di conversazione è attualmente disponibile in "en-US" e "zh-CN" nelle aree seguenti: `centralus` e `eastasia`.

* [Specifica di REST](https://aka.ms/cts/signaturegenservice)
* [Come usare la trascrizione di conversazione](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>Trascrivi e identificare i parlanti

Trascrizione di conversazione si aspetta che i flussi audio multicanale e profili utente mobili come input per generare trascrizioni e identificare i parlanti. I dati del profilo utente e audio vengono inviati al servizio di conversazione trascrizione usando il SDK di dispositivi di riconoscimento vocale. Come accennato in precedenza, una matrice di sette microfono circolare e i dispositivi Speech SDK devono utilizzare la trascrizione di conversazione.

>[!NOTE]
> Per informazioni dettagliate specifiche e la progettazione, vedere [microfono SDK di Microsoft Speech dispositivo](https://aka.ms/cts/microphone). Per altre informazioni o si acquista un kit di sviluppo, vedere [ottenere Microsoft Speech Device SDK](https://aka.ms/cts/getsdk).

Per informazioni su come usare la trascrizione di conversazione con il riconoscimento vocale Devices SDK, vedere [come usare la trascrizione di conversazione](https://aka.ms/cts/howto).


## <a name="quick-start-with-a-sample-app"></a>Avvio rapido con un'app di esempio

SDK per dispositivi vocale Microsoft dispone di un'app di esempio di avvio rapido per tutti i dispositivi esempi correlati. La trascrizione di conversazione è uno di essi. È possibile trovarlo nel [Speech SDK per dispositivi android: Guida introduttiva](https://aka.ms/sdsdk-quickstart) con app di esempio e il relativo codice sorgente per riferimento.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni sui dispositivi Speech SDK](speech-devices-sdk.md)
