---
title: Usare i set di dispositivi nell'applicazione Azure IoT Central | Microsoft Docs
description: Come usare i set di dispositivi nell'applicazione Azure IoT Central in qualità di operatore.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 87bbab041405a085d405707ff419fbad55fdcd09
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952758"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Usare i set di dispositivi nell'applicazione Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

L'articolo descrive come un operatore può usare i set di dispositivi nell'applicazione Azure IoT Central.

Un set di dispositivi è un elenco di dispositivi raggruppati in quanto corrispondono ad alcuni criteri specificati. I set di dispositivi consentono di gestire, visualizzare e analizzare grandi quantità di dispositivi raggruppando i dispositivi in gruppi logici più piccoli. Ad esempio, è possibile creare un set di dispositivi per elencare tutti i dispositivi condizionali di Seattle per consentire a un tecnico di trovare i dispositivi per i quali sono responsabili. Questo articolo descrive come creare e configurare i set di dispositivi.

## <a name="create-a-device-set"></a>Creare un set di dispositivi

Per creare un set di dispositivi:

1. Scegliere **set di dispositivi** nel riquadro sinistro.

1. Selezionare **+ nuovo**.

    ![Nuovo set di dispositivi](media/howto-use-device-sets/image1.png)

1. Assegnare al set di dispositivi un nome che sia univoco in tutta l'applicazione. È anche possibile aggiungere una descrizione. Un set di dispositivi può contenere solo dispositivi basati su un singolo modello di dispositivo. Scegliere il modello di dispositivo da usare per questo set.

1. Creare la query per identificare i dispositivi per il set di dispositivi selezionando una proprietà, un operatore di confronto e un valore. È possibile aggiungere più query per inserire nel set i dispositivi che soddisfano **tutti** i criteri. Il set di dispositivi creato è accessibile a chiunque abbia accesso all'applicazione, in modo che tutti gli utenti possano visualizzare, modificare o eliminare il set di dispositivi.

    ![Query per il set di dispositivi](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > Il set di dispositivi è una query dinamica. Ogni volta che si visualizza l'elenco dei dispositivi, possono essere presenti dispositivi diversi nell'elenco. L'elenco dipende da quali dispositivi attualmente soddisfano i criteri della query.

1. Scegliere **Salva**.

## <a name="configure-the-dashboard-for-your-device-set"></a>Configurare il dashboard per il set di dispositivi

Dopo aver creato il set di dispositivi è possibile configurare il relativo **Dashboard**. Il **Dashboard** è la Home page in cui inserire le immagini e i collegamenti. È anche possibile aggiungere griglie in cui sono elencati i dispositivi del set di dispositivi.

1. Scegliere **set di dispositivi** nel riquadro sinistro.

1. Selezionare il set di dispositivi.

1. Selezionare la scheda **Dashboard** .

1. Selezionare **Modifica**.

    ![Modalità progettazione attivata](media/howto-use-device-sets/image3.png)

1. Per informazioni sull'aggiunta di un'immagine, vedere [Preparare e caricare immagini nell'applicazione Azure IoT Central](howto-prepare-images.md).

1. Aggiungere un riquadro di collegamento:
    1. Scegliere **Link** (Collegamento) nel riquadro destro.
    1. In **Title** (Titolo) assegnare un titolo al collegamento.
    1. Scegliere un URL da aprire quando si seleziona il collegamento.
    1. Assegnare una descrizione al collegamento da mostrare sotto il **titolo**.
    1. Scegliere **Salva**.

        ![Salvare il collegamento](media/howto-use-device-sets/image7.png)

    1. È possibile spostare e ridimensionare il riquadro di collegamento nel **Dashboard**.

1. Aggiungere una griglia. Una griglia è una tabella dei dispositivi del set che include le colonne selezionate.
    1. Scegliere **Grid** (Griglia) nel riquadro destro.
    1. In **Title** (Titolo) assegnare un titolo alla griglia.
    1. Selezionare le colonne da mostrare scegliendo **Add/Remove** (Aggiungi/Rimuovi). Nel pannello che appare scegliere la colonna da visualizzare e scegliere la freccia destra per selezionarla.
    1. Scegliere **OK**.
    1. Scegliere **Salva**.

        ![Salvare la griglia](media/howto-use-device-sets/image9.png)

    1. Trascinare e rilasciare la griglia per inserirla nel **Dashboard**.

        > [!NOTE]
        > È possibile aggiungere più immagini, collegamenti e griglie.
  
    1. Selezionare **Operazione completata**.

Per altre informazioni su come usare i riquadri in Azure IoT Central, vedere [usare i riquadri del dashboard](howto-use-tiles.md).

### <a name="configure-a-location-map-in-your-device-sets-dashboard"></a>Configurare una mappa del percorso nel dashboard dei set di dispositivi

È possibile aggiungere una mappa per visualizzare la posizione dei dispositivi nel set di dispositivi.

Per aggiungere una mappa al dashboard dei set di dispositivi, è necessario aver configurato una proprietà di misurazione o posizione del percorso nel modello di dispositivo. Per altre informazioni, vedere [creare una misurazione della posizione](howto-set-up-template.md) o [creare una proprietà Location](howto-set-up-template.md).

1. Nel **Dashboard**del set di dispositivi selezionare **mapping** dalla libreria.
2. Aggiungere un titolo e scegliere la misurazione della posizione o la proprietà configurata in precedenza.
3. Selezionare **Save (Salva** ). il riquadro mappa mostra le ultime posizioni note dei dispositivi nel set di dispositivi.
4. Quando un operatore Visualizza il dashboard dei set di dispositivi, l'operatore Visualizza tutti i riquadri configurati, inclusa la mappa del percorso.

È possibile ridimensionare il riquadro mappa nel dashboard. Selezionando un pin nella mappa vengono visualizzate le informazioni sul dispositivo, il nome e la posizione. Selezionare il popup per passare alla pagina delle proprietà del dispositivo.

## <a name="configure-the-list-for-your-device-set"></a>Configurare l'elenco per il set di dispositivi

Dopo aver creato il set di dispositivi, è possibile configurare **List** (Elenco). Un **elenco** mostra tutti i dispositivi del set di dispositivi e include le colonne selezionate.

1. Scegliere **set di dispositivi** nel riquadro sinistro.

1. Scegliere la scheda **List** (Elenco).

1. Scegliere **Column Options** (Opzioni colonne).

    ![Opzioni colonne](media/howto-use-device-sets/image11.png)

1. Scegliere le colonne da mostrare selezionando la colonna che si vuole visualizzare e facendo clic sulla freccia destra per selezionarla.

    ![Scegliere la colonna](media/howto-use-device-sets/image12.png)

1. Scegliere **OK**.

## <a name="analytics"></a>Analytics

L'analisi nei set di dispositivi corrisponde alla scheda analisi principale nel riquadro sinistro. È possibile approfondire le analisi nell'articolo dedicato a [come creare le analisi](howto-use-device-sets.md).

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la procedura per usare i set di dispositivi nell'applicazione Azure IoT Central, il prossimo passo suggerito è:

> [!div class="nextstepaction"]
> [Come creare regole dei dati di telemetria](howto-create-telemetry-rules.md)
