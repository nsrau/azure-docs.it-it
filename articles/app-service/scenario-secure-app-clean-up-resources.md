---
title: 'Esercitazione: Pulire le risorse | Azure'
description: Questa esercitazione illustra come eseguire la pulizia delle risorse di Azure allocate durante la creazione dell'app Web.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 10/27/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: ab91ea7aa6e621dabc5cac83fe818dbf175214b6
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428273"
---
# <a name="tutorial-clean-up-resources"></a>Esercitazione: Pulire le risorse

Se sono stati completati tutti i passaggi di questa esercitazione in più parti, sono stati creati un servizio app, un piano di hosting del servizio app e un account di archiviazione in un gruppo di risorse.  È stata anche creata una registrazione app in Azure AD.  Quando non sono più necessarie, eliminare le risorse e la registrazione per non continuare ad accumulare costi.  

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Eliminare le risorse di Azure create durante l'esercitazione

## <a name="delete-the-resource-group"></a>Eliminare il gruppo di risorse.
Nel [portale di Azure](https://portal.azure.com) selezionare **Gruppi di risorse** dal menu del portale e selezionare il gruppo di risorse contenente il servizio app e il piano di servizio app.

Fare clic su **Elimina gruppo di risorse** per eliminare il gruppo e tutte le risorse al suo interno.

Eliminare un gruppo di risorse

L'esecuzione di questo comando potrebbe richiedere alcuni minuti.

## <a name="delete-the-app-registration"></a>Eliminare la registrazione app
Dal menu del portale selezionare **Azure Active Directory**, quindi **Registrazioni app** e infine l'applicazione creata.
:::image type="content" alt-text="Selezionare la registrazione app" source="./media/scenario-secure-app-clean-up-resources/select-app-registration.png":::

Nella panoramica della registrazione app selezionare **Elimina**.
:::image type="content" alt-text="Eliminare la registrazione app" source="./media/scenario-secure-app-clean-up-resources/delete-app-registration.png":::

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
>
> * Eliminare le risorse di Azure create durante l'esercitazione

Informazioni su come connettere un'[app .NET Core](tutorial-dotnetcore-sqldb-app.md), un'[app Python](tutorial-python-postgresql-app.md), un'[app Java](tutorial-java-spring-cosmosdb.md) o un'[app Node.js](tutorial-nodejs-mongodb-app.md) a un database.