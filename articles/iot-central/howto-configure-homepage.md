---
title: Configurare la home page dell'applicazione Azure IoT Central | Microsoft Docs
description: Informazioni su come configurare la home page dell'applicazione Azure IoT Central in qualità di costruttore.
author: tbhagwat3
ms.author: tanmayb
ms.date: 07/10/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 93f406a1d5e4a8c2ce5ad1db0c3936dd3ad2bfb9
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38992357"
---
## <a name="configuring-homepage"></a>Configurazione della home page

La home page è la pagina che viene caricata quando gli utenti che dispongono dell'accesso aprono l'URL dell'applicazione. Se durante la creazione dell'applicazione sono stati selezionati i modelli di applicazione "Sample Contoso" o "Sample Devkits", l'applicazione conterrà home page predefinite. Se invece è stato selezionato il modello di applicazione "Custom Application", la home page sarà vuota.

Ad esempio, ecco la home page delle applicazioni basate sul modello "Sample Contoso". Per personalizzare la home page dell'applicazione, prima di tutto **attivare** **Modalità progettazione** in alto a destra. 

![Home page delle applicazioni basate sul modello "Sample Contoso"](media\howto-configure-homepage\image1.png)

Con **l'attivazione** della **Modalità progettazione** verrà aperta la libreria dashboard in un pannello a sinistra. Esistono molti tipi di primitive di riquadri e dashboard che possono essere aggiunte per personalizzare la home page.

![Libreria dashboard](media\howto-configure-homepage\image2.png)

Ad esempio, è possibile aggiungere un riquadro **Settings and Properties** (Impostazioni e proprietà) per mostrare una selezione dei valori correnti di impostazioni e proprietà. A tale scopo, selezionare prima di tutto un **Device Template** (Modello di dispositivo) quindi selezionare una **Device Instance** (Istanza del dispositivo). Quindi, assegnare un titolo al riquadro e selezionare un'**impostazione** o una **proprietà** da visualizzare. In questo caso abbiamo selezionato **Fan Speed** (Velocità della ventola). Fare clic su **Save** (Salva) per visualizzare questo riquadro nella home page.

![Modulo "Configure Device Details" (Configura dettagli dispositivo) con i dettagli per impostazioni e proprietà](media\howto-configure-homepage\image3.png)

Quando un operatore visualizza la home page, ora potrà vedere questo riquadro con le proprietà o le impostazioni del dispositivo:

![Scheda "Dashboard" con visualizzate le impostazioni e le proprietà per il riquadro](media\howto-configure-homepage\image4.png)

Sperimentare con i vari altri tipi di riquadro della libreria per scoprire come è possibile personalizzare ulteriormente la home page dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come configurare la home page di Azure IoT Central, è possibile:

> [!div class="nextstepaction"]
> [Preparare e caricare immagini](howto-prepare-images.md)
