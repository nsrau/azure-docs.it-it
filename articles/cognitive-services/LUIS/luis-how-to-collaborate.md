---
title: Collaborare con altri alle app LUIS in Azure | Microsoft Docs
description: Informazioni su come collaborare con altri alle applicazioni Language Understanding (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/05/2018
ms.author: v-geberr
ms.openlocfilehash: c0451f7621a3c18dbf365f3a03934924c030092f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376905"
---
# <a name="collaborate-with-others-on-language-understanding-luis-apps"></a>Collaborare con altri alle app Language Understanding (LUIS)  

È possibile collaborare con altri a un'app LUIS. 

## <a name="owner-and-collaborators"></a>Proprietario e collaboratori
Un'app ha un singolo proprietario, ma può avere molti collaboratori. 

## <a name="add-collaborator"></a>Aggiungere un collaboratore

Per consentire ai collaboratori di modificare l'app LUIS nella pagina **Impostazioni** dell'app LUIS digitare l'indirizzo e-mail del collaboratore e fare clic su **Add collaborator** (Aggiungi collaboratore).

![Aggiungere un collaboratore](./media/luis-how-to-collaborate/add-collaborator.png)

* I collaboratori possano accedere all'app LUIS e modificarla contemporaneamente al creatore. <!--If a collaborator edits the LUIS app, you see a notification at the top of the browser.-->
* I collaboratori non possono aggiungere altri collaboratori.

## <a name="set-application-as-public"></a>Impostare l'applicazione come pubblica
Per altre informazioni, vedere [Accesso endpoint all'app pubblica](luis-concept-security.md#public-app-endpoint-access).

### <a name="transfer-of-ownership"></a>Trasferire la proprietà
LUIS attualmente non supporta il trasferimento della proprietà, ma è possibile esportare l'app e un altro utente LUIS può importarla. Potrebbero essere presenti differenze nei punteggi LUIS tra le due applicazioni. 
