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
ms.openlocfilehash: 4860532e59227618ce819772887556719ecb53fc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76020923"
---
1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**.

1. Nella finestra di dialogo **pubblica** selezionare **Microsoft Azure servizio app**, scegliere **Crea nuovo**e quindi selezionare **pubblica**.

   ![Seleziona destinazione di pubblicazione](./media/webjobs-publish-netcore/pick-publish-target.png)

1. Nella finestra di dialogo **Crea servizio app** usare le impostazioni di hosting come specificato nella tabella sotto l'immagine:

    ![Finestra di dialogo Crea servizio app](./media/webjobs-publish-netcore/app-service-dialog.png)

    | Impostazione      | Valore consigliato  | Description                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome app** | Nome globalmente univoco | Nome che identifica in modo univoco la nuova app per le funzioni. |
    | **Sottoscrizione** | Scegliere la sottoscrizione | Sottoscrizione di Azure da usare. |
    | **[Gruppo di risorse](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  Nome del gruppo di risorse in cui creare l'app per le funzioni. Per creare un nuovo gruppo di risorse scegliere **Nuovo**.|
    | **[Piano di hosting](../articles/app-service/overview-hosting-plans.md)** | Piano di servizio app | Un [piano di servizio app](../articles/app-service/overview-hosting-plans.md) specifica la località, le dimensioni e le funzionalità della server farm Web che ospita l'app. Quando si ospitano più app, è possibile limitare i costi configurando le app Web in modo che condividano un singolo piano di servizio app. I piani di servizio app definiscono l'area, le dimensioni dell'istanza, il numero di scala e lo SKU (gratuito, condiviso, Basic, standard o Premium). Scegliere **nuovo** per creare un nuovo piano di servizio app. |

1. Fare clic su **Crea** per creare una processo Web e le risorse correlate in Azure con queste impostazioni e distribuire il codice del progetto.