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
ms.openlocfilehash: c72de0ef874659a5d7840689e38bd7857c25b840
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65464148"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Gestire IoT Central dal portale di Azure

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Invece di creare e gestire le applicazioni IoT Central dalla pagina [Gestione applicazioni](https://aka.ms/iotcentral) di IoT Central, è possibile usare il [portale di Azure](https://portal.azure.com) per gestire le applicazioni.

## <a name="create-iot-central-applications"></a>Creare applicazioni IoT Central

Per creare un'applicazione, passare al [portale di Azure](https://ms.portal.azure.com) e selezionare **crea una risorsa** nel menu di spostamento principale a sinistra.

![Portale di gestione: menu di spostamento](media/howto-manage-iot-central-from-portal/image0.png)

Nella barra di ricerca digitare **IoT Central**.

![Portale di gestione: ricerca](media/howto-manage-iot-central-from-portal/image0a1.png)

Selezionare il **IoT Central Application** voce nei risultati della ricerca.

![Portale di gestione: risultati della ricerca](media/howto-manage-iot-central-from-portal/image0b1.png)

Selezionare quindi **Crea**.

![Portale di gestione: risorsa IoT Central](media/howto-manage-iot-central-from-portal/image0c1.png)

Compilare tutti i campi del modulo. Questo modulo è analogo a quello che si compila per creare applicazioni nella pagina [Gestione applicazioni](https://aka.ms/iotcentral) di IoT Central. Per altre informazioni, consultare la guida introduttiva [Creare un'applicazione Azure IoT Central](quick-deploy-iot-central.md).

![Portale di gestione: creare la risorsa IoT Central](media/howto-manage-iot-central-from-portal/image1a.png)  

Dopo aver compilato tutti i campi, selezionare **Create**.

## <a name="manage-existing-iot-central-applications"></a>Gestire le applicazioni IoT Central esistenti

Se si dispone già di un'applicazione Azure IoT Central è possibile eliminarla, spostarla in una sottoscrizione diversa o in un altro gruppo di risorse nel portale di Azure.

> [!NOTE]
> Non è possibile visualizzare le applicazioni della versione di valutazione nel portale di Azure perché non sono associate alla sottoscrizione.

Per iniziare, selezionare **tutte le risorse** nel menu di spostamento principale a sinistra. Digitare il nome dell'applicazione nella casella di ricerca per individuarla nel proprio elenco di risorse. Selezionare quindi l'applicazione IoT Central che si desidera gestire.

![Portale di gestione: gestione delle risorse](media/howto-manage-iot-central-from-portal/image2a.png)

Per passare all'applicazione, selezionare l'URL di applicazioni IoT Central.

![Portale di gestione: gestione delle risorse](media/howto-manage-iot-central-from-portal/image3.png)

Per spostare l'applicazione a un gruppo di risorse diverso, selezionare **modificare** accanto al gruppo di risorse. Nella pagina **Move resources** (Sposta risorse) selezionare il gruppo di risorse nel quale si vuole eseguire la migrazione di questa applicazione.

![Portale di gestione: gestione delle risorse](media/howto-manage-iot-central-from-portal/image4a.png)

Per spostare l'applicazione in un'altra sottoscrizione, selezionare la **modificare** collegamento accanto alla sottoscrizione. Nella finestra di dialogo visualizzata selezionare la sottoscrizione nella quale si vuole eseguire la migrazione di questa applicazione.

![Portale di gestione: gestione delle risorse](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire le applicazioni Azure IoT Central dal portale di Azure, il passaggio successivo suggerito è:

> [!div class="nextstepaction"]
> [Amministrare l'applicazione](howto-administer.md)