---
title: Provare gratuitamente il Servizio di riconoscimento vocale | Microsoft Docs
description: Di seguito viene descritto come è possibile provare il servizio di riconoscimento vocale senza alcun costo.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/17/2018
ms.author: v-jerkin
ms.openlocfilehash: 3feef04694336d9173b419285a96fcaef543e18f
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "35378937"
---
# <a name="try-the-speech-service-for-free"></a>Provare gratuitamente il Servizio di riconoscimento vocale

Iniziare a usare il servizio di riconoscimento vocale è semplice e conveniente. Una versione di valutazione gratuita di 30 giorni permette di scoprire cosa può fare il servizio e decidere se è adatto alle esigenze della propria applicazione.

Se è necessario più tempo, effettuare l'iscrizione per un account di Microsoft Azure, che offre un credito di 200 dollari che è possibile impiegare per una sottoscrizione a pagamento al servizio Voce per un massimo di 30 giorni.

Infine, il servizio Voce offre un livello gratuito di basso volume adatto allo sviluppo di applicazioni. È possibile mantenere questa sottoscrizione gratuita anche dopo la scadenza del credito di servizio.

## <a name="free-trial"></a>Versione di prova gratuita

La versione di valutazione gratuita di 30 giorni consente di accedere al piano tariffario standard S0 per un periodo di tempo limitato. Per iscriversi a una versione di valutazione gratuita di 30 giorni, seguire questi passaggi.

1. Accedere alla pagina [Prova dei servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/).

1. Passare alla scheda riconoscimento vocale e fare clic sul pulsante **Ottieni chiave API** accanto a "Servizi di riconoscimento vocale".

   ![Scheda Servizi di riconoscimento vocale](media/index/try-speech-api-free-trial1.png)<br>
   ![Chiave API](media/index/try-speech-api-free-trial2.png)

3. Accettare le condizioni e selezionare le impostazioni locali dal menu a discesa.

   ![Accettare le condizioni](media/index/try-speech-api-free-trial3.png)

