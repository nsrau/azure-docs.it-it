---
title: File di inclusione
description: File di inclusione
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 97387e24d5b55c1438a69da1a1fd0a9bc1720e47
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66136172"
---
1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**.

1. Nel **Publish** finestra di dialogo, seleziona **Microsoft Azure App Service**, scegliere **Crea nuovo**e quindi selezionare **pubblica**.

   ![Selezione destinazione di pubblicazione](./media/webjobs-publish-netcore/pick-publish-target.png)

1. Nel **Crea servizio App** finestra di dialogo, usare le impostazioni di hosting specificate nella tabella sotto l'immagine:

    ![Finestra di dialogo Crea servizio app](./media/webjobs-publish-netcore/app-service-dialog.png)

    | Impostazione      | Valore consigliato  | Descrizione                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome app** | Nome globalmente univoco | Nome che identifica in modo univoco la nuova app per le funzioni. |
    | **Sottoscrizione** | Scegliere la sottoscrizione | Sottoscrizione di Azure da usare. |
    | **[Gruppo di risorse](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Nome del gruppo di risorse in cui creare l'app per le funzioni. Per creare un nuovo gruppo di risorse scegliere **Nuovo**.|
    | **[Piano di hosting](../articles/app-service/overview-hosting-plans.md)** | Piano di servizio app | Un [piano di servizio app](../articles/app-service/overview-hosting-plans.md) specifica la località, le dimensioni e le funzionalità della server farm Web che ospita l'app. Quando si ospitano più app, è possibile limitare i costi configurando le app Web in modo che condividano un singolo piano di servizio app. I piani di servizio App definiscono l'area, dimensioni dell'istanza, numero di scala e SKU (gratuito, condiviso, Basic, Standard o Premium). Scegli **New** per creare un nuovo piano di servizio App. |

1. Fare clic su **Create** per creare un processo Web e le relative risorse in Azure con queste impostazioni e distribuire il codice del progetto.