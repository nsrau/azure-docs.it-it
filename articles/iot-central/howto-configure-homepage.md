---
title: Configurare il dashboard dell'applicazione IoT Central di Azure | Microsoft Docs
description: Come costruttore, informazioni su come configurare il dashboard predefinito dell'applicazione IoT Central di Azure.
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 94ad51ac11687dfe060176132e2030d61b8d4ffc
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850206"
---
# <a name="configure-the-application-dashboard"></a>Configurare il dashboard dell'applicazione

Il **Dashboard** è la pagina che viene caricata quando gli utenti che hanno accesso all'applicazione passano all'URL dell'applicazione. Se è stato selezionato il modello di applicazione Contoso o **esempio devkits** di **esempio** per creare l'applicazione, l'applicazione avrà un dashboard predefinito. Se si sceglie il modello applicazione **applicazione personalizzata** , il dashboard è vuoto.

> [!NOTE]
> Gli utenti possono anche [creare Dashboard personali](howto-personalize-dashboard.md) da usare al posto del dashboard dell'applicazione predefinito.

## <a name="add-tiles"></a>Aggiunta di riquadri

La schermata seguente mostra il dashboard in un'applicazione creata dal modello di **esempio contoso** . Per personalizzare il dashboard predefinito per l'applicazione, fare clic su **modifica** in alto a destra nella pagina.

![Dashboard per le applicazioni basate sul modello "esempio contoso"](media/howto-configure-homepage/image1a.png)

Selezionando **modifica**, viene aperto il pannello libreria dashboard. La libreria contiene i riquadri e le primitive del dashboard che è possibile usare per personalizzare il dashboard.

![Libreria dashboard](media/howto-configure-homepage/image2a.png)

Ad esempio, è possibile aggiungere un riquadro **Impostazioni e proprietà del dispositivo** per visualizzare una selezione dei valori delle impostazioni e delle proprietà correnti per un dispositivo. A tale scopo, selezionare prima di tutto un **Device Template** (Modello di dispositivo) quindi selezionare una **Device Instance** (Istanza del dispositivo). Quindi, assegnare un titolo al riquadro e selezionare un'**impostazione** o una **proprietà** da visualizzare. La schermata seguente mostra le impostazioni e le proprietà selezionate per l'aggiunta al riquadro. Selezionare **Done** per salvare la modifica al dashboard.

![Modulo "Configure Device Details" (Configura dettagli dispositivo) con i dettagli per impostazioni e proprietà](media/howto-configure-homepage/image3a.png)

A questo punto, quando un operatore Visualizza il dashboard dell'applicazione predefinito, viene visualizzato il nuovo riquadro con l'impostazione della **temperatura impostata** per il dispositivo:

![Scheda "Dashboard" con visualizzate le impostazioni e le proprietà per il riquadro](media/howto-configure-homepage/image4a.png)

È possibile esplorare altri tipi di riquadro nella libreria per scoprire come personalizzare ulteriormente il dashboard dell'applicazione predefinito.

Per altre informazioni su come usare i riquadri in Azure IoT Central, vedere usare i riquadri del [Dashboard](howto-use-tiles.md).

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come configurare il dashboard dell'applicazione predefinita di Azure IoT Central, è possibile:

> [!div class="nextstepaction"]
> [Preparare e caricare immagini](howto-prepare-images.md)
