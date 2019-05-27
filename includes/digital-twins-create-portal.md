---
title: File di inclusione
description: File di inclusione
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 12/17/2018
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: 0c89c8353d410ddd54e8a59f3838c914702b96ca
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66153721"
---
1. Accedere al [portale di Azure](http://portal.azure.com).

1. Nel riquadro a sinistra selezionare **Crea una risorsa**. Cercare **gemelli digitali** e selezionare **Gemelli digitali (anteprima)**. Selezionare **Crea** per avviare il processo di distribuzione.

   ![Selezioni per la creazione di una nuova istanza di Gemelli digitali](./media/create-digital-twins-portal/create-digital-twins.png)

1. Nel riquadro **Gemelli digitali** immettere le informazioni seguenti:
   * **Nome risorsa**: Creare un nome univoco per l'istanza di Gemelli digitali.
   * **Sottoscrizione** scegliere la sottoscrizione da usare per creare questa istanza di Gemelli digitali. 
   * **Gruppo di risorse**: selezionare o creare un [gruppo di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) per l'istanza di Gemelli digitali.
   * **Località**: selezionare la località più vicina ai dispositivi.

     ![Riquadro Gemelli digitali con le informazioni immesse](./media/create-digital-twins-portal/create-digital-twins-param.png)

1. Rivedere le informazioni relative a Gemelli digitali e quindi selezionare **Crea**. La creazione dell'istanza di Gemelli digitali può richiedere qualche minuto. È possibile monitorare lo stato di avanzamento nel riquadro **Notifiche**.

1. Aprire il riquadro **Panoramica** dell'istanza di Gemelli digitali. Si noti il collegamento sotto **API Gestione**.

   Il formato dell'URL dell'**API di gestione** è `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger`. Questo URL consente di passare alla documentazione dell'API REST di Gemelli digitali di Azure appropriata per l'istanza. Per informazioni su come leggere e usare la documentazione di questa API, leggere [How to use Azure Digital Twins Swagger](../articles/digital-twins/how-to-use-swagger.md) (Come usare Swagger di Gemelli digitali).

    Modificare il formato dell'URL dell'**API di gestione** in questo modo: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. L'applicazione userà l'URL modificato come URL di base per l'accesso all'istanza. Copiare l'URL modificato in un file temporaneo. Questo URL servirà nella sezione successiva.

    ![API di gestione](./media/create-digital-twins-portal/digital-twins-management-api.png)