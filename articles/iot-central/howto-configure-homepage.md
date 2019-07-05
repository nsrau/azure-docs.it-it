---
title: Configurare il dashboard dell'applicazione Azure IoT Central | Microsoft Docs
description: Un generatore di informazioni su come configurare il dashboard dell'applicazione Azure IoT Central predefinito.
author: dominicbetts
ms.author: dobett
ms.date: 02/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: e6947a4f15797028274d49069d9e2787b143860d
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503219"
---
# <a name="configure-the-application-dashboard"></a>Configurare il dashboard dell'applicazione

Il **Dashboard** è la pagina viene caricata quando gli utenti autorizzati ad accedere all'applicazione passano all'URL dell'applicazione. Se è stato selezionato il **esempio Contoso** o **esempio Devkits** modello di applicazione per creare l'applicazione, l'applicazione dispone di una dashboard predefinita. Se si sceglie la **dell'applicazione personalizzata** modello di applicazione, il dashboard è vuoto.

> [!NOTE]
> Gli utenti possono anche [creare i propri Dashboard personali](howto-personalize-dashboard.md) da usare al posto del dashboard dell'applicazione predefinito.

## <a name="add-tiles"></a>Aggiunta di riquadri

Lo screenshot seguente mostra il dashboard in un'applicazione creata mediante il **Contoso di esempio** modello. Per personalizzare il dashboard predefinito per l'applicazione, selezionare **modifica** nella parte superiore destra della pagina.

![Dashboard per le applicazioni basate sul modello di "Contoso di esempio"](media/howto-configure-homepage/image1a.png)

Selezionando **modifica**, apre il pannello di libreria dashboard. La libreria contiene i riquadri e le primitive di dashboard che è possibile usare per personalizzare il dashboard.

![Libreria dashboard](media/howto-configure-homepage/image2a.png)

Ad esempio, è possibile aggiungere un **le impostazioni del dispositivo e proprietà** riquadro per mostrare la selezione dei valori correnti delle impostazioni e le proprietà per un dispositivo. A tale scopo, selezionare prima di tutto un **Device Template** (Modello di dispositivo) quindi selezionare una **Device Instance** (Istanza del dispositivo). Quindi, assegnare un titolo al riquadro e selezionare un'**impostazione** o una **proprietà** da visualizzare. Lo screenshot seguente mostra le impostazioni e le proprietà selezionate da aggiungere al riquadro. Selezionare **Done** per salvare la modifica al dashboard.

![Modulo "Configure Device Details" (Configura dettagli dispositivo) con i dettagli per impostazioni e proprietà](media/howto-configure-homepage/image3a.png)

A questo punto quando un operatore Visualizza il dashboard dell'applicazione predefinito, viene visualizzato il nuovo riquadro con il **temperatura impostare** impostazione per il dispositivo:

![Scheda "Dashboard" con visualizzate le impostazioni e le proprietà per il riquadro](media/howto-configure-homepage/image4a.png)

È possibile esplorare altri tipi di riquadro nella libreria per informazioni su come personalizzare ulteriormente il dashboard dell'applicazione predefinito.

Per altre informazioni su come usare i riquadri in Azure IoT Central, vedere [usare i riquadri del dashboard](howto-use-tiles.md).

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come configurare il dashboard dell'applicazione predefinito Azure IoT Central, è possibile:

> [!div class="nextstepaction"]
> [Preparare e caricare immagini](howto-prepare-images.md)
