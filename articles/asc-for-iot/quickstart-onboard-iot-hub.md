---
title: Abilitare il servizio Centro sicurezza di Azure per IoT nell'hub IoT - Anteprima | Microsoft Docs
description: Informazioni su come abilitare il servizio Centro sicurezza di Azure per IoT nell'hub IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/16/2019
ms.author: mlottner
ms.openlocfilehash: 7030ae1c3a28cdd74671dc95dce59cf86cacf4c9
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65786169"
---
# <a name="quickstart-enable-service-in-iot-hub"></a>Guida introduttiva: Abilitare il servizio nell'hub IoT

> [!IMPORTANT]
> Centro sicurezza di Azure per IoT è attualmente in versione di anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo illustra come abilitare l'anteprima del servizio Centro sicurezza di Azure per IoT nell'hub IoT.  

> [!NOTE]
> Attualmente il Centro sicurezza di Azure per IoT supporta solo gli hub IoT di livello Standard.
> Il Centro sicurezza di Azure per IoT è una soluzione ad hub singolo. Se servono più hub, sono necessarie più soluzioni. 

## <a name="prerequisites-for-enabling-the-service"></a>Prerequisiti per l'abilitazione del servizio

- Area di lavoro Log Analytics
  - Per impostazione predefinita, nell'area di lavoro Log Analytics vengono archiviati due tipi di informazioni dal Centro sicurezza di Azure per IoT: gli **avvisi di sicurezza** e le **raccomandazioni**. 
  - È possibile scegliere di archiviare un altro tipo di informazioni, gli **eventi non elaborati**. L'archiviazione degli **eventi non elaborati** in Log Analytics comporta costi aggiuntivi. 
- Hub IoT (livello Standard)
- Soddisfare tutti i [prerequisiti del servizio](service-prerequisites.md) 
- Aree del servizio supportate
  - Stati Uniti centrali
  - Europa settentrionale
  - Asia sud-orientale

## <a name="enable-asc-for-iot-on-your-iot-hub"></a>Abilitare il Centro sicurezza di Azure per IoT nell'hub IoT 

Per abilitare la sicurezza nell'hub IoT, eseguire le operazioni seguenti: 

1. Aprire l'**hub IoT** nel portale di Azure. 
2. Nel menu **Sicurezza** fare clic su **Panoramica**, quindi su **Avvia anteprima**. 
3. Scegliere **Enable IoT Security** (Abilita sicurezza IoT). 
4. Fornire i dettagli dell'area di lavoro Log Analytics. 
   - Scegliere di archiviare gli **eventi non elaborati** oltre ai tipi informazioni predefiniti lasciando l'opzione **raw event** **attivata**. 
   - Scegliere di abilitare la **raccolta degli eventi gemelli** lasciando l'opzione **twin collection** **attivata**. 
5. Fare clic su **Save**. 

Congratulazioni! È stata completata l'abilitazione del Centro sicurezza di Azure per IoT nell'hub IoT. 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare la soluzione, passare all'articolo successivo...

> [!div class="nextstepaction"]
> [Configurare la soluzione](quickstart-configure-your-solution.md)
