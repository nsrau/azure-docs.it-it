---
title: 'Procedura: aggiungere una correzione in un passaggio a un comando personalizzato (anteprima)-servizio di riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come implementare correzioni in un solo passaggio per un comando nei comandi personalizzati.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: f43c28d314cb8a0211496664cd20d2c380e4d5b0
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858261"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>Procedura: aggiungere una correzione in un passaggio a un comando personalizzato (anteprima)

In questo articolo si apprenderà come aggiungere una conferma in un passaggio a un comando.

La correzione in un solo passaggio viene usata per aggiornare un comando appena completato. Ovvero, se si è appena impostato un allarme, è possibile cambiare idea e aggiornare l'ora dell'allarme. Un esempio di questo caso è il seguente:

- Input: impostare la sveglia per domani a mezzogiorno
- Output: OK, set di avvisi per 2020-05-02 12:00:00
- Input: No, domani alle 13.00
- Output: OK

Uno scenario reale, che in genere è accompagnato dall'esecuzione di un'azione da parte del client in seguito al completamento del comando. in questo articolo si presuppone che gli sviluppatori abbiano un meccanismo per aggiornare l'allarme nell'applicazione back-end.

## <a name="prerequisites"></a>Prerequisiti

È necessario avere completato i passaggi descritti negli articoli seguenti:
> [!div class="checklist"]

> * [Guida introduttiva: creare un comando personalizzato (anteprima)](./quickstart-custom-speech-commands-create-new.md)
> * [Guida introduttiva: creare un comando personalizzato con parametri (anteprima)](./quickstart-custom-speech-commands-create-parameters.md)
> * [Procedura: aggiungere una conferma a un comando personalizzato (anteprima)](./how-to-custom-speech-commands-confirmations.md)


## <a name="add-interaction-rules-for-one-step-correction"></a>Aggiungere regole di interazione per la correzione di un solo passaggio 

Per dimostrare la correzione di un solo passaggio, si estenderà il comando **sealarm** creato in [procedura: aggiungere una conferma a un comando personalizzato (anteprima)](./how-to-custom-speech-commands-confirmations.md).
1. Aggiungere una regola di interazione per aggiornare l'allarme impostato in precedenza. 

    Questa regola chiederà all'utente di confermare la data e l'ora dell'allarme ed è prevista una conferma (sì/no) per la prossima volta.

   | Impostazione               | Valore consigliato                                                  | Descrizione                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Nome regola             | Aggiorna avviso precedente                                            | Nome che descrive lo scopo della regola          |
   | Condizioni            | Il comando precedente deve essere aggiornato & parametro obbligatorio-> DateTime                | Condizioni che determinano quando la regola può essere eseguita    |   
   | Azioni               | Inviare risposta vocale-> editor semplice-> aggiornare la sveglia precedente a {DateTime}      | Azione da eseguire quando le condizioni della regola sono true |
   | Stato di post-esecuzione | Comando completato        | Stato dell'utente dopo il turno                   |

1. Spostare la regola appena creata nella parte superiore delle regole di interazione (selezionare la regola nel pannello e fare clic sulla freccia verso l'alto `...` presente sotto l'icona nella parte superiore del riquadro centrale).
   > [!div class="mx-imgBorder"]
   > ![Aggiungere una convalida dell'intervallo](media/custom-speech-commands/one-step-correction-rules.png)
    > [!NOTE]
    > In un'applicazione reale, nella sezione azioni di questa regola si invierà anche un'attività al client o si chiamerà un endpoint HTTP per aggiornare l'allarme nel sistema.

## <a name="try-it-out"></a>Procedura

Selezionare `Train`, attendere il completamento del training e selezionare `Test`.

- Input: impostare la sveglia per domani a mezzogiorno
- Output: impostare un allarme per 2020-05-02 12:00:00
- Input: Sì
- Output: OK, set di avvisi per 2020-05-02 12:00:00
- Input: No, domani alle 14:00
- Output: aggiornamento della sveglia precedente a 2020-05-02 14:00:00
