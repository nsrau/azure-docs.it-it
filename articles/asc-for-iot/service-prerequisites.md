---
title: Prerequisiti & componenti
description: Dettagli di tutto il necessario per iniziare a usare il Centro sicurezza di Azure per i prerequisiti del servizio IoT.Details of all needed to get started with Azure Security Center for IoT service prerequisites.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2019
ms.author: mlottner
ms.openlocfilehash: fb5c42ad490ed04f14ff150093a44b552434ed9e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310589"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Prerequisiti del Centro sicurezza di Azure per IoTAzure Security Center for IoT prerequisites

Questo articolo fornisce una spiegazione dei diversi componenti del servizio Del servizio di sicurezza di Azure per IoT, gli elementi da iniziare e illustra i concetti di base per comprendere meglio il servizio.

## <a name="minimum-requirements"></a>Requisiti minimi

- Livello Hub Standard IoT
  - Privilegi **a** livello di proprietario del ruolo RBAC
- [Area di lavoro di Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)
- Centro sicurezza di Azure (scelta consigliata)Azure Security Center (recommended)
  - L'uso del Centro sicurezza di Azure è una raccomandazione e non un requisito. Senza Centro sicurezza di Azure, non sarà possibile visualizzare le altre risorse di Azure all'interno dell'hub IoT.Without Azure Security Center, you'll be unable to view your other Azure resources within IoT Hub.

## <a name="working-with-azure-security-center-for-iot-service"></a>Uso del Centro sicurezza di Azure per il servizio IoTWorking with Azure Security Center for IoT service

Azure Security Center for IoT insights and reporting are available using Azure IoT Hub and Azure Security Center. Per abilitare il Centro sicurezza di Azure per l'IoT nell'hub IoT di Azure, è necessario un account con privilegi a livello di **proprietario.** Dopo aver abilitato ASC per l'IoT nell'hub IoT, il Centro sicurezza di Azure per le informazioni dettagliate IoT viene visualizzato come funzionalità **di sicurezza** nell'hub IoT di Azure e come IoT nel Centro sicurezza di Azure.After enabling ASC for IoT in your IoT Hub, Azure Security Center for IoT insights are displayed as the Security feature in Azure IoT Hub and as **IoT** in Azure Security Center.

## <a name="supported-service-regions"></a>Aree del servizio supportate

Centro sicurezza di Azure per IoT è attualmente supportato per gli hub IoT nelle aree di Azure seguenti:Azure Security Center for IoT is currently supported for IoT Hubs in the following Azure regions:

- Stati Uniti centrali
- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti centro-occidentali
- Stati Uniti occidentali
- Stati Uniti occidentali 2
- Stati Uniti centro-meridionali
- Stati Uniti centro-settentrionali
- Canada centrale
- Canada orientale
- Europa settentrionale
- Brasile meridionale
- Francia centrale
- Regno Unito occidentale
- Regno Unito meridionale
- Europa occidentale
- Europa settentrionale
- Giappone occidentale
- Giappone orientale
- Australia sud-orientale
- Australia orientale
- Asia orientale
- Asia sud-orientale
- Corea centrale
- Corea meridionale
- India centrale
- India meridionale

Il Centro sicurezza di Azure per l'IoT instrada tutto il traffico da tutte le aree europee al data center regionale dell'Europa occidentale e a tutte le aree rimanenti verso il data center regionale degli Stati Uniti centrali.

## <a name="wheres-my-iot-hub"></a>Dov'è il mio hub IoT?

Controllare la posizione dell'hub IoT per verificare la disponibilità del servizio prima di iniziare.

1. Aprire l'hub IoT.
1. Fare clic su **Overview**.
1. Verificare che il percorso elencato corrisponda a una delle [aree](#supported-service-regions)di servizio supportate.

## <a name="supported-platforms-for-agents"></a>Piattaforme supportate per gli agenti

Il Centro sicurezza di Azure per gli agenti IoT supporta un elenco crescente di dispositivi e piattaforme. Vedere l'elenco delle [piattaforme supportate](how-to-deploy-agent.md) per controllare la libreria dei dispositivi esistente o pianificata.

## <a name="next-steps"></a>Passaggi successivi

- Leggere la [panoramica](overview.md) sulla sicurezza di Azure IoTRead the Azure IoT Security Overview
- Scopri come [abilitare il servizio](quickstart-onboard-iot-hub.md)
- Leggere le domande frequenti sul Centro sicurezza di [Azure per IoTRead the Azure Security Center for IoT FAQ](resources-frequently-asked-questions.md)
- Scopri come comprendere il [Centro sicurezza di Azure per gli avvisi IoT](concept-security-alerts.md)
