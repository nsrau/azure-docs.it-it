---
title: Trascrizione conversazione-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: La trascrizione delle conversazioni è una funzionalità avanzata dei servizi di sintesi vocale che combinano il riconoscimento vocale in tempo reale, l'identificazione del relatore e la paginazione. La trascrizione delle conversazioni è ideale per la traduzione di riunioni di persona, con la possibilità di distinguere gli oratori, consente di sapere chi ha detto cosa e quando consentire ai partecipanti di concentrarsi sulla riunione e di seguire rapidamente i passaggi successivi. Questa funzionalità migliora inoltre l'accessibilità. Con la trascrizione, è possibile coinvolgere attivamente i partecipanti con problemi di udito.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: acbf82335523ee226f4b899180f0f22cb94cca5e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562880"
---
# <a name="what-is-conversation-transcription"></a>Che cos'è la trascrizione delle conversazioni?

La trascrizione delle conversazioni è una funzionalità avanzata dei servizi di sintesi vocale che combinano il riconoscimento vocale in tempo reale, l'identificazione del relatore e la paginazione. La trascrizione delle conversazioni è ideale per la traduzione di riunioni di persona, con la possibilità di distinguere gli oratori, consente di sapere chi ha detto cosa e quando consentire ai partecipanti di concentrarsi sulla riunione e di seguire rapidamente i passaggi successivi. Questa funzionalità migliora inoltre l'accessibilità. Con la trascrizione, è possibile coinvolgere attivamente i partecipanti con problemi di udito.   

La trascrizione delle conversazioni offre un riconoscimento accurato con modelli di riconoscimento vocale personalizzabili che è possibile personalizzare per comprendere il vocabolario specifico del settore e dell'azienda. Inoltre, è possibile associare la trascrizione delle conversazioni con l'SDK per i dispositivi vocali per ottimizzare l'esperienza per i dispositivi multimicrofono.

>[!NOTE]
> Attualmente, la trascrizione delle conversazioni è consigliata per riunioni di piccole dimensioni. Per estendere la trascrizione delle conversazioni per riunioni di grandi dimensioni su larga scala, contattaci.

Questo diagramma illustra l'hardware, il software e i servizi che interagiscono con la trascrizione delle conversazioni.

![Diagramma di trascrizione della conversazione di importazione](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> È necessaria una matrice circolare di sette microfoni con una specifica configurazione di geometria. Per informazioni dettagliate e di progettazione, vedere [microfono per dispositivi Microsoft Speech SDK](https://aka.ms/cts/microphone). Per ulteriori informazioni o per acquistare un kit di sviluppo, vedere la pagina relativa a come [ottenere Microsoft Speech Device SDK](https://aka.ms/cts/getsdk).

## <a name="get-started-with-conversation-transcription"></a>Inizia a usare la trascrizione delle conversazioni

Per iniziare a usare la trascrizione delle conversazioni, è necessario eseguire tre passaggi.

1. Raccogliere gli esempi vocali dagli utenti.
2. Generare profili utente usando gli esempi di User Voice
3. Usare l'SDK vocale per identificare gli utenti (speaker) e la traduzione vocale

## <a name="collect-user-voice-samples"></a>Raccogli esempi di voce utente

Il primo passaggio consiste nella raccolta di registrazioni audio da ogni utente. Il riconoscimento vocale dell'utente deve essere registrato in un ambiente silenzioso senza rumore di fondo. La lunghezza consigliata per ogni campione audio è compresa tra 30 secondi e due minuti. Gli esempi audio più lunghi comporteranno una maggiore accuratezza quando si identificano gli altoparlanti. L'audio deve essere mono Channel con una frequenza di campionamento di 16 KHz.

Oltre alle indicazioni sopra riportate, il modo in cui l'audio viene registrato e archiviato è la scelta di un database protetto. Nella sezione successiva verrà esaminato il modo in cui questo audio viene utilizzato per generare i profili utente utilizzati con l'SDK di riconoscimento vocale per riconoscere i relatori.

## <a name="generate-user-profiles"></a>Genera profili utente

Successivamente, sarà necessario inviare le registrazioni audio raccolte al servizio di generazione della firma per convalidare l'audio e generare i profili utente. Il [servizio di generazione della firma](https://aka.ms/cts/signaturegenservice) è un set di API REST che consentono di generare e recuperare i profili utente.

Per creare un profilo utente, è necessario usare l' `GenerateVoiceSignature` API. Sono disponibili i dettagli della specifica e il codice di esempio:

> [!NOTE]
> La trascrizione delle conversazioni è attualmente disponibile in "en-US" e "zh-CN" nelle aree `centralus` seguenti `eastasia`: e.

* [Specifica REST](https://aka.ms/cts/signaturegenservice)
* [Come usare la trascrizione delle conversazioni](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>Trascrizione e identificazione degli altoparlanti

La trascrizione della conversazione prevede flussi audio e profili utente multicanale come input per generare trascrizioni e identificare gli speaker. I dati relativi ai profili audio e utente vengono inviati al servizio di trascrizione delle conversazioni usando l'SDK per dispositivi vocali. Come indicato in precedenza, per usare la trascrizione delle conversazioni è necessario disporre di sette microfoni circolari e dell'SDK per dispositivi vocali.

>[!NOTE]
> Per informazioni dettagliate e di progettazione, vedere [microfono per dispositivi Microsoft Speech SDK](https://aka.ms/cts/microphone). Per ulteriori informazioni o per acquistare un kit di sviluppo, vedere la pagina relativa a come [ottenere Microsoft Speech Device SDK](https://aka.ms/cts/getsdk).

Per informazioni su come usare la trascrizione delle conversazioni con l'SDK per i dispositivi vocali, vedere [come usare](https://aka.ms/cts/howto)la trascrizione delle conversazioni.


## <a name="quick-start-with-a-sample-app"></a>Avvio rapido con un'app di esempio

Microsoft Speech Device SDK include un'app di esempio di avvio rapido per tutti gli esempi relativi ai dispositivi. La trascrizione delle conversazioni è uno di essi. È possibile trovarlo nella [Guida introduttiva per Android Device SDK](https://aka.ms/sdsdk-quickstart) con l'app di esempio e il relativo codice sorgente per il riferimento.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni sull'SDK dei dispositivi vocali](speech-devices-sdk.md)
