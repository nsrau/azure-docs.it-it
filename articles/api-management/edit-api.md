---
title: Modificare un'API con il portale di Azure | Microsoft Docs
description: Informazioni su come usare Gestione API per modificare un'API. Aggiungere, eliminare o rinominare operazioni nell'istanza di Gestione API o modificare lo swagger dell'API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 11/08/2017
ms.author: apimpm
ms.openlocfilehash: 8d3f476f4a6ecf05d63223f3ba2c2b5173e1d0bf
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905160"
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