---
title: Creazione di dashboard personali di Azure IoT Central Documenti Microsoft
description: In qualità di utente, scopri come creare e gestire i dashboard personali.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 06225e284000d7f10f575be08cd683488abec339
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985491"
---
# <a name="create-and-manage-multiple-dashboards"></a>Creare e gestire più dashboard

Il **dashboard** è la pagina che viene caricata quando si passa all'applicazione per la prima volta. Un **generatore** nell'applicazione definisce il dashboard dell'applicazione predefinito per tutti gli utenti. È inoltre possibile creare il proprio dashboard dell'applicazione personalizzato. È possibile avere diversi dashboard che visualizzano dati diversi e passare da uno all'altro.

Se si è un **amministratore** dell'applicazione, è anche possibile creare fino a 10 dashboard a livello di applicazione da condividere con altri utenti dell'applicazione. Solo **gli amministratori** hanno la possibilità di creare, modificare ed eliminare dashboard a livello di applicazione. 

## <a name="create-dashboard"></a>Creare un dashboard

Nella schermata seguente viene illustrato il dashboard in un'applicazione creata dal modello **Applicazione personalizzata.** È possibile sostituire il dashboard dell'applicazione predefinito con un dashboard personale oppure, se si è un amministratore, un altro dashboard a livello di applicazione. A tale scopo, selezionare **: Nuovo** nella parte superiore sinistra della pagina.
 
> [!div class="mx-imgBorder"]
> ![Dashboard per le applicazioni basate sul modello "Applicazione personalizzata"](media/howto-create-personal-dashboards/dashboard-custom-app.png)

Selezionando **- Nuovo** si apre l'editor del dashboard. Nell'editor è possibile assegnare un nome al dashboard e scegliere gli elementi dalla raccolta. La raccolta contiene i riquadri e le primitive del dashboard che è possibile usare per personalizzare il dashboard.

> [!div class="mx-imgBorder"]
> ![Libreria dashboard](media/howto-create-personal-dashboards/dashboard-library.png)

Se si è un **amministratore** dell'applicazione, verrà fornita la possibilità di attivare/disattivare se si desidera creare un dashboard a livello personale o un dashboard a livello di applicazione. Se si crea un dashboard di livello personale, solo tu sarai in grado di visualizzarlo. Se si crea un dashboard a livello di applicazione, ogni utente dell'applicazione sarà in grado di visualizzarlo. Dopo aver immesso un titolo e aver selezionato il tipo di dashboard che si desidera creare, è possibile salvare e aggiungere i riquadri in un secondo momento. Oppure, se si è pronti e sono stati aggiunti un modello di dispositivo e un'istanza del dispositivo, è possibile creare il primo riquadro. 

> [!div class="mx-imgBorder"]
> ![Modulo "Configura dettagli dispositivo" con i dettagli per Temperatura](media/howto-create-personal-dashboards/device-details.png)

Ad esempio, è possibile aggiungere un riquadro **Telemetria** per la temperatura corrente del dispositivo. A tale scopo, procedere come indicato di seguito:
1. Selezionare un **modello di dispositivo**
1. Selezionare **un'istanza del dispositivo** da visualizzare in un riquadro del dashboard. Verrà quindi visualizzato un elenco delle proprietà del dispositivo che possono essere utilizzate nel riquadro.
1. Per creare il riquadro nel dashboard, fare clic su **Temperatura** e trascinarlo nell'area del dashboard. È anche possibile fare clic sulla casella di controllo accanto a **Temperatura** e fare clic su **Combina**. La schermata seguente mostra la selezione di un modello di dispositivo e un'istanza del dispositivo e la creazione di un riquadro Di telemetria della temperatura nel dashboard.
1. Selezionare **Salva** in alto a sinistra per salvare il riquadro nel dashboard.

> [!div class="mx-imgBorder"]
> ![Dashboard" con i dettagli per il riquadro Temperatura](media/howto-create-personal-dashboards/temperature-tile-edit.png)

Ora, quando visualizzi il dashboard personale, viene visualizzato il nuovo riquadro con l'impostazione **Temperatura** per il dispositivo:

> [!div class="mx-imgBorder"]
> ![Dashboard" con i dettagli per il riquadro Temperatura](media/howto-create-personal-dashboards/temperature-tile-complete.png)

È possibile esplorare altri tipi di riquadro nella raccolta per scoprire come personalizzare ulteriormente i dashboard personali.

Per altre informazioni su come usare i riquadri in Azure IoT Central, vedere [Aggiungere riquadri al dashboard.](howto-add-tiles-to-your-dashboard.md)

## <a name="manage-dashboards"></a>Gestire i dashboard

È possibile avere diversi dashboard personali e passare da uno all'altro o scegliere uno dei dashboard dell'applicazione predefiniti:

> [!div class="mx-imgBorder"]
> ![Passare da un dashboard all'altro](media/howto-create-personal-dashboards/switch-dashboards.png)

È possibile modificare i dashboard personali ed eliminare i dashboard non più necessari. Se sei un **amministratore,** hai anche la possibilità di modificare o eliminare anche i dashboard a livello di applicazione.

> [!div class="mx-imgBorder"]
> ![Eliminare dashboard](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come creare e gestire dashboard personali, è possibile [imparare a gestire le preferenze dell'applicazione](howto-manage-preferences.md)
