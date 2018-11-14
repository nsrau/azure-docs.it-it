---
title: Usare i set di dispositivi nell'applicazione Azure IoT Central | Microsoft Docs
description: Come usare i set di dispositivi nell'applicazione Azure IoT Central in qualità di operatore.
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: a5dd018197ff78ff0563349fd941308c3684a456
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2018
ms.locfileid: "51004120"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Usare i set di dispositivi nell'applicazione Azure IoT Central

L'articolo descrive come un operatore può usare i set di dispositivi nell'applicazione Azure IoT Central.

Un set di dispositivi è un elenco di dispositivi raggruppati in base ad alcuni criteri specificati. I set di dispositivi consentono di gestire, visualizzare e analizzare grandi quantità di dispositivi raggruppando i dispositivi in gruppi logici più piccoli. Ad esempio è possibile creare un elenco di tutti i dispositivi condizionatore di Seattle per consentire al tecnico di Seattle di trovare tutti i dispositivi di cui il tecnico è responsabile. Questo articolo descrive come creare e configurare i set di dispositivi.

## <a name="create-a-device-set"></a>Creare un set di dispositivi

Per creare un set di dispositivi:

1. Scegliere **Device Sets** (Set di dispositivi) nel menu di spostamento a sinistra.

1. Fare clic su **+ Nuovo**.

    ![Nuovo set di dispositivi](media/howto-use-device-sets/image1.png)

1. Assegnare al set di dispositivi un nome che sia univoco in tutta l'applicazione. È anche possibile aggiungere una descrizione. Un set di dispositivi può contenere solo dispositivi basati su un singolo modello di dispositivo. Scegliere il modello di dispositivo da usare per questo set.

1. Creare la query per identificare i dispositivi per il set di dispositivi selezionando una proprietà, un operatore di confronto e un valore. È possibile aggiungere più query per inserire nel set i dispositivi che soddisfano **tutti** i criteri. Il set di dispositivi creato è accessibile a chiunque abbia accesso all'applicazione, in modo che tutti gli utenti possano visualizzare, modificare o eliminare il set di dispositivi.

    ![Query per il set di dispositivi](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > Il set di dispositivi è una query dinamica. Ogni volta che si visualizza l'elenco dei dispositivi, possono essere presenti dispositivi diversi nell'elenco. L'elenco dipende da quali dispositivi attualmente soddisfano i criteri della query.

1. Scegliere **Salva**.

## <a name="configure-the-dashboard-for-your-device-set"></a>Configurare il Dashboard per il set di dispositivi

Dopo aver creato il set di dispositivi è possibile configurare il relativo **Dashboard**. Il **Dashboard** è la home page dove è possibile inserire immagini e collegamenti. È anche possibile aggiungere griglie in cui sono elencati i dispositivi del set di dispositivi.

1. Scegliere **Device Sets** (Set di dispositivi) nel menu di spostamento a sinistra.

1. Selezionare la scheda **Dashboard**.

1. Fare clic su **Modifica modello**.

    ![Modalità progettazione attivata](media/howto-use-device-sets/image3.png)

1. Per informazioni sull'aggiunta di un'immagine, vedere [Preparare e caricare immagini nell'applicazione Azure IoT Central](howto-prepare-images.md).

1. Aggiungere un riquadro di collegamento:
    1. Scegliere **Link** (Collegamento) nel riquadro destro.
    1. In **Title** (Titolo) assegnare un titolo al collegamento.
    1. Scegliere un URL da aprire quando viene selezionato il collegamento.
    1. Assegnare una descrizione al collegamento da mostrare sotto il **titolo**.
    1. Scegliere **Salva**.

        ![Salvare il collegamento](media/howto-use-device-sets/image7.png)

    1. È possibile spostare e ridimensionare il riquadro di collegamento nel **Dashboard**.

1. Aggiungere una griglia. Una griglia è una tabella dei dispositivi del set che include le colonne selezionate.
    1. Scegliere **Grid** (Griglia) nel riquadro destro.

        ![Scegliere Grid](media/howto-use-device-sets/image8.png)

    1. In **Title** (Titolo) assegnare un titolo alla griglia.
    1. Selezionare le colonne da mostrare scegliendo **Add/Remove** (Aggiungi/Rimuovi). Nel pannello che appare scegliere la colonna da visualizzare e scegliere la freccia destra per selezionarla.
    1. Scegliere **OK**.
    1. Scegliere **Salva**.

        ![Salvare la griglia](media/howto-use-device-sets/image9.png)

    1. Trascinare e rilasciare la griglia per inserirla nel **Dashboard**.

    > [!NOTE]
    > È possibile aggiungere più immagini, collegamenti e griglie.
  
    1. Fare clic su **Done**.

    ![Modalità progettazione disattivata](media/howto-use-device-sets/image10.png)


### <a name="configuring-location-map-in-your-device-sets-dashboard"></a>Configurazione della mappa delle località nel dashboard per il set di dispositivi 
È possibile aggiungere una mappa delle località per visualizzare la posizione del set di dispositivi in una mappa.

Per aggiungere una mappa delle località al dashboard per il set di dispositivi è necessario aver configurato la proprietà in Modello di dispositivo, vedere [Creare una proprietà Location con Mappe di Azure](howto-set-up-template.md).


1. Nel dashboard set di dispositivi selezionare Mappa nella libreria.

    ![Mappe del dashboard per il set di dispositivi](media/howto-use-device-sets/LocationMaps1.png)

2. Assegnare un titolo e scegliere la proprietà Location configurata in precedenza come parte della proprietà del dispositivo.
3. Salvare e nel riquadro della mappa verrà visualizzata la posizione dei dispositivi nel Set di dispositivi.
4. Quando un operatore visualizza il dashboard per il set di dispositivi, verranno visualizzati tutti i riquadri configurati inclusa la mappa delle località per visualizzare il riepilogo delle posizioni di tutti i dispositivi. 
    
> [!NOTE] 
> Sarà possibile ridimensionare la mappa alle dimensioni desiderate. Facendo clic su una puntina nella mappa verranno visualizzate le informazioni sul dispositivo, nome e posizione. È possibile fare clic nella finestra popup per passare alla pagina delle proprietà del dispositivo.  


## <a name="configure-the-list-for-your-device-set"></a>Configurare l'elenco per il set di dispositivi

Dopo aver creato il set di dispositivi, è possibile configurare **List** (Elenco). Un **elenco** mostra tutti i dispositivi del set di dispositivi e include le colonne selezionate.

1. Scegliere **Device Sets** (Set di dispositivi) nel menu di spostamento a sinistra.

1. Scegliere la scheda **List** (Elenco).

1. Scegliere **Column Options** (Opzioni colonne).

    ![Opzioni colonne](media/howto-use-device-sets/image11.png)

1. Scegliere le colonne da mostrare selezionando la colonna che si vuole visualizzare e facendo clic sulla freccia destra per selezionarla.

    ![Scegliere la colonna](media/howto-use-device-sets/image12.png)

1. Scegliere **OK**.

## <a name="analytics"></a>Analytics

Le analisi nei set di dispositivi sono la stesse della scheda Analytics principale nel menu di navigazione a sinistra. È possibile approfondire le analisi nell'articolo dedicato a [come creare le analisi](howto-create-analytics.md).

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la procedura per usare i set di dispositivi nell'applicazione Azure IoT Central, il prossimo passo suggerito è:

> [!div class="nextstepaction"]
> [Come creare regole dei dati di telemetria](howto-create-telemetry-rules.md)
