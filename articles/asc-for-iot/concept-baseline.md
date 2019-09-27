---
title: Informazioni sul centro sicurezza di Azure per la linea di base di Internet delle cose | Microsoft Docs
description: Informazioni sul concetto di Centro sicurezza di Azure per la linea di base di Internet delle cose.
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
ms.date: 09/26/2019
ms.author: mlottner
ms.openlocfilehash: a9de9924b851024dd36c848203cc3ada2cde208c
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329409"
---
# <a name="azure-security-center-for-iot-baseline-and-custom-checks"></a>Centro sicurezza di Azure per la baseline e i controlli personalizzati

Questo articolo illustra il Centro sicurezza di Azure per la baseline e riepiloga tutte le proprietà associate dei controlli personalizzati di base.

## <a name="baseline"></a>Linea di base

Una linea di base stabilisce un comportamento standard per ogni dispositivo e rende più semplice stabilire un comportamento insolito o una deviazione dalle norme previste.  

## <a name="baseline-custom-checks"></a>Controlli personalizzati di base

I controlli personalizzati di base stabiliscono un elenco personalizzato di controlli per ogni linea di base del dispositivo usando il modulo identità gemello del dispositivo. 

## <a name="setting-baseline-properties"></a>Impostazione delle proprietà della linea di base

1. Nell'hub Internet delle cose individuare e selezionare il dispositivo che si vuole modificare.
1. Fare clic sul dispositivo e quindi fare clic sul modulo **azureiotsecurity** .
1. Fare clic su **Module Identity gemelle**.
1. Caricare il file di **controllo personalizzato di base** nel dispositivo.
1. Aggiungere le proprietà baseline al modulo Security e fare clic su **Save**.

### <a name="baseline-custom-check-file-example"></a>Esempio di file di controllo personalizzato Baseline

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
        "baselineCustomChecksFilePath": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>Proprietà controllo personalizzato Baseline

| Attività| Stato | Valori validi| Valori predefiniti| Descrizione |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|baselineCustomChecksEnabled|Obbligatorio: true |Valori validi:  **Boolean** |Valore predefinito: **false** |Intervallo di tempo massimo prima dell'invio dei messaggi con priorità alta.|
|baselineCustomChecksFilePath |Obbligatorio: true|Valori validi:  **Stringa**, **null** |Valore predefinito: **PT5H** |Percorso completo della configurazione XML di base|
|baselineCustomChecksFileHash |Obbligatorio: true|Valori validi:  **Stringa**, **null** |Valore predefinito: **PT5H** |`sha256sum` del file di configurazione XML. Per ulteriori informazioni, utilizzare il [riferimento sha256sum](https://linux.die.net/man/1/sha256sum) . |

Per esaminare esempi di base aggiuntivi, vedere [esempio di Baseline personalizzato-1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) e [esempio di Baseline personalizzato-2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml).

## <a name="next-steps"></a>Passaggi successivi

- Accedere ai [dati di sicurezza non elaborati](how-to-security-data-access.md)
- [Esaminare un dispositivo](how-to-investigate-device.md)
- Comprendere ed esplorare le [raccomandazioni sulla sicurezza](concept-recommendations.md)
- Comprendere ed esplorare gli [avvisi di sicurezza](concept-security-alerts.md)
