---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 1effd07cef439a6257028549b7b7114b742eb478
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488811"
---
## <a name="sign-in-to-luis-portal"></a>Accedere al portale LUIS

Un nuovo utente di LUIS deve seguire questa procedura:

1. Accedere al [portale LUIS](https://www.luis.ai), selezionare il paese e accettare le condizioni per l'utilizzo.
1. Selezionare **Create Azure resource** (Crea risorsa di Azure) e quindi **Create an authoring resource to migrate your apps to** (Crea una risorsa di creazione in cui eseguire la migrazione delle app).

    ![Scegliere un tipo di risorsa di creazione Language Understanding](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Inserire i dettagli relativi alla risorsa.

    ![Creare la risorsa di creazione](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    Durante la **creazione di una nuova risorsa di creazione** specificare le informazioni seguenti: 

    * **Nome della risorsa**: nome personalizzato scelto dall'utente, che viene usato nell'URL per le query dell'endpoint di creazione e stima.
    * **Tenant**: tenant a cui è associata la sottoscrizione di Azure. 
    * **Nome della sottoscrizione**: sottoscrizione a cui verrà fatturata la risorsa.
    * **Gruppo di risorse**: nome del gruppo di risorse scelto o creato. I gruppi di risorse consentono di raggruppare le risorse di Azure per l'accesso e la gestione. 
    * **Località**: la località scelta dipende dalla selezione del **gruppo di risorse**.
    * **Piano tariffario**: il piano tariffario determina il numero massimo di transazioni al secondo e al mese.

1. Viene visualizzato un riepilogo della risorsa da creare. Selezionare **Avanti**.

    ![Creare la risorsa di creazione](../media/sign-in/sign-in-confirm-key-selection.png)

1. Per confermare, selezionare **Continua**. 

    ![Creare la risorsa di creazione](../media/sign-in/sign-in-confirm-continue.png)