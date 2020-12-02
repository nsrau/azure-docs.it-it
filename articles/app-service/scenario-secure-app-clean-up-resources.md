---
title: 'Esercitazione: Pulire le risorse | Azure'
description: Questa esercitazione illustra come pulire le risorse di Azure allocate durante la creazione dell'app Web.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 10/27/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: 64421dba5578b478a5fdf0c657614770baf9d735
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025362"
---
# <a name="tutorial-clean-up-resources"></a>Esercitazione: Pulire le risorse

Se sono stati completati tutti i passaggi di questa esercitazione in più parti, sono stati creati un servizio app, un piano di hosting del servizio app e un account di archiviazione in un gruppo di risorse. È stata anche creata una registrazione app in Azure Active Directory. Quando non sono più necessarie, eliminare le risorse e la registrazione per non continuare ad accumulare costi.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Eliminare le risorse di Azure create durante l'esercitazione.

## <a name="delete-the-resource-group"></a>Eliminare il gruppo di risorse.

Nel [portale di Azure](https://portal.azure.com) selezionare **Gruppi di risorse** dal menu del portale e selezionare il gruppo di risorse contenente il servizio app e il piano di servizio app.

Fare clic su **Elimina gruppo di risorse** per eliminare il gruppo e tutte le risorse al suo interno.

:::image type="content" alt-text="Screenshot che mostra l'eliminazione del gruppo di risorse." source="./media/scenario-secure-app-clean-up-resources/delete-resource-group.png":::

L'esecuzione di questo comando potrebbe richiedere diversi minuti.

## <a name="delete-the-app-registration"></a>Eliminare la registrazione app

Nel menu del portale selezionare **Azure Active Directory** > **Registrazioni app**. Selezionare quindi l'applicazione creata.
:::image type="content" alt-text="Screenshot che mostra la selezione della registrazione app." source="./media/scenario-secure-app-clean-up-resources/select-app-registration.png":::

Nella panoramica della registrazione app selezionare **Elimina**.
:::image type="content" alt-text="Screenshot che mostra l'eliminazione della registrazione app." source="./media/scenario-secure-app-clean-up-resources/delete-app-registration.png":::

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
>
> * Eliminare le risorse di Azure create durante l'esercitazione.

Informazioni su come connettere un'[app .NET Core](tutorial-dotnetcore-sqldb-app.md), un'[app Python](tutorial-python-postgresql-app.md), un'[app Java](tutorial-java-spring-cosmosdb.md) o un'[app Node.js](tutorial-nodejs-mongodb-app.md) a un database.