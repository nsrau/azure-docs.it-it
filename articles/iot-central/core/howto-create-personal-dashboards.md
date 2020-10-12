---
title: Creare dashboard personali di Azure IoT Central | Microsoft Docs
description: Informazioni per l'utente su come creare e gestire i dashboard personali.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f7dca7352a49e668231f64632371a445985e4de3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83634580"
---
# <a name="create-and-manage-multiple-dashboards"></a>Creare e gestire più dashboard

Il **dashboard** è la pagina che viene caricata quando si passa all'applicazione per la prima volta. Un **generatore** nell'applicazione definisce il dashboard dell'applicazione predefinito per tutti gli utenti. È anche possibile creare un proprio dashboard dell'applicazione personalizzato. È possibile avere più dashboard che visualizzano dati diversi e passare dall'uno all'altro.

Se si è un **amministratore** dell'applicazione, è anche possibile creare fino a 10 dashboard a livello di applicazione da condividere con altri utenti dell'applicazione. Solo gli **amministratori** hanno la possibilità di creare, modificare ed eliminare i dashboard a livello di applicazione.  

## <a name="create-dashboard"></a>Creare un dashboard

Lo screenshot seguente visualizza il dashboard di un'applicazione creata dal modello **Applicazione personalizzata**. È possibile sostituire il dashboard dell'applicazione predefinito con un dashboard personale oppure, se si è un amministratore, un altro dashboard a livello di applicazione. A tale scopo, selezionare **+ Nuovo** nella parte superiore sinistra della pagina.

> [!div class="mx-imgBorder"]
> ![Dashboard per le applicazioni basate sul modello "Applicazione personalizzata"](media/howto-create-personal-dashboards/dashboard-custom-app.png)

Selezionando **+ Nuovo** si aprirà l'editor del dashboard. Nell'editor è possibile assegnare un nome al dashboard e scegliere gli elementi dalla libreria. La libreria contiene i riquadri e le primitive del dashboard che è possibile usare per personalizzare il dashboard stesso.

> [!div class="mx-imgBorder"]
> ![Libreria dashboard](media/howto-create-personal-dashboards/dashboard-library.png)

Se si è un **amministratore** dell'applicazione, verrà offerta la possibilità di creare un dashboard a livello personale o un dashboard a livello di applicazione. Se si crea un dashboard a livello personale, solo l'utente potrà visualizzarlo. Se si crea un dashboard a livello di applicazione, ogni utente dell'applicazione potrà visualizzarlo. Dopo aver immesso un titolo e selezionato il tipo di dashboard che si vuole creare, è possibile salvare e aggiungere riquadri in un secondo momento. In alternativa, se si è pronti ed è stato aggiunto un modello di dispositivo e un'istanza del dispositivo, è possibile procedere e creare il primo riquadro.  

> [!div class="mx-imgBorder"]
> ![Modulo "Configura dettagli dispositivo" con i dettagli per la Temperatura](media/howto-create-personal-dashboards/device-details.png)

È ad esempio possibile aggiungere un riquadro di **telemetria** per la temperatura corrente del dispositivo. A tale scopo, procedere come indicato di seguito:

1. Selezionare un **modello di dispositivo**
1. Selezionare un dispositivo da **Dispositivi** corrispondente al dispositivo che si vuole visualizzare in un riquadro del dashboard. Verrà quindi visualizzato l'elenco delle proprietà del dispositivo che possono essere usati nel riquadro.
1. Per creare il riquadro nel dashboard, fare clic su **Temperatura** e trascinare il riquadro nell'area del dashboard. È anche possibile fare clic sulla casella di controllo accanto a **Temperatura** e fare clic su **Aggiungi riquadro**. Lo screenshot seguente illustra la selezione di un modello di dispositivo e di un dispositivo e la creazione di un riquadro di telemetria della temperatura nel dashboard.
1. Selezionare **Salva** in alto a sinistra per salvare il riquadro nel dashboard.

> [!div class="mx-imgBorder"]
> ![Scheda "Dashboard" con i dettagli del riquadro Temperatura](media/howto-create-personal-dashboards/temperature-tile-edit.png)

A questo punto, quando si visualizza il dashboard personale, viene visualizzato il nuovo riquadro con l'impostazione **Temperatura** per il dispositivo:

> [!div class="mx-imgBorder"]
> ![Scheda "Dashboard" con i dettagli del riquadro Temperatura](media/howto-create-personal-dashboards/temperature-tile-complete.png)

È possibile esplorare altri tipi di riquadro nella libreria per scoprire come personalizzare ulteriormente i dashboard personali.

Per altre informazioni su come usare i riquadri in Azure IoT Central, vedere [Aggiungere riquadri al dashboard](howto-add-tiles-to-your-dashboard.md).

## <a name="manage-dashboards"></a>Gestire i dashboard

È possibile avere più dashboard personali e passare dall'uno all'altro o scegliere uno dei dashboard predefiniti dell'applicazione:

> [!div class="mx-imgBorder"]
> ![Passare da un dashboard all'altro](media/howto-create-personal-dashboards/switch-dashboards.png)

È possibile modificare i dashboard personali ed eliminare i dashboard che non sono più necessari. Se si è un **amministratore**, è anche possibile modificare o eliminare i dashboard a livello di applicazione.

> [!div class="mx-imgBorder"]
> ![Eliminare i dashboard](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come creare e gestire i dashboard personali, è possibile avere [informazioni su come gestire le preferenze dell'applicazione](howto-manage-preferences.md).
