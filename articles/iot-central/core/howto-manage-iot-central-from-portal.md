---
title: Gestire IoT Central dal portale di Azure | Microsoft Docs
description: Questo articolo descrive come creare e gestire le applicazioni IoT Central dalla portale di Azure.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/02/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 60c1109d0f540c8301cc536c2079537dca5115f4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435017"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Gestire IoT Central dal portale di Azure

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Invece di creare e gestire applicazioni IoT Central nel sito Web di [Azure IOT Central Application Manager](https://aka.ms/iotcentral) , è possibile usare la [portale di Azure](https://portal.azure.com) per gestire le applicazioni.

## <a name="create-iot-central-applications"></a>Creare applicazioni IoT Central

Per creare un'applicazione, passare alla [portale di Azure](https://ms.portal.azure.com) e selezionare **Crea una risorsa** nel riquadro principale a sinistra.

![Portale di gestione: menu di spostamento](media/howto-manage-iot-central-from-portal/image0.png)

Nella barra di ricerca digitare **IoT Central**.

![Portale di gestione: ricerca](media/howto-manage-iot-central-from-portal/image0a1.png)

Selezionare il **IOT Central** riga dell'applicazione nei risultati della ricerca.

![Portale di gestione: risultati della ricerca](media/howto-manage-iot-central-from-portal/image0b1.png)

Selezionare quindi **Crea**.

![Portale di gestione: risorsa IoT Central](media/howto-manage-iot-central-from-portal/image0c1.png)

Compilare tutti i campi del modulo. Questo modulo è simile al modulo compilato per creare applicazioni nel sito Web di [Azure IOT Central Application Manager](https://aka.ms/iotcentral) . Per altre informazioni, consultare la guida introduttiva [Creare un'applicazione Azure IoT Central](quick-deploy-iot-central.md).

È possibile creare IoT Central applicazione con le funzionalità disponibili a livello generale selezionando **applicazione legacy** come modello di applicazione, tutti gli altri modelli di applicazione usano le funzionalità di anteprima pubblica.

![Crea IoT Central form](media/howto-manage-iot-central-from-portal/image6a.png)

La **località** è l'[area geografica](https://azure.microsoft.com/global-infrastructure/geographies/) in cui si vuole creare l'applicazione. Per ottenere prestazioni ottimali, è in genere consigliabile scegliere la località fisicamente più vicina ai dispositivi. Azure IoT Central è attualmente disponibile in **Stati Uniti**, **Australia**, **Asia Pacifico** o **Europa**.  Dopo aver scelto una località, non è possibile spostare l'applicazione in un'altra località in un secondo momento.

> [!NOTE]
> I modelli di applicazione di anteprima sono attualmente disponibili solo nelle località **Europa** e **Stati Uniti** .

![Portale di gestione: creare la risorsa IoT Central](media/howto-manage-iot-central-from-portal/image1a.png)  

Dopo aver compilato tutti i campi, selezionare **Crea**.

## <a name="manage-existing-iot-central-applications"></a>Gestire le applicazioni IoT Central esistenti

Se si dispone già di un'applicazione Azure IoT Central è possibile eliminarla, spostarla in una sottoscrizione diversa o in un altro gruppo di risorse nel portale di Azure.

> [!NOTE]
> Non è possibile visualizzare le applicazioni della versione di valutazione nel portale di Azure perché non sono associate alla sottoscrizione.

Per iniziare, selezionare **tutte le risorse** nel riquadro principale a sinistra. Digitare il nome dell'applicazione nella casella di ricerca per individuarla nel proprio elenco di risorse. Quindi selezionare l'applicazione IoT Central che si vuole gestire.

![Portale di gestione: gestione delle risorse](media/howto-manage-iot-central-from-portal/image2a.png)

Per passare all'applicazione, selezionare l'URL dell'applicazione IoT Central.

![Portale di gestione: gestione delle risorse](media/howto-manage-iot-central-from-portal/image3.png)

Per spostare l'applicazione in un gruppo di risorse diverso, selezionare **Cambia** accanto al gruppo di risorse. Nella pagina **Move resources** (Sposta risorse) selezionare il gruppo di risorse nel quale si vuole eseguire la migrazione di questa applicazione.

![Portale di gestione: gestione delle risorse](media/howto-manage-iot-central-from-portal/image4a.png)

Per spostare l'applicazione in una sottoscrizione diversa, selezionare il collegamento **Cambia** accanto alla sottoscrizione. Nella finestra di dialogo visualizzata selezionare la sottoscrizione nella quale si vuole eseguire la migrazione di questa applicazione.

![Portale di gestione: gestione delle risorse](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire le applicazioni Azure IoT Central dal portale di Azure, il passaggio successivo suggerito è:

> [!div class="nextstepaction"]
> [Amministrare l'applicazione](howto-administer.md)