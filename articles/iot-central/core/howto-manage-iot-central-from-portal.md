---
title: Gestire IoT Central dal portale di Azure | Microsoft Docs
description: Questo articolo descrive come creare e gestire le applicazioni ioT Central dal portale di Azure.This article describes how to create and manage your IoT Central applications from the Azure portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: e7efda8efa27044168386e3ebbc557bf7fb74e8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157926"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Gestire IoT Central dal portale di Azure

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Anziché creare e gestire applicazioni IoT Central nel sito Web della gestione delle applicazioni di [Azure IoT Central,](https://aka.ms/iotcentral) è possibile usare il portale di [Azure](https://portal.azure.com) per gestire le applicazioni.

## <a name="create-iot-central-applications"></a>Creare applicazioni IoT Central

Per creare un'applicazione, passare al portale di [Azure](https://ms.portal.azure.com) e selezionare **Crea una risorsa**.

Nella barra **Cerca nel Marketplace** digitare *IoT Central:*

![Portale di gestione: ricerca](media/howto-manage-iot-central-from-portal/image0a1.png)

Selezionare il **riquadro Applicazione centrale IoT** nei risultati della ricerca:

![Portale di gestione: risultati della ricerca](media/howto-manage-iot-central-from-portal/image0b1.png)

A questo punto, selezionare **Crea**:

![Portale di gestione: risorsa IoT Central](media/howto-manage-iot-central-from-portal/image0c1.png)

Compilare tutti i campi del modulo. Questo modulo è simile al modulo compilato per creare applicazioni nel sito Web di Gestione applicazioni di Azure IoT Central.This form is similar to the form you comp out to create applications on the [Azure IoT Central application manager](https://aka.ms/iotcentral) website. Per altre informazioni, consultare la guida introduttiva [Creare un'applicazione Azure IoT Central](quick-deploy-iot-central.md).

![Crea modulo IoT Central](media/howto-manage-iot-central-from-portal/image6a.png)

La **località** è l'[area geografica](https://azure.microsoft.com/global-infrastructure/geographies/) in cui si vuole creare l'applicazione. Per ottenere prestazioni ottimali, è in genere consigliabile scegliere la località fisicamente più vicina ai dispositivi. Azure IoT Central è attualmente disponibile nelle aree geografiche **Australia**, **Asia Pacifico**, **Europa**, **Stati Uniti**, **Regno Unito**e **Giappone.** Dopo aver scelto una località, non è possibile spostare l'applicazione in un'altra località in un secondo momento.

Dopo aver compilato tutti i campi, selezionare **Crea**.

## <a name="manage-existing-iot-central-applications"></a>Gestire le applicazioni IoT Central esistenti

Se si dispone già di un'applicazione Azure IoT Central è possibile eliminarla, spostarla in una sottoscrizione diversa o in un altro gruppo di risorse nel portale di Azure.

> [!NOTE]
> Non è possibile visualizzare le applicazioni create nel piano tariffario gratuito nel portale di Azure perché non sono associate alla sottoscrizione.

Per iniziare, selezionare **Tutte le risorse** nel portale. Selezionare **Mostra tipi nascosti** e iniziare a digitare il nome dell'applicazione in **Filtra per nome** per trovarla. Quindi seleziona l'applicazione IoT Central che vuoi gestire.

Per passare all'applicazione, selezionare **l'URL dell'applicazione centrale IoT**:

![Portale di gestione: gestione delle risorse](media/howto-manage-iot-central-from-portal/image3.png)

Per spostare l'applicazione in un gruppo di risorse diverso, selezionare **modifica** accanto al gruppo di risorse. Nella pagina Sposta risorse scegliere il gruppo di risorse in cui si vuole spostare l'applicazione:On the **Move resources** page, choose the resource group you'to move this application to:

![Portale di gestione: gestione delle risorse](media/howto-manage-iot-central-from-portal/image4a.png)

Per spostare l'applicazione in una sottoscrizione diversa, selezionare **Modifica** accanto alla sottoscrizione. Nella pagina **Sposta risorse** scegliere l'abbonamento in cui si vuole spostare l'applicazione:

![Portale di gestione: gestione delle risorse](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire le applicazioni Azure IoT Central dal portale di Azure, il passaggio successivo suggerito è:

> [!div class="nextstepaction"]
> [Amministrare l'applicazione](howto-administer.md)