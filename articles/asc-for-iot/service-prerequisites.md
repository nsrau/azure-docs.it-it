---
title: Centro sicurezza di AZURE per IoT prerequisiti Preview | Microsoft Docs
description: Dettagli di tutto il necessario per iniziare a usare Centro sicurezza di AZURE per i prerequisiti del servizio IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 213e6a95484b5f6953f8423474953125f8739015
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541828"
---
# <a name="asc-for-iot-prerequisites"></a>Centro sicurezza di AZURE per IoT prerequisiti

> [!IMPORTANT]
> Centro sicurezza di AZURE per IoT è attualmente in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo fornisce una spiegazione dei diversi blocchi predefiniti del Centro sicurezza di AZURE per IoT servizio, ciò che occorre per iniziare e concetti di base per consentire di comprendere il servizio. 

## <a name="minimum-requirements"></a>Requisiti minimi

- Livello Standard dell'Hub IoT
    - Ruolo RBAC **proprietario** i privilegi di livello 
- [Area di lavoro di log Analitica](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Centro sicurezza di Azure (scelta consigliata)
    - Durante l'uso del Centro sicurezza di Azure è solo un'indicazione e non un requisito, in caso contrario, sarà possibile visualizzare le altre risorse di Azure all'interno dell'IoT Hub. 
 
## <a name="working-with-asc-for-iot-service"></a>Uso di Centro sicurezza di AZURE per il servizio IoT

Centro sicurezza di AZURE per informazioni dettagliate IoT e i report sono disponibili tramite l'IoT Hub di Azure e Centro sicurezza di Azure. Per abilitare Centro sicurezza di AZURE per IoT sull'IoT Hub Azure, un account con **proprietario** sono necessari privilegi di livello. Dopo l'abilitazione di Centro sicurezza di AZURE per IoT nell'IoT Hub, Centro sicurezza di AZURE per IoT insights vengono visualizzati come le **sicurezza** funzionalità nell'IoT Hub di Azure e come **IoT** nel Centro sicurezza di Azure. 

## <a name="supported-service-regions"></a>Aree del servizio supportati 

Centro sicurezza di AZURE per IoT è attualmente supportata per gli hub IoT nelle aree di Azure seguenti:
  - Stati Uniti centrali
  - Europa settentrionale
  - Asia sud-orientale

## <a name="wheres-my-iot-hub"></a>Dove si trova l'Hub IoT?

Controllare la posizione dell'IoT Hub per verificare la disponibilità del servizio prima di iniziare. 

1. Aprire l'Hub IoT. 
2. Fare clic su **Panoramica**. 
3. Verificare il percorso elencato corrisponda a uno dei [servizio aree supportate](#supported-service-regions). 


## <a name="supported-platforms-for-agents"></a>Piattaforme supportate per gli agenti 

Centro sicurezza di AZURE per gli agenti IoT supporta un elenco in continua crescita di dispositivi e piattaforme. Vedere le [elenco di piattaforme supportate](select-deploy-agent.md) per controllare la libreria di dispositivo esistente o pianificato.  

## <a name="next-steps"></a>Passaggi successivi
- [Overview](overview.md)
- [Abilitare il servizio](quickstart-onboard-iot-hub.md)
- [Centro sicurezza di AZURE per IoT domande frequenti](resources-frequently-asked-questions.md)
- [La comprensione di Centro sicurezza di AZURE per gli avvisi di IoT](concept-security-alerts.md)
