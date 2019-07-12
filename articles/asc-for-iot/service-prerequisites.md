---
title: Il Centro sicurezza di Azure per IoT prerequisiti Preview | Microsoft Docs
description: Dettagli di tutti gli elementi necessari per iniziare con il Centro sicurezza di Azure per i prerequisiti del servizio IoT.
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
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 198459887ff19b16e897b2a8dde55bca1217c8ac
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616440"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Il Centro sicurezza di Azure per IoT prerequisiti

> [!IMPORTANT]
> Centro sicurezza di Azure per IoT è attualmente in versione di anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo fornisce una spiegazione dei diversi blocchi predefiniti del Azure Security Center (ASC) per IoT servizio, ciò che occorre per iniziare e concetti di base per consentire di comprendere il servizio. 

## <a name="minimum-requirements"></a>Requisiti minimi

- Livello Standard dell'Hub IoT
    - Ruolo RBAC **proprietario** i privilegi di livello 
- [Area di lavoro di log Analitica](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Centro sicurezza di Azure (scelta consigliata)
    - Durante l'uso del Centro sicurezza di Azure è solo un'indicazione e non un requisito, in caso contrario, sarà possibile visualizzare le altre risorse di Azure all'interno dell'IoT Hub. 
 
## <a name="working-with-asc-for-iot-service"></a>Uso di Centro sicurezza di AZURE per il servizio IoT

Centro sicurezza di AZURE per informazioni dettagliate IoT e i report sono disponibili tramite l'IoT Hub di Azure e Centro sicurezza di Azure. Per abilitare Centro sicurezza di AZURE per IoT sull'IoT Hub Azure, un account con **proprietario** sono necessari privilegi di livello. Dopo l'abilitazione di Centro sicurezza di AZURE per IoT nell'IoT Hub, Centro sicurezza di AZURE per IoT insights vengono visualizzati come le **sicurezza** funzionalità nell'IoT Hub di Azure e come **IoT** nel Centro sicurezza di Azure. 

## <a name="supported-service-regions"></a>Aree del servizio supportate 

Centro sicurezza di AZURE per IoT è attualmente supportata per gli hub IoT nelle aree di Azure seguenti:
  - Stati Uniti centrali
  - Europa settentrionale
  - Asia sud-orientale

## <a name="wheres-my-iot-hub"></a>Dove si trova l'Hub IoT?

Controllare la posizione dell'IoT Hub per verificare la disponibilità del servizio prima di iniziare. 

1. Aprire l'hub IoT. 
2. Fare clic su **Panoramica**. 
3. Verificare il percorso elencato corrisponda a uno dei [servizio aree supportate](#supported-service-regions). 


## <a name="supported-platforms-for-agents"></a>Piattaforme supportate per gli agenti 

Centro sicurezza di AZURE per gli agenti IoT supporta un elenco in continua crescita di dispositivi e piattaforme. Vedere le [elenco di piattaforme supportate](how-to-deploy-agent.md) per controllare la libreria di dispositivo esistente o pianificato.  

## <a name="next-steps"></a>Passaggi successivi
- [Panoramica](overview.md)
- [Abilitare il servizio](quickstart-onboard-iot-hub.md)
- [Centro sicurezza di AZURE per IoT domande frequenti](resources-frequently-asked-questions.md)
- [La comprensione di Centro sicurezza di AZURE per gli avvisi di IoT](concept-security-alerts.md)
