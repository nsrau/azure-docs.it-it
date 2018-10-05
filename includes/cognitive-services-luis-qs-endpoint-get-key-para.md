---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: fcf3bf29e2cdf89bdc93c7ebac313e5d9a9c18f0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47044016"
---
L'accesso all'endpoint di stima viene fornito con una chiave di endpoint. Ai fini di questa Guida introduttiva, usare la chiave iniziale gratuita associata all'account LUIS. 
 
1. Accedere utilizzando il proprio account LUIS. 

    [![Screenshot dell'elenco di app di LUIS (Language Understanding)](media/cognitive-services-luis/app-list.png "Screenshot dell'elenco di app di LUIS (Language Understanding)")](media/cognitive-services-luis/app-list.png)

2. Selezionare il nome dell'account nel menu in alto a destra, quindi selezionare **Impostazioni**.

    ![Accesso al menu delle impostazioni utente di LUIS](media/cognitive-services-luis/get-user-settings-in-luis.png)

3. Copiare il valore della **Chiave di creazione**. Sarà utile più avanti nella Guida introduttiva. 

    [![Screenshot delle impostazioni utenti di LUIS (Language Understanding)](media/cognitive-services-luis/get-user-authoring-key.png "Screenshot delle impostazioni utente di LUIS (Language Understanding)")](media/cognitive-services-luis/get-user-authoring-key.png)

    La chiave di creazione consente richieste illimitate gratuite per l'API di creazione e modifica e fino a 1000 query per l'API di endpoint di stima al mese per tutte le app LUIS. <!--Once the prediction endpoint quota from the authoring key is used for the month, you need to create a **Language Understanding** key from the Azure portal. The key created in the portal is known as the endpoint key. The endpoint key is used _only_ for prediction endpoint queries.-->