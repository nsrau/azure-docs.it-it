---
title: 'Avvio rapido: Configurare Azure Kinect DK'
description: Questo argomento di avvio rapido include le istruzioni per configurare l'hardware del dispositivo Azure Kinect DK
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: quickstart
ms.date: 02/12/2020
keywords: azure, kinect, dev kit, azure dk, configurazione, hardware, rapido, usb, alimentazione, visualizzatore, sensore, streaming, configurare, SDK, firmware
ms.custom:
- CI 114092
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: b33565f319de77385c966f742fa52391f4869eed
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211279"
---
# <a name="quickstart-set-up-your-azure-kinect-dk"></a>Avvio rapido: Configurare Azure Kinect DK

Questo argomento di avvio rapido include le istruzioni per configurare il dispositivo Azure Kinect DK. Verrà illustrato come testare la visualizzazione dello streaming dei sensori e come usare il [visualizzatore di Azure Kinect](azure-kinect-viewer.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="system-requirements"></a>Requisiti di sistema

Controllare i [requisiti di sistema](system-requirements.md) per verificare che la configurazione del PC host soddisfi tutti i requisiti minimi di Azure Kinect DK.

## <a name="set-up-hardware"></a>Configurare l'hardware

> [!NOTE]
> Assicurarsi di rimuovere la pellicola protettiva della fotocamera prima di usare il dispositivo.

1. Collegare il connettore di alimentazione al jack di alimentazione sul retro del dispositivo. Connettere l'alimentatore USB all'altra estremità del cavo, quindi collegare l'alimentatore a una presa elettrica.
2. Connettere il cavo dati USB al dispositivo e quindi a una porta USB 3.0 del PC.
   >[!NOTE]
   >Per risultati ottimali, connettere il cavo direttamente al dispositivo e al PC. Evitare di usare prolunghe o altri adattatori nella connessione.

3. Verificare che il LED indicatore di alimentazione (accanto al cavo USB) emetta una luce bianca continua.
  
   L'accensione del dispositivo richiede alcuni secondi. Quando l'indicatore di streaming LED anteriore si spegne, significa che il dispositivo è pronto per l'uso.  

   Per altre informazioni sul LED indicatore di alimentazione, vedere [Cosa significa la luce?](hardware-specification.md#what-does-the-light-mean)

    ![Funzionalità complete del dispositivo](./media/quickstarts/full-device-features.png)

## <a name="download-the-sdk"></a>Scaricare l'SDK

1. Selezionare il collegamento per [scaricare l'SDK](sensor-sdk-download.md).
2. Installare l'SDK nel PC.

## <a name="update-firmware"></a>Aggiornare il firmware

Per il corretto funzionamento dell'SDK, è necessaria l'ultima versione del firmware del dispositivo. Per controllare e aggiornare la versione del firmware, seguire la procedura descritta in [Aggiornare il firmware del dispositivo Azure Kinect DK](update-device-firmware.md).

## <a name="verify-that-the-device-streams-data"></a>Verificare che il dispositivo trasmetta dati

1. Avviare il [visualizzatore di Azure Kinect](azure-kinect-viewer.md). Per avviare questo strumento, è possibile usare uno di questi metodi:
   - Avviare il visualizzatore dalla riga di comando oppure facendo doppio clic sul file eseguibile. Il file `k4aviewer.exe` si trova nella directory di strumenti dell'SDK, ad esempio `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe`, dove `X.Y.Z` corrisponde alla versione installata dell'SDK.
   - Avviare il visualizzatore di Azure Kinect dal menu **Start** del dispositivo.
2. nel visualizzatore di Azure Kinect selezionare **Open Device** > **Start** (Apri dispositivo > Avvia).

    ![Visualizzatore di Azure Kinect](./media/quickstarts/viewer.png)

3. Verificare che lo strumento visualizzi il flusso di ogni sensore:
   - Fotocamera di profondità
   - Fotocamera a colori
   - Fotocamera a infrarossi
   - IMU
   - Microfoni

    ![Strumento di visualizzazione](./media/quickstarts/visualization-tool.png)

La configurazione di Azure Kinect DK è stata completata. Ora è possibile iniziare a sviluppare l'applicazione o a integrare servizi.

In caso di problemi, vedere [Risoluzione dei problemi](troubleshooting.md).

## <a name="see-also"></a>Vedere anche

- [Informazioni sull'hardware di Azure Kinect DK](hardware-specification.md)
- [Aggiornare il firmware del dispositivo](update-device-firmware.md)
- Altre informazioni sul [visualizzatore di Azure Kinect](azure-kinect-viewer.md)

## <a name="next-steps"></a>Passaggi successivi

Quando il dispositivo Azure Kinect DK è pronto e funzionante, è anche possibile leggere altre informazioni su come
> [!div class="nextstepaction"]
> [Registrare i flussi dei sensori su un file](record-sensor-streams-file.md)
