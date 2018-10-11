---
title: Creare una parola di attivazione personalizzata
description: Imparare a creare una parola di attivazione personalizzata per Speech Devices SDK.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: 52f1d9cb242f140d485ccf7b39a55a46ee575a3d
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2018
ms.locfileid: "48883619"
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

Per usare una parola di attivazione personalizzata con il dispositivo, è necessario prima crearla usando il servizio Microsoft Custom Wake Word Generation. Dopo aver fornito una parola di attivazione, il servizio genera un file da distribuire nel kit di sviluppo per abilitare la parola di attivazione sul dispositivo.

1. Andare al [portale del Servizio di riconoscimento vocale personalizzato](https://cris.ai/).

1. Creare un nuovo account con l'indirizzo di posta elettronica che ha ricevuto l'invito per Azure Active Directory. 

    ![Creare un nuovo account](media/speech-devices-sdk/wake-word-1.png)
 
1.  Dopo l'accesso, compilare il modulo e quindi selezionare **Start my journey** (Inizia percorso).

    ![Accesso eseguito](media/speech-devices-sdk/wake-word-3.png)
 
1. Poiché la pagina **Custom Wake Word** (Parola di attivazione personalizzata) non è disponibile per il pubblico, non sono presenti collegamenti diretti per accedervi. La funzionalità di riconoscimento vocale personalizzato richiede una sottoscrizione ad Azure, al contrario della funzionalità che permette di creare una parola di attivazione personalizzata. Se si visualizza il messaggio di errore **No Subscriptions found** (Non sono state trovate sottoscrizioni), sostituire semplicemente **"Subscriptions?errorMessage=No%20Subscriptions%20found"** con "**customkws**" nell'URL e premere INVIO. L'URL deve corrispondere a uno dei seguenti: https://westus.cris.ai/customkws, https://eastasia.cris.ai/customkws o https://northeurope.cris.ai/customkws, a seconda della propria regione.

    ![La pagina Custom Wake Word è nascosta](media/speech-devices-sdk/wake-word-4.png)
 
1. Digitare la parola di attivazione scelta, quindi selezionare **Submit the word** (Invia parola).

    ![Immettere la parola di attivazione](media/speech-devices-sdk/wake-word-5.png)
 
1. La generazione dei file può richiedere alcuni minuti. Verrà visualizzato un cerchio rotante nella finestra del browser. Poco dopo viene visualizzata una barra delle informazioni con la richiesta di scaricare un file ZIP.

    ![Ricezione del file ZIP](media/speech-devices-sdk/wake-word-6.png)

1. Salvare il file ZIP nel computer. È necessario questo file per distribuire la parola di attivazione personalizzata nel kit di sviluppo. Per distribuire la parola di attivazione personalizzata, seguire le istruzioni riportate in [Introduzione a Speech Devices SDK](speech-devices-sdk-qsg.md).

1. Selezionare **Sign out** (Disconnessione).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, ottenere un [account gratuito Azure](https://azure.microsoft.com/free/) e registrarsi per Speech Devices SDK.

> [!div class="nextstepaction"]
> [Registrarsi per Speech Devices SDK](get-speech-devices-sdk.md)

