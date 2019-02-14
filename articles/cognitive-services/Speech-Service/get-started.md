---
title: Provare gratuitamente i servizi di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Iniziare a usare il servizio di riconoscimento vocale è semplice e conveniente. Una versione di valutazione gratuita di 30 giorni permette di scoprire cosa può fare il servizio e decidere se è adatto alle esigenze della propria applicazione.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 203cb07c9450a590b45caa66092fe0199d985058
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978154"
---
# <a name="try-the-speech-service-for-free"></a>Provare gratuitamente il Servizio di riconoscimento vocale

Iniziare a usare il servizio di riconoscimento vocale è semplice e conveniente. Una versione di valutazione gratuita di 30 giorni permette di scoprire cosa può fare il servizio e decidere se è adatto alle esigenze della propria applicazione.

Se è necessario più tempo, effettuare l'iscrizione per un account di Microsoft Azure, che offre un credito di 200 dollari che è possibile impiegare per una sottoscrizione a pagamento al servizio Voce per un massimo di 30 giorni.

Infine, il servizio Voce offre un livello gratuito di basso volume adatto allo sviluppo di applicazioni. È possibile mantenere questa sottoscrizione gratuita anche dopo la scadenza del credito di servizio.

## <a name="free-trial"></a>Versione di prova gratuita

La versione di prova gratuita di 30 giorni consente di accedere al piano tariffario standard per un periodo di tempo limitato.

Per iscriversi a una versione di prova gratuita di 30 giorni:

1. Andare su [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/).

1. Selezionare la scheda **API di riconoscimento vocale**.

   ![Scheda Servizi di riconoscimento vocale](media/index/try-speech-api-free-trial1.png)

1. In **Servizi di riconoscimento vocale**, selezionare il tasto **Ottieni chiave API**.

   ![Chiave API](media/index/try-speech-api-free-trial2.png)

1. Accettare le condizioni e selezionare le impostazioni locali dal menu a discesa.

   ![Accettare le condizioni](media/index/try-speech-api-free-trial3.png)

