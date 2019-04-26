---
title: Abilitare il servizio Centro sicurezza di Azure per IoT nell'hub IoT - Anteprima | Microsoft Docs
description: Informazioni su come abilitare il servizio Centro sicurezza di Azure per IoT nell'hub IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/18/2019
ms.author: mlottner
ms.openlocfilehash: cac2f740c1b2ae109f29ce44cd035fc6a83dd148
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60010561"
---
# <a name="quickstart-enable-service-in-iot-hub"></a>Guida introduttiva: Abilitare il servizio nell'hub IoT

> [!IMPORTANT]
> Centro sicurezza di Azure per IoT è attualmente in versione di anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo illustra come abilitare l'anteprima del servizio Centro sicurezza di Azure per IoT nell'hub IoT.  

> [!NOTE]
> Attualmente il Centro sicurezza di Azure per IoT supporta solo il livello Standard e gli hub IoT di livello superiore.
> Il Centro sicurezza di Azure per IoT è una soluzione ad hub singolo. Se servono più hub, sono necessarie più soluzioni. 

## <a name="prerequisites-for-enabling-the-service"></a>Prerequisiti per l'abilitazione del servizio

- Area di lavoro Log Analytics
  - Per impostazione predefinita, nell'area di lavoro Log Analytics vengono archiviati due tipi di informazioni dal Centro sicurezza di Azure per IoT: gli **avvisi di sicurezza** e le **raccomandazioni**. 
  - È possibile scegliere di archiviare un altro tipo di informazioni, gli **eventi non elaborati**. L'archiviazione degli **eventi non elaborati** in Log Analytics comporta costi aggiuntivi. 
- Hub IoT (livello standard o superiore)

## <a name="enable-asc-for-iot-on-your-iot-hub"></a>Abilitare il Centro sicurezza di Azure per IoT nell'hub IoT 

Per abilitare la sicurezza nell'hub IoT, eseguire le operazioni seguenti: 

1. Aprire l'**hub IoT** nel portale di Azure. 
2. Nel menu **Sicurezza** fare clic su **Panoramica**, quindi su **Avvia anteprima**. 
3. Scegliere **Enable IoT Security** (Abilita sicurezza IoT). 
4. Fornire i dettagli dell'area di lavoro Log Analytics. 
   - Scegliere di archiviare gli **eventi non elaborati** oltre ai tipi informazioni predefiniti lasciando l'opzione **raw event** **attivata**. 
   - Scegliere di abilitare la **raccolta degli eventi gemelli** lasciando l'opzione **twin collection** **attivata**. 
5. Fare clic su **OK**. 
6. Fare clic su **Save**. 

Congratulazioni! È stata completata l'abilitazione del Centro sicurezza di Azure per IoT nell'hub IoT. 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare la soluzione, passare all'articolo successivo...

> [!div class="nextstepaction"]
> [Configurare la soluzione](quickstart-configure-your-solution.md)