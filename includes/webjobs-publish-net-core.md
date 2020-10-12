---
title: includere file
description: includere file
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: e208b52c67f173bd0d289715b63562df656b1ec9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009774"
---
1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**.

1. Nella finestra di dialogo **pubblica** selezionare **Azure** per **destinazione**e quindi fare clic su **Avanti**. 

1. Selezionare **processi** Web di Azure per **destinazione specifica**e quindi fare clic su **Avanti**.

1. Selezionare **Crea una nuova processo Web di Azure**.

   ![Seleziona destinazione di pubblicazione](./media/webjobs-publish-netcore/pick-publish-target.png)

1. Nella finestra di dialogo **servizio app (Windows)** usare le impostazioni di hosting riportate nella tabella seguente.

    | Impostazione      | Valore consigliato  | Descrizione                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome globalmente univoco | Nome che identifica in modo univoco la nuova app per le funzioni. |
    | **Sottoscrizione** | Scegliere la sottoscrizione | Sottoscrizione di Azure da usare. |
    | **[Gruppo di risorse](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  Nome del gruppo di risorse in cui creare l'app per le funzioni. Per creare un nuovo gruppo di risorse scegliere **Nuovo**.|
    | **[Piano di hosting](../articles/app-service/overview-hosting-plans.md)** | Piano di servizio app | Un [piano di servizio app](../articles/app-service/overview-hosting-plans.md) specifica la località, le dimensioni e le funzionalità della server farm Web che ospita l'app. Quando si ospitano più app, è possibile limitare i costi configurando le app Web in modo che condividano un singolo piano di servizio app. I piani di servizio app definiscono l'area, le dimensioni dell'istanza, il numero di scala e lo SKU (gratuito, condiviso, Basic, standard o Premium). Scegliere **nuovo** per creare un nuovo piano di servizio app. |

    ![Finestra di dialogo Crea servizio app](./media/webjobs-publish-netcore/app-service-dialog.png)

1. Selezionare **Crea** per creare una processo Web e le risorse correlate in Azure con queste impostazioni e distribuire il codice del progetto.