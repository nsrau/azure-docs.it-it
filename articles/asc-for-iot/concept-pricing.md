---
title: Comprendere il Centro sicurezza di Azure per IoT ha un costo Preview | Microsoft Docs
description: Scopri i costi associati Centro sicurezza di Azure per IoT e come è possibile controllarle.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 41b2d012ef2f6dd1ca5f57e04da43eb1a06dafde
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61360150"
---
# <a name="pricing-and-associated-costs"></a>Prezzi e costi associati

> [!IMPORTANT]
> Centro sicurezza di Azure per IoT è attualmente in versione di anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo illustra Azure Security Center (Centro sicurezza di AZURE) per il modello di determinazione prezzi di IoT, riepiloga tutti i costi associati e illustra come gestirli.

## <a name="pricing"></a>Prezzi

Il Centro sicurezza di AZURE per IoT modello tariffario è costituito da due parti e viene fatturata una volta un IoT Hub [abilitato](quickstart-onboard-iot-hub.md) nel Centro sicurezza di AZURE per IoT:

- Costo per dispositivo - funzionalità di sicurezza predefinite basate sull'analisi dei log dell'IoT Hub.

- Costo per message: funzionalità avanzate di protezione basata su messaggi di sicurezza dai dispositivi IoT Edge o foglia.

  >[!Note]
  > I messaggi di sicurezza verranno addebitato anche il consumo di quota in IoT Hub.

Per altre informazioni, vedere [prezzi del Centro sicurezza](https://azure.microsoft.com/en-us/pricing/details/security-center/).

## <a name="associated-costs"></a>Costi associati

Centro sicurezza di AZURE per IoT sono disponibili due tipi di costi associati, che non fanno parte del piano tariffario diretto:

- Utilizzo di quota dell'IoT Hub

- Registrare i costi di archiviazione Analitica

È possibile ridurre i costi associati rifiuto esplicito determinate funzionalità, modificando le impostazioni.

Per modificare le impostazioni:

1. Aprire l'Hub IoT.

2. Sotto **sicurezza**, fare clic su **Panoramica**.

3. Fare clic su **Impostazioni**.

Nella tabella seguente fornisce un riepilogo dei costi associati e le implicazioni di ogni opzione.

|     | Uso | Comment |
| --- | --- | --- |
| **Utilizzo di quota dell'IoT Hub** |  |
| [Esportare dispositivi](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-bulk-identity-mgmt#export-devices) processo (esportazione dei dispositivi gemelli) | Una volta al giorno | Disabilitare _twin raccolta di metadati_ |
| **Archiviazione dei log Analitica** |  |
| Gli avvisi e le raccomandazioni di dispositivo| Raccomandazione di sicurezza e gli avvisi generati dal servizio | Non è facoltativo |
| Dati di sicurezza non elaborati| Dati di sicurezza non elaborati dai dispositivi IoT, vengono raccolti dagli agenti di protezione | Disabilitare _archiviare gli eventi di sicurezza di dispositivi raw_ |

>[!Important]
> Rifiuto con gravi implicazioni per le funzionalità di sicurezza disponibili.
  
| Rifiuta esplicitamente | Implicazioni |
| --- | --- |
| _Raccolta di metadati dei dispositivi gemelli_ | Disabilitare [avvisi personalizzati](quickstart-create-custom-alerts.md) |
| | Disabilitare le raccomandazioni del manifesto di IoT Edge |
| | Disabilitare gli avvisi e raccomandazioni basate su identità di dispositivo |
| _Eventi di sicurezza di dispositivi raw Store_ | Non sono disponibili informazioni dettagliate sulle indicazioni di base del sistema operativo dispositivo |
| | I dettagli sui [alert](concept-security-alerts.md) e [raccomandazione](concept-recommendations.md) indagini non sono disponibili |


## <a name="see-also"></a>Vedere anche 

- Accesso di [i dati di sicurezza non elaborati](how-to-security-data-access.md)
- [Provare a utilizzare un dispositivo](how-to-investigate-device.md)
- Comprendere ed esplorare [raccomandazioni sulla sicurezza](concept-recommendations.md)
- Comprendere ed esplorare [avvisi di sicurezza](concept-security-alerts.md)