1. Accedere tramite il proprio account Microsoft, Facebook, LinkedIn o GitHub.

    È possibile creare un account Microsoft gratuito sul [portale Account Microsoft](https://account.microsoft.com/account). Per iniziare, fare clic su **Accedi con Microsoft** e quindi, quando viene richiesto di accedere, fare clic su **Creane uno.** Seguire i passaggi per creare e verificare il nuovo account Microsoft.

Dopo l'accesso a Prova Servizi cognitivi, la versione di prova gratuita è pronta. La pagina Web visualizzata elenca tutti i Servizi cognitivi per i quali si dispone attualmente di sottoscrizioni di prova. Sono elencate due chiavi di sottoscrizione accanto a **Servizi di riconoscimento vocale**. È possibile usare entrambe le chiavi nelle applicazioni.

> [!NOTE]
> Tutte le sottoscrizioni di valutazione gratuite sono nell'area Stati Uniti occidentali. Quando si eseguono richieste, assicurarsi di usare l'endpoint `westus`.

## <a name="new-azure-account"></a>Nuovo account Azure

I nuovi account Azure ricevono un credito di servizio di 200 dollari che è disponibile fino a un massimo di 30 giorni. Questo credito può essere usato per scoprire ulteriori funzioni del servizio di sintesi vocale o per iniziare lo sviluppo di applicazioni.

Per creare un nuovo account Azure, andare alla [Pagina di iscrizione di Azure](https://azure.microsoft.com/free/ai/), fare clic su **Inizia gratis,** quindi creare un nuovo account Azure usando il proprio account Microsoft.

È possibile creare un account Microsoft gratuito sul [portale Account Microsoft](https://account.microsoft.com/account). Per iniziare, fare clic su **Accedi con Microsoft** e quindi, quando viene richiesto di accedere, fare clic su **Creane uno.** Seguire i passaggi per creare e verificare il nuovo account Microsoft.

Dopo aver creato il proprio account Azure, seguire i passaggi nella sezione successiva per avviare una sottoscrizione al servizio di riconoscimento vocale.

## <a name="create-a-speech-resource-in-azure"></a>Creare una risorsa di riconoscimento vocale in Azure

Per aggiungere una risorsa del servizio vocale (gratuita o a pagamento) al proprio account Azure:

1. Accedere al [portale di Azure](https://portal.azure.com/) mediante il proprio account Microsoft.

1. Selezionare **Crea una risorsa** in alto a sinistra sul portale.

    ![Creare una risorsa](media/index/try-speech-api-create-speech1.png)

1. Nella finestra **Nuova**, cercare **Voce**.

1. Nei risultati della ricerca, selezionare **Voce**.

    ![Selezionare Voce](media/index/try-speech-api-create-speech2.png)

1. Sotto **Voce**, selezionare il pulsante **Crea**.

    ![Selezionare il pulsante Crea](media/index/try-speech-api-create-speech3.png)

1. Sotto **Crea**, immettere:

    * Un nome per la nuova risorsa. Il nome consente di distinguere tra più sottoscrizioni per il servizio stesso.
    * Scegliere la sottoscrizione di Azure a cui è associata la nuova risorsa per determinare le modalità di fatturazione.
    * Scegliere l'area in cui verrà usata la risorsa. Attualmente, il servizio di riconoscimento vocale è disponibile nelle aree Asia orientale, Europa settentrionale e Stati Uniti occidentali.
    * Scegliere un piano tariffario gratuito o a pagamento. Fare clic su **Visualizza i dettagli completi sui prezzi** per informazioni complete sulle quote di utilizzo e sui prezzi per ogni livello.
    * Creare un nuovo gruppo di risorse per questa sottoscrizione di riconoscimento vocale o assegnarla a un gruppo di risorse esistente. I gruppi di risorse consentono di mantenere organizzate le diverse sottoscrizioni di Azure.
    * Per un rapido accesso alla sottoscrizione in futuro, selezionare la casella di controllo **Aggiungi al dashboard**.
    * Selezionare **Crea.**

    ![Selezionare il pulsante Crea](media/index/try-speech-api-create-speech4.png)

    È necessario qualche secondo per creare e distribuire la nuova risorsa di Riproduzione vocale. Selezionare **Avvio rapido** per visualizzare le informazioni sulla nuova risorsa.

    ![Pannello di avvio rapido](media/index/try-speech-api-create-speech5.png)

1. Sotto **Avvio rapido**, fare clic sul collegamento **Chiavi** nel passaggio 1 per visualizzare le chiavi di sottoscrizione. Ogni sottoscrizione dispone di due chiavi, entrambi utilizzabili nell'applicazione. Selezionare pulsante accanto a ogni chiave per copiarla negli Appunti e incollarla quindi nel codice.

> [!NOTE]
> È possibile creare un numero illimitato di sottoscrizioni di livello standard in una o più aree. Tuttavia, è possibile creare solo una sottoscrizione gratuita. Le distribuzioni di modelli nel livello gratuito che rimangono inutilizzate per 7 giorni saranno automaticamente rimosse.

## <a name="switch-to-a-new-subscription"></a>Passare a una nuova sottoscrizione

Per passare da una sottoscrizione a un'altra, ad esempio quando la versione di prova gratuita è scaduta o quando si pubblica la propria applicazione, sostituire l'area e la chiave di sottoscrizione nel codice con l'area e la chiave di sottoscrizione della nuova risorsa di Azure.

> [!NOTE]
> Le chiavi per le prove gratuite sono create nell'area degli Stati Uniti occidentali (`westus`). Se si desidera, una sottoscrizione creata tramite il dashboard di Azure potrebbe essere in altre aree.

* Se l'applicazione usa un [SDK per il riconoscimento vocale](speech-sdk.md), specificare il codice di area, ad esempio `westus`, durante la creazione di una configurazione di riconoscimento vocale.
* Se l'applicazione usa una delle [API REST](rest-apis.md) del Servizio di riconoscimento vocale, l'area è parte dell'URI dell'endpoint usato quando si effettuano richieste.

Le chiavi create per un'area sono valide solo in quell'area. Se si prova a usarle con altre aree si verificheranno errori di autenticazione.

## <a name="next-steps"></a>Passaggi successivi

Completare una delle guide introduttive di 10 minuti o vedere gli esempi di SDK:

> [!div class="nextstepaction"]
> [Avvio rapido: Riconoscere i contenuti vocali in C#](quickstart-csharp-dotnet-windows.md)
> [Esempi di Speech SDK](speech-sdk.md#get-the-samples)
