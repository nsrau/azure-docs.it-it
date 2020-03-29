---
title: Utilizzare le revisioni dei contenuti tramite lo strumento di revisione - Content Moderator
titleSuffix: Azure Cognitive Services
description: Lo strumento di revisione consente ai moderatori umani di rivedere le immagini all'interno di un portale Web.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: b02324923e3f004395105b8e04165390cb950fe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73044123"
---
# <a name="create-human-reviews"></a>Crea recensioni umane

In questa guida verrà illustrato come impostare [le recensioni](../review-api.md#reviews) nel sito Web dello strumento Revisione. Recensioni memorizzare e visualizzare i contenuti per i moderatori umani da valutare. I moderatori possono modificare i tag applicati e applicare i propri tag personalizzati in base alle esigenze. Quando un utente completa una revisione, i risultati vengono inviati a un endpoint di callback specificato e il contenuto viene rimosso dal sito.

## <a name="prerequisites"></a>Prerequisiti

- Accedere o creare un account nel sito dello strumento Content Moderator [Review.](https://contentmoderator.cognitive.microsoft.com/)

## <a name="image-reviews"></a>Revisioni di immagini

1. Vai allo [strumento Revisione,](https://contentmoderator.cognitive.microsoft.com/)seleziona la scheda **Prova** e carica alcune immagini da rivedere.
1. Al termine dell'elaborazione delle immagini caricate, passare alla scheda **Revisione** e selezionare **Immagine**.

    ![Finestra del browser Chrome che mostra lo strumento di revisione con l'opzione per la revisione di immagini evidenziata](images/review-images-1.png)

    Le immagini vengono visualizzate con tutte le etichette assegnate dal processo di moderazione automatica. Le immagini inviate tramite lo strumento Revisione non sono visibili agli altri revisori.

1. Facoltativamente, spostare il dispositivo di scorrimento **Revisioni per visualizzare** (1) per regolare il numero di immagini visualizzate sullo schermo. Fare clic sui **pulsanti con tag** o **senza tag** (2) per ordinare le immagini di conseguenza. Fare clic su un pannello tag (3) per attivarlo o disattivarlo.

    ![Finestra del browser Chrome che mostra lo strumento di revisione con immagini contrassegnate da tag per la revisione](images/review-images-2.png)

1. Per visualizzare ulteriori informazioni su un'immagine, fare clic sui ellissi nella miniatura e selezionare **Visualizza dettagli**. Puoi assegnare un'immagine a un sottosquadra con l'opzione **Sposta in** (vedi la sezione [Team](./configure.md#manage-team-and-subteams) per ulteriori informazioni sui sottoteam).

    ![Un'immagine con l'opzione per visualizzazione dei dettagli evidenziata](images/review-images-3.png)

1. Leggere le informazioni sulla moderazione delle immagini nella pagina dei dettagli.

    ![Un'immagine con i dettagli sulla moderazione elencati in un riquadro separato](images/review-images-4.png)

1. Dopo aver rivisto e aggiornato le assegnazioni dei tag in base alle esigenze, fare clic su **Next** (Avanti) per inviare le revisioni. Dopo l'invio l'utente ha a disposizione cinque secondi per fare clic sul pulsante **Prev** (Indietro) per tornare alla schermata precedente e rivedere di nuovo le immagini. Successivamente, le immagini non saranno più disponibili nella coda di invio e il pulsante **Prev** (Indietro) non sarà più disponibile.

## <a name="text-reviews"></a>Revisioni di testi

Le revisioni di testo funzionano in modo simile alle recensioni delle immagini. Invece di caricare contenuti, è sufficiente scrivere o incollare nel testo (fino a 1.024 caratteri). Quindi, Content Moderator analizza il testo e applica i tag (oltre ad altre informazioni di moderazione, come volgarità e dati personali). Nelle revisioni di testo, puoi attivare/disattivare i tag applicati e/o applicare tag personalizzati prima di inviare la recensione.

![Screenshot dello strumento di revisione che mostra il testo contrassegnato in una finestra del browser Chrome](../images/reviewresults_text.png)

## <a name="next-steps"></a>Passaggi successivi

In questa guida è stato illustrato come impostare e utilizzare le recensioni dello [strumento Content](https://contentmoderator.cognitive.microsoft.com)Moderator Review . Vedere quindi la [guida all'API REST](../try-review-api-review.md) o la guida introduttiva di [.NET SDK](../dotnet-sdk-quickstart.md) per informazioni su come creare revisioni a livello di codice.