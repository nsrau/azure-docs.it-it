---
title: Creare una parola di attivazione personalizzata - Servizi di riconoscimento vocale
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
ms.openlocfilehash: f43cbedc633b26a3a7fcbfb5f6a75da514bf0c45
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604876"
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

Prima di utilizzare una parola riattivazione personalizzata con il dispositivo, è necessario creare una parola di riattivazione con il servizio Microsoft personalizzato riattivazione generazione di parole. Dopo aver specificato una parola di riattivazione, il servizio genera un file che viene distribuito il kit di sviluppo per abilitare la riattivazione word nel dispositivo.

1. Andare alla [portale del servizio riconoscimento vocale personalizzato](https://aka.ms/sdsdk-speechportal) e **Accedi** o se non hai una sottoscrizione di riconoscimento vocale Scegli [ **creare una sottoscrizione**](https://go.microsoft.com/fwlink/?linkid=2086754)

    ![Il portale di servizi di riconoscimento vocale personalizzato](media/speech-devices-sdk/wake-word-4.png)

1. Nel [Custom riattivazione Word](https://aka.ms/sdsdk-wakewordportal) pagina digitare la parola di riattivazione scelta e fare clic su **Add riattivazione word**. Alcune [linee guida](#choose-an-effective-wake-word) per scegliere una parola chiave valida. Attualmente sono supportate solo lingua en-US.

    ![Immettere la parola di attivazione](media/speech-devices-sdk/wake-word-5.png)

1. Verrà creata tre alternativa pronuncia della parola di riattivazione. È possibile scegliere tutte le pronunce desiderato. Quindi selezionare **Submit** per generare la parola di riattivazione. Se si desidera modificare la parola di riattivazione, rimuovere quella esistente prima di tutto, quando passa il mouse sulla riga pronuncia che verrà visualizzata l'icona di eliminazione.

    ![Esaminare la parola di riattivazione](media/speech-devices-sdk/wake-word-6.png)

1. Potrebbe richiedere fino a un minuto per il modello da generare. Verrà richiesto di scaricare il file.

    ![Download delle parole di riattivazione](media/speech-devices-sdk/wake-word-7.png)

1. Salvare il file ZIP nel computer. È necessario questo file per la distribuzione delle parole riattivazione personalizzato al kit di sviluppo.

## <a name="next-steps"></a>Passaggi successivi

Test delle parole riattivazione personalizzati con [Guida introduttiva di riconoscimento vocale dispositivi SDK](https://aka.ms/sdsdk-quickstart).
