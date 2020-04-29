---
title: Creare Dashboard personali di Azure IoT Central | Microsoft Docs
description: Come utente, informazioni su come creare e gestire i Dashboard personali.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 06225e284000d7f10f575be08cd683488abec339
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80985491"
---
# <a name="create-and-manage-multiple-dashboards"></a>Creare e gestire più dashboard

Il **Dashboard** è la pagina che viene caricata quando si passa all'applicazione per la prima volta. Un **Generatore** nell'applicazione definisce il dashboard dell'applicazione predefinito per tutti gli utenti. È anche possibile creare un dashboard personalizzato dell'applicazione personalizzato. È possibile avere diversi dashboard che visualizzano dati diversi e passano tra loro.

Se si è un **amministratore** dell'applicazione, è anche possibile creare fino a 10 dashboard a livello di applicazione da condividere con altri utenti dell'applicazione. Solo gli **amministratori** hanno la possibilità di creare, modificare ed eliminare i dashboard a livello di applicazione. 

## <a name="create-dashboard"></a>Creare un dashboard

La schermata seguente mostra il dashboard in un'applicazione creata dal modello di **applicazione personalizzata** . È possibile sostituire il dashboard dell'applicazione predefinito con un dashboard personale oppure, se si è un amministratore, un altro dashboard a livello di applicazione. A tale scopo, selezionare **+ nuovo** nella parte superiore sinistra della pagina.
 
> [!div class="mx-imgBorder"]
> ![Dashboard per le applicazioni basate sul modello "applicazione personalizzata"](media/howto-create-personal-dashboards/dashboard-custom-app.png)

Selezionando **+ nuovo** verrà aperto l'editor del dashboard. Nell'editor è possibile assegnare un nome al dashboard e scegliere gli elementi dalla libreria. La libreria contiene i riquadri e le primitive del dashboard che è possibile usare per personalizzare il dashboard.

> [!div class="mx-imgBorder"]
> ![Libreria dashboard](media/howto-create-personal-dashboards/dashboard-library.png)

Se si è un **amministratore** dell'applicazione, si avrà la possibilità di abilitare o disabilitare se si vuole creare un dashboard di livello personale o un dashboard a livello di applicazione. Se si crea un dashboard di livello personale, solo sarà possibile visualizzarlo. Se si crea un dashboard a livello di applicazione, ogni utente dell'applicazione sarà in grado di visualizzarlo. Dopo aver immesso un titolo e selezionato il tipo di dashboard che si vuole creare, è possibile salvare e aggiungere riquadri in un secondo momento. In alternativa, se si è pronti ed è stato aggiunto un modello di dispositivo e un'istanza del dispositivo, è possibile procedere e creare il primo riquadro. 

> [!div class="mx-imgBorder"]
> ![Configurare i dettagli del dispositivo "modulo con i dettagli per la temperatura](media/howto-create-personal-dashboards/device-details.png)

Ad esempio, è possibile aggiungere un riquadro di **telemetria** per la temperatura corrente del dispositivo. A tale scopo, procedere nel seguente modo:
1. Selezionare un **modello di dispositivo**
1. Selezionare un' **istanza del dispositivo** per il dispositivo che si vuole visualizzare in un riquadro del dashboard. Verrà quindi visualizzato un elenco delle proprietà del dispositivo che è possibile usare nel riquadro.
1. Per creare il riquadro nel dashboard, fare clic su **temperatura** e trascinarlo nell'area del dashboard. È anche possibile fare clic sulla casella di controllo accanto a **temperatura** e fare clic su **combina**. Lo screenshot seguente mostra la selezione di un modello di dispositivo e di un'istanza del dispositivo e la creazione di un riquadro di telemetria della temperatura nel dashboard.
1. Selezionare **Save (Salva** ) in alto a sinistra per salvare il riquadro nel dashboard.

> [!div class="mx-imgBorder"]
> ![Scheda "Dashboard" con i dettagli del riquadro temperatura](media/howto-create-personal-dashboards/temperature-tile-edit.png)

A questo punto, quando si Visualizza il dashboard personale, viene visualizzato il nuovo riquadro con l'impostazione della **temperatura** per il dispositivo:

> [!div class="mx-imgBorder"]
> ![Scheda "Dashboard" con i dettagli del riquadro temperatura](media/howto-create-personal-dashboards/temperature-tile-complete.png)

È possibile esplorare altri tipi di riquadro nella libreria per scoprire come personalizzare ulteriormente i Dashboard personali.

Per altre informazioni su come usare i riquadri in Azure IoT Central, vedere [aggiungere riquadri al dashboard](howto-add-tiles-to-your-dashboard.md).

## <a name="manage-dashboards"></a>Gestire i dashboard

È possibile avere diversi dashboard personali e spostarsi tra di essi o scegliere uno dei dashboard predefiniti dell'applicazione:

> [!div class="mx-imgBorder"]
> ![Passare da un dashboard all'altro](media/howto-create-personal-dashboards/switch-dashboards.png)

È possibile modificare i Dashboard personali ed eliminare i dashboard che non sono più necessari. Se si è un **amministratore**, è anche possibile modificare o eliminare i dashboard a livello di applicazione.

> [!div class="mx-imgBorder"]
> ![Elimina dashboard](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come creare e gestire i Dashboard personali, è possibile [apprendere come gestire le preferenze dell'applicazione](howto-manage-preferences.md)
