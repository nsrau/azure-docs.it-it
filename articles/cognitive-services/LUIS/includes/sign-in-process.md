---
title: File di inclusione
description: File di inclusione
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 10/30/2020
ms.topic: include
ms.openlocfilehash: e592c11062e81d48014a90895a0e42b460d4b77c
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128172"
---
## <a name="sign-in-to-luis-portal"></a>Accedere al portale LUIS

Un nuovo utente di LUIS deve seguire questa procedura:

1. Accedere al [portale LUIS](https://www.luis.ai), selezionare il paese/area geografica e accettare le condizioni per l'utilizzo. Se invece si visualizza **App personali** , significa che esiste già una risorsa LUIS ed è necessario procedere alla creazione di un'app. Sono disponibili due opzioni per l'iscrizione:

    * Uso di una risorsa di Azure (scelta consigliata): consente di collegare l'account LUIS a una risorsa Creazione di Azure nuova o esistente. Questa opzione equivale all'iscrizione già trasferita. Non sarà necessario eseguire il [processo di migrazione](../luis-migration-authoring.md#what-is-migration) in seguito.

    * Uso di una chiave di valutazione. Questa opzione consente di accedere a LUIS con una risorsa di valutazione che non è necessario configurare. Se si sceglie questa opzione, alla fine sarà necessario [eseguire la migrazione dell'account](../luis-migration-authoring.md#migration-steps) e collegare le applicazioni a una risorsa di creazione.

1. Nella finestra **Choose an authoring** (Scegliere una risorsa Creazione) visualizzata, trovare la sottoscrizione di Azure e la risorsa di creazione LUIS. Se non si ha una risorsa, è possibile crearne una nuova.

    :::image type="content" source="../media/luis-how-to-azure-subscription/choose-authoring-resource.png" alt-text="Scegliere un tipo di risorsa di creazione Language Understanding.":::
    
    Quando si crea una nuova risorsa di creazione, fornire le informazioni seguenti:
    * **Nome del tenant** : il tenant a cui è associata la sottoscrizione di Azure.
    * **Nome della sottoscrizione di Azure** : la sottoscrizione in cui verrà fatturata la risorsa.
    * **Nome del gruppo di risorse di Azure** : il nome del gruppo di risorse scelto o creato. I gruppi di risorse consentono di raggruppare le risorse di Azure per l'accesso e la gestione.
    * **Nome della risorsa di Azure** : un nome personalizzato che viene usato come parte dell'URL per le query dell'endpoint di creazione e previsione.
    * **Piano tariffario** : il piano tariffario determina il numero massimo di transazioni al secondo e al mese.


