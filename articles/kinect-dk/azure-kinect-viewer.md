---
title: Visualizzatore Kinect di Azure
description: Informazioni su come visualizzare tutti i flussi di dati del dispositivo usando il Visualizzatore Kinect di Azure.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, sensore, visualizzatore, visualizzazione, profondità, RGB, colore, IMU, audio, microfono, punto cloud
ms.openlocfilehash: 57cf7df831e97da4143a7f196b69a3a10609a017
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276676"
---
# <a name="azure-kinect-viewer"></a>Visualizzatore Kinect di Azure

Il Visualizzatore Kinect di Azure, disponibile nella directory degli strumenti installati come, `k4aviewer.exe` ad esempio, `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe` dove `X.Y.Z` è la versione installata dell'SDK, può essere usato per visualizzare tutti i flussi di dati del dispositivo a:

* Verificare che i sensori funzionino correttamente.
* Guida al posizionamento del dispositivo.
* Sperimentare le impostazioni della fotocamera.
* Leggere la configurazione del dispositivo.
* Registrazioni di riproduzione effettuate con il [registratore Kinect di Azure](azure-kinect-recorder.md).

Per altre informazioni sul Visualizzatore Kinect di Azure, vedere [come usare il video di Azure Kinect](https://www.microsoft.com/videoplayer/embed/RE3hNwG).

Il Visualizzatore Kinect di Azure è [Open Source](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/tools/k4aviewer) e può essere usato come esempio per l'uso delle API.

## <a name="use-viewer"></a>USA Visualizzatore

Il visualizzatore può funzionare in due modalità: con dati dinamici dal sensore o da dati registrati ([registratore Kinect di Azure](azure-kinect-recorder.md)).

### <a name="start-application"></a>Avvia applicazione

Avviare l'applicazione eseguendo `k4aviewer.exe` .

![Verifica della versione del firmware del dispositivo tramite Visualizzatore](./media/how-to-guides/open-viewer.png)

### <a name="use-the-viewer-with-live-data"></a>Usare il Visualizzatore con dati dinamici

1. Nella sezione **Apri dispositivo** selezionare il numero di **serie** del dispositivo da aprire. Quindi, selezionare **Aggiorna**, se il dispositivo è mancante.
2. Selezionare il pulsante **Apri dispositivo** .
3. Selezionare **Start** per avviare lo streaming dei dati con le impostazioni predefinite.

### <a name="use-the-viewer-with-recorded-data"></a>Usare il Visualizzatore con i dati registrati

Nella sezione **Apri registrazione** passare al file registrato e selezionarlo.

## <a name="check-device-firmware-version"></a>Controllare la versione del firmware del dispositivo

Accedere alla versione del firmware del dispositivo nella finestra di configurazione, come illustrato nella figura seguente.

![Verifica della versione del firmware del dispositivo tramite Visualizzatore](./media/how-to-guides/check-firmware-update.png)

Ad esempio, in questo caso, l'ISP della fotocamera Depth esegue FW 1.5.63.

## <a name="depth-camera"></a>Fotocamera di profondità

Il Visualizzatore di profondità della fotocamera visualizzerà due finestre:

* Uno è denominato *luminosità attiva* che è un'immagine in scala di grigi che mostra la luminosità IR.
* Il secondo è denominato *Depth*, che ha una rappresentazione colorata dei dati di profondità.

Posizionare il puntatore del mouse sul pixel della finestra profondità per visualizzare il valore del sensore di profondità, come illustrato di seguito.

![Visualizzazione profondità](./media/how-to-guides/depth-camera.png)

## <a name="rgb-camera"></a>Fotocamera RGB

L'immagine seguente mostra la visualizzazione della fotocamera dei colori.

![Visualizzazione RGB](./media/how-to-guides/viewer-rgb-camera.png)

È possibile controllare le impostazioni della fotocamera RGB dalla finestra di configurazione durante il flusso.

![Controlli della fotocamera RGB](./media/how-to-guides/rgb-camera-settings.png)

## <a name="inertial-measurement-unit-imu"></a>Unità di misura inerziale (IMU)

La finestra IMU ha due componenti, un accelerometro e un giroscopio.

La metà superiore è l'accelerometro e Mostra l'accelerazione lineare in metri al secondo<sup>2</sup>.  Include l'accelerazione dalla gravità. Pertanto, se si trova in un piano in una tabella, l'asse Z mostrerà probabilmente intorno a 9,8 m/s<sup>2</sup>.

La metà inferiore è la parte del giroscopio e Mostra lo spostamento rotazionale in radianti al secondo

![Visualizzazione del sensore di movimento](./media/how-to-guides/viewer-mu-settings.png)

## <a name="microphone-input"></a>Input microfono

La visualizzazione microfono mostra una rappresentazione del suono sentito in ogni microfono. Se non è presente alcun suono, il grafico viene visualizzato come vuoto. in caso contrario, verrà visualizzata una forma d'onda blu scuro con una forma d'onda Azzurra sovrapposta.

L'onda scura rappresenta i valori minimo e massimo osservati dal microfono nell'intervallo di tempo. L'onda chiara rappresenta il quadrato medio radice dei valori osservati dal microfono su tale intervallo di tempo.

![Visualizzazione input microfono](./media/how-to-guides/microphone-data.png)

## <a name="point-cloud-visualization"></a>Visualizzazione cloud Point

La profondità visualizzata in 3D consente di spostarsi nell'immagine usando le chiavi fornite.

![Cloud punto di profondità](./media/how-to-guides/depth-point-cloud.png)

## <a name="synchronization-control"></a>Controllo di sincronizzazione

È possibile usare il visualizzatore per configurare il dispositivo come modalità autonoma (predefinita), Master o subordinato quando si configura la sincronizzazione di più dispositivi.
Quando si modifica la configurazione o si inserisce/rimuove il cavo di sincronizzazione, selezionare **Aggiorna** per aggiornare.

![Controllo di sincronizzazione esterno](./media/how-to-guides/sync-control.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>[Guida alla configurazione della sincronizzazione esterna](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices)
