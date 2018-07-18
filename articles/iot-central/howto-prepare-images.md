---
title: Caricare immagini nell'applicazione Azure IoT Central | Microsoft Docs
description: Informazioni su come preparare e caricare immagini nell'applicazione Azure IoT Central in qualità di costruttore.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7fd9c8ed5559b00bc755e3f04c768dceeb487562
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628022"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Preparare e caricare immagini nell'applicazione Azure IoT Central

Questo articolo descrive come personalizzare l'applicazione Microsoft Azure IoT Central caricando immagini personalizzate in qualità di costruttore. Ad esempio è possibile personalizzare un dashboard di dispositivo con un'immagine del dispositivo.

## <a name="before-you-begin"></a>Prima di iniziare

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

1. Un'applicazione Azure IoT Central. Per altre informazioni, vedere [Creare l'applicazione Azure IoT Central](howto-create-application.md).
1. Uno strumento per il ridimensionamento dei file di immagine.

## <a name="choose-where-to-use-custom-images"></a>Scegliere dove usare immagini personalizzate

È possibile aggiungere immagini personalizzate nelle posizioni seguenti di un'applicazione Azure IoT Central:

* La pagina **Application Manager**

    ![Immagine sulla pagina Application Manager](media/howto-prepare-images/applicationmanager.png)

* La home page

    ![Immagine sulla home page](media/howto-prepare-images/homepage.png)

* Un modello di dispositivo

    ![Immagine in un modello di dispositivo](media/howto-prepare-images/devicetemplate.png)

* Un riquadro in un dashboard di dispositivo

    ![Immagine in un riquadro dispositivo](media/howto-prepare-images/devicetile.png)

* Un riquadro in un dashboard di set di dispositivi

    ![Immagine in un riquadro di set di dispositivi](media/howto-prepare-images/devicesettile.png)

## <a name="prepare-the-images"></a>Preparare le immagini

In tutte le quattro posizioni è possibile usare immagini PNG, GIF o JPEG.

La tabella seguente riepiloga le dimensioni di immagine ammesse:

| Località | Dimensioni |
| -------- | ------ |
| **Application Manager** | 268 x 160 px |
| Modello di dispositivo | 64 x 64 px |
| Home page e riquadri del dashboard | Il riquadro con le minime dimensioni è 200 x 200 px, i riquadri più grandi possono essere quadrati o rettangoli che siano multipli dei riquadri piccoli. Ad esempio 200 x 400 px, 400 x 200 px o 400 x 400 px |

Per la visualizzazione ottimale nell'applicazione, è consigliabile creare immagini con le dimensioni indicate nella tabella precedente.

## <a name="upload-the-images"></a>Caricare le immagini

Le sezioni seguenti descrivono come caricare le immagini da usare nelle diverse posizioni:

### <a name="application-manager"></a>Application Manager

Per caricare un'immagine da usare in **Application Manager**, passare alla pagina **Application Settings** (Impostazioni applicazione) nella sezione **Administration** (Amministrazione). È necessario essere un amministratore per poter eseguire questa attività:

![Caricare l'immagine dell'applicazione](media/howto-prepare-images/uploadapplicationmanager.png)

Fare clic su Upload image (Carica immagine) e quindi scegliere il file da caricare nel computer locale.

### <a name="home-page"></a>Home page

Per caricare un'immagine da usare nella home page, passare alla **home page** dell'applicazione e attivate la modalità progettazione. Per poter completare questa attività è necessario essere un costruttore:

![Caricare l'immagine della home page](media/howto-prepare-images/uploadhomepage.png)

Fare clic su Upload image (Carica immagine) e quindi scegliere il file da caricare nel computer locale.

Dopo che l'immagine è stata caricata, è possibile ridimensionarla mentre è attivata la modalità progettazione.

### <a name="device-template"></a>Modello di dispositivo

Per caricare un'immagine da usare in un modello di dispositivo, passare a **Device Explorer**, scegliere il modello di dispositivo e quindi un dispositivo e attivare la modalità progettazione. Per poter completare questa attività è necessario essere un costruttore:

![Caricare un'immagine di modello di dispositivo](media/howto-prepare-images/uploaddevicetemplate.png)

Fare clic su Upload image (Carica immagine) e quindi scegliere il file da caricare nel computer locale.

### <a name="device-dashboard"></a>Pagina dashboard

Per caricare un'immagine da usare in un dashboard di dispositivo, passare a **Device Explorer**, scegliere il modello di dispositivo e poi un dispositivo. Quindi scegliere la pagina **Dashboard** e attivare la modalità progettazione. Per poter completare questa attività è necessario essere un costruttore:

![Caricare un'immagine del dashboard di dispositivo](media/howto-prepare-images/uploaddevicedashboard.png)

Fare clic su Upload image (Carica immagine) e quindi scegliere il file da caricare nel computer locale.

Dopo che l'immagine è stata caricata, è possibile ridimensionarla e riposizionarla mentre è attivata la **modalità progettazione**.

### <a name="device-set-dashboard"></a>Dashboard del set di dispositivi

Per caricare un'immagine da usare in un dashboard del set di dispositivi, passare a **Device Sets** (Set di dispositivi), scegliere il set di dispositivi e poi un dispositivo. Quindi scegliere la pagina **Dashboard** e attivare la **modalità progettazione**:

![Caricare un'immagine per il dashboard del set di dispositivi](media/howto-prepare-images/uploaddevicesetdashboard.png)

Fare clic su Upload image (Carica immagine) e quindi scegliere il file da caricare nel computer locale.

Dopo che l'immagine è stata caricata, è possibile ridimensionarla e riposizionarla mentre è attivata la modalità progettazione.

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la procedura per preparare e caricare immagini nell'applicazione Azure IoT Central, il prossimo passo suggerito è:

> [!div class="nextstepaction"]
> [Gestire i dispositivi nell'applicazione Azure IoT Central](howto-manage-devices.md)