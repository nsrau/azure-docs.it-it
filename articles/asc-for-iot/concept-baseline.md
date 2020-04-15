---
title: Controlli di base e personalizzati
description: Informazioni sul concetto di Centro sicurezza di Azure per la linea di base IoT.Learn about the concept of Azure Security Center for IoT baseline.
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
ms.date: 10/07/2019
ms.author: mlottner
ms.openlocfilehash: c52a3e55e3801eaaac885b9a3c364283f74906ba
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311649"
---
# <a name="azure-security-center-for-iot-baseline-and-custom-checks"></a>Centro sicurezza di Azure per la linea di base IoT e controlli personalizzatiAzure Security Center for IoT baseline and custom checks

Questo articolo illustra il Centro sicurezza di Azure per la linea di base IoT e riepiloga tutte le proprietà associate dei controlli personalizzati di base.

## <a name="baseline"></a>Di base

Una linea di base stabilisce un comportamento standard per ogni dispositivo e semplifica la definizione di comportamenti insoliti o deviazioni dalle norme previste.

## <a name="baseline-custom-checks"></a>Controlli personalizzati della previsione

I controlli personalizzati della previsione stabiliscono un elenco personalizzato di controlli per ogni linea di base del dispositivo usando **l'identità del modulo gemello** del dispositivo.

## <a name="setting-baseline-properties"></a>Impostazione delle proprietà della linea di base

1. Nel tuo hub IoT, individua e seleziona il dispositivo che desideri modificare.
1. Fare clic sul dispositivo e quindi sul modulo **azureiotsecurity.**
1. Fare clic su **Identità modulo Twin**.
1. Caricare il file **dei controlli personalizzati di base** nel dispositivo.
1. Aggiungere le proprietà della linea di base al modulo di protezione e fare clic su **Salva**.

### <a name="baseline-custom-check-file-example"></a>Esempio di file di controllo personalizzato della previsione

Per configurare i controlli personalizzati della linea di base:

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFileHash": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>Proprietà assegno personalizzate della previsione

| Nome| Stato | Valori validi| Valori predefiniti| Descrizione |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|baselineCustomChecksEnabled|Obbligatorio: true |Valori validi: **Boolean** |Valore predefinito: **falseDefault** value: false |Intervallo di tempo massimo prima dell'invio di messaggi con priorità alta.|
|baselineCustomChecksFilePath (percorso di riferimento |Obbligatorio: true|Valori validi: **String**, **null** |Valore predefinito: **nullDefault** value: null |Percorso completo della configurazione xml di base|
|baselineCustomChecksFileHash (informazioni in base al file di lavoro) |Obbligatorio: true|Valori validi: **String**, **null** |Valore predefinito: **nullDefault** value: null |`sha256sum`del file di configurazione xml. Utilizzare il [riferimento sha256sum](https://linux.die.net/man/1/sha256sum) per ulteriori informazioni. |

Per esaminare altri esempi di base, vedere [Esempio di base personalizzato -1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) e Esempio di base personalizzato [-2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml).

## <a name="next-steps"></a>Passaggi successivi

- Accedi ai tuoi [dati di sicurezza grezzi](how-to-security-data-access.md)
- [Analizzare un dispositivo](how-to-investigate-device.md)
- Comprendere ed esplorare [le raccomandazioni sulla sicurezza](concept-recommendations.md)
- Comprendere ed esplorare [gli avvisi di sicurezza](concept-security-alerts.md)
