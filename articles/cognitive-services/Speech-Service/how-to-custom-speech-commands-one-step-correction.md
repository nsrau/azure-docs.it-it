---
title: 'Procedura: aggiungere una correzione in un passaggio a un comando personalizzato (anteprima) - Servizio di riconoscimento vocaleHow to: Add a one-step correction to a custom command (Preview) - Speech service'
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come implementare correzioni in un passaggio per un comando nei comandi personalizzati.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 86a12bd1dccc2b6ac15010546d7e990b768ebc02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456454"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>Procedura: Aggiungere una correzione in un solo passaggio a un comando personalizzato (anteprima)How To: Add a one-step correction to a Custom Command (Preview)

In questo articolo verrà illustrato come aggiungere la conferma di un passaggio a un comando.

La correzione in un solo passaggio viene utilizzata per aggiornare un comando appena completato.

Ad esempio, se si imposta un allarme, è possibile cambiare idea e aggiornare l'ora dell'allarme.

- Ingresso: Impostare l'allarme per domani a mezzogiorno
- Uscita: "Ok, allarme impostato per 12/06/2019 12:00:00"
- Ingresso: No, domani alle 13:00
- Uscita: "Ok

Tieni presente che ciò implica che tu come sviluppatore hai un meccanismo per aggiornare l'allarme nell'applicazione back-end.

## <a name="prerequisites"></a>Prerequisiti

È necessario aver completato i passaggi nei seguenti articoli:

- [Guida introduttiva: Creare un comando personalizzato (anteprima)Quickstart: Create a Custom Command (Preview)](./quickstart-custom-speech-commands-create-new.md)
- [Guida introduttiva: Creare un comando personalizzato con parametri (anteprima)Quickstart: Create a Custom Command with Parameters (Preview)](./quickstart-custom-speech-commands-create-parameters.md)
- [Procedura: aggiungere una conferma a un comando personalizzato (anteprima)How To: Add a confirmation to a Custom Command (Preview)](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-the-advanced-rules-for-one-step-correction"></a>Aggiungere le regole avanzate per la correzione in un passaggio 

Per dimostrare la correzione in un solo passaggio, estendiamo il comando **SetAlarm** creato nella procedura di [conferma](./how-to-custom-speech-commands-confirmations.md).
 
1. Aggiungere una regola avanzata per aggiornare l'allarme precedente. 

    Questa regola chiederà all'utente di confermare la data e l'ora dell'allarme e si aspetta una conferma (sì/no) per il turno successivo.

   | Impostazione               | Valore consigliato                                                  | Descrizione                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Nome regola             | Aggiorna allarme precedente                                            | Un nome che descriva lo scopo della regola          |
   | Condizioni            | UpdateLastCommand & parametro obbligatorio - DateTime                | Condizioni che determinano quando la regola può essere eseguita    |   
   | Azioni               | SpeechResponse - "- Aggiornamento dell'allarme precedente a "DateTime"       | Azione da eseguire quando la condizione della regola è vera |
   | Stato dopo l'esecuzione | Comando Completa                                                 | Stato dell'utente dopo il turno                   |

1. Spostare la regola appena creata nella parte superiore delle regole avanzate (scorrere la regola nel pannello e fare clic sulla freccia SU).
   > [!div class="mx-imgBorder"]
   > ![Aggiungere una convalida dell'intervalloAdd a range validation](media/custom-speech-commands/one-step-correction-rules.png)

> [!NOTE]
> In un'applicazione reale, nella sezione Azioni di questa regola si invierà anche un'attività al client o si chiamerà un endpoint HTTP per aggiornare l'allarme nel sistema.

## <a name="try-it-out"></a>Provare il servizio

Selezionate il pannello Test e provate alcune interazioni.

- Ingresso: Impostare l'allarme per domani a mezzogiorno
- Uscita: "Sei sicuro di voler impostare un allarme per 12/07/2019 12:00:00?"
- Ingresso: Sì
- Uscita: "Ok, allarme impostato per 12/07/2019 12:00:00"
- Ingresso: No, domani alle 13:00
- Uscita: "Aggiornamento dell'allarme precedente a 12/07/2019 13:00:00"