4. Accedere tramite il proprio account Microsoft, Facebook, LinkedIn o GitHub. In alternativa, è possibile iscriversi a un account Microsoft gratuito:

    * Accedere al [portale per gli account Microsoft](https://account.microsoft.com/account).
    * Fare clic su **Accedi con Microsoft**.

    ![Accesso](media/index/try-speech-api-free-trial4.png)

    * Quando viene richiesto di eseguire l'accesso, fare clic su "Crea un account".

    ![Crea un nuovo account](media/index/try-speech-api-free-trial5.png)

    * Nei passaggi che seguono, immettere l'indirizzo e-mail o il numero di telefono, assegnare una password e seguire le istruzioni per verificare il nuovo account Microsoft.

Dopo l'accesso, viene avviata la versione di valutazione gratuita. La pagina Web visualizzata elenca tutti i Servizi Cognitivi per i quali si dispone attualmente di sottoscrizioni di prova. Sono elencate due chiavi di sottoscrizione accanto a "Servizi di riconoscimento vocale". È possibile utilizzare entrambe le chiavi nelle applicazioni.

> [!NOTE]
> Tutte le sottoscrizioni di valutazione gratuite sono nell'area Stati Uniti occidentali. Assicurarsi di utilizzare l'endpoint corrispondente alla propria area quando si effettuano richieste.

## <a name="new-azure-account"></a>Nuovo account Azure

I nuovi account Azure ricevono un credito di servizio di 200 dollari che dura fino a 30 giorni. Questo credito può essere utilizzato per scoprire ulteriori funzioni del servizio di sintesi vocale o per iniziare lo sviluppo di applicazioni.

Per iscriversi per un nuovo account Azure, seguire questa procedura:

1. Accedere alla [pagina di iscrizione di Azure](https://azure.microsoft.com/free/ai/). 

1. Fare clic su **Inizia gratuitamente**.

    ![Iniziare gratuitamente](media/index/try-speech-api-new-azure1.png)

3. Accedere con il proprio account Microsoft. Se non si ha un account:

    * Accedere al [portale per gli account Microsoft](https://account.microsoft.com/account).
    * Fare clic su **Accedi con Microsoft**.
    * Quando viene richiesto di eseguire l'accesso, fare clic su "Crea un account".
    * Nei passaggi che seguono, immettere l'indirizzo e-mail o il numero di telefono, assegnare una password e seguire le istruzioni per verificare il nuovo account Microsoft.

1. Immettere il resto delle informazioni richieste per l'iscrizione a un account. Specificare il paese e il nome e fornire un indirizzo e-mail e un numero di telefono.

    ![Immettere informazioni](media/index/try-speech-api-new-azure2.png)

    Verifica la propria identità per telefono e fornendo un numero di carta di credito, quindi accettare il contratto utente di Azure. (Non verranno eseguiti addebiti sulla carta di credito)

    ![Accettare il contratto](media/index/try-speech-api-new-azure3.png)

Viene creato l'account gratuito di Azure. Seguire i passaggi nella sezione successiva per avviare una sottoscrizione al servizio di riconoscimento vocale.

## <a name="create-a-speech-resource-in-azure"></a>Creare una risorsa di riconoscimento vocale in Azure

Per aggiungere una risorsa del servizio vocale al proprio account Azure, seguire questa procedura.

1. Accedere al [portale di Azure](https://ms.portal.azure.com/) mediante il proprio account Microsoft.

1. Fare clic su **Crea una risorsa** (icona verde **+**) nella parte superiore sinistra del portale.

    ![Crea risorsa](media/index/try-speech-api-create-speech1.png)

1. Nella finestra Nuovo, cercare Riconoscimento vocale.

    ![Fare clic su Riconoscimento vocale](media/index/try-speech-api-create-speech2.png)

1. Nei risultati della ricerca fare clic su "Riconoscimento vocale (anteprima)".

1. Fare clic sul pulsante **Crea** nella parte inferiore del pannello del servizio del riconoscimento vocale.

    ![Fare clic su Crea](media/index/try-speech-api-create-speech3.png)

1. Nel pannello Crea, immettere:

    * Un nome per la nuova risorsa. Il nome consente di distinguere tra più sottoscrizioni per il servizio stesso.
    * Scegliere la sottoscrizione di Azure a cui è associata la nuova risorsa per determinare le modalità di fatturazione.
    * Scegliere l'area in cui verrà usata la risorsa. Attualmente, il servizio di riconoscimento vocale è disponibile nelle aree Asia orientale, Europa settentrionale e Stati Uniti occidentali.
    * Scegliere il piano tariffario, F0 (sottoscrizione gratuita limitata) o S0 (sottoscrizione standard). Fare clic su **Visualizza i dettagli completi sui prezzi** per informazioni complete sulle quote di utilizzo e sui prezzi per ogni livello.
    * Creare un nuovo gruppo di risorse per questa sottoscrizione di riconoscimento vocale o assegnarla a uno esistente. I gruppi di risorse consentono di mantenere organizzate le diverse sottoscrizioni di Azure.
    * Per un rapido accesso alla sottoscrizione in futuro, contrassegnare la casella di controllo **Aggiungi al dashboard**.
    * Fare clic su **Crea**.

    ![Fare clic su Crea nel pannello](media/index/try-speech-api-create-speech4.png)

    Potrebbe essere necessario qualche secondo per creare e distribuire la nuova risorsa di Riproduzione vocale. Viene visualizzato il pannello di avvio rapido con informazioni sulla nuova risorsa.

    ![Pannello di avvio rapido](media/index/try-speech-api-create-speech5.png)

1. Fare clic sul collegamento **Chiavi** nel passaggio 1 del pannello avvio rapido per visualizzare le chiavi di sottoscrizione. Ogni sottoscrizione dispone di due chiavi che è possibile utilizzare nell'applicazione. Fare clic sul pulsante accanto a ogni chiave per copiarla negli Appunti e incollarla quindi nel codice.

> [!NOTE]
> È possibile creare qualsiasi numero di sottoscrizioni di livello standard in una o più aree. Tuttavia, è possibile creare solo una sottoscrizione gratuita.

## <a name="next-steps"></a>Passaggi successivi

Scaricare un SDK e alcuni codici di esempio per provare il servizio di riconoscimento vocale.

> [!div class="nextstepaction"]
> [SDK di riconoscimento vocale](speech-sdk.md)

> [!div class="nextstepaction"]
> [Codice di esempio](samples.md)
