---
title: Gestire IoT Central dal portale di Azure | Microsoft Docs
description: This article describes how to create and manage your IoT Central applications from the Azure portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/02/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 2133819ce7c298e2f73fdc5a68b80b64f9e72ea7
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480402"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Gestire IoT Central dal portale di Azure

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Instead of creating and managing IoT Central applications on the [Azure IoT Central application manager](https://aka.ms/iotcentral) website, you can use the [Azure portal](https://portal.azure.com) to manage your applications.

## <a name="create-iot-central-applications"></a>Creare applicazioni IoT Central

To create an application, navigate to the [Azure portal](https://ms.portal.azure.com) and select **Create a resource** in the main pane on the left.

![Portale di gestione: menu di spostamento](media/howto-manage-iot-central-from-portal/image0.png)

Nella barra di ricerca digitare **IoT Central**.

![Portale di gestione: ricerca](media/howto-manage-iot-central-from-portal/image0a1.png)

Select the **IoT Central Application** line-item in the search results.

![Portale di gestione: risultati della ricerca](media/howto-manage-iot-central-from-portal/image0b1.png)

Selezionare quindi **Crea**.

![Portale di gestione: risorsa IoT Central](media/howto-manage-iot-central-from-portal/image0c1.png)

Compilare tutti i campi del modulo. This form is similar to the form you fill out to create applications on the [Azure IoT Central application manager](https://aka.ms/iotcentral) website. Per altre informazioni, consultare la guida introduttiva [Creare un'applicazione Azure IoT Central](quick-deploy-iot-central.md).

You can create IoT Central application with general features by selecting **Sample Contoso**, **Custom application** and **Sample Devkits** as application templates, all the other application templates uses public preview features.

![create IoT Central form](media/howto-manage-iot-central-from-portal/image6a.png)

**Location** is the [geography](https://azure.microsoft.com/global-infrastructure/geographies/) where you’d like to create your application. Typically, you should choose the location that's physically closest to your devices to get optimal performance. Azure IoT Central is currently available in the **United States**, **Australia**, **Asia Pacific**, or in **Europe**.  Once you choose a location, you can't move your application to a different location later.

> [!NOTE]
> The preview application templates are currently only available in the **Europe** and **United States** locations.

![Portale di gestione: creare la risorsa IoT Central](media/howto-manage-iot-central-from-portal/image1a.png)  

After filling out all fields, select **Create**.

## <a name="manage-existing-iot-central-applications"></a>Gestire le applicazioni IoT Central esistenti

Se si dispone già di un'applicazione Azure IoT Central è possibile eliminarla, spostarla in una sottoscrizione diversa o in un altro gruppo di risorse nel portale di Azure.

> [!NOTE]
> Non è possibile visualizzare le applicazioni della versione di valutazione nel portale di Azure perché non sono associate alla sottoscrizione.

To get started, select **All resources** in the main pane on the left. Digitare il nome dell'applicazione nella casella di ricerca per individuarla nel proprio elenco di risorse. Then select the IoT Central application you'd like to manage.

![Portale di gestione: gestione delle risorse](media/howto-manage-iot-central-from-portal/image2a.png)

To navigate to the application, select the IoT Central Application URL.

![Portale di gestione: gestione delle risorse](media/howto-manage-iot-central-from-portal/image3.png)

To move the application to a different resource group, select **change** beside the resource group. Nella pagina **Move resources** (Sposta risorse) selezionare il gruppo di risorse nel quale si vuole eseguire la migrazione di questa applicazione.

![Portale di gestione: gestione delle risorse](media/howto-manage-iot-central-from-portal/image4a.png)

To move the application to a different subscription, select the **change** link beside the subscription. Nella finestra di dialogo visualizzata selezionare la sottoscrizione nella quale si vuole eseguire la migrazione di questa applicazione.

![Portale di gestione: gestione delle risorse](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire le applicazioni Azure IoT Central dal portale di Azure, il passaggio successivo suggerito è:

> [!div class="nextstepaction"]
> [Amministrare l'applicazione](howto-administer.md)