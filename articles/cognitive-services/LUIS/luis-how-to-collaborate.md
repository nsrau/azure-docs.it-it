---
title: Collaborare con altri alle app LUIS in Azure | Microsoft Docs
description: Informazioni su come collaborare con altri alle applicazioni Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/05/2018
ms.author: diberry
ms.openlocfilehash: 9ea0269439b3d00bf36186cf2fd5c73311526bec
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225602"
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

Per altre informazioni sugli account utente Active Directory vedere [Tenant di Active Directory di Azure](luis-how-to-account-settings.md#azure-active-directory-tenant-user). 

## <a name="set-application-as-public"></a>Impostare l'applicazione come pubblica
Per altre informazioni, vedere [Accesso endpoint all'app pubblica](luis-concept-security.md#public-app-endpoint-access).

### <a name="transfer-of-ownership"></a>Trasferire la proprietà
LUIS attualmente non supporta il trasferimento della proprietà, ma è possibile esportare l'app e un altro utente LUIS può importarla. Potrebbero essere presenti differenze nei punteggi LUIS tra le due applicazioni. 
