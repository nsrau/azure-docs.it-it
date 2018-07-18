---
title: Creazione di una parola di attivazione personalizzata | Microsoft Docs
description: Creazione di una parola di attivazione personalizzata per Speech Devices SDK.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: 2575ed24bb931ca4da05dd6663b976406af590e6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35378431"
---
# <a name="create-a-custom-wake-word-using-speech-service"></a>Creare una parola di attivazione personalizzata usando il servizio Voce

Il dispositivo è sempre in ascolto di una parola (o frase) di attivazione. Ad esempio, "Ehi Cortana" è una parola di attivazione per l'assistente Cortana. Quando l'utente pronuncia la parola di attivazione, il dispositivo inizia a inviare tutto l'audio successivo al cloud fino a quando l'utente non smette di parlare. Modificare la parola di attivazione è un modo efficace per differenziare il dispositivo e accrescere la personalizzazione.

In questo articolo viene illustrato come creare una parola di attivazione personalizzata per il dispositivo.

## <a name="choosing-an-effective-wake-word"></a>Scelta di una parola di attivazione efficace

Quando si sceglie una parola di attivazione, tenere presente le linee guida seguenti.

* La parola di attivazione deve essere una parola o una frase in lingua italiana. Per pronunciarla, non devono servire più di due secondi.

* Le parole composte da 4-7 sillabe sono ottimali. "Ehi, computer", ad esempio, è una parola di attivazione appropriata, a differenza del semplice "Ehi".

* Le parole di attivazione devono seguire le comuni regole di pronuncia italiana.

* Una parola particolare o addirittura inventata che segue le normali regole di pronuncia italiana consente di ridurre i falsi positivi. Ad esempio, "computerama" può essere una parola di attivazione valida.

* Non scegliere una parola comune. Ad esempio, "mangiare" e "andare" sono parole che le persone ripetono spesso parlando normalmente e potrebbero essere falsi trigger per il dispositivo.

* Evitare di usare una parola di attivazione che può essere pronunciata in modi diversi. Gli utenti dovranno conoscere la pronuncia "corretta" per fare in modo che il dispositivo risponda. Ad esempio, "509" può essere pronunciato "cinquecentonove", "cinque zero nove" o "cinquecento nove". "U.F.O." può essere pronunciato "U F O" oppure "ufo". "Pesca" può essere pronunciato [pèsca] o [pésca].

* Non usare caratteri speciali, simboli o cifre. Ad esempio, "Vai€" e "20 + gatti" non sono parole di attivazione adatte, mentre "vai euro" o "venti più gatti" può andare bene. È comunque possibile usare i simboli nella personalizzazione e usare il marketing e la documentazione per ribadire la pronuncia corretta.

> [!NOTE]
> Se si sceglie un marchio come parola di attivazione, assicurarsi di essere il proprietario di tale marchio o di avere l'autorizzazione del proprietario del marchio a usarlo. Microsoft non è responsabile di eventuali problemi legali che potrebbero emergere dalla scelta della parola di attivazione.

## <a name="creating-your-wake-word"></a>Creazione della parola di attivazione

Per usare una parola di attivazione personalizzata con il dispositivo, è necessario prima crearla usando il servizio Microsoft Custom Wake Word Generation. Dopo aver fornito una parola di attivazione, il servizio genera un file da distribuire nel kit di sviluppo per abilitare la parola di attivazione sul dispositivo.

1. Andare al [portale del Servizio di riconoscimento vocale personalizzato](https://cris.ai/).

2. Creare un nuovo account con l'indirizzo di posta elettronica che ha ricevuto l'invito per Azure Active Directory. 

    ![Creare un nuovo account](media/speech-devices-sdk/wake-word-1.png)
 
3.  Dopo la connessione, compilare il modulo, quindi fare clic su **Start the journey** (Inizia).

    ![Connessione completata](media/speech-devices-sdk/wake-word-3.png)
 
4. Poiché la pagina **Custom Wake Word** (Parola di attivazione personalizzata) non è disponibile per il pubblico, non sono presenti collegamenti per accedervi. Fare quindi clic su questo collegamento o incollarlo: https://cris.ai/customkws.

    ![Pagina nascosta](media/speech-devices-sdk/wake-word-4.png)
 
6. Digitare la pagina di attivazione scelta, quindi **inviarla**.

    ![Immettere la parola di attivazione](media/speech-devices-sdk/wake-word-5.png)
 
7. La generazione dei file può richiedere alcuni minuti. Verrà visualizzato un cerchio rotante nella scheda del browser. Poco dopo viene visualizzata una barra delle informazioni con la richiesta di scaricare un file `.zip`.

    ![Ricezione del file ZIP](media/speech-devices-sdk/wake-word-6.png)

8. Salvare il file `.zip` nel computer. Questo file è necessario per distribuire la parola di attivazione personalizzata nel kit di sviluppo, seguendo le istruzioni riportate in [Introduzione a Speech Devices SDK](speech-devices-sdk-qsg.md).

9. È ora possibile **disconnettersi**.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, ottenere un [account gratuito Azure](https://azure.microsoft.com/free/) e registrarsi per Speech Devices SDK.

> [!div class="nextstepaction"]
> [Registrarsi per Speech Devices SDK](get-speech-devices-sdk.md)

