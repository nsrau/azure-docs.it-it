---
title: Gestire IoT Central dal portale di Azure | Microsoft Docs
description: Gestire IoT Central dal portale di Azure.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 01/14/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 89109dec83342a8f4b5962778b1803eb36656e42
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352214"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Gestire IoT Central dal portale di Azure

Invece di creare e gestire le applicazioni IoT Central dalla pagina [Gestione applicazioni](https://aka.ms/iotcentral) di IoT Central, è possibile usare il [portale di Azure](https://portal.azure.com) per gestire le applicazioni.

## <a name="create-iot-central-applications"></a>Creare applicazioni IoT Central

Per creare un'applicazione, passare al [portale di Azure](https://ms.portal.azure.com) e fare clic su **Crea una risorsa** nel menu di spostamento principale a sinistra.

![Portale di gestione: menu di spostamento](media/howto-manage-iot-central-from-portal/image0.png)

Nella barra di ricerca digitare **IoT Central**.

![Portale di gestione: ricerca](media/howto-manage-iot-central-from-portal/image0a.png)

Fare clic sulla voce **IoT Central Application** nei risultati della ricerca.

![Portale di gestione: risultati della ricerca](media/howto-manage-iot-central-from-portal/image0b.png)

Adesso fare clic sul pulsante **Crea**.

![Portale di gestione: risorsa IoT Central](media/howto-manage-iot-central-from-portal/image0c.png)

Compilare tutti i campi del modulo. Questo modulo è analogo a quello che si compila per creare applicazioni nella pagina [Gestione applicazioni](https://aka.ms/iotcentral) di IoT Central. Per altre informazioni, consultare la guida introduttiva [Creare un'applicazione Azure IoT Central](quick-deploy-iot-central.md).

![Portale di gestione: creare la risorsa IoT Central](media/howto-manage-iot-central-from-portal/image1.png)  

Dopo aver compilato tutti i campi, fare clic sul pulsante **Crea**.

## <a name="manage-existing-iot-central-applications"></a>Gestire le applicazioni IoT Central esistenti

Se si dispone già di un'applicazione Azure IoT Central è possibile eliminarla, spostarla in una sottoscrizione diversa o in un altro gruppo di risorse nel portale di Azure.

> [!NOTE]
> Non è possibile visualizzare le applicazioni della versione di valutazione nel portale di Azure perché non sono associate alla sottoscrizione.

Per iniziare, fare clic su **All resources** (Tutte le risorse) nel menu di spostamento principale a sinistra. Digitare il nome dell'applicazione nella casella di ricerca per individuarla nel proprio elenco di risorse. Quindi fare clic sull'applicazione IoT Central che si desidera gestire.

![Portale di gestione: gestione delle risorse](media/howto-manage-iot-central-from-portal/image2.png)

Per passare all'applicazione, fare clic sull'URL dell'applicazione IoT Central.

![Portale di gestione: gestione delle risorse](media/howto-manage-iot-central-from-portal/image3.png)

Per spostare l'applicazione in un gruppo di risorse diverso, fare clic su **change** (modifica) accanto al gruppo di risorse. Nella pagina **Move resources** (Sposta risorse) selezionare il gruppo di risorse nel quale si vuole eseguire la migrazione di questa applicazione.

![Portale di gestione: gestione delle risorse](media/howto-manage-iot-central-from-portal/image4.png)

Per spostare l'applicazione in una sottoscrizione diversa, fare clic su **change** (modifica) accanto alla sottoscrizione. Nella finestra di dialogo visualizzata selezionare la sottoscrizione nella quale si vuole eseguire la migrazione di questa applicazione.

![Portale di gestione: gestione delle risorse](media/howto-manage-iot-central-from-portal/image5.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire le applicazioni Azure IoT Central dal portale di Azure, il passaggio successivo suggerito è:

> [!div class="nextstepaction"]
> [Amministrare l'applicazione](howto-administer.md)