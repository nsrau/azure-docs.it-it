---
title: Microsoft Threat Modeling Tool versione 10/16/2019 - Azure
description: Documentazione delle note sulla versione per lo strumento di modellazione delle minacce
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 10/16/2019
ms.openlocfilehash: 452b44653775a1bcb9456b62e1587b5ff2dff874
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552050"
---
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>Aggiornamento di Threat Modeling Tool versione 7.1.61015.1 - 10/16/2019

La versione 7.1.61015.1 di Microsoft Threat Modeling Tool (TMT) è stata rilasciata il 16 ottobre 2019 e contiene le seguenti modifiche:

- Miglioramenti all'accessibilità
- Correzioni di bug
- Nuovi stencil per le app per la logica di Azure e Azure Data Explorer

## <a name="notable-bug-fixes"></a>Correzioni di bug notevoli

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>Migliorata la compatibilità con le versioni precedenti dei file creati in "Threat Modeling Tool 2016"

Diversi bug correlati all'apertura o alla visualizzazione di file di modello di minaccia creati in "Threat Modeling Tool 2016" sono stati corretti.

## <a name="feature-enhancements"></a>Miglioramenti delle funzionalità

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>Nuovi stencil per le app per la logica di Azure e Azure Data Explorer

Nuovi stencil per le app per la logica di Azure e Azure Data Explorer sono stati aggiunti allo stencil di Azure insieme alle minacce e alle attenuazioni associate.

![Azure Logic Apps and Azure Data Explorer Stencils](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>Problemi noti

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Errori relativi a valori di priorità al di fuori degli intervalli previsti

Alcuni clienti hanno segnalato la ricezione del seguente messaggio di errore quando si aprono i file creati in "Threat Modeling Tool 2016" o modelli personalizzati:

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

Questo problema è sotto inchiesta

## <a name="system-requirements"></a>Requisiti di sistema

- Sistemi operativi supportati
  - [Aggiornamento dell'anniversario di Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) o versione successiva
- Versione .NET richiesta
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) o versione successiva
- Requisiti aggiuntivi
  - Per ricevere aggiornamenti dello strumento e dei modelli, è necessaria una connessione Internet.

## <a name="documentation-and-feedback"></a>Documentazione e feedback

- La documentazione relativa a Threat Modeling Tool si trova su [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) e include le informazioni [sull'uso dello strumento](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Passaggi successivi

Scaricare la versione più recente di [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
