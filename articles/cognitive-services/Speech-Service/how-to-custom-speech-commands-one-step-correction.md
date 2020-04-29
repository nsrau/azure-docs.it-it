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
ms.openlocfilehash: 86a12bd1dccc2b6ac15010546d7e990b768ebc02
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75456454"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>Procedura: aggiungere una correzione in un passaggio a un comando personalizzato (anteprima)

In questo articolo si apprenderà come aggiungere una conferma in un passaggio a un comando.

La correzione in un solo passaggio viene usata per aggiornare un comando appena completato.

Ad esempio, se si è appena impostato un allarme, è possibile cambiare idea e aggiornare l'ora dell'allarme.

- Input: impostare la sveglia per domani a mezzogiorno
- Output: "OK, set di avvisi per 12/06/2019 12:00:00"
- Input: No, domani alle 13.00
- Output: "OK

Tenere presente che questo implica che gli sviluppatori hanno un meccanismo per aggiornare l'allarme nell'applicazione back-end.

## <a name="prerequisites"></a>Prerequisiti

È necessario avere completato i passaggi descritti negli articoli seguenti:

- [Guida introduttiva: creare un comando personalizzato (anteprima)](./quickstart-custom-speech-commands-create-new.md)
- [Guida introduttiva: creare un comando personalizzato con parametri (anteprima)](./quickstart-custom-speech-commands-create-parameters.md)
- [Procedura: aggiungere una conferma a un comando personalizzato (anteprima)](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-the-advanced-rules-for-one-step-correction"></a>Aggiungere le regole avanzate per la correzione di un solo passaggio 

Per dimostrare la correzione di un solo passaggio, si estenderà il comando **sealarm** creato nelle [procedure di conferma](./how-to-custom-speech-commands-confirmations.md).
 
1. Aggiungere una regola avanzata per aggiornare l'allarme precedente. 

    Questa regola chiederà all'utente di confermare la data e l'ora dell'allarme ed è prevista una conferma (sì/no) per la prossima volta.

   | Impostazione               | Valore consigliato                                                  | Descrizione                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Nome regola             | Aggiorna avviso precedente                                            | Nome che descrive lo scopo della regola          |
   | Condizioni            | UpdateLastCommand & parametro obbligatorio-DateTime                | Condizioni che determinano quando la regola può essere eseguita    |   
   | Azioni               | SpeechResponse-"-aggiornamento della sveglia precedente a {DateTime}"       | Azione da eseguire quando la condizione della regola è true |
   | Stato dopo l'esecuzione | Comando completo                                                 | Stato dell'utente dopo il turno                   |

1. Spostare la regola appena creata nella parte superiore delle regole avanzate (scorrere la regola nel pannello e fare clic sulla freccia rivolta verso l'alto).
   > [!div class="mx-imgBorder"]
   > ![Aggiungere una convalida dell'intervallo](media/custom-speech-commands/one-step-correction-rules.png)

> [!NOTE]
> In un'applicazione reale, nella sezione azioni di questa regola si invierà anche un'attività al client o si chiamerà un endpoint HTTP per aggiornare l'allarme nel sistema.

## <a name="try-it-out"></a>Procedura

Selezionare il pannello test e provare alcune interazioni.

- Input: impostare la sveglia per domani a mezzogiorno
- Output: "impostare un allarme per 12/07/2019 12:00:00?"
- Input: Sì
- Output: "OK, set di avvisi per 12/07/2019 12:00:00"
- Input: No, domani alle 13.00
- Output: "aggiornamento della sveglia precedente a 12/07/2019 13:00:00"
