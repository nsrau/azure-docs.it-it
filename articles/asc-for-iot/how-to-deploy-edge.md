---
title: Distribuire un Centro sicurezza di AZURE per il modulo di IoT Edge | Microsoft Docs
description: Informazioni su come distribuire Centro sicurezza di AZURE per l'agente di sicurezza IoT in dispositivi perimetrali IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 87094b265a0c30c0349d64e4b956224525c04f74
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580494"
---
# <a name="deploy-security-module-on-your-iot-edge-device"></a>Distribuire un modulo di sicurezza nel dispositivo IoT Edge

> [!IMPORTANT]
> Centro sicurezza di AZURE per IoT è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Centro sicurezza di AZURE per IoT **azureiotsecurity** modulo offre una soluzione di sicurezza completa per il dispositivo IoT Edge.
Modulo di sicurezza raccoglie, aggrega e analizza i dati di sicurezza non elaborati dal sistema del sistema operativo e un contenitore in avvisi e raccomandazioni sulla sicurezza utili.
Per altre informazioni, vedere [modulo di sicurezza per IoT Edge](security-edge-architecture.md).

In questa guida descrive come distribuire un modulo di protezione nel dispositivo IoT Edge.

## <a name="deploy-security-module"></a>Distribuire un modulo di protezione

Usare la procedura seguente per distribuire un Centro sicurezza di AZURE per il modulo di sicurezza IoT per IoT Edge.

### <a name="prerequisites"></a>Prerequisiti

1. Assicurarsi che il dispositivo sia [registrato come un dispositivo IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-register-device-portal).

1. Il Centro sicurezza di AZURE per il modulo di IoT Edge richiede la [AuditD framework](https://linux.die.net/man/8/auditd) installata nel dispositivo Edge.

   Installare il framework eseguendo il comando seguente nel dispositivo Edge:
   
   `apt-get install auditd audispd-plugins`

### <a name="deployment-using-azure-portal"></a>Distribuzione mediante il portale di Azure

1. Aprire **Marketplace** nel portale di Azure.

1. Cercare **sicurezza di azure iot** e fare clic su **sicurezza di Azure IoT**.

   ![](media/howto/edge_onboarding_7.png)

1. Fare clic su **Crea**.

1. Scegliere il **abbonamento**, **dell'IoT Hub** e **nome del dispositivo IoT Edge**, quindi fare clic su **Create**.

1. Fare clic su **successivo** due volte a **revisione distribuzione**.

1. Assicurarsi che **edgeHub.settings.createOptions** è configurato come indicato di seguito:

   `"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"`

1. Fare clic su **Submit** per completare la distribuzione.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle opzioni di configurazione, continuare alla Guida alle procedure per la configurazione del modulo. 
> [!div class="nextstepaction"]
> [Configurazione del modulo Guida alle procedure](./how-to-agent-configuration.md)