---
title: Usare le revisioni del contenuto tramite lo strumento di revisione-Content Moderator
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
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044123"
---
# <a name="create-human-reviews"></a>Crea recensioni umane

In questa guida verrà illustrato come configurare le [revisioni](../review-api.md#reviews) nel sito Web dello strumento di revisione. Esamina l'archivio e visualizza il contenuto per i moderatori umani da valutare. I moderatori possono modificare i tag applicati e applicare i propri tag personalizzati in base alle esigenze. Quando un utente completa una revisione, i risultati vengono inviati a un endpoint di callback specificato e il contenuto viene rimosso dal sito.

## <a name="prerequisites"></a>Prerequisiti

- Accedere o creare un account nel sito [dello strumento di revisione](https://contentmoderator.cognitive.microsoft.com/) content moderator.

## <a name="image-reviews"></a>Revisioni di immagini

1. Passare allo [strumento di verifica](https://contentmoderator.cognitive.microsoft.com/), selezionare la scheda **try** e caricare alcune immagini da rivedere.
1. Al termine dell'elaborazione delle immagini caricate, passare alla scheda **Revisione** e selezionare **immagine**.

    ![Finestra del browser Chrome che mostra lo strumento di revisione con l'opzione per la revisione di immagini evidenziata](images/review-images-1.png)

    Le immagini vengono visualizzate con le etichette assegnate dal processo di moderazione automatica. Le immagini inviate tramite lo strumento di revisione non sono visibili ad altri revisori.

1. Facoltativamente, spostare le **revisioni per visualizzare** il dispositivo di scorrimento (1) per modificare il numero di immagini visualizzate sullo schermo. Fare clic sui pulsanti **con tag** o senza **tag** (2) per ordinare le immagini di conseguenza. Fare clic su un pannello Tag (3) per attivarlo o disattivarlo.

    ![Finestra del browser Chrome che mostra lo strumento di revisione con immagini contrassegnate da tag per la revisione](images/review-images-2.png)

1. Per visualizzare altre informazioni su un'immagine, fare clic sui puntini di sospensione nell'anteprima e selezionare **Visualizza dettagli**. È possibile assegnare un'immagine a un sottoteam con l'opzione **Move to** (vedere la sezione [Teams](./configure.md#manage-team-and-subteams) per altre informazioni sui sottoteam).

    ![Un'immagine con l'opzione per visualizzazione dei dettagli evidenziata](images/review-images-3.png)

1. Leggere le informazioni sulla moderazione delle immagini nella pagina dei dettagli.

    ![Un'immagine con i dettagli sulla moderazione elencati in un riquadro separato](images/review-images-4.png)

1. Dopo aver rivisto e aggiornato le assegnazioni dei tag in base alle esigenze, fare clic su **Next** (Avanti) per inviare le revisioni. Dopo l'invio l'utente ha a disposizione cinque secondi per fare clic sul pulsante **Prev** (Indietro) per tornare alla schermata precedente e rivedere di nuovo le immagini. Successivamente, le immagini non saranno più disponibili nella coda di invio e il pulsante **Prev** (Indietro) non sarà più disponibile.

## <a name="text-reviews"></a>Revisioni di testi

Le revisioni del testo funzionano in modo analogo alle verifiche immagini Anziché caricare il contenuto, è sufficiente scrivere o incollare testo (fino a 1.024 caratteri). Quindi, Content Moderator analizza il testo e applica i tag, oltre ad altre informazioni sulla moderazione, ad esempio la volgarità e i dati personali. Nelle revisioni del testo è possibile impostare i tag applicati e/o applicare tag personalizzati prima di inviare la revisione.

![Screenshot dello strumento di revisione che mostra il testo contrassegnato in una finestra del browser Chrome](../images/reviewresults_text.png)

## <a name="next-steps"></a>Passaggi successivi

In questa guida si è appreso come configurare e usare le revisioni dallo [strumento content moderator Review](https://contentmoderator.cognitive.microsoft.com). Vedere quindi la [Guida dell'API REST](../try-review-api-review.md) o la Guida [introduttiva di .NET SDK](../dotnet-sdk-quickstart.md) per informazioni su come creare recensioni a livello di codice.