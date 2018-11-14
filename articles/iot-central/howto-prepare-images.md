---
title: Caricare immagini nell'applicazione Azure IoT Central | Microsoft Docs
description: Informazioni su come preparare e caricare immagini nell'applicazione Azure IoT Central in qualità di costruttore.
author: tbhagwat3
ms.author: tanmayb
ms.date: 10/31/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: b55b9929129b882f893b1fea58b7061131daffb8
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2018
ms.locfileid: "51005139"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Preparare e caricare immagini nell'applicazione Azure IoT Central

Questo articolo descrive come personalizzare l'applicazione Azure IoT Central caricando immagini personalizzate in qualità di costruttore. Ad esempio è possibile personalizzare un dashboard di dispositivo con un'immagine del dispositivo.

## <a name="before-you-begin"></a>Prima di iniziare

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

1. Un'applicazione Azure IoT Central. Per altre informazioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central.md).
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
| Application Manager | 268 x 160 px |
| Modello di dispositivo | 64 x 64 px |
| Home page e riquadri del dashboard | Il riquadro con le minime dimensioni è 200 x 200 px, i riquadri più grandi possono essere quadrati o rettangoli che siano multipli dei riquadri piccoli. Ad esempio 200 x 400 px, 400 x 200 px o 400 x 400 px |

Per la visualizzazione ottimale nell'applicazione, è consigliabile creare immagini con le dimensioni indicate nella tabella precedente.

## <a name="upload-the-images"></a>Caricare le immagini

Le sezioni seguenti descrivono come caricare le immagini nelle diverse posizioni:

### <a name="application-manager"></a>Application Manager

Per caricare un'immagine in **Application Manager**, passare alla pagina **Application Settings** (Impostazioni applicazione) nella sezione **Administration** (Amministrazione). È necessario essere un amministratore per poter eseguire questa attività:

![Caricare l'immagine dell'applicazione](media/howto-prepare-images/uploadapplicationmanager.png)

Fare clic sul riquadro Immagine applicazione per caricare l'immagine preparata (268 x 160 px) dal computer locale.

### <a name="home-page"></a>Home page

Per caricare un'immagine nella home page, passare alla **home page** dell'applicazione e fare clic su **Modifica**. Per poter completare questa attività è necessario essere un costruttore:

![Caricare l'immagine della home page](media/howto-prepare-images/uploadhomepage.png)

In Configura immagine fare clic sul riquadro Immagine per caricare l'immagine preparata dal computer locale. Il riquadro con le minime dimensioni è 200 x 200 px, i riquadri più grandi possono essere quadrati o rettangoli che siano multipli dei riquadri piccoli. Ad esempio 200 x 400 px, 400 x 200 px o 400 x 400 px.

**Salvare** l'immagine caricata. È possibile ridimensionarla in modalità di modifica. Al termine, fare clic su **Done** (Operazione completata). 

### <a name="device-template"></a>Modello di dispositivo

Per caricare un'immagine in un modello di dispositivo, passare a **Device Explorer**, scegliere il modello di dispositivo e poi un dispositivo. Per poter completare questa attività è necessario essere un costruttore:

![Caricare un'immagine di modello di dispositivo](media/howto-prepare-images/uploaddevicetemplate.png)

Fare clic sul riquadro immagine per caricare l'immagine preparata (64 x 64 px) dal computer locale. 

### <a name="device-dashboard"></a>Pagina dashboard

Per caricare un'immagine in un dashboard di dispositivo, passare a **Device Explorer**, scegliere il modello di dispositivo e poi un dispositivo. Quindi scegliere la pagina **Dashboard** e fare clic su **Modifica modello**. Per poter completare questa attività è necessario essere un costruttore:

![Caricare un'immagine del dashboard di dispositivo](media/howto-prepare-images/uploaddevicedashboard.png)

In Configura immagine fare clic sul riquadro Immagine e scegliere il file da caricare dal computer locale. Il riquadro con le minime dimensioni è 200 x 200 px, i riquadri più grandi possono essere quadrati o rettangoli che siano multipli dei riquadri piccoli. Ad esempio 200 x 400 px, 400 x 200 px o 400 x 400 px.

**Salvare** l'immagine caricata. È possibile ridimensionarla e riposizionarla in modalità di modifica. Al termine, fare clic su **Done** (Operazione completata).

### <a name="device-set-dashboard"></a>Dashboard del set di dispositivi

Per caricare un'immagine in un dashboard del set di dispositivi, passare a **Device Sets** (Set di dispositivi), scegliere il set di dispositivi e poi un dispositivo. Quindi scegliere la pagina **Dashboard** e fare clic su **Modifica**:

![Caricare un'immagine per il dashboard del set di dispositivi](media/howto-prepare-images/uploaddevicesetdashboard.png)

In Configura immagine fare clic sul riquadro Immagine per caricare l'immagine preparata dal computer locale. Il riquadro con le minime dimensioni è 200 x 200 px, i riquadri più grandi possono essere quadrati o rettangoli che siano multipli dei riquadri piccoli. Ad esempio 200 x 400 px, 400 x 200 px o 400 x 400 px.

**Salvare** l'immagine caricata. È possibile ridimensionarla e riposizionarla in modalità di modifica. Al termine, fare clic su **Done** (Operazione completata).

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la procedura per preparare e caricare immagini nell'applicazione Azure IoT Central, il prossimo passo suggerito è:

> [!div class="nextstepaction"]
> [Gestire i dispositivi nell'applicazione Azure IoT Central](howto-manage-devices.md)