---
title: Abilitare il servizio Centro sicurezza di Azure per IoT nell'hub IoT | Microsoft Docs
description: Informazioni su come abilitare il servizio Centro sicurezza di Azure per IoT nell'hub IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/16/2019
ms.author: mlottner
ms.openlocfilehash: a794ccea13323f38b20906458e216f85652bfc3e
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596957"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>Guida introduttiva: Eseguire l'onboarding del servizio Centro sicurezza di Azure per IoT nell'hub IoT

Questo articolo illustra come abilitare il servizio Centro sicurezza di Azure per IoT nell'hub IoT esistente. Se non si ha già un hub IoT, vedere [Creare un hub IoT usando il portale di Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) per iniziare. 

> [!NOTE]
> Attualmente il Centro sicurezza di Azure per IoT supporta solo gli hub IoT di livello Standard.
> Il Centro sicurezza di Azure per IoT è una soluzione ad hub singolo. Se servono più hub, sono necessarie più soluzioni Centro sicurezza di Azure per IoT. 

## <a name="prerequisites-for-enabling-the-service"></a>Prerequisiti per l'abilitazione del servizio

- Area di lavoro Log Analytics
  - Per impostazione predefinita, nell'area di lavoro Log Analytics vengono archiviati due tipi di informazioni dal Centro sicurezza di Azure per IoT: gli **avvisi di sicurezza** e le **raccomandazioni**. 
  - È possibile scegliere di archiviare un altro tipo di informazioni, gli **eventi non elaborati**. L'archiviazione degli **eventi non elaborati** in Log Analytics comporta costi aggiuntivi. 
- Hub IoT (livello Standard)
- Soddisfare tutti i [prerequisiti del servizio](service-prerequisites.md) 

|Aree del servizio Azure supportate | ||
|---|---|---|
| Stati Uniti centrali |Stati Uniti orientali |Stati Uniti orientali 2 |
| Stati Uniti centro-occidentali |Stati Uniti occidentali |Stati Uniti occidentali 2 |
| Stati Uniti centro-meridionali|Stati Uniti centro-settentrionali | Canada centrale|
| Canada orientale| Europa settentrionale|Brasile meridionale|
| Francia centrale| Regno Unito occidentale|Regno Unito meridionale|
|Europa occidentale|Europa settentrionale| Giappone occidentale|
|Giappone orientale | Australia sud-orientale|Australia orientale|
|Asia orientale| Asia sud-orientale| Corea del Sud centrale|
|Corea del Sud meridionale| India centrale| India meridionale|
|

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>Abilitare il Centro sicurezza di Azure per IoT nell'hub IoT 

Per abilitare la sicurezza nell'hub IoT, eseguire le operazioni seguenti: 

1. Aprire l'**hub IoT** nel portale di Azure. 
1. Nel menu **Sicurezza** fare clic su **Secure your IoT solution** (Proteggere la soluzione IoT)
1. Lasciare l'opzione predefinita **Enable** (Abilita) selezionata. 
1. Selezionare l'area di lavoro Log Analytics.
1. Specificare i dettagli dell'area di lavoro Log Analytics. 
   - Scegliere di abilitare la **raccolta degli eventi gemelli** lasciando l'opzione **twin collection** **attivata**.
   - Scegliere di archiviare gli **eventi non elaborati** oltre ai tipi di informazioni predefiniti dell'archiviazione selezionando **Store raw device security events** (Archivia eventi di sicurezza non elaborati del dispositivo) in Log Analytics. Lasciare l'opzione **raw event** **attivata**. 
    
1. Fare clic su **Save**. 

Congratulazioni! È stata completata l'abilitazione del Centro sicurezza di Azure per IoT nell'hub IoT. 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare la soluzione, passare all'articolo successivo...

> [!div class="nextstepaction"]
> [Configurare la soluzione](quickstart-configure-your-solution.md)


