---
title: Distribuire il modulo Centro sicurezza di Azure per IoT Edge Documenti Microsoft
description: Informazioni su come distribuire un security agent del Centro sicurezza di Azure per IoT in IoT Edge.Learn about how to deploy an Azure Security Center for IoT security agent on IoT Edge.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 3aee30e8ad82f9657c3bc9e97a7657a1e8c7989d
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548919"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Distribuire un modulo di sicurezza nel dispositivo IoT Edge


**Il modulo Centro sicurezza di Azure per IoT** offre una soluzione di sicurezza completa per i dispositivi IoT Edge.Azure Security Center for IoT module provides a comprehensive security solution for your IoT Edge devices.
Il modulo di sicurezza raccoglie, aggrega e analizza i dati di sicurezza non elaborati dal sistema operativo e dal sistema contenitore in avvisi e consigli di sicurezza utilizzabili.
Per ulteriori informazioni, consultate [Modulo di sicurezza per IoT Edge.](security-edge-architecture.md)

In questo articolo verrà illustrato come distribuire un modulo di sicurezza nel dispositivo IoT Edge.In this article, you'll learn how to deploy a security module on your IoT Edge device.

## <a name="deploy-security-module"></a>Distribuire il modulo di sicurezzaDeploy security module

Usare la procedura seguente per distribuire un modulo di sicurezza del Centro sicurezza di Azure per IoT per IoT Edge.Use the following steps to deploy an Azure Security Center for IoT security module for IoT Edge.

### <a name="prerequisites"></a>Prerequisiti

1. Nell'hub IoT, assicurati che il dispositivo sia [registrato come dispositivo IoT Edge.](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)

1. Il modulo Centro sicurezza di Azure per IoT Edge richiede che il framework AuditD sia installato nel dispositivo IoT Edge.Azure Security Center for IoT Edge module requires the [AuditD framework](https://linux.die.net/man/8/auditd) is installed on the IoT Edge device.

    - Installare il framework eseguendo il comando seguente nel dispositivo IoT Edge:
   
    `sudo apt-get install auditd audispd-plugins`

    - Verificare che AuditD sia attivo eseguendo il comando seguente: 
   
    `sudo systemctl status auditd`<br>
    - La risposta prevista è:`active (running)` 
        

### <a name="deployment-using-azure-portal"></a>Distribuzione tramite il portale di AzureDeployment using Azure portal

1. Dal portale di Azure aprire **Marketplace.**

1. Selezionare **Internet of Things**, quindi cercare Centro sicurezza di Azure per **IoT** e selezionarlo.

   ![Selezionare Centro sicurezza di Azure per IoTSelect Azure Security Center for IoT](media/howto/edge-onboarding-8.png)

1. Fare clic su **Crea** per configurare la distribuzione. 

1. Scegliere la **sottoscrizione** di Azure dell'hub IoT, quindi selezionare l'hub **IoT**.<br>Selezionare **Distribuisci in un dispositivo** per scegliere come destinazione un singolo dispositivo o **Distribuisci su scala** per la destinazione di più dispositivi e fare clic su **Crea**. Per ulteriori informazioni sulla distribuzione su larga scala, vedere [Come distribuire](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor). 

    >[!Note] 
    >Se è stata selezionata l'opzione **Distribuisci in scala**, aggiungere il nome e i dettagli del dispositivo prima di continuare nella scheda **Aggiungi moduli** nelle istruzioni seguenti.     

Complete each step to complete your IoT Edge deployment for Azure Security Center for IoT. 

#### <a name="step-1-modules"></a>Fase 1: Moduli

1. Selezionare il modulo **AzureSecurityCenterforIoT.Select the AzureSecurityCenterforIoT** module.
1. Nella scheda **Impostazioni modulo** modificare il **nome in** **azureiotsecurity**.
1. Nella scheda **Variabili di ambiente** aggiungere una variabile, se necessario, ad esempio a livello di debug.
1. Nella scheda **Opzioni di creazione contenitore** aggiungere la configurazione seguente:On the Container Create Options tab, add the following configuration:

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
    
1. Nella scheda **Impostazioni modulo twin** aggiungere la seguente configurazione:
      
    ``` json
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
    ```

1. Selezionare **Aggiorna**.

#### <a name="step-2-runtime-settings"></a>Passaggio 2: Impostazioni di runtimeStep 2: Runtime settings

1. Selezionare **Impostazioni runtime**.
1. In **Hub Edge**, modificare **l'immagine** in **mcr.microsoft.com/azureiotedge-hub:1.0.8.3**.
1. Verificare che **le opzioni** di creazione siano impostate sulla seguente configurazione: 
         
    ``` json
    { 
       "HostConfig":{ 
          "PortBindings":{ 
             "8883/tcp":[ 
                { 
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[ 
                { 
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[ 
                { 
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```
    
1. Selezionare **Save** (Salva).
   
1. Fare clic su **Avanti**.

#### <a name="step-3-specify-routes"></a>Passaggio 3: Specificare i percorsiStep 3: Specify routes 

1. Nella scheda **Specifica route** assicurarsi di disporre di una route (esplicita o implicita) che invierà i messaggi dal modulo **azureiotsecurity** a **$upstream** in base agli esempi seguenti. Solo quando il percorso è attivo, selezionare **Avanti**.

   Percorsi di esempio:

    ~~~Default implicit route
    "route": "FROM /messages/* INTO $upstream" 
    ~~~

    ~~~Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ~~~

1. Fare clic su **Avanti**.

#### <a name="step-4-review-deployment"></a>Passaggio 4: Esaminare la distribuzioneStep 4: Review deployment

- Nella scheda **Verifica distribuzione** esaminare le informazioni di distribuzione, quindi selezionare **Crea** per completare la distribuzione.

## <a name="diagnostic-steps"></a>Passaggi diagnostici

Se si verifica un problema, i log del contenitore sono il modo migliore per conoscere lo stato di un dispositivo del modulo di sicurezza IoT Edge.If you encounter an issue, container logs are the best way to learn about the state of an IoT Edge security module device. Usare i comandi e gli strumenti illustrati in questa sezione per raccogliere informazioni.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Verificare che i contenitori necessari siano installati e funzionino come previsto

1. Eseguire il comando seguente sul dispositivo IoT Edge:
    
    `sudo docker ps`
   
1. Verificare che siano in esecuzione i contenitori seguenti:
   
   | Nome | IMAGE |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |
   
   Se i contenitori minimi necessari non sono presenti, verificare se il manifesto di distribuzione di IoT Edge è allineato con le impostazioni consigliate. Per ulteriori informazioni, consultate [Distribuire il modulo Deploy IoT Edge](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Esaminare i registri dei moduli per individuare eventuali errori
   
1. Eseguire il comando seguente sul dispositivo IoT Edge:

   `sudo docker logs azureiotsecurity`
   
1. Per log più dettagliati, aggiungere la seguente variabile di ambiente `logLevel=Debug`alla distribuzione del modulo **azureiotsecurity:** .

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle opzioni di configurazione, continuare con la guida alle procedure per la configurazione del modulo. 
> [!div class="nextstepaction"]
> [Guida alla configurazione del modulo](./how-to-agent-configuration.md)
