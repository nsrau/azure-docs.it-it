---
title: Distribuire il Centro sicurezza di Azure per il modulo di IoT Edge | Microsoft Docs
description: Informazioni su come distribuire un centro di sicurezza di Azure per l'agente di sicurezza IoT in dispositivi perimetrali IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/1/2019
ms.author: mlottner
ms.openlocfilehash: 5d56c878eab92a7152bc0b248cab5c2ebf7e6dde
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65198402"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Distribuire un modulo di protezione nel dispositivo IoT Edge

> [!IMPORTANT]
> Centro sicurezza di Azure per IoT è attualmente in versione di anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

**Azure Security Center (Centro sicurezza di AZURE) per IoT** modulo offre una soluzione di sicurezza completa per il dispositivo IoT Edge.
Modulo di sicurezza raccoglie, aggrega e analizza i dati di sicurezza non elaborati dal sistema del sistema operativo e un contenitore in avvisi e raccomandazioni sulla sicurezza utili.
Per altre informazioni, vedere [modulo di sicurezza per IoT Edge](security-edge-architecture.md).

In questa guida descrive come distribuire un modulo di protezione nel dispositivo IoT Edge.

## <a name="deploy-security-module"></a>Distribuire un modulo di protezione

Usare la procedura seguente per distribuire un Centro sicurezza di AZURE per il modulo di sicurezza IoT per IoT Edge.

### <a name="prerequisites"></a>Prerequisiti

- Nell'IoT Hub, assicurarsi che il dispositivo sia [registrato come un dispositivo IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).

- Centro sicurezza di AZURE per il modulo di IoT Edge richiede [AuditD framework](https://linux.die.net/man/8/auditd) è installato nel dispositivo IoT Edge.

    - Installare il framework eseguendo il comando seguente nel dispositivo IoT Edge:
   
      `sudo apt-get install auditd audispd-plugins`
   
    - Verificare che sia attivo AuditD eseguendo il comando seguente:
   
      `sudo systemctl status auditd`
      
        La risposta prevista è `active (running)`. 

### <a name="deployment-using-azure-portal"></a>Distribuzione mediante il portale di Azure

1. Dal portale di Azure, aprire **Marketplace**.

1. Selezionare **Internet delle cose**, quindi cercare **Centro sicurezza di Azure per IoT** e selezionarlo.

   ![Selezionare Centro sicurezza di Azure per IoT](media/howto/edge-onboarding-8.png)

1. Fare clic su **Create** per configurare la distribuzione. 

1. Scegliere di Azure **abbonamento** dell'IoT Hub, quindi selezionare la **dell'IoT Hub**.<br>Selezionare **Distribuisci in un dispositivo** per un singolo dispositivo di destinazione o selezionare **Distribuisci su larga scala** per supportare più dispositivi, fare clic su **Create**. Per altre informazioni sulla distribuzione su larga scala, vedere [come distribuire](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor). 

    >[!Note] 
    >Se è stato selezionato **distribuzione su larga scala**, aggiungere il nome del dispositivo e i dettagli prima di continuare con la **aggiungere moduli** scheda nelle istruzioni seguenti.     

Esistono tre passaggi per creare una distribuzione di IoT Edge per il Centro sicurezza di Azure per IoT. illustrati nelle sezioni seguenti. 

#### <a name="step-1-add-modules"></a>Passaggio 1: Aggiungi moduli

1. Dal **Add Modules** della scheda **moduli di distribuzione** area, fare clic su **AzureSecurityCenterforIoT**. 
   
1. Modifica il **name** al **azureiotsecurity**.
1. Modifica il **URI di immagine** al **mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.3**.
1. Verificare i **opzioni di creazione del contenitore** valore è impostato su:      
    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }    
    ```
1. Verificare che **proprietà desiderate del dispositivo gemello del modulo Set** sia selezionata e modificare l'oggetto di configurazione per:
      
    ``` json
      "properties.desired": {
        "azureiot*com^securityAgentConfiguration^1*0*0": {
        }
      }
      ```

1. Fare clic su **Save**.
1. Scorrere fino alla fine della scheda e selezionare **Configura le impostazioni di Runtime di Edge avanzata**.
   
   >[!Note]
   > Effettuare **non** disabilitare la comunicazione di AMQP per l'Hub di IoT Edge.
   > Il Centro sicurezza di Azure per il modulo di IoT richiede la comunicazione di AMQP con l'Hub di IoT Edge.
   
1. Modifica il **immagine** sotto **Hub di Edge** al **mcr.microsoft.com/ascforiot/edgehub:1.0.9-preview**.

   >[!Note]
   > Il Centro sicurezza di Azure per il modulo di IoT richiede una versione creata tramite fork di Hub di IoT Edge, base SDK versione 1.20.
   > Modificando l'immagine dell'Hub di IoT Edge, memorizza nel dispositivo IoT Edge per sostituire la versione stabile più recente con la versione creata tramite fork di Hub di IoT Edge, che non è ufficialmente supportato dal servizio IoT Edge.

1. Verificare **creare opzioni** è impostata su: 
         
    ``` json
    {
      "HostConfig": {
        "PortBindings": {
          "8883/tcp": [{"HostPort": "8883"}],
          "443/tcp": [{"HostPort": "443"}],
          "5671/tcp": [{"HostPort": "5671"}]
        }
      }
    }
    ```
      
1. Fare clic su **Save**.
   
1. Fare clic su **Avanti**.

#### <a name="step-2-specify-routes"></a>Passaggio 2: Specifica route 

1. Nel **specificare le route** scheda, impostare il **ASCForIoTToIoTHub** indirizzare a **"da messaggi/moduli/azureiotsecurity/\* in $upstream"**, fare clic su  **Avanti**.

   ![Specificare le route](media/howto/edge-onboarding-9.png)

#### <a name="step-3-review-deployment"></a>Passaggio 3: Esaminare la distribuzione

1. Nel **distribuzione rivedere** scheda, esaminare le informazioni sulla distribuzione e quindi selezionare **Submit** per completare la distribuzione.

## <a name="diagnostic-steps"></a>Passaggi di diagnostica

Se si verifica un problema, i log dei contenitori sono il modo migliore per apprendere lo stato di un dispositivo di modulo di protezione di IoT Edge. Usare i comandi e gli strumenti illustrati in questa sezione per raccogliere informazioni.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Verificare che i contenitori necessari siano installati e funzionino come previsto

1. Eseguire il comando seguente nel dispositivo IoT Edge:
    
     `sudo docker ps`
   
1. Verificare che siano in esecuzione i contenitori seguenti:
   
   | NOME | IMMAGINE |
   | --- | --- |
   | azureIoTSecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.3 |
   | edgeHub | mcr.microsoft.com/ascforiot/edgehub:1.0.9-preview |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0 |
   
   Se il requisito minimo necessario di contenitori non sono presenti, controllare se il manifesto di distribuzione di IoT Edge è allineato con le impostazioni consigliate. Per altre informazioni, vedere [modulo di distribuzione di IoT Edge](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Esaminare i registri di modulo per gli errori
   
1. Eseguire il comando seguente nel dispositivo IoT Edge:

   `sudo docker logs azureiotsecurity`
   
1. Per i log più dettagliati, aggiungere la variabile di ambiente seguenti **azureiotsecurity** distribuzione del modulo: `logLevel=Debug`.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle opzioni di configurazione, continuare alla Guida alle procedure per la configurazione del modulo. 
> [!div class="nextstepaction"]
> [Guida alle procedure di configurazione di modulo](./how-to-agent-configuration.md)
