---
title: Informazioni sul centro sicurezza di Azure per i costi dell'it | Microsoft Docs
description: Informazioni sui costi associati al centro sicurezza di Azure e su come controllarli.
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
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: e742848df56e4e2be1b9edf42586e642f0c938cb
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933795"
---
# <a name="pricing-and-associated-costs"></a>Prezzi e costi associati

Questo articolo illustra il Centro sicurezza di Azure per il modello di determinazione dei prezzi di Internet, riepiloga tutti i costi associati e spiega come gestirli.

## <a name="pricing"></a>Prezzi

Il modello di determinazione dei prezzi del Centro sicurezza di Azure per gli Internet è costituito da due parti e viene fatturato una volta che un hub Internet delle cose è [abilitato](quickstart-onboard-iot-hub.md) nel centro sicurezza di Azure per tutto:

- Costo per funzionalità di sicurezza incorporate del dispositivo in base all'analisi dei log dell'hub Internet.

- Costo per funzionalità di sicurezza avanzate per i messaggi in base ai messaggi di sicurezza provenienti da dispositivi IoT Edge o foglia.

  >[!Note]
  > I messaggi di sicurezza comportano anche il consumo di quote nell'hub Internet.

Per altre informazioni, vedere [prezzi del Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="associated-costs"></a>Costi associati

Il Centro sicurezza di Azure per le cose ha due tipi di costi associati, che non fanno parte dei prezzi diretti:

- Consumo quota hub tutto

- Costi di archiviazione Log Analytics

È possibile ridurre i costi associati escludendo alcune funzionalità, modificando le impostazioni.

Per modificare le impostazioni:

1. Aprire l'hub Internet.

2. In **sicurezza**fare clic su **Panoramica**.

3. Fare clic su **Impostazioni**.

Nella tabella seguente viene fornito un riepilogo dei costi associati e delle implicazioni di ogni opzione.

|     | Utilizzo | Commento |
| --- | --- | --- |
| **Consumo quota hub tutto** |  |
| [Esportare](https://docs.microsoft.com/azure/iot-hub/iot-hub-bulk-identity-mgmt#export-devices) il processo del dispositivo (esportazione gemella) | Una volta al giorno | Disabilitare la _raccolta di metadati gemelli_ |
| **Archiviazione Log Analytics** |  |
| Suggerimenti e avvisi del dispositivo| Avvisi e avvisi di sicurezza generati dal servizio | Non facoltativo |
| Dati di sicurezza non elaborati| Dati di sicurezza non elaborati dai dispositivi Internet, raccolti dagli agenti di sicurezza | Disabilitare l' _archiviazione degli eventi di sicurezza dei dispositivi non elaborati_ |
|

>[!Important]
> La disattivazione ha gravi implicazioni nel centro sicurezza di Azure per la disponibilità delle funzionalità di sicurezza. 
  
| Rifiuta esplicitamente | Implicazioni |
| --- | --- |
| _Raccolta di metadati gemelli_ | Disabilitare gli [avvisi personalizzati](quickstart-create-custom-alerts.md) |
| | Disabilitare le raccomandazioni del manifesto IoT Edge |
| | Disabilitare le raccomandazioni e gli avvisi basati sull'identità del dispositivo |
| _Archiviare gli eventi di sicurezza del dispositivo non elaborato_ | Non sono disponibili informazioni dettagliate sulle raccomandazioni di base del sistema operativo del dispositivo |
| | Informazioni dettagliate sulle analisi degli [avvisi](concept-security-alerts.md) e delle [raccomandazioni](concept-recommendations.md) non sono disponibili |
|


## <a name="see-also"></a>Vedere anche

- Accedere ai [dati di sicurezza non elaborati](how-to-security-data-access.md)
- [Esaminare un dispositivo](how-to-investigate-device.md)
- Comprendere ed esplorare le [raccomandazioni sulla sicurezza](concept-recommendations.md)
- Comprendere ed esplorare gli [avvisi di sicurezza](concept-security-alerts.md)
