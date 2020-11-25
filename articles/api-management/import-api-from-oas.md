---
title: Importare una specifica OpenAPI usando il portale di Azure | Microsoft Docs
description: Informazioni su come importare una specifica OpenAPI con Gestione API e quindi testare l'API nel portale di Azure e nel portale per sviluppatori.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 05295efec3d7651c3a77dd5ad6e2c72b57a6c5a3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994700"
---
# <a name="import-an-openapi-specification"></a>Importare una specifica OpenAPI

Questo articolo illustra come importare un'API back-end "Specifica OpenAPI" che risiede in https://conferenceapi.azurewebsites.net?format=json. Questa API back-end è fornita da Microsoft e ospitata in Azure. e come testare l'API di Gestione API.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Importare un'API back-end "Specifica OpenAPI"
> * Testare l'API nel portale di Azure
> * Testare l'API nel portale per sviluppatori

## <a name="prerequisites"></a>Prerequisiti

Completare l'avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importare e pubblicare un'API back-end

1. Passare al servizio Gestione API nel portale di Azure e selezionare **API** dal menu.
2. Selezionare **Specifica OpenAPI** dall'elenco **Add a new API** (Aggiungere una nuova API).

    ![Specifica OpenAPI](./media/import-api-from-oas/oas-api.png)
3. Immettere le impostazioni API. È possibile impostare i valori durante la creazione o configurarli successivamente passando alla scheda **Impostazioni**. Le impostazioni sono illustrate nell'esercitazione [Importare e pubblicare la prima API](import-and-publish.md#import-and-publish-a-backend-api).
4. Selezionare **Create** (Crea).

> [!NOTE]
> Le limitazioni relative all'importazione delle API sono documentate in [un altro articolo](api-management-api-import-restrictions.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Trasformare e proteggere un'API pubblicata](transform-api.md)
