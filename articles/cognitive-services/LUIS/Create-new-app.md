---
title: Creazione di una nuova applicazione con LUIS | Microsoft Docs
description: Creare e gestire le applicazioni nella pagina Web di Language Understanding (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/17/2018
ms.author: v-geberr
ms.openlocfilehash: 998a85720f5707fbf6ed4c5cfa3ed0dab5d1cc0e
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865046"
---
# <a name="create-an-app"></a>Creare un'applicazione
È possibile creare una nuova applicazione in modi diversi: 

* [Inizia](#create-new-app) con un'applicazione vuota e crea finalità, espressioni ed entità.
* [Inizia](#create-new-app) con un'applicazione vuota e aggiungi un [dominio predefinito](luis-how-to-use-prebuilt-domains.md).
* [Importa un'applicazione LUIS](#import-new-app) da un file JSON che contiene già finalità, espressioni ed entità.

## <a name="what-is-an-app"></a>Definizione dell'applicazione
L'applicazione contiene [versioni](luis-how-to-manage-versions.md) del modello, nonché qualsiasi [collaboratore](luis-how-to-collaborate.md) dell'applicazione. Quando crei l'applicazione, selezioni le impostazioni cultura ([linguaggio](luis-supported-languages.md)) che **non possono essere modificate successivamente**. 

La versione predefinita di una nuova applicazione è "0.1" 

È possibile creare e gestire le applicazioni nella pagina **App personali**. È sempre possibile accedere a questa pagina selezionando **App personali** dalla barra di navigazione superiore del sito Web [LUIS](luis-reference-regions.md). 

[![](media/luis-create-new-app/apps-list.png "Schermata dell'elenco di applicazioni")](media/luis-create-new-app/apps-list.png#lightbox)

## <a name="create-new-app"></a>Creare una nuova applicazione

1. Nella pagina **App personali**, seleziona **Crea una nuova applicazione**.
2. Nella finestra di dialogo, assegna all'applicazione il nome di "TravelAgent".

    ![Creare una nuova finestra di dialogo dell'applicazione](./media/luis-create-new-app/create-app.png)

3. Scegli le impostazioni cultura dell'applicazione (per l'applicazione TravelAgent, scegli la lingua inglese), quindi seleziona **Fine**. 

    >[!NOTE]
    >Le impostazioni cultura non possono essere modificate dopo la creazione dell'applicazione. 

## <a name="import-new-app"></a>Importare una nuova applicazione
È possibile impostare il nome (50 caratteri al massimo), la versione (10 caratteri al massimo) e la descrizione di un'applicazione nel file JSON. Esempi di file JSON dell'applicazione sono disponibili in [LUIS-Esempi](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/Examples-BookFlight).

1. Nella pagina **App personali**, seleziona **Importa una nuova applicazione**.
2. Nella finestra di dialogo **Importa una nuova applicazione**, seleziona il file JSON che definisce l'applicazione LUIS.

    ![Importare una nuova finestra di dialogo dell'applicazione](./media/luis-create-new-app/import-app.png)

## <a name="export-app"></a>Esportare l'applicazione
1. Nella pagina **App personali** seleziona i puntini di sospensione (***...***) alla fine della riga dell'applicazione.

    [![](media/luis-create-new-app/apps-list.png "Schermata della finestra di dialogo popup delle azioni per applicazione")](media/luis-create-new-app/three-dots.png#lightbox)

2. Seleziona **Esporta l'applicazione** dal menu. 

## <a name="rename-app"></a>Rinominare l'applicazione

1. Nella pagina **App personali** seleziona i puntini di sospensione (***...***) alla fine della riga dell'applicazione. 
2. Seleziona **Rinomina** dal menu.
3. Inserisci il nuovo nome dell'applicazione e seleziona **Fine**.

## <a name="delete-app"></a>Eliminare l'applicazione

> [!CAUTION]
> Stai provando a eliminare l'applicazione per tutti i collaboratori e il proprietario. [Esporta](#export-app) l'applicazione prima di eliminarla. 

1. Nella pagina **App personali** seleziona i puntini di sospensione (***...***) alla fine della riga dell'applicazione. 
2. Seleziona **Elimina** dal menu.
3. Nella finestra di conferma fai clic su **Ok**.

## <a name="export-endpoint-logs"></a>Esportare i log di endpoint
I log contengono Query, Ora UTC e Risposta LUIS JSON.

1. Nella pagina **App personali** seleziona i puntini di sospensione (***...***) alla fine della riga dell'applicazione. 
2. Seleziona **Esporta i log di endpoint** dal menu.

```
Query,UTC DateTime,Response
text i'm driving and will be 30 minutes late to the meeting,02/13/2018 15:18:43,"{""query"":""text I'm driving and will be 30 minutes late to the meeting"",""intents"":[{""intent"":""None"",""score"":0.111048922},{""intent"":""SendMessage"",""score"":0.987501}],""entities"":[{""entity"":""i ' m driving and will be 30 minutes late to the meeting"",""type"":""Message"",""startIndex"":5,""endIndex"":58,""score"":0.162995353}]}"
```

## <a name="next-steps"></a>Passaggi successivi

La prima attività dell'applicazione consiste nell'[aggiungere finalità](luis-how-to-add-intents.md).