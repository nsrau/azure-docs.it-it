---
title: Uso dei domini predefiniti nelle applicazioni LUIS in Azure | Microsoft Docs
description: Imparare a usare domini predefiniti nelle applicazioni di in Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 72ca0def8528adae5e46a02fa5bfccd14a3b6ab4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "35378539"
---
# <a name="use-prebuilt-domains-in-luis-apps"></a>Usare i domini predefiniti nelle app LUIS  

Language Understanding (LUIS) fornisce *domini predefiniti*, ovvero gruppi predefiniti di [finalità](luis-how-to-add-intents.md) ed [entità](luis-concept-entity-types.md) che cooperano in funzione di domini o di categorie generiche di applicazioni client. I domini predefiniti sono stati sottoposti a training preventivo e sono pronti da aggiungere all'app LUIS. Le finalità e le entità in un dominio predefinito sono completamente personalizzabili una volta aggiunte all'app: è possibile eseguire il training con le espressioni provenienti dal sistema in modo tale che dagli utenti possano consultarli. È possibile usare un intero dominio predefinito come punto di partenza per la personalizzazione o, in alternativa, selezionare solo alcune finalità o entità provenienti da un dominio predefinito. 

Esplora la scheda **Domini predefiniti** per visualizzare altri domini predefiniti da usare nell'app. Fai clic sul riquadro per aggiungere un dominio all'app, oppure su **Altre informazioni** nel riquadro stesso per scoprire altri dettagli sulle finalità e le entità ad esso riferite.

> [!TIP]
> È possibile consultare l'elenco completo dei domini predefiniti nei [riferimenti dei domini predefiniti](./luis-reference-prebuilt-domains.md).

![Aggiungi dominio predefinito](./media/luis-how-to-prebuilt-domain-entities/add-prebuilt-domain.png)


## <a name="add-a-prebuilt-domain"></a>Aggiungere un dominio predefinito
Nella scheda **Domini predefiniti**, seleziona il dominio RestaurantReservation e fai clic su **Aggiungi dominio**. Una volta aggiunto il dominio predefinito all'app LUIS, apri **Finalità** e fai clic sulla finalità RestaurantReservation.Reserve. Noterai che molte espressioni di esempio sono già state fornite ed etichettate con le entità.

![Finalità RestaurantReservation.Reserve](./media/luis-how-to-prebuilt-domain-entities/prebuilt-domain-restaurant-reservation.png)


## <a name="designing-luis-apps-from-prebuilt-domains"></a>Progettazione di app LUIS a partire da domini predefiniti
Quando usi i domini predefiniti nell'app LUIS, puoi personalizzare un intero dominio predefinito o iniziare con alcune delle finalità e delle entità.

## <a name="customizing-an-entire-prebuilt-domain"></a>Personalizzazione di un intero dominio predefinito
I domini predefiniti sono ideati per essere generici. Contengono molte finalità ed entità da cui attingere per personalizzare un'app a proprio piacimento. Se in primo luogo personalizzi un intero dominio predefinito, elimina le finalità e le entità non necessarie alla tua app. Puoi inoltre aggiungere alcune finalità o entità al set già fornito dal dominio predefinito. Ad esempio, se usi il dominio predefinito **Eventi** per un'app di eventi sportivi, puoi aggiungere le entità per squadra. Quando inizi a [fornire espressioni](luis-how-to-add-example-utterances.md) a LUIS, includi i termini specifici per la tua app. LUIS impara a riconoscerli e adatta le finalità e le entità del dominio predefinito alle esigenze dell'applicazione. 

> [!TIP]
> Le finalità e le entità in un dominio predefinito hanno una maggiore efficacia quando operano in modo collaborativo. È consigliabile combinare le finalità e le entità provenienti dallo stesso dominio, quando possibile.
> * Una procedura consigliata consiste nell'usare le finalità correlate provenienti dallo stesso dominio. Ad esempio, se stai personalizzando la `MakeReservation` finalità nel dominio **Località**, seleziona la `Cancel` finalità dal dominio **Località** anziché la finalità Elimina nei domini **Eventi** oppure **Utilità**.

## <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Modifica del comportamento delle finalità di dominio predefinito
È possibile che un dominio predefinito contenga una finalità simile a un'altra di cui vuoi disporre nell'app LUIS, ma che vuoi operi in modo diverso. Ad esempio, il dominio predefinito **Località** fornisce una `MakeReservation` finalità per l'esecuzione di una prenotazione al ristorante, ma vuoi che l'app usi tale finalità per prenotare alberghi. In tal caso, è possibile modificare il comportamento di tale finalità fornendo a LUIS espressioni sulle prenotazioni di hotel e assegnandovi etichette tramite la `MakeReservation` finalità, quindi è possibile eseguire nuovamente il training di LUIS per riconoscere la `MakeReservation` finalità in una richiesta di prenotazione di un hotel.

> [!TIP]
> Estrai il dominio di Utilità per le finalità predefinite che è possibile personalizzare per l'uso in qualsiasi dominio. Ad esempio, puoi aggiungere `Utilities.Repeat` alla tua app ed eseguirvi il training per riconoscere qualsiasi azione che l'utente vuole ripetere nell'applicazione.


## <a name="next-step"></a>Passaggio successivo

Personalizza un dominio predefinito aggiungendo altre espressioni di esempio.

> [!div class="nextstepaction"]
> [Aggiungi espressioni di esempio](./luis-how-to-add-example-utterances.md)

## <a name="additional-resources"></a>Risorse aggiuntive

Vedi il [riferimento dei domini predefiniti](./luis-reference-prebuilt-domains.md) per altre informazioni sui domini predefiniti.
