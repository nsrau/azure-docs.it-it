---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 14678a143e1b14b9b0b89435f356ac5df98ef40c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478684"
---
L'accesso all'endpoint di stima viene fornito con una chiave di endpoint. Ai fini di questa Guida introduttiva, usare la chiave iniziale gratuita associata all'account LUIS. 
 
1. Accedere utilizzando il proprio account LUIS. 

    [![Screenshot dell'elenco di app di LUIS (Language Understanding)](media/cognitive-services-luis/app-list.png "Screenshot dell'elenco di app di LUIS (Language Understanding)")](media/cognitive-services-luis/app-list.png)

2. Selezionare il nome dell'account nel menu in alto a destra, quindi selezionare **Impostazioni**.

    ![Accesso al menu delle impostazioni utente di LUIS](media/cognitive-services-luis/get-user-settings-in-luis.png)

3. Copiare il valore della **Chiave di creazione**. Sarà utile più avanti nella Guida introduttiva. 

    [![Screenshot delle impostazioni utenti di LUIS (Language Understanding)](media/cognitive-services-luis/get-user-authoring-key.png "Screenshot delle impostazioni utente di LUIS (Language Understanding)")](media/cognitive-services-luis/get-user-authoring-key.png)

    La chiave di creazione consente richieste illimitate gratuite per l'API di creazione e modifica e fino a 1000 query per l'API di endpoint di stima al mese per tutte le app LUIS. <!--Once the prediction endpoint quota from the authoring key is used for the month, you need to create a **Language Understanding** key from the Azure portal. The key created in the portal is known as the endpoint key. The endpoint key is used _only_ for prediction endpoint queries.-->
