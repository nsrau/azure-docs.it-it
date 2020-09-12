---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: c7be10e14daf3ee769e86d51f648cc6b656a416a
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89303052"
---
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Verificare la CI/CD di IoT Edge con la compilazione e le pipeline di rilascio

Per attivare un processo di compilazione, è possibile eseguire il push di un commit al repository del codice sorgente o attivarlo manualmente. In questa sezione si attiva manualmente la pipeline CI/CD per verificarne il funzionamento. Si verifica quindi che la distribuzione abbia esito positivo.

1. Dal menu riquadro a sinistra selezionare **pipeline** e aprire la pipeline di compilazione creata all'inizio di questo articolo.

2. È possibile attivare un processo di compilazione nella pipeline di compilazione selezionando il pulsante **Esegui pipeline** in alto a destra.

    ![Attivare manualmente la pipeline di compilazione usando il pulsante Esegui pipeline](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/manual-trigger.png)

3. Esaminare le impostazioni di **esecuzione della pipeline** . Selezionare quindi **Esegui**.

    ![Specificare le opzioni di esecuzione della pipeline e selezionare Esegui](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/run-pipeline-settings.png)

4. Selezionare il **processo dell'agente 1** per controllare lo stato dell'esecuzione. È possibile esaminare i log dell'output del processo selezionando il processo. 

    ![Esaminare l'output del log del processo](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/view-job-run.png)

5. Se la pipeline di compilazione viene completata correttamente, viene attivata una fase di **sviluppo** . La versione di **sviluppo** riuscita crea IOT Edge distribuzione per i dispositivi IOT Edge di destinazione.

    ![Rilascia a dev](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/pending-approval.png)

6. Fare clic su **dev** stage per visualizzare i log della versione.

    ![Log di versione](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/release-logs.png)