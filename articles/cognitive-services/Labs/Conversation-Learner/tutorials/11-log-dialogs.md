---
title: Come registrare i dialoghi in un modello di Conversation Learner - Servizi cognitivi Microsoft| Microsoft Docs
titleSuffix: Azure
description: Informazioni su come registrare i dialoghi in un modello di Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e3a84bfa643ebe74983bcef0d0ea72c701ffa589
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60707517"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-model"></a>Informazioni su come registrare i dialoghi in un modello di Conversation Learner

Questa esercitazione illustra come i dialoghi di log vengono usati per migliorare il training dei modelli di Conversation Learner partendo dalle interazioni registrate con gli utenti del mondo reale.

## <a name="video"></a>Video

[![Anteprima dell'esercitazione sui dialoghi di log](https://aka.ms/cl_Tutorial_v3_LogDialogs_Preview)](https://aka.ms/cl_Tutorial_v3_LogDialogs)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione

    npm run tutorial-general

e il modello del meteo creato nelle esercitazioni precedenti.

## <a name="details"></a>Dettagli
I dialoghi di log sono log registrati dell'interazione del bot con gli utenti finali. Sfruttando questi dialoghi di log è possibile correggere le etichette delle entità e le selezioni delle azioni in modo da migliorare le prestazioni del modello e del sistema complessivo.

## <a name="steps"></a>Passaggi

Nell'interfaccia utente Web fare clic su "Import tutorials" (Importa esercitazioni) e selezionare il modello denominato "Tutorial-11-LogDialogs".

Il modello contiene un'entità denominata "city" e le azioni progettate per rispondere alle richieste di informazioni sul meteo nella città indicata. Per eseguire il training del modello sono stati usati due dialoghi di training e quindi le aspettative in termini di prestazioni sono leggermente inferiori. Il modello migliorerà con un training aggiuntivo e con l'esposizione alle interazioni degli utenti del mondo reale.

### <a name="create-a-new-conversation"></a>Creare una nuova conversazione

1. Nel riquadro a sinistra fare clic su "Log Dialogs" (Dialoghi di log), quindi sul pulsante "New Log Dialog" (Nuovo dialogo di log).
2. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "Austin weather forecast".
3. Fare clic sul pulsante "Done Testing" (Test completato).
4. Fare clic sul dialogo di log "Austin weather forecast" nell'elenco.
5. Fare clic sull'espressione "Austin weather forecast" nel pannello della chat.
6. Fare clic su "Austin", quindi su "city" nell'elenco di entità.
7. Fare clic sul pulsante Save Changes (Invia modifiche).
    - Questa modifica del valore dell'entità comporta modifiche downstream nella conversazione poiché sono presenti nuovi valori delle entità in memoria. È probabile che le azioni successive siano diventate non valide, in particolare quelle relative all'entità "city".
8. Fare clic sull'espressione "Which city?" nel pannello della chat.
9. Selezionare la risposta "The weather in Austin is probably sunny".
10. Fare clic sul pulsante "Save As Train Dialog" (Salva come dialogo di training).
    - Il training viene avviato immediatamente.

![](../media/T11_logdialog.png)

Un'ultima nota: in base alle esigenze aziendali è possibile disattivare la funzione di registrazione delle conversazioni passando a Settings (Impostazioni) e deselezionando "Log Conversations" (Registra conversazioni).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Callback di rilevamento entità](./12-entity-detection-callback.md)
