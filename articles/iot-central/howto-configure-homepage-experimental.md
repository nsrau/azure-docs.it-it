---
title: Configurare la home page dell'applicazione Azure IoT Central | Microsoft Docs
description: Informazioni su come configurare la home page dell'applicazione Azure IoT Central in qualità di costruttore.
author: dominicbetts
ms.author: dobett
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 599616f046b2eccee009e8a08e8a2f51cf968644
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55772255"
---
# <a name="configuring-homepage"></a>Configurazione della home page

La home page è la pagina che viene caricata quando gli utenti che dispongono dell'accesso all'applicazione aprono l'URL dell'applicazione. Se durante la creazione dell'applicazione sono stati selezionati i modelli di applicazione "Sample Contoso" o "Sample Devkits", l'applicazione conterrà home page predefinite. Se invece è stato selezionato il modello di applicazione "Custom Application", la home page sarà vuota.

## <a name="add-tiles"></a>Aggiunta di riquadri

Ad esempio, ecco la home page delle applicazioni basate sul modello "Sample Contoso". Per personalizzare la home page dell'applicazione, selezionare prima **Modifica** in alto a destra. 

![Home page delle applicazioni basate sul modello "Sample Contoso"](media/howto-configure-homepage-experimental/image1.png)

Selezionando **Modifica** verrà aperta la libreria dashboard in un pannello a sinistra. Esistono molti tipi di primitive di riquadri e dashboard che possono essere aggiunte per personalizzare la home page.

![Libreria dashboard](media/howto-configure-homepage-experimental/image2.png)

Ad esempio, è possibile aggiungere un riquadro **Settings and Properties** (Impostazioni e proprietà) per mostrare una selezione dei valori correnti di impostazioni e proprietà. A tale scopo, selezionare prima di tutto un **Device Template** (Modello di dispositivo) quindi selezionare una **Device Instance** (Istanza del dispositivo). Quindi, assegnare un titolo al riquadro e selezionare un'**impostazione** o una **proprietà** da visualizzare. In questo caso è stato selezionato **Set Temperature** (Imposta temperatura). Fare clic su **Fine** per visualizzare questo riquadro nella home page.

![Modulo "Configure Device Details" (Configura dettagli dispositivo) con i dettagli per impostazioni e proprietà](media/howto-configure-homepage-experimental/image3.png)

Quando un operatore visualizza la home page, ora potrà vedere questo riquadro con le proprietà o le impostazioni del dispositivo:

![Scheda "Dashboard" con visualizzate le impostazioni e le proprietà per il riquadro](media/howto-configure-homepage-experimental/image4.png)

Sperimentare con i vari altri tipi di riquadro della libreria per scoprire come è possibile personalizzare ulteriormente la home page dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come configurare la home page di Azure IoT Central, è possibile:

> [!div class="nextstepaction"]
> [Preparare e caricare immagini](howto-prepare-images-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
