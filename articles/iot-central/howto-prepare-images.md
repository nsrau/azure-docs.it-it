---
title: Caricare immagini nell'applicazione Azure IoT Central | Microsoft Docs
description: Informazioni su come preparare e caricare immagini nell'applicazione Azure IoT Central in qualità di costruttore.
author: dominicbetts
ms.author: dobett
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: a20662c2fc9b416fefce89a6ebe706307ee71bb7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65236476"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Preparare e caricare immagini nell'applicazione Azure IoT Central

Questo articolo descrive come personalizzare l'applicazione Azure IoT Central caricando immagini personalizzate in qualità di costruttore. Ad esempio è possibile personalizzare un dashboard di dispositivo con un'immagine del dispositivo.

## <a name="before-you-begin"></a>Prima di iniziare

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

1. Un'applicazione Azure IoT Central. Per altre informazioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central.md).
1. Uno strumento per il ridimensionamento dei file di immagine.

## <a name="choose-where-to-use-custom-images"></a>Scegliere dove usare immagini personalizzate

È possibile aggiungere immagini personalizzate nelle posizioni seguenti di un'applicazione Azure IoT Central:

* Il **mie applicazioni** pagina

    ![Immagine sulla pagina Application Manager](media/howto-prepare-images/applicationmanager.png)

* Il dashboard delle applicazioni

    ![Immagine nel dashboard dell'applicazione](media/howto-prepare-images/homepage.png)

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
| Application Manager | 268 x 160 px |
| Modello di dispositivo | 64 x 64 px |
| Riquadri del dashboard | Il riquadro con le minime dimensioni è 200 x 200 px, i riquadri più grandi possono essere quadrati o rettangoli che siano multipli dei riquadri piccoli. Ad esempio 200 x 400 px, 400 x 200 px o 400 x 400 px |

Per la visualizzazione ottimale nell'applicazione, è consigliabile creare immagini con le dimensioni indicate nella tabella precedente.

## <a name="upload-the-images"></a>Caricare le immagini

Le sezioni seguenti descrivono come caricare le immagini nelle diverse posizioni:

### <a name="application-manager"></a>Application Manager

Per caricare un'immagine da utilizzare nel **applicazioni personali** pagina, passare al **le impostazioni dell'applicazione** nella pagina il **amministrazione** sezione. È necessario essere un amministratore per poter eseguire questa attività:

![Caricare l'immagine dell'applicazione](media/howto-prepare-images/uploadapplicationmanager.png)

Selezionare il **immagine applicazione** riquadro per caricare un'immagine (268 x 160 px) dal computer locale.

### <a name="application-dashboard"></a>Dashboard dell'applicazione

Per caricare un'immagine nel dashboard dell'applicazione, passare al **Dashboard** pagina dell'applicazione e selezionare **modificare**. Per poter completare questa attività è necessario essere un costruttore:

![Caricare l'immagine del dashboard](media/howto-prepare-images/uploadhomepage.png)

Sotto **immagine configurare**, selezionare la **immagine** riquadro per caricare un'immagine dal computer locale. Il riquadro con le minime dimensioni è 200 x 200 px, i riquadri più grandi possono essere quadrati o rettangoli che siano multipli dei riquadri piccoli. Ad esempio 200 x 400 px, 400 x 200 px o 400 x 400 px.

**Salvare** l'immagine caricata. È possibile ridimensionarla in modalità di modifica. Selezionare **al termine**.

### <a name="device-template"></a>Modello di dispositivo

Per caricare un'immagine in un modello di dispositivo, passare a **Modelli di dispositivi** e scegliere il modello di dispositivo. Per poter completare questa attività è necessario essere un costruttore:

![Caricare un'immagine di modello di dispositivo](media/howto-prepare-images/uploaddevicetemplate.png)

Selezionare il riquadro immagine per caricare un'immagine (64x64 px) dal computer locale.

### <a name="device-dashboard"></a>Pagina dashboard

Per caricare un'immagine in un dashboard di dispositivo, passare a **Modelli di dispositivi** e scegliere il modello di dispositivo. Quindi selezionare la scheda **Dashboard**. Per poter completare questa attività è necessario essere un costruttore:

![Caricare un'immagine del dashboard di dispositivo](media/howto-prepare-images/uploaddevicedashboard.png)

Sotto **immagine configurare**, selezionare la **immagine** riquadro e quindi scegliere il file da caricare dal computer locale. Il riquadro con le minime dimensioni è 200 x 200 px, i riquadri più grandi possono essere quadrati o rettangoli che siano multipli dei riquadri piccoli. Ad esempio 200 x 400 px, 400 x 200 px o 400 x 400 px.

**Salvare** l'immagine caricata. È possibile ridimensionarla e riposizionarla in modalità di modifica. Selezionare **al termine**.

### <a name="device-set-dashboard"></a>Dashboard del set di dispositivi

Per caricare un'immagine in un dashboard del set di dispositivi, passare a **Device Sets** (Set di dispositivi), scegliere il set di dispositivi e poi un dispositivo. Quindi scegliere il **Dashboard** pagina e selezionare **modificare**:

![Caricare un'immagine per il dashboard del set di dispositivi](media/howto-prepare-images/uploaddevicesetdashboard.png)

Sotto **immagine configurare**, selezionare la **immagine** riquadro per caricare un'immagine dal computer locale. Il riquadro con le minime dimensioni è 200 x 200 px, i riquadri più grandi possono essere quadrati o rettangoli che siano multipli dei riquadri piccoli. Ad esempio 200 x 400 px, 400 x 200 px o 400 x 400 px.

**Salvare** l'immagine caricata. È possibile ridimensionarla e riposizionarla in modalità di modifica. Selezionare **al termine**.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come preparare e caricare immagini in un'applicazione Azure IoT Central, ecco i passaggi successivi consigliati:

* [Personalizzare l'interfaccia utente di Azure IoT Central](./howto-customize-ui.md)
* [Configurare il dashboard dell'applicazione](./howto-configure-homepage.md)
* [Gestire i dispositivi nell'applicazione Azure IoT Central](howto-manage-devices.md)