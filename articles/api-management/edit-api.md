---
title: Modificare un'API con il portale di Azure | Microsoft Docs
description: Questa esercitazione illustra come usare Gestione API per modificare un'API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/08/2017
ms.author: apimpm
ms.openlocfilehash: 6f1a0cf6025cb3a398ab93320c6fcb69b1e62429
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60558098"
---
# <a name="edit-an-api"></a>Modificare un'API

I passaggi di questa esercitazione illustrano come usare Gestione API per modificare un'API. 

+ È possibile modificare l'API aggiungendo, eliminando e rinominando operazioni nell'istanza di Gestione API. 
+ È possibile modificare lo swagger dell'API.

## <a name="prerequisites"></a>Prerequisiti

+ [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md)
+ [Importare e pubblicare la prima API](import-and-publish.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="edit-an-api-in-apim"></a>Modificare un'API in Gestione API

![Modificare un'API](./media/edit-api/edit-api001.png)

1. Fare clic sulla scheda **API**.
2. Selezionare una delle API importate in precedenza.
3. Selezionare la scheda **Progettazione**.
4. Selezionare un'operazione che si vuole modificare.
5. Per rinominare l'operazione, selezionare la **matita** nella finestra **Front-end**.

## <a name="update-the-swagger"></a>Aggiornare lo swagger

È possibile aggiornare l'API back-end dal portale di Azure seguendo questa procedura:

1. Selezionare **Tutte le operazioni**.
2. Fare clic sulla matita nella finestra **Front-end**.

    ![Modificare un'API](./media/edit-api/edit-api002.png)

    Verrà visualizzato lo swagger dell'API.

    ![Modificare un'API](./media/edit-api/edit-api003.png)

3. Aggiornare lo swagger.
4. Premere **Salva**.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esempi di criteri di Gestione API](policy-samples.md)
> [Trasformare e proteggere un'API pubblicata](transform-api.md)