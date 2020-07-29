---
title: Come usare il portale di Azure per richiamare metodi diretti
description: Questo articolo illustra l'uso del portale di Azure per richiamare metodi diretti.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: how-to
ms.custom: ''
ms.date: 07/24/2020
ms.author: inhenkel
ms.openlocfilehash: 763dd82c8263a5e180468f9fbd7f86526295a80d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87279288"
---
# <a name="how-to-use-azure-portal-to-invoke-direct-methods"></a>Come usare il portale di Azure per richiamare metodi diretti

L'hub IoT offre la possibilità di richiamare [metodi diretti](/azure/iot-hub/iot-hub-devguide-direct-methods#method-invocation-for-iot-edge-modules) nei dispositivi perimetrali dal cloud. Il modulo Analisi video live in Azure IoT Edge espone diversi [metodi diretti](/azure/media-services/live-video-analytics-edge/direct-methods) che è possibile usare per definire, distribuire e creare un'istanza di flussi di lavoro diversi per l'analisi di video live.

Questo articolo illustra come richiamare chiamate a metodi diretti in Analisi video live per un modulo IoT Edge tramite il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Il modulo Analisi video live in IoT Edge deve essere in esecuzione nel dispositivo perimetrale, usando i metodi descritti in [Avvio rapido: Analisi video live in IoT Edge](/azure/media-services/live-video-analytics-edge/get-started-detect-motion-emit-events-quickstart) oppure tramite il [portale](/azure/media-services/live-video-analytics-edge/deploy-iot-edge-device).

* Si conosce il funzionamento di [Analisi video live](/azure/media-services/live-video-analytics-edge/overview) e il [concetto di grafico multimediale](/azure/media-services/live-video-analytics-edge/media-graph-concept).

## <a name="invoking-direct-methods-via-azure-portal"></a>Richiamo di metodi diretti tramite il portale di Azure

Ognuno dei [metodi diretti](/azure/media-services/live-video-analytics-edge/direct-methods) esposti dal modulo Analisi video live può essere richiamato tramite il portale di Azure. La procedura seguente consente di ottenere i dettagli relativi a un singolo metodo diretto. È possibile richiamare altri metodi diretti usando una procedura analoga. Per ogni metodo diretto è però richiesto un corpo JSON specifico.

Usare la chiamata al metodo `GraphTopologyList` per recuperare un elenco di tutte le topologie di grafico attualmente distribuite nel modulo Analisi video live in IoT Edge. Per richiamare questo metodo diretto, seguire questa procedura:

1. Accedere al portale di Azure
1. Individuare l'hub IoT nel gruppo di risorse pertinente nella home page del portale oppure se si conosce quale sia l'hub IoT selezionarlo direttamente.
    ![Gruppo di risorse nella home page del portale](media/use-azure-portal-to-invoke-directs-methods/portal-rg-home.png)
1. Nella pagina Hub IoT selezionare IoT Edge in Gestione dispositivi automatica per elencare i vari ID dispositivo. Selezionare l'ID dispositivo pertinente per elencare i moduli in esecuzione nel dispositivo.
    ![Pagina Hub IoT](media/use-azure-portal-to-invoke-directs-methods/iot-hub-page.png)
1. Selezionare il modulo Analisi video live in IoT Edge per visualizzarne la pagina di configurazione.<br><br>
    ![Selezionare il modulo Analisi video live in IoT Edge per visualizzarne la pagina di configurazione](media/use-azure-portal-to-invoke-directs-methods/modules.png)
1. Selezionare l'opzione di menu Metodo diretto. <br><br>
    ![Fare clic sull'opzione di menu Metodo diretto](media/use-azure-portal-to-invoke-directs-methods/module-details.png)
    > [!NOTE]
    > Potrebbe essere necessario aggiungere un valore nelle sezioni Stringa di connessione, come si può vedere nella pagina corrente. Non è necessario nascondere o modificare alcun valore nella sezione Nome impostazione. È accettabile lasciarlo impostato su pubblico.

1. Digitare *GraphTopologyList* nel campo **Nome metodo**.
1. Copiare e incollare il codice JSON seguente nel campo **Payload**.
    ```json
    {
    "@apiVersion":
    }
    ```
1. Selezionare il pulsante **Richiama metodo** nella parte superiore della pagina.<br><br>
    ![Pulsante Richiama metodo](media/use-azure-portal-to-invoke-directs-methods/direct-method.png)
1. Verrà visualizzato un messaggio di stato 200 nell'area **Risultato**.<br><br>
    ![Timeout della connessione](media/use-azure-portal-to-invoke-directs-methods/connection-timeout.png)

## <a name="responses"></a>Risposte

| Condizione             | Codice di stato | Codice di errore dettagliato |
|-----------------------|-------------|---------------------|
| Operazione completata               | 200         | N/D                 |
| Errori utente generici   | Intervallo 400   |                     |
| Errori server generici | Intervallo 500   |                     |

## <a name="next-steps"></a>Passaggi successivi

Per altri metodi diretti, vedere la pagina [Metodi diretti](/azure/media-services/live-video-analytics-edge/direct-methods).

> [!NOTE]
> Con un'istanza del grafico viene creata un'istanza di una topologia specifica. Assicurarsi quindi di avere impostato la topologia corretta prima di creare un'istanza del grafico.

[Avvio rapido: Rilevare gli eventi di generazione del movimento](/azure/media-services/live-video-analytics-edge/get-started-detect-motion-emit-events-quickstart) include informazioni utili per comprendere la sequenza esatta delle chiamate a metodo diretto da effettuare.
