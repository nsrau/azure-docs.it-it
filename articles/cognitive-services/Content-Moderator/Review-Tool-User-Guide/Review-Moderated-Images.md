---
title: Usare le verifiche del contenuto tramite lo strumento di revisione - Content Moderator
titlesuffix: Azure Cognitive Services
description: Lo strumento di revisione consente ai moderatori umani di rivedere le immagini all'interno di un portale Web.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: a482ecf4a0d321525ab7e392695d2c4c0eebeadc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60629046"
---
# <a name="create-human-reviews"></a>Creazione di revisione umana

In questa guida si apprenderà come configurare [esamina](../review-api.md#reviews) sul sito Web dello strumento di revisione. Le verifiche di archiviano e visualizzare il contenuto da moderatori umani per valutare. I moderatori possono modificare i tag applicati e applicare i propri tag personalizzati come appropriato. Quando un utente ha completato un'analisi, i risultati vengono inviati a un endpoint di callback specificati e il contenuto viene rimosso dal sito.

## <a name="prerequisites"></a>Prerequisiti

- Accedi o crea un account su Content Moderator [strumento di revisione](https://contentmoderator.cognitive.microsoft.com/) sito.

## <a name="image-reviews"></a>Revisioni di immagini

1. Andare alla [strumento di revisione](https://contentmoderator.cognitive.microsoft.com/), selezionare la **provare** scheda e caricare alcune immagini per esaminare.
1. Dopo che le immagini caricate hanno terminato l'elaborazione, passare al **revisione** scheda e selezionare **immagine**.

    ![Finestra del browser Chrome che mostra lo strumento di revisione con l'opzione per la revisione di immagini evidenziata](images/review-images-1.png)

    Le immagini vengono visualizzati con le etichette che sono state assegnate dal processo di moderazione automatica. Le immagini di che aver inviato tramite lo strumento di revisione non sono visibili agli altri revisori.

1. Facoltativamente, spostare il **verifiche da visualizzare** slider (1) per modificare il numero di immagini che vengono visualizzati sullo schermo. Fare clic sui **tagged** o **senza tag** pulsanti (2) per ordinare le immagini di conseguenza. Fare clic su un pannello tag (3) per attivare/disattivare o disattivare.

    ![Finestra del browser Chrome che mostra lo strumento di revisione con immagini contrassegnate da tag per la revisione](images/review-images-2.png)

1. Per altre informazioni su un'immagine, fare clic sui puntini di sospensione in anteprima e selezionare **visualizzare i dettagli**. È possibile assegnare un'immagine a un Sub-team con il **spostarne** opzione (vedere la [Team](./configure.md#manage-team-and-subteams) sezione per altre informazioni su sottoteam).

    ![Un'immagine con l'opzione per visualizzazione dei dettagli evidenziata](images/review-images-3.png)

1. Leggere le informazioni sulla moderazione delle immagini nella pagina dei dettagli.

    ![Un'immagine con i dettagli sulla moderazione elencati in un riquadro separato](images/review-images-4.png)

1. Dopo aver rivisto e aggiornato le assegnazioni dei tag in base alle esigenze, fare clic su **Next** (Avanti) per inviare le revisioni. Dopo l'invio l'utente ha a disposizione cinque secondi per fare clic sul pulsante **Prev** (Indietro) per tornare alla schermata precedente e rivedere di nuovo le immagini. Successivamente, le immagini non saranno più disponibili nella coda di invio e il pulsante **Prev** (Indietro) non sarà più disponibile.

## <a name="text-reviews"></a>Revisioni di testi

Testo esamina un funzionamento simile alle revisioni di immagini. Invece di caricare il contenuto, sufficiente scrivere o incollare testo (fino a 1.024 caratteri). Quindi, Content Moderator analizza il testo e applica tag (oltre alle informazioni di moderazione, ad esempio il contenuto volgare e i dati personali). Nelle revisioni di testi, è possibile attivare o disattivare i tag applicati e/o applicare tag personalizzati prima di inviare la revisione.

![Screenshot dello strumento di revisione che mostra il testo contrassegnato in una finestra del browser Chrome](../images/reviewresults_text.png)

## <a name="next-steps"></a>Passaggi successivi

In questa Guida, si è appreso come configurare e usare le verifiche di Content Moderator [strumento di revisione](https://contentmoderator.cognitive.microsoft.com). Successivamente, vedere la [Guida all'API REST](../try-review-api-review.md) o il [Guida di .NET SDK](../moderation-reviews-quickstart-dotnet.md) per imparare a creare le verifiche a livello di codice.