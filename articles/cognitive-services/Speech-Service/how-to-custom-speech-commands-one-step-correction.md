---
title: Aggiungere la correzione in un solo passaggio nell'anteprima comandi personalizzati-servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come aggiungere una correzione in un passaggio per un comando in un'app di anteprima dei comandi personalizzati.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 05f63ba4e70f649df33905f1e92fb1fab866a86c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310428"
---
# <a name="add-a-one-step-correction-to-a-custom-command-in-a-custom-commands-preview-application"></a>Aggiungere una correzione in un passaggio a un comando personalizzato in un'applicazione di anteprima di comandi personalizzati

In questo articolo si apprenderà come aggiungere una conferma in un passaggio a un comando in un'app di anteprima di comandi personalizzati.

La correzione in un solo passaggio viene usata per aggiornare un comando appena completato. Quando si aggiunge una correzione in un passaggio a un comando di allarme, è possibile cambiare idea e aggiornare l'ora dell'allarme. Ad esempio:

- Input: impostare la sveglia per domani a mezzogiorno
- Output: OK, set di avvisi per 2020-05-02 12:00:00
- Input: No, domani alle 13.00
- Output: OK

> [!NOTE]
> In uno scenario reale, il client esegue un'azione in seguito al completamento del comando. Questo articolo presuppone che si disponga di un meccanismo per aggiornare l'allarme nell'applicazione back-end.

## <a name="prerequisites"></a>Prerequisiti

Completare i passaggi descritti negli articoli seguenti:
> [!div class="checklist"]

> * [Guida introduttiva: creare un'app di anteprima per i comandi personalizzati](./quickstart-custom-speech-commands-create-new.md)
> * [Guida introduttiva: creare un'app di anteprima dei comandi personalizzati con i parametri](./quickstart-custom-speech-commands-create-parameters.md)
> * [Procedura: aggiungere conferme a un comando in un'app di anteprima di comandi personalizzati](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-interaction-rules-for-one-step-correction"></a>Aggiungere regole di interazione per la correzione di un solo passaggio

Per dimostrare la correzione di un solo passaggio, estendere il comando **sealarm** creato in [procedura: aggiungere una conferma a un comando nell'anteprima dei comandi personalizzati](./how-to-custom-speech-commands-confirmations.md).

1. Aggiungere una regola di interazione per aggiornare il comando **sealarm** .

    Questa regola chiede all'utente di confermare la data e l'ora dell'allarme e prevede una conferma (sì/no) per la prossima volta.

   | Impostazione               | Valore consigliato                                                  | Descrizione                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | **Nome regola**             | **Aggiorna avviso precedente**                                            | Nome che descrive lo scopo della regola          |
   | **Condizioni**            | **Il comando precedente deve essere aggiornato & parametro obbligatorio-> DateTime**                | Condizioni che determinano quando la regola può essere eseguita    |   
   | **Actions**               | **Inviare risposta vocale-> editor semplice-> aggiornare la sveglia precedente a {DateTime}**      | Azione da eseguire quando le condizioni della regola sono true |
   | **Stato di post-esecuzione** | **Comando completato**        | Stato dell'utente dopo il turno                   |

1. Nel riquadro selezionare la regola di interazione appena creata. Selezionare il pulsante con i puntini di sospensione (**...**) nell'angolo superiore sinistro del riquadro. Usare quindi la freccia **Sposta su** per spostare la regola nella parte superiore dell'elenco delle **regole di interazione** .
   > [!div class="mx-imgBorder"]
   > ![Aggiungere una convalida dell'intervallo](media/custom-speech-commands/one-step-correction-rules.png)

    > [!NOTE]
    > In un'applicazione reale, usare la sezione **Actions** per restituire un'attività al client o chiamare un endpoint HTTP per aggiornare l'allarme nel sistema.

## <a name="try-it-out"></a>Procedura

1. Selezionare il pulsante **Train** (Esegui il training).

1. Al termine del training, selezionare **test**, quindi provare le interazioni seguenti:

   - Input: impostare la sveglia per domani a mezzogiorno
   - Output: impostare un allarme per 2020-05-02 12:00:00
   - Input: Sì
   - Output: OK, set di avvisi per 2020-05-02 12:00:00
   - Input: No, domani alle 14:00
   - Output: aggiornamento della sveglia precedente a 2020-05-02 14:00:00
