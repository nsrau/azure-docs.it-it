---
title: 'Modello demo di Conversation Learner: reimpostazione della password - Servizi cognitivi Microsoft| Microsoft Docs'
titleSuffix: Azure
description: Informazioni su come creare un modello dimostrativo di apprendimento della conversazione.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f633dd375d690a1c3e66a2a6e02ae69665dbe960
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170634"
---
# <a name="demo-password-reset"></a>Demo: Reimpostazione della password
Questa demo illustra un semplice bot di supporto tecnico che può offrire assistenza nelle operazioni di reimpostazione della password. 

Viene mostrato come Conversation Learner può apprendere flussi di dialogo non elementari e sequenze di più turni, includendo una classe fuori dominio. Questa dimostrazione non usa codice o entità.

## <a name="video"></a>Video

[![Demo: Anteprima della password](http://aka.ms/cl-demo-password-preview)](http://aka.ms/blis-demo-password)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di reimpostazione della password sia in esecuzione.

    npm run demo-password

### <a name="open-the-demo"></a>Aprire la demo

Nell'elenco dei modelli dell'interfaccia utente Web fare clic su Tutorial Demo Password Reset. 

### <a name="actions"></a>Azioni

È stato creato un set di azioni per l'utente che ricerca assistenza per la password, includendo le soluzioni.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Dialoghi di training

Sono disponibili diversi dialoghi di training. Sono presenti anche dimostrazioni di una classe fuori dominio. Ad esempio, le richieste dell'utente come "driving directions" sono fuori dominio. Al bot sono stati forniti esempi di alcune richieste fuori dominio affinché possa rispondere "I can't help with that".

![](../media/tutorial_pw_reset_entities.PNG)

A titolo di esempio, si proverà una sessione di training.

1. Fare clic su Train Dialogs (Dialoghi di training) e quindi su New Train Dialog (Nuovo dialogo di training).
1. Immettere "I lost my password".
2. Fare clic su Score Action (Punteggio azione).
3. Fare clic per selezionare "Is that for your local account or Microsoft account?".
4. Immettere "Local account".
5. Fare clic su Score Actions (Punteggio azioni).
3. Fare clic per selezionare "Which version of Windows do you have?".
4. Immettere "Windows 8".
5. Fare clic su Score Actions (Punteggio azioni).
6. Selezionare "SOLUTION: How to reset password on Windows 8".
4. Fare clic su Done Teaching (Training completato).

Si proverà ora l'apprendimento nel bot di una classe fuori dominio.

1. Fare clic su Train Dialogs (Dialoghi di training) e quindi su New Train Dialog (Nuovo dialogo di training).
1. Immettere "web search".
    - Questo è un esempio di classe fuori dominio. 
2. Fare clic su Score Action (Punteggio azione).
3. Fare clic per selezionare "Sorry, I can't help with that".
    - Si noti che il punteggio di questa opzione è attualmente basso. Proseguendo un po' con il training, il punteggio diventerà più elevato.
4. Fare clic su Done Teaching (Training completato).

È stato illustrato come creare una demo di supporto tecnico di base e come può essere eseguito l'apprendimento per fornire soluzioni e gestire query fuori campione.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Demo: Ordinazione pizza](./demo-pizza-order.md)
