---
title: 'Avvio rapido: Configurare e abilitare il modulo di sicurezza per Azure RTOS'
description: Informazioni su come eseguire l'onboarding e abilitare il servizio Modulo di sicurezza per Azure RTOS nell'hub IoT di Azure.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: 6f438bd7dd0b3e45fd292947a3b08db80401b825
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944411"
---
# <a name="quickstart-security-module-for-azure-rtos-preview"></a>Avvio rapido: Modulo di sicurezza per Azure RTOS (anteprima)

Questo articolo illustra i prerequisiti da soddisfare prima di abilitare il servizio Modulo di sicurezza per Azure RTOS in un hub IoT. Se non si ha già un hub IoT, vedere [Creare un hub IoT usando il portale di Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) per iniziare.

> [!NOTE]
> Il modulo di sicurezza per Azure RTOS è supportato solo negli hub IoT di livello Standard.

## <a name="prerequisites"></a>Prerequisiti 

### <a name="supported-devices"></a>Dispositivi supportati

- ST STM32F746G Discovery Kit
- NXP i.MX RT1060 EVK
- Microchip SAM E54 Xplained Pro EVK

Scaricare, compilare ed eseguire uno dei file con estensione zip per la scheda e lo strumento specifici (IAR, semi IDE o PC) a scelta dalla [risorsa GitHub del modulo di sicurezza per Azure RTOS](hhtps://github.com/azure-rtos/azure-iot-preview/releases).

### <a name="azure-resources"></a>Risorse di Azure

La fase successiva per iniziare consiste nel preparare le risorse di Azure. È necessario un hub IoT ed è consigliabile usare un'area di lavoro Log Analytics. Per l'hub IoT, è necessaria la stringa di connessione dell'hub IoT per connettersi al dispositivo. 
  
### <a name="iot-hub-connection"></a>Connessione dell'hub IoT

Per iniziare, è necessaria una connessione dell'hub IoT. 

1. Aprire l'**hub IoT** nel portale di Azure.
1. Copiare la stringa di connessione IoT nel [file di configurazione](how-to-azure-rtos-security-module.md).


Le credenziali per le connessioni vengono ricavate di valori di **HOST_NAME**, **DEVICE_ID** e **DEVICE_SYMMETRIC_KEY** della configurazione dell'applicazione utente.

Il modulo di sicurezza per Azure RTOS usa le connessioni middleware di Azure IoT basate sul protocollo **MQTT**.


### <a name="log-analytics-workspace"></a>Area di lavoro Log Analytics

L'inserimento di Log Analytics nell'hub IoT è disattivato per impostazione predefinita nella soluzione Defender per IoT. Per abilitarlo per l'uso del modulo di sicurezza per Azure RTOS, seguire questa procedura: 
1. Nel portale di Azure passare all'hub IoT.
1. Scegliere **Impostazioni** dal menu **Sicurezza**.
   :::image type="content" source="media/quickstart/azure-rtos-hub-settings.png" alt-text="Accesso all'opzione di raccolta dati per Azure RTOS"::: 
1. Selezionare **Raccolta di dati**. 
1. Nell'opzione **Configurazione dell'area di lavoro** impostare l'interruttore su **Sì**. 
1. Creare una nuova area di lavoro Log Analytics o collegarne una esistente. Assicurarsi che l'opzione **Access to raw security data** (Accesso a dati non elaborati sulla sicurezza) sia selezionata. 
 :::image type="content" source="media/quickstart/azure-rtos-data-collection-on.png" alt-text="Configurazione di Azure RTOS che mostra l'opzione di raccolta dati e le opzioni per i dati non elaborati sulla sicurezza selezionate":::
1. Selezionare **Salva**
1. Tornare all'elenco delle risorse di Azure e verificare che l'area di lavoro Log Analytics creata o collegata sia abilitata per l'hub IoT.
    :::image type="content" source="media/quickstart/verify-azure-resource-list.png" alt-text="Controllare l'elenco delle risorse di Azure per verificare l'aggiunta dell'area di lavoro Log Analytics corretta per un hub IoT"::: 

## <a name="next-steps"></a>Passaggi successivi

Procedere con l'articolo successivo per completare la configurazione e la personalizzazione della soluzione.

> [!div class="nextstepaction"]
> [Configurare il modulo di sicurezza per Azure RTOS](how-to-azure-rtos-security-module.md)
