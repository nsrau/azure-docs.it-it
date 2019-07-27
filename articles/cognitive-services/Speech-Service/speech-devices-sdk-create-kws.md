---
title: Creare un servizio di riconoscimento vocale di riattivazione personalizzato
titleSuffix: Azure Cognitive Services
description: Il dispositivo è sempre in ascolto di una parola (o frase) di attivazione. Quando l'utente pronuncia la parola di attivazione, il dispositivo invia tutto l'audio successivo al cloud fino a quando l'utente non smette di parlare. Modificare la parola di attivazione è un modo efficace per differenziare il dispositivo e accrescere la personalizzazione.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 2bc1a6cbbf1e0d790326849a41b0788e332daa31
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553109"
---
# <a name="create-a-custom-wake-word-by-using-the-speech-service"></a>Creare una parola di attivazione personalizzata usando il servizio Voce

Il dispositivo è sempre in ascolto di una parola (o frase) di attivazione. Ad esempio, "Ehi Cortana" è una parola di attivazione per l'assistente Cortana. Quando l'utente pronuncia la parola di attivazione, il dispositivo invia tutto l'audio successivo al cloud fino a quando l'utente non smette di parlare. Modificare la parola di attivazione è un modo efficace per differenziare il dispositivo e accrescere la personalizzazione.

In questo articolo viene illustrato come creare una parola di attivazione personalizzata per il dispositivo.

## <a name="choose-an-effective-wake-word"></a>Scegliere una parola di attivazione efficace

Quando si sceglie una parola di attivazione, tenere presente le linee guida seguenti:

* La parola di attivazione deve essere una parola o una frase in lingua italiana. Per pronunciarla, non devono servire più di due secondi.

* Le parole composte da 4-7 sillabe sono ottimali. "Ehi, computer", ad esempio, è una parola di attivazione appropriata. "Ehi" è una parola inappropriata.

* Le parole di attivazione devono seguire le comuni regole di pronuncia italiana.

* Una parola particolare o addirittura inventata che segue le normali regole di pronuncia italiana potrebbe ridurre i falsi positivi. Ad esempio, "computerama" potrebbe essere una parola di attivazione valida.

* Non scegliere una parola comune. Ad esempio, "mangiare" e "andare" sono parole che le persone ripetono spesso parlando normalmente e potrebbero essere falsi trigger per il dispositivo.

* Evitare di usare una parola di attivazione che potrebbe essere pronunciata in modi diversi. Gli utenti dovranno conoscere la pronuncia "corretta" per fare in modo che il dispositivo risponda. Ad esempio, "509" può essere pronunciato "cinquecentonove", "cinque zero nove" o "cinquecento nove". "U.F.O." può essere pronunciato "U-F-O" oppure "ufo". "Ancora" può essere pronunciata "àncora" o "ancóra".

* Non usare caratteri speciali, simboli o cifre. Ad esempio, "Vai€" e "20 + gatti" non sono parole di attivazione adatte, mentre "vai euro" o "venti più gatti" possono andare bene. È comunque possibile usare i simboli nella personalizzazione e usare il marketing e la documentazione per ribadire la pronuncia corretta.

> [!NOTE]
> Se si sceglie un marchio come parola di attivazione, assicurarsi di essere il proprietario di tale marchio o di avere l'autorizzazione del proprietario del marchio per usare tale parola. Microsoft non è responsabile di eventuali problemi legali che potrebbero emergere dalla scelta della parola di attivazione.

## <a name="create-your-wake-word"></a>Creare la parola di attivazione

Prima di poter usare una parola di riattivazione personalizzata con il dispositivo, sarà necessario creare una parola di riattivazione con il servizio di generazione di Word di riattivazione personalizzata Microsoft. Dopo aver fornito una parola di riattivazione, il servizio produce un file che viene distribuito al kit di sviluppo per abilitare la parola di riattivazione nel dispositivo.

1. Passare al [portale del servizio riconoscimento vocale personalizzato](https://aka.ms/sdsdk-speechportal) e **accedere** oppure, se non si ha una sottoscrizione vocale, scegliere [**Crea una sottoscrizione**](https://go.microsoft.com/fwlink/?linkid=2086754)

    ![Portale del servizio Riconoscimento vocale personalizzato](media/speech-devices-sdk/wake-word-4.png)

1. Nella pagina di riattivazione [personalizzata](https://aka.ms/sdsdk-wakewordportal) Digitare la parola di riattivazione desiderata e fare clic su **Aggiungi parola**di riattivazione. Sono disponibili alcune [linee guida](#choose-an-effective-wake-word) che consentono di scegliere una parola chiave efficace. Attualmente è supportato solo il linguaggio en-US.

    ![Immettere la parola di attivazione](media/speech-devices-sdk/wake-word-5.png)

1. Verranno create tre pronunce alternative della parola di riattivazione. È possibile scegliere tutte le pronunce desiderate. Quindi selezionare **Submit (Invia** ) per generare la parola di riattivazione. Per modificare la parola di riattivazione, rimuovere prima di tutto quella esistente, quando si passa il mouse sulla riga di pronuncia, viene visualizzata l'icona di eliminazione.

    ![Esaminare la parola di riattivazione](media/speech-devices-sdk/wake-word-6.png)

1. La generazione del modello potrebbe richiedere fino a un minuto. Verrà richiesto di scaricare il file.

    ![Scaricare la parola di riattivazione](media/speech-devices-sdk/wake-word-7.png)

1. Salvare il file ZIP nel computer. Questo file sarà necessario per distribuire la parola di riattivazione personalizzata al kit di sviluppo.

## <a name="next-steps"></a>Passaggi successivi

Testare la Guida introduttiva all'SDK della parola di riattivazione personalizzata con [dispositivi vocali](https://aka.ms/sdsdk-quickstart).
