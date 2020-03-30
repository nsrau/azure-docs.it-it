---
title: Informazioni sul Centro sicurezza di Azure per i costi IoT Documenti Microsoft
description: Informazioni sui costi associati al Centro sicurezza di Azure per l'IoT e su come controllarli.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/27/2019
ms.author: mlottner
ms.openlocfilehash: dc9dcbfd00b5205fa5c66e334b30c76d549d8a42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71348512"
---
# <a name="pricing-and-associated-costs"></a>Prezzi e costi associati

Questo articolo illustra il modello di determinazione dei prezzi del Centro sicurezza di Azure per IoT, riepiloga tutti i costi associati e spiega come gestirli.

## <a name="pricing"></a>Prezzi

Il modello di determinazione dei prezzi del Centro sicurezza di Azure per IoT è costituito da due parti e viene fatturato una volta abilitato un hub IoT nel Centro sicurezza di Azure per IoT:The Azure Security Center for IoT pricing model is comprised of two parts, and is billd once an IoT Hub is [enabled](quickstart-onboard-iot-hub.md) in Azure Security Center for IoT:

- Costo per dispositivo: funzionalità di sicurezza incorporate basate sull'analisi dei log dell'hub IoT.Cost by device - built-in security capabilities based on analysis of IoT Hub logs.

- Costo per messaggio: funzionalità di sicurezza avanzate basate sui messaggi di sicurezza provenienti da dispositivi IoT Edge o leaf.


Per ulteriori informazioni, consultate Prezzi del [Centro sicurezza.](https://azure.microsoft.com/pricing/details/security-center/)

## <a name="associated-costs"></a>Costi associati

Il Centro sicurezza di Azure per l'IoT ha costi associati, che non fanno parte dei prezzi diretti:Azure Security Center for IoT has associated costs, which are not part of the direct pricing:


- Costi di archiviazione di Log AnalyticsLog Analytics storage costs

È possibile ridurre i costi associati disattivando determinate funzionalità della soluzione. Disattiva la procedura modificando le impostazioni.

Per modificare le impostazioni:

1. Aprire l'hub IoT.

2. In **Sicurezza**fare clic su **Panoramica**.

3. Fare clic su **Impostazioni**.

Nella tabella seguente viene fornito un riepilogo dei costi associati e delle implicazioni di ciascuna opzione.

|     | Uso | Comment |
| --- | --- | --- |
| **Archiviazione di Log AnalyticsLog Analytics storage** |  |
| Raccomandazione e avvisi per i dispositivi| Raccomandazione di sicurezza e avvisi generati dal servizio | Non facoltativo |
| Dati di sicurezza grezzi| Dati di sicurezza grezzi provenienti da dispositivi IoT, raccolti dagli agenti di sicurezza | Disabilitare gli eventi di _sicurezza dei dispositivi non elaborati dell'archivioDisable store raw device security events_ |
|

>[!Important]
> La disattivazione ha gravi implicazioni per il Centro sicurezza di Azure per la disponibilità delle funzionalità di sicurezza IoT.Opting out has severe implications to Azure Security Center for IoT security feature availability. 
  
| Rifiuto esplicito | Implicazioni |
| --- | --- |
| _Raccolta di metadati gemella_ | Disabilitare [gli avvisi personalizzati](quickstart-create-custom-alerts.md) |
| | Disabilitare i suggerimenti per il manifesto di IoT EdgeDisable IoT Edge manifest recommendations |
| | Disabilitare gli avvisi e i suggerimenti basati sull'identità del dispositivoDisable device identity-based recommendations and alerts |
| _Archiviare gli eventi di sicurezza dei dispositivi non elaboratiStore raw device security events_ | I dettagli sulle raccomandazioni di base del sistema operativo del dispositivo non sono disponibiliDetails on device OS baseline recommendations are not available |
| | I dettagli sulle indagini di [allerta](concept-security-alerts.md) e [raccomandazione](concept-recommendations.md) non sono disponibili |
|


## <a name="see-also"></a>Vedere anche

- Accedi ai tuoi [dati di sicurezza grezzi](how-to-security-data-access.md)
- [Analizzare un dispositivo](how-to-investigate-device.md)
- Comprendere ed esplorare [le raccomandazioni sulla sicurezza](concept-recommendations.md)
- Comprendere ed esplorare [gli avvisi di sicurezza](concept-security-alerts.md)
