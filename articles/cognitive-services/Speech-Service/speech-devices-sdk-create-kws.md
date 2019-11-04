---
title: Creare una parola chiave personalizzata-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Il dispositivo è sempre in ascolto di una parola chiave (o frase). Quando l'utente dice la parola chiave, il dispositivo invia tutti i file audio successivi al cloud, fino a quando l'utente non smette di parlare. Personalizzare la parola chiave è un modo efficace per distinguere il dispositivo e rafforzare la personalizzazione.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: bf9afb66163532b4095e0d30b1167010320abbf8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490926"
---
# <a name="create-a-custom-keyword-by-using-the-speech-service"></a>Creare una parola chiave personalizzata usando il servizio di riconoscimento vocale

Il dispositivo è sempre in ascolto di una parola chiave (o frase). Ad esempio, "Hey Cortana" è una parola chiave per Cortana Assistant. Quando l'utente dice la parola chiave, il dispositivo invia tutti i file audio successivi al cloud, fino a quando l'utente non smette di parlare. Personalizzare la parola chiave è un modo efficace per distinguere il dispositivo e rafforzare la personalizzazione.

Questo articolo illustra come creare una parola chiave personalizzata per il dispositivo.

## <a name="choose-an-effective-keyword"></a>Scegliere una parola chiave valida

Quando si sceglie una parola chiave, tenere presenti le linee guida seguenti:

* La parola chiave deve essere una parola inglese o una frase. Per pronunciarla, non devono servire più di due secondi.

* Le parole composte da 4-7 sillabe sono ottimali. Ad esempio, "Hey, computer" è una parola chiave corretta. "Ehi" è una parola inappropriata.

* Le parole chiave devono seguire le regole comuni di pronuncia della lingua inglese.

* Una parola particolare o addirittura inventata che segue le normali regole di pronuncia italiana potrebbe ridurre i falsi positivi. Ad esempio, "computerama" potrebbe essere una parola chiave corretta.

* Non scegliere una parola comune. Ad esempio, "mangiare" e "andare" sono parole che le persone ripetono spesso parlando normalmente e potrebbero essere falsi trigger per il dispositivo.

* Evitare di usare una parola chiave che potrebbe avere pronunce alternative. Gli utenti dovranno conoscere la pronuncia "corretta" per fare in modo che il dispositivo risponda. Ad esempio, "509" può essere pronunciato "cinquecentonove", "cinque zero nove" o "cinquecento nove". "U.F.O." può essere pronunciato "U-F-O" oppure "ufo". "Ancora" può essere pronunciata "àncora" o "ancóra".

* Non usare caratteri speciali, simboli o cifre. Ad esempio, "go #" e "20 + Cats" non sono parole chiave valide. mentre "vai euro" o "venti più gatti" possono andare bene. È comunque possibile usare i simboli nella personalizzazione e usare il marketing e la documentazione per ribadire la pronuncia corretta.

> [!NOTE]
> Se si sceglie una parola con marchio come parola chiave, assicurarsi di essere proprietari del marchio o di avere l'autorizzazione del proprietario del marchio a usare la parola. Microsoft non è responsabile di eventuali problemi legali che potrebbero derivare dalla scelta della parola chiave.

## <a name="create-your-keyword"></a>Crea la parola chiave

Prima di poter usare una parola chiave personalizzata con il dispositivo, è necessario creare una parola chiave con il servizio di generazione di parole chiave personalizzato di Microsoft. Dopo aver fornito una parola chiave, il servizio produce un file che viene distribuito al kit di sviluppo per abilitare la parola chiave nel dispositivo.

1. Passare a [speech studio](https://aka.ms/sdsdk-speechportal) e **accedere** oppure, se non si dispone ancora di una sottoscrizione vocale, scegliere [**Crea una sottoscrizione**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Nella pagina della [parola chiave Custom](https://aka.ms/sdsdk-wakewordportal) Digitare la parola chiave scelta e fare clic su **Aggiungi parola chiave**. Sono disponibili alcune [linee guida](#choose-an-effective-keyword) che consentono di scegliere una parola chiave efficace. Il supporto è attualmente limitato al linguaggio en-US.

    ![Immettere la parola chiave](media/speech-devices-sdk/custom-kws-portal-enter-keyword.png)

1. Il portale creerà ora le pronunce candidate per la parola chiave. Ascoltare ogni candidato facendo clic sui pulsanti di riproduzione e rimuovere i controlli accanto a tutte le pronunce non corrette. Una volta selezionate solo le pronunce valide, selezionare **Submit (Invia** ) per iniziare a generare la parola chiave. Se si desidera modificare la parola chiave, rimuovere prima di tutto quella esistente facendo clic sul pulsante Elimina visualizzato sul lato destro della riga al passaggio del mouse.

    ![Esaminare la parola chiave](media/speech-devices-sdk/custom-kws-portal-review-keyword.png)

1. La generazione del modello potrebbe richiedere fino a un minuto. Verrà quindi richiesto di scaricare il file.

    ![Scaricare la parola chiave](media/speech-devices-sdk/custom-kws-portal-download-keyword.png)

1. Salvare il file ZIP nel computer. Questo file sarà necessario per distribuire la parola chiave personalizzata al kit di sviluppo.

## <a name="next-steps"></a>Passaggi successivi

Testare la parola chiave personalizzata con la [Guida introduttiva all'SDK dei dispositivi vocali](https://aka.ms/sdsdk-quickstart).
