---
title: File di inclusione
description: File di inclusione
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/05/2020
ms.topic: include
ms.openlocfilehash: fda9df6c7e9651bbd3b0b70ad9d47f23c0c19d01
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91541433"
---
## <a name="sign-in-to-luis-portal"></a>Accedere al portale LUIS

Un nuovo utente di LUIS deve seguire questa procedura:

1. Accedere al [portale LUIS](https://www.luis.ai), selezionare il paese o l'area geografica e accettare le condizioni per l'utilizzo. Se invece si visualizza **App personali**, significa che esiste già una risorsa LUIS ed è necessario procedere alla creazione di un'app. Per le aree supportate, vedere [Creazione e pubblicazione di aree e chiavi associate](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions).

1. Selezionare **Create Azure resource** (Crea risorsa di Azure) e quindi **Create an authoring resource to migrate your apps to** (Crea una risorsa di creazione in cui eseguire la migrazione delle app).

    ![Scegliere un tipo di risorsa di creazione Language Understanding](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Inserire i dettagli relativi alla risorsa.

    ![Screenshot che mostra l'opzione per la creazione di una nuova risorsa per il riquadro di creazione.](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    Durante la **creazione di una nuova risorsa di creazione** specificare le informazioni seguenti:

    * **Nome della risorsa**: nome personalizzato scelto dall'utente, che viene usato nell'URL per le query dell'endpoint di creazione e stima.
    * **Tenant**: tenant a cui è associata la sottoscrizione di Azure.
    * **Nome della sottoscrizione**: sottoscrizione a cui verrà fatturata la risorsa.
    * **Gruppo di risorse**: nome del gruppo di risorse scelto o creato. I gruppi di risorse consentono di raggruppare le risorse di Azure per l'accesso e la gestione.
    * **Località**: la località scelta dipende dalla selezione del **gruppo di risorse**.
    * **Piano tariffario**: il piano tariffario determina il numero massimo di transazioni al secondo e al mese.

1. Viene visualizzato un riepilogo della risorsa da creare. Selezionare **Avanti**.

    ![Screenshot che mostra la pagina iniziale con le opzioni per collegarsi all'account Azure.](../media/sign-in/sign-in-confirm-key-selection.png)

1. Per confermare, selezionare **Continua**.

    ![Screenshot che mostra la pagina iniziale dopo il collegamento all'account Azure.](../media/sign-in/sign-in-confirm-continue.png)
